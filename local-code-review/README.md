# local-code-review Skill

Skill portátil para uso com Claude Code e Codex.

## Conteúdo

- `SKILL.md`: instruções compartilhadas da skill. O formato segue o padrão Agent Skills e pode ser usado nos dois ambientes.

## Instalação no Claude Code

Copie a pasta `local-code-review` para:

```text
.claude/skills/local-code-review/
```

Estrutura esperada:

```text
.claude/
└── skills/
    └── local-code-review/
        └── SKILL.md
```

## Instalação no Codex

Copie a pasta `local-code-review` para:

```text
$CODEX_HOME/skills/local-code-review/
```

Se `CODEX_HOME` não estiver definido, o diretório padrão normalmente é:

```text
~/.codex/skills/local-code-review/
```

Estrutura esperada:

```text
.codex/
└── skills/
    └── local-code-review/
        └── SKILL.md
```

## Uso

Acione a skill `local-code-review` e, se desejar, informe a intenção da mudança como argumento/contexto.

Exemplo:

```text
/local-code-review Implementar validação de permissões no cadastro de usuários.
```

O nome `local-code-review` evita conflito com o comando `/code-review` nativo do Claude Code. A skill tem `disable-model-invocation: true`, então só roda quando você chama explicitamente.

A skill usa o diff em stage já fornecido pelo ambiente, quando disponível. Caso contrário, solicita/obtém o diff usando apenas comandos Git de leitura.

## Integração com SpecForge

A tabela principal de achados mantém as colunas:

`ID | Severidade | Arquivo:linha | Causa | Impacto | Correção`

Cada achado recebe um identificador sequencial `CR-001`, `CR-002` etc. Esses dados podem ser usados pelo `spec-forge.md` para gerar uma SPEC independente por achado selecionado.

## Segurança

A skill é somente leitura. Ela não executa testes, lint, typecheck ou build e não modifica arquivos, stage ou histórico Git. Esses comandos aparecem apenas como recomendações para execução manual pelo autor.
