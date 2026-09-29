---
description: Debate técnico A/B entre 3 modelos gratuitos (2 rounds: voto → réplica) para destravar decisão de refinamento. Uso avulso, fora ou dentro de sessão; advisory, nunca decide. Só com o perfil --with-debate.
agent: build
---

Você está abrindo um **debate** do `{{PROJETO}}` (perfil `--with-debate`, bloco
`sdd-debate` no SKILL.md) — 3 modelos baratos votando num dilema A/B para destravar
uma decisão. Advisory: o debate **recomenda**, o usuário **decide**.

## 1. Brief (antes de qualquer spawn)

Escreva o brief fechado — sem ele, não há debate:
- **Contexto** — sessão/projeto, arquivos/linhas, padrão vigente.
- **Opção A / Opção B** — 1–3 prós e 1–3 contras cada.
- **Pergunta** — o que votar, e **o que viraria o voto**.

Se o dilema vier de sessão SDD, tire do arquivo da sessão + mapa do `refinador`;
se for avulso (como escolha de projeto), o usuário cola o texto.

## 2. Round 1 — 3 votos em paralelo

Dispare 3 `debatedor` (subagent, `task`), cada um com `model:` de família distinta
(tabela no bloco `sdd-debate`; nunca o mesmo id nos 3). Cada voto: 3 bullets +
`VEREDITO: A/B`.

**Diversidade:** confira o `routed:` de cada voto — `auto/*` varia por chamada; dois
votos no mesmo backend = re-dispare o terceiro com outra família.

## 3. Round 2 — réplica cruzada

Re-dispare cada debatedor com os outros dois votos no prompt: advogado-do-diabo
(melhor argumento contra o próprio voto) + `VEREDITO FINAL:` + condição de flip.
**Teto 2 rounds** — empate vira "sem consenso".

## 4. Síntese (você, não o debatedor)

- Vencedor → recomendação (no mapa do `refinador`, ou resposta direta se avulso).
- Cada **condição de flip → check explícito** (teste, leitura `arquivo:linha`, ou
  critério) antes de fechar.
- Debatedor nunca edita, commita, marca critério (S1), reabre decisão (S3) nem grava
  memória (S6) — tudo no texto da conversa.

## Fora do debate

Sem `.opencode/agent/debatedor.md`, ou sem A/B travado: **não debate** — refinamento
normal ou 1 opinião inline com o modelo default (`diversidade: nenhuma` declarado).
`oc/*` só via subagent (curl direto = 403); sem provider `omniroute`, só `auto/*` não
existe — caia no modelo default.
