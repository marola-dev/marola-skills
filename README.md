<div align="center">

# marola-skills

<sub>o catálogo das skills da marola · <a href="docs/4-reference_index.md">índice completo</a> · <a href="docs/4-reference.md">auditoria</a> · <a href="#achados-da-revisão">achados</a> · <a href="https://docs.marola.dev/3-Ways-of-working/AGENT-SKILLS/">quando usar cada uma</a></sub>

[![índice: diário](https://img.shields.io/badge/%C3%ADndice-di%C3%A1rio-FF4F00?style=flat-square)](docs/4-reference_index.md)
[![refresh](https://github.com/marola-dev/marola-skills/actions/workflows/refresh.yml/badge.svg)](https://github.com/marola-dev/marola-skills/actions/workflows/refresh.yml)
[![ci](https://github.com/marola-dev/marola-skills/actions/workflows/ci.yml/badge.svg)](https://github.com/marola-dev/marola-skills/actions/workflows/ci.yml)
[![licença: MIT](https://img.shields.io/badge/licen%C3%A7a-MIT-1682D4?style=flat-square)](LICENSE)

Uma lista de todas as skills e subagentes do Claude Code que usamos na
[marola-dev](https://github.com/marola-dev) e nos repositórios do
[h0ffmann](https://github.com/h0ffmann). Para cada um: onde mora, de onde veio, o que já foi
testado e o que precisa de uma pessoa olhando.

</div>

**Skill** é um arquivo `SKILL.md` com instruções que o Claude Code segue numa tarefa.
**Subagente** é um ajudante com papel fixo, como revisar um PR.

**Este repositório só descreve.** Nada aqui é carregado pelo Claude Code. Cada skill continua no
repositório que a usa, e é lá que ela é corrigida.

## Automação

Todo dia às 06:17 UTC, o `refresh.yml` roda o [`scripts/refresh.py`](scripts/refresh.py). Você
também pode rodar à mão, pela aba Actions, ou com `just refresh`.

O script procura todo `SKILL.md` e todo subagente:

- em todos os repositórios públicos da **marola-dev**;
- nos repositórios do **h0ffmann** com push nos últimos 15 dias (`active_days` em
  [`data/sources.json`](data/sources.json));
- nos repositórios de fora que a gente acompanha (`watch`, no mesmo arquivo).

Ele escreve [`data/index.json`](data/index.json) e
[`docs/4-reference_index.md`](docs/4-reference_index.md). Pastas de teste, de exemplo e de
`evals/` ficam de fora.

**Não gasta tokens de modelo.** Só usa a API do GitHub. Um repositório sem push novo custa uma
resposta 304 (o GitHub diz "nada mudou"). Só a lista "esperando revisão" precisa de um agente
lendo.

Para acompanhar as skills de outra pessoa, ponha `"dono"` ou `"dono/repo"` em `watch`.

## Num relance

Contagem do índice gerado em 2026-10-16.

| | Quantos |
|---|---|
| Skills e subagentes nossos | 81 (74 skills, 7 subagentes), 75 nomes diferentes |
| Escritos aqui | 54 |
| Copiados de terceiros (vendored) | 27: 16 iguais à versão fixada, 10 atrás da versão de origem, 1 diferente |
| Revisados | 33 |
| Esperando revisão | 48: 34 novos, 14 mudaram depois da revisão |
| Skills de fora que acompanhamos | 263 |

Onde moram: marola-site (21), marola-devkit (17), ww3-gpu (15), picasso (11), closed-source (8),
o guarda-chuva marola (4), h0ffmann (2), marola-app, marola-corpus e marola-research (1 cada).

Os subagentes: `mip-reviewer` e `mip-claims-auditor` (devkit), `jar-verifier` (marola-app),
`news-copy-review` e `news-fact-check` (marola-site), `revisor-proposta` e `security-reviewer`
(ww3-gpu).

## Por etiqueta

Cada skill tem uma etiqueta (tag) que diz para que serve:

| Etiqueta | Para quê | Quantas |
|---|---|---|
| FE | o mapa e as páginas do marola.dev: design, animação, testes, Mapbox | 17 |
| workflow | o processo do time: MIPs, issues, PRs empilhados, trazer skills de fora | 12 |
| research | escrever, citar e publicar pesquisa | 10 |
| backend | qualidade e simplicidade do código, o app em Scala | 8 |
| DE | trazer dados para dentro: documentos do corpus, notas de voz | 2 |
| sem etiqueta | ainda não classificadas, a maioria nova | 33 |

A lista completa, com links, está no [índice](docs/4-reference_index.md). A
[auditoria](docs/4-reference.md#tags) tem a origem, os testes e as notas de cada uma.

## Plugins e servidores MCP

Um **plugin** traz várias skills, agentes e ganchos (hooks) de uma vez. Um **servidor MCP** dá
ao Claude Code uma ferramenta nova, como buscar documentação ou abrir um navegador. Esta lista
vem do `.claude/settings.json` e do `.mcp.json` de cada repositório, lidos em 2026-10-16.

| Nome | O que é | Onde está ligado | Para quê |
|---|---|---|---|
| [marola-devkit](https://github.com/marola-dev/marola-devkit) | plugin nosso, `v0.12.0` | marola, marola-app, marola-site, marola-oods, marola-ml, marola-corpus, marola-skills | skills de MIP e de issues, os agentes revisores e os ganchos comuns a todo repositório |
| [Ponytail 5](https://github.com/DietrichGebert/ponytail) | plugin, `v5.2.0` | marola-site; marola-research liga o plugin sem declarar de onde ele vem, então pode não carregar | a solução mais simples que funciona; `ponytail-review` e `ponytail-audit` também vêm copiados dentro do devkit |
| [superpowers](https://github.com/obra/superpowers) | plugin oficial | marola, marola-app | o fluxo de trabalho: planos, TDD, revisão de código |
| skill-creator | plugin oficial da Anthropic | marola, marola-app | criar skills e rodar as evals delas |
| [writing-skills](https://github.com/surendranb/writing-skills) | plugin, fixado num commit | marola | estilos de escrita: `plain-language`, `google-dev-docs` e outros |
| [Graphify](https://github.com/Graphify-Labs/graphify) | ferramenta de linha de comando, não plugin | todo repositório com o devkit, pelo `just graph` | um grafo do código para o agente consultar (veja [Em destaque](#em-destaque-graphify)) |
| [context7](https://context7.com) | servidor MCP | marola-app (`.mcp.json`, precisa de `CONTEXT7_API_KEY`) | documentação atual de bibliotecas, em vez da memória do modelo |
| playwright | servidor MCP, `@playwright/mcp@0.0.83` | marola-site | dirigir um navegador para as fotos de antes e depois do site |
| figma | servidor MCP | marola-site | ler os desenhos do Figma |
| marola | servidor MCP nosso | marola-app (`just mcp-server`, in a marola-app checkout) | as ferramentas da própria marola: praias, condições do mar |
| ai-memory | servidor MCP | só no ambiente do mantenedor; nenhum repositório liga | memória de longo prazo por projeto, entre sessões |
| [mattpocock-skills](https://github.com/mattpocock/skills) | plugin | só no ambiente do mantenedor; nenhum repositório liga | skills do dia a dia de um engenheiro; está no [top 20](#as-20-maiores-coleções-de-skills) |

**ouroboric-dev** (13 repositórios) não liga nenhum plugin nem servidor MCP. A regra de lá é o
contrário: copiar as skills de fora para o `havamal`, fixadas e revisadas num `skills.lock`, como
na MIP-0080, e nunca instalar ganchos de plugin. Ponytail, superpowers e Graphify aparecem só na
documentação, como referência.

## Em destaque: Graphify

O [Graphify](https://github.com/Graphify-Labs/graphify) transforma um código num grafo de
conhecimento. Um agente consulta o grafo em vez de ler pasta por pasta. A marola usa o Graphify
pela [MIP-0076](https://docs.marola.dev/6-MIPs/MIP-0076-agent-routing-tooling/). Não
instalamos a skill. É assim:

- **Um comando só.** O devkit traz o Graphify fixado pelo Nix. Você usa `just graph build` e
  `just graph query "<pergunta>"`.
- **Offline e sem chave.** Cada chamada roda sem rede e sem variáveis de ambiente além de cinco.
  Os modos que chamariam um modelo (`extract`, `update`, `--backend`) são recusados.
- **Fora do repositório.** O grafo fica em `~/.cache/marola-graph/<repo>`, nunca num commit.
- **Quando usar.** O `AGENTS.md` do guarda-chuva manda perguntas como "onde mora este símbolo?"
  para o `just graph query`. A skill `routing` do devkit diz quando o `git grep` sai mais barato.
- **No CI.** O guarda-chuva monta o grafo quando um submódulo, o devkit ou o Graphify mudam.

[Como, e em que pé está](docs/4-reference_graphify.md).

## Lago OODS (MIP-0075)

A [MIP-0075](https://docs.marola.dev/6-MIPs/MIP-0075-water-quality-store-r2/) monta um lago de
dados do oceano (um lugar único para guardar os dados) no Cloudflare R2. Estas são as skills que
ajudam: as da Cloudflare, as da DuckDB e as de engenharia de dados. Cada uma faz um ou mais de
cinco trabalhos: INIT, MANAGE, GOV, DEBUG e CONNECT. A atualização diária conta quantas já foram
adotadas:

<!-- oods:start -->
**0 of 13 adopted** in MIP-0075's repos. By job: INIT 0/2 · MANAGE 0/6 · GOV 0/5 · DEBUG 0/6 · CONNECT 0/3.
<!-- oods:end -->

[Cada skill, a origem, como entra e o que vigiar](docs/4-reference_oods.md).

## As 20 maiores coleções de skills

Escolhidas para o nosso trabalho e ordenadas pelas estrelas ganhas nos últimos 30 dias. **Só
listadas, não revisadas**: passe uma pela auditoria antes de copiar. O GitHub só mostra os clones
para quem mantém o repositório, então estrelas e forks são o sinal que temos. A tabela é refeita
todo dia a partir de [`data/curated.json`](data/curated.json). Até juntar 30 dias de histórico,
"Gained" conta desde a primeira leitura.

<!-- top-repos:start -->
| # | Repository | Stars | Forks | Gained | Tag | Why it is here |
|---|---|---|---|---|---|---|
| 1 | [mattpocock/skills](https://github.com/mattpocock/skills) | 294,583 | 24,706 | +1,638 since 2026-10-15 | backend | An engineer's day-to-day skills; the most starred skills repo this month |
| 2 | [affaan-m/ECC](https://github.com/affaan-m/ECC) | 281,987 | 41,938 | +1,534 since 2026-10-15 | backend | A whole agent harness (skills, memory, security); compare with marola-devkit before borrowing |
| 3 | [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | 51,963 | 4,600 | +1,417 since 2026-10-15 | research | 177 science skills: scientific writing, peer review, marine carbonate chemistry, CFD, GPU, Polars and Dask |
| 4 | [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design) | 56,724 | 3,608 | +762 since 2026-10-15 | FE | Diagram design rules for docs and READMEs (MIP-0068's diagrams) |
| 5 | [tt-a1i/archify](https://github.com/tt-a1i/archify) | 84,684 | 5,729 | +606 since 2026-10-15 | FE | Architecture and workflow diagrams as self-contained HTML; the same job as the umbrella's `architecture-diagram` |
| 6 | [mksglu/context-mode](https://github.com/mksglu/context-mode) | 30,416 | 2,184 | +599 since 2026-10-15 | backend | Context-window savings for agents, the token budget this repo optimises for |
| 7 | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | 107,630 | 11,206 | +561 since 2026-10-15 | backend | Production engineering skills: testing, performance, review |
| 8 | [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | 30,449 | 1,900 | +522 since 2026-10-15 | backend | Multi-phase security audit with machine-readable findings, for the app and the site |
| 9 | [nexu-io/open-design](https://github.com/nexu-io/open-design) | 101,816 | 11,698 | +444 since 2026-10-15 | FE | Design-system skills for agent-built UI; a candidate next to marola-site's `design-taste-frontend` |
| 10 | [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | 101,313 | 8,876 | +244 since 2026-10-15 | backend | Memory across sessions; overlaps the devkit's `obsidian-vault` handoffs |
| 11 | [alibaba/open-code-review](https://github.com/alibaba/open-code-review) | 47,526 | 3,469 | +126 since 2026-10-15 | backend | Code review skills, next to `ponytail-review` and Claude Code Review |
| 12 | [NVIDIA/SkillSpector](https://github.com/NVIDIA/SkillSpector) | 20,409 | 1,793 | +100 since 2026-10-15 | research | Scans a skill for risks before it is installed; automates part of this repo's audit |
| 13 | [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 35,876 | 3,893 | +82 since 2026-10-15 | research | A curated directory of 1,000+ skills, to search before writing one |
| 14 | [Agents365-ai/drawio-skill](https://github.com/Agents365-ai/drawio-skill) | 10,198 | 801 | +23 since 2026-10-15 | FE | draw.io diagrams from text, for proposal and docs figures |
| 15 | [teng-lin/notebooklm-py](https://github.com/teng-lin/notebooklm-py) | 19,768 | 2,633 | +17 since 2026-10-15 | research | NotebookLM from an agent, for literature and proposal reading |
| 16 | [max-sixty/worktrunk](https://github.com/max-sixty/worktrunk) | 9,266 | 331 | +12 since 2026-10-15 | backend | Git worktrees for parallel agents, the devkit's stacked-PR worktrees |
| 17 | [yusufkaraaslan/Skill_Seekers](https://github.com/yusufkaraaslan/Skill_Seekers) | 15,168 | 1,635 | +10 since 2026-10-15 | DE | Turns documentation sites, repos and PDFs into skills, e.g. the WAVEWATCH III manual for ww3-gpu |
| 18 | [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | 126,931 | 12,243 | +0 since 2026-10-15 | backend | Codebase knowledge graph; marola adopts it through MIP-0076 (docs/4-reference_graphify.md) |
| 19 | [Orchestra-Research/AI-Research-SKILLs](https://github.com/Orchestra-Research/AI-Research-SKILLs) |  |  |  | research | ML research and training skills, for marola-ml's fine-tune and benchmark |
| 20 | [tigerless-labs/autoharness](https://github.com/tigerless-labs/autoharness) | 11,126 | 627 | +-7 since 2026-10-15 | DE | Distils skills from past sessions and prunes unused ones |
<!-- top-repos:end -->

## Achados da revisão

Do mais grave para o menos grave. Cada um tem a prova, uma correção proposta e a issue no
repositório que vai corrigir. Situação em 2026-10-16:

| | Achado | Situação |
|---|---|---|
| [R1](docs/4-reference_review.md#r1-mip-solve-perpetual-implements-draft-mips-unattended) | `mip-solve-perpetual` implementa MIPs em rascunho sem ninguém olhando | corrigido em [marola-devkit#142](https://github.com/marola-dev/marola-devkit/pull/142) |
| [R2](docs/4-reference_review.md#r2-voice-to-feature-is-allowed-to-file-and-close-issues) | `voice-to-feature` pode abrir e fechar issues | corrigido em [marola-devkit#142](https://github.com/marola-dev/marola-devkit/pull/142) |
| [R3](docs/4-reference_review.md#r3-stale-copies-of-the-mip-skills-on-the-claudeai-account) | cópias velhas das skills de MIP na conta claude.ai | na sua conta claude.ai, sem issue |
| [R4](docs/4-reference_review.md#r4-paths-to-a-renamed-folder) | caminhos para uma pasta que mudou de nome | corrigido em [marola-devkit#142](https://github.com/marola-dev/marola-devkit/pull/142) |
| [R5](docs/4-reference_review.md#r5-humanizer-is-a-version-behind-upstream) | `humanizer` atrás da versão de origem | [marola-devkit#39](https://github.com/marola-dev/marola-devkit/issues/39); atualizado em #57, e o índice já o mostra atrás de novo |
| [R6](docs/4-reference_review.md#r6-ponytail-review-and-ponytail-audit-are-behind-upstream) | `ponytail-review` e `ponytail-audit` atrás da origem | PR aberto: [marola-devkit#141](https://github.com/marola-dev/marola-devkit/pull/141) |
| [R7](docs/4-reference_review.md#r7-sharingan-and-skill-copy-pin-a-model-and-the-highest-effort) | `sharingan` e `skill-copy` fixam um modelo e o esforço máximo | aberto: [marola-devkit#41](https://github.com/marola-dev/marola-devkit/issues/41) |
| [R8](docs/4-reference_review.md#r8-evals-that-ship-but-have-never-been-run-here) | evals que vêm junto e nunca rodaram aqui | aberto: [marola#700](https://github.com/marola-dev/marola/issues/700) |
| [R9](docs/4-reference_review.md#r9-design-taste-frontend-pulls-against-the-sites-rules) | `design-taste-frontend` puxa contra as regras do site | PR aberto: [marola-site#152](https://github.com/marola-dev/marola-site/pull/152) |
| [R10](docs/4-reference_review.md#r10-two-mapbox-skills-that-do-not-fit-marola-site) | duas skills de Mapbox que não servem ao site | PR aberto: [marola-site#153](https://github.com/marola-dev/marola-site/pull/153) |
| [R11](docs/4-reference_review.md#r11-karpathy-guidelines-has-no-licence-file) | `karpathy-guidelines` sem arquivo de licença | aberto: [marola-site#91](https://github.com/marola-dev/marola-site/issues/91); a origem não tem o texto da licença |
| [R12](docs/4-reference_review.md#r12-two-different-skills-named-eli5) | duas skills diferentes chamadas `eli5` | não precisa corrigir |
| [R13](docs/4-reference_review.md#r13-site-frontends-baseline-run-is-claimed-but-not-recorded) | o teste de base do `site-frontend` foi citado, mas não registrado | aberto: [marola-site#92](https://github.com/marola-dev/marola-site/issues/92) |

## Documentos

- [Índice](docs/4-reference_index.md): gerado todo dia; todas as skills, com links.
- [Auditoria](docs/4-reference.md): escrita à mão; origem, versão fixada e testes de cada uma.
- [Precisa de revisão](docs/4-reference_review.md): os achados, com prova e correção proposta.
- [Graphify](docs/4-reference_graphify.md): como a marola usa, as regras e o que falta.
- [Skills do lago OODS](docs/4-reference_oods.md): o que ajuda a montar o lago da MIP-0075.
- [Desenvolvimento](docs/3-development.md): como cada situação foi verificada, com os comandos.

A página [Agent skills](https://docs.marola.dev/3-Ways-of-working/AGENT-SKILLS/) do guarda-chuva
diz quando usar cada skill. Este repositório é o inventário e a auditoria.

## Contratos

| Direção | O que passa |
|---|---|
| repositórios → aqui | Só leitura: as skills são lidas do `main` de cada repositório; nada volta |
| aqui → guarda-chuva | `submodule-updated` a cada push no `main`, e `submodule-docs-updated` quando `README.md` ou `docs/` mudam (`notify-umbrella.yml`) |
| aqui → devkit | `bump-ready` quando o CI de um PR de bump do devkit termina verde (`bump-ready.yml`) |
| devkit → aqui | o `marola-devkit`, fixado numa tag em `flake.nix`, nos workflows e em `.claude/settings.json` |

[AGENTS.md](AGENTS.md) tem as regras para agentes que trabalham aqui. Licença MIT; cada skill
mantém a licença do repositório onde mora.
