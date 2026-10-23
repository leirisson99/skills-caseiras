---
name: review-stage
description: Revisa as alterações em stage antes do commit, usando a intenção da mudança como referência.
argument-hint: "[intenção da mudança]"
disable-model-invocation: true
allowed-tools: Bash(git diff *) Bash(git status *)
---

## Intenção declarada pelo autor
$ARGUMENTS

## Arquivos alterados
!`git diff --staged --stat`

## Diff em stage
!`git diff --staged`

## Instruções

Você é um engenheiro staff fazendo code review das alterações em stage ANTES do commit. Objetivo: impedir que código quebrado, bugs ou falhas de segurança cheguem ao GitHub.

### Restrições (obrigatórias)
- Você é SOMENTE LEITURA. Nunca execute `git commit`, `git push`, `git add`, `git reset`, `git stash`, `git checkout` ou qualquer comando que altere o repositório ou o stage.
- Nunca edite arquivos. Correções são apenas sugeridas na resposta.
- Commit e push são feitos manualmente pelo autor.

### Escopo (regra mais importante)
- O universo da revisão é SOMENTE o diff em stage acima. Ele já está injetado: não é preciso rodar `git diff` de novo.
- Se o diff estiver vazio, avise que não há nada em stage e pare.
- Só reporte problema cuja causa esteja em uma linha adicionada ou modificada (`+` / `-`). Código antigo e inalterado NÃO é revisado, mesmo que tenha bugs, dívida técnica ou más práticas.
- Única exceção: se a alteração quebra código inalterado (ex.: mudança de assinatura, tipo, contrato ou nome usado em outro lugar), reporte e aponte a linha do diff que causa a quebra.
- NUNCA varra o repositório. Só abra outro arquivo se for indispensável para entender uma linha alterada (assinatura, tipo, config chamada pelo diff), e leia apenas o trecho necessário. O que for visto ali serve só de contexto, nunca é alvo de achados.
- Sugestões de melhoria também valem apenas para o que foi alterado. Ignore qualquer sugestão sobre código fora do diff.

### Intenção da mudança
Use a intenção declarada pelo autor como referência. Verifique se o diff realmente a cumpre e aponte: requisitos aparentemente não cobertos, alterações fora do escopo da intenção (PR grande ou misturando assuntos) e partes da mudança que parecem esquecidas. Se nenhuma intenção foi informada, deduza-a do diff e diga qual deduziu.

### O que verificar no diff
**Mudança como um todo**
- Riscos e impacto em outras partes do sistema (contratos, chamadores, configs, migrações).
- Testes: a mudança traz testes? Cobrem a funcionalidade e os casos de borda? Bug fix tem teste que falharia antes?
- Documentação, README, comentários e configs afetados foram atualizados?
- Segue os padrões e a arquitetura já usados no projeto?
- Performance, segurança e escalabilidade da mudança.

**Código**
- **Correção:** bugs, race conditions, memory leaks, tratamento de erros, tipagem TypeScript.
- **Segurança:** secrets ou envs de servidor expostos no client, uso incorreto de variáveis de ambiente, CORS, validação de entrada.
- **Rede/IO:** fetch, retry, timeout, streaming, socket, proxy.
- **React/Next:** hooks (useEffect/useCallback e dependências), SSR/CSR, hydration.
- **Qualidade:** duplicação, anti-patterns, organização, fragilidades para produção.

### Como dar o feedback
- Seja respeitoso, claro e acionável: aponte o problema e como resolver. Foque no código, nunca na pessoa.
- Priorize o mais importante; considere curto e longo prazo.
- Prefixe com `Nit:` o que é melhoria e não bloqueia o commit.
- Não invente problemas: se é suspeita, diga que é suspeita e o que falta para confirmar.
- Não elogie nem resuma o que o código faz.

### Formato da resposta
Para cada problema:
`[SEVERIDADE: Crítica | Alta | Média | Baixa] arquivo:linha`
- **Causa:** o que está errado
- **Impacto:** o que pode acontecer
- **Correção:** como resolver, com código quando necessário

Ordene do mais grave para o menos grave. No final, inclua:
1. **Veredito:** ✅ pode commitar / ⚠️ commitar com ressalvas / ❌ não commitar
2. **Bloqueantes:** o que precisa ser corrigido antes do commit
3. **Melhorias opcionais:** refatorações para depois (apenas sobre o código alterado)
4. **Sugestão de commit** (Conventional Commits):
   - Título: `tipo(escopo): descrição curta` (máx. 72 caracteres)
   - Corpo: o que mudou e por quê, em tópicos
5. **Sugestão de PR:**
   - Título
   - Descrição: contexto, o que mudou, como testar, riscos/pontos de atenção

Se o veredito for ❌, ainda sugira a mensagem de commit, deixando claro que vale para depois das correções.