<!-- sdd-debate:bloco -->
## Debate entre modelos (opcional; só com o perfil `--with-debate`)

Vale **só** se `.opencode/agent/debatedor.md` existir. Na fase 1, se uma decisão A/B
travar com tradeoff real e nenhum critério decidir (ou o usuário pedir): escreva o
brief (contexto + A/B com prós/contras + "o que viraria o voto") e dispare **3
`debatedor` em paralelo com `model:` de famílias distintas** (tabela no bloco
`sdd-debate` do SKILL.md) → cheque o `routed:` de cada um (re-dispare duplicata) →
round 2 com os votos cruzados → **sintetize você**: vencedor como recomendação no mapa,
condição de flip como check explícito. **Teto 2 rounds**; empate = usuário decide.
Sem o arquivo, ou sem A/B travado, **pule** — refinamento normal. O debatedor nunca
edita, commita, marca critério nem grava memória: devolve tudo no texto.
<!-- fim sdd-debate:bloco -->
