---
layout: default
title: "Referências e Links Wokwi"
---

# Referências e Links para o Wokwi

## Como criar um projeto no Wokwi

1. Acesse [wokwi.com](https://wokwi.com)
2. Clique em **New Project**
3. Selecione **ESP32** → **MicroPython**
4. Monte o circuito na aba **Diagram**
5. Cole o código na aba **main.py**
6. Clique em **Play** para simular

---

## Mapeamento de pinos por aula

### ESP32 (principal)

| Função | GPIO | Aulas |
|--------|:----:|-------|
| LED0 — bit 0 | 2 | 1, 2, 3, 4, 6, 7, 8 |
| LED1 — bit 1 | 4 | 2, 3, 4, 6, 8 |
| LED2 — bit 2 | 5 | 3, 4, 6, 8 |
| LED3 — bit 3 | 18 | 3, 4, 6, 8 |
| Botão A (único) | 4 | 1 |
| Botão A (dois botões) | 13 | 2, 5, 6 |
| Botão B | 14 | 2, 6 |
| LED Verde (semáforo) | 2 | 5 |
| LED Amarelo (semáforo) | 4 | 5 |
| LED Vermelho (semáforo) | 5 | 5 |
| Botão semáforo | 13 | 5 |
| UART1 TX (loopback) | 4 | 7, 8 |
| UART1 RX (loopback) | 5 | 7, 8 |
| UART2 TX (loopback) | 17 | 7, 8 |
| UART2 RX (loopback) | 16 | 7, 8 |
| LED (loopback aula 7) | 2 | 7 |
| Botão (loopback aula 7) | 13 | 7 |

### Raspberry Pi Pico (alternativa)

| Função | GPIO |
|--------|:----:|
| LED0 | 0 |
| LED1 | 1 |
| LED2 | 2 |
| LED3 | 3 |
| Botão A | 14 |
| Botão B | 15 |
| UART0 TX | 0 |
| UART0 RX | 1 |

> Para o Pico, selecione **Raspberry Pi Pico** ao criar o projeto no Wokwi.
> O código MicroPython é idêntico — ajuste apenas os números dos pinos conforme a tabela acima.

---

## Templates de circuito por aula

> **Atenção:** os templates abaixo são pontos de partida.
> Os `diagram.json` validados pelo professor estão dentro de cada arquivo de aula —
> use-os preferencialmente.

---

### Aulas 1 e 2 — 1 LED + 1 ou 2 botões

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados",
  "editor": "wokwi",
  "parts": [
    { "type": "wokwi-esp32-devkit-v1", "id": "esp", "top": 0, "left": 0, "attrs": {} },
    { "type": "wokwi-led", "id": "led1", "top": 100, "left": 200, "attrs": { "color": "red" } },
    { "type": "wokwi-resistor", "id": "r1", "top": 140, "left": 200, "attrs": { "value": "220" } },
    { "type": "wokwi-pushbutton", "id": "btn1", "top": 100, "left": 350, "attrs": {} },
    { "type": "wokwi-resistor", "id": "r2", "top": 140, "left": 350, "attrs": { "value": "10000" } }
  ],
  "connections": [
    ["esp:2",  "r1:1",     "green", []],
    ["r1:2",   "led1:A",   "green", []],
    ["led1:K", "esp:GND.1","black", []],
    ["esp:4",  "btn1:1.l", "blue",  []],
    ["btn1:2.l","esp:3V3", "red",   []],
    ["btn1:1.l","r2:1",    "blue",  []],
    ["r2:2",   "esp:GND.1","black", []]
  ]
}
```

---

### Aulas 3 e 4 — 4 LEDs

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados",
  "editor": "wokwi",
  "parts": [
    { "type": "wokwi-esp32-devkit-v1", "id": "esp", "top": 0, "left": 0, "attrs": {} },
    { "type": "wokwi-led", "id": "led0", "top": 80, "left": 250, "attrs": { "color": "red"    } },
    { "type": "wokwi-led", "id": "led1", "top": 80, "left": 290, "attrs": { "color": "yellow" } },
    { "type": "wokwi-led", "id": "led2", "top": 80, "left": 330, "attrs": { "color": "green"  } },
    { "type": "wokwi-led", "id": "led3", "top": 80, "left": 370, "attrs": { "color": "blue"   } },
    { "type": "wokwi-resistor", "id": "r0", "top": 120, "left": 250, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r1", "top": 120, "left": 290, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r2", "top": 120, "left": 330, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r3", "top": 120, "left": 370, "attrs": { "value": "220" } }
  ],
  "connections": [
    ["esp:2",  "r0:1", "green", []], ["r0:2", "led0:A", "green", []], ["led0:K", "esp:GND.1", "black", []],
    ["esp:4",  "r1:1", "green", []], ["r1:2", "led1:A", "green", []], ["led1:K", "esp:GND.1", "black", []],
    ["esp:5",  "r2:1", "green", []], ["r2:2", "led2:A", "green", []], ["led2:K", "esp:GND.1", "black", []],
    ["esp:18", "r3:1", "green", []], ["r3:2", "led3:A", "green", []], ["led3:K", "esp:GND.1", "black", []]
  ]
}
```

---

### Aula 5 — Semáforo (diagram.json validado — use o da própria aula)

> O `diagram.json` validado está em [Aula 5](../aulas/aula05-maquina-estados).
> Projeto Wokwi: [https://wokwi.com/projects/462409529242615809](https://wokwi.com/projects/462409529242615809)

---

### Aula 6 — 4 LEDs + 2 botões (flags)

```json
{
  "version": 1,
  "author": "RMB - Mini Curso Embarcados",
  "editor": "wokwi",
  "parts": [
    { "type": "wokwi-esp32-devkit-v1", "id": "esp", "top": 0, "left": 0, "attrs": {} },
    { "type": "wokwi-led", "id": "led0", "top": 80, "left": 250, "attrs": { "color": "green"  } },
    { "type": "wokwi-led", "id": "led1", "top": 80, "left": 290, "attrs": { "color": "blue"   } },
    { "type": "wokwi-led", "id": "led2", "top": 80, "left": 330, "attrs": { "color": "yellow" } },
    { "type": "wokwi-led", "id": "led3", "top": 80, "left": 370, "attrs": { "color": "red"    } },
    { "type": "wokwi-resistor", "id": "r0", "top": 120, "left": 250, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r1", "top": 120, "left": 290, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r2", "top": 120, "left": 330, "attrs": { "value": "220" } },
    { "type": "wokwi-resistor", "id": "r3", "top": 120, "left": 370, "attrs": { "value": "220" } },
    { "type": "wokwi-pushbutton", "id": "btnL", "top": 180, "left": 250, "attrs": { "color": "green"  } },
    { "type": "wokwi-pushbutton", "id": "btnT", "top": 180, "left": 340, "attrs": { "color": "yellow" } },
    { "type": "wokwi-resistor", "id": "rpL", "top": 220, "left": 210, "attrs": { "value": "10000" } },
    { "type": "wokwi-resistor", "id": "rpT", "top": 220, "left": 300, "attrs": { "value": "10000" } }
  ],
  "connections": [
    ["esp:2",  "r0:1", "green",  []], ["r0:2", "led0:A", "green",  []], ["led0:K", "esp:GND.1", "black", []],
    ["esp:4",  "r1:1", "blue",   []], ["r1:2", "led1:A", "blue",   []], ["led1:K", "esp:GND.1", "black", []],
    ["esp:5",  "r2:1", "yellow", []], ["r2:2", "led2:A", "yellow", []], ["led2:K", "esp:GND.1", "black", []],
    ["esp:18", "r3:1", "red",    []], ["r3:2", "led3:A", "red",    []], ["led3:K", "esp:GND.1", "black", []],
    ["esp:13", "btnL:1.l", "green",  []], ["btnL:2.l", "esp:3V3", "red", []], ["btnL:1.l", "rpL:1", "green",  []], ["rpL:2", "esp:GND.1", "black", []],
    ["esp:14", "btnT:1.l", "yellow", []], ["btnT:2.l", "esp:3V3", "red", []], ["btnT:1.l", "rpT:1", "yellow", []], ["rpT:2", "esp:GND.1", "black", []]
  ]
}
```

---

### Aulas 7 e 8 — Loopback UART (diagram.json validado — use o da própria aula)

> O `diagram.json` validado da Aula 7 está em [Aula 7](../aulas/aula07-uart-primeiros-bytes).
> Projeto Wokwi: [https://wokwi.com/projects/462409529242615809](https://wokwi.com/projects/462409529242615809)

Esquema do loopback interno para referência:

```
UART1 TX (GPIO 4)  ──────► UART2 RX (GPIO 16)
UART2 TX (GPIO 17) ──────► UART1 RX (GPIO 5)
UART0              ──────► $serialMonitor  (monitor serial — não usar para dados)
```

---

## Restrições importantes do Wokwi

| Situação | Problema | Solução |
|----------|----------|---------|
| Comunicação serial | UART0 reservada ao monitor | Usar UART1 + UART2 em loopback |
| Leitura serial no terminal | `uart.any()` falha com UART0 | Usar `input()` |
| Circuitos gerados automaticamente | Conexões frequentemente incompletas | Validar no Wokwi antes de publicar |

---

## Referências

- [Documentação MicroPython — machine.Pin](https://docs.micropython.org/en/latest/library/machine.Pin.html)
- [Documentação MicroPython — machine.UART](https://docs.micropython.org/en/latest/library/machine.UART.html)
- [ESP32 Technical Reference Manual — GPIO](https://www.espressif.com/sites/default/files/documentation/esp32_technical_reference_manual_en.pdf)
- [Wokwi ESP32 + MicroPython](https://docs.wokwi.com/pt-BR/guides/micropython)
- [Wokwi Raspberry Pi Pico](https://docs.wokwi.com/pt-BR/parts/wokwi-pi-pico)
