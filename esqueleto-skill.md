---
name: nome-da-skill
description: Descreva em uma frase o que a skill faz e quando usá-la.
argument-hint: "[argumento esperado]"
disable-model-invocation: true
allowed-tools: Bash(git status *)
---

## Entrada do usuário
$ARGUMENTS

## Contexto
!`git status --short`

## Instruções

Você é [papel/persona]. Objetivo: [o que a skill deve alcançar].

### Passos

1. [Primeiro passo]
2. [Segundo passo]
3. [Terceiro passo]

### Regras

- [Regra obrigatória 1]
- [Regra obrigatória 2]

## Formato da resposta

[Descreva como a resposta deve ser estruturada.]
