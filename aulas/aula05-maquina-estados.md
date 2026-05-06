---
layout: default
title: "Aula 5 — Máquinas de Estados: Do Diagrama ao Código"
---

# Aula 5 — Máquinas de Estados: Do Diagrama ao Código

> **Duração estimada:** 30 minutos
> **Bloco:** 3 de 3 — Integração e preparação para UART

---

## Objetivos

Ao final desta aula você será capaz de:

- Explicar o que é uma máquina de estados com suas próprias palavras
- Ler e interpretar um diagrama de estados
- Identificar estados, transições e condições em um sistema real
- Traduzir um diagrama de estados diretamente para código MicroPython

---

## 1. Conceito

### O que é uma máquina de estados?

Pense em um elevador. Ele está sempre em um estado bem definido: **parado**, **subindo** ou **descendo**. Ele nunca está subindo e descendo ao mesmo tempo. E ele só muda de estado quando algo acontece — alguém aperta um botão, ele chega no andar.

Isso é uma **máquina de estados finitos**: um sistema que:

- Está sempre em **um único estado por vez**
- Só muda de estado quando uma **condição** é satisfeita
- Ao mudar, executa uma **ação** determinada

Três perguntas que definem qualquer máquina de estados:

| Pergunta | Nome técnico |
|----------|-------------|
| Em que estado estou agora? | Estado atual |
| O que pode acontecer aqui? | Evento ou condição |
| Para onde vou se isso acontecer? | Transição |

---

### Representando com um diagrama

Usamos um **grafo** para visualizar a máquina de estados:

- Cada **círculo** representa um estado
- Cada **seta** representa uma transição
- O **rótulo da seta** indica a condição que a dispara
- O estado inicial é indicado por uma seta sem origem

Exemplo mínimo — uma lâmpada:

```
          [btn]           [btn]
            ──►  APAGADA  ──────►  ACESA
               ▲                     |
               |                     |
                ─────────────────────
```

Apenas dois estados, uma condição para cada sentido. Simples — mas já é uma máquina de estados completa.

---

### O semáforo como máquina de estados

Você já conhece o semáforo. Agora vamos lê-lo como máquina de estados:

```
        ┌─────────────────────────────────────────┐
        │              [btn pressionado]          │
        │                                         │
        ▼                                         │
  ┌───────────┐   [2 segundos]   ┌────────────┐   │
  │  VERMELHO │                  │   AMARELO  │   │
  │  LED: ●   │                  │  LED: ●    │   │
  └───────────┘                  └────────────┘   │
        │                             ▲           │
        │   [btn pressionado]         │           │
        │                    [2 seg]  │           │
        ▼                             │           │
  ┌───────────┐ ──────────────────────┘           │
  │   VERDE   │                                   │
  │  LED: ●   │ ──────────────────────────────────┘
  └───────────┘        [btn pressionado]
```

Lendo o diagrama:

| Estado | O que faz | Condição de saída | Próximo estado |
|--------|-----------|:-----------------:|---------------|
| VERDE | LED verde aceso | botão pressionado | AMARELO |
| AMARELO | LED amarelo aceso | 2 segundos | VERMELHO |
| VERMELHO | LED vermelho aceso | botão pressionado | VERDE |

Dois tipos de transição aparecem aqui:
- **Por evento:** botão pressionado — o sistema aguarda uma ação externa
- **Por tempo:** 2 segundos — o sistema avança sozinho após um período

---

## 2. Do diagrama ao código

O diagrama vira código de forma direta. Cada estado vira um bloco `if/elif`, e cada seta vira uma atribuição `estado = NOVO_ESTADO`:

```python
# constantes para os estados — facilita leitura
VERDE    = 0
AMARELO  = 1
VERMELHO = 2

estado = VERDE      # estado inicial (seta sem origem no diagrama)

while True:
    if estado == VERDE:
        # ações do estado VERDE
        # ...
        if condicao:            # rótulo da seta de saída
            estado = AMARELO    # transição — seta no diagrama

    elif estado == AMARELO:
        # ações do estado AMARELO
        # ...
        if condicao:
            estado = VERMELHO

    elif estado == VERMELHO:
        # ações do estado VERMELHO
        # ...
        if condicao:
            estado = VERDE
```

> **Regra prática:** conte as setas que saem de cada estado no diagrama.
> Cada uma vira um `if` dentro do bloco `elif estado ==` correspondente.

---

## 3. Circuito

| Componente | Quantidade |
|------------|------------|
| ESP32 DevKit | 1 |
| LED vermelho | 1 |
| LED amarelo | 1 |
| LED verde | 1 |
| Resistor 220 Ω | 3 |
| Botão (pushbutton) | 1 |
| Resistor 10 kΩ | 1 |

**Conexões:**

```
GPIO2  ──► R220 ──► LED Verde    ──► GND
GPIO4  ──► R220 ──► LED Amarelo  ──► GND
GPIO5  ──► R220 ──► LED Vermelho ──► GND

GPIO13 ──► Botão ──► 3,3V
GPIO13 ──► R10k  ──► GND   (pull-down)
```

```
# Pico: led_verde  = Pin(0, ...)  | led_amarelo = Pin(1, ...)
# Pico: led_verm   = Pin(2, ...)  | btn         = Pin(14, ...)
```

---

## 4. Código

```python
from machine import Pin
import time

# --- pinos ---
led_verde    = Pin(2,  Pin.OUT)
led_amarelo  = Pin(4,  Pin.OUT)
led_vermelho = Pin(5,  Pin.OUT)
btn          = Pin(13, Pin.IN)    # pull-down externo

# Pico: led_verde = Pin(0, Pin.OUT) | led_amarelo = Pin(1, Pin.OUT)
# Pico: led_vermelho = Pin(2, Pin.OUT) | btn = Pin(14, Pin.IN)

# --- constantes de estado ---
VERDE    = 0
AMARELO  = 1
VERMELHO = 2

# --- funções auxiliares ---
def apagar_todos():
    """Apaga todos os LEDs — chamada antes de acender o do estado atual."""
    led_verde.value(0)
    led_amarelo.value(0)
    led_vermelho.value(0)

def aplicar_leds(estado):
    """Acende apenas o LED correspondente ao estado atual."""
    apagar_todos()
    if estado == VERDE:
        led_verde.value(1)
    elif estado == AMARELO:
        led_amarelo.value(1)
    elif estado == VERMELHO:
        led_vermelho.value(1)

def btn_pressionado():
    """Retorna True se o botão está pressionado.
    O sleep de 20ms evita leituras falsas por bouncing."""
    if btn.value():
        time.sleep_ms(20)       # debounce
        return btn.value()      # confirma após estabilizar
    return False

# --- máquina de estados ---
estado = VERDE      # estado inicial
aplicar_leds(estado)
print("Semáforo iniciado — estado: VERDE")

while True:

    # ── VERDE ──────────────────────────────────────────────────────
    if estado == VERDE:
        # transição por evento: botão avança para AMARELO
        if btn_pressionado():
            estado = AMARELO
            aplicar_leds(estado)
            print("Transição: VERDE → AMARELO")
            time.sleep_ms(200)      # aguarda soltar o botão

    # ── AMARELO ────────────────────────────────────────────────────
    elif estado == AMARELO:
        # transição por tempo: 2 segundos avançam para VERMELHO
        time.sleep(2)
        estado = VERMELHO
        aplicar_leds(estado)
        print("Transição: AMARELO → VERMELHO  (timeout 2s)")

    # ── VERMELHO ───────────────────────────────────────────────────
    elif estado == VERMELHO:
        # transição por evento: botão volta para VERDE
        if btn_pressionado():
            estado = VERDE
            aplicar_leds(estado)
            print("Transição: VERMELHO → VERDE")
            time.sleep_ms(200)      # aguarda soltar o botão

    time.sleep_ms(50)   # cadência do loop principal
```

---

## 5. Circuito Wokwi — diagram.json

Cole o conteúdo abaixo no arquivo `diagram.json` do seu projeto Wokwi (sempre confira se está correto):

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados — Aula 5",
  "editor": "wokwi",
  "parts": [
    { "type": "wokwi-esp32-devkit-v1", "id": "esp", "top": 0, "left": 0, "attrs": {} },
    { "type": "wokwi-led", "id": "led_v", "top": 6, "left": 253.4, "attrs": { "color": "green" } },
    {
      "type": "wokwi-led",
      "id": "led_a",
      "top": 6,
      "left": 291.8,
      "attrs": { "color": "yellow" }
    },
    { "type": "wokwi-led", "id": "led_r", "top": 6, "left": 330.2, "attrs": { "color": "red" } },
    {
      "type": "wokwi-resistor",
      "id": "rv",
      "top": 89.8,
      "left": 248.75,
      "rotate": 270,
      "attrs": { "value": "220" }
    },
    {
      "type": "wokwi-resistor",
      "id": "ra",
      "top": 99.4,
      "left": 287.15,
      "rotate": 270,
      "attrs": { "value": "220" }
    },
    {
      "type": "wokwi-resistor",
      "id": "rr",
      "top": 118.6,
      "left": 325.55,
      "rotate": 270,
      "attrs": { "value": "220" }
    },
    { "type": "wokwi-pushbutton", "id": "btn", "top": 179, "left": 230.4, "attrs": {} },
    {
      "type": "wokwi-resistor",
      "id": "rpd",
      "top": 147.4,
      "left": 143.15,
      "rotate": 270,
      "attrs": { "value": "10000" }
    }
  ],
  "connections": [
    [ "esp:2", "rv:1", "green", [] ],
    [ "rv:2", "led_v:A", "green", [] ],
    [ "led_v:K", "esp:GND.1", "black", [] ],
    [ "esp:4", "ra:1", "yellow", [] ],
    [ "ra:2", "led_a:A", "yellow", [] ],
    [ "led_a:K", "esp:GND.1", "black", [] ],
    [ "esp:5", "rr:1", "red", [] ],
    [ "rr:2", "led_r:A", "red", [] ],
    [ "led_r:K", "esp:GND.1", "black", [] ],
    [ "esp:13", "btn:1.l", "blue", [] ],
    [ "btn:2.l", "esp:3V3", "red", [ "h-96", "v-39.2", "h-33.1" ] ],
    [ "btn:1.l", "rpd:1", "blue", [] ],
    [ "rpd:2", "esp:GND.1", "black", [ "v-8.4", "h-19.2", "v33.8" ] ],
    [ "rv:1", "esp:D2", "green", [ "v19.2", "h-28.8", "v-67.2", "h-115.2", "v53.6" ] ],
    [ "ra:1", "esp:D4", "gold", [ "v19.2", "h-76.8", "v-48", "h-115.2", "v14.4" ] ],
    [ "rr:1", "esp:D5", "red", [ "v9.6", "h-124.8", "v-76.8", "v4.9" ] ],
    [ "esp:GND.2", "led_r:C", "black", [ "h-24.2", "v-168.2", "h355.2", "v86.4", "h9.6" ] ],
    [ "led_a:C", "esp:GND.2", "black", [ "v19.2", "h-9.2", "v-86.4", "h-316.8", "v19.2" ] ],
    [ "led_v:C", "esp:GND.2", "black", [ "v19.2", "h-9.2", "v-86.4", "h-278.4", "v163.2", "v5" ] ]
  ],
  "dependencies": {}
}
```

---

## 6. Experimento

Execute o código e observe o comportamento do semáforo.

**a)** Desenhe abaixo o diagrama de estados do semáforo conforme o comportamento que você observou — sem olhar para o diagrama da seção 1:

```
  Estado inicial: __________

  (  ________  ) ──[ condição: __________ ]──► (  ________  )
                                                      │
                                          [ condição: __________ ]
                                                      │
                                                      ▼
                                               (  ________  )
                                                      │
                                          [ condição: __________ ]
                                                      │
                                                      └──────────────► ...
```

**b)** No estado AMARELO o código usa `time.sleep(2)` e então muda de estado. Por que isso é diferente de como o estado VERDE e VERMELHO funcionam?

> _________________________________________________________________
> _________________________________________________________________

**c)** Retire o `time.sleep_ms(20)` dentro de `btn_pressionado()` e teste. O que acontece? Por que o debounce é necessário?

> _________________________________________________________________
> _________________________________________________________________

**d)** O código usa `apagar_todos()` antes de acender o LED do novo estado. O que aconteceria se você não fizesse isso?

> _________________________________________________________________

---

## 7. Desafio

**Desafio principal:** adicione um quarto estado `PISCA` entre AMARELO e VERMELHO. Nesse estado o LED amarelo pisca 3 vezes antes de avançar para VERMELHO:

```
VERDE ──[btn]──► AMARELO ──[2s]──► PISCA ──[3 piscadas]──► VERMELHO ──[btn]──► VERDE
```

```python
PISCA = 3   # nova constante de estado

# dentro do while True, adicione:
elif estado == PISCA:
    for _ in range(3):              # pisca 3 vezes
        led_amarelo.value(1)
        time.sleep(_____)
        led_amarelo.value(0)
        time.sleep(_____)
    estado = _________              # qual o próximo estado?
    aplicar_leds(estado)
```

**Desafio bônus:** modifique o estado VERMELHO para que, se o botão for mantido pressionado por mais de 3 segundos, o semáforo entre em modo `EMERGENCIA` — todos os LEDs piscam juntos indefinidamente até um reset:

```python
EMERGENCIA = 4

elif estado == EMERGENCIA:
    apagar_todos()
    time.sleep(0.3)
    # acende todos simultaneamente
    led_verde.value(1)
    led_amarelo.value(1)
    led_vermelho.value(1)
    time.sleep(0.3)
    # este estado não tem saída — só reset resolve
```

---

## Resumo da aula

- Uma máquina de estados está sempre em **um único estado por vez**
- **Nós** do diagrama = estados; **setas** = transições; **rótulos** = condições
- Transições podem ser disparadas por **eventos** (botão) ou **tempo** (timeout)
- No código: cada estado vira um `elif estado ==`; cada seta vira um `estado = NOVO`
- O diagrama e o código são representações diferentes da mesma lógica — aprenda a ler os dois

---

*← [Aula 4](./aula04-deslocamento-escrita-porta.md) | Próxima → [Aula 6: Flags e Controle de Estado](./aula06-flags-controle.md)*
