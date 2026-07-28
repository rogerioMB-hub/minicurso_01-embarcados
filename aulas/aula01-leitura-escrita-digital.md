---
layout: default
title: "Aula 1 — Leitura e Escrita Digital no ESP32"
---

# Aula 1 — Leitura e Escrita Digital no ESP32

> **Duração estimada:** 30 minutos  
> **Bloco:** 1 de 3 — Fundamentos digitais

---

## Objetivos

Ao final desta aula você será capaz de:

- Configurar pinos como entrada e saída usando MicroPython
- Ler o estado de um botão e acionar um LED
- Entender que o valor digital é um único bit: `0` ou `1`
- Relacionar os estados elétricos com valores binários

---

## 1. Conceito

Um pino digital do ESP32 só conhece dois estados:

| Estado | Tensão | Valor | Representação |
|--------|--------|-------|---------------|
| LOW    | 0 V    | `0`   | `0b0`         |
| HIGH   | 3,3 V  | `1`   | `0b1`         |

Esse único bit é a unidade mínima de informação digital. Tudo que faremos nas próximas aulas — máscaras, flags, protocolos — é construído sobre ele.

> **Conexão com teoria:** um pino em HIGH é equivalente ao nível lógico `1` nas tabelas-verdade das portas lógicas. Um pino em LOW equivale ao nível `0`.

---

## 2. Circuito

Monte no Wokwi com os seguintes componentes:

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit | 1 |
| LED (qualquer cor) | 1 |
| Resistor 220 Ω | 1 |
| Botão (pushbutton) | 1 |
| Resistor 10 kΩ | 1 |

**Conexões:**

```
ESP32 GPIO2  ──► Resistor 220Ω ──► LED (+) ──► GND
ESP32 GPIO4  ──► Botão ──► 3,3V
ESP32 GPIO4  ──► Resistor 10kΩ ──► GND   (pull-down)
```

> O resistor de 10 kΩ garante que o pino leia `0` quando o botão não está pressionado,
> evitando leituras flutuantes.

*Link do Wokwi → [Minicurso 01 - aula 01-wokwi-botão e led](https://wokwi.com/projects/470798465049406465)*

---

## 3. Código

Copie o código abaixo para o editor do Wokwi e execute.

```python
from machine import Pin
import time

# Pico: from machine import Pin  (idêntico)

# --- configuração dos pinos ---
led = Pin(2, Pin.OUT)   # GPIO2 como saída
btn = Pin(4, Pin.IN)    # GPIO4 como entrada
# Pico: led = Pin(15, Pin.OUT)
# Pico: btn = Pin(14, Pin.IN)

# --- loop principal ---
while True:
    estado_botao = btn.value()   # lê 0 ou 1
    led.value(estado_botao)      # escreve 0 ou 1 no LED

    # exibe no terminal para observarmos o bit
    print("botão =", estado_botao, " | LED =", estado_botao)
    time.sleep(0.1)
```

**O que cada linha faz:**

| Linha | Explicação |
|-------|------------|
| `Pin(2, Pin.OUT)` | Declara GPIO2 como saída digital |
| `Pin(4, Pin.IN)` | Declara GPIO4 como entrada digital |
| `btn.value()` | Retorna `0` ou `1` conforme o estado do botão |
| `led.value(estado_botao)` | Aplica esse valor diretamente no pino do LED |

---

## 4. Experimento

Execute o código e responda:

**a)** Com o botão solto, qual valor aparece no terminal? `_____`

**b)** Com o botão pressionado, qual valor aparece? `_____`

**c)** O valor que aparece no terminal é o mesmo valor elétrico no pino. Escreva esse valor em binário:

- Botão solto → `0b_____`  
- Botão pressionado → `0b_____`

**d)** Observe que `led.value(estado_botao)` não usa `if`. Explique com suas palavras por que isso funciona:

> _________________________________________________________________  
> _________________________________________________________________

---

## 5. Desafio

Modifique o código para **inverter a lógica**: o LED deve ficar **aceso quando o botão estiver solto** e **apagar quando for pressionado**.

**Dica:** em Python, `not valor` inverte um booleano. Como você faria o mesmo com um inteiro `0` ou `1`?

```python
# complete aqui:
estado_invertido = ___________
led.value(estado_invertido)
```

> **Para pensar:** existe uma operação aritmética simples que transforma `0` em `1` e `1` em `0`?
> Na próxima aula veremos o operador que faz exatamente isso — e que tem um nome especial em lógica digital.

---

## Resumo da aula

- `Pin.OUT` → pino de saída; `Pin.IN` → pino de entrada
- `.value()` lê ou escreve um bit: `0` ou `1`
- O valor digital é diretamente o nível lógico da porta — sem conversão
- Manipular o bit diretamente (sem `if`) é o caminho para a lógica bitwise

---

*Próxima aula → [Aula 2: Seu Primeiro Operador Bitwise em Hardware](./aula02-operadores-bitwise.md)*
