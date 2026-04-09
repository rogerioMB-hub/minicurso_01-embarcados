# Sistemas Embarcados — Mini Curso: Lógica Digital com ESP32

> Estudo dirigido para alunos do Curso Técnico em Automação Industrial.

---

## Sobre o curso

Este material conduz o aluno desde a leitura e escrita digital básica no ESP32 até a manipulação de bytes via UART, passando por operadores bitwise, listas, estruturas de repetição, máscaras de bits e escrita direta em registrador de hardware.

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
├── aulas/
│   ├── aula01-leitura-escrita-digital.md
│   ├── aula02-operadores-bitwise.md
│   ├── aula03-listas-mascaras.md
│   ├── aula03-extra-funcoes.md
│   ├── aula04-deslocamento-escrita-porta.md
│   ├── aula05-flags-estados.md
│   └── aula06-uart-bytes.md
└── assets/
    └── wokwi-links.md
```

---

## Sequência de aulas

| # | Título | Operadores | Entregável |
|---|--------|------------|------------|
| [1](./aulas/aula01-leitura-escrita-digital.md) | Leitura e escrita digital | — | LED controlado por botão |
| [2](./aulas/aula02-operadores-bitwise.md) | Primeiro operador bitwise em hardware | `&` `\|` `~` | LED responde a combinação de 2 botões |
| [3](./aulas/aula03-listas-mascaras.md) | Listas de pinos e máscara de bits | `&` `\|` com máscara | Banco de 4 LEDs controlado por byte |
| [3 ★](./aulas/aula03-extra-funcoes.md) | **Extra:** Funções em MicroPython | — | Apoio para a Aula 3 |

> ★ Aula de apoio — leia antes da Aula 3 se o uso de `def` ainda não for familiar.
| [4](./aulas/aula04-deslocamento-escrita-porta.md) | Deslocamento e escrita direta em porta | `<<` `>>` `mem32` | Sequenciador de LEDs |
| [5](./aulas/aula05-flags-estados.md) | Flags e máquina de estados | `&=` `\|=` `^=` `~` | Sistema com estados visuais |
| [6](./aulas/aula06-uart-bytes.md) | Bytes pela UART | todos | Protocolo de comando por byte |

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
