# Sistemas Embarcados — Mini Curso: Lógica Digital com ESP32

![Banner do curso](./assets/banner.png)

> Estudo dirigido para alunos do Curso Técnico em Automação Industrial.

---

## Sobre o curso

Este material conduz o aluno desde a leitura e escrita digital básica no ESP32 até a comunicação serial via UART, passando por operadores bitwise, listas, estruturas de repetição, máscaras de bits, escrita direta em registrador de hardware, máquinas de estados e flags.

O objetivo é construir uma base sólida que conecte os conceitos teóricos de **portas lógicas** com a **programação real em hardware embarcado**.

---

## Plataforma

| Item | Especificação |
|------|---------------|
| Microcontrolador | ESP32 DevKit (principal) |
| Compatibilidade | Raspberry Pi Pico (instruções nos comentários do código) |
| Linguagem | MicroPython |
| Simulador | [Wokwi](https://wokwi.com) — nenhuma instalação necessária |

> **Nota Pico:** sempre que o código diferir para o Raspberry Pi Pico, a linha correspondente aparece em comentário com o prefixo `# Pico:`.

---

## Estrutura do repositório

```
minicurso-embarcados/
├── README.md
├── index.md
├── _config.yml
├── COMO-PUBLICAR.md
├── aulas/
│   ├── aula01-leitura-escrita-digital.md
│   ├── aula02-operadores-bitwise.md
│   ├── aula03-listas-mascaras.md
│   ├── aula03-extra-funcoes.md        ← apoio opcional
│   ├── aula04-deslocamento-escrita-porta.md
│   ├── aula05-maquina-estados.md
│   ├── aula06-flags-maquina-lavar.md
│   ├── aula07-uart-primeiros-bytes.md
│   └── aula08-uart-nibbles.md
└── assets/
    ├── banner.png
    ├── banner.svg
    ├── esp32_loopback_aula7.jpg
    └── wokwi-links.md
```

---

## Sequência de aulas

| # | Título | Operadores | Entregável |
|---|--------|------------|------------|
| [1](./aulas/aula01-leitura-escrita-digital.md) | Leitura e escrita digital | — | LED controlado por botão |
| [2](./aulas/aula02-operadores-bitwise.md) | Primeiro operador bitwise em hardware | `&` `\|` `~` | LED responde a combinação de 2 botões |
| [3](./aulas/aula03-listas-mascaras.md) | Listas de pinos e máscara de bits | `&` `\|` com máscara | Banco de 4 LEDs controlado por byte |
| [3 ★](./aulas/aula03-extra-funcoes.md) | **Extra:** Funções em MicroPython | — | Apoio opcional para a Aula 3 |
| [4](./aulas/aula04-deslocamento-escrita-porta.md) | Deslocamento e escrita direta em porta | `<<` `>>` `mem32` | Sequenciador de LEDs |
| [5](./aulas/aula05-maquina-estados.md) | Máquinas de estados: do diagrama ao código | — | Semáforo com 3 estados |
| [6](./aulas/aula06-flags-maquina-lavar.md) | Flags na prática: máquina de lavar | `\|=` `&=~` `^=` `&` | Sistema com 4 flags simultâneas |
| [7](./aulas/aula07-uart-primeiros-bytes.md) | UART: primeiros bytes | — | LED controlado via serial |
| [8](./aulas/aula08-uart-nibbles.md) | UART: protocolo de nibbles | `>>` `&` | Comando + argumento em um byte |

> ★ Aula de apoio — leia antes da Aula 3 se o uso de `def` ainda não for familiar.

---

## Como usar

1. Acesse [wokwi.com](https://wokwi.com) e crie um projeto com **ESP32** e **MicroPython**.
2. Monte o circuito conforme descrito em cada aula (componentes listados na seção **Circuito**).
3. Copie o código da seção **Código** para o editor do Wokwi.
4. Execute, observe e responda as perguntas da seção **Experimento**.
5. Tente o **Desafio** antes de passar para a próxima aula.

---

## Pré-requisito

Saber criar um projeto no Wokwi com ESP32 e abrir o editor MicroPython.
