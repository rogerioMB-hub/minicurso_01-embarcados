---
layout: default
title: "Aula 3 — Listas de Pinos e Máscara de Bits"
---

# Aula 3 — Listas de Pinos e Máscara de Bits

> **Duração estimada:** 30 minutos  
> **Bloco:** 2 de 3 — Listas, laços e lógica combinatória

---

## Objetivos

Ao final desta aula você será capaz de:

- Representar um banco de LEDs como uma lista de pinos
- Usar um único byte para controlar múltiplas saídas
- Extrair bits individuais de uma palavra binária com máscara e shift
- Usar `for` para varrer pinos e aplicar lógica bitwise em cada um

---

## 1. Conceito

Nas aulas anteriores controlamos um LED por vez. Agora vamos tratar **um grupo de pinos como uma palavra de bits**.

Imagine 4 LEDs conectados aos GPIOs 2, 4, 5 e 18. Podemos representar o estado de todos eles com um único número de 4 bits:

```
Palavra:    0b 1 0 1 1
Pino:          18 5 4 2
```

Para saber o estado do pino correspondente ao **bit N**, usamos:

```python
bit = (palavra >> N) & 1
```

Isso é uma **máscara de extração**: deslocamos a palavra para a direita até o bit desejado ficar na posição 0, e então isolamos esse bit com `& 1`.

| Operação | O que faz |
|----------|-----------|
| `palavra >> N` | Move o bit N para a posição 0 |
| `& 1` | Descarta todos os bits exceto o bit 0 |
| `& 0b1111` | Mantém apenas os 4 bits menos significativos |

---

## 2. Circuito

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit | 1 |
| LED | 4 |
| Resistor 220 Ω | 4 |

**Conexões:**

```
GPIO2  ──► R220 ──► LED0 ──► GND   (bit 0, menos significativo)
GPIO4  ──► R220 ──► LED1 ──► GND   (bit 1)
GPIO5  ──► R220 ──► LED2 ──► GND   (bit 2)
GPIO18 ──► R220 ──► LED3 ──► GND   (bit 3, mais significativo)
```

```
# Pico: use GPIO 0, 1, 2, 3 no lugar de 2, 4, 5, 18
```

---

## 3. Código

> **Nota:** o código desta aula usa funções definidas com `def`. Se esse conceito
> ainda não é familiar, leia a [Aula 3 — Extra: Funções em MicroPython](./aula03-extra-funcoes.md)
> antes de continuar — ela é curta e vai deixar o código abaixo completamente claro.

### Parte A — escrevendo um padrão binário nos LEDs

```python
from machine import Pin
import time

# lista de pinos: índice 0 = bit menos significativo
pinos = [
    Pin(2,  Pin.OUT),   # bit 0
    Pin(4,  Pin.OUT),   # bit 1
    Pin(5,  Pin.OUT),   # bit 2
    Pin(18, Pin.OUT),   # bit 3
]
# Pico: pinos = [Pin(0,Pin.OUT), Pin(1,Pin.OUT), Pin(2,Pin.OUT), Pin(3,Pin.OUT)]

def escrever_palavra(palavra):
    """Aplica os 4 bits de 'palavra' nos 4 LEDs."""
    for i, pino in enumerate(pinos):
        bit = (palavra >> i) & 1   # extrai o bit i
        pino.value(bit)

# --- experimento: escreva padrões e observe os LEDs ---
padroes = [0b0001, 0b0010, 0b0100, 0b1000,   # um LED por vez
           0b1010, 0b0101,                     # alternados
           0b1111, 0b0000]                     # todos acesos / apagados

for padrao in padroes:
    print(f"Palavra: {padrao:04b} ({padrao})")
    escrever_palavra(padrao)
    time.sleep(0.8)
```

---

### Parte B — contador binário de 0 a 15

```python
while True:
    for valor in range(16):             # 0b0000 até 0b1111
        escrever_palavra(valor)
        print(f"{valor:2d} = 0b{valor:04b}")
        time.sleep(0.4)
```

---

## 4. Experimento

Execute a **Parte A** e preencha a tabela observando os LEDs:

| Palavra (binário) | LED3 | LED2 | LED1 | LED0 | Decimal |
|:-----------------:|:----:|:----:|:----:|:----:|:-------:|
| `0b0001` | __ | __ | __ | __ | 1 |
| `0b0010` | __ | __ | __ | __ | 2 |
| `0b0100` | __ | __ | __ | __ | 4 |
| `0b1000` | __ | __ | __ | __ | 8 |
| `0b1010` | __ | __ | __ | __ | __ |
| `0b0101` | __ | __ | __ | __ | __ |
| `0b1111` | __ | __ | __ | __ | __ |

**Perguntas:**

**a)** Qual padrão binário é necessário para acender apenas LED1 e LED3?

> `0b_______` = decimal `_____`

**b)** Acompanhe a linha `escrever_palavra` no código. Quando `i = 2` e `palavra = 0b1010`:
- `palavra >> 2` = `0b_______`  
- `(palavra >> 2) & 1` = `_____`  
- O LED2 vai ficar: aceso / apagado?

**c)** Execute a **Parte B**. Qual padrão visual você observa nos LEDs à medida que o contador avança?

> _________________________________________________________________

---

## 5. Desafio

**Desafio principal:** modifique o contador para exibir **apenas os valores pares** de 0 a 14 nos LEDs.

```python
# Dica: um número é par quando seu bit 0 vale 0.
# Como verificar isso com um operador bitwise?
for valor in range(16):
    if ___________:        # condição bitwise para número par
        escrever_palavra(valor)
        time.sleep(0.5)
```

**Desafio bônus:** crie uma função `ler_palavra()` que leia 4 botões e retorne o valor binário formado por eles. Use a operação inversa: `palavra |= (btn.value() << i)`.

---

## Resumo da aula

- Uma **lista de pinos** permite tratar um banco de I/O como uma palavra de bits
- `(palavra >> i) & 1` extrai o bit na posição `i`
- `& mascara` preserva apenas os bits que a máscara tem em `1`
- Um `for enumerate()` percorre pinos e índices simultaneamente
- O valor binário de 4 bits pode representar 16 estados distintos (0 a 15)

---

*← [Aula 2](./aula02-operadores-bitwise.md) | Próxima → [Aula 4: Deslocamento e Escrita Direta em Porta](./aula04-deslocamento-escrita-porta.md)*
