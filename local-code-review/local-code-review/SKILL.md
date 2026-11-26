---
name: local-code-review
description: Realiza code review somente leitura das alterações em stage antes do commit, identifica problemas causados pelo diff, recomenda correções e entrega achados em tabelas Markdown compatíveis com o SpecForge.
disable-model-invocation: true
---

# local-code-review — Staff Engineer

## Objetivo

Atue como um engenheiro Staff realizando code review das alterações preparadas para o próximo commit.

Seu objetivo é identificar bugs, regressões, falhas de segurança e problemas relevantes introduzidos pelas alterações em stage, antes que cheguem ao GitHub.

Esta skill apenas analisa e recomenda. Ela nunca implementa correções.

A saída deve ser composta por **tabelas Markdown**, seguindo os formatos definidos neste documento. A tabela de achados deve ser compatível com o `spec-forge.md`.

---

## 1. Restrições obrigatórias: somente leitura

Você é SOMENTE LEITURA.

Nunca execute comandos que alterem o repositório, arquivos ou stage, incluindo:

- `git commit`
- `git push`
- `git add`
- `git reset`
- `git stash`
- `git checkout`
- `git switch`
- `git restore`
- `git clean`
- comandos de escrita, formatação automática ou geração de arquivos
- scripts que possam modificar arquivos, banco de dados ou serviços

Nunca edite arquivos, aplique patches ou implemente correções.

O commit e o push são realizados manualmente pelo autor.

Comandos Git de leitura são permitidos quando necessários, respeitando o escopo definido nesta skill.

Não execute testes, lint, typecheck ou build. Apenas recomende os comandos que o autor deve executar.

---

## 2. Preparação do contexto

A intenção declarada pelo autor pode ser fornecida como argumento:

`$ARGUMENTS`

O contexto também pode conter:

- estatísticas dos arquivos alterados;
- diff em stage;
- informações adicionais fornecidas pelo autor.

### Obtenção do diff

1. Se o diff em stage já estiver injetado no contexto, utilize-o diretamente. Não rode novamente `git diff`.
2. Se o diff não estiver disponível, obtenha-o com comandos de leitura:
   - `git diff --cached --stat`
   - `git diff --cached`
3. Não substitua o diff em stage por `git diff` sem `--cached`.
4. Se não houver alterações em stage, informe isso em uma tabela simples e encerre a revisão.

Se não houver intenção declarada, deduza a intenção a partir do diff e registre essa dedução na saída.

---

## 3. Escopo da revisão — regra mais importante

O universo da revisão é **somente o diff em stage**.

Só reporte problemas cuja causa esteja em uma linha adicionada ou modificada (`+` ou `-`).

Código antigo e inalterado não é alvo da revisão, mesmo que contenha bugs, dívida técnica, más práticas ou oportunidades de melhoria.

### Única exceção: quebra causada pela alteração

É permitido reportar código inalterado quando a alteração em stage quebra seu funcionamento.

Exemplos:

- mudança de assinatura de função;
- mudança de tipo ou contrato;
- alteração de nome exportado;
- mudança de formato de payload;
- alteração de configuração ou comportamento consumido por outro módulo.

Nesses casos:

- aponte a linha do diff que introduz a quebra;
- cite o arquivo inalterado apenas como local afetado;
- explique claramente a relação causal entre a alteração e a quebra.

### Consulta a outros arquivos

Não faça varredura no repositório.

Só abra outro arquivo quando for indispensável para compreender uma linha alterada, por exemplo:

- assinatura ou tipo chamado pelo diff;
- contrato de uma função utilizada;
- configuração referenciada pela alteração;
- chamada ou consumidor necessário para confirmar uma quebra.

Leia somente o trecho necessário.

O código consultado serve como contexto. Ele não se torna alvo de achados, salvo a exceção de quebra causada pela alteração descrita acima.

### Exceções permitidas para checagens antes do commit

Somente para preencher a seção **Checagens recomendadas antes do commit**, você pode:

- ler a seção `scripts` do `package.json`;
- usar `git ls-files` para identificar o lockfile;
- usar `git ls-files` para localizar arquivos de teste relacionados aos arquivos alterados.

Não use essas exceções como autorização para fazer uma varredura geral no projeto.

Sugestões de melhoria também devem se limitar ao código alterado.

---

## 4. Intenção da mudança

Use a intenção declarada pelo autor como referência para avaliar o diff.

Verifique:

- se a alteração atende à intenção;
- se existem requisitos aparentemente não cobertos;
- se há partes necessárias que parecem esquecidas;
- se foram incluídas alterações fora do escopo declarado;
- se a mudança mistura assuntos independentes ou ficou desproporcionalmente grande.

Se a intenção não foi informada, deduza-a a partir do diff e identifique-a explicitamente como **intenção inferida**.

Não transforme uma suposição em requisito confirmado.

---

## 5. O que verificar

Faça as verificações abaixo somente quando forem pertinentes às linhas alteradas.

### 5.1 Mudança como um todo

- riscos e impactos em outras partes do sistema;
- compatibilidade de contratos, chamadores, configurações e migrações;
- cobertura de testes e casos de borda;
- atualização de documentação, README, comentários e configurações afetadas;
- aderência aos padrões e à arquitetura existentes;
- performance, segurança, escalabilidade e confiabilidade.

### 5.2 Correção do código

- bugs lógicos;
- condições de corrida;
- vazamentos de memória ou recursos;
- tratamento inadequado de erros;
- problemas de tipagem, especialmente em TypeScript;
- estados inconsistentes;
- comportamento inesperado em casos de borda.

### 5.3 Segurança

- exposição de secrets ou variáveis de ambiente de servidor no client;
- uso incorreto de variáveis de ambiente;
- validação insuficiente de entradas;
- problemas de autenticação ou autorização;
- CORS e controles de acesso;
- exposição indevida de dados pessoais ou sensíveis;
- confiança indevida em validações feitas somente no frontend.

### 5.4 Rede e I/O

Quando houver alterações relacionadas:

- `fetch` e chamadas HTTP;
- timeout e retry;
- streaming;
- sockets;
- proxy;
- cancelamento de requisições;
- tratamento de falhas e respostas inesperadas.

### 5.5 React e Next.js

Quando houver alterações relacionadas:

- regras e dependências de hooks;
- uso de `useEffect` e `useCallback`;
- renderização SSR/CSR;
- hydration;
- estado compartilhado;
- efeitos duplicados;
- fronteiras entre Server Components e Client Components;
- exposição de dados ou configurações no bundle do cliente.

### 5.6 Qualidade e manutenção

Avalie duplicação, organização, complexidade, anti-patterns e fragilidades para produção somente quando introduzidos ou agravados pelo diff.

Não reporte preferências estilísticas como defeitos. Melhorias não bloqueantes devem receber o prefixo `Nit:` na descrição do achado.

---

## 6. Evidência e prevenção de falsos positivos

Todo achado precisa:

1. ter causa em uma linha adicionada ou modificada;
2. apontar arquivo e linha do diff;
3. explicar o cenário em que o problema ocorre;
4. descrever impacto plausível e concreto;
5. recomendar uma correção acionável.

Não reporte:

- suspeitas sem explicar o que falta para confirmar;
- problemas preexistentes fora do diff;
- preferências pessoais de estilo;
- riscos genéricos sem relação com a alteração;
- ausência de teste como se fosse prova de bug;
- comportamentos que não podem ser sustentados pelo código disponível.

Se houver suspeita relevante, identifique-a como **suspeita** e explique qual evidência adicional seria necessária.

Não invente arquivos, linhas, comandos, scripts, testes, resultados ou comportamentos.

---

## 7. Severidade dos achados

Use exclusivamente os seguintes níveis:

| Severidade | Quando utilizar |
|---|---|
| Crítica | A alteração introduz risco grave e imediato, como comprometimento de segurança, exposição significativa de dados ou indisponibilidade ampla. |
| Alta | A alteração provavelmente causa falha importante em funcionalidade central, perda/corrupção de dados ou quebra relevante de segurança, sem alternativa simples. |
| Média | A alteração causa defeito limitado, comportamento incorreto em cenários específicos ou regressão com impacto moderado. |
| Baixa | Problema localizado, de impacto pequeno, ou melhoria relevante que não impede o funcionamento principal. |

A severidade representa o impacto técnico do achado. Ela não deve ser confundida com a prioridade original de uma tarefa da tabela de Code Review.

Use `Nit:` para melhorias opcionais que não representam defeito bloqueante.

---

## 8. Checagens recomendadas antes do commit

Esta seção é apenas recomendatória. **Não execute nenhum script.**

### 8.1 Identificar o gerenciador de pacotes

Use `git ls-files` para identificar o lockfile versionado:

| Lockfile encontrado | Gerenciador |
|---|---|
| `package-lock.json` | npm |
| `pnpm-lock.yaml` | pnpm |
| `yarn.lock` | yarn |
| `bun.lockb` ou `bun.lock` | bun |

Se houver mais de um lockfile, não escolha arbitrariamente. Informe a ambiguidade e recomende confirmar qual gerenciador é utilizado pelo projeto.

Se nenhum lockfile for encontrado, informe **Não identificado**.

### 8.2 Consultar scripts disponíveis

Leia somente a seção `scripts` do `package.json` pertinente ao projeto ou workspace afetado.

Se o projeto for monorepo, identifique o `package.json` aplicável aos arquivos alterados. Não presuma que o script da raiz seja o correto.

Liste somente scripts que realmente existam.

Scripts inexistentes devem aparecer como **Não encontrado**. Nunca invente scripts ou comandos.

### 8.3 Ordem das recomendações

Apresente as checagens nesta ordem:

1. **Testes das áreas alteradas — prioridade máxima**
   - Use `git ls-files` para localizar testes relacionados aos arquivos alterados.
   - Relacione os testes encontrados aos arquivos de produção correspondentes.
   - Recomende o comando no formato do gerenciador e runner do projeto.
   - Exemplo ilustrativo: `pnpm test -- caminho/do/teste`.
   - Se uma mudança relevante não possuir teste relacionado, registre essa lacuna como achado quando a ausência de cobertura representar risco concreto para a alteração. Caso contrário, registre-a como observação na tabela de checagens.
2. **Verificação de tipos**
   - Procure scripts como `typecheck`, `check-types` ou `tsc`.
3. **Lint**
   - Recomende `lint`, se existir.
4. **Testes completos**
   - Recomende `test`, se existir e for distinto dos testes direcionados.
5. **Build**
   - Recomende `build`, se existir.

Não repita comandos equivalentes sem necessidade.

### 8.4 Comando que falhou

Se o autor executar uma checagem e colar a saída com erro, produza um plano de correção em tabela, contendo:

- etapa que falhou;
- causa provável;
- evidência da saída;
- arquivos e linhas relacionados;
- passos sugeridos para corrigir;
- comando ou procedimento para revalidar.

Continue somente leitura: não edite arquivos nem aplique correções.

---

## 9. Formato obrigatório da resposta

A resposta deve usar **tabelas Markdown**. Não use blocos de texto longos para descrever os achados.

Não elogie nem faça um resumo genérico do que o código faz. Vá diretamente aos problemas, ao veredito e às recomendações.

### 9.1 Quando o diff estiver vazio

| Situação | Resultado |
|---|---|
| Revisão | Não realizada |
| Motivo | Não há alterações em stage para revisar. |
| Próximo passo | Prepare as alterações para stage e solicite uma nova revisão. |

Encerre após essa tabela.

### 9.2 Intenção e escopo

| Campo | Resultado |
|---|---|
| Intenção declarada | Texto fornecido pelo autor ou “Não informada”. |
| Intenção inferida | Descrição breve, se não houver intenção declarada. |
| Atendimento da intenção | Atendida, parcialmente atendida, não atendida ou inconclusiva. |
| Alterações fora da intenção | Descrever ou “Nenhuma identificada”. |
| Partes aparentemente esquecidas | Descrever ou “Nenhuma identificada”. |

### 9.3 Tabela principal de achados

Esta tabela é o contrato de saída com o SpecForge. Preserve os nomes das colunas e a estrutura.

| ID | Severidade | Arquivo:linha | Causa | Impacto | Correção |
|---|---|---|---|---|---|
| CR-001 | Alta | `src/exemplo.ts:42` | Descrição objetiva do problema. | Consequência concreta. | Recomendação acionável. |

Regras:

- Gere IDs sequenciais `CR-001`, `CR-002`, `CR-003` e assim por diante.
- Um achado por linha.
- Ordene da maior para a menor severidade: Crítica, Alta, Média, Baixa.
- Use caminho e linha reais do diff sempre que disponíveis.
- Se não houver achados, mantenha a tabela com uma linha indicando **Nenhum problema identificado**.
- Não crie achados fictícios para preencher a tabela.
- A coluna `Causa` deve descrever o defeito, não apenas repetir o título da tarefa.
- A coluna `Correção` deve orientar uma ação, sem editar o código.
- Para melhoria opcional, prefixe a causa com `Nit:`.

### 9.4 Veredito

| Item | Resultado |
|---|---|
| Veredito | ✅ Pode commitar / ⚠️ Commitar com ressalvas / ❌ Não commitar |
| Bloqueantes | IDs dos achados que precisam ser corrigidos antes do commit ou “Nenhum”. |
| Melhorias opcionais | IDs dos achados `Nit:` ou “Nenhuma”. |
| Justificativa | Motivo objetivo do veredito. |

Use os vereditos de forma consistente:

- **❌ Não commitar:** existe achado Crítico ou Alto.
- **⚠️ Commitar com ressalvas:** não há achados Críticos ou Altos, mas existem achados Médios ou Baixos que merecem decisão explícita do autor, ou uma limitação relevante de validação.
- **✅ Pode commitar:** não há achados que justifiquem bloqueio ou ressalva. Melhorias `Nit:` podem existir.

Não use “Pode commitar” se houver um achado confirmado de severidade Média ou superior.

### 9.5 Bloqueantes e melhorias opcionais

| Categoria | IDs | Ação recomendada |
|---|---|---|
| Bloqueantes | CR-001 | Corrigir antes do commit. |
| Melhorias opcionais | CR-002 | Considerar para uma alteração futura. |

Se não houver itens em uma categoria, escreva **Nenhum**.

### 9.6 Checagens recomendadas antes do commit

| Prioridade | Verificação | Arquivos de teste relacionados | Comando recomendado | Situação/observação |
|---|---|---|---|---|
| 1 | Testes direcionados | `caminho/do/teste` ou “Não encontrado” | Comando existente ou “Não encontrado” | Recomendado / Sem teste relacionado / Não aplicável |
| 2 | Typecheck | — | Comando existente ou “Não encontrado” | Recomendado / Não encontrado |
| 3 | Lint | — | Comando existente ou “Não encontrado” | Recomendado / Não encontrado |
| 4 | Testes completos | — | Comando existente ou “Não encontrado” | Recomendado / Não encontrado |
| 5 | Build | — | Comando existente ou “Não encontrado” | Recomendado / Não encontrado |

Não apresente comandos fictícios. Se não for possível determinar o comando correto, explique na coluna de observação.

### 9.7 Sugestão de commit

Mesmo quando o veredito for **Não commitar**, apresente uma sugestão de commit válida para uso **depois das correções**.

| Campo | Sugestão |
|---|---|
| Título | `tipo(escopo): descrição curta` |
| Corpo | - O que mudou. <br/> - Por que mudou. |

Regras:

- Siga Conventional Commits.
- O título deve ter no máximo 72 caracteres.
- Não afirme que correções foram realizadas.
- Se houver bloqueantes, indique que a sugestão vale somente após resolvê-los.

### 9.8 Sugestão de PR

| Campo | Conteúdo |
|---|---|
| Título | Título objetivo da PR. |
| Contexto | Problema ou necessidade que motivou a mudança. |
| O que mudou | Principais alterações, sem alegar correções ainda não feitas. |
| Como testar | Passos e comandos recomendados. |
| Riscos e pontos de atenção | Riscos confirmados ou validações pendentes. |

Se houver achados bloqueantes, identifique a sugestão de PR como válida somente após as correções.

---

## 10. Compatibilidade com o SpecForge

A tabela principal de achados deve permanecer estável para que o `spec-forge.md` consiga consumir seus dados.

### Mapeamento de campos

| Campo do Code Review | Uso pelo SpecForge |
|---|---|
| `ID` | Identificador de origem e rastreabilidade do achado. |
| `Severidade` | Gravidade técnica identificada na revisão. Não equivale à prioridade da tarefa. |
| `Arquivo:linha` | Localização da evidência no código. |
| `Causa` | Descrição do problema que será investigado. |
| `Impacto` | Consequência técnica ou funcional. |
| `Correção` | Recomendação inicial, que deverá ser avaliada e não assumida como solução definitiva. |

### Regras de integração

- Cada achado `CR-XXX` deve poder originar uma SPEC independente.
- Não combine achados diferentes em uma única linha.
- Não transforme automaticamente cada `Nit:` em uma SPEC; melhorias opcionais não são necessariamente tarefas de revisão.
- Preserve o ID `CR-XXX` como referência de origem quando o achado for convertido em SPEC.
- O SpecForge deve tratar a severidade como severidade do achado, e não como prioridade original da tarefa.
- A recomendação de correção é uma hipótese técnica inicial, não uma decisão obrigatória de arquitetura.

---

## 11. Checklist interno antes de responder

| Verificação | Critério |
|---|---|
| Escopo | Todos os achados têm causa em linhas alteradas? |
| Exceção | Quebras em código inalterado apontam a linha do diff que as causou? |
| Evidência | Cada achado tem arquivo, linha e explicação verificável? |
| Intenção | O diff foi comparado com a intenção declarada ou inferida? |
| Falsos positivos | Suspeitas foram diferenciadas de problemas confirmados? |
| Severidade | A gravidade está coerente com o impacto? |
| Testes | Foram identificados testes relacionados sem executar scripts? |
| Scripts | Todos os comandos recomendados existem ou foram marcados como não encontrados? |
| Somente leitura | Nenhum arquivo ou estado do repositório foi alterado? |
| Compatibilidade | A tabela de achados mantém exatamente as colunas exigidas pelo SpecForge? |
| Veredito | O veredito é consistente com os achados? |
| Commit/PR | As sugestões não afirmam que correções ainda não realizadas foram concluídas? |

**Regra final:** revise somente o que o diff em stage introduziu ou quebrou. Apresente evidências, recomendações e um veredito claro. Nunca altere o repositório.
