---
layout: default
title: "Aula 3 (Extra) — Funções em MicroPython"
---

# Aula 3 (Extra) — Funções em MicroPython

> **Duração estimada:** 20 minutos  
> **Quando usar:** leia esta aula antes da Aula 3 se o código com `def` pareceu confuso.

---

## Por que esta aula existe?

Na Aula 3 o código usa uma **função** chamada `escrever_palavra()`. Se você nunca criou uma função em Python ou MicroPython, esta aula explica o conceito com exemplos simples antes de você voltar para a Aula 3.

---

## 1. O que é uma função?

Uma função é um bloco de código com um nome. Você o escreve uma vez e pode executá-lo quantas vezes quiser, só chamando o nome.

Sem função — código repetido:

```python
# acender LED
led.value(1)
time.sleep(0.5)
led.value(0)

# acender LED de novo
led.value(1)
time.sleep(0.5)
led.value(0)

# acender LED mais uma vez
led.value(1)
time.sleep(0.5)
led.value(0)
```

Com função — código escrito uma vez, chamado três vezes:

```python
def piscar():
    led.value(1)
    time.sleep(0.5)
    led.value(0)

piscar()   # primeira vez
piscar()   # segunda vez
piscar()   # terceira vez
```

---

## 2. Estrutura de uma função

```python
def nome_da_funcao(parametro):
    # corpo da função — indentado com 4 espaços
    instrucao_1
    instrucao_2
```

| Parte | O que é |
|-------|---------|
| `def` | palavra reservada que inicia a definição |
| `nome_da_funcao` | nome que você escolhe — use letras minúsculas e `_` |
| `(parametro)` | valor que a função recebe para trabalhar — pode ser omitido com `()` |
| corpo indentado | as instruções que a função executa |

---

## 3. Função sem parâmetro

```python
from machine import Pin
import time

led = Pin(2, Pin.OUT)

def acender():
    led.value(1)

def apagar():
    led.value(0)

# chamadas
acender()
time.sleep(1)
apagar()
```

A função não precisa de nenhuma informação externa — ela sempre faz a mesma coisa.

---

## 4. Função com parâmetro

Um parâmetro é uma variável que a função recebe no momento em que é chamada:

```python
def piscar_n_vezes(n):
    for _ in range(n):
        led.value(1)
        time.sleep(0.2)
        led.value(0)
        time.sleep(0.2)

piscar_n_vezes(3)   # pisca 3 vezes
piscar_n_vezes(5)   # pisca 5 vezes
```

Quando você chama `piscar_n_vezes(3)`, o valor `3` vai para dentro da função como `n`.

---

## 5. A função da Aula 3 — explicada linha a linha

Na Aula 3 você encontrará esta função:

```python
def escrever_palavra(palavra):
    for i, pino in enumerate(pinos):
        bit = (palavra >> i) & 1
        pino.value(bit)
```

Traduzindo cada linha:

| Linha | Significado |
|-------|-------------|
| `def escrever_palavra(palavra):` | define a função; recebe um número inteiro chamado `palavra` |
| `for i, pino in enumerate(pinos):` | percorre a lista `pinos`; `i` é o índice (0,1,2,3) e `pino` é cada objeto Pin |
| `bit = (palavra >> i) & 1` | extrai o bit na posição `i` da palavra |
| `pino.value(bit)` | aplica esse bit (0 ou 1) no pino correspondente |

Quando você chama `escrever_palavra(0b1010)`, o número `0b1010` entra como `palavra` e a função distribui seus bits pelos LEDs.

---

## 6. Experimento

Monte o circuito da Aula 1 (1 LED no GPIO2) e execute:

```python
from machine import Pin
import time

led = Pin(2, Pin.OUT)

def definir_led(valor):
    """Liga o LED se valor=1, desliga se valor=0."""
    led.value(valor)

# teste
definir_led(1)   # acende
time.sleep(1)
definir_led(0)   # apaga
time.sleep(1)

# chamando com variável
for v in [1, 0, 1, 0, 1]:
    definir_led(v)
    time.sleep(0.3)
```

**Perguntas:**

**a)** O que acontece se você chamar `definir_led(5)`? Teste e registre:

> _________________________________________________________________

**b)** Reescreva o laço `for` acima sem usar a função `definir_led` — usando `led.value()` diretamente. Qual versão você considera mais legível?

> _________________________________________________________________

---

## Pronto — volte para a Aula 3

Agora que você entende o que `def` faz e como passar um parâmetro, o código da Aula 3 vai fazer sentido imediato.

→ [Voltar para a Aula 3](./aula03-listas-mascaras.md)
