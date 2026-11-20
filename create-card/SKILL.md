---
name: create-card
description: Cria cards de trabalho em docs/, um por pasta, a partir de uma ideia, documento ou PRD, com problema, critérios de aceite e plano técnico baseado no código do projeto.
argument-hint: "<ideia, ou caminho de um documento/PRD>"
disable-model-invocation: true
allowed-tools: Read Grep Glob
---

## Pedido
$ARGUMENTS

## Instruções

Você transforma uma ideia ou um documento em cards de trabalho. Objetivo: cada card deve permitir que alguém (ou um agente de IA) execute a tarefa mais tarde, sem ter visto esta conversa.

Você só planeja. Escreva apenas dentro de `docs/`; não implemente a tarefa, não altere código e não faça commit.

### 1. Entender o pedido
- Se o pedido estiver vazio, pergunte o que a pessoa quer transformar em card e pare.
- Se for um caminho de arquivo (PRD, anotações, especificação), leia o arquivo inteiro. Se o arquivo não existir, avise e pare; não deduza o conteúdo pelo nome.

### 2. Olhar o código e os cards existentes
- A raiz do projeto é a raiz do repositório git, ou a pasta atual se não houver git.
- Leia o `CLAUDE.md` e o `README` se existirem.
- Procure só o que o pedido toca: pontos de entrada, módulos, modelos de dados, testes e alguma implementação parecida que sirva de referência. Não varra o projeto inteiro.
- Veja os cards que já estão em `docs/`. Se algum já cobre o pedido, no todo ou em parte, avise antes de criar outro.
- Se o projeto ainda não tiver código, siga em frente e diga no plano técnico que não há base para citar arquivos.

Investigar antes de perguntar evita perguntas que o próprio repositório já responde.

### 3. Perguntar o que falta
Pergunte só o que mudaria o card: comportamento esperado, regra de negócio, o que fica de fora, escolha técnica com consequência. Faça no máximo 4 perguntas, de uma vez, com uma sugestão de resposta em cada. Se não faltar nada, pule este passo.

O que a pessoa não souber responder entra em "Pontos em aberto". Não invente regra de negócio: ela vira requisito sem ninguém ter decidido.

### 4. Propor a divisão e esperar o ok
Um card é uma entrega que dá para revisar em um PR e testar sozinha. Divida por resultado ("usuário consegue redefinir a senha"), não por camada ("banco", "API", "tela"), porque cards por camada não podem ser testados isoladamente. Não divida o que já é pequeno.

Mostre a lista e espere a confirmação antes de criar qualquer arquivo, mesmo que seja um card só:

| Nº | Pasta | Título | O que entrega | Depende de |
|----|-------|--------|---------------|------------|

Se a pessoa pedir ajustes, mostre a lista de novo.

### 5. Criar os arquivos
Cada card ganha sua própria pasta, com um `card.md` dentro:

```
docs/
├── 001-cadastro-de-usuario/
│   └── card.md
└── 002-login-com-email/
    └── card.md
```

- Crie `docs/` na raiz do projeto se não existir.
- Nome da pasta: `NNN-titulo-curto`, em minúsculas, com hífens, sem acentos nem `ç`.
- `NNN` continua o maior número já usado nas pastas de card em `docs/`; comece em `001`. Outras pastas e arquivos de `docs/` não contam e não devem ser mexidos.
- Nunca sobrescreva um `card.md` existente. Para mudar um card, só quando a pessoa pedir, edite o trecho necessário.
- Numere na ordem em que os cards devem ser feitos.

### Modelo do `card.md`

```markdown
# NNN - Título do card

**Depende de:** [pastas dos cards anteriores, ou "nenhum"]
**Origem:** [ideia descrita na conversa, ou caminho do documento]

## Problema
O que acontece hoje ou o que falta, quem é afetado. Linguagem simples.

## Objetivo
O que passa a ser possível quando o card estiver pronto.

## Critérios de aceite
- [ ] CA1: ...
- [ ] CA2: ...

## Fora de escopo
- ...

## Plano técnico

### Contexto no código
- `caminho/arquivo`: o que existe ali e por que importa

### Passos
1. ...

### Como testar
- CA1: passo ou comando e o resultado esperado

## Pontos em aberto
- ...
```

Omita "Fora de escopo" e "Pontos em aberto" quando não houver o que dizer.

### Regras do conteúdo
- Português.
- Cada critério de aceite descreve um comportamento que dá para verificar com sim ou não. "Senha com menos de 8 caracteres é recusada com mensagem de erro" serve; "validação funcionando bem" não.
- Inclua os casos de erro e de limite que importam, não só o caminho feliz.
- Todo critério aparece em "Como testar".
- Cite apenas arquivos e funções que você viu no projeto. Marque o que ainda vai ser criado com "(novo)".
- Não invente comando de teste: use os que o projeto já tem, ou escreva "a definir".
- Tamanho proporcional à tarefa: uma correção pequena rende um card curto.
- Cada card se explica sozinho; não escreva "como conversamos" nem dependa do texto de outro card para ser entendido.
- Não adicione assinatura de ferramenta nem linha de co-autoria.

### 6. Responder
Liste os cards criados com o link de cada `card.md`, a ordem sugerida e os pontos em aberto que a pessoa precisa decidir. Não repita o conteúdo dos cards no chat.
