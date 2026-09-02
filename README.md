# AI Platform — Repositório Padrão

Esse repositório é um template para APIs com arquitetura DDD/hexagonal, com regras de engenharia de software para APIs e uma versão adaptada para AI Platform. 
Além disso, contém regras de linting (`pyproject.toml`) estritas para automatizar mais checks de conformidade do software.

## Regras de código

- `docs/llm/software-engineering-rules.md`
  - Regras de software da Google + recomendações de PEP8, para APIs
  - https://google.github.io/styleguide/pyguide.html
  - \+ https://realpython.com/python-pep8/#when-to-ignore-pep-8
- `docs/llm/AI-Platform/software-engineering-rules.md`
  - Regras de software mas com adaptações pras regras de AI Platform
- `pyproject.toml` — Ruff + Mypy
- `pyproject-minimal.toml` — Alternativa mais enxuta que não checa violação DDD de imports

## Estrutura de rules com progressive disclosure

Principalmente, o repositório tem uma estrutura de contexto para IA com progressive disclosure: as regras somente mencionam aspectos essenciais e curtos e servem de apontadores (ponteiros) para documentos em `docs/llm` que detalham o funcionamento, lidos somente sob necessidade.

Organização e hierarquia de regras:

- **Always on (2)**: Meta rules, project rules
- **On-demand (4)**: agents-feature-checklist, project-architecture, project-domain-api, project-tests

Por isso, a regra mais importante é a `meta-rules.md`. A segunda essencial é `project-rules`, também always on, que detalha especificidades do seu projeto que gostaria que o agente SEMPRE soubesse; enquanto o `AGENTS.md` define o comportamento.

`agents-feature-checklist` detalha um checklist do que fazer sempre que planejar ou implementar uma mudança no seu repositório, como por exemplo, quando atualizar x regras e y docs para não ficarem desatualizados. Outras regras são layer specific (architecture, domain-api, tests), podem ser substituídas para casos de uso não API. Seguem o mesmo princípio de poucas informações essenciais para evitar explorações e apontar para documentos mais detalhados.

```text
┌────────────────────────────────────────────────────────────────────────┐
│  NÍVEL 1: AGENTS.md                                                    │
│  - Comportamento, restrições, fluxo de pensamento do agente            │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
┌───────────────────────────────────▼────────────────────────────────────┐
│  NÍVEL 2: REGRAS ALWAYS-ON (~25 linhas)                                │
│  - meta-rules: Aponta para contexto, meta-regras e  triggers de skills │
│  - project-rules: Detalhes desse projeto sempre injetados              │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
┌───────────────────────────────────▼────────────────────────────────────┐
│  NÍVEL 3: REGRAS SOMENTE LIDAS POR CONTEXTO                            │
│  * Contém o essencial para evitar exploração desnecessária             │
│                                                                        │
│  - agents-feature-checklist: Checklist para implementar mudanças       │
│  - project-architecture, project-domain-api, project-tests: Layers     │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
┌───────────────────────────────────▼────────────────────────────────────┐
│  NÍVEL 4: DOCUMENTOS DENSOS DE CONTEXTO (docs/llm)                     │
│  * Lidos SOMENTE sob demanda quando a regra apontar                    │
│                                                                        │
│  - software-engineering-rules.md: Padrões Python                       │
│  - architecture-guide.md, domain-api-guide.md, tests-guide.md: Layers  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
┌───────────────────────────────────▼────────────────────────────────────┐
│  NÍVEL 5: SKILLS EXECUTÁVEIS (.agents/skills/)                         │
│  - @catchup, @interview-plan, @adversarial-review: Workflow local      │
│  - @commit, @PR-code-review, @project-rules-writing: Skills dedicadas     │
└────────────────────────────────────────────────────────────────────────┘
```

### Skills dedicadas do repo

- **PR code review** — Inspirado na Google (eng-practices)
- **Commit** — Inspirado na Google e git conventional (styleguide → git-commit)
- **Project rules writing** — Ensina como editar e a estratégia de funcionamento das regras (SharePoint)

### Skills da Anthropic
- **Catchup** — Recompõe o estado verificável de uma tarefa retomada a partir do Git, código e testes.
- **Interview plan** — Resolve decisões materiais antes do plano para mudanças ambíguas ou relevantes.
- **Adversarial review** — Revisa cada diff com contexto independente e hipóteses de quebra verificáveis.

### Generalistas (opcionais):
- **Fix test coverage** — Do Cursor (add-test-coverage)

## Sincronização de regras (.agents)

Adicionalmente, há uma automação que converte regras e skills do `.agents` para a convenção correta de regras de `.cursor`, `.claude` e `.github`. Assim, basta editar `.agents`, rodar `sync_agents.py` e todas regras são ajustadas corretamente. Isso é bom pois qualquer pessoa pode utilizar seu repositório sem esforço.

```text
                      ┌──────────────────────┐
                      │   .agents/           │
                      │  ├── AGENTS.md       │
                      │  ├── rules/          │
                      │  └── skills/         │
                      └──────────┬───────────┘
                                 │
                     python scripts/sync_agents.py
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
   ┌───────────┐           ┌───────────┐           ┌───────────┐
   │  Cursor   │           │  Claude   │           │  Copilot  │
   ├───────────┤           ├───────────┤           ├───────────┤
   │.cursor/   │           │.claude/   │           │.github/   │
   │rules/.mdc │           │  rules/   │           │instructions/│
   │  skills/  │           │  skills/  │           │  skills/  │
   │AGENTS.md  │           │CLAUDE.md  │           │AGENTS.md  │
   └───────────┘           └───────────┘           └───────────┘
```

---

## Como usar

1. Crie um repositório a partir deste template. Apague esse documento e mantenha só `README-padrão.md`
2. Escolha as variantes que serão mantidas:
   - **Lint**:
     - `pyproject.toml` — Ruff + Mypy
     - `pyproject-minimal.toml` — Alternativa mais enxuta que não checa violação DDD de imports
   - **Regras Python** (apague uma):
     - `docs/llm/software-engineering-rules.md`
     - `docs/llm/AI-Platform/software-engineering-rules.md`
3. Adapte o quality gate necessário
4. Quando construir o projeto de fato, a IA deve automaticamente:
   1. Substituir os marcadores FILL nas regras e nos guias pelos fatos do novo projeto.
   2. Sincronizar as regras para as ferramentas suportadas com `python scripts/sync_agents.py` (veja Sincronização de regras).
