---
layout: default
title: Início
---

# Sistemas Embarcados — Lógica Digital com ESP32

![Banner do curso](./assets/banner.svg)

> **Mini Curso 01** — Estudo dirigido para alunos do Curso Técnico em Automação Industrial.  
> Plataforma: ESP32 com MicroPython · Simulador: [Wokwi](https://wokwi.com)  
> *Continuação: Mini Curso 02 — UART com MicroPython: Do Eco ao Protocolo*

---

## Sobre o curso

Este material conduz o aluno desde a leitura e escrita digital básica no ESP32 até a manipulação de bytes via UART, passando por operadores bitwise, listas, estruturas de repetição, máscaras de bits e escrita direta em registrador de hardware.

O objetivo é construir uma base sólida que conecte os conceitos teóricos de **portas lógicas** com a **programação real em hardware embarcado** — tudo simulado no Wokwi, sem necessidade de hardware físico — preparando o aluno para o Mini Curso 02, onde esses conceitos são aplicados em comunicação serial UART.

---

## Aulas

| # | Título | Operadores | Entregável |
|---|--------|------------|------------|
| [1](./aulas/aula01-leitura-escrita-digital) | Leitura e escrita digital | — | LED controlado por botão |
| [2](./aulas/aula02-operadores-bitwise) | Primeiro operador bitwise em hardware | `&` `\|` `~` | LED responde a 2 botões |
| [3](./aulas/aula03-listas-mascaras) | Listas de pinos e máscara de bits | `&` `\|` com máscara | Banco de 4 LEDs por byte |
| [3 ★](./aulas/aula03-extra-funcoes) | **Extra:** Funções em MicroPython | — | Apoio para a Aula 3 |
| [4](./aulas/aula04-deslocamento-escrita-porta) | Deslocamento e escrita direta em porta | `<<` `>>` `mem32` | Sequenciador de LEDs |
| [5](./aulas/aula05-flags-estados) | Flags e máquina de estados | `&=` `\|=` `^=` `~` | Sistema com estados visuais |
| [6](./aulas/aula06-uart-bytes) | Bytes pela UART | todos | Protocolo de comando por byte |

> ★ Aula de apoio — leia antes da Aula 3 se o uso de `def` ainda não for familiar.

---

## Como usar

1. Acesse [wokwi.com](https://wokwi.com) e crie um projeto **ESP32 + MicroPython**
2. Monte o circuito conforme a seção **Circuito** de cada aula
3. Copie o código para o editor e execute
4. Responda as perguntas da seção **Experimento** no seu caderno
5. Tente o **Desafio** antes de avançar para a próxima aula

> Templates de circuito prontos para o Wokwi estão em [assets/wokwi-links.md](./assets/wokwi-links.md).

---

## Pré-requisito

Saber criar um projeto no Wokwi com ESP32 e abrir o editor MicroPython.

---

## Próximo passo

Após concluir este mini-curso, continue com o **[Mini Curso 02 — UART com MicroPython: Do Eco ao Protocolo](https://rogerioMB-hub.github.io/minicurso_02-embarcados)**.

---

## Repositório

```
git clone https://github.com/rogerioMB-hub/minicurso-embarcados.git
```

Contribuições e correções são bem-vindas via *Issues* ou *Pull Requests*.
