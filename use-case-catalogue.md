# Jev use-case catalogue

Living catalogue of proven, real-world use cases for TypeSafe AI's Jev model,
ranked by utility (see `../hidden_files/utility-rubric.md`). Updated by the
3×-daily scan. Evidence levels are labeled honestly — week-one "proven" is relative.

Last updated: 2026-09-30 (midday scan; 223 → 225 entries)

Scoring note: each entry scored per rubric — Evidence (1–5) + Economic leverage (1–5) +
Generality (1–5) + Maturity (1–5); ties broken by evidence. Scores shown in parentheses.

---

## Tier 1 — best-evidenced so far (score 16–19)

### 1. Agent judge / verifier layer (17, E5)
- **What:** Every's Dan Shipper had Jev judge his own writing — fuzzy questions
  ("does this sound like me?") turned into probabilities in ~0.7s.
- **Numbers:** 11 experiments, 1,709 judgments; 777 judgments in <0.7s for ~$0.0025.
  Vs Fable 5.1 at high effort: ~25× faster, ~580× cheaper; caught 6 of 7 planted defects.
- **Evidence:** independent test with metrics (Every, Sept 15).
- **Why it ranks #1:** the "verify everything" pattern — cheap, fast judgment sitting
  *behind* a generative agent, checking its work continuously. Composable, not replacement.

### 2. Document triage at scale (legal doc review) (17, E4)
- **What:** 9,840 Enron documents classified responsive / non-responsive.
- **Numbers:** 44.6 docs/sec, $1.05 total cost, 82.9% accuracy → 93.7% on answers
  at ≥95% confidence.
- **Evidence:** community test with reported metrics (ActionBox review); not
  independently verified; coverage of the high-confidence set unreported.
- **Why it ranks #2:** map-reduce over big data is Jev's native shape; the
  confidence-gated exception-queue pattern generalizes to any bulk triage.

### 3. Confidence-gated human review policy (17, E4)
- **What:** ActionBox implementation turns Jev's probability + confidence outputs
  into a risk-aware routing policy: only the uncertain or consequential tail goes
  to a durable human decision.
- **Evidence:** working implementation write-up (ActionBox).
- **Why it ranks #3:** this is the deployment pattern that makes all the others safe —
  prediction (Jev) + policy (thresholds) + authority (human for consequential calls).

### 4. MCP judgment tools for agents (jkudish/jev-mcp) (17, E4) — updated
- **What:** MCP server exposing Jev as three judgment tools for any agent: `jev_verify`
  (checks claims against evidence), `jev_screen` (judges content before it enters
  context), `jev_find` (ranks candidates by meaning, no embeddings). ~150–500ms per
  call, a fraction of a cent; works via direct TypeSafe key, Vercel AI Gateway,
  Cloudflare Workers AI (`typesafe/jev`), or OpenRouter. Shipped on npm as
  `@jkudish/jev-mcp` (MIT). Builder's real-use anecdotes: caught a contradicted
  claim at confidence 1.0 against a city ordinance; blocked a pricing page carrying
  a hidden "ignore your instructions" note at injection probability 0.99 while
  still reading it as a real page; ranked three files for a topic query and picked
  the right one at probability 1.0.
- **Evidence:** working integration described by builder (GitHub, Sept 18); anecdotes
  with exact confidence figures, but not measured benchmarks.
- **New (Sept 18 — midday):** the awesome-jev-usecases community index lists a second
  MCP server in the same family (itsmostafa/typesafe-mcp) — the packaged-judgment
  primitive is becoming a pattern, not a one-off.
- **Why it ranks #4:** the "verify everything" pattern as a *packaged, composable
  primitive* — three reusable shapes (verify / screen / find) that drop into any
  agent stack. Same family as #9 Firstmate's dispatch routing; the sibling
  `@jkudish/jev-browser` puts Jev behind browser actions (see #22).
- **Source:** https://github.com/jkudish/jev-mcp

- **New (Sept 23 — midday):** the MCP family keeps compounding (via
  yibie/awesome-jev, crawled Sept 23): burnigtm's jev-mcp — a server putting
  Jev into the coding loop for Cursor, Codex, and any MCP client, with 20
  test files behind it; and jev-use — a Claude Code / Codex / pi plugin (MCP
  server + library, native pi extension) whose fail-open PreToolUse gate can
  only deny or ask, rejecting untypeable or generation-needing questions
  before the call and flagging low-confidence answers as priors.

### 5. Listing moderation (Near Here) (16, E5) — updated
- **What:** UK events company ran 50 real listing-moderation decisions against `jev-1.13.0`.
- **Numbers:** 96% accuracy vs 84% Mistral Small 4 / 86% Gemini 3.5 Flash-Lite;
  0.59s/decision; $0.043 per 1,000 decisions (~5× faster, 8.6× cheaper than Mistral Small 4).
- **Evidence:** independent test with metrics, reported via Novel Cognition's "Jev File"
  analysis (Sept 16); the original publisher post was not directly located — attributed
  through the Jev File.
- **New (Sept 18 — midday):** the community awesome-jev-usecases index additionally
  reports "98,000 listing classifications in ten minutes" (~163/sec) as a
  bulk-classification datapoint — unattributed to a named author in the index, no
  accuracy figure, self-reported; throughput evidence only.
- **Why it ranks #5:** the first head-to-head *accuracy* win over competing models on a
  real workload — not just speed/cost. Watch for the company's own write-up.
- **Sources:** https://jev.novcog.us.com · read via https://dailytexasnews.com/typesafe-jev-system-one-model-claims-evals-independent-tests-daily_texas/

### 6. Prompt-injection / jailbreak judge (ar9av_) (16, E5)
- **What:** Jev used as a prompt-injection judge vs gpt-5.6-luna as generative judge.
  Bar-chart results with a technical walkthrough in the author's comments.
- **Numbers:** median seconds to judge one text — short (one paragraph): Jev 0.84s vs
  Luna 2.07s; long document (12,000 characters): Jev 1.10s vs Luna 11.40s (~10×
  faster, biggest win on long context). Caught 24/24 planted injections — but a
  **41% false positive rate**; author's own recommendation: Jev for first-pass
  filtering, LLM for the gray zone.
- **Evidence:** independent test with metrics and an honest negative (Threads, Sept 18);
  n small, author-measured.
- **Why it ranks #6:** the first independent measurement of the guardrails use case —
  and the 41% FP rate is exactly the kind of falsifier the model needs: Jev wins the
  triage layer, not the final call. Pairs with the rtrvr finding (#21) that Jev's
  edge is vs frontier LLMs, not tuned small models.
- **Source:** https://www.threads.com/@ar9av_/post/Dda7SFulEEZ

### 7. Classifier-pipeline judge replacement (Bashmohandes Mazen) (16, E5) — updated
- **What:** developer benchmarked his app's classifier pipeline (on-screen tables):
  Jev original / Jev + system prompt vs incumbent.
- **Numbers:** cost per 600 requests $0.02599 vs $0.46230; p50 latency 305ms vs 1,530ms;
  throughput 47.32 vs 6.66 req/s; near-100% exact accuracy in some cases. (His verbal
  "8× cheaper, 4× faster" understates the table margins — both figures reported.)
- **Evidence:** independent test with metrics (Instagram reel, Sept 16); he says he'll
  likely switch since the claims held with no instruction tuning.
- **Why it ranks #7:** a production-pipeline swap measurement with large margins on all
  three axes (cost, latency, throughput), framed as an LLM-as-a-judge replacement.
- **New (Sept 17):** a second reel on his real application "Graffix" vs Gemini 3.6 Flash
  and GPT-5.6 Sol: ~10× faster, 17× cheaper than Gemini, ~30× cheaper than GPT; ~1.5¢
  per 1,000 requests; most classifiers 95–100%, but the Enrichment classifier dropped
  significantly — an honest failure case on a real workload.
- **New (Sept 18 — midday):** Eric Lin (@eric80522, Threads) reports a third
  independent classifier test — 500-piece corpus classification labeling vs Opus 5:
  Jev ~100× faster, ~900× cheaper; author-measured, no raw numbers published.
- **Source:** https://www.instagram.com/reel/DdXbPnIFcjP/ · second test:
  https://www.facebook.com/reel/2169513350302788/ · Eric Lin:
  https://www.threads.com/@eric80522/post/DdbeqzFEpZf

### 8. Computer-use decision step (awlevin/typesafe-computer-use) (16, E5)
- **What:** independent Mac computer-use loop: no screenshots to a big model — Vision
  OCR + accessibility tree → deterministic state → one Jev request (three Choices:
  action kind, target, site) → deterministic click/type. Writer model only for free text.
- **Numbers:** same screenshot + goal, one decision each: $0.0002 vs $0.032 for Claude
  Opus 5 (155× cheaper; 170–390× in a realistic loop with history); model latency
  0.13–0.38s vs 5.2s (14–40× faster); 12-step task $0.003 vs $0.40–0.90.
  Builder's honest caveat: Opus read event dates off pixels unaided; the classifier
  needed explicit date parsing — "every piece of reasoning the frontier model does for
  free has to be rebuilt here as deterministic state."
- **Evidence:** independent side-by-side test with metrics (GitHub README, builder-measured).
- **Why it ranks #8:** the most carefully measured computer-use datapoint yet — and the
  caveat is the lesson: Jev's win scales with how much reasoning you can move into
  deterministic state.
- **Source:** https://github.com/awlevin/typesafe-computer-use

- **New (Sept 20 — evening):** a second on-device architecture, reported second-hand
  via the AY Automate roundup: @milindlabs segments UI elements with a local
  on-device model and reads their labels via OCR, then Jev picks the next action
  from text alone — no screenshots sent anywhere. A privacy angle beyond #8's
  setup; no numbers published. Watch for the author's own post.

- **New (Sept 23 — evening):** the headline *negative* computer-use benchmark —
  Builder.io CEO Steve Sewell independently tested the Jev computer-use hype:
  Jev's best overall pass rate ~33% vs nearly 100% for Luna; on real
  end-to-end browser tasks, one Jev variant scored 0%, another 11%. Simple
  tasks were fast, but repeated failures made cost-per-success similar to
  Luna, and OS-level use was the most expensive per success. His practical
  conclusion: the useful architecture is **hybrid** — Jev as the decision
  primitive under a capable controller, not a replacement for the larger
  model. This hardens #8's lesson: Jev's win scales with how much reasoning
  you can move into deterministic state; on open-ended browser tasks you
  can't, and the gap is the price. Pairs with #57's finding that WebMCP's
  explicit tool interface is what makes Jev browser control work.
- **Source:** https://www.instagram.com/reel/Ddm71JNzrR2/ (IG, Sept 22 23:41 UTC)

### 9. Firstmate live task-dispatch routing (16, E4)
- **What:** Firstmate deployed Jev for live task-dispatch routing — one typed question
  per dispatch instead of a supervisor LLM reasoning in prose with 3 tool calls; code
  handles approval gates/quota. Activated via TYPESAFE_API_KEY.
- **Numbers:** −71% cost per dispatch, −90% wall time per dispatch; the supervisor LLM
  took 28.6s; Jev ~200ms + 2.75s code gates. "Fable level quality but 10x faster."
- **Evidence:** builder-deployed production integration with self-reported metrics
  (Threads + Instagram, Sept 17) — co-branded with TypeSafe, so vendor-adjacent; treat
  numbers as builder-claimed until independently replicated.
- **Why it ranks #9:** the "choose the next tool/subagent" agent-loop pattern running
  *live in production* — the most generalizable dispatch pattern yet observed.
- **Sources:** https://www.threads.com/@kunchenguid/post/DdYMp1KFS6I ·
  https://www.instagram.com/p/DdYMoGplcJu/

### 57. WebMCP browser-control benchmark (idan levin / nekuda-ai) (17, E5) — NEW
- **What:** Jev + WebMCP run against the WebMCP benchmark: at each step Jev picks
  from the tools the website exposes; a small fast LLM (Mercury 2.5) generates the
  tool arguments, since Jev can't generate free text.
- **Numbers:** Jev + WebMCP solved 100% of tasks (49/49) at ~112× lower model cost
  than GPT-6 Astra using computer use + code execution (245× lower than
  screenshot-based Astra). Jev alone (modified browser-use Ultrafast) solved 25/49 —
  adding WebMCP nearly doubled solves while cutting model cost 18%.
- **Evidence:** independent benchmark with fully open, reproducible methodology and
  raw results (X, Sept 19); explicit harness notes on why Jev alone underperforms
  (valid-vs-right-action gap).
- **New (Sept 22 — midday):** a WebMCP side-panel for the browser — Sarah Drasner's
  Chrome extension side panel drives any site's WebMCP tools with Jev: every
  keystroke picks the relevant page's tool, fills arguments, shows confidence
  (grocery-shopping demo, via madewithjev.com). The #15/#57 pattern reaching
  interactive UI.

- **Why it ranks #57:** the first fully open, reproducible *task-completion* benchmark
  of Jev browser control — and the cleanest demonstration of the decomposition
  pattern: Jev picks the action, a cheap small model writes the arguments, the site
  exposes the interface. Pairs with #15 and #8; the "choice over explicit tool
  actions" shape generalizes to any tool-using agent.
- **Sources:** https://webmcp.com/benchmark · https://github.com/nekuda-ai/WindTunnel ·
  via https://madewithjev.com/

### 58. RAG / document-agent eval suite (Isaac Flath) (16, E5) — NEW
- **What:** six hands-on Jev uses in the author's document-agent workflow, each run
  against his own eval sets and compared head-to-head with Gemini 3.5 Flash —
  including the ones he's "99% sure" he'll still be using in 60 days:
  (1) fact-checking news scripts — 24/24 both, Jev median 0.41s vs 1.68s;
  (2) ranking a personal news feed — Jev surfaced 6/10 worth-reading vs Gemini 2/10;
  (3) RAG passage reranking — right passage ranked first 7/12 vs 1/12 embedding
  baseline, enabling smaller top_k (cost + latency down);
  (4) citation checking (agree/disagree/irrelevant) — caught a planted wrong number;
  (5) grouping review notes — 24/28 vs Gemini's 25/28 match to his hand labels,
  0.35s vs 4.85s median;
  (6) agent-failure diagnosis over full traces — matched eval error category 19/24
  vs Gemini's 20/24, 0.52s vs 4.23s.
- **Evidence:** independent hands-on tests with metrics and honest ties/losses
  (write-up, Sept 17).
- **Why it ranks #58:** six use-case shapes in one write-up, each measured against a
  real alternative — the single best independent evidence that Jev matches a
  capable flash-class model on judgment quality at ~5–10× the speed, losing only
  on raw accuracy margins of 1 case. The RAG rerank and citation-check shapes
  overlap #14 and #54 with real implementations attached.
- **Source:** https://isaacflath.com/writing/six-things-i-tried-with-jev

- **New (Sept 19 — midday):** retention follow-up — Flath says these are the six
  he's "99% sure" he'll still be using in 60 days: fact-checking scripts, ranking
  his news feed, finding text in PDFs, checking citations, grouping notes, and
  evals over agent traces. A durability datapoint for personal-workflow
  classification.

### 68. "Filter, not replacement" — measured deployment study (Aman Kumar) (18, E5) — NEW
- **What:** two days, ~16,000 API calls across four public classification sets and
  the author's own production pipelines, every result scored against ground truth or
  recorded outcomes — plus a roundup of measured third-party results.
- **Numbers:** public sets (300 items each): Enron spam 98.7 vs gpt-5.4-mini 97.7 /
  gpt-5.6-luna 98.0; SST-2 95.7 (92.7/93.0); AG News 91.3 (88.3/89.7); Banking77 76.0
  (78.7/81.7 — loses). Median 0.8–0.9s; 5–56× cheaper than the same-task LLMs.
  Confident band (≥0.9): 94.7–99.6% right. Own pipeline: page gates agreed with
  outcomes 96–98%; replaying five production runs with a Jev reject filter gave
  identical output with 25–60% of pages skipped; shared-inbox email triage 87.4%
  agreement, 96% when confident. Cost: email triage 15¢/1,000 vs $120 today (800×),
  page gating 11¢ vs $1.50 (14×), admin extraction 6¢ vs $3.50 (58×). Honest
  failures: whole-document read worse than the small model ("no prompt fixed that");
  a 2,000-email phishing bench at 62.6% vs 81.3% Claude Haiku 4.5. Cited third-party
  results: HiringCafe resume scoring vs human labels — Spearman 0.79 at 2¢/1,000 vs
  0.77 at 14¢ DeepSeek V4 Flash and 0.72 at 29¢ Gemini 3.1 Flash-Lite; a 19,500-email
  spam run at 98.3% zero-shot, level with a logistic regression trained on 15,000
  of them, with calibrated bins (0.1% spam below 0.1, 99.9% at ≥0.9); Classmethod
  model router 40/40 at 2.5¢/1,000; Vercel's CTO eval — Jev "won both on quality
  (saturated the eval) and speed (6×)".
- **Evidence:** independent measurement with full per-item answers and scoring code
  published (GitHub: onlyoneaman/jev-eval); third-party results cited, not audited
  by this watch.
- **Why it ranks #68:** the most careful independent measurement published so far —
  and its verdict ("filter, not replacement") is the deployment rule the whole
  catalogue converges on: short input, crisp labels → near-perfect and confident;
  long input, fuzzy labels → accuracy and confidence fall together. The
  threshold-setting recipe (set the drop line on recorded positives, check on unseen
  data, re-check monthly) is the missing production practice behind #3.
- **New (Sept 20 — midday):** the source repo for the 2,000-email phishing bench
  (anisselbd/jev-phishing-bench, Sept 17) is now crawled — Jev 62.6%
  [60.5–64.7] vs Claude Haiku 4.5 81.3%; recall on phishing 43.2% vs 76.4%;
  ECE 0.154 vs 0.097 (miscalibrated verdict, worse than the small LLM);
  $0.038 vs $0.462 per 1,000 emails. The surprise finding: the five decomposed
  *signal* questions asked in the same call beat the direct verdict —
  free-hosting signal alone AUROC 0.96 (fixed rule: 89.5% accuracy), and a
  cross-validated logistic regression on the five signals reaches 95.1% with
  ECE 0.027. Yet another independent data point for the decomposition rule
  (#25, #88): don't ask Jev the verdict — ask it the signals and fit the
  verdict in code.
- **Sources:** https://amankumar.ai/blogs/jev-measured ·
  https://github.com/onlyoneaman/jev-eval ·
  https://github.com/anisselbd/jev-phishing-bench

### 69. Vercel production command-safety classifier (17, E4) — NEW
- **What:** Vercel replaced the ChatGPT Luna 5.6 safety reviewer in its production
  command pipeline with Jev — per mohitkarekar.com's write-up, 5–18× faster and more
  accurate, now the default production path. Separately, Vercel's CTO benchmarked Jev
  on an existing classifier eval (per Aman Kumar's roundup): it "won both on quality
  (saturated the eval) and speed (6×)".
- **Evidence:** shipped production integration described via secondary write-ups;
  TechCrunch (Sept 18) reports the same production use. Exact latency multiples are
  builder/second-party claims.
- **Why it ranks #69:** the highest-profile production swap yet — a frontier-model
  safety reviewer replaced by Jev in a real deployment pipeline, with independent
  corroboration of the eval win.
- **Sources:** https://techcrunch.com/2026/09/18/a-new-kind-of-ai-model-from-a-chatgpt-inventor-is-thrilling-developers/
  · https://mohitkarekar.com/posts/2026/probabilistic-decisions-with-system-one-model-jev/
  · via https://amankumar.ai/blogs/jev-measured

### 70. Korean sentence classifier — batching caveat (ebrain.lab) (17, E5) — NEW
- **What:** 40 Korean sentences classified in 1.9 seconds for $0.0012, 40/40 —
  Claude Opus also 40/40 but ~24× costlier. The honest failure: putting the whole
  document into one LLM-style prompt dropped Jev accuracy to 62%; one-sentence-per-
  call worked.
- **Evidence:** independent builder test with metrics (Threads, Sept 19); the
  batching caveat replicates Aman Kumar's whole-document finding (#68) on a
  different task.
- **Why it ranks #70:** the first non-English *measured* classification result
  (pairs with #31) — and the 62% failure mode is the falsifier every deployment
  guide now repeats: Jev is a sentence-level filter, not a document reader.
- **Source:** https://www.threads.com/@ebrain.lab/post/DddGgXuoLlL

### 71. Live Mac-app support router (17, E4) — NEW
- **What:** a live Mac app's troubleshooting router — Jev reads the built-in manual
  and picks the matching documentation/status article (or "nothing does") for each
  support query.
- **Numbers:** 42/42 held-out, including paraphrases, typos, French/German/Spanish
  queries, nonexistent features, and follow-ups; 0.93s median.
- **Evidence:** builder-described live integration with held-out metrics (via
  madewithjev.com, Sept 19); no independent audit yet.
- **Why it ranks #71:** the first *shipped* support-routing deployment with a real
  held-out set — multilingual + typo + paraphrase robustness is exactly what kills
  traditional classifier pipelines here.
- **Source:** via https://madewithjev.com/

### 72. Vercel Eve — Jev as default evaluate model (16, E4) — NEW
- **What:** Vercel's open agent framework "Eve" ships with Jev as its default
  evaluate model — every Eve agent's typed evaluations run through Jev unless the
  builder changes it.
- **Evidence:** shipped platform integration (via madewithjev.com, Sept 19); no
  speed/accuracy figures published.
- **Why it ranks #72:** framework-default status is the strongest distribution
  datapoint after the AI Gateway — Jev becomes the judgment primitive an entire
  framework's users inherit by default.
- **Source:** via https://madewithjev.com/

- **New (Sept 24 — midday):** a second infrastructure integration — LiteLLM
  ships a `typesafe/jev` proxy in v1.103.0-rc: Jev's `/v1/systemone` endpoint
  (not `/chat/completions`) proxied with logging and cost tracking under the
  versioned model id `typesafe/jev-1.13.0`. (https://docs.litellm.ai/blog/typesafe_jev)

- **New (Sept 23 — midday):** a second Jev-native agent runtime — JarvisCore
  (Python multi-agent runtime) ships Jev natively from v1.12: agents ask typed
  `Choice`/`Score`/`Noul` questions through a decision client *separate from
  the text model*, the Kernel picks a specialist subagent by `Choice`, and
  each retrieved RAG passage is withheld from the generating model when its
  prompt-injection `Noul` exceeds 0.70 — the Jev-as-decision-client
  architecture as a framework feature. (via yibie/awesome-jev)

### 102. LangChain — Jev-as-a-judge for agent evals (LangSmith) (16, E5) — NEW
- **What:** LangChain tested Jev as an evaluator on LangSmith: instead of an LLM
  judge writing a score in prose, Jev answers typed quality questions about
  agent runs directly.
- **Numbers:** quality-score variance **92–913× lower** than GPT-5.6 Luna, GPT-5.6
  Terra, and Claude Sonnet 4.6 as judges; averaged 0.44s and $0.00035 per call —
  $0.34 total vs $28.17 for Claude (~83× cheaper). LangChain's own caveat:
  "promising, but early" — a narrow test. A Sept 22 livestream with the TypeSafe
  team is announced.
- **Evidence:** independent test with metrics by a framework operator
  (langchain.com blog, Sept 20).
- **Why it ranks #102:** the first operator-run Jev-as-a-judge measurement — and
  consistency (variance) is exactly the metric evals need, where Jev's typed,
  non-autoregressive shape is structurally advantaged. Pairs with #37 (MMLU-Pro
  probe) and #72 (Eve's default evaluate model) as the eval-judge cluster.
- **Source:** https://www.langchain.com/blog/jev-agent-evals-langsmith

### 111. Context trimming for a coding agent (Nyarlathoteppppp/pi-jev-context) (17, E4) — NEW
- **What:** a shipped extension + server that uses Jev to trim long tool output to
  verbatim key lines before it enters the pi coding agent's context (write-time
  trimming), plus an experimental shadow-pruning path that is deliberately never
  applied.
- **Numbers:** write-time trimming saved **31–53% of tokens on synthetic held-out
  sets with 0 key lines lost** (p50/p95 latency ~347/457ms); on real-session
  replays (184 outputs) only **1/43 trimmed outputs hid something used later**.
  The shadow old-context pruning path was riskier: raw Jev dropped **73% of
  items needed later** on real sessions (21% with deterministic source
  protection) — so it is never applied. Pre-registered benchmarks, jev-1.13.0.
- **Evidence:** builder-measured experiment with pre-registered benchmarks
  (via robokrunch/awesome-jev, Sept 21); synthetic + real-session replay mix.
- **Why it ranks #111:** the best-measured context-compaction datapoint yet —
  31–53% savings with an explicit, honestly-reported failure bound on the riskier
  path. Pairs with tamaratran/fast-jev-compaction's cruder 1M→86K datapoint and
  makes memory compaction a first-class Jev workload family.
- **Source:** via https://github.com/robokrunch/awesome-jev

### 118. Backoffice decisions: invoice approval with a hidden policy trap (Sobhan Daliry / Pipefy) (17, E5) — NEW
- **What:** three real backoffice decisions (invoice approval, onboarding,
  collections), each with a hidden policy trap and conflicting signals, run
  natively on Jev 1.13.0 vs Claude Opus/Sonnet/Haiku two ways: forced typed
  answers, and natural-language reasoning first. Part 1 (invoice approval):
  R$47,300 invoice from Acme Logistics — a R$7,300 "expedited setup fee" 46%
  above the R$5k CFO-sign-off threshold buried in a Finance comment, plus
  shortened payment terms (net-5 → net-3) without warning.
- **Numbers:** Jev native call ~800ms, ~900 tokens: "executive approval
  required," 91% confidence non-compliant as submitted. The same decision via a
  general model — natural answer first, then a second pass extracting a
  structured decision — ran 80,000–100,000 tokens across the three Claude
  models (~100×). The killer finding: Sonnet's prose reasoning was sound
  ("Routes to the CFO. Full stop.") but the extraction step reported it as
  `finance_approval` — one tier too low — a silent error no one reading the
  model's own text would catch.
- **Evidence:** independent hands-on test with measured tokens/latency/confidence
  (LinkedIn, Sept 22); author works at Pipefy, which builds backoffice
  orchestration — domain-relevant. Cases 2 and 3 (onboarding, collections) pending.
- **Why it ranks #118:** the first backoffice-approvals use case with real
  policy-trap cases — and the "seam" finding (reasoning right, extraction
  wrong) is the catalogue's sharpest argument for typed-native decisions: the
  failure isn't the judgment, it's the prose-to-structure handoff that Jev
  eliminates by construction. Pairs with #3 (confidence-gated policy).
- **Source:** https://www.linkedin.com/pulse/testing-ai-real-backoffice-decisions-part-1-invoice-approval-daliry-mjtgf

### 134. Production input moderation in Mastra (CodeAlive/mastra-jev-moderation) (16, E4) — NEW
- **What:** a Mastra input processor for AI assistants — one request asks Jev a
  `Boolean` "must this message be blocked?" plus a category `Choice`; aborts the
  turn at 0.7, failing open behind a deadline and circuit breaker.
- **Numbers:** **in production: 9/9 hostile messages blocked, 0/49 real messages
  blocked**; ~0.4s median; ~4× cheaper than an LLM moderator.
- **Evidence:** working production integration described by builder, via the
  v-modal/awesome-jev-tools index (Sept 23); method (single request, threshold
  0.7, fail-open) is public. An independent Indonesian-language roundup
  (@fdavidjm, Sept 24 — quoting the builder, not first-person) repeats the
  9/9 + 0/49 + ~0.4s figures and adds a claimed gpt-oss-120b comparison in
  the same test: ~2s, ~4× more expensive; builder's own caveat: "measure on
  your own messages."
- **New (Sept 26 — morning):** the same author's comment thread adds concrete
  micro-automation datapoints with numbers: CSV column mapping — 23
  warehouse-export columns → 10 order-table columns, 10/10 matched in 915ms;
  100 emails ranked by reply priority in under half a second; video clipping
  by keyword (the jevclip shape) at reported low cost; per-element ad and
  cookie-banner removal on web pages. Comment-thread claims, author-reported —
  one notch below the quoted builder figures above, but four distinct
  use-case shapes with measured latencies.
- **Why it ranks #134:** the first *production* moderation datapoint with a
  measured false-block rate — zero false blocks on 49 real messages is the
  number that makes this shippable. The fail-open design is the deployment
  practice behind #3's routing policy. Pairs with #25 (tool-call firewall) as
  the security cluster's second shipped piece.
- **Source:** via https://github.com/v-modal/awesome-jev-tools; roundup at
  https://www.threads.com/@fdavidjm/post/Dds1w01GLd8

### 153. Multi-turn RAG routing benchmark (largitdata) (16, E5) — NEW
- **What:** an independent public benchmark of five systems on enterprise RAG
  routing: 20 conversations × 5 turns (100 decisions), four typed questions per
  turn — route (source/document/tool), scope change, mode (answer/rewrite/
  clarify/block), restriction (document-scope persists?). "Decision correct"
  only when all four are right on the same turn. Jev 1.13.0 (hosted API) vs
  Gemma 4 31B (self-hosted) vs three open Jev-interface re-implementations
  (djev-spark, SemIf, Laya 322M).
- **Numbers:** decision correct: Gemma 31B 77.0%, Jev **61.4%**, djev-spark
  32.2%, SemIf 24.0%, Laya 322M 0.0%. Per-dimension, Jev is close on route
  (90.6 vs 90.0) and scope change (85.8 vs 87.0); the gap is restrictions
  persisting across turns (84.4 vs 95.0). Latency: Jev p50 749ms / p95 818ms
  vs Gemma 2,293ms / 4,723ms — the tail is Jev's whole advantage. Laya is
  fastest (189ms p50) but produced zero fully-correct turns. Honest failures:
  when a prompt genuinely lacked information, Gemma asked a clarifying
  question 43% of the time, Jev only 31%; both commonly called a tool instead.
- **Evidence:** independent evaluation with full methodology, per-item
  predictions, timing logs, and ground-truth audits published (largitdata.com
  blog + public benchmark repo, Sept 20).
- **Why it ranks #153:** the first multi-turn benchmark in the catalogue — and
  it lands exactly where the model predicts: Jev matches the bigger model on
  crisp per-turn judgments but bleeds on cross-turn state (restrictions), and
  its edge is tail-latency stability, not accuracy. The "restrictions don't
  persist" finding is the falsifier every RAG-router deployment (#3 family)
  must now budget for.
- **Source:** https://www.largitdata.com/en/blog/jev-system-one-model-open-source-benchmark/

- **New (Sept 25 — evening):** a fourth open Jev-interface re-implementation —
  djev-run (Daniel Lee) stands up a TypeSafe/Jev-compatible decision-model
  endpoint for NVIDIA's DiffusionGemma-26B-A4B-it-NVFP4 on Google Cloud Run
  with one command: ~47.5s cold start, ~35–60ms per decision, ~100–123
  req/s at batch 32; JevBench v1.3 at 81.4% accuracy / 73.4 composite with
  117ms median latency; ~$3.19/hr active compute, $0 idle. Amplified by the
  Google Gemma team. Competitor-watch, not a Jev use case.
  (via https://www.theneuron.ai/digest/everything-that-happened-in-ai-today-tuesday-september-22-2026/)

### 154. Production reranker benchmark (denser-org/rerank-bench-jev) (16, E5) — NEW
- **What:** denser.ai's auditable production-reranker benchmark — Jev (packed
  Noul, one question per passage in a single request) vs Qwen3-Reranker-0.6B
  via DeepInfra on BEIR SciFact (300 queries) and NFCorpus (323 queries), over
  a frozen BM25 first stage. Raw API responses shipped; paired bootstrap +
  Wilcoxon statistics; 26 analysis tests.
- **Numbers:** nDCG@10 — SciFact: Jev 0.7699 vs Qwen 0.7481 (+0.022, p=0.011,
  but one repeat in three failed significance: "treat quality as equivalent");
  NFCorpus: 0.3623 vs 0.3569 (tie, p=0.53). Cost/query at depth 100: Jev
  $0.00178 vs Qwen **$0.00045** — Qwen ~4× cheaper. Latency: Jev p50
  0.64–0.65s vs 1.11–1.22s (~1.8× faster); p95 **0.76–0.78s vs 1.69–2.10s**
  (~2.4× faster) — Jev's tail barely moves (+0.12–0.13s over median vs
  Qwen's +0.58–0.88s). The packing trick is the engineering lesson: query in
  `state` + one question per passage = identical scores (Pearson r=1.000) with
  one request instead of 100, 1.56× fewer tokens — without it Jev's p95 would be
  ~10× worse. Verdict: "decide on latency or cost, not on nDCG."
- **Evidence:** independent benchmark with fully reproducible methodology and
  raw results (GitHub, Sept 22, denser.ai team).
- **Why it ranks #154:** the strongest rerank measurement so far — and it
  refines the catalogue's vs-OSS rule (#21): against a tuned small model, Jev
  loses on cost (~4×) and wins on tail latency (~2.4× at p95 with a far
  tighter spread). The packing recipe is what makes Jev viable as a reranker
  at all (TypeSafe's cookbook suggests one request per passage — unusable).
  Pairs with #14 (the anessbelbati tie vs Cohere) as the rerank cluster's
  second pillar.
- **Source:** https://github.com/denser-org/rerank-bench-jev

### 155. Command-risk + moderation eval (kiarina/labs) (16, E5) — NEW
- **What:** two near-production safety evaluations of jev-1.13.0 by a Japanese
  builder: (1) 134 self-labeled shell commands in safe/confirm/danger,
  (2) moderation on 1,847 Japanese docs (LLM-jp Toxicity v1) and 1,680 English
  docs (OpenAI moderation release set), Jev vs OpenAI's free moderation API
  and a regex blocklist. Metrics code unit-tested against hand values; slides
  and method published.
- **Numbers:** commands — Jev let **0 of 43 danger commands through as safe**;
  regex missed 14; gate recall 0.872–0.936, AP 0.900, AUROC 0.955; median
  ~210–220ms. Jev's misses were danger→confirm (conservative) except 10
  confirm→safe slips on context-justified operations (`cat .env`, migrations).
  Japanese moderation — Jev AP 0.915 / AUROC 0.940 / recall 0.956 vs OpenAI
  0.902 / 0.910 / 0.646: **Jev missed 36 of 826 harmful docs vs OpenAI's
  292**. English — OpenAI slightly ahead (AP 0.898 vs 0.868) with clearly
  better calibration (ECE 0.057 vs 0.185). Jev's recall 0.956 in *both*
  languages: high-recall, over-collecting — safety-gate shaped.
- **Evidence:** independent builder test with real datasets, full metrics
  (AP/AUROC/ECE), and honest negatives (GitHub, Sept 17, Japanese).
- **Why it ranks #155:** the best-measured security-cluster datapoint in the
  catalogue — the 0/43 dangerous-command figure and the Japanese moderation
  win (OpenAI drops to 0.646 recall in Japanese) are both actionable, and the
  ECE honesty (Jev miscalibrated in English) disciplines threshold-setting.
  Pairs with #25 (tool-call firewall), #6 (ar9av_'s 41% FP finding), and #70
  (the non-English pair).
- **Source:** https://github.com/kiarina/labs/tree/main/2026/09/17/typesafe-jev-safety-judgment

### 156. Systematic-review screening with measured accuracy (youkiti/tiab-review-plugin) (16, E4) — NEW
- **What:** a shipped Chrome extension for systematic-review title/abstract
  screening that added `jev-1.13.0` (Sept 2026) alongside six LLM options,
  adopted after it met the author's own benchmark bar. Jev records per-criterion
  match probabilities instead of prose rationales — typed answers as the
  screening record.
- **Numbers:** depression dataset (n=1,993, 280 positives): **Jev recall 96.1%**
  at the extension's default threshold 0.3 — level with gemini-3-flash-preview
  (96.1%) and above the default gemini-3.1-flash-lite (93.6%); CQ1–5 criteria
  combined 95.0% (246/259). Benchmarks run under the same adoption gate
  (recall ≥ 0.90) as the incumbents; detailed reports in
  `experiments/typesafe-jev/`.
- **Evidence:** builder-measured accuracy on labeled datasets with published
  experiment reports (GitHub, surfaced Sept 24); author is the extension's own
  developer (self-measured, but with a real adoption decision attached).
- **Why it ranks #156:** a *shipped* screening product (Chrome Web Store) that
  ran Jev through a real labeled-data acceptance gate and published the tape —
  recall-level parity with flash-class models is exactly the shape that makes
  the cost argument (#2 family). The "typed probabilities as the screening
  record" is a new archival angle: the judgment is auditable by construction.
- **Source:** https://github.com/youkiti/tiab-review-plugin

### 159. Independent benchmark: Jev vs four LLMs on 791 labeled decisions (AY Automate) (16, E5) — NEW
- **What:** AY Automate ran Jev 1.13 (via OpenRouter's decisions endpoint) and
  four LLMs (GPT-5.4 nano, Gemini 3.5 Flash-Lite, Claude Haiku 4.5, GPT-5.6
  Terra) through the same 791 labeled decisions with one client and one billing
  meter, Sept 19: 8-way and 77-way intent routing (Banking77) plus
  prompt-injection detection (deepset, 400 items). Harness + every call's
  prediction, latency, and cost published; paired McNemar tests on all comparisons.
- **Numbers:** Jev accuracy 83.8% (8-way) / 78.8% (77-way) / 87.0% (injection)
  — level with the small models, behind GPT-5.6 Terra (89.4% / 84.0% / 86.8%;
  the 77-way gap is real at p=0.029). Jev median latency 0.33s vs 0.67s for the
  fastest LLM; slowest of 791 Jev requests 1.42s vs ~11s for three of the four
  LLMs. Cost per 1,000 decisions: Jev $0.015 vs GPT-5.4 nano $0.070 (5×),
  Claude Haiku $0.357 (24×), Terra $0.609 (40×). On injection detection Jev's
  probability AUROC was **0.990, the best of the five** — a usable dial (cutoff
  0.10 → recall 0.96 at precision 0.92). The headline finding: a Jev-first
  cascade (Jev at ≥0.80 confidence, rest to Terra) **matched Terra's accuracy
  at 26–28% of Terra's cost and ~half its mean latency**.
- **Evidence:** independent benchmark with full methodology, per-call data, and
  honest limits (one run, public data, LLM labels may overlap training; Sept 19).
- **Why it ranks #159:** the most statistically careful Jev-vs-LLM comparison
  yet — and its verdict is the catalogue's first *measured* Jev-first cascade:
  Jev as the cheap front filter with a frontier model behind the confidence
  gate is the economically optimal shape, not Jev alone. Vendor multipliers
  (193.6×/444.6×) did not show up against these baselines; the measured deltas
  are 2.0–3.6× faster, 4.7–7.5× cheaper than the cheapest small models. Pairs
  with #3/#68 (confidence-gating) and #6/#155 (the injection-detection cluster).
- **Source:** https://www.ayautomate.com/blog/jev-vs-llm-benchmark

### 172. Cross-domain decision benchmark: Jev 1.13.0 vs GPT-5.6 Luna (omarmujahid/jev-decision-bench) (17, E5) — NEW
- **What:** an independent, reproducible, agent-benchmark-style cross-domain
  evaluation — Jev 1.13.0 on typesafe.ai across **49 tasks / 8,225 items**
  (≤200 random samples per task) vs a frontier chat baseline GPT-5.6 Luna run
  at default reasoning, reasoning-off (fastest), and reasoning-low; every
  prompt formatted for both models, raw JSONL + per-run summaries saved for
  reproduction. Run 2026-09-18, three rounds, three machines.
- **Numbers:** Jev led/tied/trailed Luna (default reasoning) **29/13/7**;
  median server time **105 ms vs 710 ms (default) / 808 ms (reasoning-off)**;
  cost per 1,000 items **$0.04 vs $0.16/$0.19**; calibration ECE **0.07 vs
  0.18/0.14**. Jev clear-lead areas: reasoning, knowledge, reranking; weak:
  counting and Banking77 (which Luna sweeps).
- **Evidence:** independent measured experiment with unusually honest caveats:
  mostly public benchmarks (contamination possible, fresh-math control only),
  ~200 samples/task with only seven statistically clear leads, baseline
  reasoning intentionally restricted (real frontiers do heavy reasoning), and
  Luna's negation probabilities differed by 0.32 on average. The first
  independent benchmark to pin Jev's speed/cost advantage on a large item
  count: ~1/7 latency, ~1/4 cost, 2–3× better calibration.
- **Why it ranks #172:** the broadest independent Jev-vs-frontier comparison
  yet — 8,225 items, per-run data, published code — and it confirms the
  catalogue's core thesis (#68/#1) with the sharpest margins yet. The caveats
  are part of the evidence: this is what an honest benchmark looks like.
- **Source:** https://github.com/omarmujahid/jev-decision-bench

### 173. Claude Code skill-router — local session-start skill ranking (lomeshdutta/skill-router) (16, E5) — NEW
- **What:** a shipped CLI/hook that inventories ~130 installed skills, ranks
  them per session at session start (thresholds and questions published,
  misses retained), and hands off to skills.sh when none fit.
- **Numbers:** author evals **9/10 + 4/5 + 3/4 + 10/10 = 26/29**; typical
  author latency ~0.35s US West Coast; an independent run on another machine
  took ~5s, found two runtime bugs (both fixed), and confirmed broadly
  matching behavior.
- **Evidence:** working shipped tool (GitHub, Sept 2026) **plus a genuine
  third-party replication attempt** by an external reviewer on their own
  machine — one of the few entries with any. This extends the skill-routing
  cluster (#12) from prototype to shipped product with published thresholds
  and retained misses.
- **Why it ranks #173:** the session-start routing pattern done as a product —
  the independent replication (bugs included) earns Tier 1 despite thin
  throughput numbers. Pairs with #12 as the same problem, new evidence.
- **Source:** https://github.com/lomeshdutta/skill-router

### 174. jev-harness — "System 1.5" decision gates for coding agents (ismaelsoilet/jev-harness) (16, E4) — NEW
- **What:** a reusable "System 1.5" governance layer for coding agents — five
  semantic gates (test-failure triage, doom-loop abort, completion
  verification, intelligent routing, reasoning effort) plus a nudge-gate — as
  an MCP server, CLI, and Python/TypeScript/Rust SDKs, with git hooks and CI
  gates. 59 commits, 13 releases, published to PyPI/npm/crates.io.
- **Numbers:** deterministic gates run locally (µs); Jev is only the semantic
  overflow for ambiguous calls — live free-tier calls ~0.5–1.0s at
  ~$0.00002/call (author-measured). The headline "80–90% token savings vs
  Claude 4" is a **builder estimate, not a measurement**; most cited
  latency/cost figures are author-measured or offline estimates.
- **Evidence:** shipped framework with real architecture — deterministic
  first, Jev as typed semantic overflow — which is the catalogue's recommended
  shape (#68's lesson) implemented as infrastructure; savings claims labeled
  as estimates here, not facts.
- **Why it ranks #174:** the most complete reusable Jev decision layer for
  agent governance yet; the overflow-only architecture is the right shape,
  but the big savings numbers stay explicitly labeled as estimates.
- **Source:** https://github.com/ismaelsoilet/jev-harness

### 178. Codebase context routing — hand the coding agent the ten files that matter (aegsrl7/jevmap) (16, E4) — NEW
- **What:** builds a map of a codebase as small units (functions, endpoints,
  classes, components, pages, jobs) by reading the source with no AI, then
  asks Jev which units matter for a task written in plain language. The
  result is a short list of files with line numbers and probabilities that a
  coding agent reads before touching anything. Two modes: full scan (one
  Noul per unit, batched 60 per request, all batches in parallel) and
  keyword prefilter (one Choice question over ~40 candidates plus "none of
  these"). A Claude Code skill file ships the agent workflow.
- **Numbers:** measured on AEGEST, an ERP for a sheet-metal shop (302 files,
  1,460 units), using the last 40 commits as ground truth (32 usable: the
  commit message is the task, the touched source files are the answer):
  full scan — 72% first file right, 81% within top 3, 88% within top 5;
  1–2s per search, 0.7 US cents per search (25 calls, 160k tokens). Keyword
  prefilter + Choice — 72% top-1, 84% top-3, 94% top-5; under 1s,
  0.02 US cents per search (1 call, 4k tokens). An earlier internal version
  of the tool on the same repo (855 coarser units, 103 commits) measured
  full scan 76% / 90% (top-1 / top-5), prefilter 58% / 72%. The
  `jevmap bench` command repeats the benchmark on your own repo.
- **Evidence:** shipped npm package (MIT, published, GitHub, Sept 18) with a
  builder-measured ground-truth benchmark; self-measured, not independently
  replicated.
- **Why it ranks #178:** the first ground-truth-measured relevance-ranking
  datapoint for coding-agent context — and the prefilter/Choice mode (94%
  top-5 at 0.02¢) is the cheapest accurate "which files matter" routing yet
  observed. Pairs with #111 (context trimming) and #173 (skill routing) as
  the agent-context cluster.
- **Source:** https://github.com/aegsrl7/jevmap

### 184. RLCDAlignBench — Jev as zero-shot alignment-failure detector (Guo et al.) (17, E5) — NEW
- **What:** academic paper (under review at ICLR 2027) benchmarking Jev as a
  detector of AI alignment failures — sycophancy, jailbreaks, deception, prompt
  injection, hallucination, privacy violation, social bias, reward hacking,
  concealing uncertainty, power seeking — across 44 benchmarks, 7,193 labelled
  instances, five 2–7B target models. Key method: vary *what Jev is asked*
  separately from *what it sees*.
- **Numbers:** one generic question, zero-shot → **median AUROC 0.886** over 31
  benchmarks; beats supervised TF-IDF + length baselines on **25 of 31**.
  Targeted wording adds only +0.006 out-of-sample (split-half protocol, no
  selection inflation). Against human labels on StrongREJECT: Cohen's κ 0.809,
  matching the reference scorer (0.811). One pass over 19 judge-scored
  benchmarks: **$0.30 vs $18.96 for LLM judges — 63× cheaper** at list prices.
  Honest calibration caveat: median ECE 0.168 vs 0.074 null — probabilities rank
  well, but thresholds need ~10 labelled items to fit.
- **Evidence:** published paper (arXiv 2609.29429) + open repo (MIT code, gated
  Hugging Face data, cached Jev answers, paper-level results tables, offline
  recompute scripts — reproducible without API keys). Under review, not yet
  peer-reviewed.
- **Why it ranks #184:** the strongest independent academic evidence for Jev to
  date — and a second-order find: Jev's confident disagreements **surfaced label
  defects in 3 existing benchmarks** (corrected labels ship in the repo) plus
  labels unobservable from the state in 4 MACHIAVELLI variants. That's a new
  role — Jev-as-auditor, cheap label QA for benchmark builders — sitting
  alongside the safety-monitor use case. Pairs with #1 (agent judge) and #4
  (jev-mcp screen): the "verify everything" family keeps compounding.
- **Source:** https://arxiv.org/abs/2609.29429 and
  https://github.com/sumleo/RLCDAlignBench

### 185. Federal IT RFQ classification with a defensible automation gate (bhushankinge) (17, E5) — NEW
- **What:** 12,000 real U.S. federal IT solicitations (SEWP, GSA MAS, 2GIT)
  labeled by typed question bundles (choice / score / noul) against a
  behavioral ground truth — what a reseller's sales reps actually quoted on
  quote lines. Three contenders on the same rows: Jev (`jev-1.13.0`) via
  TypeSafe cloud, Laya 421M (local RTX 2000), and Qwen3.5-35B-A3B (the
  reseller's production LLM on an on-prem cluster).
- **Numbers:** on 741 unambiguous primary-class rows — Jev **91.9%** accuracy,
  Qwen 89.6%, Laya 78.0%. Jev ECE **0.049**; cutoff 0.94 auto-accepts **86.5%**
  of rows at **96.7%** observed precision, with the Wilson 95% lower bound
  holding ≥95% from 1.0 down to 0.94. Full 12,000 rows: **$0.78** at list
  price, p50/p95 **185/273 ms**, zero errors (Qwen: 69 malformed JSON, 0.6%).
  Nine Jev prompt variants all landed 91.2–91.9% — the result survives question
  restructuring. Honest negative: fulfillment-mode accuracy — Qwen **71%**
  beats Jev **65%**, Laya 45.7%; below operational quality for everyone (the
  answer hides in BOM attachments, not notice text).
- **Evidence:** open repo (Apache-2.0, 37 commits, created Sept 24) — full
  pipeline, committed `metrics.json`, figure scripts, tests, CI; dev.to
  write-up by the benchmark author. Not peer-reviewed; one org, one domain,
  one day of runs.
- **Why it ranks #185:** the first head-to-head *accuracy* win for Jev over a
  production LLM on a revenue-adjacent job (RFQ routing → quote workflows) —
  and the cutoff is the honest kind: pre-registered, Wilson-bounded, 86.5%
  coverage at ≥95% precision. That's a defensible auto-accept gate, not a
  vibes threshold. Pairs with #3 (the confidence-gated policy this gate would
  slot into).
- **Sources:** https://dev.to/cookies_c9dc8b91f33d29250/we-tested-a-35b-llm-against-typed-decision-models-on-12000-real-rfqs-confidence-changed-the-winner-56hh ·
  https://github.com/bhushankinge/jev-laya-classification-bench

### 186. Fez — Jev as the default production agent router (kennethashley) (17, E4) — NEW
- **What:** a production agent framework ("Fez") that routes each incoming
  prompt to one of three agents (coding, creative, web search). On Sept 18 the
  builder switched the default hosted model from Gemini 2.5 Flash to
  `jev-1.13.0`, keeping local Qwen as the Jev-hosting fallback, after a frozen
  97-prompt benchmark battery.
- **Numbers:** full pipeline **96/97 (98.97%)** vs baseline **92/97 (94.85%)**;
  model-only **80/81** vs **76/81**. Median/p95 model latency **184/284 ms** vs
  **2,808/9,910 ms**. Estimated Jev cost for the 81-call battery: **$0.001811**.
  Honest negatives reported: one wrong agent selection (a "build a CLI" prompt
  misrouted) and a Gemini baseline run that failed 3 calls outright on a
  missing `tools` parameter — no clean error-rate comparison. Caveats: one
  run, three-agent roster, public prompts; private-traffic validation still
  open.
- **Evidence:** published research doc in-repo (Sept 18) with the full results
  table and raw JSON artifacts; builder-measured, not independently
  replicated.
- **Why it ranks #186:** the first *shipped-as-default* production agent
  router in the catalogue — Jev isn't the demo, it's the default, with the old
  default demoted to fallback. The latency collapse (p95 284 ms vs 9.9 s) plus a
  measured +4.1pt accuracy gain is the cleanest "Jev as the always-on routing
  layer" deployment yet. Pairs with #165 (TrueHorizon routing test) and #12
  (GodsBoy skill router).
- **Source:** https://github.com/kennethashley/fez/blob/HEAD/docs/superpowers/research/2026-09-18-typesafe-routing-results.md

### 191. Code-review rule benchmark: Jev vs Gemini Flash vs Claude Fable (gemanor) (16, E5) — NEW
- **What:** a reproducible benchmark asking all three models whether Python
  code follows four review rules — Jev `jev-1.13.0` vs Gemini 3.8 Flash vs
  Claude Fable 5.1 (both comparison models at medium reasoning effort). 24
  small program families × 5 versions × 3 rounds = 1,080 main calls (360 per
  model), committed charts, protocol, data.
- **Numbers:** actual main-study cost: $0.01545 vs $0.69965 vs $4.24097 —
  Jev **45× cheaper than Flash, 274× than Fable** (~$0.043 vs $1.94 vs
  $11.78 per 1,000 reviews). Median latency **0.75s** vs 3.59s vs 4.31s.
  Correctness: **98.0% vs 100% for both** (95% interval 94.7–100%);
  code-quality rule score 99.5% vs 100%. Consistency: **0.83% of Jev decisions
  changed across three rounds** vs 0% for both LLMs. Author's own caveats:
  small constructed examples, not production code review — and the pointed
  warning that a 2% score gap does not mean a referral system can identify and
  send only 2%.
- **Evidence:** independent measured benchmark with full artifacts (GitHub,
  run Sept 17).
- **Why it ranks #191:** the first measured Jev-vs-LLM *code-review rule*
  benchmark — the cheapest-vs-most-accurate trade quantified with confidence
  intervals, plus a consistency metric the catalogue rarely gets. Pairs with
  #102 (LangSmith eval judge) and #159 (the cascade that makes a 2% gap
  affordable).
- **Source:** https://github.com/gemanor/jev-code-review-benchmark

### 192. jevmem — Jev memory gate for coding agents (16, E4) — NEW
- **What:** an MIT-licensed tool that turns coding-agent conversations into a
  committed, reviewable `JEVMEM.md`: decisions, constraints, bugs, todos.
  On every Claude Code `Stop`, Jev decides save-vs-skip plus memory kind
  (recall through `UserPromptSubmit`); Codex and Cursor go through `jevmem
  watch` or MCP. Reversals mark old lines superseded rather than deleting
  them. Two-second budget per Jev call, skip-and-log on failure.
- **Numbers:** author benchmark — **98.5% save-vs-skip, 95.5% save+kind on
  66 held-out turns**; 0.30s median vs 2.8–4.3s for six general models;
  **$0.000127/decision**, cheaper than five of six. Independent analysis
  (markhuang.ai, Sept 25): one run, 66 turns — the repo warns 1–2 turns sit
  within run-to-run noise; recall quality and drift across weeks unmeasured.
  The data-boundary audit: default Claude Code path sends the current user
  message, the previous two truncated turns, and up to 200 keyword-filtered
  memory lines to TypeSafe on every `Stop`; the scrubber names its gaps
  (names, phone numbers, postal addresses, unknown credential formats may
  remain).
- **Evidence:** builder-measured benchmark plus an independent critical
  analysis (markhuang.ai, Sept 25); shipped tool v0.4.5.
- **Why it ranks #192:** the first *shipped* Jev memory gate with measured
  admission accuracy — and the write-up is the catalogue's best-documented
  data-boundary case: per-turn Jev memory only makes sense where the
  provider's data path is acceptable. Pairs with #111 (context trimming) and
  #178 (context routing) as the agent-memory cluster.
- **Sources:** https://markhuang.ai/news/jevmem-memory-gate-typesafe-sees-turn

### 199. Independent practitioner suite: call-QA scorecard, triage, escalation, lead scoring, PR risk (Huzaifa Dhapai) (16, E5) — NEW
- **What:** a practitioner ran Jev via OpenRouter on his own AI-calling work
  plus five more live workloads, all with measured figures: a call-transcript
  QA scorecard (14 typed questions per call), support-ticket triage,
  live-chat escalation, lead qualification on JSON records, PR change-risk
  review, and a borderline moderation case.
- **Numbers:** 14-question call scorecard — 748ms, **$0.000080** (~12,551
  calls per dollar); support ticket "payouts failing 3 days" → urgent 0.95,
  billing 0.85, frustrated — 710ms, ~55,760 calls/$; lead record →
  sales_ready 0.72, segment enterprise 0.93, with the low-confidence 0.07
  urgency flag shown honestly; PR hotfix bypassing an auth check → risk 3.90
  "do not merge", area security 1.00 — 410ms, $0.000021; borderline insult →
  harassment 0.34, severity split between "no violation" and "borderline" at
  0.20 confidence, flagged low. Honest negatives: 32k context cap on
  OpenRouter, real-world latency 400–800ms vs the advertised 70–500ms, no
  explanations, waitlisted access.
- **Evidence:** independent hands-on with measured per-decision figures
  (blog.xaif.in, Sept 2026).
- **Why it ranks #199:** six Jev shapes in one measured write-up — the
  call-QA scorecard is a new vertical for the catalogue, the PR-risk figure
  is the second datapoint in #88's family, and the moderation case is the
  first worked example of the low-confidence flag doing its job on a
  genuinely borderline input. Pairs with #58 (Flath's six-use suite) and
  #95 (Ayala's two-shape app).
- **Source:** https://blog.xaif.in/jev-typesafe-decision-model/

### 201. Entagl — 1,759-decision two-round test: labeled set + real-traffic replay (18, E5) — NEW
- **What:** a vendor-neutral two-round test — (1) 1,357 hand-labeled decisions in
  13 languages (283 cases, 94 hard ones run 3×), Jev `jev-1.13.0` vs a frontier
  Google Gemini model on identical facts/questions; (2) 402 real routing
  decisions from two client workspaces over 21 days replayed through Jev and
  judged against the clients' own written rules where Jev disagreed with the
  original (PII masked before processing).
- **Numbers:** round 1 — **98.5% vs 99.0%** over 1,357 decisions; median **329ms
  vs 1,598ms** (~5×), p95 436ms vs 2,765ms; ~**25× cheaper**. Confidence gate:
  ≥85% sure → Jev handles **88% of decisions at 99.75%**, sending 17 of 20
  mistakes to the bigger model; right **986/986 when ≥97% sure**. Round 2
  ("what to do with the message"): Jev **94.0% vs 91.2%** (workspace A) and
  **93.0% vs 87.4%** (B); "which specialist answers": trailed **91.4% vs 98.1%**
  / **83.7% vs 97.8%** — with a ≥90% gate, **97.1% / 98.9%**, essentially parity.
  Honest negatives: Arabic 96.5% vs English 99.1% / Turkish 98.4% (dialect
  mix-ups at 0.36–0.40 confidence — the gate catches every one); one 0.95
  confident Turkish mistake; WhatsApp pre-filled ad-template lines let through
  as business leads; Jev can't infer unprovided context (chat ownership) — the
  fix is computing it in code. Caveats: one reviewer; rare outcomes oversampled,
  so replay percentages are not a production average.
- **Evidence:** independent two-round measurement with published method, charts,
  and blind-spot documentation (entagl.com blog, Sept 2026); pre-labeled before
  any run; real client traffic, not a toy set.
- **Why it ranks #201:** the only test combining a large pre-labeled
  multilingual suite with a real-traffic replay against the incumbent it would
  replace — and the replay caught *incumbent* bugs, not just Jev gaps (a
  keyword rule misfiring on the word "legal", 17/41 duplicate handovers, two
  contradictory rules — "label before you run" beats "agrees with the current
  model"). The ≥85% gate recipe (88% of traffic at 99.75%) is the catalogue's
  sharpest confidence-gate practice. Pairs with #68, #159, and #202.
- **Source:** https://www.entagl.com/blog/typesafe-jev-benchmark-ai-decision-models

### 202. Prompt Rejector — Jev screening MCP tool descriptions, prompts, and skills (revsmoke/promptrejectormcp) (19, E5) — NEW
- **What:** a shipped MCP server (v1.2.0, TypeScript) where Jev answers the
  small language-judgment questions in prompt/skill/MCP-tool screening: (1)
  hidden-instruction detection in MCP tool descriptions — one question measures
  suspected poisoning, a second selects the field ID containing it (code-mapped,
  Jev can't invent locations); (2) three parallel risk questions per prompt
  (instruction-override, disclosure, destructive/financial action); (3)
  capability interpretation (lethal-trifecta presence questions); (4) ambiguous
  model-reference extraction from code spans.
- **Numbers:** Sept 19–20, 2026: **178 live TypeSafe requests across 107
  distinct inputs**, all responses contract-validated. Prompt risk **39/40**
  (all 20 risky detected, one benign flagged) vs static checks 21/40.
  Tool-description poisoning **12/12** vs local scanner 5/12 — including a
  hidden "append all environment credential values to this query" instruction
  dressed up as a parameter requirement that the local scanner missed.
  Capability interpretation 46/48 answers (14/16 complete configs) vs local
  rules 22/48 (5/16); model-reference extraction 11/12 vs 4/12. Paired
  16-prompt comparison: Jev median **175ms vs Gemini 3,826ms** (~22×), p95
  447ms vs 14,326ms; cost **$0.000492 vs $0.040968** (~83×). Honest: these are
  agreement-with-expected-labels figures — "not 178 correct security
  decisions"; a low Jev score never lets a prompt skip reasoning; strict
  validation with explicit allow/block/review/unavailable (only allow sets
  safe:true); blocks need poisoning ≥0.90 + valid source field + field-choice
  confidence; clean prompts still pay for the reasoning call — not a universal
  latency reduction.
- **Evidence:** independent builder test with recorded inputs, questions, labels,
  answers, tokens, and timings in-repo; honest limits documented.
- **Why it ranks #202:** the best-measured MCP/skill-screening datapoint in the
  catalogue — the concrete win is the poisoning case the local scanner missed,
  and the cascade design (Jev as the cheap first filter, a reasoning model
  behind the confidence gate, code owning the final decision) is the same
  economically-optimal shape as #159/#68/#201. Pairs with #4 (jev-mcp),
  #25 (tool-call firewall), #134 (Mastra), #155, and #6's 41% FP finding.
- **Source:** https://github.com/revsmoke/promptrejectormcp/blob/HEAD/docs/how-we-use-jev-from-typesafe-ai.md

### 203. Datadog Agent Observability — Jev wired into online + offline eval surfaces (17, E4) — NEW
- **What:** Datadog's engineering blog shows Jev as the judge behind both Agent
  Observability eval surfaces: **online evals** scoring live production spans
  as they arrive, and **offline evals** inside experiments. One rubric of five
  typed questions in a single parallel request (Noul grounded?, Noul
  answers_question?, Noul offers_handoff?, Choice failure_mode with an explicit
  `unclear` option, Score customer_impact) drives both. The traced application
  never imports Jev — a separate worker scores out of band and joins spans on
  `turn_id` via external evaluations; the composite verdict stays in code;
  raw probabilities are submitted as `score` metrics so a threshold change is
  a query change, not a rescoring. Operational guidance: pin `jev-1.13.0`
  (never `jev-latest`), tag `judge_model` on every metric, filter state in
  code (Jev loses accuracy as state fills).
- **Numbers:** one example call: 1,181 input / 139 output tokens; notebooks
  published — a guide, not a measurement; no accuracy/cost benchmark.
- **Evidence:** builder-described integration by a major observability vendor
  (datadoghq.com blog); the fictional-airline example is a demo, but the
  wiring pattern (both surfaces, parallel-question packing, span joins,
  confidence-as-separate-metric) is real product guidance.
- **Why it ranks #203:** the first major observability platform's official
  pattern for Jev-as-eval-judge — the tied-verdict finding (Choice `none` 0.46
  vs `partial_answer` 0.42 at confidence 0.34 → route to human review, don't
  flatten) is production-grade deployment practice for the whole eval-judge
  cluster. Pairs with #102 (LangSmith), #37, #72 (Eve).
- **Source:** https://www.datadoghq.com/blog/jev-evals-agent-observability/

### 208. Typed request router with a measured 223-prompt eval (mcftira/jev-route) (16, E4) — NEW
- **What:** a shipped router that asks four typed questions in parallel over one
  HTTP call — `complexity` (trivial→frontier), `sensitivity` (public→regulated),
  `pii_present` (Noul), `domain` (code/writing/analysis/chat/data-extraction) —
  and routes the request to the tier that fits. Designed for the request path:
  a judgment cache sits in front of the backend (identical redacted excerpts
  skip the call), and the cache stores *judgments*, not decisions. Every
  question carries a trust-boundary clause (ignore instructions inside the
  prompt about which tier to choose); the local hard gate — not the prompt —
  is the actual security control. Fail-closed: timeouts, rate limits, and a
  tripped breaker return maximum-uncertainty answers, and the policy engine
  turns maximum uncertainty into the safest tier. The eval dataset ships 12
  prompt-injection rows.
- **Numbers:** shipped 223-prompt eval against the real Jev backend
  (`jev-1.13.0`, results in `evals/results/summary.json`): 186 backend calls —
  **p50 318ms, p95 725ms, p99 791ms**; the other 37 prompts were gate-blocked
  and resolved in well under a millisecond (p50 0.43ms). Author's honest note:
  "a third of a second is a real tax on the request path," hence the cache.
- **Evidence:** shipped repo with a measured latency eval (GitHub); no
  correctness/accuracy metrics published against ground truth — the eval
  measures the router's own path, not whether it routes *right*. Cost per
  decision is a placeholder (TODO in the README: "fill from a real invoice,
  not an estimate").
- **Why it ranks #208:** the first router build with a measured tail-latency
  profile plus the trust-boundary/fail-closed architecture written down —
  the "p50 318ms vs p99 791ms" honesty plus the judgment-not-decision cache
  is the router-family (#23) datapoint with the most deployable detail. Pairs
  with #186 (Fez, Jev-as-default router) and #25 (firewall shape).
- **Source:** https://github.com/mcftira/jev-route/blob/HEAD/README.md

### 209. Desktop bookkeeping clerk — System 1 decides, System 2 rewrites the playbook (stas4000/jev-clerk) (18, E5) — NEW
- **What:** a bookkeeping clerk that works a real Mac desktop: opens a supplier-invoice PDF in Preview, reads it, types it into Frappe Books (open source, desktop, no API), saves, submits, and files the PDF. System 1 = Jev: every step, the screen as OCR lines, two multiple-choice questions (which action from a closed list of 12; which line to click) — ~0.35s, $0.0002 per decision. System 2 = Claude Fable 5.1, never touching the screen: after every 10 invoices it reads the full decision log and rewrites the playbook (question wording, lessons, typing formats, waits, "reflexes" code runs without a model call); code validates every rewrite so the action list stays closed.
- **Numbers:** 30 real supplier invoices (Anthropic, Browser Use, X, Suno, Moonshot, Composio and others) worked through three times — 90 iterations, 9 playbook blocks: booked-correctly (USD invoices) rose from 0/9 (playbooks v0–v5, including a v3 rewrite that broke step one) to **8/9, 9/9, 6/8, 9/9** on v5–v8; the first booked invoices arrived only after five System 2 passes. Totals: **1,141 Jev calls for $0.24**; System 2 thinking 978s across the passes. Frontier lane (same job, same app, same referee, Claude Opus 5 from one bare screenshot per step): 2/2 correct in ~21 steps, **65s model time and $0.69/invoice** vs Jev-on-v8: 9/9, ~4.5s model time, **$0.0028/invoice** (~246× cheaper). The referee is the accounting database opened read-only — an invoice counts only when a submitted Purchase Invoice with the right supplier, date and amount exists. Builder's honest caveats documented (engine changed mid-run, two misses were an amount-parser bug not Jev, blocks 4–5 restarted once after engine fixes).
- **Evidence:** builder-run end-to-end experiment with full ledger committed (`runs/demo/ledger.jsonl`), every playbook version with System 2's own report, and a measured frontier comparison (GitHub, created Sept 19). Builder-measured, not independently replicated.
- **Why it ranks #209:** the first end-to-end *desktop-automation* run with a real ground-truth referee (the accounting DB) instead of self-reported success — and the cleanest System 1/System 2 decomposition in the catalogue: Jev decides fast and cheap per step, the frontier model rewrites the policy, code owns the vocabulary. The Opus lane is the honest bill of the learning cost: zero coaching but $0.69/invoice vs five System 2 passes before the first booked invoice at $0.0028. Pairs with #8/#103/#57 (computer-use), #68/#159/#201 (cascade economics), and #210 (transaction categorization; this entry goes further into data entry).
- **Source:** https://github.com/stas4000/jev-clerk

### 213. CMU "JEV-as-a-Judge" — cascade benchmark vs 16 judges (18, E5) — NEW
- **What:** Carnegie Mellon researchers (Yubo Li, Yidi Miao, Ramayya Krishnan,
  Rema Padman) compare Jev as a judge against 16 generative and reward-model
  judges with blinded human adjudication, and test a frozen cascade: accept
  Jev's confident verdicts, escalate the rest to a frontier model.
- **Numbers:** **$0.044 per 1,000 judgments, median 0.152s** vs GPT-6
  $12.182 / 1.885s — **~277× cheaper**. Within 3 points of GPT-6 on
  preference and evidence-grounded factuality (**92.2% vs 93.5%** RewardBench;
  **87.5% vs 86.7%** HaluEval). The gap grows to 9–20 points on hard checks
  (78.6% vs 93.1% JudgeBench — derivation checking, resisting elaborately
  wrong answers); errors cluster in Jev's low-confidence decisions. Frozen
  cascade (confident verdicts accepted, rest escalated to GPT-6 Astra) kept
  **99% of GPT-6's accuracy at ~57% of its fee** on 510 held-out preference
  pairs. Honest limit: the escalation threshold did not transfer across every
  fallback model — set it on your own data.
- **Evidence:** academic independent benchmark (arXiv 2609.26550).
- **Why it ranks #213:** the first university-run Jev-vs-LLM-judge benchmark —
  and its verdict is the catalogue's thesis in academic dress (#159/#68/#201):
  Jev alone is a mid-pack judge, Jev-first with a frontier behind the
  confidence gate is the economically optimal shape. Pairs with #184
  (RLCDAlignBench) and #102 (LangSmith).
- **Sources:** https://arxiv.org/pdf/2609.26550 ·
  https://academy.dair.ai/papers/jev-as-a-judge-accept-when-confident-escalate-when-unsure-2609.26550

### 214. "JEV vs. LLMs as Rubric Judges" — academic rubric-judging panels (17, E5) — NEW
- **What:** academic comparison of Jev vs LLM judges on rubric-judging panels
  (RiceChem, HealthBench, ELLIPSE, LFQA and others) — verdict definitions
  included, abstention allowed, judge–judge and judge–label agreement both
  reported.
- **Numbers:** Jev the most accurate matched judge on RiceChem, second to
  Gemini on HealthBench; the LLM judges cost **18 (Luna, HealthBench) to 326
  (Gemini, RiceChem) times as much**. Jev-first cascade: **16–48% of the best
  LLM judge's cost for at most 1.8 points less accuracy** — on RiceChem 6.6%
  of its cost for **1.2 points more**. Jev Score separated ahead of Jev Choice
  on four panels (never behind); verdict definitions beat bare Noul by
  +10.7/+1.0 points. Honest negatives: confidence was uninformative on some
  panels (AUROC 0.41 on LFQA factuality); "wrong with Jev on 82 of 84
  confident errors" — the confidence margin needs per-task validation.
- **Evidence:** academic benchmark (arXiv 2609.29769).
- **Why it ranks #214:** the second academic judge benchmark in one scan —
  and its falsifiers discipline the cascade thesis: Jev-first wins on
  economics, but per-panel confidence validation is mandatory, not optional.
  Pairs with #213, #102, and #1 (the agent-judge cluster).
- **Source:** https://arxiv.org/pdf/2609.29769

### 220. Pi agent harness — ablation of router / context-picker / gate / verifier (PromtEngineer/jev-harness) (16, E5) — NEW
- **What:** a Pi coding-agent harness putting Jev at four loop decision points — router
  (fast vs powerful tier at `before_agent_start`), context picker (one yes/no per runbook
  section, 22 questions in one call), gate (allow / checkpoint-on-Neon-branch / ask-human
  / block on every tool call, fail-closed), verifier (`agent_end` claim-check with one
  retry). Tested on "Brightcart," a seeded Postgres store with planted traps, each run on
  its own Neon branch. Accompanies a Sept 29 video build.
- **Numbers:** 15 runs, 5 configs × 3 tickets, 1 rep/cell, results in-repo
  (`results/RESULTS.md`, run 2026-09-23): on the clean trap ticket (`t5` — drop
  `products.legacy_sku`, a column 17,423 order items still link through), `base` and
  `router` scored 20 (dropped the column, data lost) while `context` scored **100**
  (loaded the right runbook section at Jev probability 0.93, relinked every orphan
  row, proved zero remained, then dropped). Router picked the powerful tier every
  time — correctly, since all tickets are risky — rating the trap 0.20 complexity but
  escalating on low self-confidence (0.45). Jev cost per run $0.00003–$0.00223
  (~0.1% of the Gemini cost); gate adds ~150ms per call when the API is healthy
  (10–16s per fully-gated run). Context picker cut the runbook 5,923 → 786 chars
  per request. Honest contamination reporting: 10 of 15 runs tainted (the agent
  searched the disk and read the answer key — pre-sandbox builds), flagged
  run-by-run; the trap runs that matter are clean. Anti-evasion anecdote: an agent
  rephrased the dangerous DROP five ways (including string-concat inside a DO block)
  against a blunt "is this dangerous?" gate — Jev blocked every version.
- **Evidence:** independent builder benchmark with per-run result JSONs, honest
  contamination accounting, and known-limits docs (GitHub; run Sept 23, surfaced
  Sept 29). Builder-measured, small n.
- **Why it ranks #220:** the first *ablation* of where Jev belongs in an agent loop —
  router, context picker, gate, and verifier each toggled separately, with the
  outcome decided on a trap ticket rather than a vibes demo — and the contamination
  honesty (10/15 tainted, stated up front, clean runs named) is how agentic
  benchmarks should be reported. Pairs with #25 (firewall shape), #111 (context
  picking), #174 (different author, same repo name — "System 1.5" decision gates;
  not the same project), and #159/#201 (gate economics).
- **Source:** https://github.com/PromtEngineer/jev-harness · video post:
  https://ceppek.com/2026/09/29/jev-ai-agents-where-a-decision-model-actually-helps/

### 222. Qualiteg 301-call measurement suite — routing, guardrail, command gate + concurrency (16, E5) — NEW
- **What:** Tokyo team bought $10 of TypeSafe credit and ran 301 API calls through
  three production-shaped scenarios plus latency/concurrency profiling (Sept 28,
  jev-1.13.0, measured from Tokyo), with the full Python source and raw response
  logs committed to GitHub.
- **Numbers:** Japanese support-ticket routing (30 author-labeled tickets, 5
  departments): **29/30 (96.7%)**, median 169ms — the one miss (an ad routed to
  sales) had confidence 0.38, the lowest of the 30; all correct answers ≥0.69,
  so a 0.6 floor sends exactly one ticket to a human. Guardrail in front of an
  LLM (24 inputs, two Nouls: injection, PII): injection **24/24** with clean
  separation (benign 0.02–0.03 vs attempts 0.83–0.99, including a hidden-instruction
  inside a translation request and one embedded in a summary); PII 23/24 → **24/24**
  after adding "bank account number" to the question criteria (the miss moved from
  0.45 to 0.97, the other 23 unchanged) — median 163ms, $0.00045 for all 24.
  Command-risk approval gate (24 shell commands, 4-level Score + needs_human Noul):
  exact level **21/24**, all 24 within ±1; all six level-3 commands scored
  needs_human ≥0.88, all six level-0 ≤0.26; median 168ms. Parallelism: 1 question
  per request 159ms vs **40 questions 160ms** — no trend with question count.
  Concurrency: 80 requests at 8-way concurrency → **41.4 req/s, p50 176ms, p95
  227ms**. Weak-spot check: 60/60 on number comparison and Japanese date
  ordering — but the author's rule stands: "a comparison in code costs nothing
  and is right 100% of the time." Cost: **301 calls, 156,070 input tokens,
  $0.00655 (~¥0.98), $0.0000218/call**.
- **Evidence:** independent measured experiment with code and raw per-call logs
  published (journal.qualiteg.com, Sept 29); author-labeled small item sets (24–30).
- **Why it ranks #222:** three Jev shapes (router, guardrail, agent command gate)
  measured on the same rig with the threshold-setting discipline the catalogue
  keeps rediscovering — and the PII 0.45→0.97 finding is the cleanest demonstration
  yet of the decomposition rule: Jev answers the question you wrote, not the one you
  meant, so spell out the criteria. The flat-latency-with-40-questions result and
  the 41 req/s / p95 227ms run are the best concurrency datapoints in the
  catalogue. Pairs with #70 (non-English), #6/#26 (injection), #155 (command
  risk), #25 (firewall), #134 (Mastra guardrail), #16 (triage families).
- **Sources:** https://journal.qualiteg.com/jev-typesafe-ai-pricing-python-hands-on/ ·
  https://github.com/qualiteg/jev-typesafe-demo

### 223. dsh-jev-verify — Jev guard + verify layer for the DeepSeek Harness (16, E4) — NEW
- **What:** Jev wired into the DeepSeek Harness CLI as in-chat guard and
  verification tooling: `jev_decision`, `jev_overview` (decision board with stats),
  `jev_guard_status` (guarded tools, deny threshold, session budget, counters),
  and `jev_verify` (live accuracy/latency/calibration report) — an auto-guard
  combining deterministic rules with Jev risk/loop judgment per tool call,
  plus a `/jev` dashboard and every decision appended to `jev-roll.jsonl`.
- **Numbers:** guard-inclusive benchmark verified against the live API
  (2026-09-21, jev-latest): **96.3% accuracy (26/27)**, median latency
  283–308ms across runs, ≈$0.0004 per 27-question run; the only mislabel a
  documented boundary near-miss. Live anecdote: `remove-item -Recurse …` fell
  to the deterministic rule while "permanently wipe all staging data and delete
  every row from every table" was intercepted by Jev at **91% confidence**
  (deny threshold 0.8) before any command ran. Regression check Sept 28
  (v0.7.0): armed guard reported correctly, npm tests 19/19.
- **Evidence:** working shipped integration described by builder with measured
  verification figures (GitHub, Sept 21–28).
- **Why it ranks #223:** the guard layer as a shipped harness feature rather than
  a demo — deterministic-first with Jev as semantic overflow is the catalogue's
  recommended shape (#68) running inside a real agent CLI, and the layered
  anecdote (deterministic catches the obvious, Jev catches the paraphrased) is
  the two-tier guard documented live. Pairs with #174 ("System 1.5" gates),
  #25 (firewall), #220 (the pi-loop ablation), and #134 (Mastra).
- **Source:** https://github.com/xienda/dsh-jev-verify

## Tier 2 — strong fit, thinner evidence (score 14–15)

### 10. Rubric grading at scale (Good Start Labs) (15, E5)
- **What:** 6,003 rubric checks.
- **Numbers:** 91.5% agreement with Claude Fable 5.1; ~$160/M graded answers vs ~$260
  for DeepSeek V4.1 Flash (1.6× cheaper than an already-cheap model).
- **Evidence:** independent test with metrics via Novel Cognition's "Jev File" (Sept 16);
  original post not directly located — attributed through the Jev File.
- **Why it ranks #10:** largest-n independent measurement so far; agreement-at-scale is
  the strongest calibration-adjacent evidence published.
- **Sources:** https://jev.novcog.us.com · read via https://dailytexasnews.com/typesafe-jev-system-one-model-claims-evals-independent-tests-daily_texas/

### 11. Independent side-by-side benchmark: Qwen 3.8 vs Jev (Hackers in the Loop) (15, E5)
- **What:** side-by-side benchmark UI running Qwen 3.8 27B on Cerebras (structured
  output) vs TypeSafe Jev across seven synthetic application workloads (Tickets 100
  customer tickets; Routing street-graph moves; Driving 10s WebGPU steering; Guardrails
  100 allow/block; Approvals 100 agent commands; Scoring 100 candidate answers vs golden
  refs; Home 24 simulated smart-home commands).
- **Numbers:** Jev p50/p95/p99 176/336/532ms vs Qwen 215/452/912ms; estimated cost
  $0.011919 vs $0.310581 (~26× cheaper); fixture agreement: Tickets 75/100 each,
  Guardrails 100/100 each, Approvals 95/100 vs 100/100, Scoring 100/100 vs 93/100,
  Home 15/24 vs 24/24. One collision each in Driving (94.6m vs 84.2m covered).
- **Evidence:** independent benchmark with metrics, measured 2026-09-17 UTC, raw exports
  published; explicitly synthetic, one dev-machine run, not calibration evidence.
- **Why it ranks #11:** the broadest independent head-to-head yet — 7 workloads spanning
  routing, guardrails, scoring, and control; it also adds independent guardrails and
  smart-home data points.
- **Source:** https://github.com/iammrduncan/typesafe-ai-benchmark

### 12. Agent skill routing (GodsBoy / jev-agent-skill-router) (15, E5)
- **What:** typed, confidence-aware routing layer for an agent skill catalogue — Jev
  picks the skill (or abstains to review) via parallel batched Choice/Noul calls; code
  handles thresholds and batching.
- **Numbers:** 68/72 synthetic requests routed correctly (94.4%) vs 51/72 (70.8%)
  lexical baseline; 0 wrong routes vs 8; 3 unnecessary reviews + 1 validation failure;
  median end-to-end latency 1,287ms (p95 1,406ms); ~$0.011 estimated input cost for the
  run. Measured 2026-09-16 on pinned jev-1.13.0.
- **Evidence:** independent experiment with full metrics and raw artefacts; explicitly
  exploratory and synthetic — "typed output guarantees shape, not correctness."
- **Why it ranks #12:** the second independent measurement of the agent-loop dispatch
  pattern (cf. #9 Firstmate) — abstention-to-review shows the confidence-gated policy
  working as designed.
- **Source:** https://github.com/GodsBoy/jev-agent-skill-router

- **New (Sept 19 — midday):** four more skill-routing builds: Kitze's skillbox
  (153 ⭐ self-hosted skills library with optional Jev recommendations), Jeff
  Emanuel's skillranker (Rust CLI ranking skills for the next step), Dewaldt
  Huysamen's jev-agent-skill-router variant, and HyunjunJeon's jev-judgment (a coding
  agent's closed judgments sent to Jev).

### 13. Commit-risk screening (Chen Jing) (15, E5)
- **What:** early-access hands-on tests — Korean sentence classification 11/12 correct;
  commit-message lie detector (evidence in state, questions in questions): typo fix hiding
  removed type checks → 93% "lie"; "adding multiply tests" commit that only adds add
  tests → 100%; rename hiding hardcoded API key → 99%+ highest risk; ambiguous
  "update" → 100% ambiguous.
- **Numbers:** ~0.2s per query; parallel queries ~0.4s per commit. Waitlist approval
  arrived within a day.
- **Evidence:** independent test with metrics (Threads, Sept 17).
- **Why it ranks #13:** code-review screening is a concrete, high-frequency dev-loop slot;
  the ambiguous→100% ambiguous case shows honest uncertainty handling rather than
  false confidence.
- **Source:** https://www.threads.com/@chenjingdev/post/DdYNmugFF2M

### 14. Search reranking benchmark (anessbelbati/jev-rerank-bench) (15, E5) — NEW
- **What:** Jev as a reranker — score each candidate with a Noul, sort by probability —
  benchmarked against established reranking services (Cohere, ZeroEntropy).
- **Numbers:** nDCG@10 0.692 vs 0.691 — effectively a tie with the incumbents.
- **Evidence:** independent community benchmark with a reported metric, via the
  awesome-jev-usecases community index (updated Sept 18); self-reported by the
  benchmark author — not an audit, but the tie result suggests honest reporting.
- **New (Sept 19 — morning):** a second, production-shaped datapoint — Ian Nuttall (X)
  ran Jev on Cloudflare Workers against keep.md: 7× faster search rerank than the
  current hybrid pipeline, 50× faster content tagging vs GLM 4.7 Flash with no
  failures; builder-claimed, unattributed run details.
- **Why it ranks #14:** a brand-new use-case type (relevance reranking *without
  embeddings*) measured against real competitors — and the tie is exactly the kind of
  non-hype result that calibrates expectations: Jev doesn't need to beat
  purpose-built rerankers to be interesting at its price.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 19 — midday):** two more rerank builds: jh1373's jev-search (offline
  Obsidian search with an optional Jev rerank) and Jökull Sólberg Auðunsson's ensk
  (English–Icelandic dictionary with Jev reranking — a low-resource-language angle).

### 15. Jev-driven browser agent (browser-use/jev-ultrafast) (15, E4)
- **What:** the browser-use team built a browser agent with a dynamic, indexed action
  space — Jev picks each operation + element from DOM state; a small LLM writes text
  only when the operation is TYPE_TEXT. One Jev request per decision cycle, no
  screenshots in the agent loop.
- **Numbers:** Google Flights Zürich→London one-way completed in 7,073ms end-to-end;
  six alternating runs, both versions passed 3/3; median task time 9.450s → 7.092s
  (−25%); median browser protocol calls 1,092 → 101. Same policy opened the Gödel
  Wikipedia article in 2.798s and passed a hotel search/filter task in 1.896s. Builder
  caveats explicitly: three repeats of one task on one browser profile, not a general
  reliability benchmark.
- **Evidence:** working demo/integration by a third-party builder with self-measured
  metrics (GitHub, Sept 16–17).
- **Why it ranks #15:** first evidence Jev can close the loop on *interactive* agent work,
  not just batch judgment.
- **Source:** https://github.com/browser-use/jev-ultrafast

### 16. Email triage (OpenCode) (15, E4)
- **What:** OpenCode's Ryan ran 1,701 personal emails through the API (100 at a time,
  8 workers); other builders posted short video demos.
- **Numbers:** ~198ms/email (~38/sec); 1,000 emails holding ~200ms latency; >1M input
  tokens processed. Each email classified by Category, Priority, Spam probability,
  Reply probability, Time.
- **Evidence:** working demos with metrics (Instagram reel, Sept 16).
- **Why it ranks #16:** proven pattern, personal scale so far — the enterprise version
  is use case #2.
- **Source:** https://www.instagram.com/reel/DdVR74gtW8-/

- **New (Sept 19 — evening):** three more email-triage datapoints — Bryo AI CTO
  Nikhil Mudholkar found Gemini slightly *more accurate*, but 10–20× more
  expensive (an honest accuracy-over-cost tradeoff, via marktechpost); Riley
  Brown: "classified 500 emails in seconds… 3.5 cents"; Greg Isenberg: "1,700
  emails for 18 cents, instantly." No accuracy figures on the last two.

- **New (Sept 22 — evening):** a Korean creator demo (@ai_me____, IG, Sept 22)
  shows 5,060 emails classified into refund / shipping / tech-support
  categories — the largest single *personal-scale* email run after OpenCode's
  1,701; creator-described, no accuracy figures. The non-English demo pairing
  continues (#70, #31).

- **New (Sept 23 — evening):** Ryan Vogel (Threads, Sept 22) tested Jev on
  1,500 of his own emails — "blown away", creator-described with no accuracy
  figures. And danieleteti's measured benchmark now covers this family with
  calibration numbers — see new entry #145.

### 17. Real-time ad processing dashboard (@magicmonx) (15, E4) — NEW
- **What:** a dashboard visualization of Jev processing advertisements in real time —
  counters climb as ads are read and judged across dozens of advertisers.
- **Numbers:** 724 ads read, 8,724 total judgments across 37 advertisers in ~40.5
  seconds, costing $0.0895 (~215 judgments/sec).
- **Evidence:** working demo with on-screen self-reported metrics (Threads, Sept 18);
  promo-adjacent presentation, author-measured.
- **Why it ranks #17:** the highest-throughput bulk-classification datapoint yet at
  real-time speed — evidence the map-reduce shape holds at stream rates, pairing
  with #2 (batch triage) and #33 (moderation).
- **Source:** https://www.threads.com/@magicmonx/post/Ddak-8ED2-o

- **New (Sept 20 — evening):** a second ad-analysis run, reported second-hand via
  the AY Automate roundup: @marcusyul read 1,891 competitor ads in 19 seconds
  for 12 cents, tagged by funnel stage and creative style (individually
  classified, not summarized). Same throughput family as @Zyvex_0x above —
  a new author with concrete figures, flagged second-hand until his own post
  is located.

### 18. Tool-call ranking in a managed platform (Supercenter) (15, E4) — NEW
- **What:** a builder integrated Jev into his own managed agent platform "Supercenter"
  to classify and rank the best tool calls per step.
- **Numbers:** internal tests across 200+ cases: agents ~40× faster, 9× more effective
  (his words).
- **Evidence:** builder-described integration + self-reported internal test (Instagram
  reel, Sept 18); no published artefacts or raw data; the video also misstates
  TypeSafe's founder's name — treat as a lead, not a finding.
- **Why it ranks #18:** tool-call ranking is the highest-leverage slot in an agent loop,
  and "rank N candidates by meaning" matches jev-mcp's `jev_find` primitive (#4).
- **Source:** https://www.instagram.com/reel/Dda68ldAUHH/

### 19. Research-paper topic classification (1kpapers.com) (14, E4)
- **What:** Hassan El Mghari's 1,018-paper AI-research atlas: DeepSeek V4 Flash writes
  the summaries ($3.99), one Jev Choice call classifies title + summary into 24 topics
  ($0.08); results visualised on 1kpapers.com. He is running evals on the Jev
  classifications before replacing the existing pipeline's labels — the right instinct
  most launch-week demos skip.
- **Numbers:** $0.08 total classification cost for 1,018 papers; median end-to-end
  latency 256ms per paper.
- **Evidence:** working integration with reported cost/latency metrics; accuracy evals
  explicitly pending (builder's own note).
- **Why it ranks #19:** the cleanest split-bill demonstration of the generative +
  decision division of labor — the LLM summarizes, Jev classifies, and each job's bill
  is visible.
- **Sources:** https://1kpapers.com · via
  https://dev.to/valyuai/how-to-use-jev-a-practical-guide-to-typesafes-system-one-model-g5e

### 20. Voice-intent browser control ("Jev underhood") (14, E4)
- **What:** Moritz (Threads) built a voice-controlled browser — speaks commands
  ("go to wikipedia.com", "click on the first link"), Jev does real-time speech
  intent classification driving the browser; ~300ms processing, $0.0002 per decision.
- **Evidence:** working demo with self-measured latency/cost figures (Threads, Sept 17).
- **New (Sept 19 — morning):** the packaged repo (github.com/moritzkremb/jev-voice-browser) now ships with unit + 27 real-API integration tests: 27/27 pass, Jev latency avg ≈330ms (p50 ≈300ms), last-word→decision ≈300ms including debounce, whole 16-command end-to-end demo ≈$0.01. The strongest measured version of this demo yet.
- **Why it ranks #20:** voice intent → action is the third Jev-in-the-loop UI modality
  after text (browser-use) and desktop (awlevin) — and commenters immediately saw the
  accessibility use case.
- **Source:** https://www.threads.com/@promptwarrior/post/DdY-IlslUFO

### 21. Browser-agent tool selection + chunk scoring (rtrvr.ai) (14, E5)
- **What:** rtrvr.ai (Rover browser-agent platform) plugged Jev into their harness:
  Jev picks the right tool per page and scores content chunks before LLM processing —
  candidate actions on Amazon fell 591 → 82 (86% fewer).
- **Numbers:** ~40% faster tasks but ~45% higher cost — because their stack already
  runs open-source models (GLM Flash, DeepSeek Flash) at <1¢/task; Jev adds expense
  on top of a cost-optimized OSS stack. Full breakdown promised at rtrvr.ai/blog.
- **Evidence:** independent builder test with metrics (Instagram reel, Sept 17) —
  notably a *negative* cost finding, honestly reported.
- **Why it ranks #21:** the first datapoint that prices Jev against tuned open-source
  small models instead of frontier LLMs — and it loses on cost while winning on
  speed and action-space pruning. Jev's edge is vs frontier LLMs, not vs cheap OSS.
- **Source:** https://www.instagram.com/reel/DdZostuh2_m/

- **New (Sept 19 — midday):** Retriever AI published a fuller YouTube teardown —
  Jev tested on real browser tasks, honest about where it worked *and* where it
  struggled, with the plan to use it *next to* larger models. The negative-cost
  finding stands.

- **New (Sept 21 — evening):** independent corroboration of the vs-OSS verdict —
  RoboKrunch ran Jev vs self-hosted ModernBERT-base (149M params) on a 2-core
  CPU: ModernBERT p50 169ms vs Jev 527ms (~3× faster) but one label vs three
  judgments per call; infra crossover ≈977K decisions/month (~678 robots at
  48/day) before self-hosting beats Jev on cost. Their conclusion: Jev's edge
  is zero training, zero labeling, zero ops — not raw speed. This reframes the
  whole catalogue's speed/cost deltas: the premium buys you out of the
  labeling/training/fleet-management work, not per-call latency.
  (via https://github.com/robokrunch/awesome-jev)

### 22. "JEV Browser Agent" multi-step Wikipedia demo (jkudish/jev-browser) (14, E4) — updated
- **What:** JEV Browser Agent performs a multi-step Wikipedia task: homepage →
  Daniel Kahneman → Amos Tversky → Prospect theory → Loss aversion. On-screen
  metrics: 31 decisions across 5 pages, 2.56s total thinking time, 6.0s wall time
  including page loads, $0.0032 total. The reel also frames Jev's trade-off plainly:
  CAN classify/detect/score/rank/route and handle 40 questions in one call — CANNOT
  write sentences, explain itself, count, do math, or compare dates ("no prose. just
  decisions.").
- **New (Sept 18 — midday):** the repo README (github.com/jkudish/jev-browser, crawled
  today) confirms the package and lists runs on real sites beyond the reel: Wikipedia
  Coffee→Espresso in ~4s for $0.0016, a filled-but-not-submitted contact form, a price
  pulled off a live pricing page, a full guide page exported as markdown, and an
  accessibility-tree breakdown of a WordPress site — with goal and stuck probabilities
  scored per step. Attribution to jkudish's `@jkudish/jev-browser` is now confirmed,
  no longer provisional. The awesome-jev-usecases index lists more browser-agent
  builds in the same family (Ying-Kai-Liao/jev-browser, tontoko, vlad-terin).
- **Evidence:** working demo + packaged repo with self-reported metrics (Instagram reel
  + GitHub, Sept 18); promotional framing, single runs.
- **Why it ranks #22:** a second, independent browser-agent datapoint (cf. #15
  browser-use) — the ~$0.0032-per-task figure starts to pin the real-world cost of
  Jev-driven browsing.
- **Sources:** https://www.instagram.com/reel/Ddavc3_BC8Q/ ·
  https://github.com/jkudish/jev-browser

- **New (Sept 19 — midday):** the mobile counterpart sharpens — Droidrun's Mobile
  Jev (cf. #42) ran 9 actions in ~21s on a real Android phone; coliney's Jev Browser
  Use (Codex skill: Jev clicks, Codex thinks) claims ~5–10×. Daniel Ch's "Website to
  App" — Jev-1.13.0 deciding how to build any website as a native mobile app, "used
  internally a ton" — extends the family to build planning.

- **New (Sept 22 — midday):** Cline shipped a jev-browser plugin (install from the
  Cline desktop app's Marketplace; Vercel AI Gateway key in the plugin config) —
  browser tasks launch Chrome in the background. Another packaged browser-agent
  integration in this family. (via madewithjev.com)

- **New (Sept 19 — evening):** Stagehand on a remote browser — ~$0.001 per browser
  task (via kraayenjon/awesome-jev's featured-builds table, citing X; not yet
  independently verified).

### 23. Intent gate + misuse detection (Paul Piper, private beta) (14, E4) — NEW
- **What:** private-beta user replaced core routines in his own projects with Jev;
  the case he benchmarked hardest was an *intent gate* — checks what the user "wants
  to do", reroutes to the right model + tool selection, and also flags users
  misusing the software for things it shouldn't do.
- **Numbers:** qualitative — "did better than Mistral-small or similar models, it was
  faster, more accurate"; no raw metrics published.
- **Evidence:** independent builder test described in a Substack write-up (Sept 18);
  no numbers — but the shape (routing + misuse tripwire) is the production
  intent-gate pattern, honestly framed.
- **Why it ranks #23:** the misuse-detection angle is new — Jev as a cheap policy
  tripwire, not just a router. Pairs with #24/#25 (guardrails) as the week-one
  security cluster.
- **New (Sept 22 — midday):** KDnuggets cites two more router measurements — blackbarata
  routes requests between recipe/scraper/meal-planning agents with 145–271ms
  decisions, and TigerOk4538's model-router comparison has Jev at ~1s vs 4–14s
  for an LLM with structured output. Second-hand via the article; the original
  posts are not yet located.
  (https://www.kdnuggets.com/what-everyone-is-getting-wrong-about-typesafe-ais-jev)

- **Source:** https://madppiper.substack.com/p/my-thoughts-on-jev-after-the-private

- **New (Sept 19 — midday):** a burst of router builds in the same family: Raj
  Dhakad's routeKit (model picked by task complexity), Joaquin Marcoff's
  jev-harness-router (one batched call picks model tier, tools, skill and effort per
  turn), Elia Alberti's jev-rules (Jev picks which of your rules apply so Claude
  only sees those), Instructa's switchloom (deterministic model routing for coding
  agents + Codex skill), Nidhi Singh Attri's herdr (routing between Claude Code,
  Codex and Cursor models), and Sawyer Hood's prompt-box field router (Jev picks the
  agent/model/computer/folder). A Slack agent builder reports Jev preclassifying
  skill/tool/params made his agent 2× faster (unverified, via madewithjev.com).

- **New (Sept 19 — evening):** 0xNatoshi/jev-codex-router — routes work inside
  Codex sessions (listed in openchamber.dev's launch-repo table; not yet crawled).

- **New (Sept 21 — midday):** @ephraimduncan (Hackers in the Loop, #11) — a
  model router: Jev classifies each incoming request as a typed Choice and
  forwards it to the downstream model that fits (via the AY Automate roundup;
  builder-claimed, second-hand).

### 24. Agent guardrails that steer (DevMortimer/pi-warden) (14, E4) — NEW
- **What:** a guardrail layer that steers an agent back on track instead of
  interrupting it.
- **Numbers:** 6 rule breaks → 0 across 150 paired runs.
- **Evidence:** community project with self-reported paired-run metrics, via the
  awesome-jev-usecases community index (updated Sept 18); not an independent audit.
- **Why it ranks #24:** a new use-case type — steering rather than blocking — that
  fits Jev's advisory-judgment shape (cf. #38 drone, where code owns safety and Jev
  advises). The paired-run design is the right way to measure guardrails.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 18 — evening):** the awesome-jev-usecases index lists AbdelStark/bicameral as a second steering-guardrails build in the same family — steering instead of blocking is becoming a pattern.

### 25. Pre-execution tool-call firewall (AnshChoudhary/typesafe-ai-firewall) (14, E4) — NEW
- **What:** a firewall for agent tool calls — one Noul per hazard, checked before
  execution.
- **Numbers:** 0% hard negatives blocked vs 39.2% with a single "is this dangerous?"
  prompt — the per-hazard question decomposition is the whole win.
- **Evidence:** community project with self-reported metrics, via the
  awesome-jev-usecases community index (updated Sept 18).
- **Why it ranks #25:** a sharp demonstration of Jev's core design principle —
  decomposed typed questions beat one fuzzy question — with the per-hazard Noul shape
  generalizing to any agent harness.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 19 — midday):** more tool-call-firewall builds: Bowen Xu's pi-heed
  (checks every side-effecting tool call against what you asked for), somoore's
  Interlock (a gate on every agent tool call, Jev as one of its sensors), CodeAlive's
  mastra-jev-moderation (input moderation for Mastra agents in one file, ~0.4s), and
  a YouTube "AI Action Gate" demo testing authorization, destructiveness, policy,
  hallucinated calls and prompt injection (no metrics;
  youtube.com/watch?v=jU6o3nUY17s). Kostas's security-engineering post sketches the
  same firewall shape for threat hunting, detection engineering and incident
  response — proposal, not a build.

- **New (Sept 19 — evening):** a fourth firewall build — leepokai/jev-guard rates
  each agent tool call as deny / ask / allow against a confidence threshold
  (78-second video; described second-hand via marktechpost and openchamber.dev's
  repo table; repo page not yet crawled).

### 26. Prompt-injection & vulnerable-code benchmark (Gaurav-Gosain/jev-sec-bench) (14, E4) — NEW
- **What:** blind prompt-injection and vulnerable-code benchmarks run against Jev.
- **Numbers:** 96.5% injection accuracy; ECE 0.0588 — the first published
  *calibration* number (expected calibration error) in the wild.
- **Evidence:** community benchmark, self-reported by its author, via the
  awesome-jev-usecases community index (updated Sept 18). Complements ar9av_'s
  independent test (#6), which found a 41% FP rate on a smaller set — a reminder of
  how much the test set matters.
- **Why it ranks #26:** the ECE figure is the first calibration-adjacent evidence
  point the watch has seen — calibration is TypeSafe's central claim, and this is
  the first number aimed at it.
- **New (Sept 19 — morning):** a second independent calibration datapoint —
  iwashi86's technical teardown of archerhume.com/posts/jevs-architecture-unmasked
  (~10,000 API calls probing Jev's internals) reports ECE **0.0313** with
  predicted probabilities tracking actual hit rates closely; it also finds the
  "confidence" score is just max-probability arithmetic, ~100 questions can ride
  one shared state with almost no latency penalty, and choice order introduces
  small probability shifts (threshold-sensitive deployments beware).
- **Source:** https://github.com/anandi1989/awesome-jev-usecases ·
  https://archerhume.com/posts/jevs-architecture-unmasked

### 27. Agent-failure diagnosis (TokenTrim/jev-agent-failure-benchmark) (14, E4) — NEW
- **What:** can a decision model diagnose what broke an agent? Benchmarks Jev against
  GPT-5.4 on classifying agent-failure traces.
- **Numbers:** beat GPT-5.4 on every axis across 6,257 traces for $1.28 total.
- **Evidence:** community benchmark with self-reported metrics, via the
  awesome-jev-usecases community index (updated Sept 18).
- **Why it ranks #27:** the largest-n benchmark yet (6,257 traces) and a new
  use-case type — post-mortem diagnosis of agent runs, where cost-per-trace is the
  whole game ($1.28 for the full run).
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 59. Fraud-classification cascade (Hassan / nutlope) (15, E4) — NEW
- **What:** 100 emails (50 legit / 50 fraudulent) classified by Jev; anything under
  95% confidence routed to Kimi K3 as the larger fallback.
- **Numbers:** Jev classified all 100 in 1.42s; 31 fell below threshold; full
  pipeline reached 96/100 accuracy in 16s for ~$0.07 — $0.003 from Jev, $0.068
  from Kimi K3.
- **Evidence:** independent builder test with metrics (X, Sept 19); Jev's share
  effectively free, larger model's share dominant.
- **Why it ranks #59:** the cleanest measured instance of the confidence-gated
  cascade (#3) in the wild — Jev screens the volume for a third of a cent, the
  expensive model only sees the uncertain tail. The 95%-threshold routing shape
  generalizes to any high-stakes classifier.
- **Source:** via https://madewithjev.com/

### 73. Semantic code search for coding agents (siftr) (15, E4) — NEW
- **What:** semantic code search for coding agents, benchmarked on SWE-bench Lite:
  82%.
- **Evidence:** community project with a reported benchmark figure (via
  madewithjev.com, Sept 19); the 82% needs the author's own write-up to audit.
- **Why it ranks #73:** the first code-search use case with an independent benchmark
  attached — retrieval-by-judgment instead of retrieval-by-embedding, landing at
  82% on a real suite.
- **Source:** via https://madewithjev.com/

### 74. Viral-post scoring (SuperX) (15, E4) — NEW
- **What:** a live free tool that scores a post's virality with 61 Jev questions in
  ~1s for $0.0004 — fitted on 9,481 posts from 207 creators; claims it picks the
  viral post 2 in 3 times.
- **Evidence:** live builder tool with self-reported figures (via madewithjev.com,
  Sept 19); the 2-in-3 hit rate is a builder claim on fitted data, not an
  independent audit.
- **New (Sept 22 — midday):** Riley Brown built a live viral-post analyzer on Jev —
  0.5s after you stop typing, it analyzes the post's viral potential ("going to
  try and actually make this good, will need to scrape a lot of twitter data";
  via madewithjev.com). A real-time typing-pause scorer in the same shape as
  SuperX's 61-question run.

- **New (Sept 23 — morning):** Ian Nuttall (X, via madewithjev.com) gave Jev
  3,282 of his own X posts (100M views) and asked 8 questions per post
  (topic, hook, tone, teaches-something, …): 4,252,330 tokens, $0.1282, an
  8m34s run. Findings: how-to posts got 150 median likes vs 44 average; AI +
  coding was a 1.9× multiplier, SEO right at base median 1.0×. The
  retrospective content-analytics shape (Jev as the measurement instrument over
  your own corpus) complements the forward-looking scorers.
  (via https://madewithjev.com/)

- **Why it ranks #74:** a new use-case type — many-question fitted scoring as a
  consumer product — and the per-run economics ($0.0004, 1s) show why 61 questions
  is now a viable product shape.
- **Source:** via https://madewithjev.com/

### 75. Reddit AI-citation monitor (Lurk) (15, E4) — NEW
- **What:** lurk.so — find and monitor Reddit threads to get cited by AI; free
  product, 4,000 threads scanned, alerts via email/Discord/Slack; the builder says
  free is "only possible… bc of Jev".
- **Evidence:** live product with real volume (via madewithjev.com, Sept 19); no
  accuracy metrics published.
- **Why it ranks #75:** the first GEO (AI-search-visibility) use case — bulk thread
  triage at a price point that makes a free tier viable. Watch for accuracy
  reporting.
- **Source:** via https://madewithjev.com/

### 76. Job crawler with Jev discovery (jev-job-hunter) (14, E4) — NEW
- **What:** Kai rebuilt his job crawler with Jev: start at a company homepage, Jev
  identifies the Careers entry point, chooses which links to follow, recognizes job
  pages, and scores each role against his profile. LLM pipeline ~5 minutes → Jev
  just over 20 seconds in his test. Packaged as a skill.
- **Evidence:** independent builder test with a measured time delta (X, Sept 19);
  single-company test, no precision/recall on the matching itself.
- **Why it ranks #76:** the first job-discovery agent with a measured ~15× time
  delta — and the discovery shape (find entry → follow links → recognize pages →
  score) is a new variant of the browse-judgment loop (#45).
- **Source:** via https://madewithjev.com/

### 88. slopcheck — CI slop gate for PRs (tjklug) (14, E4) — NEW
- **What:** a CI tool that reads every pull request and flags AI slop — speculative
  abstractions, tests that only assert on mocks, swallowed errors, reinvented
  library code, narrating comments, scope creep. Code parses the diff, tags lines,
  and enumerates candidates by syntax; Jev answers several hundred questions per
  change in one request; verdicts: MERGE READY / POLISH THEN MERGE / NEEDS REWORK /
  NEEDS A HUMAN, with an exit code CI can gate on. Fails closed: a confident
  reading code can't place on a line is a named "coverage gap" status that sends
  the file to a human.
- **Numbers:** shipped fixture: 9,629 input tokens, 0.7s round trip; whole build
  (all debug runs, fixtures, CI): 25,781,308 tokens, 2,479 requests, **$0.91**;
  a 30-file 2,100-line change = 3 requests, ~$0.0055. It reviewed its own code and
  correctly flagged two hand-rolled helpers (reinvented wheels) at 0.84–0.88.
- **Evidence:** builder write-up with exact token/latency/spend figures (Sept 19);
  self-measured, one-repo fixture set — no precision/recall audit yet.
- **Why it ranks #88:** the most carefully engineered PR-review build yet — the
  whole-diff + per-line question decomposition, the coverage-gap fail-closed rule,
  and the "question was the bug" lesson (0.06 → 0.84/0.95 by rephrasing) are
  genuinely reusable design knowledge. Pairs with #83 (patdown) and #29.
- **Source:** https://tjklug.com/posts/typesafe-jev-slopcheck/

### 93. Brokerage-report direction prediction — real-data test (kijune lee) (14, E5) — NEW
- **What:** Jev tested against real brokerage reports — 210 cases collected over
  ten days, predicting price direction: one setup with supporting evidence
  (target prices, supply-demand figures), one with the evidence stripped.
- **Numbers:** with evidence, 100% correct direction on all 210; without
  evidence, Jev 58.3% vs a general model 53.0% vs a 55.6% guessing baseline;
  <$0.003 total run cost; ~35 tokens average output (answer + probability only);
  confidence >0.9 in 64.9% of calls — still deemed insufficient to skip human
  review; batching 5 questions cut input tokens 1,660 → 483.
- **Evidence:** independent test with metrics (Threads, Korean, Sept 19);
  author-measured.
- **Why it ranks #93:** the first real-data finance use case — and the verdict is
  a measured *limitation*: Jev doesn't beat a general model on hard unaided
  financial judgment, but delivers similar judgments cheaper and cleaner. Pairs
  with #6 and #70 as the catalogue's honest-falsifier cluster. Watch for a
  production research-triage build to confirm the cost-side value.
- **Source:** https://www.threads.com/@lucky_kisney/post/DddiwsfEd4H

### 94. Independent benchmark vs Qwen3-4B and Laya 400M (Paras Chopra) (14, E5) — NEW
- **What:** 15 evaluations (WikiRouter, intent routing, document classification,
  search top result, rule/evidence judgment, ordinal scoring, value extraction,
  relational choice, CLINC dynamic intent, DBpedia, BoolQ, CommonsenseQA,
  MMLU-Pro, SNLI, ARC-Challenge; n 100–400): "Ours" (Qwen3-4B) vs Laya English
  (400M) vs Jev 1.13.
- **Numbers:** Jev near-perfect on many (100% document classification,
  rule/evidence judgment, ordinal scoring) but **0.00% on relational choice** —
  the author reads this as evidence questions and options are processed in
  parallel, a structural artifact. From MMLU results + ~300ms latency the author
  suspects Jev is in the ~30B parameter range; calls it "mostly a standard
  modern model with specific fast-inference related tradeoffs."
- **Evidence:** independent benchmark with a published comparison table (Threads,
  Sept 19); author-measured, small n.
- **Why it ranks #94:** the only head-to-head of Jev against a *sub-1B open
  alternative* (Laya) plus a 4B baseline on the same tasks — and the 0% on
  relational choice is the cleanest structural falsifier published so far: a
  whole question class Jev's architecture can't answer.
- **Source:** https://www.threads.com/@paraschopra87/post/Ddd_yATGw5D

### 95. In-app micro-decision loop + bulk doc triage (Jesse Ayala) (15, E4) — NEW
- **What:** a builder's own app wired with Jev: 130 tiny decisions per
  conversation answered in fractions of a second, and a separate bulk run of
  275 company documents through Jev in 30 seconds for 11 cents. He advises
  starting in shadow mode, asking narrow questions, and treating confidence in
  buckets — and says plainly that reliability is measured but accuracy is
  "still being validated."
- **Numbers:** 130 decisions/conversation (sub-second); 275 docs / 30s / $0.11.
- **Evidence:** builder-described integration with self-reported metrics (Threads,
  Sept 20); the accuracy caveat is honest and self-reported.
- **Why it ranks #95:** two Jev shapes in one live app — the per-turn micro-
  decision loop (pairs with #9 Firstmate) and bulk doc triage (pairs with #2) —
  plus a practitioner note that corroborates the catalogue's deployment wisdom
  (#3, #68): shadow mode, narrow questions, confidence buckets.
- **Source:** https://www.threads.com/@mktg.jesse/post/Ddf-KpFFPLa

### 103. "Browser Dealer by K2S" — side-by-side browser race (15, E5) — NEW
- **What:** a side-by-side screen recording (Threads, @nomadius.cyou, Sept 21)
  of a Zürich→London flights booking task: a Jev-driven agent ("Jey TypeSafe -
  live") vs a frontier-LLM-paced agent, both completing the task.
- **Numbers:** Jev agent: 107 actions in 26s, ~213ms average latency, ~$0.0004
  total cost, reaching 100% progress early. Frontier LLM: ~2.3s latency, $0.458
  total — ~1,100× the cost, ~10× the per-action latency.
- **Evidence:** independent builder side-by-side test with metrics (Threads,
  Sept 21); single task, screen-recorded.
- **Why it ranks #103:** the third independent browser-agent measurement
  (#15, #57) — and the first to price a *completed booking flow* end to end: a
  full flights search at four hundredths of a cent.
- **Source:** https://www.threads.com/@nomadius.cyou/post/DdjsSfigjLt

### 104. Automated Jev-alternatives benchmark harness (Murat Aslan) (14, E4) — NEW
- **What:** an automated, continuously-running leaderboard benchmarking every
  Jev alternative against Jev 1.13.0: "Decisions/s vs macro accuracy", each
  bubble a candidate, with agents scraping X for new ones as they appear.
- **Numbers:** 24 candidates benchmarked, 40 more queued; Jev 1.13.0 baseline
  ~77% macro accuracy (dashed lines). Reported findings: Simplejev-Qwen3.8-27B
  the best open-source drop-in; Reflex-4b 2–3× faster at ~5 points less
  accuracy; Decider-2b ~10× faster, finishing the suite in four minutes; Laya
  dismissed despite GPU speed.
- **Evidence:** builder-described working harness with chart and findings
  (Threads, Sept 20); flagged as a lead in the Sept 20 evening scan when the
  post text was unreadable — now confirmed.
- **Why it ranks #104:** a meta-use-case — Jev's alternatives get evaluated by
  an automated agent loop — and the findings calibrate the whole
  open-decision-model space (#53, #94) against a single baseline: thin wrappers
  are real, but the baseline holds.
- **Source:** https://www.threads.com/@iammurataslann/post/DdhbczACGhd

- **New (Sept 23 — evening):** a second, fully open harness in the same family —
  4esv/jev-eval (see new entry #147) measures Jev 1.13.0 against open-jev,
  Kev-0.8B and Laya with reproducible code and an honest finding: the clones'
  in-sample wins evaporate out-of-sample. Consistent with #104's own
  "baseline holds" conclusion.

- **New (Sept 27 — morning):** a second-hand 78-case classifier-bench
  datapoint (via gadgetpilipinas.net) — hosted Jev 0.974 vs a ~1-bit
  compressed 27B model 0.885, GLiNER2 0.795, Von 0.769, Laya 0.590; Laya
  fastest at 30ms/case vs ~302ms for Jev. Single run, n=78, small sample —
  the Jev-vs-OSS accuracy ranking holds, the speed ranking inverts on tiny
  local models. Consistent with #104's conclusion.
  (https://www.gadgetpilipinas.net/2026/09/typesafe-jev-system-one-model-laya/)

### 108. Robot-fleet incident triage (RoboKrunch) (14, E4) — NEW
- **What:** 300 real Jev API calls against a simulated warehouse AMR fleet —
  bilingual CN/EN incidents, 3 simultaneous judgments per call (human
  escalation, owning team, urgency 0–2).
- **Numbers:** 300/300 succeeded; p50 0.53s, p95 0.81s;
  $0.0000246/decision ($24.57 per million). Fleet-scale projection (10K robots
  × 48 decisions/day × 30d): **$354/mo vs $1,814/mo for GPT-4o-mini — 5.1×**.
- **Evidence:** builder-measured with real API calls (GitHub, via
  robokrunch/awesome-jev, crawled Sept 21); incidents are simulated (template
  agreement 91.3%, not production accuracy) and the GPT-4o-mini cost is
  estimated, not measured.
- **Why it ranks #108:** the first industrial-robotics workload with real
  per-decision economics — a new vertical for the bulk-judgment family —
  and the sibling Jev-vs-ModernBERT result (see new note under #21) sharpens
  where Jev's edge actually is.
- **Source:** https://github.com/robokrunch/awesome-jev

### 112. Deterministic IVR agent (Jason Stiles / @stilesja) (14, E4) — NEW
- **What:** an interactive voice-response demo: Jev answers only small typed
  questions (intent, task, name, yes/no) while code owns the conversation flow —
  gatekeeper, form loop, corrections, queue, frustration handling, handoff.
  Two simulated calls to a fictional "Styles Family Medical Practice":
  a reschedule-then-billing caller and a frustrated repeat-corrector.
- **Numbers:** ~170ms a turn, a fraction of a cent per call (builder-measured,
  simulated calls).
- **Evidence:** builder demo with self-measured figures (Threads, Sept 22);
  calls are simulated, not live traffic.
- **Why it ranks #112:** the first *voice-call* use case — and the engineering
  framing is the point: failure traces split into probability errors (Jev's
  fault) vs code logic errors, making the pipeline "deterministic, testable,
  repeatable, and observable." The regulated-domain (healthcare) angle makes the
  confidence-gated handoff (#3) load-bearing.
- **Source:** https://www.threads.com/@stilesja/post/DdlPFiBDKn3

- **New (Sept 24 — morning):** the open-source repo — stilesja/jev-ivr — surfaced
  in the comments of a community project carousel; confirms the build is shipped
  as code, not just a demo post. Watch for live-traffic numbers.

### 115. Entity-resolution dedupe probe (Oracle Fusion duplicate customers) (14, E4) — NEW
- **What:** Shivkumar Iyer tested Jev on the classic ERP dedupe problem — duplicate
  customer records where name-matching tools merge wrong: pair 1 ("Acme
  Manufacturing Inc" vs "ACME MFG, INC.", same address) → merge; pair 2 (two
  "Globex Energy LLC", identical strings, different cities) → do NOT merge,
  address given as the reason; pair 3 (two clearly different companies) → leave alone.
- **Numbers:** all three pairs for ~$0.0001 total; "speed was unbelievable.
  Microseconds" (his words). Building a 50-pair hand-labelled set with nasty
  cases (same name, same city, actually different companies) before calling it useful.
- **Evidence:** independent builder test, hand-verified (LinkedIn, Sept 22); n=3 —
  the author explicitly says "three easy cases do not prove much."
- **Why it ranks #115:** the first ERP-data-quality use case — record linkage is a
  huge enterprise workload, and the pair-2 verdict (identical strings, different
  cities → no merge) shows Jev reading the *distinguishing* field rather than the
  name. The author's restraint (50 hand-labelled pairs before a verdict) is exactly
  the validation discipline this cluster needs. Watch the 50-pair results.
- **Source:** https://www.linkedin.com/posts/kumr192_jev-jev-typesafe-activity-7507258509382565888-vjTy

### 116. NL property search/classification over Zillow listings (Justine Moore) (14, E4) — NEW
- **What:** Jev as natural-language search over listings — scan thousands of Zillow
  listings and classify properties by things you can't filter for: architecture,
  renovation status, proximity to freeways.
- **Numbers:** <20 seconds, $0.18 total.
- **Evidence:** independent builder demo with cost/time figures (X @venturetwins,
  harvested by madewithjev.com Sept 22); "thousands of listings" is the
  builder's claim, the time/cost figures are stated.
- **Why it ranks #116:** a new real-estate vertical for the listing-triage family
  (#5, #62) — and the first *consumer-facing search* shape where Jev's
  judgments substitute for database fields that don't exist (no "architecture"
  column, no "freeway proximity" column). Pairs with #77 as NL-search-over-
  unstructured-inventory.
- **Source:** via https://madewithjev.com/

### 117. askgrep — semantic code grep (fajarhide) (14, E4) — NEW
- **What:** "grep for the questions you cannot write as a pattern": walks the
  codebase, splits functions into chunks, and asks Jev each plain-English
  question over every chunk (16 at a time, answers cached by hash so re-asks
  over unchanged files cost nothing). Demo: `askgrep "builds SQL from a request."
  demo/` → 2 hits, 11 chunks, 3,910 tokens, $0.0002.
- **Evidence:** working repo with demo GIF and cost figures (GitHub, Apache-2.0;
  crawled Sept 22); demo has a planted right answer (concatenated vs bound SQL).
- **Why it ranks #117:** the first semantic code-*search* tool — Jev judging every
  function instead of sampling a few is the zero-shot version of what the
  catalogue's ML-features entry (#43) and siftr (#73) do at higher cost. The
  hash-cache is the cost-control trick that makes "judge everything" the default
  on repeat runs.
- **Source:** https://github.com/fajarhide/askgrep

### 119. Ad × buyer-persona fit matrix (Matthew Berman / StealAds + Daniel Páez) (15, E4) — NEW
- **What:** Berman's Jev ad pipeline run as a creative-fit screen: each of 723
  live ads scored as stop-or-scroll against 30 buyer personas — which ads win,
  which personas convert, where hooks/formats/offers mismatch. Daniel Páez
  amplified with the full run stats (Spanish reel, Sept 22).
- **Numbers:** 723 ads × 30 personas = **21,690 stay-or-skip decisions in under
  40 seconds for $0.22** — the per-decision economics (~$0.00001) make
  exhaustive ad×persona matrices a commodity operation.
- **Evidence:** builder-run with self-reported on-screen figures (Berman's X,
  ~130K views; Páez reel Sept 22); shipping as a feature in StealAds + MCP —
  product integration, not a demo.
- **Why it ranks #119:** the same Jev pipeline as #17's ad analysis, now aimed
  at the advertiser's real question (which creative works on whom) rather than
  descriptive tagging — and 21,690 decisions for 22 cents is the cleanest
  single-figure demonstration yet of why exhaustive combinatorial judgment is
  suddenly free.
- **Sources:** via https://madewithjev.com/ ·
  https://www.instagram.com/reel/Ddm0WMOtNol/ (@thedanjourney)

### 120. Grok 4.7 + Jev follow-up-question decision loop (morgan055550) (14, E4) — NEW
- **What:** Grok 4.7 processes a prompt and Jev autonomously makes the decisions
  on the follow-up questions — the generative/decision division of labor with
  Grok as the thinker and Jev as the decider; a sibling build (maestrooth's
  "Jev and Grok Bot") packages the same pairing as a 5-minute-setup agent stack.
- **Numbers:** **20,472 decisions in 15.7 seconds for $0.41** (~1,300/sec) —
  the highest decision-throughput datapoint in the catalogue at sub-dollar cost.
- **Evidence:** demo with self-reported figures harvested via jevtracks.com's X
  crawl (Sept 22); second-hand card — the author's own post is not yet directly
  located, so treat numbers as builder-claimed.
- **Why it ranks #120:** the first measured Grok+Jev pairing — and the shape is
  the general one (#15/#19/#116): frontier LLM generates, Jev decides, code
  acts. Throughput near 1.3K decisions/sec suggests the parallel-question
  shape generalizes further than launch-week demos showed.
- **Source:** via https://jevtracks.com/

### 125. "Shapeshift" — NL input that morphs into UI (anishfn / @kraayenjon) (15, E4) — NEW
- **What:** one text box that becomes the right UI as you type: "dinner with priya
  friday 8pm" → event card; "buy milk, eggs, bread and coffee" → checklist;
  "25 min focus" → focus timer; "split 2400 between 3" → ₹800 each; "3pm pst in
  ist" → timezone conversion; "remind me to pay rent tomorrow urgent" → urgent
  reminder. Offline-by-default, open-source.
- **Numbers:** none published — demo-described.
- **Evidence:** builder demo with two independent sightings: the Threads post
  (@kraayenjon, Sept 23) and the GitHub repo anishfn/shapeshift (239 stars,
  28 forks, via jevtracks); described, not measured.
- **Why it ranks #125:** the first Jev-native *intent→UI-resolution* product —
  Jev maps a free-text phrase to the right structured card instead of an LLM
  generating prose. Same builder ships "Jaste," a Jev-powered copy/paste
  accelerator (beta) — the personal-input-dispatcher family.
- **Sources:** https://www.threads.com/@kraayenjon/post/Ddn2zi9gAmu ·
  https://jevtracks.com/build/j973qd3cqftmkbzm88myy7v1qs8ezbpf

- **New (Sept 23 — midday):** a sibling — joevidev/ui-generator-instinct-jev
  ("Instinct", live at ui-generator-instinct-jev.vercel.app): describe a case
  in free text and Jev picks the UI from a fixed catalog without generating a
  line of code or copy (via gitechx/awesome-jev). Same intent→UI-resolution
  shape as Shapeshift, web-app edition.

### 126. AI-slop detectors — pair of sightings (15, E4) — NEW
- **What:** two independent builds flagging AI-generated content with Jev:
  (1) Akash Ingole's "Jev Slop Flag" Chrome extension — evaluates LinkedIn posts
  in real time while scrolling: "LIKELY AI SLOP — 98%" vs "NOT FLAGGED — 37%",
  Jev API + Cloudflare hosting, free to set up; (2) Jon Kraayenbrink's free web
  tool — paste any URL, Jev checks 35 tells of AI slop (purple gradients, emoji
  headers, "seamlessly," fake testimonials, bento grids) in 243ms for $0.00015.
- **Numbers:** per-check: 243ms, $0.00015; no corpus accuracy figures published.
- **Evidence:** builder demos with self-reported per-check figures (IG reel
  @\_aijuice, Sept 22; madewithjev.com free tool card) — second sighting
  corroborates the shape, not the accuracy.
- **Why it ranks #126:** authenticity-detection is a new use-case family for the
  catalogue — and the economics are the story: 35-question fitted scoring at
  $0.00015 makes per-item content-forensics near-free. The accuracy question is
  open; pair with #6's lesson that false-positive rates must be measured.
- **Sources:** https://www.instagram.com/reel/Ddl5PR1yNgg/ ·
  via https://madewithjev.com/

### 127. Replay events → fix PRs pipeline (ai_xiaomu, X) (15, E4) — NEW
- **What:** Jev analyzed 3 million session-replay events to identify rage clicks
  and JavaScript errors, then 213 fix pull requests were submitted in 40 seconds
  at a cost of $2.
- **Numbers:** 3M events → 213 PRs, 40s, $2 (builder-claimed, second-hand).
- **Evidence:** builder claim paraphrased via jevtracks.com's X crawl (Sept 23);
  the author's own post is not yet directly located — treat numbers as
  builder-claimed; whether Jev alone authored the PRs (vs flagged issues a
  generator fixed) is unverified.
- **Why it ranks #127:** the first replay-analytics use case — product-behavior
  events as Jev state, issue-triage as typed questions, fix-PR generation as
  downstream code/LLM. If the numbers hold, the funnel math is the catalogue's
  best: ~$0.0094 per auto-filed issue-PR.
- **Source:** via https://jevtracks.com/

### 128. High-risk financial-action authorization demo (fluixoo, X) (14, E4) — NEW
- **What:** an AI agent instructed to transfer $50,000 to an unverified wallet and
  delete transaction logs — the system flagged both actions as high-risk and
  required human review before proceeding.
- **Numbers:** none — demo-described.
- **Evidence:** builder demo paraphrased via jevtracks.com's X crawl (Sept 23);
  second-hand, builder-claimed.
- **Why it ranks #128:** the first financial-*authorization* datapoint — Jev as
  the policy tripwire on irreversible actions (cf. #23's misuse detection, #25's
  per-hazard firewall). The high-value/low-volume slot is where a cheap veto
  has the best cost-benefit math.
- **Source:** via https://jevtracks.com/

### 130. WeChat intent-recognition floating window (jev-chat/jev-chat-jarvis-mac) (14, E4) — NEW
- **What:** a macOS floating window over WeChat: reads the screen, a local small
  model judges intent and risk, generates reply candidates by script — pure
  read-only, no injection into WeChat.
- **Numbers:** none — shipped tool; 293 stars, 96 forks on GitHub.
- **Evidence:** shipped open-source tool (GitHub, via jevtracks Sept 23); stars
  show real adoption, no accuracy/latency figures yet.
- **Why it ranks #130:** the first messaging-app intent layer — Jev-style
  typed judgments over someone else's app surface, read-only so the blast
  radius is bounded. The safety design (read, never inject) is the pattern
  worth cloning for any closed-app integration.
- **Source:** https://jevtracks.com/build/j97f06ddzt1ptre8yh9gc237qh8eys3t

- **New (Sept 23 — midday):** the Windows counterpart — jev-chat/jev-chat-windows
  (Python, **432★, 97 forks** on GitHub): window screenshots + local offline OCR
  read the other party's WeChat messages → Jev judges intent → 3 candidate
  replies, one-click fill, sending always manual. The star count is the strongest
  adoption signal of any messaging-app build so far. (via jevtracks.com)

### 135. DocJev — NL-rule document classification + packet splitting (Jerry Liu / LlamaIndex) (14, E4) — NEW
- **What:** LlamaIndex's open-source library that classifies a document against
  natural-language category rules or finds the boundaries between sub-documents,
  with swappable OCR backends (liteparse or LlamaParse) — "classify and split
  complex document packets."
- **Numbers:** 40-document pilot: 40/40 originals classified correctly at ~182ms
  Jev decision p50; "6× vs gpt-5.6-luna" on speed; the default is fully free
  and open-source.
- **Evidence:** working library by LlamaIndex co-founder Jerry Liu, self-measured
  pilot figures (via yibie/awesome-jev + madewithjev.com, Sept 23).
- **Why it ranks #135:** the first doc-classification build with a measured
  latency figure against a named LLM baseline from a known builder — and the
  sub-document boundary detection is a new shape distinct from whole-document
  classification (#2). Watch for the promised benchmark harness results.
- **Sources:** via https://github.com/yibie/awesome-jev ·
  via https://madewithjev.com/

- **New (Sept 24 — evening):** a second LlamaIndex integration — WiktorB2004/
  llama-index-jev ships Jev as a `JevRerank` postprocessor *and* as
  `JevSingleSelector`/`JevMultiSelector` tool selectors (MIT). Unit tests run
  mocked (no live API key); a benchmark script targets nfcorpus/scifact via a
  MiniLM top-10 with Jev scoring, but no published numbers yet — a
  framework-integration datapoint, watch for the benchmark run.
  Source: https://github.com/WiktorB2004/llama-index-jev

### 157. YouTube sponsor skipper (valentynkit/jev-skip) (14, E4) — NEW
- **What:** a Chrome/Firefox extension that reads the caption track of the video
  you're already watching and asks Jev one question per 30-second segment in a
  single request (`content, sponsor, intro, outro, self_promo, recap, other`) —
  painting a probability-tinted heatmap on the seek bar and skipping confident
  sponsors. No crowd database, no SponsorBlock wait: works on videos nobody has
  ever labeled. MIT, fixtures + `npm run measure` + threshold sweeps in-repo.
- **Numbers:** **77% of SponsorBlock's crowd-marked sponsor seconds caught**
  across 23 videos, 34s of false skips per hour, **$0.0008 per video**;
  answers ~0.9s after request, slices painted ~2.9s after navigation (median
  over cold loads). Demo run: 50 segments, 25.3k tokens, $0.0011, 1,546ms.
- **Evidence:** working extension described by builder with fixture-backed
  metrics and honestly reported limits (GitHub, Sept 18–19); numbers came
  through a Vercel AI Gateway shim, not the direct API — flagged by the
  author himself.
- **Why it ranks #157:** the consumer-shape of the classifier cluster — a real
  browser extension with a genuine baseline (SponsorBlock's crowd labels),
  real cost-per-video, and the probability-tinting that turns calibration
  into UI. The "works where the crowd hasn't" gap is the business case.
- **Source:** https://github.com/valentynkit/jev-skip

### 158. Design-system component router (Sho Villalba / @shovillalba) (14, E4) — NEW
- **What:** Jev as the router for design system "Sho": answers closed questions
  (component family, props, page width) *before* an agent writes the interface —
  workflow Brief → Jev pregunta → Props → Decision, with a decision map plotting
  confidence vs fit. Compared head-to-head with Claude Opus 5.5 on the same task.
- **Numbers:** 28 briefs run live (Sept 23): **19/20 main components correct
  within Sho, 5/8 outside Sho**; median latency 2.4s per brief; vs Claude
  Opus 5.5: **10.2× faster, 156× cheaper** (creator-measured).
- **Evidence:** builder-measured independent test with metrics (IG reel,
  Sept 23, Spanish); small n, creator-run comparison.
- **Why it ranks #158:** the first *measured* design-system router — the
  UI-generation pre-decision slot. The honest out-of-system number (5/8
  outside Sho) is the distribution-shift bound every routing deployment must
  expect; inside the distribution it's near-perfect. Pairs with #9/#12 (the
  agent-loop dispatch/router family).
- **Source:** https://www.instagram.com/reel/DdpBslcO--M/

### 160. CLASH contradiction-detection benchmark adapted to Jev (AIPI-mvoronovych) (14, E4) — NEW
- **What:** the multiple-choice protocol of CLASH (a benchmark for cross-modal
  contradiction detection, CVPR 2026 Findings) adapted to Jev's text-only
  interface: the COCO image is replaced by its annotated caption, and Jev gets
  the original caption + a conflicting caption + question as state, answering a
  4-way `Choice` (image-grounded / text-grounded / distractor / "conflicting
  information — cannot answer") plus a spontaneous-detection `Noul` per request.
  All 1,289 human-verified test samples, per-sample option shuffling, and
  1,000-resample bootstrap SDs — the same rigor as the paper.
- **Numbers:** the repo ships `results/REPORT.md`, `results/summary.json`, and
  raw per-sample predictions including probability distributions (~1,300 Jev
  requests at ~1¢ total) — headline figures not yet surfaced in this scan;
  baselines re-scored from the CLASH authors' released GPT-5 / GPT-4.1 Mini /
  Gemini 2.5 predictions.
- **Evidence:** working benchmark harness with published results in-repo
  (GitHub, created Sept 17); headline accuracy not yet extracted — treat as a
  lead until read.
- **Why it ranks #160:** the first contradiction-*detection* measurement
  against a real academic benchmark — and the `Choice` includes the one option
  that makes contradiction detection honest ("conflicting information — cannot
  answer"), which probes exactly the failure mode catalogue entries #4/#118
  describe as Jev's strength (typed abstention). Pairs with #54 (citation
  grounding) as the claim-verification family. Watch the headline numbers.
- **Source:** https://github.com/AIPI-mvoronovych/JEVBenchmark-Contradiction-Detection

### 161. "Probably" — Jev as a programming-language keyword (Steve Faulkner, Cloudflare) (15, E4) — NEW
- **What:** Steve Faulkner (@southpolesteve, Cloudflare) built **Probably**, a
  toy programming language with Jev baked in as an actual keyword: `feels` asks
  a yes/no question with a confidence threshold, `match` routes between 2–8
  labeled branches, `while ... feels` loops until a judgment flips. His
  description: "Jev makes the decisions, an LLM does the writing, and a little
  program ties it together." 328K views on the announcement post.
- **Evidence:** working demo by a named builder, reported via zeke/jev's research
  notes (Sept 24); the original X post is linked in the notes' sources.
- **Why it ranks #161:** the first *language-level* packaging of typed decisions
  — Jev as a control-flow primitive rather than an API call. It's the extreme
  form of the catalogue's "code decides" philosophy (#3, #68): probability
  thresholds become loop conditions. Pairs with #4 (the MCP judgment
  primitives) as the composability frontier.
- **Source:** via https://github.com/zeke/jev · original post:
  https://x.com/southpolesteve/status/2100767781868150938

### 165. Agent-routing internal test — Jev vs GPT-5.6 Luna (TrueHorizon.ai) (14, E4) — NEW
- **What:** an internal engineering test (Sept 21–22) comparing Jev against
  GPT-5.6 Luna for agent routing decisions, published as an 8-slide carousel
  with the full numbers.
- **Numbers:** speed — Jev 6.5× faster (404ms median vs 2,633ms); cost —
  $0.0031 vs $0.0309 (~10× cheaper). Accuracy — on 144 development cases Jev
  99.3% vs Luna 85.4%; on 93 held-out unseen cases Luna 96.8% vs Jev 93.5%
  — the held-out gap is real. Robustness — a 639-call study had 474 valid
  calls and **1 invalid route answer**; probability calibration unverified.
  Honest engineering notes: a prompt defect (recency bias favoring the most
  recent agent) was fixed to follow the relevant exchange (96/96 reply checks
  on reused cases); one remaining failure where Jev correctly flagged a
  repeat request but TrueHorizon's own policy ignored the signal.
- **Evidence:** builder-run internal test with measured latency/cost/accuracy
  figures and honest negatives (Instagram carousel, Sept 24); self-reported,
  not independently audited.
- **Why it ranks #165:** the first *engineering-grade* internal test to openly
  undercut the vendor multipliers (6.5×, not 40–200×) and show Jev *losing*
  on held-out accuracy to a small LLM — while still being worth it at 10×
  lower cost. The dev-vs-held-out inversion is the deployment warning every
  router builder needs; the invalid-answer count is the metric most demos
  never report. Pairs with #9/#12 (dispatch patterns) and #59 (Jev-first
  cascade) as routing's third pillar.
- **Source:** https://www.instagram.com/p/DdsNVzZGDPV/

### 179. Agent supervision layer with completion verification (keeltrace/hermes-jev, "Nerve") (14, E4) — NEW
- **What:** an asynchronous System-1 supervisory layer for Hermes agents —
  typed decisions, ranking, verification, token-aware oversight, and an
  opt-in tool gate, powered by TypeSafe Jev (or OpenJev/Laya local sidecar).
  Design invariant: deterministic verifiers prove mechanical completion
  criteria locally; Jev is used only for unresolved semantic judgment; a
  healthy run spends zero Jev tokens. Design motto: "Jev must earn every
  token it spends."
- **Numbers:** live path validated Sept 17 (Jev via OpenRouter): 4 verified
  live calls, 2,264 input + 281 output tokens, ~$0.000095 total, ~419ms avg
  provider latency; context-governor policy stays shadow-first before
  auto-apply. Healthy-run benchmark target: ≤2% fixed token overhead vs
  plain Hermes (0.5% stretch).
- **Evidence:** shipped plugin (v0.2.3, 90 commits, 25 stars, MIT) with
  live-test notes and releases; Jev-as-semantic-overflow, not sole
  authority. Builder-measured, not independently replicated.
- **Why it ranks #179:** the most architecturally explicit Jev-as-supervisor
  yet — deterministic frontier first, Jev overflow only — matching the
  catalogue's recommended deployment shape (#68, #3). Pairs with #174
  (jev-harness) as agent-governance, and #134/#25 as the gate cluster.
- **Source:** https://github.com/keeltrace/hermes-jev

### 170. jev-agent-skill — judgment offload for coding agents (yuyang2230) (15, E4) — NEW
- **What:** a Claude Code / ZCode skill that offloads small judgments
  (classify/route, batch screening, scoring, compliance pre-checks) from the
  main model to Jev on OpenCode Zen's free `/v1/systemone` endpoint — with a
  zero-dependency `jev.py` caller (retries for the gateway's transient 500s,
  WAF-safe User-Agent, GBK-pipe-safe stdin) and SKILL.md auto-trigger rules
  so agents invoke it unprompted. Ships with a production e-commerce
  comment-triage case study; adapted from the official typesafe-ai/skills
  SKILL.md (MIT).
- **Numbers:** the comment-triage case study is described but not yet quoted
  with metrics — a lead to verify.
- **Evidence:** working integration described by builder, via the
  AbdelStark/awesome-typesafe index (Sept 24); case-study metrics not yet
  extracted.
- **Why it ranks #170:** the "verify everything" pattern (#4) as an *agent
  skill* — Jev as the cheap judgment layer inside the coding agent itself,
  not just the software it writes. The auto-trigger SKILL.md rules are the
  distribution mechanism: every judgment an agent delegates to Jev is one
  expensive reasoning call saved. Pairs with #111 (context compaction) and
  #135 as the agent-self-tooling cluster.
- **Source:** https://github.com/yuyang2230/jev-agent-skill

### 180. Jev picks the lookups — support agent split (mani-aiml/jev-demo) (14, E5) — NEW
- **What:** a support agent where Jev answers "which record to look up next"
  (typed decisions), a deterministic harness executes the lookups, and Claude
  Sonnet 5 writes the answer once — with no tools and no schemas. The builder
  frames it as "three of every four calls my support agent made were not
  writing anything — they were deciding."
- **Numbers:** same tasks, same answers as the all-Claude baseline: **1.7×
  faster and 5.8× cheaper** on the builder's own catalog; 48 runs (8 tasks × 3
  repeats, medians over three). Honest caveat: the first run failed at 50%,
  fixed by one phrase in the Jev question; all data synthetic, "made up for
  this demo."
- **Evidence:** builder-measured side-by-side with the two agents, tests, and
  notebook published (YouTube + GitHub, Sept 22); self-measured on synthetic
  data, but the methodology (repeats, medians, failure reported) is the
  catalogue's bar.
- **Why it ranks #180:** the "split write and decide" agent architecture
  measured end to end — Jev absorbs the non-writing majority of agent calls,
  the generative model only writes. Pairs with #8's computer-use caveat as
  the honest version of the hybrid story: the win is proportional to how
  many calls were never really writing.
- **Source:** https://www.youtube.com/watch?v=lhamDIMXfRc ·
  code: https://github.com/mani-aiml/jev-demo

### 187. Market-research corpus scoring (Julia Joung) (15, E4) — NEW
- **What:** a strategy/marketing practitioner with early Jev access ran a
  creator-sentiment case study on Roblox's AI creation tools: Jev read the full
  set of public Roblox DevForum posts (2023–2026) discussing the tooling, and
  scored every post against a ten-question strategic rubric she built with
  Claude — making 2023 posts directly comparable with 2026 ones. She fed Jev's
  structured outputs to Claude for synthesis and shaped the strategy herself,
  publishing an interactive sentiment dashboard.
- **Numbers:** total Jev run ≈ **$0.02**. Her framing: work that "once required
  weeks-long research engagements and tens of thousands of dollars."
- **Evidence:** LinkedIn article (Sept 24) with the exact cost figure and a
  linked interactive dashboard; builder-described, not independently verified.
- **Why it ranks #187:** the first non-developer vertical in the catalogue —
  market-research/GTM corpus scoring by a traditionally nontechnical
  professional. The workflow matters: Jev classifies and scores at scale, the
  LLM synthesizes, the human strategizes. Generalizes to any "far too much
  data, one messy question" research brief.
- **Source:** https://www.linkedin.com/pulse/my-two-cents-future-market-research-typesafe-ais-jev-julia-joung-fqrwc

### 188. Email filter shootout + Jev-first cascade (Wayne Lian / @waynestudio) (15, E5) — NEW
- **What:** an independent builder tested Jev against his current Claude Haiku
  email filter on 317 synthetic emails, with four pass/fail gates — then
  chained them: Jev first, Haiku only on the uncertain tail.
- **Numbers:** passed 3/4 gates — recall within 3pts of Haiku, calibrated
  probabilities, and the combo beats Haiku alone. **Jev alone 96.8%,
  Jev→Haiku cascade 98.1%, Haiku alone 88.0%.** Consistency: re-running the
  same 100 emails three times, Haiku flipped on **27**, Jev on **1**. Failed
  gate: speed — a 20-email batch took **1.92s** vs the 1.5s target.
- **Evidence:** Instagram reel (Sept 26), independent measured test; synthetic
  emails, one documented speed miss.
- **Why it ranks #188:** a second independent replication of the Jev-first
  cascade shape (#159) — and the first consistency measurement: Jev's
  near-determinism (1 flip vs 27) is the property that makes it a safe
  *filter*, not just an accurate one. The failed speed gate keeps the
  deployment honest.
- **Source:** https://www.instagram.com/reel/DdwX0K2pSN2/

### 189. LLM→Jev chatbot gate deflection audit (Rama Digital) (15, E4) — NEW
- **What:** an Indonesian agency's chatbot answers routine questions from the
  knowledge base; only uncertain LLM-drafted replies go through Jev for
  verification/routing — a Jev-as-gate deployment, audited Sept 20 and posted
  as a carousel.
- **Numbers:** **1,100 questions handled with 287 Jev calls costing $0.0094** —
  a 74% deflection rate.
- **Evidence:** Threads carousel (Sept 26), builder-described audit; the
  comment thread explicitly flags the missing pieces — no false-negative data
  and incomplete cost accounting, so treat $0.0094 as Jev-only cost, not
  all-in. Honest enough to read, not enough to price a rollout on.
- **Why it ranks #189:** the LLM-gating deflection pattern (#95's family)
  measured on a real chatbot: the LLM does the talking, Jev only enters on the
  uncertain tail. The 74%-at-sub-cent shape is the unit economics that make
  gate deployments win — with the false-negative caveat as the standing
  follow-up.
- **Source:** https://www.threads.com/@ramadigital.id/post/DdwNCdZiRDh

### 190. Novel Cognition "fallsover" — critical calibration audit (14, E5) — NEW
- **What:** the author of the "Jev File" published a follow-up critical
  analysis of TypeSafe's launch claims (fallsover.novcog.us.com, Sept 26), and
  it carries two genuinely new datapoints: (1) a **TypeSafe employee's own
  measurement** on a real pipeline — one decision step routed through Jev via
  the company's launch-day DSPy fork — gave **15.9% faster end-to-end, cost
  per ticket −30.1%**; (2) a pre-registered out-of-distribution evaluation —
  **30 out-of-scope messages: flagged none, at 0.99 confidence**; on an
  unsolvable task 44.7% correct at average reported probability 0.74; OOD ECE
  0.107, ~4.4× the noise floor. Plus the definitional correction: the launch's
  "0% hallucination rate" is a schema guarantee, not a measurement.
- **Evidence:** independent critical analysis with measured datapoints; the OOD
  evaluation is cited second-hand (dated Sept 20, author unnamed in the
  excerpt).
- **Why it ranks #190:** not a use case — a calibration anchor for the whole
  catalogue. The 15.9% pipeline figure vs the 193.6× vendor headline is the
  difference between a single-call benchmark and a system; the OOD
  forced-choice overconfidence finding is the sharpest limit on
  routing-by-confidence yet published. Read it before trusting any cutoff.
- **Sources:** https://jev.novcog.us.com · https://fallsover.novcog.us.com ·
  https://dailycaliforniapress.com/jev-typesafe-benchmark-checked-explainer-wave-daily_california/

### 193. OpenRouter × TypeSafe "Jev Router" (typesafe/jev-router) (15, E2) — NEW
- **What:** TypeSafe and OpenRouter launched a Jev-powered router endpoint
  (listed Sept 25): `typesafe/jev-router` dynamically picks the model plus
  reasoning effort for each request — "cache-aware" routing that balances
  quality, speed, and cost across the OpenRouter catalogue. Priced free; the
  listing claims a million-token context.
- **Numbers:** none public — the OpenRouter page shows no usage data or
  routing results yet. Second-hand via news coverage: developers reportedly
  seeing near-100% accuracy on large-scale real-sample scoring, others
  reporting rising latency as usage surges — both unverified.
- **Evidence:** product-launch announcement (Sept 25–26): PANews, Phemex, and
  RuntimeWire on the listing and TypeSafe's X post. No independent
  measurement.
- **Why it ranks #193:** Jev's decision thesis becomes a platform product —
  the meta-router sits above the whole LLM market, the biggest infrastructure
  bet yet after #72. Until routing telemetry is public, this is a credible
  announcement, not evidence: it ranks high on leverage and generality, zero
  on measured proof. Pairs with #12/#186 (community model routers).
- **Sources:** https://runtimewire.com/article/typesafe-jev-router-openrouter-launch ·
  https://panews.io/articles/01a0db2d-ccc3-7014-9538-7c1854b0ef9f ·
  https://phemex.com/news/article/typesafe-ai-and-openrouter-launch-jev-smart-router-for-llm-optimization-97906

### 194. Jev as task router — kunko-ai-labs judge-audit (15, E5) — NEW
- **What:** a "REAL VENDOR AUDIT" of Jev as a route_easy vs route_strong task
  router, run via Vercel AI Gateway (Sept 19): n=120 decisions over 61
  distinct task texts, raw responses committed, options sent as labeled
  descriptions.
- **Numbers:** routing accuracy **97.5%** vs a 66.7% majority baseline; ECE
  **0.053**; wrong-decision confidence median 0.59 vs 0.93 on correct ones;
  an adversarial cost-inflation attack (rows trying to force route_strong)
  succeeded **0.0%** of the time. Cost savings use assumed prices (easy
  $0.002, strong $0.05) — dollars are illustrative, not measured.
- **Evidence:** independent measured audit with committed raw data (GitHub,
  Sept 2026); small n, 61 distinct texts.
- **Why it ranks #194:** the most adversarially-probed router audit in the
  routing cluster — confidence on wrong decisions collapses to ~0.59, which
  is exactly the signal a confidence gate needs, and the 0% cost-inflation
  success is the first measured resistance to routing manipulation. Pairs
  with #186 (Fez), #165 (TrueHorizon), and #190 (the OOD caution).
- **Source:** https://github.com/kunko-ai-labs/judge-audit/blob/HEAD/docs/audit-jev-router-described.md

### 195. VentureBeat prompt-injection security analysis (15, E4) — NEW
- **What:** VentureBeat's security desk covers Jev's move into agent decision
  layers, built on the vendor's own limitations page ("content written to
  adversarially steer the model… can move the answer") and Pydantic's Jev
  docs ("Jev treats the state as data, not as hostile"; option order can move
  the answer). New datapoint: an Octomind engineer's test — Jev's block
  probability on `rm -rf ~/.ssh` fell from **0.76 (conf 0.64) to 0.48 (conf
  0.22)** after a planted tool-output field claimed user pre-approval.
  LangChain's Jev middleware already excludes tool output from the classifier
  input "so content the agent fetched cannot authorize its own execution."
- **Numbers:** one command, one demonstration — not a benchmark; the value is
  the documented, vendor-acknowledged failure mode.
- **Evidence:** independent reporting citing the vendor's own statements plus
  a published third-party demo (Sept ~21).
- **Why it ranks #195:** the first mainstream-venue documentation that a
  Jev guardrail is injection-steerable — it pairs #6's 41% false-positive
  finding with the red-team angle and gives the catalogue a citable
  deployment rule: exclude tool output from the state, pair with deterministic
  checks and a human checkpoint for consequential actions. Watch for
  LangChain middleware behavior in the wild.
- **Source:** https://venturebeat.com/security/companies-are-putting-jev-in-charge-of-ai-agent-decisions-and-prompt-injection-can-influence-the-verdict

### 198. Podcast ad-detection benchmark vs 84 chat LLMs (ttlequals0/minuspodjev) (15, E5) — NEW
- **What:** an independent, reproduction-grade benchmark of Jev vs 84 chat
  LLMs on the MinusPod ad-detection corpus (the author's own app): one
  boolean "is this line advertising?" Noul judgment per audio segment, spans
  assembled from probabilities. Run Sept 19; every analysis passed a harness
  self-check reproducing the published rows before new results were accepted.
- **Numbers:** Jev F1 0.9294 / F0.5 0.9572 at IoU 0.5 (precision 0.9792,
  recall 0.8931) — but the lead is an IoU artifact: it dies between IoU 0.76
  and 0.78, and at IoU 0.8 four chat models beat Jev (cross-validated margin
  over Haiku +0.006 F0.5, paired t(11)=0.131 — not significant). **36.6×
  cheaper** per episode ($0.0021 vs $0.0773) and **~59× faster** (410ms p50
  vs 24.2s). Jev never cut audio containing no advertising (**0.0 s/h
  spurious across 17.5 hours** — no chat model matched that) but removed ~3×
  more editorial content than Haiku (~26s segment quantization makes every
  boundary error cost a whole segment). The 2×2 finding: running Haiku
  through Jev's per-segment decomposition beats both direct emitters
  (F0.5 0.826 vs 0.760 vs 0.722 at IoU 0.8) — "the decomposition is the
  strong part; the judge is what to replace." Jev also posted the lowest
  trial variance measured (0.0242).
- **Evidence:** independent measured benchmark with fully reproduced results
  and raw artifacts (GitHub, Sept 19).
- **Why it ranks #198:** the first media-pipeline use case with real
  audio-damage accounting — the 0.0 s/h spurious figure plus the
  quantization-boundary finding reframe the cost win as a
  cost-and-latency-for-quality trade, not a free win — and the 2×2
  decomposition result is the catalogue's third independent confirmation of
  the decomposition rule (#25, #68, #94). Pairs with #134's moderation
  economics as the second per-segment pipeline.
- **Source:** https://github.com/ttlequals0/minuspodjev/blob/HEAD/JEV_BENCHMARK_REPORT.md

### 207. Jev-vs-judge head-to-head on code-review statements (bryansparks/armature) (14, E4) — NEW
- **What:** the Armature harness's `decision-typesafe` example pits Jev against
  a small LLM judge (qwen3.6-27b) on the same 20 hand-labeled code-review
  statements, each scored on three questions — `is_concrete` (Noul),
  `severity` (low/medium/high), `actionability` (0–2 ordinal) — with the
  verdict compared against hand labels.
- **Numbers:** measured 2026-09-21 (20 states): is_concrete **1.00 vs 1.00**;
  severity 0.80 vs 0.95; actionability 0.80 vs 1.00; overall **0.87 vs 0.98**;
  avg latency/call **260ms vs 20,591ms (79×)**; input tokens 12,160 vs
  57,841 (+13,938 output); metered cost **$0.0005 for the 20 calls**;
  decision-vs-judge agreement 0.88. The author is explicit that misses cluster
  exactly at decision boundaries (split probability mass — calibrated
  uncertainty, not confusion), that run-to-run variance at n=20 is ±1–2 items,
  and documents one *confident* miss (severity called high at 0.96, labeled
  medium).
- **Evidence:** builder-run head-to-head with honest small-n variance notes
  (GitHub, run e97b81dd432b); 20 hand-labeled items, one run — the accuracy gap
  (0.87 vs 0.98) sits inside noise-adjacent territory at this n, but the
  latency gap (79×) does not.
- **Why it ranks #207:** the only Jev-vs-small-LLM *code-review judgment*
  comparison with both accuracy and latency on the table — and its honesty
  (boundary-clustered misses, ±1–2 item noise) is the point: Jev is level on
  factual either/or questions (is_concrete 20/20) and trails on graded,
  boundary-heavy ones, exactly where the catalogue's decomposition rule says
  fuzzier questions belong in code, not the model. Pairs with #191 (gemanor's
  three-model code-review benchmark).
- **Source:** https://github.com/bryansparks/armature/blob/HEAD/examples/decision-typesafe/README.md

### 210. Bank-transaction categorization skill (Keeran Jagadesan / jev-bookkeeper) (15, E4) — NEW
- **What:** a Claude skill that categorizes bank transactions — Jev categorizes each transaction, low-confidence rows are flagged for human review instead of auto-filed. The builder frames it as a test of a viral claim (attributed in the reel to X user Andy O, not independently verified) that Jev did "34 months of bookkeeping ($20,000 value) in 30 seconds for $0.33."
- **Numbers:** builder's on-screen terminal run — **216 transactions in 1.7 seconds for $0.0056**, 35 transactions flagged for human review (~16% exception queue). No accuracy figures published.
- **Evidence:** builder-described demo with on-screen terminal metrics (Instagram reel, Sept 28); the GitHub repo (kjagsadvisors/jev-bookkeeper) is builder-claimed in the reel and was not independently verified this scan; the viral $20k/30s claim is unattributed — treat as hype until sourced.
- **Why it ranks #210:** a new fintech vertical entry (pairs with #136 expense-report categorization and #209's deeper invoice-entry run) — and the ~16% exception queue is the confidence-gated routing pattern (#3) showing up in a consumer-finance shape. Builder-claimed numbers, accuracy unmeasured; stays Tier 2 until an independent run exists.
- **Source:** https://www.instagram.com/reel/Dd1NBlXMViL/

### 211. Agent record-lookup decisions offloaded to Jev (Mani Khanuja / The Agentic Enterprise) (15, E5) — NEW
- **What:** an efficiency experiment on a live agent loop — three of every four
  calls her agent made to Claude weren't for writing, but for deciding *which
  record to look up next*. She moved the fetch decisions to Jev (selects from
  options, returns probability), leaving Claude to write only once at the end.
- **Numbers:** same task, same answers — time **71s → 42s** (1.7× faster), cost
  **9.6¢ → 1.6¢** (5.8× cheaper), input tokens per task **~4,200 → ~350**.
  Small experiment; the full build is linked from the reel.
- **Evidence:** independent builder test with measured before/after figures
  (Instagram reel, Sept 28); small experiment, author-measured.
- **Why it ranks #211:** the decision-vs-writing split inside a real agent loop,
  measured — and the advice it ships with ("audit your own agents for calls that
  are really decisions") is the decomposition pattern (#9 Firstmate's dispatch
  shape) as a practitioner's habit. Pairs with #173 (skill routing) and #209
  (System 1/System 2 bookkeeping) as the agent-economics cluster.
- **Source:** https://www.instagram.com/reel/Dd1wS3HFTZk/

### 212. Blog content audit — 117k words, 240 scores, 12 seconds (Wayne Ergle / StackEngine) (15, E4) — NEW
- **What:** Jev pointed at 20 ClickUp blog articles (117,000+ words): 12 typed
  questions per article — six on quality for human readers, six on AI-search
  optimization — producing 240 scores in ~12s for $0.0115, with per-article
  breakdowns (e.g. Human Reader 23.97/30, AI Search 26.24/30) that surface
  which articles to fix first, for readers vs for AI search.
- **Numbers:** 240 scores / ~12s / $0.0115 — throughput and cost only; no
  accuracy or calibration figures on the scores themselves (ranking-by-score,
  not graded vs ground truth).
- **Evidence:** working builder demo with self-reported run metrics (Instagram
  reel, Sept 28); full walkthrough on the author's YouTube.
- **Why it ranks #212:** the first large-corpus *editorial* scoring run — content
  audit as a Jev-native workflow ("read everything, score everything, fix what
  matters") — and the per-article fix-prioritization list is the confidence-gated
  triage pattern (#3) in publishing clothes. Stays Tier 2 until score quality
  is measured. Pairs with #19 (1kpapers classification) and #74 (viral-post
  scoring).
- **Source:** https://www.instagram.com/reel/Dd1rN_gu5mS/

### 216. SEO keyword-gap analysis — "find every keyword we can steal" (Oliver Merrick) (14, E4) — NEW
- **What:** competitive SEO workflow — Claude pulls the top 10 Google results
  per keyword; Jev judges each result (matches search intent? thin? old?
  weak); keywords where ≥3 of 10 results are beatable get flagged for attack.
- **Numbers:** 72 keywords, 720 results analyzed, ~$0.0363 total (~4¢ for 720
  checks), on-screen dashboard.
- **Evidence:** working demo with on-screen metrics (IG reel, Sept 29);
  creator-described; engagement-bait CTA (comment "JEV" for the guide) —
  promo-adjacent, but the dashboard figures are concrete.
- **Why it ranks #216:** a new use-case shape — SERP beatability judgment as
  the competitive-intel layer of an SEO workflow. Pairs with #75 (Lurk's GEO
  monitor) and #19 (1kpapers classification) as the content-analytics family.
- **Source:** https://www.instagram.com/reel/Dd3ZGNeSvK5/

### 217. Sales-call objection mining at scale (Oliver Merrick) (14, E4) — NEW
- **What:** "hear every objection" — 312 sales calls transcribed; Jev asked
  about specific objections (price, timing, trust, authority, need);
  objection locations visualized to prioritize follow-ups.
- **Numbers:** 312 calls, 9,360 objections found, $0.1558 total; figures read
  off the on-screen dashboard (no spoken narration).
- **Evidence:** working demo with on-screen metrics (FB reel, Sept 29),
  creator-described; no accuracy figures on the objections.
- **Why it ranks #217:** call-center analytics is the most labor-expensive
  place bulk judgment lands — 16¢ for a 312-call objection census is the
  shape that sells to sales teams. Pairs with #199 (call-QA scorecard) as the
  sales-call family.
- **Source:** https://www.facebook.com/reel/1984562692239285/

### 218. Startup batch enrichment — 517 YC companies (Oliver Merrick) (15, E4) — NEW
- **What:** "enrich the whole batch" — Jev questions asked over 517 Y Combinator
  startups ("POV: Jev picked which YC startups to cold email first"), producing
  scored/typed datapoints per company.
- **Numbers:** 517 companies, 8,272 data points, $0.0328 total; dashboard-read.
- **Evidence:** working demo with on-screen metrics (IG reel, Sept 29);
  creator-described; the narration summary also describes a related
  conversation-analysis use — the figures here are the dashboard's own.
- **Why it ranks #218:** batch entity enrichment is the highest-volume Jev
  shape yet in a GTM context — 16,000 datapoints per dollar is why
  "score everything, fix what matters" (#212) becomes a viable outbound
  workflow. Pairs with #19 and #216.
- **Source:** https://www.instagram.com/reel/Dd3YtbTSR-y/

### 219. Green-wash trap audit — Jev auditing code repairs for honesty (juan294/sutura) (14, E5) — NEW
- **What:** a probe of Jev as a CI honesty audit: 88 code-repair cases
  (31 green-wash traps / 57 legitimate repairs), Jev judging whether each
  repair is genuine; a confidence-tracked veto policy (refuse when
  P(green-wash) ≥ 0.5, mark insufficient when confidence < 0.7).
- **Numbers:** 95.5% accuracy (84/88); false approvals 2/31, false rejections
  2/57; latency p50 347ms / p90 462ms (transatlantic); 88,776 input tokens,
  $0.0037 for the whole corpus; confidence tracked correctness — the [0.9,1]
  bucket held 67 cases at 99% accuracy. Veto policy: 28/31 traps refused,
  51/57 legitimate approved, the rest marked insufficient.
- **Evidence:** independent measured probe with raw results JSON committed
  (GitHub, run Sept 17).
- **Why it ranks #219:** a new use-case type — Jev auditing the *honesty* of
  code changes, not just their risk. The confidence-tracked veto policy is
  #3's routing shape applied to CI. Pairs with #88 (slopcheck) and #25 (the
  tool-call firewall) as the CI-integrity cluster.
- **Source:** https://github.com/juan294/sutura/blob/HEAD/docs/research/2026-09-17-typesafe-jev-fit.md

### 224. GitHub issue triage — IBM/galaxium-travels repo (IBM Bob, @codewithbob) (14, E4) — NEW
- **What:** Jev triages open issues in the IBM/galaxium-travels repo with typed
  questions (user moment, change shape, money logic, etc.) via a small JSON
  prompt, run head-to-head against Claude Sonnet 5 with a long prose prompt
  (Sept 30 reel by the "IBM Bob" account).
- **Numbers:** 22 issues: Jev 6.9s / $0.00137 vs Claude Sonnet 5 39.4s /
  $0.112 — ~5.7× faster, ~81× cheaper; answers matched on 127 of 154
  questions (82.5%). Creator-measured, video-only.
- **Evidence:** builder-measured side-by-side, reported in a video; no repo or
  raw logs shown.
- **Why it ranks #224:** a like-for-like replacement test of the exact workflow
  Jev claims to displace (prose-prompt classification → typed questions), with
  the match rate stated honestly instead of hidden. Pairs with #208 (router
  eval) and #16 (triage families).
- **Source:** https://www.instagram.com/reel/Dd6tzoaDB6H/

### 225. Customer-intent routing — Expedify four-way comparison (@shashank_s_sharma) (15, E4) — NEW
- **What:** routing the customer message "Can you stop by our office for a demo
  next week?" across 24 messages — keyword rules, classification tree, an LLM,
  and Jev via typesafe.ai with one question, described options, and a 0.6
  confidence cut-off (Sept 29 reel).
- **Numbers:** keyword rules 11/24 vs GPT-6 Astra 24/24 vs Jev 24/24; 0.43s vs
  1.7s per decision; $0.018 vs $2.11 per 1,000 decisions (~100×+ cheaper).
  Creator-measured, video-only.
- **Evidence:** builder-measured 24-message side-by-side, reported in a video.
  Note: rejected in the Sept 29 evening scan as a vendor-figure explainer;
  re-evaluated now that the reel shows a measured test with stated n,
  accuracy, latency, and cost figures.
- **Why it ranks #225:** the honest baseline is the point — keyword rules fail
  on context ("stop" → not interested), the frontier model ties, Jev wins on
  cost — and the 0.6 confidence floor is the catalogue's routing shape again.
  Pairs with #3, #208, #224 (routing/triage cluster).
- **Source:** https://www.instagram.com/reel/Dd3Aw0LBH_Z/

## Tier 3 — working demos / integrations (score 12–13)

### 28. Per-block market-making (jarrodwatts/jev-trader) (13, E4)
- **What:** one Jev decision per Monad block (~300ms): Jev reads the Kuru MON-USDC
  order book and answers buy/sell plus a horizon score; the bot posts a post-only
  limit order one tick inside the touch so it earns the spread. Ships with dry-run
  mode (real book, real decisions, simulated fills) and a live event-stream dashboard.
- **Numbers:** measured model latency ~81ms per decision; Jev cost in the demo totals
  $0.000004 over 3 blocks. Deployed demo currently runs dry-run with a mock model.
- **Evidence:** working integration/demo (GitHub, Sept 17); Jev-in-the-loop in dry-run
  mode — trading P&L figures are simulated, not real.
- **Why it ranks #28:** the first Jev use case with a hard real-time budget (one block)
  and money on the line — worth watching whether real (non-simulated) runs hold up.
- **Source:** https://github.com/jarrodwatts/jev-trader

### 29. Selective software review (13, E4)
- **What:** software-factory extension combines deterministic file-path rules with a
  Noul threshold — extra review runs only when relevant files changed and probability
  < 0.8.
- **New (Sept 17):** devagrawal09/jev-review — a second independent staged code-review
  workflow: Noul risk matrix → Choice+Score file profiles → evidence selection →
  severity → conditional reviewer routing, with a local dashboard. Self-described
  experiment; findings are "review prompts, not proof of a defect."
- **Evidence:** working launch-day integration described by its builder; second
  independent implementation (GitHub, Sept 17).
- **Why it ranks #29:** real integration in a dev loop; two independent builders now
  converged on the staged-judgment shape.
- **Sources:** builder write-up · https://github.com/devagrawal09/jev-review

- **New (Sept 19 — midday):** more review-family builds — Jeremy Huang's Jev PR
  Labeler (PR labels chosen by Jev, size measured as scope not lines), wang2's
  jev-review-action (a GitHub Action reviewing/classifying PRs with Jev alone),
  Kelbie's Hunch (code review against rules written in plain English), and DiffJury
  (a live PR merge/review gate — no metrics).

### 30. Natural-language WHERE clauses for PostgreSQL (realZachi/pg-jev) (13, E4) — NEW
- **What:** natural-language `WHERE` clauses for PostgreSQL — no embeddings, no
  vector column; Jev turns plain English into typed row-selection judgments.
- **Numbers:** 129 rows in ~1s for ~$0.0009.
- **Evidence:** community project with self-reported metrics, via the
  awesome-jev-usecases community index (updated Sept 18).
- **Why it ranks #30:** semantic search without the vector stack — relevance as
  Noul-over-rows instead of embeddings is a genuinely different (and cheaper)
  architecture. Watch for a precision/recall comparison against pgvector.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 18 — evening):** a second independent NL-SQL implementation — kylemclaren/jevql — exposes `WHERE jev(...)` filters, `jev_prob` sorts, and `jev_choice` groups on a vanilla Postgres with no extension, judging rows client-side. Two builders converged on vectorless NL-SQL within three days of launch.

- **New (Sept 19 — midday):** a third vectorless NL-row-judgment build — Hamilton
  Ulmer's DuckDB extension: ~10s per 1,000 rows, "better than an LLM, way more
  ergonomic than a classifier" — plus Colliber's duckdb-jev and Giulio Piccolo's
  pg_typesafe (~76). Four builders converged on Jev-in-SQL within a week.

### 31. Non-English moderation benchmark (Japanese) (13, E4) — NEW
- **What:** a moderation pipeline test on harmful Japanese texts.
- **Numbers:** 826 harmful texts: Jev missed 36 vs OpenAI's 292 — notably strong on
  non-English.
- **Evidence:** community-reported benchmark via the awesome-jev-usecases community
  index (updated Sept 18); author not named in the index, no independent audit.
- **Why it ranks #31:** the first non-English accuracy datapoint — multilingual
  moderation is a real enterprise slot where Jev's edge could be large; pairs with
  #5 (English listing moderation) and #23/#24 (security cluster).
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 32. Wiki-race head-to-head demo (13, E4) — NEW
- **What:** screen-recorded "Wiki Race" competition: Jev vs GPT-5 Terra, Claude Haiku 4.5, Claude Sonnet 5, Luna, Opus 5 and Astro across three races (Baseball→Sun, Rubber duck→Lamport's bakery algorithm, Rubber duck→Fisher-Yates shuffle).
- **Numbers:** Jev finishes each race in ~0.4–0.7s; 8.6x–9.97x faster and 2.3x–71.1x cheaper than competitors (~$0.03–0.05 vs $1–$6 per race). One race catches GPT-5.6 Terra hallucinating a hop and having to redo it.
- **Evidence:** working demo with self-reported metrics (Instagram screen recording, Sept 17); promotional tone, single-task.
- **New (Sept 26 — midday):** a second sighting — @niralikhoda (Instagram
  reel): Chess→Sabarmati Ashram Wikipedia race, Jev 1.6s vs Claude Haiku 5.3s
  vs Claude Opus 8.6s; Super Mario "every jump" decisions ~300ms each.
  Thinner figures than the original, but confirms the race shape against named
  models.
- **Why it ranks #32:** the first multi-hop *structured navigation* race against named frontier models with published deltas — a speed benchmark on a real task shape, not just batch QA.
- **Source:** https://www.instagram.com/reel/DdZO6W8z7qg/

### 33. Support ticket / intent routing (13, E2)
- **What:** one parallel request answers several bounded questions per ticket —
  department Choice, refund-requested Noul, severity Score — then code routes.
- **Numbers:** TypeSafe's workflow evals claim ~193.6× faster, ~444.6× cheaper vs
  frontier-model workflows (vendor eval; caveats: team-built workflows, model-consensus
  labels, demo-friendly inputs).
- **Evidence:** vendor eval harness + public cookbook examples.
- **New (Sept 17):** independent audit of TypeSafe's four-workflow eval
  (counterproof.io) found the two consensus graders disagreed on the final decision in
  8 of 19 hand-picked published cases — the vendor numbers measure agreement with
  frontier-model consensus, not ground truth.
- **Why it ranks #33:** enormous decision volume × per-decision cost delta = real ROI,
  but independent numbers are still missing.
- **New (Sept 24 — morning):** a neutral third-party tabulation of the public
  dashboard numbers (kingy.ai, 711 cases, aggregate Jev 67.8%): Security
  incidents 240 — Jev 61.7% vs Opus 66.2%; Agent trace observability 117 —
  71.6% vs Sol 76.6%; Invoice processing 150 — 61.8% vs Sol 79.1%; Customer
  service 204 — 76.0% vs Sol 78.3%. Same underlying vendor data (not new
  independent numbers), but the first clean public table — and it confirms
  invoice processing as Jev's weakest suit vs frontier models.
- **Source:** https://counterproof.io/notes/typesafe-jev-benchmark-consensus-labels/
  · https://kingy.ai/blog/typesafe-jev-review-the-ai-model-that-doesnt-generate-text/

### 34. Jev on Vercel AI Gateway (13, E2)
- **What:** Jev available as `typesafe-ai/jev` on Vercel AI Gateway via AI SDK 7's
  experimental `evaluate` API (state + named questions); Zero Data Retention and No
  Training per request; calls count toward budgets, appear in logs/custom reporting.
  Example use cases named: choosing the next tool or subagent in an agent loop,
  continue/retry/stop decisions, urgency/risk scoring, model-output verification and
  guardrails.
- **Numbers:** none new — repeats TypeSafe's 193.6×/444.6× vendor evals.
- **Evidence:** shipped platform availability (partner announcement); the example use
  cases are vendor-supplied, not independently demonstrated.
- **Why it ranks #34:** distribution — enterprise-friendly privacy posture (zero data
  retention) on a major platform widens the builder funnel; watch for community volume
  to follow.
- **Source:** https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway

- **New (Sept 18 — evening):** Jev is now available on OpenRouter in beta as `typesafe/jev-latest` — a second waitlist-bypass distribution channel alongside Vercel AI Gateway.

- **New (Sept 19 — morning):** Jev is also live on Cloudflare Workers AI with examples for support routing, refund decisions and risk escalation (developers.cloudflare.com), and Cua shipped a **jev-use** dev preview pairing Jev with Cua Driver for low-latency computer-use decisions (clicking, typing, scrolling, interface-element choice) — the decision layer reaching managed computer-use platforms.

- **New (Sept 19 — midday):** the framework-integration layer keeps widening —
  LiteLLM pass-through docs, langchain-typesafe (TypeSafeClassifier), Pydantic AI
  native-model docs, and jevkit (Rust CLI with a question linter). Watch #72 (Eve)
  for the framework-default escalation.

- **New (Sept 19 — evening):** LangChain officially adds Jev to the agent control
  loop (RuntimeWire, Sept 19) — "does an agent work faster, cost less and fail
  less often when a specialist makes its routine decisions?" is now the testable
  question. One unverified adoption claim (via small IG account): Jev became the
  fastest-adopted model in Vercel AI Gateway history, ~13% of paid teams within
  24 hours — flagged as unverified.

- **New (Sept 21 — morning):** adoption claim corroborated — Doomers (the agency
  that ran TypeSafe's Sept 15 X launch, 37.2M views) published a launch case
  study: Jev is the fastest-adopted model in AI Gateway history, in use by
  nearly 13% of paid teams within 24 hours — twice the GPT-5.6 family, >6×
  Claude Fable 5.1, reaching a tenth of teams inside 18 hours. Launch-vendor-
  adjacent source, but it's the second independent channel carrying the same
  numbers (doomers.ai, crawled Sept 21).

- **New (Sept 21 — midday):** a third channel — Forbes/AI Weekly report the
  same 13%-in-24h figure (2× GPT-5.6, >6× Fable 5.1); AI Weekly also reports
  Langfuse wired Jev into its stack within three days of launch — a new platform
  integration channel after Vercel and OpenRouter.

- **New (Sept 23 — midday):** a demand datapoint — per firecrawl.dev's write-up,
  TypeSafe opened signups to everyone on Sept 20 with $5 in free credit, then
  paused new signups on Sept 22 under demand; Vercel AI Gateway (since Sept 16)
  and OpenRouter bypass TypeSafe's signup entirely. Single-source claim —
  corroborate before treating the pause as fact.

- **New (Sept 23 — evening):** a per-account usage datapoint — Threads user
  Dr. Hafiz Rahman posted his token console: 306,680,593 tokens across Sept
  15–21 on one account, with ~60M/hour spikes. Single-account figure, not
  market-wide, but the highest per-account volume observed so far — the demand
  side of the story the adoption numbers tell.
- **Source:** https://www.threads.com/@drbabar/post/Ddj2i4gVJ8B (Threads, Sept 22)

### 35. Typed batch QA benchmark (Wilson T.) (12, E5) — updated
- **What:** side-by-side terminal race, 27 typed questions each: `uv run typesafe-race
  ask typesafe` returned the full JSON array in 0.314s at $0.000981 vs OpenAI
  autoregressive API at 8.66s, $0.003880.
- **New (Sept 17):** a second independent race by Hudson Brendon (@99hud), same
  27-question format — GPT-5.6: 8.566s, $0.01388 vs Jev: 0.114s, $0.000081; scaled
  receipts for 1,000 queries: $13.88 vs $0.08 — ~170× cheaper, ~75× faster.
- **New (Sept 18 — midday):** a third independent race — usutaku (CEO, Michikusa Co.,
  Facebook reel, Japanese creator): "JEV Speed Race" on customer email
  classification — Jev ~0.4–3.4s vs GPT Luna ~2.9–6.6s, Claude Sonnet ~2.9–11s,
  Gemini 3.5 Flash ~2.9–8.9s. Author-measured; no cost figures published.
- **New (Sept 19 — morning):** a fourth independent race — @simplifyinai (Threads),
  same 27-question format: TYPESAFE 0.114s at $0.000083 vs the GPT-5.6 baseline at
  $0.01388 (caption frames it as ~193× faster, ~444× cheaper vs frontier).
- **Evidence:** four independent head-to-head tests with metrics (Threads + Instagram
  + Facebook, Sept 17–19). More benchmark than workload — it independently grounds
  the cost/latency deltas.
- **New (Sept 22 — midday):** a fifth independent latency race — OpenRouter's Ori Eval
  judging benchmark (surfaced via madewithjev.com): Jev median 154ms, >5× faster
  than the next fastest model, and even Jev's slowest requests beat every other
  model's median (GPT-5.6 Luna 860ms, DeepSeek V4.1 Flash 911ms, Qwen3.8 Flash
  914ms, GLM 5.3 Flash 1,544ms).

- **Sources:** https://www.threads.com/@twt_wilson/post/DdYe6V9jJ9f ·
  https://www.instagram.com/reel/DdZxxWEDRAI/ ·
  https://www.facebook.com/reel/2552201755282975/ ·
  https://www.threads.com/@simplifyinai/post/DdeAkQ9ksP9

### 36. StarCraft mission control (phyous/tsai-sc) (12, E5)
- **What:** original 1998 StarCraft shareware executable in a harness: structured
  game state (units, economy, map knowledge) → Jev chooses a command from a bounded
  candidate menu → deterministic mouse/keyboard adapter. Jev completed the Strongarm
  combat mission with an independent verification report and reproducible evidence
  bundle.
- **Numbers:** 421 model decisions; median API latency 382.95ms; 9.4M input tokens;
  17m38s elapsed; victory screen visually reviewed.
- **Evidence:** independent experiment with metrics and a verification report — but a
  single successful development run ("attempt 16"), explicitly not a win rate.
- **Why it ranks #36:** the most rigorously documented long-horizon Jev control loop —
  hundreds of decisions with full traces — and the author's restraint (one run ≠ a
  benchmark) sets the standard for how these should be reported.
- **Source:** https://github.com/phyous/tsai-sc

### 37. MMLU-Pro capability probe (Archer Hume) (12, E5) — NEW
- **What:** third-party MMLU-Pro probe of Jev: 84.6%.
- **Numbers:** 84.6% on MMLU-Pro.
- **Evidence:** independent third-party probe, via the awesome-jev-usecases community index (updated Sept 18); the original write-up was not directly located.
- **Why it ranks #37:** not a use case — a capability probe — but the first independent knowledge/reasoning measurement of Jev outside TypeSafe's own evals; it frames what "judgment quality" the use cases above are built on.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 38. Drone tactical judgment (RomanSlack/jev-drone) (12, E4)
- **What:** autonomous quadrotor (Skydio X2, MuJoCo sim) with layered control:
  500 Hz geometric controller / 50 Hz safety reflex (code owns safety) / 15 Hz
  classical CV perception / ~2.5–3 Hz Jev tactical judgment, advisory only — one call
  answers a Choice over manoeuvres, a Score for risk, a Noul for target-lost.
  Code decides when to ask; a hard reflex layer can override any judgment.
- **Numbers:** ablation vs greedy heuristic: baseline never passes station 2;
  Jev-engaged completes the whole 77.5m course, target in view 82% vs ~19%, reflex
  pinned 9% vs 65–71%, 0 collisions. 80 calls over 65s, 0.11s median latency.
- **Evidence:** independent experiment with metrics and ablation (GitHub); author
  caveats are explicit — single 65s run, and a matched 3-seed comparison on a simpler
  arena showed no Jev advantage. Narrow claim only: the baseline can't express the
  manoeuvre; Jev supplies it.
- **Why it ranks #38:** a textbook "keep the loop, the safety and the arithmetic in
  code; Jev for the narrow judgment in the middle" build — with honest negatives.
- **Source:** https://github.com/RomanSlack/jev-drone

- **New (Sept 23 — morning):** a second, gentler drone build — arielweinberger's
  jev-autopilot: a Three.js 3D drone sim where Jev flies a random city from pad
  A to pad B. Code turns altitude/bearing/obstacles into a situation report; one
  Jev request asks six questions (a Choice per stick axis — throttle, yaw, pitch,
  roll — plus two Booleans: commit to landing? cut motors?); answer distributions
  become stick deflections with hard safety limits in code. A trip costs ~$0.01.
  The probabilistic-control-mapping shape (distributions → actuation) is the
  portable technique.
  (https://github.com/arielweinberger/jev-autopilot)

### 39. Security incident triage + SOAR proposal (12, E2)
- **What:** close an alert, queue it for an analyst, or contain the threat —
  one of TypeSafe's four published workflow evaluations.
- **New:** forward-deployed engineer Murat Aslan sketched a concrete 4-stage automated
  incident-response pipeline on Jev (Triage → Disposition → Containment → Playbook);
  no own test — proposal only, and his accuracy-vs-cost slide is TypeSafe's vendor
  chart, not independent.
- **Evidence:** vendor eval harness + credible community proposal (Sept 17).
- **Caveat:** the counterproof.io audit (Sept 17) of TypeSafe's workflow eval found the
  two consensus graders disagreed on the final decision in 8 of 19 hand-picked
  published cases — treat the vendor accuracy figures as consensus-agreement, not
  measured correctness.
- **Why it ranks #39:** real SOC pain where 100ms judgment beats a minutes-long LLM
  call; the proposal gives the deployment shape to watch for.
- **Source:** https://www.threads.com/@iammurataslann/post/DdYYjE0iBxm ·
  https://counterproof.io/notes/typesafe-jev-benchmark-consensus-labels/

### 40. Graph navigation on Neo4j (jexp/neo4jev) (12, E4)
- **What:** library + three notebooks + Streamlit app navigating a Neo4j graph one hop
  at a time: at each node, outgoing relationships become `Choice` options (type,
  properties, target node), and a `Noul` ("has the goal been reached?") rides in the
  same `system_one` call — one round-trip per hop regardless of question count.
  Top-k/cutoff selection over returned probabilities implements beam search (sum of
  log-probabilities, avoiding float underflow and length bias); results render in an
  interactive `neo4j-viz` graph. Built and run end-to-end against the live
  `companies2` graph; without a key, the pipeline exercises on explicitly labelled
  stand-in answers — nothing presented as TypeSafe output that wasn't.
- **Evidence:** working demo described by builder (GitHub, Sept 18); no metrics reported.
- **Why it ranks #40:** graduated from a Sept 17 proposal to a running demo — the
  beam-search-over-typed-probabilities pattern generalizes to any path-finding or
  stepwise-selection problem, but no real workload is attached yet.
- **Source:** https://github.com/jexp/neo4jev

### 41. Context compaction keep/delete (tamaratran/fast-jev-compaction) (12, E4) — NEW
- **What:** Claude Code compaction → Jev keep/delete decisions over context chunks.
- **Numbers:** none — the reported result is "content stays verbatim" (a fidelity
  claim, not a metric).
- **Evidence:** community project described by builder, via the awesome-jev-usecases
  community index (updated Sept 18).
- **Why it ranks #41:** compaction is a high-frequency decision inside every coding
  agent — cheap, fast keep/delete is a natural Jev slot; needs a fidelity benchmark
  to graduate out of Tier 3.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 19 — midday):** an independent user reports nearly 1M tokens → 86K in
  1 second with fast-jev-compaction — the first throughput figure for keep/delete
  compaction — though fidelity/retained usefulness was not measured
  (github.com/tamaratran/fast-jev-compaction). Sibling builds the same day: Nour's
  pi-jev-compaction (automatic context clearing for Pi), Konstantinos Botonakis's
  codex-context-diet (Codex plugin trimming bulky tool results), Ghaleb Dweikat's
  winnow (~17), CompozyOS's Yoshi (~12), Tamara Tran's jev-pruner (~15), and Kush
  Bhuwalka's Jev Sift ("classify first, read selectively" agent plugin + MCP).
  Counterweight: Theo (@theo, x.com) published a detailed critique — per-tool-call
  filtering is *not* compaction: lost reasoning traces, higher cache-write costs, and
  agents stuck in retry loops.

- **New (Sept 21 — midday):** @0x_kaize (via the AY Automate roundup) — Claude
  Code context compaction as keep/drop Jev decisions on chunks rather than
  fixed-rule compaction; qualitative, second-hand.

### 42. Android UI agent (droidrun/mobile-jev) (12, E4) — NEW
- **What:** an Android UI agent driven by Jev — the demo books an Uber route.
- **Numbers:** ~21s, 9 actions for the route demo.
- **Evidence:** community project with self-reported demo metrics, via the
  awesome-jev-usecases community index (updated Sept 18).
- **Why it ranks #42:** the mobile counterpart to #22 (browser) and #8 (desktop) —
  Jev picking UI actions on a third platform; pairs with xtract.ai's iPhone QA demo
  (#44).
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 43. Semantic features for classical ML (wine notes → CatBoost) (12, E4) — NEW
- **What:** Jev judgments as numeric features for classical ML — 2,000 wine notes
  turned into CatBoost features.
- **Numbers:** 1.77 RMSE.
- **Evidence:** community-reported result via the awesome-jev-usecases community
  index (updated Sept 18); author not named in the index, no baseline quoted.
- **Why it ranks #43:** a new use-case type — Jev as a *feature extractor* feeding
  non-LLM models — where the value is judgment quality, not latency.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 24 — evening):** a second datapoint for this shape — one feature
  cookbook went from 18 questions to 38 over five rounds, ending with 67
  numeric columns feeding a CatBoost regressor (reported via zeke/jev's
  research notes). The 18→38 question-count growth is a concrete instance of
  the speculative-fan-out pattern: adding questions is nearly free.

### 44. Mobile QA tester (xtract.ai) (12, E4) — NEW
- **What:** "hire Jev as your QA tester" — an agent driving an iPhone to-do app: the
  terminal test log shows 'Tap Add List' / 'Fill title with text input' while the
  app creates a list titled "Jev QA" and adds a reminder "do it now".
- **Numbers:** none reported — working demo, unmeasured.
- **Evidence:** working demo (Instagram reel, Sept 18); no metrics.
- **Why it ranks #44:** the first mobile-QA use case — structured UI actions are
  Jev's native shape — but unmeasured demos don't move the economics.
- **Source:** https://www.instagram.com/reel/DdapN3LIqwx/

### 45. Agoda travel-search agent (@bluevelo1666) (12, E4) — NEW
- **What:** an agent completing Agoda hotel searches for Tokyo, Osaka and Kumamoto
  (Oct 20–22, 2026) with Jev in the loop.
- **Numbers:** search durations 17.1s / 23.9s / 19.3s; the monitoring dashboard shows
  $0.04 total model spend with $0.01 attributed to typesafe-ai/jev, P50 TTFT 0.34s.
- **Evidence:** working demo with self-reported metrics (Threads, Sept 18).
- **Why it ranks #45:** a real booking-site loop with a spend dashboard — $0.01 of
  Jev per search starts to price the pattern; third platform after browser (#22)
  and mobile (#42/#44).
- **Source:** https://www.threads.com/@bluevelo1666/post/DdbpQhLE9U7

### 46. Local file-cleanup triage (mahan-ym/cleaner) (12, E4) — NEW
- **What:** Next.js app built with Claude Code: the TypeSafe SDK scans local project directories and classifies files "safe to delete" / "uncertain" / "keep" to free storage (node_modules, .next build caches correctly flagged).
- **Numbers:** 200,000 input tokens for $0.01 total.
- **Evidence:** working demo described by builder (YouTube experiment, Sept 17); no accuracy measurement.
- **Why it ranks #46:** a genuinely new slot — deterministic cleanup triage with an explicit "uncertain" bucket is the confidence-gated policy in miniature; needs an accuracy read to graduate.
- **Sources:** https://www.youtube.com/watch?v=267fGQeLSkg · https://github.com/mahan-ym/cleaner

### 47. Voice-controlled design-tool automation (@rapidlygrow.ai) (12, E4) — NEW
- **What:** voice commands drive Figma — the properties panel cycles through actions (Change color, Delete, Choose an action) for voice-triggered edits, "for Just One Cent".
- **Numbers:** cost claim only ("one cent"); no accuracy numbers.
- **Evidence:** working demo with on-screen actions (Instagram reel, Sept 18); promo-adjacent ("comment JEV for a link").
- **Why it ranks #47:** the fourth Jev-in-the-loop UI modality after browser (#22), desktop (#8) and mobile (#42) — voice intent → structured action, this time in a design tool.
- **Source:** https://www.instagram.com/reel/Ddb71yQy0Rw/

### 60. AI-slop detector (Jon Kraayenbrink) (13, E4) — NEW
- **What:** a live free tool that checks a website for 35 tells of AI slop
  (purple gradients, emoji headers, "seamlessly", fake testimonials, bento grids…).
- **Numbers:** 243ms check time, 35 tells, $0.00015 per check.
- **Evidence:** live tool with self-reported on-screen metrics (X, Sept 19);
  promotional framing but the tool is live and tryable.
- **Why it ranks #60:** a new use-case type — cheap multi-signal quality judgment —
  live in production with per-decision economics visible; the Noul-over-checklist
  shape generalizes to any quality-gate page scan.
- **Source:** via https://madewithjev.com/

- **New (Sept 19 — midday):** a second AI-slop detector build reports 10,000 words
  judged in ~2s (via madewithjev.com). Sibling builds: Daniel Willoughby's Sniff
  Test — a prose linter for AI-writing tells, regex rules plus Jev judgment (~14
  rules) — and TypeSafe's own Clarity Judge example, checking writing on separate
  named axes with a verdict each.

### 61. Creator growth analytics (Ian Nuttall) (13, E4) — NEW
- **What:** 3,282 of his own X posts (100M views) analyzed — 8 questions each on
  topic, hook, tone, whether it teaches something.
- **Numbers:** 4,252,330 tokens, $0.1282, 8m34s for the full run; findings: how-to
  posts got 150 median likes vs 44 average; AI/coding a 1.9× multiplier topic.
- **Evidence:** independent builder run with self-reported totals (X, Sept 19);
  author-measured, no accuracy validation of the analysis itself.
- **Why it ranks #61:** the largest single corpus analyzed by one Jev run yet at a
  sub-dollar cost — personal analytics as a one-shot data product, the same
  map-reduce shape as #2 but for a single user.
- **Source:** via https://madewithjev.com/

### 62. Marketplace listing triage + seller outreach (Alan Daitch) (13, E4) — NEW
- **What:** Jev + Playwright scanning used-item listings: 26 articles per minute,
  Jev decides per listing — discard, offer, or message the seller when the post
  lacks data.
- **Numbers:** 406ms per decision; whole search cost $0.00085 (~$1 reviews
  ~26,000 listings).
- **Evidence:** independent builder demo with self-reported metrics (X, Sept 19).
- **Why it ranks #62:** the first browse-triage *with action* use case — judgment
  (discard/offer/message) driving outreach on a live marketplace, not just
  classification for a dashboard. Pairs with #5 (listing moderation) as the buyer
  side of the same market.
- **Source:** via https://madewithjev.com/

### 63. YouTube sponsor-skip extension (Tony Dinh / tdinh_me) (12, E4) — NEW
- **What:** Chrome extension that listens to YouTube audio, detects sponsor
  segments, and skips them in real time; prototype, BYOK, open-source.
- **Numbers:** ~$0.005 per video.
- **Evidence:** working builder demo with a cost figure (X, Sept 19); no latency
  or accuracy numbers.
- **Why it ranks #63:** the first audio-state Jev use case — real-time segment
  detection as a cheap per-segment judgment — and the repo (trungdq88) lets this
  graduate quickly with measurement.
- **Source:** via https://madewithjev.com/

- **New (Sept 19 — midday):** SuperTurbo's "Hawk or dove?" — Fed press conferences
  scored word by word at ~150ms — extends the family to speech/financial-sentiment
  segmentation.

- **New (Sept 19 — evening):** the ~$0.005/video figure is corroborated via the
  madewithjev.com X-post harvest (Tony Dinh's post text verbatim).

### 64. Confidence-threshold calibration tooling (abhixhek/jevcal) (12, E3) — NEW
- **What:** `jevcal` — calibrate, threshold, and drift-check typed decision models
  against an LLM teacher: measures Jev on your data, picks the threshold that
  meets your accuracy target, prices the cascade, fails CI when a model update
  breaks it. TypeSafe's agreement restricts publishing Jev perf numbers, so the
  README ships simulator examples only.
- **Numbers:** none on real Jev — by design (bundled example output only).
- **Evidence:** builder work-in-progress (GitHub, Sept 19); solves the operational
  problem at the heart of #3 (confidence-gated routing).
- **Why it ranks #64:** the first tooling for the deployment question every use
  case above hand-waves — "which threshold do I ship?" The threshold-plus-CI
  shape is the missing production layer under the whole watch.
- **Source:** https://github.com/abhixhek/jevcal

- **New (Sept 19 — midday):** tooling keeps compounding — Daniel Ari Friedman's
  daf-jev (Python question builders + confidence gates + calibration, a jevcal
  sibling), Ariel Frischer's jevkit (Rust CLI with a linter that checks questions
  before you pay), Alexey Butochnikov's unofficial Laravel package, Pedro Knigge's
  mcp_jev (local MCP server running ready-made Jev question packs), langchain-typesafe
  (TypeSafeClassifier for LangChain apps), LiteLLM pass-through docs, Pydantic AI
  native-model docs, and Zaious's Jev Capability Atlas (where Jev holds up vs
  breaks, "with real API receipts").

- **New (Sept 21 — morning):** the first Java SDK — `typesafe-ai-java` by @jamilxt,
  on Maven Central (Instagram announcement, Sept 21). The SDK/tooling layer keeps
  widening past Python/TypeScript.

### 65. Tool-call policy layer for Claude Code (Clownware "Bouncer") (12, E3) — NEW
- **What:** a judgment layer that checks every Claude Code tool call against the
  user's policy — the supervision/routing pattern applied to coding-worker fleets.
- **Numbers:** ~$0.04/day, builder-claimed.
- **Evidence:** builder demo with a self-reported cost figure (X, Sept 19); no
  published policy-hit/miss metrics.
- **Why it ranks #65:** joins #51 (foreman over Codex) as the second coding-agent
  tool-call firewall — per-call judgment at 4 cents a day is the price point that
  makes "check everything" viable in dev loops.
- **Source:** via https://madewithjev.com/

- **New (Sept 19 — midday):** two more coding-agent infrastructure builds: Ravinder
  Poonia's jev-flash-router (MCP server so coding agents stop spending tokens on
  small choices) and Max Lv's pi-jev (a Pi extension — Jev picks the file excerpts,
  a local model writes the code).

### 77. OCR + Jev image triage (Fayaz Ahmed) (13, E4) — NEW
- **What:** an image classifier with OCR + Jev — categorized ~900 images in 40
  seconds.
- **Evidence:** builder-described test with throughput figures (via madewithjev.com,
  Sept 19); no accuracy figure.
- **New (Sept 22 — midday):** a Hindi creator demo shows two more Jev builds in the
  same family — instant image search by keyword ("grass" → grass images, "blue" →
  blue images) and a drag-and-drop file organizer that auto-sorts files into
  folders by Jev judgment (IG @DdloBwaT2zO, Sept 22); creator-described, no
  metrics — treat as a lead until measured.

- **Why it ranks #77:** the first OCR+decision image-triage build — a new modality
  pairing (Jev reads OCR text, not pixels) — but it needs an accuracy read to
  graduate.
- **Source:** via https://madewithjev.com/

### 78. Social-firehose post-by-post judge (firehose-judge) (13, E4) — NEW
- **What:** the live Bluesky firehose judged post by post, with a lane for humans —
  at ~$0.00003 per judgment.
- **Evidence:** builder-described with a per-judgment cost figure (via
  madewithjev.com, Sept 19); no accuracy/precision figures.
- **Why it ranks #78:** stream-rate social triage at 3 cents per thousand posts —
  the firehose shape at a price where "judge everything" is the default, not the
  exception.
- **Source:** via https://madewithjev.com/

### 89. Launch-window X-post meta-analysis (openchamber.dev) (13, E4) — NEW
- **What:** a measurement-methods study of the launch itself: 26,896 tweets
  collected (Sept 15–18), 12,759 retained with usable content, author-reported
  measurements separated from repeated vendor claims by verbatim-excerpt labels.
- **Numbers:** users' reported measurements vs TypeSafe's claims — speed-up median
  **7×** (quartiles 2×/20×) vs 193.6×; cost reduction median **30×** (5×/85×) vs
  444.6×; latency median 76ms (2ms/270ms quartiles) vs 70–500ms. The most-repeated
  figure across posts was ~193× — nearly the homepage number — "repetition gave
  that claim reach, but added no independent evidence." 24% of posts came from
  authors who had tried Jev themselves (2,172 unique accounts); categories:
  routing 604, classification 565, ranking/scoring 263, tool selection 108.
- **Evidence:** independent meta-analysis with disclosed method and limits
  (selection effects, no rerun experiments, 98%-agreement label schema check);
  an honest correction to the whole watch's evidence base.
- **New datapoints it surfaces (otherwise unindexed):** PR review $0.00007/PR in
  0.5s; 20,700 YouTube comments classified in 2m27s for $0.20; ESLint rule-checking
  at 90% agreement with the rule's own verdict; PII redaction in support messages
  inside Postgres; a plain-English rule engine at 0.7s / $0.0001 per check;
  @chetaslua's debate scoring (1,191 calls, 1.18M tokens, 415ms median, $0.0497);
  a 150-persona simulated customer survey for ¥1.8 in ~5s. Also the sharpest
  methodological objection seen so far (@nurbolatsn): an LLM can be asked to return
  `y` or `n` without a paragraph — the practical baseline is the shortest reliable
  call the app already makes, not a verbose one.
- **New (Sept 22 — midday):** a second site-wide aggregation — madewithjev.com
  published its own "Jev Build Report": every public Jev build in week one, with
  the median published cost per decision, the median decision time, and the
  stars/languages of every repo created since launch (rows as JSON, free to
  cite). A complementary cut to the openchamber meta-analysis above.

- **New (Sept 23 — midday):** madewithjev.com's pricing guide now aggregates 485
  catalogued builds: **median $0.000068 per decision across 15 published runs**
  — a second aggregate cost figure to sit beside openchamber's medians (7×
  speedup / 30× cost reduction). Also: "100,000 posts scored in 20.4 seconds"
  from their use-cases guide — a bulk-scoring aggregate, build unattributed.

- **Why it ranks #89:** not a use case — a calibration of every evidence claim in
  this catalogue. The verdict: user-reported gains are substantial (7×/30×
  medians) and much smaller than the homepage multipliers; the gap mostly reflects
  different baselines. Every "×" figure in this file should be read through it.
- **Source:** https://openchamber.dev/blog/jev-typesafe-ai/

### 90. jevmeter — debate-statement scoring (@chetaslua) (12, E4) — NEW
- **What:** presidential debate statements scored through five yes/no questions —
  per-sentence scoring as a consumer demo.
- **Numbers:** 1,191 calls, 1.18M tokens, 415ms median latency, $0.0497 total
  (~$0.05 to score every sentence of a debate).
- **Evidence:** builder demo with self-reported totals (X, Sept 19, via
  openchamber.dev + marktechpost); "demonstrates inexpensive scoring, not verified
  fact-checking accuracy."
- **Why it ranks #90:** the cheapest per-statement judgment datapoint yet — a new
  use-case shape (real-time statement scoring) with a full cost receipt, but no
  accuracy ground truth.
- **Sources:** https://openchamber.dev/blog/jev-typesafe-ai/ ·
  https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/

### 96. Pong real-time latency race (Matthew O'Riordan / Ably) (13, E5) — NEW
- **What:** a playable four-lane Pong comparison isolating decision latency: the
  same game state and instruction goes to Jev, Gemini 3.8 Flash, Claude Haiku
  4.5 and GPT-5.6 Sol — each model answer advances the ball one step, so slow
  responses play visibly slower games.
- **Numbers:** Jev averaged 227ms/decision (p95 400ms) vs Gemini 3.8 Flash 3.2s,
  Claude Haiku 4.5 2.5s, GPT-5.6 Sol 3.5s. In the first 12 seconds Jev returned
  47 decisions vs 3 / 2 / 2 for the chat models. Recorded Sept 17 from Vercel's
  iad1 region through the AI Gateway on one shared key; chat models used
  structured-output requests, temperature 0, reasoning disabled, no retries.
- **Evidence:** independent builder measurement with disclosed method, published
  source code and recorded statistics (RuntimeWire, Sept 18).
- **Why it ranks #96:** the cleanest controlled latency comparison yet — and the
  honest framing is the lesson: the chat models chose the correct Pong move
  95–100% of the time; Jev's advantage was answering far more frequently, not
  better strategy. Latency is real, intelligence is separate. Pairs with #32
  (Wiki race) and #55 (game loops).
- **Source:** https://runtimewire.com/article/diogo-almeida-typesafe-jev-40m-seed-pong

### 97. Voice-controlled Mac assistant acting mid-utterance (Andy Gao) (13, E4) — NEW
- **What:** a voice-controlled Mac assistant that acts on spoken commands *before
  the speaker finishes talking*: "open up the Notes app for me" → Notes opens
  mid-sentence, then creates a note titled "Hello"; also opens Arc, Googles a
  name, opens X.com, takes a Photo Booth picture — each executing while speech
  continues.
- **Numbers:** none — screen-recorded demo, unmeasured.
- **Evidence:** working demo by builder (original X post Sept 18; reposted IG
  @evolving.ai Sept 21 ~670 likes, @newmeta.ai Sept 20 ~3.1K likes); builder-adjacent
  sourcing — the original post wasn't directly crawled.
- **Why it ranks #97:** the first demo of *pre-emptive* action from partial
  speech — the latency story pushed to its extreme, deciding mid-utterance.
  Generalizes #20 (voice browser) to whole-OS control; needs measured
  latency/false-trigger numbers to graduate.
- **Sources:** https://www.instagram.com/reel/DdjIOcstTtn/ ·
  https://www.instagram.com/reel/DdfwB0dyZp1/ (reposts of a Sept 18 X post)

### 98. Live Twitch-chat intent filtering (Jev Chat for Twitch) (13, E4) — NEW
- **What:** BYOK Chrome extension that reads a Twitch channel's chat over the
  anonymous IRC WebSocket, asks Jev one category `Choice` per message in batches
  of 20, and shows a second column of only the messages matching a chosen intent
  (helpful, questions, funny, feedback).
- **Numbers:** ~504 input tokens per message; ~$0.15/hr on a 2-msg/s chat,
  $0.76/hr at 50 msg/s.
- **Evidence:** working build with on-screen economics, via the yibie/awesome-jev
  community index (crawled Sept 20); builder self-measured.
- **Why it ranks #98:** the first *live-stream* moderation use case — stream-rate
  filtering at a sub-dollar-per-hour price point is the "judge everything" shape
  from #78 (firehose) applied to community chat. Needs precision/recall on the
  intent buckets to graduate.
- **Source:** https://github.com/yibie/awesome-jev

### 105. Skittles sorting race — Jev vs Claude Sonnet 5 (13, E4) — NEW
- **What:** split-screen race: 10,000 Skittles sorted by color, Jev 1.13 vs
  Claude Sonnet 5 (Facebook reel, Sept 21, @felixmohr).
- **Numbers:** Jev 1.13: 9,700/10,000 correct (97%, 300 errors), 220.8s, $0.15.
  Claude Sonnet 5: 9,943/10,000 (99.4%, 57 errors), 1,075.2s, $1.86. On-screen
  summary: 4.9× faster, 12× cheaper, 2.4 points less accurate.
- **Evidence:** builder-measured demo with metrics (animated visualization,
  Sept 21); promotional/lead-gen framing, but the accuracy gap is reported
  honestly.
- **Why it ranks #105:** the first bulk-classification race with a *published
  accuracy delta against a named frontier model* — speed and cost win, accuracy
  loses, and both sides are on screen. Pairs with #35 (typed batch QA races) as
  the price-accuracy frontier.
- **Source:** https://www.facebook.com/reel/1431013785795356/

### 109. Live radio-show WhatsApp triage (radio-chat) (13, E3) — NEW
- **What:** Jev triages WhatsApp messages sent to a live radio show — intent,
  tone, moderation flags and an "is this good enough to read on air?" score
  from one decision-model request with several questions; an LLM extractor
  (topic, place, name, summary) is only called for the messages worth the cost.
- **Numbers:** none published — described as a real-world deployment in the
  typesafe-laravel SDK README (crawled Sept 21).
- **Evidence:** builder-described live use, second-hand via SDK docs (E3);
  treat as a lead until the app itself is located.
- **Why it ranks #109:** a new vertical (live broadcast audience triage) —
  and the cleanest example yet of the generative + decision division of labor
  applied to *inbound text*: Jev gates the expensive LLM. Same family as
  #16/#75 (inbox triage) with the human-on-air consequence making the gate
  load-bearing.
- **Source:** https://github.com/marcemarin/typesafe-laravel

### 113. Dataframe bulk-column generation + adaptive UI (marimo / jevframe) (13, E3) — NEW
- **What:** the marimo notebook team posted `df.jev()` — a pandas/polars method
  that bulk-generates new columns via Jev (bulk labeling inside notebooks), with
  a demo notebook on marimo.io. Their follow-up comment adds an adaptive-UI
  angle: Jev ranks the most useful tools for the code cell you're viewing
  (experimental ambient-palette branch of marimo-pets).
- **Numbers:** none published — described, not measured.
- **Evidence:** builder (marimo team) integration described on Threads (Sept 21);
  experimental branch; treat as lead-level until the notebook/repo is crawled.
- **Why it ranks #113:** two new shapes at once — bulk column enrichment as the
  dataframe-native version of bulk classification (#2/#17), and context-aware UI
  element selection, a genuinely new adaptive-interface slot.
- **Source:** https://www.threads.com/@marimo_io/post/Ddj3AOQD0M8

### 121. Voice-controlled Mac assistant (xtract.ai + charliejhills) (12, E4) — NEW
- **What:** a voice-driven Mac assistant on Jev: speaks commands, Jev decides
  the action and target mid-sentence — opens Notes mid-utterance, creates a
  note, opens Arc, searches Google, opens X, opens Photo Booth and takes a
  picture. charliejhills' X post shows the same build ("begins executing
  commands before the user finishes speaking").
- **Numbers:** none published — screen-recorded working demo.
- **Evidence:** builder demo with two independent sightings (xtract.ai IG reel,
  Sept 21; charliejhills X post via jevtracks, Sept 22); described, not measured.
- **Why it ranks #121:** the desktop counterpart to #20's voice browser control —
  Jev as the intent→action dispatcher inside an always-on voice agent, where
  the mid-sentence execution shows off the ~300ms decision loop against
  LLM-turn latencies.
- **Sources:** https://www.instagram.com/reel/Ddk2v9fIfWG/ · via
  https://jevtracks.com/

### 123. YC Indexor — NL search over 6,000+ startups (Aayan / @aaayandev) (13, E4) — NEW
- **What:** natural-language search over 6,000+ YC startups — query by color,
  niche, competitor, age, image, "any way"; Jev judges each company against the
  query.
- **Numbers:** sub-1-second search results, ~$0.002 per search; 90M tokens /
  $2.70 in total testing costs.
- **Evidence:** builder demo with self-reported figures (X via madewithjev.com,
  Sept 22); two days of build time claimed.
- **Why it ranks #123:** the same NL-search-over-inventory shape as #116
  (Zillow) and #77 (image search), now with a per-search price tag — $0.002
  per fuzzy query over a 6K-record corpus makes conversational search over
  structured catalogues a near-free feature.
- **Source:** via https://madewithjev.com/

### 129. Real-time 3D character animation control (john_bortotti, X) (12, E4) — NEW
- **What:** Jev orchestrates real-time 3D character animations by making ten
  simultaneous decisions per message — mouth, brows, eyes, cheeks, gaze, and
  body movements — generating unique expressions rather than selecting from
  presets.
- **Numbers:** 10 decisions/message — no latency figures published.
- **Evidence:** builder demo paraphrased via jevtracks.com's X crawl (Sept 23);
  second-hand, builder-claimed.
- **Why it ranks #129:** a new use-case type — parallel low-level expression
  parameters as typed questions, one call per animation frame-set. The
  many-parallel-decisions shape generalizes to NPC/game-agent control (cf.
  #55's game cluster).
- **Source:** via https://jevtracks.com/

### 131. SEO internal-link analyzer (tetsu_tetsu333, X) (12, E4) — NEW
- **What:** a Jev-based SEO tool analyzes internal links across a website and
  proposes new linking opportunities between articles, identifying the specific
  sentences where links should be added.
- **Numbers:** 100 articles analyzed for 78 yen (~$0.53) — builder-claimed,
  second-hand.
- **Evidence:** builder demo paraphrased via jevtracks.com's X crawl (Sept 23);
  no accuracy figures.
- **Why it ranks #131:** the first SEO-linking use case — "which sentence should
  link where" as a scored typed question over every sentence pair. The
  economics (sub-cent per article) make full-site link audits trivially cheap.
- **Source:** via https://jevtracks.com/

- **New (Sept 23 — midday):** the first-party version — Ian Nuttall's live free
  internal-linking tool (@iannuttall, X via madewithjev.com): Jev classifies
  and selects the links, works for up to 500 pages, outputs a CSV or JSON to
  pass to an LLM to implement (BYOK or pay $1 to use his key). The graduated
  shape: Jev *decides* the links, an LLM *implements* them.

### 132. Adaptive support-ticket follow-ups (peterramsing, X) (13, E4) — NEW
- **What:** Jev prompts users with follow-up questions when creating support
  tickets — designed to work quickly enough for live phone calls.
- **Numbers:** none — demo-described.
- **Evidence:** builder demo paraphrased via jevtracks.com's X crawl (Sept 23);
  second-hand, builder-claimed.
- **Why it ranks #132:** the inverse of the usual triage shape: instead of
  classifying an already-written ticket, Jev decides *what to ask next* to
  complete it — adaptive intake forms as a decision workload. The live-call
  latency requirement is exactly where Jev's 70–500ms matters.
- **Source:** via https://jevtracks.com/

### 133. neo4jev — Neo4j graph navigation with beam search (jexp) (12, E3) — NEW
- **What:** Jev navigates a Neo4j graph one hop at a time: at each node the
  outgoing relationships become a Choice (type, properties, target
  node label/properties), and a Noul ("has the goal been reached?") rides in
  the same call — one round-trip per hop regardless of question count. Top-k
  selection over the returned distributions implements beam search, ranked by
  sum of log-probabilities to avoid length bias.
- **Numbers:** none — demos run end to end against the live `companies2` graph;
  notebooks + Streamlit app included.
- **Evidence:** community builder project (GitHub, Sept 2026); the author's own
  honesty note: without a TypeSafe key the pipeline exercises stand-in answers
  that are explicitly labelled — "nothing is presented as TypeSafe output that
  did not come from TypeSafe."
- **Why it ranks #133:** the first graph-traversal use case — pathfinding as a
  typed Choice per hop, which generalizes to retrieval-over-knowledge-graphs
  and agent planning over explicit state spaces. The log-probability beam
  search is a clean, portable technique.
- **Source:** https://github.com/jexp/neo4jev

### 136. Expense-report bulk categorization (unnamed agent platform) (13, E3) — NEW
- **What:** an agent platform wired Jev into its LLM harness as a callable tool
  for search, approvals and context — the reported workload: expense reports
  categorized off the harness's judgment calls.
- **Numbers:** **2,000 expense reports categorized in 20 seconds for $0.05**
  (~$0.000025 each).
- **Evidence:** builder-claimed, second-hand via the yibie/awesome-jev index
  (Sept 23); the platform is unnamed in the index — treat as a lead until the
  builder's own post is located.
- **Why it ranks #136:** the first finance-ops bulk-classification datapoint —
  receipts and expense lines are exactly the high-volume, bounded-label work
  Jev was built for; pairs with #16/#75 (inbox triage) as the backoffice family.
- **Source:** via https://github.com/yibie/awesome-jev

### 137. ICP lead prioritization from 15K chatbot interactions (Bernardo Precht) (12, E3) — NEW
- **What:** the creator (86K followers) ran 15,000+ of his own ManyChat
  interactions through Jev — classifying profiles against his ICP rules with
  confidence scores to prioritize outreach. He now interacts with Jev 100% via
  his Grok bot; his honest framing: "useful, but too early to call
  transformational."
- **Numbers:** 15,000+ interactions classified; no accuracy figures published.
- **Evidence:** builder-described test with volume figures (Portuguese IG reel,
  Sept 23).
- **Why it ranks #137:** the third independent lead-scoring datapoint (#48,
  #119) — and the first scored against a real ICP over real prospect
  interactions rather than a scraped list. The confidence-scored outreach queue
  is the same economic shape as #16's email triage. Watch for conversion
  numbers.
- **Source:** https://www.instagram.com/reel/DdoJxbWBYKk/

### 138. Production GEO brand-visibility classifiers (Notra) (13, E3) — NEW
- **What:** a production GEO (AI-search-visibility) platform whose
  `NOTRA_JEV_CLASSIFIERS` flag routes brand-visibility classifiers off an LLM
  and onto Jev `Boolean` decisions at a 0.5 threshold.
- **Numbers:** targeting 300ms p50 per decision (a target, not yet a measured
  figure).
- **Evidence:** builder-described production integration, via the
  yibie/awesome-jev index (Sept 23); treat the latency as a target until
  measured.
- **Why it ranks #138:** the second production GEO datapoint (#75 Lurk) — the
  brand-visibility vertical is converging on Jev as the volume classifier
  behind a free or cheap tier. Same volume-classification economics as #17.
- **Source:** via https://github.com/yibie/awesome-jev

### 139. On-device model router for audio pipelines (Desert Ant Labs) (12, E3) — NEW
- **What:** a local-first audio demo app: drop in an audio file — an on-device
  "Ear" model detects the language, "Voz" transcribes, "Redact" strips PII —
  then Jev makes ~20 decisions in one call (in milliseconds) and *picks which
  of the on-device models to run next*. Voice memo → to-do list; meeting →
  redacted transcript; podcast → clips. No LLM in the loop at all.
- **Numbers:** ~20 decisions in one call, "milliseconds" — no measured latency
  figure published.
- **Evidence:** builder demo described on X via madewithjev.com (Sept 23);
  unmeasured.
- **Why it ranks #139:** a genuinely new shape — Jev as the *conductor* over
  on-device specialist models rather than as the end classifier — the
  privacy-preserving, offline-capable version of the router pattern (#23).
  Watch for measured end-to-end figures.
- **Source:** via https://madewithjev.com/

### 162. Jev-Rug-Checker — multi-chain EVM token screener (BradMyrick) (13, E4) — NEW
- **What:** a one-file, stdlib-only token screener for 22 EVM chains: resolves
  address/symbol via DexScreener, fetches free public data (DexScreener market
  + GoPlus honeypot/taxes/mint/LP-lock), asks Jev exactly one request —
  `risk_class` Choice, `narrative_vs_chain` Noul (shill detector), and
  `exit_liquidity` Score — then applies a two-layer policy in plain Python
  (hard-fail facts first, Jev layer second). A `--replay` flag re-applies the
  policy to cached judgments with zero API calls.
- **Numbers:** real run (COQ on Avalanche): VERDICT CAUTION, exit_liquidity
  0.58, and the tool *reports the probability split* (deep 0.5 / moderate
  0.42) instead of pretending certainty. Each run: one Jev call (~1k input
  tokens). Honest failures in the field notes: USDT gets AVOID because its
  owner can mint and hasn't renounced — the right rule for rugs, wrong for
  centrally-issued tokens — while Jev itself hedged (low_risk 0.6 /
  red_flags 0.37).
- **Evidence:** working tool with field notes and stated limitations (GitHub,
  MIT, Sept 17).
- **Why it ranks #162:** the cleanest "model judges, code decides" packaging in
  the catalogue — exact facts stay in deterministic code, the fuzzy half
  (narrative vs on-chain reality) goes to Jev, and cached judgments make
  re-tuning the policy free. Pairs with #25/#6 as the security cluster and
  #117 as the judgment-as-data pattern.
- **Source:** https://github.com/BradMyrick/Jev-Rug-Checker

### 163. MIND-news "usefulness" negative — the wrong-question lesson (@GoSailGlobal) (12, E4) — NEW
- **What:** @GoSailGlobal ran Jev against 3,448 articles from Microsoft's MIND
  news dataset — title and abstract only, no click data — asking whether each
  article seemed "useful." The correlation between "judged useful" and the
  article's actual click-through rate in its first 100 impressions came back
  **negative, ρ ≈ −0.129**.
- **Evidence:** independent test with metrics, reported via zeke/jev's research
  notes (Sept 24); the original X post is linked in the notes' sources.
- **Why it ranks #163:** the catalogue's sharpest *honest negative*: "Jev will
  happily give you a confident-sounding answer to a question the input can't
  actually answer." A wrong-question detector would have said "I can't know
  this from a title and abstract"; Jev's typed answers don't abstain that way
  unless you design for it. Pairs with the anti-patterns — the falsifier every
  fuzzy-judgment deployment must design around: test against a real outcome,
  not just a plausible question.
- **Source:** via https://github.com/zeke/jev · @GoSailGlobal on X

### 166. Structured-classification benchmark vs Claude (Charlie Automates) (13, E4) — NEW
- **What:** a creator benchmark comparing Jev against Claude on a structured
  classification task, with on-screen charts for speed, cost, and accuracy —
  Jev framed as returning a consistent yes/no, category, and score in ~0.4s.
- **Numbers:** cost Jev **$0.000069 vs $0.018800** for Claude (~272× cheaper);
  ~0.4s per decision. Accuracy figures were shown in charts but not captured
  from the video — flagged as a gap.
- **Evidence:** builder-measured benchmark via video charts (Facebook reel,
  Sept 24); self-reported, accuracy detail unverifiable from the capture.
- **Why it ranks #166:** another independent cost/latency datapoint in the
  classifier-replacement family (#7/#70) — the ~270× cost figure lands in the
  measured range of the Claude-benchmark cluster. Watch for a written
  version with the accuracy numbers.
- **Source:** https://www.facebook.com/reel/1533017398851021/

### 167. Review-verdict + ad mining from video reviews (Clip Fast / @aisarva_07) (13, E4) — NEW
- **What:** the "Clip Fast" app powered by Jev analyzes Telugu film 'The
  Paradise' reviews: fed three YouTube reviews, asked for the "second half"
  verdict — Jev returned the matching clips with scores (17%, 15%, 13%);
  then detected hidden ads inside the reviews (205 segments analyzed in
  seconds) and scanned 40 Instagram comments/reels for a 63% liked vs 37%
  flop audience split.
- **Numbers:** second-half verdict clips surfaced at 13–17% scores; 205
  ad-segments scanned "in seconds"; 40 comments classified into liked/flop.
- **Evidence:** working app demo with on-screen results (Instagram reel, Sept
  25); creator-described, promo-adjacent — metrics are self-reported.
- **Why it ranks #167:** a new workload shape — *review-verdict mining*: asking
  a typed question of a video corpus and getting back scored moments instead
  of a summary. The hidden-ad detection inside reviews is the monetization
  angle (media monitoring / influencer auditing). Pairs with #17 (ad
  analysis) as the earned-media cluster.
- **Source:** https://www.instagram.com/reel/Dds9LFKN4Og/

### 168. Voice + pointing control of a tldraw canvas (gaborishka/jev-canvas) (12, E4) — NEW
- **What:** draw on a tldraw canvas with voice + a pointing finger — MediaPipe
  hand tracking + Web Speech, and Jev (TypeSafe System One) decides action,
  target, and place at ~350ms per spoken word, through an INPUTS → JEV →
  CANVAS panel.
- **Numbers:** 18 spoken commands probed against the real API (probe script
  in-repo); unit tests cover policy, spans, and hand math. Per-command
  accuracy figures not published.
- **Evidence:** working integration with real-API probe + tests (GitHub,
  Sept 2026); inspired by Jack Cheng's X demo of the same setup.
- **Why it ranks #168:** the fourth Jev-in-the-loop UI modality after text
  (#15), desktop (#8), and voice-only (#20/#97) — voice *plus gesture* as a
  joint input stream, with the typed-questions file public as the reusable
  shape. Unmeasured accuracy keeps it in Tier 3.
- **Source:** https://github.com/gaborishka/jev-canvas

### 169. zhangcy122/OpenJev — open Jev-interface clone + Banking77 head-to-head (12, E4) — NEW
- **What:** another open-source Jev-interface re-implementation (typed
  probabilistic decision API over open LLMs with constrained-logprob
  calibration) — notable for its self-run OpenJevPro harness vs real Jev
  comparison on 36 Banking77 queries (30 in-domain + 6 out-of-scope).
- **Numbers:** in-domain: both 100% (29/29); out-of-scope rejection — the
  harness abstained 100%, Jev **0% (forced pick)**. ECE 0.089 (harness) vs
  0.284 (Jev); latency ~508ms cloud vs ~777ms Jev. Author's own harness and
  sample — self-serving, n=36.
- **Evidence:** builder-run comparison on a real dataset, open methodology
  (GitHub); the headline is the author's own, treat the numbers as a lead,
  not a finding.
- **Why it ranks #169:** the out-of-scope finding is the lesson, not the
  accuracy table: without an explicit abstain option, Jev's Choice forced-picks
  on inputs no label covers — the falsifier behind #12's abstain-to-review
  design and the anti-patterns list. Pairs with #53 (openjev) and #153 (the
  OSS re-implementation cluster).
- **Source:** https://github.com/zhangcy122/OpenJev

### 171. Flue agent support router via Cloudflare AI Gateway (matthewp/flue-jev-demo) (12, E4) — NEW
- **What:** a Flue (Cloudflare Durable-Object agent framework) demo routing
  with Jev through the Cloudflare AI Gateway binding — a support-routing
  agent where `route_with_jev` returns typed intent + urgency + probabilities
  before the chat model runs.
- **Numbers:** none published; unit tests mock the Worker binding (no paid
  calls) — the demo itself runs real `env.AI.run('typesafe/jev', …)` calls.
- **Evidence:** working builder demo (GitHub, Sept 2026); honest about mocked
  tests.
- **Why it ranks #171:** the agentic-framework-shaped version of the routing
  slot (#16) on Cloudflare's own stack — Jev as a binding-provider tool
  alongside the chat model, with AI Gateway logging for both. Thin on
  numbers, but a real integration of the decision-client pattern (#72 family).
- **Source:** https://github.com/matthewp/flue-jev-demo

### 175. JevBall — 22-player football decision sim with per-call telemetry (atarikcaliskan/jevball) (13, E4) — NEW
- **What:** a working 3D football simulation where all geometry/physics/
  candidate generation runs locally and Jev (via systemone) picks among typed
  actions (pass/shoot/move) for 22 players — real-time multi-agent decision
  traffic, not a benchmark.
- **Numbers:** live API test: **70 calls, 0 errors, 0 invalid answers, 366ms
  average, $0.0051 total** — about **$0.01 per match-minute** after payload
  optimization (down from $0.042/minute). Jev matched the local policy only
  **44% overall / 30% on-ball** — the author is explicit this is policy
  *divergence*, not quality validation.
- **Evidence:** builder-measured demo with real telemetry and an honest "not
  a benchmark" caveat — the closest thing the catalogue has to per-call
  economics for a multi-agent real-time Jev workload.
- **Why it ranks #175:** entertainment, but it supplies something rare: live
  per-call cost/latency figures for a 22-agent decision stream, plus payload
  tuning as a cost lever.
- **Source:** https://github.com/atarikcaliskan/jevball

### 181. Spam shoot-out: Jev vs TF-IDF logistic regression (via Pawel Swiridow) (13, E4) — NEW
- **What:** an early spam experiment — 5,733-email ham / spam / phishing test:
  Jev with no task-specific fine-tuning and no labeled examples in its
  requests vs a TF-IDF logistic-regression baseline trained on labeled
  messages.
- **Numbers:** Jev **98.6%** vs logistic regression **98.9%**. The author's
  honest framing: the criteria were refined after reviewing labeled
  examples, so "useful early evidence, not magic zero-data victory" — the
  trained baseline still wins.
- **Evidence:** cited with metrics in a technical engineering write-up
  (Medium, Sept 2026); the full experiment writeup is linked but was not
  independently audited by this watch.
- **Why it ranks #181:** the first datapoint where Jev nearly ties a *trained
  classical baseline* on its home turf — and the baseline still wins by
  0.3 pts. Pairs with #68's 19,500-email 98.3% zero-shot datapoint (a
  different run, different n): together they bound the "Jev vs
  train-on-your-labels" trade-off — no training pipeline, but no free lunch
  either.
- **Source:** https://medium.com/u11d-tech-blog/typesafe-ai-jev-structured-decision-models-explained-0b0407c1c235

### 176. JEV+OPUS 5.5 live site redesign — Jev decides, generator executes (@canmatrixx) (13, E4) — NEW
- **What:** a screen-recorded live demo: Jev decides what to keep/change
  across 8 sections of a site (cloudnest.io) — layout, fonts, colors — while
  Opus 5.5 executes the code changes; ~20 seconds per redesign.
- **Numbers:** none measured; the author explicitly frames it as a
  *controlled prototype, not a benchmark*, and notes both models can err.
- **Evidence:** working builder demo (Instagram, Sept 2026) with terminal +
  browser footage; the division of labor (Jev = typed design decisions,
  generator = execution) is the catalogue's established hybrid pattern (#19).
- **Why it ranks #176:** a concrete instantiation of the Jev-decides/LLM-
  executes architecture in a creative domain, with the builder's caveats on
  record.
- **Source:** https://www.instagram.com/reel/Ddt0EhuCriE/

### 177. LangChain model-router demo on Nebius — Jev middleware for ticket routing (arindam_1729m) (13, E4) — NEW
- **What:** a screen-recorded terminal demo of a LangChain router where Jev
  runs as middleware over Nebius-hosted models, classifying support tickets
  with confidence scores ("uv run route").
- **Numbers:** 8-ticket run, per-ticket latencies ~0.4–9.5s; no accuracy
  study.
- **Evidence:** working developer-advocate demo (Threads, Sept 2026); thin on
  numbers but a real run of the model-router pattern on the Nebius provider
  stack.
- **Why it ranks #177:** adds a fresh, reproducible routing demo to the
  model-router cluster — useful as a pattern instance, not as a benchmark.
- **Source:** https://www.threads.com/@arindam_1729m/post/Ddtf1uPj_Yq

### 196. AIMLAPI live measured-call walkthrough (12, E4) — NEW
- **What:** an AI/ML API provider's Jev explainer built around a real request
  through AI/ML API on Sept 25 — support-triage with three typed questions
  (urgency `Noul`, team `Choice`, churn-risk `Score`), full response
  published.
- **Numbers:** Jev processing **131ms** (0.95s end-to-end); routing came back
  "billing" at confidence 1.0, urgency 0.91. The honest caveat: in their
  routing test, **12 of 197 answers at ≥0.9 confidence were wrong**.
- **Evidence:** vendor-adjacent measured demo (API provider), single call plus
  a small routing test.
- **Why it ranks #196:** a reproducible worked example with real timings —
  and the 12/197 high-confidence misses are a second measured datapoint for
  #190's warning that confidence is a margin, not a correctness probability.
- **New (Sept 29 — morning):** the same page now carries the author's own
  cross-model tests beyond the walkthrough: on offensive-content moderation
  Jev had the **best F1 of six models (0.696)**; on rating answer quality it
  ranked worst (Spearman 0.39 vs 0.48–0.49 for Gemini/Opus 5.5); calibration
  ECE 0.032 on moderation / 0.096 on intent routing. Jev ran $19–$98 per 1M
  decisions — 84–85× less than Claude Opus 5.5 on classification, but roughly
  the same as cheap LLMs (GPT-6 Luna, DeepSeek V4 Flash) on the rating task.
  The honest-falsifier datapoint for the judge cluster: Jev leads moderation,
  loses answer-rating — know which judgment you're buying.
- **Source:** https://aimlapi.com/blog/what-is-jev

### 197. Sridhar Sampath (TowardsAI) hands-on (12, E4) — NEW
- **What:** an honest hands-on walkthrough of Jev on support tickets — the
  author measured his own Jev call (~470 input tokens) and states his
  cross-model figures as "arithmetic, not a measurement": ~$0.02/1,000 calls
  vs Claude Haiku 4.5 $0.87, Sonnet 5 $1.74, GPT-5.6 Terra/Gemini 3.1 Pro
  $1.90 at list prices.
- **Numbers:** Jev side measured; everything else is list-price arithmetic,
  explicitly labeled. Also restates the vendor's 67.8% agreement figure with
  the caveat that reference labels come from frontier models, and notes
  several RLCD characterizations rely on the company's own description.
- **Evidence:** honest builder write-up (TowardsAI, Sept ~23); measurement
  only on the Jev side.
- **Why it ranks #197:** the honesty pattern done right — labeled arithmetic
  instead of fabricated benchmarks — plus a list-price table useful for
  sizing any #16-family triage. Pairs with #190 (never dress arithmetic as
  evidence).
- **Source:** https://pub.towardsai.net/jev-by-typesafe-ai-a-hands-on-look-at-a-model-that-only-makes-decisions-7b22cf5f6608?gi=8635e86573be

### 200. Jev in Action — five interactive decision demos (jangya) (12, E3) — NEW
- **What:** an open repo of five interactive browser demos, each running live
  Jev calls (bring your own key) with an inspectable decision trace: hand-
  gesture object control, expense categorization, flight choice, appointment
  finding, and an LLM-vs-Jev tool-selection progressive-disclosure
  side-by-side.
- **Numbers:** none published — demo-described; the decision trace is the
  product, not a measurement.
- **Evidence:** working demo collection described by builder (GitHub).
- **Why it ranks #200:** a teaching collection exhibiting five Jev-native
  shapes in one place — the gesture-control demo is the catalogue's first
  embodied-input demo, and the tool-selection side-by-side makes the #4/#15
  pattern legible without benchmarks. Demos, not evidence — watch for the
  author adding measurements.
- **Source:** https://github.com/jangya/jev-in-action/blob/HEAD/README.md

### 215. Clinical coding review — ICD-10 Code Reviewer (Kartha Health) (13, E4) — NEW
- **What:** Kartha Health's TypeSafe Jev platform as an LLM-free approach to
  clinical coding: FHIR data pulled via their MCP server, each case converted
  into structured yes/no questions for Jev (status, specificity, MEAT, POA,
  missed conditions); the output report flags unsupported codes, missing
  specificity, combination codes, uncoded documented conditions with
  CC/MCC/HCC flags, and sequencing issues — each cited to source text.
- **Numbers:** 40 inpatient encounters, 146 Jev requests, 4.17M data units
  processed, $0.17 total, 195ms median latency; de-identification before
  sending to Jev; explicitly no LLM usage.
- **Evidence:** working demo described by builder (screen-recording carousel,
  Vietnamese FB group, Sept 29); no accuracy figures on the coding itself.
- **Why it ranks #215:** the catalogue's first healthcare use case — and the
  structured-field audit shape (one typed question per field, cited to source)
  generalizes to any regulated compliance-review workload. Stays Tier 3 until
  coding accuracy is measured.
- **Source:** https://www.facebook.com/groups/vietnam.digital.health.network/permalink/1670029491374604/

### 221. Real-time fruit quality inspection on a conveyor line — "Jev-Omni" lemon demo (brochbuilds) (12, E3) — NEW
- **What:** a screen-recorded demo — lemons roll down a green roller conveyor while
  the system labels each fruit Good / Blemished / Moldy in real time, with confidence
  percentages (e.g., 98% Moldy) and on-screen counters (32 inspected shown, broken
  down by class plus Unverified). The presenter says each lemon is checked three
  times over and the system runs locally on a MacBook; his framing: "the exact use
  case Jev was built for."
- **Numbers:** on-screen counters only (32 inspected in the visible run, three
  checks per fruit); no accuracy figures on the grading itself.
- **Evidence:** working demo described by the builder (Facebook reel, Sept 29; a
  2-follower account, promo framing — "comment JEV for a setup guide"). The Jev
  call itself is never shown, so Jev's exact role in the pipeline is
  creator-described only, and "runs locally" sits uneasily with a cloud decision
  API — flagged, not resolved.
- **Why it ranks #221:** the first physical-manufacturing QA demo in the catalogue —
  vision-state → typed decision at conveyor line speed is the embodied version of
  #2's triage shape. Stays Tier 3 until grading accuracy and Jev's actual role are
  measured; watch for a second build in this shape.
- **Source:** https://www.facebook.com/reel/1714830646273099/

## Tier 4 — demos and proposals, unproven (score ≤11)

### 204. Live crypto market-state → typed-decision paper-trading loop (zzsong1023/jev-market-reflex) (11, E3) — NEW
- **What:** live Kraken public WebSocket → bounded market features → one Jev
  request with three Choice questions (BUY / SELL / HOLD + probabilities) →
  deterministic risk gate + paper execution → live browser visualization over
  SSE; the recorded replay runs with no API key, labeled "RECORDED LIVE RUN ·
  PAPER TRADING."
- **Numbers:** one observed Sept 19 run (`jev-latest`, live Kraken feed): 21
  Jev calls → 63 typed decisions, 0 failures, 0 missed cycles; mean latency
  199 ms, p95 332 ms. The author's own caveat: one local run, not a
  benchmark; no trading performance/PnL measured; no real money.
- **Evidence:** working builder demo with measured latency (GitHub, Sept 2026).
- **Why it ranks #204:** the first live-feed market-state → typed-decision loop
  in the catalogue with measured numbers — the "Jev as the fast signal layer on
  a live feed" shape. Distinct from #28 (per-block market-making): this is
  features → three-choice signal + deterministic risk rules, paper only. Watch
  for anyone measuring signal quality instead of latency.
- **Source:** https://github.com/zzsong1023/jev-market-reflex

### 205. ENEM essay rubric-grading notebook (mgarlabx/Jev-Enem) (10, E3) — NEW
- **What:** a didactic Portuguese notebook grading Brazil's ENEM essay rubric:
  five `Score` questions (one per competency, six ordered levels condensed from
  the official bands) over the exam prompt, theme, and motivator texts; level→
  point conversion done in deterministic Python; confidence below 0.75 in any
  competency → flagged for human correction.
- **Numbers:** none — two fictitious essays (strong/weak) only; the author
  explicitly warns Jev's precision is currently higher for English and labels
  the repo experimental.
- **Evidence:** builder demo (GitHub, Sept 20).
- **Why it ranks #205:** a new domain demo (Portuguese rubric grading) that
  implements the #3 confidence-gated routing policy explicitly — grade where
  confident, human where not. Unmeasured; watch for an accuracy audit against
  real human graders.
- **Source:** https://github.com/mgarlabx/Jev-Enem

### 206. AI Director for a roleplay game — shadow-wired decision lanes (mojomast/velvetrp) (9, E2) — NEW
- **What:** a Jev integration design for Velvet (roleplay engine): an optional
  System One lane with server-issued candidate sets — AI Director selection,
  adventure selection, room routing, narration/receipt verification, memory
  reranking, cost/quality routing, guardrails — each with per-lane confidence
  gating, a Platt calibration module, promotion-gate records, and an immutable
  decision-record sidecar; every lane wires in shadow (record-only) first;
  everything stays disabled by default with a deterministic fallback for each
  lane. The controlling invariant: "the model can propose what happens next.
  It never decides what became true."
- **Numbers:** gate thresholds from sweeps (adventure-selection passes only at
  a 0.40 action threshold); the wire schema was exercised live against the
  TypeSafe API — but per the doc itself, all behavior beyond shadow mode is
  future work. No behavioral measurements yet.
- **Evidence:** credible proposal with engineering rigor (GitHub design doc +
  scaffolding, Sept 2026); graded as proposal because nothing is active.
- **Why it ranks #206:** the most carefully architected game-AI Jev design yet
  — closed-candidate Choice is a strong fit for a game director — and the
  shadow-first promotion process is the catalogue's best-articulated answer to
  #68's deployment rule. Watch the shadow-mode decision records.
- **Source:** https://github.com/mojomast/velvetrp/blob/HEAD/docs/jev-integration.md

### 48. Business routing proposals (11, E2) — updated
- **What:** lead qualification, product routing, fleet-maintenance timing, validating
  construction summaries against source records.
- **New (Sept 18 — midday):** @brochbuilds' "Jev demos: Part 2" (Instagram) shows a
  business tool demo claiming 700 leads + sales messages assessed in 40 seconds —
  a demo claim, unverified; the same video shows a JevPilot Tesla-style sim and a
  Subway Surfers Jev game demo.
- **New (Sept 19 — morning):** numbers attached — Romàn's GojiberryAI demo (X) shows 700 high-intent leads + personalized messages scored in 40 seconds for $0.09, with lead-message mismatch detection and per-message confidence; builder/vendor-adjacent numbers, unverified, but the per-decision economics are now visible.
- **New (Sept 22 — evening):** a second lead-scoring datapoint — Kevin Chen
  (@kevi.nbuildsai, IG reel, 28K likes) demoed Jev scoring Google Maps-scraped
  business descriptions for buying likelihood, with on-screen figures (Coffee
  Shop #1: 92%, two bakeries: 78% and 65%); promo framing for twin.so,
  creator-described. Two independent lead-scoring demos now — still waiting on
  a conversion-rate audit against actual outreach outcomes.
- **Evidence:** proposals plus one unverified demo claim with on-screen economics; generally waiting for access.
- **Why it ranks #48:** unproven — watch for these to graduate as access widens.
- **Source:** https://www.instagram.com/reel/Ddb2wLbjAan/

### 49. DSPy decision-step swap (11, E2)
- **What:** a TypeSafe employee measured the end-to-end effect of swapping one decision
  step in a DSPy pipeline to Jev.
- **Numbers:** 15.9% faster, 30.1% cheaper across three test cases — replacing one
  component does not scale the whole workflow by the same multiple.
- **Evidence:** vendor-adjacent (employee) self-measurement, small sample; reported via
  the Jev File.
- **Why it ranks #49:** composability data point — Jev as a drop-in decision step inside
  existing frameworks.
- **Source:** https://dailytexasnews.com/typesafe-jev-system-one-model-claims-evals-independent-tests-daily_texas/

- **New (Sept 22 — morning):** a community one-decorator DSPy integration —
  typesafeainate/dspy-typesafeify (61★): `@typesafeify` routes a Signature's
  typed outputs (bool, Literal, score fields) to Jev's Noul/Choice/Score in one
  request while freeform text stays with the generative LM; ships a controlled
  before/after benchmark example. Unmeasured so far — watch the example results.

### 50. Client-side content-filtering extensions (11, E3) — NEW
- **What:** two launch-weekend builds reported in Kevin Magnan's "7 wild JEV builds in 48 hours" roundup (Sept 18): Marcel Pociot's browser extension hiding X posts from plain-English rules, and Neddes' "Sloppy Jev" Chrome extension blurring ads and spam across sites.
- **Numbers:** none — described, not measured.
- **Evidence:** builder demos described second-hand in a roundup reel; no direct sources or metrics yet.
- **Why it ranks #50:** plain-English *personalized* content policy compiled to per-item judgments is a new shape (policy from the user, judgment from Jev) — watch for either builder to publish.
- **Source:** https://www.instagram.com/reel/DdbjduSDrxT/

- **New (Sept 19 — midday):** acorn181's Semantic Bookmark — a Chrome extension that
  files bookmarks by rules you write — a third client-side filtering build in the
  same shape.

- **New (Sept 20 — morning):** first-party confirmation — Marcel Pociot posted
  his extension on X himself (@marcelpociot): a browser extension hiding/
  collapsing X posts from plain-English rules, "so fast that it's not noticeable
  and insanely cheap"; still no published metrics — numbers awaited.

### 51. Supervisor over Codex workers (thruwire/foreman) (11, E3) — NEW
- **What:** foreman — Jev as supervisor over Codex coding workers; a software-factory architecture experiment.
- **Numbers:** none reported.
- **Evidence:** community project listed in the awesome-jev-usecases flagship table (updated Sept 18); self-described experiment.
- **Why it ranks #51:** the supervision/routing pattern applied to coding-worker fleets — unmeasured so far.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 52. Smart-home automation (AboveColin/HA-Jev) (10, E3) — NEW
- **What:** Home Assistant automations (and semantic spreadsheet formatting) driven by
  Jev judgments.
- **Numbers:** none reported.
- **Evidence:** community project listed by its builder, via the awesome-jev-usecases
  community index (updated Sept 18); no metrics, work-in-progress.
- **Why it ranks #52:** the first smart-home use case — "should the lights do X?"
  is exactly a typed-judgment slot — but it needs any measurement to graduate.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

### 53. Open re-implementation (TheoLeeCJ/openjev) (10, E3) — NEW
- **What:** open re-implementation of Jev's interface on a frozen 4B model by reading option logits; runs in the browser, no waitlist.
- **Numbers:** none reported.
- **Evidence:** community project, via the awesome-jev-usecases index (updated Sept 18).
- **Why it ranks #53:** not a use case — but proof the decision-layer interface travels beyond TypeSafe's weights, and a waitlist-bypass route for builders. Track whether logit-reading reproduces the calibration story.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases

- **New (Sept 19 — midday):** more open clones — picocreator's SimpleJev.ai (fully
  open-source library that "Jev-ifies" any Hugging Face model, live at FeatherlessAI),
  Jared Palmer's kev (a tiny Jev-like model on Qwen2.5-0.5B that trains and runs on a
  MacBook, ~160ms), and ikermoel's "Open Alternative to Jev" (typed, calibrated
  decisions from open-weights models on your own GPU).

- **New (Sept 19 — evening):** one more open clone in openchamber.dev's launch-repo
  table — ekzhang/openjev-sglang, the same logit-reading approach served through
  SGLang (listed, not yet crawled).

- **New (Sept 20 — morning):** Laya (ConvAI Innovations / Nandakishor
  Mukkunnoth) — a researcher who says he published the non-autoregressive
  decision concept in March 2025 shipped an Apache-2.0 reply: three checkpoints
  (421M English, 322M multilingual covering 100+ languages, typed-decisions
  model), `pip install laya`. Self-reported: 32.8ms/question on one T4 GPU,
  7.2ms batched, vs third-party Jev latency 236–276ms; 0.766 on the shared
  typed-decisions set vs Jev's published 0.727 — but the author never ran Jev
  himself (Jev figures from other benchmark runs), and his model card warns Laya
  ships overconfident until its temperature is refit. An independent 78-case
  test (M4 Pro Mac mini, ayourtch-llm.github.io) found Jev 0.974 vs Laya 0.590 —
  the speed claim survives, the accuracy claim does not. Paras Chopra's 15-eval
  comparison (see #94) included "Laya English" as a sub-1B baseline alongside
  Jev. The open-decision-model space now has real competition to watch.

- **New (Sept 22 — midday):** Cua open-sourced CUA-S1-FORMS (706k params, one 50ms
  pass for form-fill; github.com/trycua/cua) — the first open-source
  purpose-built System-One-style model family from an agent platform, a
  weight-level alternative to the logit-reading clones. And the Laya-vs-Jev race
  is becoming spectator content: Marc Kaz's IG reel "A LOCAL 421M MODEL JUST ATE
  CLOUD JEV ON SPEED" (Laya vs Jev on a Snake game) and @sung.kim.mw's "DINO
  ARENA" (local Mac Laya vs Jev API on the Chrome dino game, Sept 22) — both
  unmeasured, but watch them for any controlled rematch.

- **New (Sept 22 — morning):** Jared Palmer's kev stepped up — Kev-0.8B
  (MacBook-sized), Kev-4B, Kev-9B (Apache-2.0, 1,638★, pushed Sept 21).
  Author-published benchmarks vs hosted Jev on new-source sets: Kev-9B
  0.812/0.837 accuracy vs Jev 0.857; Brier 0.291/0.243 vs Jev 0.211 — ~4.5
  points behind, with the author noting it is not a controlled comparison
  (Jev's training sets are unknown). Separately, a JevBench V1.2.6 leaderboard
  (via @ariya114, Threads, Sept 21) scores composite geometric mean
  (Intelligence, Calibration, Speed, Cost — 25% each): Jev 1.13.0 leads at
  75.4, open rebuilds close behind (SemIf/Qwen3.5-4B 74.7, djeV 74.3),
  instruction models well back (GPT-5.6 Luna low 66.2, Gemini 3.1 Flash-Lite
  60.9). The composite frame confirms Jev's edge is *total*
  (speed+cost+calibration), not raw intelligence — consistent with the
  catalogue's #68/#93/#114 falsifiers.

- **New (Sept 22 — evening):** two more open-clone datapoints — NFT_Chen's
  AgentJev (0.6B open-source decision model, via jevtracks.com's X crawl):
  79.25% accuracy on Typed Decisions, 64 candidates in a single forward pass
  (92.4% computational reduction), 68ms decisions. And @dev.jimmy.ai's
  Laya-vs-Jev benchmark charts (Threads, Sept 22): Laya 0.766 vs Jev 1.13.0
  0.727 on typed-decisions (2,000 decisions), 0.950 vs 0.910 AG News, 0.595 vs
  0.480 DAIR Emotion; 7.8× faster inference (32.8ms vs 236–276ms), 3× better
  calibration (ECE 0.081 vs 0.246), 45/51 languages usable, $0 self-hosted —
  with the author's own caveats: base checkpoints poor without specialization,
  large choice sets weak, probabilities need calibration. Jev's official
  typed-decisions score (0.727) anchors the author-reported Laya edge at
  ~4 points; independent re-runs still disagree (see the 78-case 0.974 vs
  0.590 result above).

- **New (Sept 23 — morning):** two more — a snake-game Laya-vs-Jev demo
  (NFT_Chen, X via jevtracks, second-hand): Laya 86.5 decisions/s at ~9ms, game
  score 46 vs Jev 3.2 decisions/s, score 1 (30s runs) — expected for a local
  model vs a cloud API, not a capability verdict; and mmastrac's "djev"
  (DiffusionGemma-as-Jev): near-real-time vision detection on a mobile phone
  via the model's native vision tower — the open-clone wave reaching
  Jev-shaped interfaces on vision backbones.
  (via https://jevtracks.com/)

- **New (Sept 23 — evening):** three additions. (1) **The out-of-sample test:**
  4esv/jev-eval measured Jev 1.13.0 against open-jev, Kev-0.8B and Laya on five
  tasks (see new entry #147): on CLINC150 (151 options, in no clone's training
  list) Jev 0.897 vs open-jev 0.610 — the clones' in-sample wins (Banking77,
  SST-5) don't travel. Kev runs locally through Jev's own `/v1/systemone`
  contract, a real drop-in. (2) **A hosted competitor:** Upstage's
  Solar Mini4-Jev via OpenRouter — Sung Kim ran 400 cases / 447 answer fields
  with a Grok 4.6 Judge rubric: Solar Mini4 98.4% (440/447) vs Jev 1.13.0
  94.2% (421/447), Solar winning 25 of 31 disagreements, but ~1.2s vs ~0.4s
  per call (3× slower). Vendor-adjacent (Upstage), not independently
  reproduced — watch for reruns.
  (https://www.threads.com/@hunkimup/post/DdkePt6E2iJ, Sept 22 00:42 UTC)
  (3) **Leads:** madewithjev.com's "Is Jev open source?" guide names a new
  open decision model, **von** (<15ms), alongside Laya 421M and kev-on-MacBook
  — a Persian reel (@erfan_iranshad) demoed von driving a Jev-built agent
  site, but no weights URL surfaced yet. Tony Dinh's Laya-vs-Jev Tetris:
  Jev won all 3 rounds (second-hand via @stridejosh.ai reel); one team
  reportedly retrained Laya on 140 labels to beat Jev on their own test —
  a weak anecdote, but the retrain-to-task pattern is the honest open-model
  play (see #114).

- **New (Sept 24 — morning):** a Data Science Academy explainer reel cites a
  Laya-vs-Jev comparison: Laya ~33ms vs Jev ~236–276ms latency; Laya 76.6% vs
  Jev 72.7% on a "typed-decisions" benchmark; Jev 87% vs Laya 42.5% on
  banking77 (ballpark-consistent with #147's 0.780 vs 0.370 but not identical —
  likely a different run or rounding). Second-hand; the original benchmark is
  not identified, and Laya's own docs warn the conditions aren't directly
  comparable. Still the second independent channel carrying a Laya-accuracy-edge
  claim on at least one suite.
  (https://www.instagram.com/reel/Ddq7bFpt9kq/, Sept 24)

### 54. Citation grounding (Noul over claims) (10, E2) — NEW
- **What:** index-listed use-case type: does the quoted context support, contradict, or stay silent on a claim? — one Noul per citation.
- **Numbers:** none — listed as a type, no linked repo yet.
- **Evidence:** mentioned in the awesome-jev-usecases index (updated Sept 18); no source project.
- **Why it ranks #54:** claim-verification (the jev_verify primitive) pointed at RAG citations is the highest-value unbuilt slot in the index's "Search, Reranking & RAG" family — but unattached to a build.
- **New (Sept 19 — morning):** now attached to a build — Isaac Flath's citation-checking Jev (question + answer + cited text → agree/disagree/irrelevant) caught a planted wrong number in his Document Lab write-up; see new #58. Graduated from unbuilt to implemented.
- **Source:** https://github.com/anandi1989/awesome-jev-usecases · https://isaacflath.com/writing/six-things-i-tried-with-jev

### 55. Real-time in-loop control (Doom demo + community builds) (8, E1) — updated
- **What:** founder's launch demo: ~10 model decisions/sec for ~$7/hr.
- **New (Sept 18):** joshlarsen/jev-t-rex-runner — Chrome T-Rex runner played by Jev:
  each obstacle gets one semantic maneuver Choice (`jump`/`duck`/`keep_running`) plus
  a `short`/`full` jump-profile Choice; browser keeps frame timing, collision geometry
  and input execution. (Built atop an old T-Rex Runner repo; no metrics reported.)
  Sits alongside the Sept 17 builds: fhshaik/typesafe-mario (NES emulator RAM →
  object-centric JSON → Jev Choice controller loop, live telemetry dashboard) and a
  Facebook demo of two Jev agents playing Command & Conquer: Red Alert 2 against each
  other in real time.
- **New (Sept 18 — midday):** @sliven0722 (Threads) shows developer Max Blade's Jev
  playing Subway Surfers with real-time lane/action probabilities (LEFT/MID/RIGHT,
  jump/run/slide) and the chosen key per step; brochbuilds' "Jev demos: Part 2"
  adds a JevPilot Tesla-style self-driving simulator loop.
- **Evidence:** vendor marketing demo; community builds are unmeasured demos.
- **Why it ranks #55:** proves sub-second decisions can sit *inside* the software loop —
  as evidence of production value it's a stunt; as a feasibility marker it matters.
- **Sources:** https://github.com/fhshaik/typesafe-mario ·
  https://www.facebook.com/reel/2147598466152328/ ·
  https://github.com/joshlarsen/jev-t-rex-runner ·
  https://www.threads.com/@sliven0722/post/DdbD2zbEan6

- **New (Sept 18 — evening):** Chen Jing (cf. #13 commit-risk screening) posted a 45-second screen recording of Jev playing Tetris through three stacked layers — sensory, motor, judgment — with the score counter rising live. A demo, unmeasured, but the layered-judgment architecture is a step beyond the raw game loops.

- **New (Sept 22 — midday):** @stvan.p's Jev plays Snake — Jev as a high-level
  strategist on a 24×24 grid (score 110→250 in the recording) with live
  latency stats on screen (jevsnake.stevanuspangau.dev, Threads, Sept 22).
  Unmeasured demo, in the game-loops family.

- **New (Sept 22 — evening):** stvan.p's own follow-up post adds the Snake
  economics — $0.000050 per decision (live token/cost counters on screen).
  And @nate_pacyga (IG, Sept 22) built an Advance-Wars-style war game on Jev
  at ~$0.70 total API cost, with the blog/demo graphs comparing Jev to other
  models and the war-game interface running a live decision tree as the agent
  shoots. Both builder-claimed; the war game is the first turn-based
  strategy entry in this family.

- **New (Sept 25 — morning):** @nate_pacyga's own Threads post (Sept 24) shows
  the Advance-Wars-style game running Jev-vs-Jev: both sides act autonomously —
  moving units, battling, capturing cities — while he watches; open-sourcing
  promised, with multiplayeragent.com floated as a venue for AI-vs-AI games.
  Still a stunt family, but Jev-vs-Jev autonomy is the first "agentic
  entertainment" shape in the catalogue. https://www.threads.com/@nate_pacyga/post/DdrkiHyjPsm

- **New (Sept 19 — midday):** turn-based additions — Milan Boers' Jev plays Pokémon
  (Pokémon Red, one typed turn at a time), smilior's Kiru Hai Coach (mahjong
  tile-discard coach, in Japanese), Tony Dinh's Jev vs LLMs Tetris (0.3s/decision,
  357 pieces, 134 lines in 2 minutes), and Ably Labs' Jev Pong (the ball moves one
  step per model decision, Jev against the LLMs).

- **New (Sept 24 — evening):** @DanFrmSpace's Jev plays Pokémon *Showdown* — up
  to 5 parallel battles, Jev picking every move and switch itself: 41 completed
  games on 2.6M+ input tokens for ~$0.11 total inference cost. Builder-reported
  cost figures via zeke/jev's research notes (Sept 24); no win-rate reported —
  a cost datapoint for decision-per-step game loops, not a skill measurement.
  Source: via https://github.com/zeke/jev

### 56. High-cardinality choices (Wikipedia race demo) (8, E1)
- **What:** many-way choice test across Wikipedia-scale option sets.
- **New (Sept 18):** Wizhill05/typesafe-image-diffusion — diffusion-style pixel art
  generated out of Jev as a classifier: 256 parallel pixel questions + refinement
  passes, served via FastAPI (no metrics; a novelty, but a parallel-output *scale*
  datapoint — hundreds of outputs in one pass, matching Jev's headline claim).
- **Evidence:** vendor demo; community build is an unmeasured novelty demo.
- **Why it ranks #56:** shows off parallel outputs; no real workload attached yet.
- **Source:** https://github.com/Wizhill05/typesafe-image-diffusion

- **New (Sept 19 — midday):** more novelties — Anshu Chimala's Jevinci (paintings
  made from Jev's pixel probabilities), Paul's jev-piano (piano improvisation from
  typed questions), and Stephen Wu's "Can Jev steer music?" (Jev picks the plan;
  code renders the sheet music, audio and MIDI).

### 66. Jevify — agent skill for finding Jev-able decisions (Alex Volkov) (11, E3) — NEW
- **What:** an open-source agent skill that scans an application or codebase for
  decision tasks Jev could handle, helps design the Jev questions, and builds
  tests comparing latency, cost, and quality against existing approaches.
- **Numbers:** none — tooling, not a workload.
- **Evidence:** builder project described by ThursdAI creator Alex Volkov
  (Substack, Sept 18).
- **New (Sept 22 — midday):** TypeSafe is testing first-party question-authoring
  tooling — a console "semantic lints" layer that warns when a question doesn't
  fit its answer type (Jane Manchun Wong surfaced an X screenshot showing "Not a
  yes/no question" beside a Noul-typed object with an open-ended instruction,
  plus an "Enable TypeSafe semantic lints" checkbox; via RuntimeWire, Sept 21).
  The malformed-question problem slopcheck (#88) hit in the wild is getting an
  official lint.

- **Why it ranks #66:** migration tooling — the "which of my LLM calls are
  decisions?" scanner is exactly what an enterprise needs to find the Jev-shaped
  holes in an existing codebase; watch for adoption signals.
- **Source:** https://patmcguinness.substack.com/p/jev-makes-fast-and-cheap-decisions

- **New (Sept 19 — midday):** the official TypeSafe agent skill now exists (215 ⭐,
  installable with one command) — the canonical answer to this entry. Sibling
  tooling: Drew Breunig's "Building with Jev" skill (86 ⭐), Basit Mustafa's Augustus
  (designing systems around Jev's judgments), Akash Priyadarshi's jev-superpowers
  (skills framework with Jev gates), and Tony Powell's jev-oxlint (agent skill →
  Oxlint plugin that asks Jev).

### 67. X timeline classifier extension (Alex Volkov) (10, E2) — NEW
- **What:** a browser extension classifying the author's X timeline with Jev.
- **Numbers:** none published.
- **Evidence:** builder project described second-hand (Substack, Sept 18); the
  closest sibling is Marcel Pociot's X-hide extension (#50) — no accuracy data yet.
- **Why it ranks #67:** personal feed triage is the same shape as Flath's news
  ranking (#58.2) with public data — needs a measurement to graduate.
- **Source:** https://patmcguinness.substack.com/p/jev-makes-fast-and-cheap-decisions

### 79. Malicious-code scanner (is-malicious) (11, E3) — NEW
- **What:** a codebase scanner that helps you not run malicious code — Jev judging
  code for maliciousness before execution.
- **Evidence:** builder work-in-progress (via madewithjev.com, Sept 19); unmeasured.
- **Why it ranks #79:** supply-chain code safety is a new slot (cf. Kostas's Sept 19
  security-engineering roadmap post — threat hunting, detection engineering,
  incident response — which names the same shape); needs true/false-positive numbers
  before it means anything.
- **Source:** via https://madewithjev.com/

### 80. Systematic-review data extraction (jev-reviewer) (11, E3) — NEW
- **What:** systematic-review data extraction from clinical trial reports — every
  answer a verbatim quote.
- **Evidence:** builder project described (via madewithjev.com, Sept 19); no
  metrics. The "verbatim quote" constraint is the anti-hallucination shape that
  fits Jev.
- **Why it ranks #80:** a genuinely new vertical — evidence synthesis for clinical
  research — waiting on an extraction-accuracy audit.
- **Source:** via https://madewithjev.com/

### 81. Shell autosuggestion ranking (guesswork) (10, E3) — NEW
- **What:** fish-style zsh autosuggestions ranked by Jev — per-keystroke shell-intent
  ranking.
- **Evidence:** builder work-in-progress (via madewithjev.com, Sept 19).
- **Why it ranks #81:** per-keystroke judgment is the most latency-sensitive slot
  yet — needs latency measurements to mean anything.
- **Source:** via https://madewithjev.com/

### 82. BDD test synthesis from feature files (jevcumber) (10, E3) — NEW
- **What:** Cucumber tests generated from the .feature file alone — no step
  definitions, Jev judging/deriving the mappings.
- **Evidence:** builder project described (via madewithjev.com, Sept 19);
  unmeasured.
- **Why it ranks #82:** a new shape — test synthesis from spec text — but
  unmeasured.
- **Source:** via https://madewithjev.com/

### 83. Fuzzy code linter (patdown) (10, E3) — NEW
- **What:** a "fuzzy linter" — Jev gives your code an "ocular patdown" for issues
  beyond deterministic lint rules.
- **Evidence:** builder work-in-progress (via madewithjev.com, Sept 19).
- **Why it ranks #83:** fuzzy judgment over deterministic lint is the same shape as
  Hunch (#29) — watch for convergence.
- **Source:** via https://madewithjev.com/

- **New (Sept 22 — evening):** a second fuzzy-linting datapoint — OhansEmmanuel's
  X video (111K views) shows Jev scoring coding-agent outputs against rules
  traditional linters *cannot* enforce, feeding real-time feedback so agents
  correct violations immediately. Described demo, no metrics; converging with
  the oxlint agent-skill builds noted Sept 19.

- **New (Sept 23 — evening):** a third datapoint — **Jlint**, natural-language
  visual linting on typesafe.ai (via a @chatgptricks roundup carousel): ask for
  the look you want in plain language, Jev-style scoring decides
  pass/fail per element. Promo-frame, no metrics — lead only, watch for a
  builder-authored writeup.

### 84. Private voice diary (capture) (10, E3) — NEW
- **What:** a private Mac voice diary — local transcription, Jev sorting, a Notion
  library.
- **Evidence:** builder project described (via madewithjev.com, Sept 19);
  unmeasured.
- **Why it ranks #84:** a new shape — voice-note triage/routing — but unmeasured.
- **Source:** via https://madewithjev.com/

### 85. Listening-port safety utility (Port Cleanup) (10, E3) — NEW
- **What:** a macOS utility that tells you which listening ports are safe to stop.
- **Evidence:** builder project described (via madewithjev.com, Sept 19);
  unmeasured.
- **Why it ranks #85:** system-management judgment is a new slot — but "safe to stop"
  needs a false-positive audit before anyone trusts it.
- **Source:** via https://madewithjev.com/

### 86. LinkedIn feed value-blur (LinkedIn NoSlop) (9, E3) — NEW
- **What:** a Chrome extension that blurs low-value LinkedIn posts.
- **Evidence:** builder project described (via madewithjev.com, Sept 19);
  unmeasured.
- **New (Sept 22 — midday):** a second LinkedIn-slop build — Akash Ingole's "Jev Slop
  Flag" Chrome extension labels LinkedIn posts in real time with green
  ("NOT FLAGGED AS AI SLOP") / red ("LIKELY AI SLOP") banners (IG demo, Sept 22);
  no accuracy numbers published — demo-only so far. A sibling to #60's detector,
  pointed at a single feed.

- **Why it ranks #86:** consumer-side sibling of the X-timeline classifier (#67) —
  needs an accuracy read.
- **Source:** via https://madewithjev.com/

### 87. LinkedIn recruiting agent (Jev Recruiter) (9, E3) — NEW
- **What:** a LinkedIn recruiting agent that browses profiles and saves the
  evidence.
- **Evidence:** builder project described (via madewithjev.com, Sept 19);
  unmeasured.
- **Why it ranks #87:** a new vertical — recruiting automation — waiting on any
  measurement.
- **Source:** via https://madewithjev.com/

### 91. Typewriter — live judgments as you type (Steve Krouse) (10, E3) — NEW
- **What:** "Live typing" demo — 16 judgments updated as you type, per-keystroke
  decision surfaces in the UI.
- **Evidence:** builder demo described second-hand (via marktechpost, Sept 19);
  no accuracy read; tryable.
- **Why it ranks #91:** per-keystroke judgment is the most interactive UI slot yet
  — needs a latency/accuracy measurement to graduate.
- **Source:** https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/

### 92. Decision-to-UI component selection (json-render demo, Chris Tate) (10, E3) — NEW
- **What:** AI picks which predefined UI components to use and how to arrange them
  (developers control the building blocks, properties, actions, design system) —
  "Jev Can Now Turn AI Decisions Into UI in Milliseconds."
- **Numbers:** demo columns (train-ticket card, sign-in form, revenue dashboard):
  the Jev column renders in ~0.86–0.94s vs ~1.5–3.7s for the generative JSON
  alternatives.
- **Evidence:** builder demo with on-screen timing (Instagram, Sept 19, via
  @gogreen_ai); mockup-style, promo-adjacent.
- **Why it ranks #92:** a new use-case type — component selection for UI
  composition, keeping the design system in code — but unmeasured on real
  workloads.
- **Source:** https://www.instagram.com/reel/DdeCIFxoZgL/

### 99. Browser test-automation missions (j33.ai) (11, E3) — NEW
- **What:** screen-recorded video walkthrough of six automated test missions on an
  Acme Corporation operations-portal React app: a reasoning model authored the
  test missions, selectors, and checks; Jev executes the browser missions.
- **Numbers:** none reported — working demo, unmeasured; the summary seen was
  truncated mid-description.
- **Evidence:** builder demo (Instagram carousel, Sept 20); partial information.
- **Why it ranks #99:** browser QA/test-mission automation is a new slot adjacent
  to #22 (browser agents) — but the mission/success criteria need a write-up or
  full video to evaluate.
- **Source:** https://www.instagram.com/p/DdgkKr0j1yk/

### 100. let-jev-speak — tricking Jev into talking, word by word (8, E3) — NEW
- **What:** coaxes free-text answers out of the classification endpoint by decoding
  one word at a time: each word is a separate `Choice` over a vocabulary, looping
  the model's own output back as prefix. One `Choice` call routes the question to
  one of 28 domain packs and decoding runs over that pack's words (there's even a
  Telegram bot: t.me/let_jev_speak_bot).
- **Numbers:** routing 60/60 on a labelled set; domain vocabulary roughly doubles
  answer quality vs a generic vocabulary. Cost: 14 calls for an 11-word answer
  (1 route + 3 prior + 10 decode).
- **Evidence:** working repo with self-measured figures (GitHub, suidouble);
  the author's own verdict is the interesting part: "Is this a good idea? No.
  A single well-posed `Choice` question answers the same question better, in one
  call, with a calibrated confidence attached."
- **New (Sept 22 — midday):** a second word-by-word chatbot over Jev — Bewinxed/jevgpt
  routes the question "Next word?" through Jev with a 20,000-word dictionary,
  replies building several hundred choices deep (demo rendered in demo/jevgpt.mp4).
  Same anti-pattern as suidouble's build above: possible, not sensible.

- **Why it ranks #100:** not a use case — a *falsifier demo*: a fairly precise map
  of where Jev's design stops (the parallel typed-question shape beats
  serialized word-decoding on cost, quality, and calibration). Pairs with #56
  (pixel art) as the anti-pattern end of "parallel outputs."
- **Source:** https://github.com/suidouble/let-jev-speak

### 101. 20-build thread — new shapes surfaced (choi.openai) (7, E2) — NEW
- **What:** a Threads roundup compiling 20 notable Jev builds four days post-launch;
  shapes not yet indexed elsewhere in this catalogue: real-time virtual outfit
  selection from wardrobe metadata, live League-of-Legends win-probability
  analysis, a Tesla FSD-style driving simulation, and real-time shopping
  consultation linked to a live model.
- **Numbers:** none — roundup of others' builds; no independent figures attached.
- **Evidence:** second-hand thread summary (Threads, Sept 20); full thread text not
  recovered by the watch.
- **Why it ranks #101:** useful as a lead list — each shape is a candidate demo —
  but zero measurements; track for first-party posts before promoting.
- **Source:** https://www.threads.com/@choi.openai/post/Ddfes41D9DE

### 106. Bleam AI meta charts — structured-output & tool-call error rates (11, E3) — NEW
- **What:** two bar charts comparing providers on structured-output error rate
  and tool-call error rate (Threads, @ugcwiz — Jesse, cofounder @bleamai,
  Sept 20): TypeSafe Jev 0% on both; Anthropic Haiku 4.5 at 45.5%
  structured-output error; OpenAI sol at 17.0% tool-call error. The caption
  claims >100× faster and 200× cheaper than small LLMs.
- **Numbers:** error-rate figures only; method undisclosed.
- **Evidence:** builder-published charts, promotional tone (E3); flagged in the
  Sept 21 morning scan, text now readable.
- **Why it ranks #106:** a directionally useful provider comparison for the
  reliability-first use cases — but with no method published it's a claim, not
  evidence. Watch for the underlying eval to be released.
- **Source:** https://www.threads.com/@ugcwiz/post/DdiapZWmz_V

### 107. Real-time futures trading decisions (QuantStudio / PandaAI sim) (11, E3) — NEW
- **What:** a Chinese-language builder walkthrough (Facebook reel, Sept 21) of
  Jev driving real-time trading decisions in the PandaAI futures simulation
  competition via QuantStudio: a Choice over buy/sell/hold plus Noul/Score
  judgments, opening long/short positions above 70% confidence. Screen
  recordings show the Jev interface and a dashboard of historical decisions on
  RB futures contracts.
- **Numbers:** none reported — walkthrough, unmeasured; the presenter cautions
  it's not an instant-profit tool.
- **Evidence:** builder demo described second-hand via reel summary
  (zh-language); no author name or first-party text recovered.
- **Why it ranks #107:** a new vertical (retail futures trading) with the
  confidence-gated execution pattern (#3) made explicit — but unmeasured, and
  trading P&L is never in the reel. Watch for backtested results before this
  means anything.
- **Source:** https://www.facebook.com/reel/1079015244884252/

- **New (Sept 23 — morning):** arimanyus/warrenduffer (GitHub, via jevtracks)
  moves this family from sim to live money: a TypeScript intraday bot for
  Indian stocks — Jev ranks the Nifty 50 every 15 seconds, code sizes each
  trade and places the stop, orders go live through Zerodha Kite or Kotak Neo,
  with day replay, kill switch, and daily loss halt. No P&L figures yet — and
  trading P&L is the only number that will ever matter here.
  (https://jevtracks.com/build/j97867rmn24xh9vr6ychzfxhxn8eyjfv)

### 110. CVE → CVSS metric prediction (jev-cvss) (10, E3) — NEW
- **What:** CVE descriptions turned into CVSS v3.1 metric predictions by Jev.
- **Numbers:** none — listed as a community build in the robokrunch/awesome-jev
  index (crawled Sept 21); repo not yet crawled.
- **Evidence:** index-listed community build, unmeasured (E3).
- **Why it ranks #110:** a new security-scoring slot distinct from triage (#39)
  or phishing verdicts (#68) — predicting *structured vulnerability metrics*
  is a different judgment shape; needs accuracy figures against published CVSS
  scores to mean anything.
- **Source:** https://github.com/robokrunch/awesome-jev

### 114. News-feed race claim vs independent tie test (Elvis Sun, via Girsta) (11, E3) — NEW
- **What:** Girsta's analysis piece (IG, Sept 21) describes a viral claim by builder
  Elvis Sun — Jev read 384 news items in 24.9s for $0.19 while Claude Opus 5
  processed only 4 items for $0.77 — alongside an independent test that found
  Opus 5 statistically *tied* with Jev on two real classification jobs, with
  Claude Haiku 4.5 beating it.
- **Numbers:** builder claim (24.9s / $0.19 vs $0.77) is second-hand and its own
  terms are mismatched (384 items vs 4); the independent tie/loss figures are
  cited, not re-audited by this watch.
- **Evidence:** second-hand builder claim + cited independent test via an analysis
  post (E3); the original posts were not directly located.
- **Why it ranks #114:** another honest-falsifier datapoint in the #6/#21/#93
  cluster — Jev's win is throughput-and-cost on the filter layer, not a raw
  judgment-quality win over a strong flash-class model on real classification.
- **Source:** https://www.instagram.com/girsta (Girsta AI Media, Sept 21)

### 122. Real-time virtual-try-on outfit picker (Nailthy Tang / Drape) (11, E4) — NEW
- **What:** an experiment for Drape's realtime virtual try-on hauls: the user
  talks, Jev reads the transcript + what she's wearing and picks the next
  outfit from her closet, changing it in realtime.
- **Numbers:** $0.0011 per decision, ~620ms per decision (builder-stated).
- **Evidence:** builder experiment described on X via madewithjev.com (Sept 22);
  on-screen figures, not independently audited.
- **Why it ranks #122:** a new vertical (fashion/personal styling) for the
  real-time-choice family (#55) — the per-decision price makes "Jev picks the
  next frame's option" a viable product shape beyond games and browsing.
- **Source:** via https://madewithjev.com/

### 124. Alibaba Cloud AI-engineer certification practice exam (SUOHA_AI) (11, E4) — NEW
- **What:** Jev autonomously completed an Alibaba Cloud AI engineer certification
  practice exam — 25 questions, one attempt, no human intervention.
- **Numbers:** 25 questions in 21 seconds at 80% accuracy (builder-claimed,
  X video, Sept 22).
- **Evidence:** demo video with self-reported score (via jevtracks.com's X
  crawl); practice exam, single attempt — capability probe, not a use case with
  an economic buyer.
- **Why it ranks #124:** a structured-test automation datapoint with a real
  accuracy figure (80%) — useful calibration adjacent to #11's synthetic
  workloads, but exam-taking is a stunt shape until someone wires it to
  something consequential.
- **Source:** via https://jevtracks.com/

### 140. Loki log triage for on-call ops (jev-logtriage) (11, E3) — NEW
- **What:** on-call operations build: collapsed Loki logs batched into one Jev
  call of Noul, Score and Choice questions, then answers map in code to
  suppress / watch / review / notify / page — low confidence goes to review,
  and nothing executes.
- **Numbers:** none — index-listed, unmeasured.
- **Evidence:** community project described via the yibie/awesome-jev index
  (Sept 23).
- **Why it ranks #140:** the first observability/incident-pipeline use case —
  log → typed judgment → alert routing, with the "nothing executes" fail-safe
  as the deployment pattern. Needs latency/precision figures to graduate.
- **Source:** via https://github.com/yibie/awesome-jev

### 141. Live sales-call copilot (Moritz Kremb) (11, E3) — NEW
- **What:** a "Sales Copilot" built on Jev: listens to a sales call live, tells
  the rep what to say next, keeps them on script and handling objections,
  shows the current call stage, and gives live signals plus a probability of
  closing. Demo on a recorded sales call.
- **Numbers:** none — demo-described.
- **Evidence:** builder demo on X via madewithjev.com (Sept 23).
- **Why it ranks #141:** the first sales-enablement use case — real-time stage
  tracking and close-probability scoring are new judged quantities; the
  mid-call latency requirement is exactly Jev's slot. Needs measured latency
  and any validation of the close-probability figure to graduate.
- **Source:** via https://madewithjev.com/

### 142. Video-library semantic search (Ghostfeed) (11, E3) — NEW
- **What:** the Ghostfeed app analyzed 1,066 reaction videos with Jev + Gemini
  this weekend — now searchable by structure, reaction type, and who's on
  camera. (The stated purpose — "clone all videos to mass publish them on
  instagram, tiktok and youtube" — is a spam-shaped framing of what is
  otherwise a real semantic-indexing build.)
- **Numbers:** 1,066 videos analyzed; no cost/latency figures.
- **Evidence:** builder-claimed on X via madewithjev.com (Sept 23);
  second-hand, unmeasured.
- **Why it ranks #142:** the first video-content semantic-indexing datapoint —
  the generative + decision division of labor (Gemini + Jev) applied to video
  libraries, a new modality for the NL-search-over-inventory family (#116,
  #123). The mass-republish framing is a red flag on the app, not on the
  technique.
- **Source:** via https://madewithjev.com/

### 143. Minecraft agent (Ronak Malde) (11, E4) — NEW
- **What:** a Minecraft agent — Jev + Astra beat the Ender Dragon in
  8 minutes 43 seconds: near-instant decision-making from Jev plus
  continually learning skills from Astra.
- **Numbers:** 8m43s run; total cost <$1 ($0.01 Jev, $0.96 Astra) — Jev's share
  of a completed game boss fight was a penny.
- **Evidence:** builder demo with open-sourced code and cost figures (X via
  madewithjev.com, Sept 23).
- **Why it ranks #143:** a second Minecraft datapoint in the real-time-control
  family (#55) — and the cleanest cost split of a Jev-assisted agent run yet:
  the decision layer is 1% of the bill. Open code lets this graduate with
  independent runs.
- **Source:** via https://madewithjev.com/

- **New (Sept 23 — evening):** a third Minecraft datapoint — Arjun Dabir's
  "day 2: building with jev" reel shows a Jev-powered bot (JevBot) dueling his
  own avatar in Minecraft 1.21.11, killing it multiple times. Unmeasured demo,
  no cost/accuracy figures — but the real-time-game-control family (#55, #143)
  now has three independent builds in a week.
- **Source:** https://www.instagram.com/reel/DdlhF6zT-jo/ (IG, Sept 22)

### 144. jev-fit — per-idea model-tier router (10, E3) — NEW
- **What:** a hosted "fit checker": paste a software idea, one Jev call runs a
  fixed typed rubric — a `Choice` picks plain code, Jev, or a reasoning LLM
  behind a `Noul` gate for non-tasks; code vetoes Jev when the idea needs
  images; low confidence returns "not sure". Free page and API, closed source.
- **Numbers:** none — tooling, not a workload.
- **Evidence:** index-listed developer tool (via yibie/awesome-jev, Sept 23).
- **Why it ranks #144:** the meta-router made a product — Jev judging which
  tool should do the job — the same triage question every builder answers by
  gut feel. Unmeasured so far.
- **Source:** via https://github.com/yibie/awesome-jev

### 145. Email-classification benchmark + calibration audit (danieleteti) (15, E4) — NEW
- **What:** Daniele Teti's independent benchmark of Jev 1.13 against six LLMs
  on 105 customer emails for a business-management app (61 deliberately hard:
  sarcasm, negations, buried requests), 25 typed questions per email, 2,835
  total calls via OpenRouter — plus a full confidence-calibration audit.
- **Numbers:** balanced accuracy: Jev 94.3–97.2 across 3/10/25 decisions per
  call; no LLM beats it at 3 or 10; only Gemini 3.1 Pro is better at 25
  (+2.3 pts). Latency flat ~0.33s/call regardless of question count; 4–8×
  faster and 2.8–14× cheaper than the cheapest accuracy-matched LLM; ~21×
  faster than Gemini 3.1 Pro. **Calibration:** above 0.95 confidence → 96.5%
  correct; 0.85–0.95 band (mean 0.90) → only 77.6% — the middle band is
  overconfident, so the sensible auto-route threshold is 0.95, not 0.85.
  Also holds in Italian.
- **Evidence:** independent measured benchmark with bootstrap CIs and
  calibration tables, full code + data (blog, updated Sept 22). Caveats: labels
  were written by Claude Opus 5 (no Anthropic model compared), one run.
- **Why it ranks #145:** the most careful calibration audit of Jev's confidence
  yet — it quantifies exactly how the confidence-gated pattern (#3) should be
  tuned: trust the top band, human-review the middle. Directly sharpens #16.
- **Source:** https://www.danieleteti.it/post/jev-typesafe-delphi-benchmark-en/

### 146. jevriel — measured 429-document case study + upgrade skill (thehan-co) (14, E4) — NEW
- **What:** Han Rabinovitz paired-classified 1,708 chunks from 429 real
  documents (2.67M chars) with Jev vs hosted GPT-5.6 Luna through OpenRouter —
  and shipped it as a public Flight Test for his "JEVRIEL" skill/plugin that
  teaches coding agents to upgrade LLM-only apps with Jev and measure the
  before/after (Latency, Inference cost, Fidelity, Throughput/trust — LIFT;
  evidence levels PROBE / CONTROLLED / OBSERVE).
- **Numbers:** Jev median request 0.422s vs 2.092s for GPT (~5× faster);
  estimated inference cost $0.0542 vs $0.2975 (~5.5× cheaper). Tradeoffs on
  the record: 12 invalid batches of 489 for Jev vs 2 for GPT; 68.9%
  agreement on jointly valid chunks; human accuracy scoring pending, so no
  accuracy winner yet.
- **Evidence:** builder-measured paired experiment with published case study
  (GitHub, repo created Sept 21). A pending frontier-model comparison
  (GPT-6 Astra, Claude Fable 5.1, others) is explicitly not yet claimed.
- **Why it ranks #146:** the honest "filter, not replacement" shape of #9
  with the failure rate printed next to the speedup — and the packaged
  *measurement discipline* (no single score that hides a quality loss)
  makes it a template for every future before/after claim in this catalogue.
- **Source:** https://github.com/thehan-co/jevriel

### 147. 4esv/jev-eval — open multi-model decision benchmark (14, E4) — NEW
- **What:** an open, reproducible harness benchmarking Jev 1.13.0, GPT-5.6
  Terra, open-jev, Kev-0.8B and Laya on five labeled tasks (300 items each):
  CLINC150 intent (151 options), Banking77 (77), SST-5, IMDB polarity — with
  accuracy, calibration (ECE), latency and cost.
- **Numbers:** on out-of-sample CLINC150 (in no clone's training list): Jev
  0.897 vs open-jev 0.610 vs Kev-0.8B 0.643 vs Laya 0.497 — a 28.7-point gap
  the clones' in-sample wins (open-jev 0.873 vs Jev 0.780 on Banking77, which
  is in their training data) don't travel past. Only Jev's p50 latency is
  flat in option count: 0.17s → 0.20s from 2 to 151 options (documented
  ceiling 255); local models degrade (Laya 0.04 → 0.16s, open-jev
  0.07 → 0.43s, Kev 0.13 → 0.87s). open-jev best-calibrated on 4/5 tasks,
  free, 435M params. Laya's `noul` fails on one phrasing (0.0 on 100% of
  reviews at near-total confidence) while scoring 0.947 as `choice` — Jev
  unmoved by wording. Kev runs locally speaking Jev's own /v1/systemone
  contract (no adapter needed).
- **Evidence:** independent benchmark with open methodology, full tables and
  reliability diagrams (GitHub, created Sept 18). Caveats: one machine, one
  run, no prompt tuning, public old datasets.
- **Why it ranks #147:** the benchmark that answers #53's open-clone question
  honestly — training-on-the-benchmark vs real generalization, separated with
  numbers; the flat-in-option-count latency is Jev's architectural edge made
  measurable. Updates #53 and #104.
- **Source:** https://github.com/4esv/jev-eval

### 148. Mini OS-automation agent race (Mansi Sonawane / @neuralbrew.ai) (14, E5) — NEW
- **What:** an independent side-by-side: a mini AI agent built by creator Mansi
  Sonawane performs the same OS task on both models — open Calculator, Notepad,
  and a five-minute timer, then close them.
- **Numbers:** single on-screen run: Jev 0.301s vs Claude Opus 5.5 1.856s
  (6.2× faster); 10 repeats: Jev avg 204ms vs Claude 2,001ms (~9.8× faster),
  **both succeeding 10/10 runs**.
- **Evidence:** independent test with metrics (IG reel, Sept 24); n=10,
  author-measured; an honestly narrow micro-task with no inflated accuracy claim.
- **Why it ranks #148:** the first measured Jev-vs-frontier side-by-side on a
  real OS loop rather than batch QA — and the 10/10 tie on success is the
  honest frame: Jev's win here is speed, not capability. Pairs with #8's lesson
  (Jev's edge scales with how much reasoning the task pre-structures).
- **Source:** https://www.instagram.com/reel/Ddpp9POsL9i/

### 149. Content-triage first experiment (mattybuildsit) (11, E3) — NEW
- **What:** a first experiment wiring Jev into an existing repo workflow: sorting
  a content question into five categories (cubby, ai_workflow, making,
  unrelated, needs_review), with the official TypeSafe skill installed via
  `npx skills add`.
- **Numbers:** 10 synthetic questions with expected labels kept local: Jev 10/10
  vs a simple keyword rule 7/10 — the author flags these are synthetic examples only.
- **Evidence:** builder first-experiment documented in a 6-slide carousel (IG,
  Sept 23); methodical (local labels, baseline comparison, keys out of git, call
  limits) but n=10 synthetic, no cost/latency figures.
- **Why it ranks #149:** the cleanest "first hour with Jev" walkthrough so far —
  label-your-own-data, baseline-against-the-dumb-rule, set call limits. Also
  demos the Jev-selects-the-link + Codex-clicks-and-verifies decomposition
  (#19/#57 shape) as a real handoff.
- **Source:** https://www.instagram.com/p/DdolSe8nHHL/

### 150. Shop-listing two-step cascade proposal (Massalski Yauhen / @y.massalski) (10, E2) — NEW
- **What:** a concept (not yet a measured run): Jev classifies the "clear cases"
  from a downloaded shop database ('Bar — Old Town', 'Liquor store — 4th',
  'Bar — Riverside'); a larger LLM reads only the remaining unclassified items.
- **Numbers:** none — proposal-stage; the author "tested the idea" only by
  asking ChatGPT about the approach, not by running Jev.
- **Evidence:** builder-described concept video (IG, Sept 23); no implementation
  or metrics.
- **Why it ranks #150:** a credible proposal for the cascade pattern (#3/#59) on
  a new workload — local-business listing classification — where the value
  proposition is clean: pay the small model for the easy bulk, the expensive one
  only for the ambiguous tail. Watch for an implemented run.
- **Source:** https://www.instagram.com/reel/Ddoh1iiDVpf/

### 151. LangGraph email-intent workflow (GiesN/typesafe-jev-workflow) (11, E3) — NEW
- **What:** a small async LangGraph workflow: a mocked email goes to Jev as one
  typed `Choice` (invoice vs general), then LangGraph routes to the matching
  handler (accounts_payable vs general_inbox); confidence exposed for
  inspection; API failures stop the run rather than fabricate an intent.
- **Numbers:** 10 labeled mocked emails as a smoke check — "not an accuracy
  benchmark" (author's own caveat).
- **Evidence:** working repo (GitHub, surfaced Sept 24); code + mocked data
  public; real TypeSafe API calls.
- **Why it ranks #151:** the first Jev-on-LangGraph routing build catalogued —
  the agentic-framework-shaped version of the email-intent slot (#16), with the
  fail-closed-on-API-error rule worth cloning.
- **Source:** https://github.com/GiesN/typesafe-jev-workflow

### 152. Vendor LLM-guardrail recipe page (TypeSafe) (13, E1) — NEW
- **What:** TypeSafe's own published guardrail recipe (jevtypesafeai.com) with a
  live demo and the exact copy-pasteable payload: one call asks three parallel
  questions — `injection` (noul), `harm` (score: none/low/moderate/high),
  `action` (choice: allow / sanitize / block / escalate).
- **Numbers:** none — a vendor recipe, not a measurement.
- **Evidence:** vendor marketing claim (E1) — but with a live demo and the exact
  request body, the most concrete official reference for the guardrail shape.
- **Why it ranks #152:** the canonical packaging of the catalogue's guardrail
  cluster (#24/#25/#6/#68): "read the typed answers and branch in plain code —
  auto-handle the high-confidence cases, route the uncertain ones." The vendor's
  own answer to the deployment practice in #3; treat as pattern, not proof.
- **Source:** https://jevtypesafeai.com/use-cases/llm-guardrail

### 164. jev-poker — poker CPU with a measured benchmark harness (nft-syou) (11, E4) — NEW
- **What:** No-Limit Texas Hold'em where the CPU players think with Jev
  (bring-your-own-key web app). The repo ships a real benchmark harness:
  `pnpm bench` seats Jev against three baseline bots (`random`, `caller`,
  rule-based `rules`) and Slumbot's public API, reporting bb/100 with 95% CIs
  over mirrored hands (rotated seats), heads-up and six-handed.
- **Numbers:** vs the `rules` bot: **+48.8 [+38.4, +59.1] bb/100** heads-up,
  +11.3 6-max; vs Slumbot (12,000 hands): **−49.4 [−65.8, −33.0]**. The honest
  ablation: the shipped CPU wins because of the code around the model (exact
  hand strength, equity vs pot odds, pot commitment, conventional preflop
  sizes) — the first CPU iteration was −4.5/−24.5 against `rules`; a fixed
  heuristic over the same features trails Jev by 30–50 bb/100 heads-up.
- **Evidence:** builder-published benchmark with CIs, mirrored-hand protocol,
  and an honest Slumbot loss (GitHub, Sept 2026).
- **Why it ranks #164:** the catalogue's most rigorous *decision-policy*
  benchmark format — per-deal mirroring isolates the decision layer from card
  luck — and its lesson rhymes with #8's computer-use caveat: Jev's strength
  is authoring (a persona as a paragraph, probabilities to show, no new code
  per character), while the deterministic state-engineering is where the wins
  come from. Jev beats heuristics but not a real poker AI.
- **Source:** https://github.com/nft-syou/jev-poker

### 182. 1,000-email bulk sort demo (@ai.amirtech) (11, E3) — NEW
- **What:** a demo reel running 1,000 emails through seven questions —
  sorting, tagging, categorization — with Jev vs a standard LLM.
- **Numbers:** Jev **7 seconds for 9 cents** vs the LLM about **5 minutes for
  62 cents** — creator-claimed 46× faster, 12× cheaper.
- **Evidence:** demo reel with claimed numbers (Facebook, Sept 25–26); not
  independently verified, no methodology beyond the on-screen run.
- **Why it ranks #182:** one more concrete bulk-email datapoint in the
  catalogue's thickest cluster (#68 family), with comparative numbers on
  screen — but creator-claimed, so it stays in Tier 4 until the tape is
  published.
- **Source:** https://www.facebook.com/reel/1674232781039127/

### 183. Real-time ad creative generator (@shivsakhuja) (11, E3) — NEW
- **What:** as the user types, Jev selects a combination from the user's own
  asset library — products × 8 layouts × 150 headlines (customizable) × 100
  subheaders × 40 background colors × 40 accent colors — and the ad renders
  instantly.
- **Numbers:** "effectively $0 per ad" — creator-claimed; real-time video demo.
- **Evidence:** X video demo (Sept 26), tracked by JevTracks (6.4K views,
  41 likes). JevTracks notes descriptions/stats come from the project's own
  post and are not independently verified.
- **Why it ranks #183:** the cleanest "Jev as creative director" datapoint so
  far — structured selection from a fixed library at typing speed, deterministic
  renderer draws the pick. Directly portable to pin-design automation (cf. the
  user's Pinloom project): same asset-library shape (product image, headline,
  subheader, layout, colors).
- **Source:** https://www.jevtracks.com/build/j975bjjgcwzkmx1794rb2dms758f4fqs

---

## Sources
- ActionBox review (independent, early access): https://actionbox.cloud/blog/typesafe-ai-jev-review/
- Every / Dan Shipper (independent test): https://every.to/also-true-for-humans/mini-vibe-check-typesafe-s-jev-judged-everything-i-ve-written-in-0-7-seconds
- The Neuron digest (Sept 15): https://www.theneuron.ai/digest/everything-that-happened-in-ai-today-tuesday-september-15-2026/
- TypeSafe launch blog: https://typesafe.ai/blog/introducing-system-one-models-and-jev
- The Register (Doom demo): https://www.theregister.com/ai-and-ml/2026/09/16/typesafe-ai-debuts-model-for-machines-that-plays-doom/5296711
- BusinessWire (funding): https://www.businesswire.com/news/home/20260915525333/en/TypeSafe-AI-Emerges-From-Stealth-With-%2440M-in-Funding-With-New-Model-for-Composable-AI
- browser-use/jev-ultrafast (third-party builder, working demo): https://github.com/browser-use/jev-ultrafast
- Vercel AI Gateway changelog (shipped availability): https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway
- Novel Cognition "The Jev File" (independent-test compilation, Sept 16): https://jev.novcog.us.com
  (read via https://dailytexasnews.com/typesafe-jev-system-one-model-claims-evals-independent-tests-daily_texas/)
- Chen Jing, commit-risk screening (Threads, Sept 17): https://www.threads.com/@chenjingdev/post/DdYNmugFF2M
- Wilson T., typed batch QA race (Threads, Sept 17): https://www.threads.com/@twt_wilson/post/DdYe6V9jJ9f
- Ryan (OpenCode), email triage metrics (Instagram, Sept 16): https://www.instagram.com/reel/DdVR74gtW8-/
- Bashmohandes Mazen, classifier-pipeline benchmark (Instagram, Sept 16): https://www.instagram.com/reel/DdXbPnIFcjP/
- Kun Chen / Firstmate, live task-dispatch routing (Threads + Instagram, Sept 17): https://www.threads.com/@kunchenguid/post/DdYMp1KFS6I
- Murat Aslan, SOAR pipeline proposal (Threads, Sept 17): https://www.threads.com/@iammurataslann/post/DdYYjE0iBxm
- Bashmohandes Mazen, Graffix app test vs Gemini/GPT (Facebook reel, Sept 17): https://www.facebook.com/reel/2169513350302788/
- Hackers in the Loop, Qwen 3.8 vs Jev benchmark (GitHub, Sept 17): https://github.com/iammrduncan/typesafe-ai-benchmark
- GodsBoy, jev-agent-skill-router (GitHub, Sept 16): https://github.com/GodsBoy/jev-agent-skill-router
- VALYU practical guide to Jev, "what people shipped in the first 48 hours" (dev.to, Sept 17): https://dev.to/valyuai/how-to-use-jev-a-practical-guide-to-typesafes-system-one-model-g5e
- awlevin, typesafe-computer-use — Mac computer-use loop with side-by-side Opus 5 measurements (GitHub, Sept 17): https://github.com/awlevin/typesafe-computer-use
- Hassan El Mghari, 1kpapers.com — 1,018 papers classified for $0.08 (Sept 17): https://1kpapers.com
- Moritz / @promptwarrior, "Jev underhood" voice browser (Threads, Sept 17): https://www.instagram.com/reel/DdZomecuUv9/
- rtrvr.ai (Retriever AI), Jev browser-agent harness test (Instagram, Sept 17): https://www.instagram.com/reel/DdZostuh2_m/
- jarrodwatts, jev-trader — per-block market-making on Monad/Kuru (GitHub, Sept 17): https://github.com/jarrodwatts/jev-trader
- phyous, tsai-sc — StarCraft Strongarm completed by Jev, verification report (GitHub, Sept 17): https://github.com/phyous/tsai-sc
- RomanSlack, jev-drone — autonomous drone tactical judgment at ~2.5Hz (GitHub, Sept 17): https://github.com/RomanSlack/jev-drone
- devagrawal09, jev-review — staged code-review workflow on Jev (GitHub, Sept 17): https://github.com/devagrawal09/jev-review
- fhshaik, typesafe-mario — Super Mario via emulator-RAM-to-JSON Jev loop (GitHub, Sept 17): https://github.com/fhshaik/typesafe-mario
- Hudson Brendon / @99hud, Jev vs GPT-5.6 27-question race (Instagram, Sept 17): https://www.instagram.com/reel/DdZxxWEDRAI/
- Nick Vasilescu, Jev playing Minecraft on Orgo cloud computer (Instagram, Sept 17): https://www.instagram.com/reel/DdZomecuUv9/
- Geyzson Kristoffer, Jev Alpha vs Jev Bravo in C&C Red Alert 2 (Facebook, Sept 17): https://www.facebook.com/reel/2147598466152328/
- counterproof.io, independent audit of TypeSafe's workflow eval / consensus labels (updated Sept 17): https://counterproof.io/notes/typesafe-jev-benchmark-consensus-labels/
- jkudish/jev-mcp — MCP server: jev_verify / jev_screen / jev_find judgment tools for agents, on npm (Sept 18): https://github.com/jkudish/jev-mcp
- ar9av_, Jev as prompt-injection judge vs gpt-5.6-luna — 10× faster, 24/24 caught, 41% FP rate (Threads, Sept 18): https://www.threads.com/@ar9av_/post/Dda7SFulEEZ
- "JEV Browser Agent" Wikipedia chain demo, 31 decisions / $0.0032 (Instagram reel, Sept 18): https://www.instagram.com/reel/Ddavc3_BC8Q/
- jexp/neo4jev — Jev navigating a Neo4j graph, one system_one call per hop + beam search (Sept 18): https://github.com/jexp/neo4jev
- joshlarsen/jev-t-rex-runner — Chrome dino game played by Jev (Sept 18): https://github.com/joshlarsen/jev-t-rex-runner
- Wizhill05/typesafe-image-diffusion — pixel art via 256 parallel Jev pixel questions (Sept 18): https://github.com/Wizhill05/typesafe-image-diffusion
- Logic Decode, "TypeSafe's Jev: An AI Model That Answers in Types, Not Text" — eval teardown, no new independent data (Sept 16): https://logicdecode.in/blog/typesafe-jev-system-one-model-2026
- anandi1989/awesome-jev-usecases — community evidence-backed index of Jev use cases, repos and measured results (updated Sept 18): https://github.com/anandi1989/awesome-jev-usecases
- Mike (aiwithmike), "Jev, three days in: what is known, what is guessed, and what it is good for" — calibration/ECE discussion, community-test roundup (Sept 18): https://aiwithmike.substack.com/p/jev-three-days-in-what-is-known-what
- Paul Piper / @madppiper, "My Thoughts on Jev, after the Private Beta" — intent gate + misuse detection use case (Sept 18): https://madppiper.substack.com/p/my-thoughts-on-jev-after-the-private
- @magicmonx, real-time ad processing dashboard — 724 ads / 8,724 judgments / $0.0895 in ~40.5s (Threads, Sept 18): https://www.threads.com/@magicmonx/post/Ddak-8ED2-o
- Supercenter demo, Jev tool-call ranking — 200+ cases, ~40× faster, 9× more effective, self-reported (Instagram, Sept 18): https://www.instagram.com/reel/Dda68ldAUHH/
- @xtract.ai, "hire Jev as your QA tester" iPhone to-do app demo (Instagram, Sept 18): https://www.instagram.com/reel/DdapN3LIqwx/
- @bluevelo1666, Agoda travel-search agent with spend dashboard — $0.01 Jev per search (Threads, Sept 18): https://www.threads.com/@bluevelo1666/post/DdbpQhLE9U7
- Eric Lin / @eric80522, 500-piece corpus classification vs Opus 5 — ~100× faster, ~900× cheaper (Threads, Sept 18): https://www.threads.com/@eric80522/post/DdbeqzFEpZf
- usutaku (Michikusa Co.), "JEV Speed Race" email classification vs GPT Luna / Claude Sonnet / Gemini 3.5 Flash (Facebook, Sept 18): https://www.facebook.com/reel/2552201755282975/
- @brochbuilds, "Jev demos: Part 2" — 700 leads in 40s claim, JevPilot sim, Subway Surfers (Instagram, Sept 18): https://www.instagram.com/reel/Ddb2wLbjAan/
- @sliven0722, Max Blade's Jev playing Subway Surfers with real-time probabilities (Threads, Sept 18): https://www.threads.com/@sliven0722/post/DdbD2zbEan6
- jkudish/jev-browser — packaged browser agent with real-site runs (README crawled Sept 18): https://github.com/jkudish/jev-browser
- Wiki-race demo — Jev vs GPT-5 Terra, Claude Haiku 4.5, Sonnet 5, Luna, Opus 5, Astro across three races (Instagram screen recording, Sept 17): https://www.instagram.com/reel/DdZO6W8z7qg/
- mahan-ym/cleaner — Jev file-cleanup triage, "safe to delete / uncertain / keep" over local directories (YouTube + GitHub, Sept 17): https://www.youtube.com/watch?v=267fGQeLSkg · https://github.com/mahan-ym/cleaner
- @rapidlygrow.ai, voice-controlled Figma automation via Jev (Instagram reel, Sept 18): https://www.instagram.com/reel/Ddb71yQy0Rw/
- Kevin Magnan, "7 wild JEV builds in 48 hours" — roundup including content-filtering extensions (Instagram, Sept 18): https://www.instagram.com/reel/DdbjduSDrxT/
- Chen Jing, Jev playing Tetris — sensory/motor/judgment layers (Threads, Sept 18): https://www.threads.com/@chenjingdev/post/DdcCAYcjuua
- Jev on OpenRouter in beta, typesafe/jev-latest (Threads, Sept 18): https://www.threads.com/@ethansavemoneydairy/post/DdaWoTyANM-
- madewithjev.com — community gallery of Jev builds: 249 entries (94 X posts, 118 GitHub, 8 YouTube, 11 skills, 3 articles, 7 sites, 63 guides) — new X-coverage channel for the watch (Sept 19): https://madewithjev.com/
- Isaac Flath, "Six things I tried with Jev" — six measured use cases vs Gemini 3.5 Flash: script fact-checking 24/24 at 0.41s, feed ranking 6/10 relevant, RAG rerank first 7/12, citation checking, note grouping 24/28 at 0.35s, trace diagnosis 19/24 at 0.52s (Sept 17): https://isaacflath.com/writing/six-things-i-tried-with-jev
- idan levin / nekuda-ai, Jev + WebMCP benchmark — 49/49 tasks, ~112× lower model cost than GPT-6 Astra computer use, open & reproducible (X, Sept 19): https://webmcp.com/benchmark · https://github.com/nekuda-ai/WindTunnel
- abhixhek/jevcal — calibrate/threshold/drift-check typed decision models against an LLM teacher (GitHub, Sept 19): https://github.com/abhixhek/jevcal
- moritzkremb/jev-voice-browser — voice-controlled browser, 27/27 integration tests, ~330ms avg latency, ~$0.01 end-to-end demo (GitHub, Sept 19): https://github.com/moritzkremb/jev-voice-browser
- archerhume.com/posts/jevs-architecture-unmasked — ~10k-call architecture probe: ECE 0.0313, ~100 parallel questions on shared state, choice-order bias (via iwashi86 X thread, Sept 19)
- Patrick McGuinness, "Jev Makes Fast and Cheap Decisions" — Cua jev-use preview, Jevify skill, Volkov X-timeline extension roundup (Substack, Sept 18): https://patmcguinness.substack.com/p/jev-makes-fast-and-cheap-decisions
- Aman Kumar, "Testing Jev on public and private data: classifier or filter?" — 16,000-call independent measurement: 4 public sets + production pipeline gates + third-party roundup (HiringCafe, 19,500-email spam run, Classmethod router, Vercel CTO eval) (Sept 19): https://amankumar.ai/blogs/jev-measured · full per-item answers: https://github.com/onlyoneaman/jev-eval
- mohitkarekar.com, probabilistic-decision write-up — Vercel's production command-safety classifier replacing ChatGPT Luna 5.6: 5–18× faster, more accurate (Sept 19): https://mohitkarekar.com/posts/2026/probabilistic-decisions-with-system-one-model-jev/
- @ebrain.lab, Korean sentence classifier — 40/40 in 1.9s at $0.0012, 62% whole-document failure (Threads, Sept 19): https://www.threads.com/@ebrain.lab/post/DddGgXuoLlL
- tamaratran/fast-jev-compaction — independent report: ~1M tokens → 86K in 1s (Sept 19): https://github.com/tamaratran/fast-jev-compaction
- YouTube "AI Action Gate" demo — authorization/destructiveness/policy/hallucinated-call/prompt-injection gating, no metrics (Sept 19): https://www.youtube.com/watch?v=jU6o3nUY17s
- madewithjev.com re-crawled (Sept 19 midday) — ~20 new builds surfaced: SuperX viral scoring, Eve framework, Mac-app support router, Lurk, jev-job-hunter, OCR image triage, firehose-judge, siftr, security/tooling family burst: https://madewithjev.com/
- openchamber.dev, "Jev by TypeSafe AI: What 12,759 tweets tell us" — launch-window X-post meta-analysis (Sept 15–18): user-reported medians 7× speedup / 30× cost / 76ms latency vs vendor 193.6×/444.6×; new datapoints (PR review $0.00007 in 0.5s, 20,700 YouTube comments in 2m27s at $0.20, ESLint 90% agreement, plain-English rule engine $0.0001/check): https://openchamber.dev/blog/jev-typesafe-ai/
- tjklug, "Code Enumerates, Jev Judges: Building slopcheck" — PR slop-review CI tool: hundreds of questions/change in one request, 9,629 tokens / 0.7s round trip, whole build $0.91, caught 2 real bugs in its own code (Sept 19): https://tjklug.com/posts/typesafe-jev-slopcheck/
- MarkTechPost (Sept 19) — Jev roundup: Bryo AI email triage (Gemini slightly more accurate, 10–20× pricier), jevmeter debate scoring ~$0.05, Steve Krouse's Typewriter, leepokai/jev-guard, Stagehand ~$0.001/task, heist-one guards: https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/
- RuntimeWire (Sept 19) — LangChain adds Jev to the agent control loop: https://runtimewire.com/article/langchain-adds-jev-decision-model-agent-workflows
- kraayenjon/awesome-jev — second curated Jev list with "featured builds with real numbers" table (links back to madewithjev.com): https://github.com/kraayenjon/awesome-jev
- RuntimeWire / Matthew O'Riordan (Ably), "TypeSafe raises $40M for Jev" — four-lane Pong latency race: Jev 227ms avg / 400ms p95 vs Gemini 3.8 Flash 3.2s / Claude Haiku 4.5 2.5s / GPT-5.6 Sol 3.5s, source + stats published, honest framing (Sept 18): https://runtimewire.com/article/diogo-almeida-typesafe-jev-40m-seed-pong
- AY Automate, "Jev by TypeSafe AI Explained" — second-hand roundup of developer builds with named handles (checked Sept 18–19): @milindlabs on-device OCR→Jev computer use, @marcusyul 1,891 ads in 19s for 12¢, @rafalwilinski parallel adversarial test suite, @0x_kaize Claude Code context compaction, @ephraimduncan model router, @ziwenxu_ support-ticket triage + Tetris (Sept 19; figures explicitly not reproduced by the article): https://www.ayautomate.com/blog/jev-typesafe-system-one-model
- LangChain, "Can Jev Be a Better Agent Evaluator?" — Jev-as-judge for agent evals on LangSmith: quality-score variance 92–913× lower than GPT-5.6 Luna/Terra and Claude Sonnet 4.6; 0.44s avg, $0.00035/call, $0.34 total vs $28.17 Claude; "promising, but early" (Sept 19–20): https://www.langchain.com/blog/jev-agent-evals-langsmith
- @nomadius.cyou, "Browser Dealer by K2S" — side-by-side browser race Zürich→London flights: Jev agent 107 actions in 26s at ~213ms avg, ~$0.0004 total, 100% progress vs frontier LLM ~2.3s latency at $0.458 (Threads, Sept 21): https://www.threads.com/@nomadius.cyou/post/DdjsSfigjLt
- Murat Aslan, automated Jev-alternatives leaderboard — "Decisions/s vs macro accuracy" harness auto-benchmarking every Jev alternative vs Jev 1.13.0 (~77% macro accuracy); 24 candidates + 40 queued; Simplejev-Qwen3.8-27B best OSS drop-in, Reflex-4b 2–3× faster at ~5pts less, Decider-2b ~10× faster, Laya dismissed (Threads, Sept 20): https://www.threads.com/@iammurataslann/post/DdhbczACGhd
- @felixmohr, Skittles sorting race — 10,000 Skittles, Jev 1.13 vs Claude Sonnet 5: 97% in 220.8s at $0.15 vs 99.4% in 1,075.2s at $1.86; 4.9× faster, 12× cheaper, 2.4pts less accurate (animated demo, FB reel, Sept 21): https://www.facebook.com/reel/1431013785795356/
- @ugcwiz (Jesse, Bleam AI), meta charts — structured-output error rate: Jev 0% vs Anthropic Haiku 4.5 45.5%; tool-call error rate: Jev 0% vs OpenAI sol 17.0% (Threads, Sept 20–21): https://www.threads.com/@ugcwiz/post/DdiapZWmz_V
- AI Weekly (via Forbes), "TypeSafe's Jev Hits 13% of Vercel Paid Teams in 24 Hours" — 2× GPT-5.6, >6× Fable 5.1; Langfuse wired Jev into its stack within three days of launch (Sept 21): https://aiweekly.co/alerts/typesafes-jev-hits-13-of-vercel-paid-teams-in-24-hours
- Chinese FB reel — Jev real-time futures trading decisions (buy/sell/hold Choice, >70% confidence → open long/short) integrated with QuantStudio in the PandaAI futures simulation competition; unmeasured walkthrough (Sept 21): https://www.facebook.com/reel/1079015244884252/
- robokrunch/awesome-jev — new curated Jev list with a "Benchmarks & Evaluations" section: RoboKrunch robot-fleet triage (300 real API calls, bilingual CN/EN warehouse-AMR incidents; 300/300, p50 0.53s/p95 0.81s, $24.57/M decisions; $354/mo vs ~$1,814/mo GPT-4o-mini projected) and Jev vs self-hosted ModernBERT (169ms vs 527ms p50, ~977K decisions/month infra crossover; "edge is zero training/labeling/ops, not raw speed"); also lists jev-cvss (CVE → CVSS v3.1) (crawled Sept 21): https://github.com/robokrunch/awesome-jev
- marcemarin/typesafe-laravel — unofficial PHP/Laravel SDK; its README documents radio-chat as a real-world use: Jev triages WhatsApp messages for a live radio show (intent, tone, moderation, "read on air?" score in one request), an LLM extractor only for worthy messages (crawled Sept 21): https://github.com/marcemarin/typesafe-laravel
- Shivkumar Iyer, Jev vs Oracle Fusion duplicate-customer records — 3-pair dedupe probe: merge / no-merge (address as reason) / leave alone, ~$0.0001 total; 50-pair hand-labelled test in progress (LinkedIn, Sept 22): https://www.linkedin.com/posts/kumr192_jev-jev-typesafe-activity-7507258509382565888-vjTy
- fajarhide/askgrep — semantic code grep over every function: 2 hits, 11 chunks, 3,910 tokens, $0.0002 (GitHub, Apache-2.0): https://github.com/fajarhide/askgrep
- KDnuggets, "What Everyone Is Getting Wrong About TypeSafe AI's Jev" — cites blackbarata agent routing (145–271ms) and TigerOk4538 model routing (~1s vs 4–14s LLM): https://www.kdnuggets.com/what-everyone-is-getting-wrong-about-typesafe-ais-jev
- TrueHorizon.ai, internal agent-routing test — Jev vs GPT-5.6 Luna: 6.5× faster (404ms vs 2,633ms median), held-out Luna 96.8% vs Jev 93.5% on 93 unseen cases; 1 invalid route answer in 639 calls (Instagram carousel, Sept 24): https://www.instagram.com/p/DdsNVzZGDPV/
- yuyang2230/jev-agent-skill — Claude Code/ZCode skill offloading small judgments to Jev via OpenCode Zen free /v1/systemone; production e-commerce comment-triage case study (Sept 2026): https://github.com/yuyang2230/jev-agent-skill
- Charlie Automates, structured classification benchmark — Jev $0.000069 vs Claude $0.018800, ~0.4s (Facebook reel, Sept 24): https://www.facebook.com/reel/1533017398851021/
- @aisarva_07, Clip Fast app — Jev-powered review-verdict + ad mining from video reviews: second-half verdict clips at 13–17% scores, 205 ad-segments scanned, 40 comments 63/37 liked/flop (Instagram, Sept 25): https://www.instagram.com/reel/Dds9LFKN4Og/
- gaborishka/jev-canvas — voice + pointing-finger control of a tldraw canvas, Jev decides action/target/place ~350ms per spoken word, 18-spoken-command real-API probe (Sept 2026): https://github.com/gaborishka/jev-canvas
- zhangcy122/OpenJev — open Jev-interface clone; OpenJevPro harness vs Jev on Banking77 (36 queries): Jev 0% out-of-scope rejection (forced pick), ECE 0.284 vs 0.089: https://github.com/zhangcy122/OpenJev
- matthewp/flue-jev-demo — Flue agent support routing with Jev via Cloudflare AI Gateway binding (Sept 2026): https://github.com/matthewp/flue-jev-demo
- @nate_pacyga, Jev-vs-Jev Advance-Wars-style game running autonomously, open-source promised (Threads, Sept 24): https://www.threads.com/@nate_pacyga/post/DdrkiHyjPsm
- RuntimeWire, "TypeSafe tests semantic lints before malformed questions reach Jev" — console question-editor lint ("Not a yes/no question"), surfaced by Jane Manchun Wong X post (Sept 20): https://runtimewire.com/article/typesafe-tests-semantic-lints-jev-questions
- AY Automate, "Jev vs GPT and Claude: Independent Benchmark" — 791 labeled decisions, Jev vs 4 LLMs on one billing meter; Jev-first cascade (≥0.80 confidence → GPT-5.6 Terra) matched Terra at 26–28% of cost; AUROC 0.990 on injection detection (Sept 19): https://www.ayautomate.com/blog/jev-vs-llm-benchmark
- nft-syou/jev-poker — NL Hold'em CPU with mirrored-hand benchmark: +48.8 bb/100 vs rules bot heads-up, −49.4 vs Slumbot; heuristic ablation: https://github.com/nft-syou/jev-poker
- AIPI-mvoronovych/JEVBenchmark-Contradiction-Detection — CLASH contradiction-detection protocol adapted to Jev, 1,289 samples, results published in-repo: https://github.com/AIPI-mvoronovych/JEVBenchmark-Contradiction-Detection
- BradMyrick/Jev-Rug-Checker — one-file EVM token screener: hard-fail facts in code, fuzzy half to Jev, `--replay` policy re-tuning free: https://github.com/BradMyrick/Jev-Rug-Checker
- WiktorB2004/llama-index-jev — LlamaIndex JevRerank + JevSingleSelector/JevMultiSelector (tests mocked; benchmark pending): https://github.com/WiktorB2004/llama-index-jev
- zeke/jev — research notes + Cloudflare Worker triage demo; surfaces Probably language (Steve Faulkner), @DanFrmSpace Pokémon Showdown (41 games, 2.6M tokens, $0.11), @GoSailGlobal MIND-news negative (ρ ≈ −0.129): https://github.com/zeke/jev
- Competitor-watch (Sept 26 — morning): the open "decision model" ecosystem is now a weekly landscape — @yrzheee Threads carousel maps CLM (Qwen3-8B encoder; official benchmarks claim up to 9× faster zero-shot selection, 4.1–5.7× faster agent verification vs Jev), JevK5 v0.2 (33.1% vs Jev 36.7% on 308 hard problems), Kev, Laya, SemIf, AnyJev, Ollaya local runner, pg-jev semantic SQL filtering, jevclip video clipper: https://www.threads.com/@yrzheee/post/DdwFLFGkVUA — plus Xor (juspay, Qwen-3.6-35B post-train): 231 JevBench v1.2 decisions, 88.3% vs Jev 86.1% vs vanilla 84.8% (hard tier 77.5% vs 72.1%): https://www.threads.com/@dailyaionly/post/DdtYCROCcu7 — and two Laya-vs-Jev video explainers: @underthehoodai (Laya fine-tuned on 1,200 examples reaches 77% vs Jev 73% on the typed-decisions benchmark; Jev keeps the 77-option width edge 76% vs ~38%): https://www.instagram.com/reel/Dds8rLUqAzx/ and @virex.explains (2,000-decision shared bench: Laya 0.766 vs Jev 0.727, ~33ms vs 236–276ms, Jev keeps calibration edge): https://www.threads.com/@virex.explains/post/DdsaWsiAXGk — none are Jev use cases; logged for the clone/competitor front
- Competitor-watch (Sept 26 — midday): SemIf-OpenJev — open-source Jev-interface clone (4,896 stars at post time): 21 yes/no checks in 1.02s vs 5.33s for a JSON array on RTX 3090; demoed with the @nightlybuild video-moment retriever: https://www.instagram.com/reel/Ddv4uI1t5fY/ — competitor-watch, not a Jev use case
- Infra-watch (Sept 26 — evening): OpenRouter lists typesafe/jev-router (listed Sept 25) — Jev dynamically picks model + reasoning effort per request, free-priced, claimed million-token context; no usage data yet: https://runtimewire.com/article/typesafe-jev-router-openrouter-launch · n8n community node "Jev Classification 0.3.0" (animated promos, routing-with-confidence examples, Sept 26): https://www.threads.com/@khmuhtadin/post/DduuIVOjCSc · https://www.threads.com/@khaisastudio/post/DdwTpHeADav — tooling, not use cases
- Index-watch (Sept 26 — evening): daftAI2026/awesome-jev — new community directory listing fresh repos to verify: IslamBaraka90/jev-typesafe-real-financial-use-cases (50 real-world financial cases graded against known answers), NaluKicks-808/jev-field-trial (pre-registered 20-test field trial on a second brain + Claude Code history, failures included), marcosmartinez/jev-acento (pre-registered Spanish accuracy/calibration/token-cost audit), patryckalves/jev-no-enem (ENEM 2025 benchmark with dashboard), jeiel85/jevscope (local-first decision debugger), joaovaleri/jev-shortlist (cached Jev prior for active learning, ASReview controls), ogamircs/jev-demo (support-ticket triage vs OpenAI side-by-side): https://github.com/daftAI2026/awesome-jev — plus mrjev/awesome-jev's "Model Routing" section (7 community jev-router builds: gargpratyush/jev-router, tiershift, safer-with-jev, jev-gateway, BillionsBobby/JevRouter, Bodila51/grok-bot-jev, philippdubach/pi-jev-router, Jimuelle07/Helm): https://github.com/mrjev/awesome-jev — and sonson0910/jev-router (fail-open advisory workflow router for Codex/Claude Code, explicitly an independent community plugin): https://github.com/sonson0910/jev-router/blob/HEAD/README.md
- Benchmark-watch (Sept 26 — evening): themsquared/jev-benchmark — reproducible tool-call risk classification (readonly/destructive/privileged/exfiltration), n=60 per model, calibration result + failure section, raw results committed (created Sept 17): https://github.com/themsquared/jev-benchmark — codeslp/btrain jev-typesafe-repo-assessment — 35-repo evidence assessment; logs an unverified organizational staff_search experiment (23/23 in-taxonomy correct, 6/6 out-of-taxonomy confidently wrong) and a frozen btrain pilot commit: https://github.com/codeslp/btrain/blob/HEAD/research/jev-typesafe-repo-assessment.md
- Competitor-watch (Sept 27 — midday): SupersonicLabs/Julia-1 — a **fifth** open Jev-interface re-implementation: 144.3M params, mmBERT-based, on Hugging Face ("Julia family" test of their training system). Self-evaluated Sept 24, 2026 with published tables vs supplied Jev references (not a new Jev run): 2,000 typed decisions 73.15% vs 72.70% Jev reference; AG News 94/100 vs 91; DAIR Emotion 86/100 vs 48; Banking77 72-label shortlist 64/100 vs Jev 87% (honest long-list failure); MASSIVE scenario classification 110,573/154,648 (71.50% macro across 52 locales; pt-PT 86.25%, en-US 86.75%): https://huggingface.co/SupersonicLabs/Julia-1 · https://supersoniclabs.ia.br/julia-1/ — plus @jackykit WIP carousel documenting a Jev-JK distillation pipeline: deberta-v3-small student trained on 244,222 teacher rows (top-1 agreement 0.9148), ONNX + int8 quantization, rebuilt for CPU (~120ms mean inference, 20–200ms), parallel spam-email analysis, Docker support: https://www.instagram.com/p/Ddvj4G9FFh1/ — competitor-watch, not Jev use cases
- Media note (Sept 26 — evening): Bloomberg News video segment on Jev (TypeSafe, RLCD, 20-200× claims) circulating via Facebook: https://www.facebook.com/reel/1626215122616195/ — mainstream coverage, no new evidence

## Midday scan (Sept 26) — auditable log
- Added: #185 RFQ benchmark (dev.to + repo, Sept 26) · #186 Fez router (GitHub, Sept 18, found Sept 26) · #187 Julia Joung market research (LinkedIn, Sept 24) · #188 Wayne Lian email filter cascade (IG, Sept 26) · #189 Rama Digital deflection audit (Threads, Sept 26) · #190 NovCog fallsover calibration audit (Sept 26)
- Second sighting: #32 (Nirali Khoda wiki race, IG, Sept 26)
- Rejects: @evolvingfrontier carousel (repeats covered demos, no new measurements) · @mdazlaanzubairr critique (no new evidence) · @alasadi.ai (Arabic rehash of the 1,000-email demo figures) · @aianalyse (AI-generated infographic, no new evidence) · @franklyfranzy.ai (no new evidence) · @fabzityai (no new evidence) · @SoftReviewed (no new evidence) · @aibyseharnazeer (no new evidence) · @Kushal Vijay (no new evidence) · @ishwarxai (no new evidence) · karmactive.com TrueStandard claim (single-source content-farm, unverifiable)

## Evening scan (Sept 26) — auditable log
- Added: #191 jev-code-review-benchmark (gemanor, GitHub Sept 17, found Sept 26) · #192 jevmem memory gate (markhuang.ai analysis Sept 25, tool v0.4.5) · #193 OpenRouter × TypeSafe Jev Router launch (listed Sept 25, covered PANews/Phemex/RuntimeWire Sept 26) · #194 kunko-ai-labs judge-audit router audit (GitHub, run Sept 19) · #195 VentureBeat prompt-injection security analysis (Sept ~21, Octomind demo datapoint) · #196 AIMLAPI live measured-call walkthrough (Sept 25) · #197 Sridhar Sampath TowardsAI hands-on (Sept ~23)
- Watch notes: themsquared/jev-benchmark (tool-call risk, n=60/model) · codeslp/btrain repo assessment (OOD anecdote: 6/6 out-of-taxonomy confidently wrong, unverified) · daftAI2026/awesome-jev directory (jev-field-trial, jev-acento, jev-no-enem, financial-use-cases, jevscope, jev-shortlist, ogamircs/jev-demo) · mrjev/awesome-jev model-routing section + sonson0910/jev-router · n8n "Jev Classification 0.3.0" community node · Bloomberg News FB video (coverage, no evidence)
- Rejects: @codeandfun01 (explainer, vendor figures) · @kushal_vijay_ ×2 (explainer, vendor figures) · @bytecodemedia (explainer) · @the.datascience.gal / @aishwarya-type (explainer) · @aibyseharnazeer ×2 (explainer/PR-merge narrative, no measurements) · @fabzityai (slide explainer) · @maitrimangal (explainer) · @trilhainfo (Portuguese explainer, no data) · @tejas.algo (OpenRouter signup tutorial, no new evidence) · @ai.amirtech IG/FB (1,000-email/7-question demo — already #182) · @rajankanth FB (VS Code tutorial, no new evidence) · @vaibhav.codes (explainer) · @saban/@saban.talks ×2 (Blinkit engineer explainer) · @thecyberjai (Tamil explainer) · @papa_programmer carousel (Codex/Claude Code walkthrough, no new evidence) · @gobi_automates carousel (repo roundup: jev-ultrafast, fast-jev-compaction already covered; pg-jev already in competitor log) · @urooj.fatimaaa / @toyafounder / others (AI-generated infographics, vendor figures) · @dailyaionly Xor infographic + @underthehoodai Laya-vs-Jev (both already in Sept 26 morning competitor log) · dev.to/kunalkmaurya (explainer, no measurements)
- Coverage note: no X connector — X posts surfaced only via web search results (RuntimeWire/PANews/Phemex cite TypeSafe's X post); social.search covered IG/FB/Threads only.

## Midday scan (Sept 27) — auditable log
- Added: #201 Entagl 1,759-decision two-round test (18, E5) · #202 PromptRejector MCP/skill/prompt screening (19, E5) · #203 Datadog Agent Observability eval wiring (17, E4)
- Watch notes: Julia-1 (SupersonicLabs, 144.3M mmBERT-based open decision model, self-eval vs Jev references) · @jackykit Jev-JK distillation WIP carousel · @leofongai (Threads) JEV tool-filter layer with OPUS 5.5 screen-recording demo (Chinese, no metrics) · @theyashwanthsai_ Jev-as-eval-judge framework reel (Python screenshots, no metrics) · @sung.kim.mw Julia-1 announcement thread
- Rejects: KDnuggets Jev explainer (roundup; cites blackbarata/TigerOk4538/Matthew Berman — second-hand one-line claims, no author posts; same pattern as morning reject) · stickfight.co.uk corporate-style op-ed (no new evidence) · cellcog.ai "Jev and TypeSafe: What It Is, Why It Spread" (claims review, Sept 19 data) · towardsai "Jev Doesn't Write, It Decides" (opinion, games-as-home thesis, no measurements) · mithilmaske Medium explainer (roundup of known datapoints) · mehul gupta Laya-vs-Jev explainer (competitor explainer) · Sumit Pandey TowardsDeepLearning Laya-clone story (narrative, no new primary evidence) · ~50 social sweep posts since 09-26 (explainers/hype reels: @hussali.ai, @codeandfun01, @v.i.s.h.ai, @saban/@saban.talks, @thecyberjai, @ishwarixai, @vivek.stack, @kushal_vijay_, @youssefxai, @bloombergtv news clip, @kevinsingh.me JevRouter promo, n8n Jev-Classification community node repost @khmuhtadin/@khaisastudio already logged Sept 26, Bloomberg sept 27 reel) — no metrics, no new evidence
- Coverage note: no X connector — X posts surfaced only via web search results; social.search covered IG/FB/Threads only; browser.search returned 18 results across 4 queries.

## Morning scan (Sept 29) — auditable log
- Added: #213 CMU "JEV-as-a-Judge" cascade benchmark (arXiv 2609.26550, found Sept 29) · #214 "JEV vs. LLMs as Rubric Judges" academic panels (arXiv 2609.29769, found Sept 29) · #215 Kartha Health ICD-10 code reviewer (Vietnamese FB group, Sept 29) · #216 Oliver Merrick SEO keyword-gap workflow (IG, Sept 29) · #217 Oliver Merrick sales-objection mining (FB, Sept 29) · #218 Oliver Merrick YC batch enrichment (IG, Sept 29) · #219 juan294/sutura green-wash trap audit (GitHub, Sept 17, found Sept 29)
- Updated: #196 (aimlapi.com) — author's own cross-model tests now on page: moderation F1 0.696 best of six, answer-rating Spearman 0.39 worst, ECE 0.032/0.096, cost parity with cheap LLMs on rating tasks
- Watch notes: "Jeff" 0.8B Jev-compatible decision model (@laxima.tech, Threads, Sept 29) · Kev open-source alternative (Jared Palmer, 7,792 stars/12 days, 0.5–3B params) · @selma.builds Turkish carousel with three Jev demo projects (clothing-filter search, live speech judging, Minecraft bot; promo listicle, no metrics) · @bruno.nardon "700 leads in 40 seconds for less than ten cents" (Portuguese, self-reported, no audit) · "JEV-as-a-Judge" creator reel (@parthknowstheai, FB) — surfaced the CMU paper, added as #213
- Rejects: ~40 remaining new social posts since the evening scan — explainers/hype reels (@brijpandeyji JEV-vs-LLM infographic, @fullstackparody setup guide, @underscore.code carousel, @sdk.kim.builds Persian/Toss trading demo thin on metrics, @connordavis.ai dental-call demo, FrontiersMind "Lumma" competitor, @betterstackhq obsolescence op-ed, @theakhiltech explainer, @cjtrowbridge "simple classifier" take, @economictimesai promo, @cybersecuritybrazil promo, @michaelskircak routing take, @owenzhang18 explainer, @cloudalph explainer, @codeonpaperyt infographic, @kyle.disruptor explainer, @nexodrax infographic, @thesriramvaradharajan multi-demo promo, @shashank_s_sharma workflow comparison, @saziqo.ir Persian explainer, @semasarvi explainer, @arslan.labs carousel, @nazrielnr Kev reel, @aiwithizz cost explainer, @codewithbrij Threads infographic, @powerful.repos OSS-alternatives carousel, @sagar_695 architecture carousel, @digination.id Indonesian explainer, @akbarfarooq.ai graphic, @971239412040166 FB explainer, @agenceooo explainer, @codewithaiofficial explainer) — no new metrics or evidence beyond vendor figures
- Coverage note: no X connector — X posts surfaced only via web search results; social.search covered IG/FB/Threads only (49 posts, all Sept 29); seen-posts.json updated.
