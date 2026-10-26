---
name: titulo-e-descricao
description: Sugere o título do commit e o título e a descrição do PR a partir do que está em stage ou da branch atual.
argument-hint: "[contexto extra] (opcional)"
disable-model-invocation: true
allowed-tools: Bash(git diff --staged *) Bash(git diff main...HEAD *) Bash(git log main..HEAD *) Bash(git branch --show-current)
---

## Contexto extra do autor
$ARGUMENTS

## Branch atual
!`git branch --show-current`

## Arquivos em stage
!`git diff --staged --stat`

## Diff em stage
!`git diff --staged`

## Commits da branch em relação à main
!`git log main..HEAD --format=%s`

## Arquivos alterados na branch em relação à main
!`git diff main...HEAD --stat`

## Instruções

Você escreve mensagens de commit e PR. Objetivo: sugerir textos claros e fiéis ao que mudou, prontos para copiar. Você é SOMENTE LEITURA: nunca execute `git commit`, `git push`, `git add`, nem crie o PR; o autor faz isso manualmente.

### Fonte do texto
1. Se houver diff em stage, ele é a fonte do **commit**.
2. Para o **PR**, a fonte são os commits e o diff da branch em relação à `main`. Se a seção de commits estiver vazia, use o diff em stage.
3. Se não houver nada em stage nem na branch (ou se a `main` não existir e as seções vierem vazias), avise e pare.
4. Diga em uma linha de onde tirou o texto (stage ou branch).
5. Se precisar de mais detalhe de um arquivo específico, leia só o trecho necessário com `git diff --staged -- <arquivo>` ou `git diff main...HEAD -- <arquivo>`.

### Regras
- Português, como no histórico do projeto.
- Descreva só o que está no diff. Não invente motivo, teste ou impacto; se faltar contexto, use o "Contexto extra do autor" ou pergunte.
- Não liste arquivos um a um; descreva o que mudou e por quê.
- Se a mudança mistura assuntos sem relação, avise e sugira dividir em mais de um commit ou PR, com um título para cada.
- Não adicione linha de co-autoria nem assinatura de ferramenta.
- Títulos de commit e de PR e nomes de branch nunca levam acentos, `ç` nem caracteres especiais (ex: escreva `descricao`, não `descrição`). Use só letras sem acento, números, espaços e, em nomes de branch, hífen e `/`. Se sugerir um nome de branch, use minúsculas e hífens no lugar de espaços. O corpo do commit e a descrição do PR podem manter acentos normalmente.

### Commit
- Título no padrão Conventional Commits: `tipo(escopo): descrição curta`, no imperativo ou no estilo do histórico, com no máximo 72 caracteres.
- Tipos: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `style`, `build`, `ci`.
- Escopo: o módulo ou a pasta principal afetada; omita se não houver um claro.
- Corpo opcional, em tópicos curtos com o que mudou e por quê. Só inclua se o título não bastar.

### PR
- Título no mesmo padrão do commit; se a branch tem vários commits, resuma o conjunto, não o último.
- Descrição com estas seções:
  - **Contexto:** por que a mudança existe.
  - **O que mudou:** tópicos curtos.
  - **Como testar:** passos objetivos; se não houver como inferir, escreva "a definir".
  - **Riscos e pontos de atenção:** o que pode quebrar ou merece olhar extra; omita a seção se não houver nada.

## Formato da resposta

**Origem:** [stage | branch]

**Commit**
```
tipo(escopo): descrição curta

- [corpo opcional]
```

**PR**

Título: `tipo(escopo): descrição curta`

```
## Contexto
...

## O que mudou
- ...

## Como testar
- ...

## Riscos e pontos de atenção
- ...
```
