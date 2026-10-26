---
name: resumo-daily
description: Gera um resumo curto e em linguagem simples do que foi feito, para usar na daily e defender entregas e tempo gasto.
argument-hint: "[período, ex: 3 dias] (opcional)"
disable-model-invocation: true
allowed-tools: Bash(git log *) Bash(git status *) Bash(git diff *) Bash(git config *) Bash(git -C * log *) Bash(git -C * status *) Bash(git -C * diff *) Bash(find ~/Documentos/github *) Bash(date *)
---

## Período pedido
$ARGUMENTS

## Dia da semana (1 = segunda)
!`date +%u`

## Autor
!`git config --global user.name`

## Repositórios encontrados
!`find ~/Documentos/github -maxdepth 3 -name .git -type d`

## Instruções

Você é a pessoa autora do trabalho preparando sua fala para a daily. Objetivo: um resumo SUPERFICIAL e organizado, que ajude a explicar o que foi entregue e onde o tempo foi gasto, para quem não é técnico.

### Passos

1. Defina o período:
   - Se houver período em "Período pedido", use-o.
   - Senão, use as últimas 24 horas; se hoje for segunda (dia da semana = 1), use desde a sexta-feira.
2. Em cada repositório da lista, busque os commits do autor no período (`git -C <repo> log --author=<autor> --since=<período> --oneline`) e o que está alterado e ainda não commitado (`git -C <repo> status --short` e `git -C <repo> diff --stat`).
3. Ignore repositórios sem nenhuma atividade no período.
4. Junte commits parecidos em um único item e traduza cada um para o resultado que gerou (o que passou a funcionar, foi corrigido ou ficou melhor).
5. Trabalho não commitado entra como "em andamento".

### Regras

- Português, frases curtas e linguagem simples, sem jargão técnico.
- Foque no que foi entregue e no valor disso, não em como foi feito.
- Não cite hash de commit, nome de arquivo, função ou tecnologia, a menos que seja indispensável para entender.
- Não invente trabalho que não aparece no git.
- Seja breve: 2 a 5 itens por bloco, uma linha cada.
- Se não houver atividade em nenhum repositório, diga isso em vez de preencher o resumo.
- Não altere nada em nenhum repositório; use apenas comandos de leitura.

## Formato da resposta

### Projeto: [nome do projeto]
**O que entreguei**
- [item]

**Em andamento**
- [item]

**Bloqueios**
- [só inclua este bloco se houver algum; senão omita]

Repita o bloco "Projeto" para cada repositório com atividade.
