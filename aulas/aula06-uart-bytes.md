---
layout: default
title: "Aula 6 — Bytes pela UART: Enquadramento e Extração de Campos"
---

# Aula 6 — Bytes pela UART: Enquadramento e Extração de Campos

> **Duração estimada:** 30 minutos  
> **Bloco:** 3 de 3 — Integração e preparação para UART

---

## Objetivos

Ao final desta aula você será capaz de:

- Receber bytes pela UART e tratá-los como palavras binárias
- Extrair campos de um byte usando máscaras e shift
- Implementar um protocolo simples de comando por byte
- Compreender que UART transmite exatamente os bytes que aprendemos a manipular

---

## 1. Conceito

### O byte que você já conhece

Nas aulas anteriores manipulamos bytes diretamente na memória e nos pinos. A UART faz o mesmo — só que **transmitindo esses bytes serialmente**, bit a bit, a uma velocidade configurável (baud rate).

Quando o ESP32 recebe um byte pela UART, esse byte chega como um inteiro Python — e você pode aplicar todas as operações que já aprendeu:

```python
byte_recebido = uart.read(1)[0]   # é um inteiro de 0 a 255
nibble_alto   = (byte_recebido >> 4) & 0x0F   # bits 7-4
nibble_baixo  =  byte_recebido       & 0x0F   # bits 3-0
```

### Protocolo de comando por byte

Vamos definir um protocolo simples:

```
Bit 7-4 (nibble alto):  tipo de comando
Bit 3-0 (nibble baixo): valor / argumento

Exemplos:
  0b0001_0101  →  comando 1, valor 5
  0b0010_1111  →  comando 2, valor 15
  0b0000_0000  →  reset geral
```

> **Conexão com o que já fizemos:** o "nibble alto" é exatamente o tipo de extração
> com máscara que praticamos na Aula 3. A UART só adiciona o transporte do dado.

---

## 2. Circuito

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit | 1 |
| LED | 4 |
| Resistor 220 Ω | 4 |

**Conexões:**

```
GPIO2  ──► R220 ──► LED0 ──► GND
GPIO4  ──► R220 ──► LED1 ──► GND
GPIO5  ──► R220 ──► LED2 ──► GND
GPIO18 ──► R220 ──► LED3 ──► GND
```

No Wokwi, a UART0 do ESP32 já está conectada ao terminal serial — não é necessário nenhum componente extra.

```
# Pico: use UART0 (GPIO0=TX, GPIO1=RX) — idêntico conceitualmente
# Pico: uart = UART(0, baudrate=9600)
# Pico: LEDs em GPIO 2,3,4,5
```

---

## 3. Código

### Parte A — receber um byte e exibir seus campos

```python
from machine import Pin, UART
import time

# --- pinos dos LEDs ---
leds = [Pin(2,Pin.OUT), Pin(4,Pin.OUT), Pin(5,Pin.OUT), Pin(18,Pin.OUT)]

# --- UART0: TX=GPIO1, RX=GPIO3 (padrão ESP32 UART0)
uart = UART(0, baudrate=9600)
# Pico: uart = UART(0, baudrate=9600, tx=Pin(0), rx=Pin(1))

def aplicar_nibble(nibble):
    """Escreve os 4 bits do nibble nos LEDs."""
    for i, led in enumerate(leds):
        led.value((nibble >> i) & 1)

def processar_byte(byte):
    """Extrai os dois nibbles e executa o comando."""
    cmd   = (byte >> 4) & 0x0F   # nibble alto: tipo de comando
    valor = byte        & 0x0F   # nibble baixo: argumento

    print(f"Byte recebido : 0b{byte:08b}  (0x{byte:02X}  dec={byte})")
    print(f"  Comando (b7-4): {cmd}  =  0b{cmd:04b}")
    print(f"  Valor   (b3-0): {valor}  =  0b{valor:04b}")

    if cmd == 0:
        # comando 0: apaga tudo
        aplicar_nibble(0b0000)
        print("  → Ação: apagar todos os LEDs")

    elif cmd == 1:
        # comando 1: acende LEDs conforme padrão no nibble baixo
        aplicar_nibble(valor)
        print(f"  → Ação: padrão de LEDs = 0b{valor:04b}")

    elif cmd == 2:
        # comando 2: sequenciador — 'valor' define número de ciclos
        print(f"  → Ação: sequenciador por {valor} ciclos")
        for _ in range(valor):
            for pos in range(4):
                aplicar_nibble(1 << pos)
                time.sleep(0.1)
        aplicar_nibble(0)

    else:
        print(f"  → Comando {cmd} não reconhecido")

# --- loop principal ---
print("Aguardando bytes pela UART (baud=9600)...")
print("No terminal Wokwi, envie um byte em hexadecimal.")
print("Exemplos: 0x15 (cmd=1, val=5)  0x2F (cmd=2, val=15)  0x00 (reset)")

while True:
    if uart.any():
        dado = uart.read(1)
        if dado:
            byte = dado[0]
            processar_byte(byte)
    time.sleep(0.05)
```

---

### Parte B — ESP32 também envia: eco com campos anotados

```python
# adicione dentro do loop, após processar_byte:
    resposta = f"ACK cmd={cmd} val={valor}\r\n"
    uart.write(resposta)
```

---

## 4. Experimento

No terminal serial do Wokwi, envie os bytes abaixo e preencha a tabela:

| Byte enviado | Binário | Nibble alto (cmd) | Nibble baixo (val) | LEDs acesos |
|:------------:|:-------:|:-----------------:|:------------------:|:-----------:|
| `0x10` | `0001 0000` | 1 | 0 | nenhum |
| `0x1F` | `0001 1111` | 1 | 15 | todos |
| `0x1A` | `________` | __ | __ | _______ |
| `0x05` | `________` | __ | __ | _______ |
| `0x25` | `________` | __ | __ | _______ |
| `0x00` | `________` | __ | __ | _______ |

**Perguntas:**

**a)** Por que `(byte >> 4) & 0x0F` e não apenas `byte >> 4`?

> _________________________________________________________________

**b)** Qual byte você enviaria para acender apenas LED0 e LED2 usando o comando 1?

```
cmd = 1  →  nibble alto = 0b0001
LEDs 0 e 2  →  nibble baixo = 0b____
Byte completo = 0b________  = 0x____
```

**c)** O byte `0x2F` ativa o comando 2 com valor 15. Quantos ciclos de sequenciador serão executados?

> _________________________________________________________________

**d)** Sem a UART, como você produziria o mesmo efeito de receber o padrão `0b00001010` e acender os LEDs correspondentes? (dica: já fizemos isso na Aula 3)

> _________________________________________________________________

---

## 5. Desafio

**Desafio principal:** adicione o **comando 3**, que implementa o acendimento progressivo:

```
cmd=3, val=N → acende N LEDs da direita para a esquerda
val=1 → 0b0001  val=2 → 0b0011  val=3 → 0b0111  val=4 → 0b1111
```

```python
elif cmd == 3:
    # acendimento progressivo: val define quantos LEDs acender (1 a 4)
    mascara = ___________   # como montar essa máscara com bitwise?
    aplicar_nibble(mascara)
    print(f"  → Progressivo: {valor} LED(s)")
```

**Dica:** `(1 << N) - 1` gera uma máscara com N bits em `1` a partir do bit 0.

**Desafio bônus:** implemente um **buffer de dois bytes**: o primeiro byte é sempre `0xFF` (marcador de início de quadro) e o segundo é o byte de comando. O ESP32 só processa o comando se receber `0xFF` antes:

```python
aguardando_inicio = True
while True:
    if uart.any():
        byte = uart.read(1)[0]
        if aguardando_inicio:
            if byte == 0xFF:
                aguardando_inicio = False
        else:
            processar_byte(byte)
            aguardando_inicio = True
```

---

## Resumo da aula

- Um byte recebido pela UART é um inteiro Python — manipulável com todos os operadores bitwise
- `(byte >> 4) & 0x0F` extrai o nibble alto (bits 7–4)
- `byte & 0x0F` extrai o nibble baixo (bits 3–0)
- Um protocolo de comando por byte é uma aplicação direta de máscaras e shifts
- O enquadramento (byte de início `0xFF`) é a base dos protocolos seriais reais

---

## Parabéns — você concluiu o mini curso!

Você partiu de um único bit em um pino e chegou à extração de campos de um protocolo serial. Os conceitos que usou ao longo das 6 aulas são exatamente os mesmos presentes em protocolos industriais como Modbus, CANbus e UART com enquadramento — a diferença está apenas na complexidade do protocolo, não nos operadores.

---

*← [Aula 5](./aula05-flags-estados.md) | [Voltar ao início](../README.md)*
