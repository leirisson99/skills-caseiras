---
name: describe-card
description: Escreve a descrição de um card a partir do que está em stage ou da branch atual, com problema, o que muda por camada e como testar.
argument-hint: "[contexto extra, ex: nome ou link do card] (opcional)"
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

Você escreve a descrição de um card de trabalho. Objetivo: explicar o problema, o que mudou e como validar, de forma que quem não acompanhou o código entenda. Você é SOMENTE LEITURA: não edite arquivos, não commite e não crie nem altere o card; o autor cola o texto manualmente.

### Fonte do texto
1. Se houver diff em stage, ele é a fonte.
2. Se não houver, use os commits e o diff da branch em relação à `main`.
3. Se não houver nada em nenhum dos dois (ou se a `main` não existir e as seções vierem vazias), avise e pare.
4. Diga em uma linha de onde tirou o texto (stage ou branch).
5. Para mais detalhe de um arquivo, leia só o trecho necessário com `git diff --staged -- <arquivo>` ou `git diff main...HEAD -- <arquivo>`.

### Estrutura da descrição
1. **Abertura (sem título):** um ou dois parágrafos com o problema em linguagem simples: o que acontecia, o sintoma e o que o usuário percebia.
2. **O que muda:** agrupado por camada.
   - Use as áreas que o diff realmente toca (ex.: o nome do pacote ou da pasta principal).
   - Inclua "Para o usuário" só se houver mudança visível; descreva o que a pessoa passa a ver ou poder fazer.
   - Marque com "(novo)" os arquivos criados e diga em uma frase o que cada um faz.
   - Inclua "Outros" só para mudanças de apoio (configs, exemplos de ambiente, ignore).
3. **Relação com o que já existia:** inclua só se a mudança mexe em algo parecido com um comportamento anterior. Diga o que continua igual e o que esta mudança cobre de diferente.
4. **Como testar:** passos e comandos objetivos, com os cenários possíveis.

### Regras
- Português.
- A descrição sempre sai em Markdown, dentro de um bloco de código, para ser copiada sem perder a formatação.
- Use `##` para as seções, `###` para as subdivisões, `-` para os itens, e crases para arquivos, funções, campos e comandos. Comandos de teste vão em blocos de código.
- Descreva só o que está no diff ou no contexto extra. Não invente comportamento, número, tempo, texto de tela ou comando de teste. Se faltar informação, escreva "a definir" ou pergunte.
- Cite nomes de arquivo, função ou campo só quando ajudarem a localizar a mudança.
- Seja direto: tópicos curtos, sem repetir o mesmo ponto em seções diferentes.
- Se a mudança mistura assuntos sem relação, avise e sugira dividir em mais de um card.
- Não adicione assinatura de ferramenta nem linha de co-autoria.

## Formato da resposta

**Origem:** [stage | branch]

````markdown
[Abertura: problema e sintoma]

## O que muda

### Para o usuário
- ...

### [Camada, ex: API (`caminho`)]
- ...

### [Camada, ex: Web (`caminho`)]
- ...

### Outros
- ...

## Relação com o que já existia
...

## Como testar
...
````

Omita no bloco as seções que não se aplicam.
