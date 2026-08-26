---
layout: default
title: "Aula 4 — Deslocamento de Bits e Escrita Direta em Porta"
---

# Aula 4 — Deslocamento de Bits e Escrita Direta em Porta

> **Duração estimada:** 30 minutos  
> **Bloco:** 2 de 3 — Listas, laços e lógica combinatória

---

## Objetivos

Ao final desta aula você será capaz de:

- Usar `<<` e `>>` para deslocar bits e criar padrões sequenciais
- Entender o conceito de palavra binária como representação de múltiplas saídas
- Escrever diretamente no registrador GPIO do ESP32 usando `machine.mem32`
- Comparar a abordagem pino-a-pino com a escrita direta de uma palavra inteira

---

## 1. Conceito

### Deslocamento de bits

O operador `<<` desloca todos os bits de um número para a esquerda, inserindo `0` à direita. O operador `>>` faz o movimento contrário:

```
0b00000001 << 1  =  0b00000010   (bit "caminha" para esquerda)
0b00000100 >> 1  =  0b00000010   (bit "caminha" para direita)
```

Aplicado a LEDs: se cada bit corresponde a um LED, deslocar é fazer o LED "andar".

### Escrita direta em porta (registrador GPIO)

Nos exemplos anteriores escrevemos em cada pino individualmente dentro de um `for`. O ESP32 possui um registrador de hardware chamado **GPIO_OUT** que controla todos os pinos de uma vez. Escrever nele com uma única instrução é significativamente mais rápido.

**Endereço do registrador GPIO_OUT no ESP32:**

```python
GPIO_OUT = 0x3FF44004   # registrador de saída GPIO 0–31
```

Para escrever uma palavra de 32 bits nesse registrador:

```python
from machine import mem32
mem32[GPIO_OUT] = palavra
```

Cada bit da `palavra` corresponde a um GPIO de mesmo índice. Bit 2 → GPIO2, bit 4 → GPIO4, etc.

> **Raspberry Pi Pico:** o registrador equivalente é `SIO_GPIO_OUT` no endereço `0xD0000010`.  
> A forma mais portável no Pico é usar `machine.mem32[0xD0000010]` ou a classe `machine.Pin` com atribuição direta.  
> `# Pico: GPIO_OUT = 0xD0000010`

---

## 2. Circuito

Mesmo circuito da Aula 3 — 4 LEDs nos GPIOs 2, 4, 5 e 18.

> **Atenção:** na escrita direta via `mem32`, os bits devem corresponder exatamente aos números dos GPIOs. GPIO2 = bit 2, GPIO4 = bit 4, GPIO5 = bit 5, GPIO18 = bit 18 da palavra de 32 bits.
---

### Funcionamento do sequenciador

A animação abaixo mostra como a instrução `1 << pos` desloca o bit `1`,
ativando sequencialmente os GPIOs 2, 4, 5 e 18.

![Animação do sequenciador em MicroPython](assets/sequenciador_esp32.gif)

---

## 3. Código

### Parte A — sequenciador com shift (abordagem por lista)

```python
from machine import Pin
import time

pinos = [Pin(2,Pin.OUT), Pin(4,Pin.OUT), Pin(5,Pin.OUT), Pin(18,Pin.OUT)]

def escrever_palavra(palavra):
    for i, pino in enumerate(pinos):
        pino.value((palavra >> i) & 1)

print("Sequenciador — ida")
while True:
    for pos in range(4):
        padrao = 1 << pos          # desloca o bit '1' para a posição pos
        escrever_palavra(padrao)
        print(f"pos={pos}  padrão={padrao:04b}")
        time.sleep(0.2)
```

---

### Parte B — escrita direta via registrador GPIO_OUT

```python
from machine import Pin, mem32
import time

# configura pinos como saída (necessário antes de usar mem32)
for gpio in [2, 4, 5, 18]:
    Pin(gpio, Pin.OUT)

GPIO_OUT = 0x3FF44004   # ESP32 — registrador GPIO_OUT (GPIO 0-31)
# Pico: GPIO_OUT = 0xD0000010

# mapa: posição no padrão (0..3) → número do GPIO real
gpios = [2, 4, 5, 18]

def montar_mascara(padrao_4bits):
    """Converte 4 bits de posição em máscara de 32 bits para o registrador."""
    mascara = 0
    for i, gpio in enumerate(gpios):
        if (padrao_4bits >> i) & 1:
            mascara |= (1 << gpio)   # seta o bit correspondente ao GPIO
    return mascara

print("Sequenciador via mem32 — ida e volta")
sequencia = [1<<i for i in range(4)] + [1<<i for i in range(2,-1,-1)]

while True:
    for padrao in sequencia:
        mem32[GPIO_OUT] = montar_mascara(padrao)
        print(f"padrão={padrao:04b}  máscara={montar_mascara(padrao):032b}")
        time.sleep(0.15)
```

---

## 4. Experimento

Execute a **Parte A** e observe o sequenciador.

**a)** Complete a tabela de deslocamento:

| Expressão | Resultado binário | LED aceso |
|-----------|:-----------------:|:---------:|
| `1 << 0` | `0b____` | LED____ |
| `1 << 1` | `0b____` | LED____ |
| `1 << 2` | `0b____` | LED____ |
| `1 << 3` | `0b____` | LED____ |

**b)** Execute a **Parte B**. O comportamento visual é o mesmo que a Parte A?

> _________________________________________________________________

**c)** Na função `montar_mascara`, quando `padrao_4bits = 0b0101` e `gpio = 5` (índice `i=2`):
- `(padrao_4bits >> 2) & 1` = `_____`
- `1 << 5` = `_____` (decimal)
- Esse bit será setado na máscara? sim / não

**d)** Por que configurar os pinos com `Pin(gpio, Pin.OUT)` antes de usar `mem32` é necessário?

> _________________________________________________________________

---

## 5. Desafio

**Desafio principal:** modifique a Parte A para criar um **sequenciador bidirecional** que vai do LED0 ao LED3 e volta, sem repetir os extremos:

```
LED0 → LED1 → LED2 → LED3 → LED2 → LED1 → LED0 → ...
```

```python
# Dica: monte a sequência com uma lista de valores deslocados
sequencia = ___________________________________________
```

**Desafio bônus:** usando `mem32`, escreva no registrador de forma que dois LEDs acendam simultaneamente com um único `mem32[GPIO_OUT] = ...`. Qual é a máscara que acende GPIO2 e GPIO18 ao mesmo tempo?

```python
mascara = (1 << 2) | (1 << ____)   # complete
mem32[GPIO_OUT] = mascara
```

---

## Resumo da aula

- `1 << N` cria uma palavra com apenas o bit N em `1` — útil para selecionar pinos
- Deslocar um bit numa sequência produz o efeito de "caminhar" entre saídas
- `mem32[endereco]` permite escrever 32 bits de uma só vez no registrador de hardware
- A máscara de 32 bits para `GPIO_OUT` usa o número do GPIO como índice do bit
- Escrita direta é mais rápida e atômica — todos os pinos mudam no mesmo instante

---

*← [Aula 3](./aula03-listas-mascaras.md) | Próxima → [Aula 5: Flags e Máquina de Estados](./aula05-flags-estados.md)*
