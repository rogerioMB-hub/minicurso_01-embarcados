---
layout: default
title: "Aula 8 — UART: Protocolo de Nibbles"
---

# Aula 8 — UART: Protocolo de Nibbles

> **Duração estimada:** 30 minutos
> **Bloco:** 3 de 3 — Integração e preparação para UART

---

## Objetivos

Ao final desta aula você será capaz de:

- Estruturar um byte como protocolo com dois campos independentes
- Extrair nibble alto e nibble baixo usando máscaras e shift
- Implementar um receptor que interpreta comandos e argumentos
- Aplicar no Wokwi (loopback) e entender a adaptação para duas placas reais

---

## 1. Conceito

### Por que estruturar o byte?

Na Aula 7 usamos bytes simples — `0x01` significa LIGAR, `0x00` significa DESLIGAR. Funciona bem para dois comandos. Mas e se precisarmos de mais informação em uma única transmissão?

Um byte tem 8 bits. Podemos dividí-lo em dois grupos de 4 bits — chamados **nibbles** — e colocar informações diferentes em cada um:

```
Byte:   0b  C C C C   V V V V
              ───────   ───────
           nibble alto  nibble baixo
           (comando)    (valor/argumento)
```

Com 4 bits para o comando, temos até **16 comandos distintos**.
Com 4 bits para o valor, temos argumentos de **0 a 15**.

Isso é exatamente o que fazemos com máscaras — e você já sabe como:

```python
byte     = 0x25              # exemplo: 0b00100101
cmd      = (byte >> 4) & 0x0F   # desloca 4 bits → pega nibble alto  = 2
valor    =  byte       & 0x0F   # zera nibble alto → pega nibble baixo = 5
```

> **Conexão direta com a Aula 3:** `>> 4` desloca o nibble alto para a posição 0,
> e `& 0x0F` isola os 4 bits de interesse. É a mesma máscara de extração que
> usamos para controlar LEDs — agora aplicada a bytes recebidos pela UART.

### Protocolo desta aula

| Nibble alto (cmd) | Nibble baixo (val) | Ação |
|:-----------------:|:------------------:|------|
| `0` | qualquer | Apaga todos os LEDs |
| `1` | padrão (0–15) | Acende LEDs conforme padrão binário do nibble baixo |
| `2` | N (1–4) | Sequenciador: percorre N ciclos nos LEDs |

Exemplos:

```
0x00  →  cmd=0, val=0  →  apaga tudo
0x1A  →  cmd=1, val=10 →  padrão 0b1010 nos LEDs (LED1 e LED3 acesos)
0x23  →  cmd=2, val=3  →  sequenciador por 3 ciclos
```

---

## 2. A limitação do Wokwi — mesma solução da Aula 7

> Esta aula usa a **mesma configuração de loopback** da Aula 7:
> UART1 como transmissora e UART2 como receptora, conectadas
> internamente por fios no diagrama.
>
> **Diferença importante desta aula:** o transmissor não usa botão —
> o byte de comando é digitado pelo usuário via `input()` no terminal.
> O `input()` funciona de forma confiável no Wokwi e evita o problema
> de bloqueio que ocorreria com leitura contínua da UART0.

---

## 3. Circuito — Wokwi (loopback)

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit C V4 | 1 |
| LED | 4 |
| Resistor 220 Ω | 4 |

**Conexões internas (loopback):**

```
GPIO 4  (TX1) ──────► GPIO 16 (RX2)
GPIO 17 (TX2) ──────► GPIO 5  (RX1)

GPIO 2  ──► R220 ──► LED0 ──► GND   (bit 0)
GPIO 12 ──► R220 ──► LED1 ──► GND   (bit 1)
GPIO 14 ──► R220 ──► LED2 ──► GND   (bit 2)
GPIO 27 ──► R220 ──► LED3 ──► GND   (bit 3)
```

> A UART0 permanece conectada ao monitor serial (terminal Wokwi).

---

## 4. Circuito Wokwi — diagram.json

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados — Aula 8",
  "editor": "wokwi",
  "parts": [
    {
      "type": "board-esp32-devkit-c-v4",
      "id": "esp",
      "top": 0,
      "left": 0,
      "attrs": { "env": "micropython-20231227-v1.22.0" }
    },
    { "type": "wokwi-led", "id": "led0", "top": 20, "left": 220, "attrs": { "color": "red" } },
    { "type": "wokwi-led", "id": "led1", "top": 20, "left": 258, "attrs": { "color": "yellow" } },
    { "type": "wokwi-led", "id": "led2", "top": 20, "left": 296, "attrs": { "color": "green" } },
    { "type": "wokwi-led", "id": "led3", "top": 20, "left": 334, "attrs": { "color": "blue" } },
    {
      "type": "wokwi-resistor",
      "id": "r0",
      "top": 100,
      "left": 215,
      "rotate": 90,
      "attrs": { "value": "220" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r1",
      "top": 100,
      "left": 253,
      "rotate": 90,
      "attrs": { "value": "220" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r2",
      "top": 100,
      "left": 291,
      "rotate": 90,
      "attrs": { "value": "220" }
    },
    {
      "type": "wokwi-resistor",
      "id": "r3",
      "top": 100,
      "left": 329,
      "rotate": 90,
      "attrs": { "value": "220" }
    }
  ],
  "connections": [
    [ "esp:TX", "$serialMonitor:RX", "", [] ],
    [ "esp:RX", "$serialMonitor:TX", "", [] ],
    [ "esp:4", "esp:16", "green", [ "h20", "v-10" ] ],
    [ "esp:17", "esp:5", "green", [ "h20", "v-10" ] ],
    [ "esp:2", "r0:2", "red", [] ],
    [ "r0:1", "led0:A", "red", [] ],
    [ "led0:K", "esp:GND.1", "black", [] ],
    [ "esp:12", "r1:2", "yellow", [ "h-23.81", "v105.6", "h301.55" ] ],
    [ "r1:1", "led1:A", "yellow", [] ],
    [ "led1:K", "esp:GND.1", "black", [] ],
    [ "esp:14", "r2:2", "green", [ "h-33.41", "v124.8", "h349.15" ] ],
    [ "r2:1", "led2:A", "green", [] ],
    [ "led2:K", "esp:GND.1", "black", [] ],
    [ "esp:27", "r3:2", "blue", [ "h-43.01", "v144", "h396.75" ] ],
    [ "r3:1", "led3:A", "blue", [] ],
    [ "led3:K", "esp:GND.1", "black", [] ],
    [ "esp:GND.3", "led0:C", "black", [ "h0" ] ],
    [ "esp:GND.3", "led1:C", "black", [ "h120.04", "v-86.4", "h38.4", "v86.4", "h13.8" ] ],
    [ "esp:GND.3", "led2:C", "black", [ "h120.04", "v-86.4", "h76.8", "v86.4", "h13.4" ] ],
    [ "esp:GND.3", "led3:C", "black", [ "h120.04", "v-86.4", "h115.2", "v86.4", "h13" ] ]
  ],
  "dependencies": {}
}
```

> **Atenção:** este `diagram.json` é uma sugestão de ponto de partida.
> Valide e ajuste as conexões no Wokwi antes de usar com os alunos.

---

## 5. Código — Wokwi (loopback com input)

```python
# ============================================================
# Aula 8 — UART: Protocolo de Nibbles (Wokwi loopback)
#
# O usuário digita um byte em hexadecimal no terminal.
# A UART1 transmite o byte para a UART2 (loopback interno).
# A UART2 extrai nibble alto (comando) e baixo (valor)
# e executa a ação correspondente nos LEDs.
#
# Conexão interna:
#   GPIO 4  (TX1) → GPIO 16 (RX2)
#   GPIO 17 (TX2) → GPIO 5  (RX1)
#
# Protocolo:
#   nibble alto = comando (0, 1 ou 2)
#   nibble baixo = valor/argumento (0 a 15)
#
# Nota Wokwi: input() é usado para receber o byte do usuário
# pois evita conflito com UART0 (monitor serial).
# ============================================================

from machine import UART, Pin   # type: ignore[import]
import time

# --- UARTs ---
uart_tx = UART(1, baudrate=9600, tx=4,  rx=5)
uart_rx = UART(2, baudrate=9600, tx=17, rx=16)
# Pico: uart_tx = UART(1, baudrate=9600, tx=Pin(4), rx=Pin(5))
# Pico: uart_rx = UART(0, baudrate=9600, tx=Pin(0), rx=Pin(1))

# --- LEDs ---
pinos = [Pin(2, Pin.OUT), Pin(12, Pin.OUT), Pin(14, Pin.OUT), Pin(27, Pin.OUT)]
# Pico: pinos = [Pin(2,Pin.OUT), Pin(3,Pin.OUT), Pin(4,Pin.OUT), Pin(5,Pin.OUT)]

# --- funções auxiliares ---
def aplicar_nibble(nibble):
    """Escreve os 4 bits do nibble nos LEDs."""
    for i, pino in enumerate(pinos):
        pino.value((nibble >> i) & 1)

def processar_byte(byte):
    """Extrai nibble alto (cmd) e baixo (val) e executa o comando."""
    cmd   = (byte >> 4) & 0x0F   # nibble alto → comando
    valor =  byte       & 0x0F   # nibble baixo → argumento

    print(f"  Byte   : 0b{byte:08b}  (0x{byte:02X})")
    print(f"  cmd    : {cmd}  =  0b{cmd:04b}")
    print(f"  valor  : {valor}  =  0b{valor:04b}")

    if cmd == 0:
        aplicar_nibble(0b0000)
        print("  Ação   : apagar todos os LEDs")

    elif cmd == 1:
        # nibble baixo define padrão binário dos LEDs
        aplicar_nibble(valor)
        print(f"  Ação   : padrão 0b{valor:04b} nos LEDs")

    elif cmd == 2:
        # nibble baixo define número de ciclos do sequenciador
        print(f"  Ação   : sequenciador — {valor} ciclo(s)")
        for _ in range(valor):
            for pos in range(4):
                aplicar_nibble(1 << pos)
                time.sleep(0.15)
        aplicar_nibble(0b0000)

    else:
        print(f"  Ação   : comando {cmd} não reconhecido")

# --- loop principal ---
print("Aula 8 — UART Protocolo de Nibbles")
print("Digite o byte em hexadecimal (ex: 1A, 23, 00) e pressione Enter.")
print("─" * 48)

while True:
    entrada = input(">> ").strip()   # lê do terminal (UART0 / monitor Wokwi)

    try:
        byte = int(entrada, 16)      # converte hex para inteiro
        if not (0 <= byte <= 255):
            print("Valor fora do intervalo — use 00 a FF.")
            continue
    except ValueError:
        print("Entrada inválida — use dois dígitos hex (ex: 1A).")
        continue

    # transmite o byte via UART1
    uart_tx.write(bytes([byte]))
    print(f"[TX] Enviou 0x{byte:02X}")
    time.sleep(0.05)               # aguarda transmissão

    # recebe e processa via UART2
    if uart_rx.any():
        dado = uart_rx.read(1)
        if dado:
            print(f"[RX] Recebeu 0x{dado[0]:02X}")
            processar_byte(dado[0])
    else:
        print("[RX] Nenhum dado recebido — verifique as conexões do loopback.")

    print("─" * 48)
```

---

## 6. Aplicação real — duas placas físicas

**Ligação física:**

```
Placa A (transmissora)        Placa B (receptora)
   TX (GPIO1) ──────────────► RX (GPIO3)
   RX (GPIO3) ◄────────────── TX (GPIO1)
   GND        ───────────────  GND      ← obrigatório
```

**Código — Placa A (transmissora):**

```python
from machine import UART
import time

uart = UART(0, baudrate=9600)

print("Placa A — Transmissora.")
print("Digite o byte em hex (ex: 1A) e pressione Enter.")

while True:
    entrada = input(">> ").strip()
    try:
        byte = int(entrada, 16)
        uart.write(bytes([byte]))
        print(f"[TX] Enviou 0x{byte:02X}")
    except ValueError:
        print("Entrada inválida.")
```

**Código — Placa B (receptora):**

```python
from machine import UART, Pin
import time

uart  = UART(0, baudrate=9600)
pinos = [Pin(2,Pin.OUT), Pin(12,Pin.OUT), Pin(14,Pin.OUT), Pin(27,Pin.OUT)]

def aplicar_nibble(nibble):
    for i, pino in enumerate(pinos):
        pino.value((nibble >> i) & 1)

def processar_byte(byte):
    cmd   = (byte >> 4) & 0x0F
    valor =  byte       & 0x0F
    print(f"[RX] 0x{byte:02X}  cmd={cmd}  val={valor}")
    if cmd == 0:
        aplicar_nibble(0)
    elif cmd == 1:
        aplicar_nibble(valor)
    elif cmd == 2:
        for _ in range(valor):
            for pos in range(4):
                aplicar_nibble(1 << pos)
                time.sleep(0.15)
        aplicar_nibble(0)

print("Placa B — Receptora pronta.")

while True:
    if uart.any():
        dado = uart.read(1)
        if dado:
            processar_byte(dado[0])
    time.sleep(0.02)
```

> Use o **Thonny** para gravar os códigos. Execute primeiro a Placa B,
> depois a Placa A.

---

## 7. Experimento

Execute o código no Wokwi e teste os bytes abaixo:

| Byte digitado | Binário | cmd | val | Resultado esperado | Resultado observado |
|:-------------:|:-------:|:---:|:---:|-------------------|---------------------|
| `00` | `0000 0000` | 0 | 0 | LEDs apagados | _________________ |
| `1F` | `0001 1111` | 1 | 15 | todos acesos | _________________ |
| `1A` | `________` | __ | __ | _________________ | _________________ |
| `10` | `________` | __ | __ | _________________ | _________________ |
| `23` | `________` | __ | __ | _________________ | _________________ |
| `05` | `________` | __ | __ | _________________ | _________________ |

**Perguntas:**

**a)** Por que `(byte >> 4) & 0x0F` e não apenas `byte >> 4`?

> _________________________________________________________________

**b)** Qual byte você enviaria para acender apenas LED0 e LED2 com o comando 1?

```
cmd = 1   →  nibble alto = 0b0001
LED0 e LED2  →  nibble baixo = 0b____
Byte completo = 0b________  =  0x____
```

**c)** O byte `0x24` usa o comando 2 com val=4. Quantos ciclos o sequenciador executa?

> _________________________________________________________________

**d)** O código usa `input()` em vez de ler continuamente a UART0. Por que essa escolha é necessária no Wokwi?

> _________________________________________________________________
> _________________________________________________________________

---

## 8. Desafio

**Desafio principal:** adicione o **comando 3** — acendimento progressivo:

```
cmd=3, val=N → acende os N primeiros LEDs (da direita para esquerda)
val=1 → 0b0001   val=2 → 0b0011   val=3 → 0b0111   val=4 → 0b1111
```

```python
elif cmd == 3:
    mascara = (1 << valor) - 1   # gera N bits em '1' a partir do bit 0
    aplicar_nibble(mascara)
    print(f"  Ação   : progressivo — {valor} LED(s)  máscara=0b{mascara:04b}")
```

Teste com: `31` (1 LED), `32` (2 LEDs), `33` (3 LEDs), `34` (4 LEDs).

**Desafio bônus:** adicione enquadramento com byte de início. O receptor só processa um byte de comando se o byte anterior for `0xFF`:

```python
aguardando_inicio = True

# dentro do bloco if uart_rx.any():
if aguardando_inicio:
    if dado[0] == 0xFF:
        aguardando_inicio = False
        print("[RX] Marcador 0xFF recebido — aguardando comando...")
else:
    processar_byte(dado[0])
    aguardando_inicio = True
```

Teste enviando: `FF` seguido de `1A`. Compare com enviar apenas `1A` direto.

---

## Resumo da aula

- Um byte pode carregar dois campos independentes dividindo-o em nibbles
- `(byte >> 4) & 0x0F` extrai o nibble alto (bits 7–4) — o comando
- `byte & 0x0F` extrai o nibble baixo (bits 3–0) — o argumento
- No Wokwi, `input()` permite receber dados do terminal sem conflito com UART0
- Em aplicação real com duas placas, cada placa usa sua própria UART0 com `.any()`
- Enquadramento com byte de início (`0xFF`) é a base dos protocolos seriais reais

---

*← [Aula 7](./aula07-uart-primeiros-bytes.md) | [Voltar ao início](../README.md)*
