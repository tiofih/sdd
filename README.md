# SDD — Spec-Driven Development (kit portável)

Kit de **Spec-Driven Development em sessões**: especificação antes do código
(refinamento), TDD estrito e validação pelo dono do produto — com critérios de
aceite **verificáveis por teste** e documentação viva.

Este diretório é **auto-contido**: não referencia o projeto que o hospeda, e é
também um **repo Git próprio** (`git@github.com:tiofih/sdd.git`, branch `main`).
Projetos **instalam** o kit com `install.sh` (veja abaixo): os artefatos vão para o
projeto, o kit fica aqui — fora deles.

## Conteúdo

| Caminho | Papel |
| --- | --- |
| `PROTOCOL.md` | Metodologia canônica: ciclo de 3 fases, donos, regras S1–S8, convenções de commit. **O ponto único de verdade do processo.** |
| `install.sh` | Instalador: copia o skeleton, substitui placeholders, anexa as regras no `AGENTS.md` e valida com o `check_docs` (idempotente). |
| `skeleton/` | Modelos para instanciar o SDD num projeto novo (copie e adapte). |
| `skeleton/AGENTS.md` | Bloco de regras para o agente/assistente seguir (cola no `AGENTS.md` do projeto). |
| `skeleton/REQUIREMENTS.md` | Stub da fonte da verdade dos requisitos (`{{PROJETO}}`). |
| `skeleton/SESSIONS.md` | Stub do registro de sessões (ciclo + tabela + "Próxima sessão"). |
| `skeleton/sessions/template.md` | Modelo do arquivo de cada sessão (as 9 seções + S1/S2). |
| `skeleton/agents/` | Subagents (opencode) por papel do fluxo: `refinador`, `implementador-teste`, `revisor`, `playtester`, `redator-pr` (corpo do PR, barato — `--with-pr`) — instalados no projeto em `.opencode/agent/`. |
| `skeleton/agents/specialists/` | Especialistas da fase 2 por lane: `backend`, `frontend`, `qa`, `ui-designer`, `game-designer`, `security-reviewer`, `a11y-auditor` — instalados só com `--with-especialistas`; inerte sem `> Equipe:` na sessão. |
| `skeleton/especialistas/` | Blocos de invocação dos especialistas (`<!-- sdd-especialistas:bloco -->`) para `.opencode/skills/sdd/SKILL.md` e `.opencode/commands/sessao.md`. |
| `skeleton/commands/` | Comandos orquestradores `/sessao` (ciclo de papéis, `--rapido` inline) e `/bugfix` (caminho enxuto PROTOCOL 3c). |
| `skeleton/skills/sdd/` | Skill `sdd` — guia do ciclo de papéis (fases, S7 loop, parada na validação). |
| `skeleton/scripts/check_docs` | Verificação de consistência (roda no host, só grep). |
| `skeleton/scripts/abrir-pr` | Abertura do PR/MR do modo `--with-pr` (corpo já gerado do arquivo da sessão; sem portão de texto) — instalado só com `--with-pr` (ver tabela de perfis). |
| `skeleton/pr/**` | Modo PR: README do modo e os blocos anexados ao `AGENTS.md`/`sessions/template.md` — instalados só com `--with-pr`. |
| `skeleton/tooling/INDEX-FIRST.md` | Disciplina tool-agnostic (definitions-before-grep) — instalado só com `--with-indexing`. |
| `skeleton/tooling/adapters/*` | Adaptadores (`graphify-cbm-zvec.md`, `context-mode.md`, `ai-memory.md`) — instalados só com `--with-indexing` / `--with-context-mode` (ver tabela de perfis). |
| `skeleton/STACK.md` | Template de especialização por área (tokens `{{AREA_*}}`/`{{PATHS_*}}` preenchidos à mão) — instalado por padrão; nunca sobrescrito. |
| `skeleton/commands/iniciar-sessao.md` + `levantar-roadmap.md` | Comandos extras de abertura/digest — instalados só com `--with-extra-commands`. |
| `skeleton/agents/optional/debugger.md` | Agente opcional de diagnóstico (read-only) — agrupamento provisório com `--with-extra-commands` (ver tabela de perfis). |
| `skeleton/agents/optional/arquiteto.md` | Agente opcional de **desenho técnico** (fase 1b, read-only) — instalado só com `--with-arquiteto`; as dependências dele estão declaradas no próprio arquivo (`## Dependências`). |
| `skeleton/arquiteto/*-block.md` | Blocos de invocação da fase 1b (marcador `<!-- sdd-arquiteto:bloco -->`) anexados a `.opencode/skills/sdd/SKILL.md` e `.opencode/commands/sessao.md` — instalados só com `--with-arquiteto`. |

## Instalação (recomendada — `install.sh`)

```bash
./sdd/install.sh /caminho/do/projeto --projeto "Meu App"
```

Cria `REQUIREMENTS.md`, `SESSIONS.md`, `sessions/` (template + sessão **0001** já
instanciada), `scripts/check_docs`, `scripts/*` de apoio, `.opencode/agent/` (subagents
dos papéis do fluxo), `.opencode/commands/sessao`, `.opencode/skills/sdd` (skill do ciclo)
e anexa as regras de workflow no `AGENTS.md` do alvo
— tudo idempotente (2ª execução pula o que existe; `--force` sobrescreve) e encerra
rodando o `check_docs` (instalação só é "sucesso" com docs consistentes).

Opções: `--proxima "texto"` (seção "Próxima sessão"), `--primeira "nome"` (nome da 1ª
sessão), `--no-agents`, `--force` — mais os perfis opt-in `--with-*` (ver tabela abaixo).
_Um diretório posicional primeiro (o alvo)._

Render: o install resolve sozinho os globais `{{PROJETO}}`, `{{PRÓXIMA_SESSAO}}`,
`{{ROOT}}` (dir alvo, absoluto), `{{DRAFT_PATH}}`, `{{GOTCHAS_PATH}}` e `{{INDEX}}` — e
`NNNN`/`NOME`/`DATA`/`slug` na sessão 0001. Só os tokens de área do `STACK.md` e os slots
de prosa do `sessions/template.md` ficam para preencher à mão (por projeto) — nenhum
deles é revertido pelo `--force`, porque `STACK.md` nunca é sobrescrito.

Perfis opt-in (default off = comportamento atual):

| Flag | Instala (origem → destino no projeto) |
| --- | --- |
| `--with-indexing` | `skeleton/tooling/INDEX-FIRST.md` → `tooling/INDEX-FIRST.md` + `skeleton/tooling/adapters/graphify-cbm-zvec.md` → `tooling/adapters/` |
| `--with-context-mode` | `skeleton/tooling/adapters/context-mode.md` + `ai-memory.md` → `tooling/adapters/` |
| `--with-stack` | (obsoleto) `skeleton/STACK.md` agora é instalado **por padrão**; a flag é aceita como no-op. |
| `--with-extra-commands` | `skeleton/commands/iniciar-sessao.md` + `levantar-roadmap.md` → `.opencode/commands/` |
| `--with-extra-commands` (+ debugger, agrupamento provisório — plano §2.6/§2.7 separam, §2.8 sem flag própria) | `skeleton/agents/optional/debugger.md` → `.opencode/agent/` |
| `--with-pr` | Modo PR (a entrega da sessão é um PR/MR): `skeleton/scripts/abrir-pr` → `scripts/` (+x); `skeleton/pr/` (README do modo) → `docs/pr/` (**create-only**); bloco do modo PR (marcador `<!-- sdd-pr: ativo -->`) anexado ao `AGENTS.md` e ao `sessions/template.md`; cria `docs/pr/` + `sessions/pr/`. Corpo do PR gerado do arquivo da sessão (sem template, sem `checar-pr`). Sem a flag, nada disso existe. |
| `--with-arquiteto` | Papel de **desenho técnico (fase 1b, opcional, read-only)**: `skeleton/agents/optional/arquiteto.md` → `.opencode/agent/arquiteto.md` (**create-only**); blocos de invocação (`skeleton/arquiteto/`) anexados sob marcador `<!-- sdd-arquiteto:bloco -->` a `.opencode/skills/sdd/SKILL.md` e `.opencode/commands/sessao.md` (append idempotente, arquivo-base intacto). **Não toca `AGENTS.md`.** Dependências declaradas no próprio agente (`## Dependências`) — o install avisa o que falta, sem falhar. |
| `--with-especialistas` | **Especialistas da fase 2 (lane por papel)**: `skeleton/agents/specialists/{backend,frontend,qa,ui-designer,game-designer}.md` → `.opencode/agent/` (**create-only**); blocos (`skeleton/especialistas/`) sob marcador `<!-- sdd-especialistas:bloco -->` a `.opencode/skills/sdd/SKILL.md` e `.opencode/commands/sessao.md`. Paralelo só com lanes disjuntas; portão duplo com `> Equipe:` na sessão. **Não toca `AGENTS.md`.** |

## Adicionar o SDD a outro projeto (kit externo)

O kit tem um repo canônico próprio: `git@github.com:tiofih/sdd.git` (branch `main`) —
e ele **não entra no repo do projeto**: nem por `subtree`, nem por cópia de `sdd/`. O
projeto fica só com os **artefatos instalados** (`AGENTS.md`, `sessions/`, `scripts/`,
`.opencode/`), e o kit vive aqui e no GitHub.

```bash
cd /caminho/do/projeto                       # repo git do projeto
git clone git@github.com:tiofih/sdd.git /tmp/sdd
/tmp/sdd/install.sh . --projeto "Meu App"    # + --with-pr / --with-especialistas / …
```

### Sync: projeto ↔ kit (sem `sdd/` no projeto)

- **Instalação create-only:** rodar de novo só cria o que faltar, nunca sobrescreve.
  Melhorias do kit em arquivos já instalados entram por **troca de bloco** (marcadores
  `<!-- sdd-*:bloco -->` no `AGENTS.md`, `SKILL.md`, `sessao.md`) ou merge manual do diff
  do kit — sem cópia versionada do kit dentro do projeto.
- O `PROTOCOL.md` (método completo) é lido no repo canônico
  (`github.com/tiofih/sdd/blob/main/PROTOCOL.md`); o `AGENTS.md` instalado carrega as
  regras mandatórias do dia a dia.
- Nunca edite o kit dentro de um projeto — a fonte é este repo.

## Instalação (manual, ~5 min)

1. Copie `skeleton/REQUIREMENTS.md`, `skeleton/SESSIONS.md` e `skeleton/scripts/` para a
   raiz do projeto novo.
2. Substitua os globais pelos valores reais — o `install.sh` faz isso sozinho; na cópia
   manual, faça à mão: `{{PROJETO}}`, `{{PRÓXIMA_SESSAO}}`, `{{ROOT}}` (dir do projeto,
   absoluto), `{{DRAFT_PATH}}`, `{{GOTCHAS_PATH}}` e `{{INDEX}}`.
3. Copie `skeleton/sessions/template.md` → `sessions/0001-<slug>.md` (e mantenha o
   `template.md` como modelo para as próximas).
4. Copie `skeleton/agents/*.md` → `.opencode/agent/` no projeto (opcional; roles do fluxo).
5. Cole o conteúdo de `skeleton/AGENTS.md` (seção "workflow rules") no `AGENTS.md`
   do projeto — ou use o `PROTOCOL.md` como guia manual.
6. Rode `./scripts/check_docs` a cada transição de fase (refinamento/validação).

## Criando a sessão N

1. Copie `template.md` → `sessions/NNNN-slug.md` (`NNNN` = próximo número da tabela).
2. No refinamento (fase 1): feche **Objetivo**, **Critérios de aceite** (apontando os
   **testes que provam** cada um — S1) e **Plano TDD**.
3. Commit do refinamento atualizando **também** `SESSIONS.md` (tabela + "Próxima
   sessão" — S4).