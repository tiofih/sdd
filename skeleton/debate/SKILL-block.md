<!-- sdd-debate:bloco -->
## Debate entre modelos (`debatedor`, só com o perfil `--with-debate`)

- **Portão:** só se `.opencode/agent/debatedor.md` existir **e** o usuário pedir, ou o
  `refinador` travar num A/B com tradeoff real que nenhum critério decide. Sem isso, o
  debate **não existe** — refinamento normal, sem custo extra.
- **Brief (orquestrador escreve, 1×):** contexto + A/B com prós/contras + pergunta +
  "o que viraria o voto". Sem brief, não há spawn.
- **Round 1 — 3 votos independentes, em paralelo**, cada spawn com `model:` de uma
  **família distinta** (nunca o mesmo id nos 3):

  | Voto | `model:` sugerido | Família |
  |---|---|---|
  | 1 | `omniroute/auto/smart` | raciocínio |
  | 2 | `omniroute/auto/gemini` | Google |
  | 3 | `omniroute/oc/mimo-v2.5-free` | OpenCode free |

  Alternativas livres: `omniroute/auto/cheap`, `omniroute/auto/llama`, `omniroute/auto/glm`.
- **Checagem de diversidade (obrigatória):** cada voto declara o backend real (`routed:`).
  `auto/*` varia por chamada — se dois votos caírem no mesmo backend, re-dispare o
  terceiro com outra família antes do round 2. Debate com 1 backend só é 1 opinião
  repetida, não debate.
- **Round 2 — réplica cruzada:** cada debatedor recebe os outros dois votos e devolve
  advogado-do-diabo + `VEREDITO FINAL:`. **Teto: 2 rounds** — sem terceiro round, sem
  desempate por voto extra; empate vira "sem consenso" e o usuário decide.
- **Síntese (orquestrador + `refinador`, nunca o debatedor):** vencedor entra no mapa de
  decisões como recomendação; cada **condição de flip vira check explícito** (teste,
  leitura de código ou critério) antes de fechar. Debatedor não marca critério (S1),
  não reabre decisão (S3) e não grava memória (S6).
- **Custo:** free-tier do gateway + resposta curta (cortar em ~400 tokens). Debate é o
  papel mais barato do ciclo depois do `redator-pr`.
- **Gotchas (medidos em 2026-09-29):** `oc/*` só responde via subagent (origem OpenCode;
  curl direto dá `403 free tier can only be used from within OpenCode`) — nunca rode o
  debate por script/shell. Sem provider `omniroute` no `opencode.json`, rode 1× inline
  com o modelo default e declare `diversidade: nenhuma`.
- No harness sem override de modelo por spawn: reduza a 1 debatedor inline (recomendação
  única + condição de flip) em vez de fingir debate.
<!-- fim sdd-debate:bloco -->
