# Feature Delta: bookmark-cli

Wave: DISCOVER Date: 2026-06-06 Status: Complete — all gates G1-G4 passed

---

## Wave: DISCOVER / [REF-01] Persona Identification

**Primary Persona: Terminal-First Developer**
A backend developer, DevOps engineer, or open-source maintainer for whom the terminal is the primary workspace. Uses the
terminal for coding, git, system administration, and tooling. Has accumulated a fragmented set of bookmarking
workarounds (browser bookmarks, Slack DMs to self, markdown notes repos, shell script comments) because no tool fits the
terminal context where the pain occurs.

JTBD: "When I encounter a useful URL while working in the terminal, I want to save it and retrieve it instantly without
leaving my current context, so I can maintain flow and stop re-searching for things I've already found."

**Secondary Persona: CLI-Adjacent Data Scientist**
Uses the terminal for scripts and git but bookmarks via Notion or similar. Has the same link-loss problem but a
different primary workspace. Lower urgency. CLI tool may serve as secondary use case, not primary.

**Tertiary Persona: Security Researcher / SRE**
Security researchers and site-reliability engineers who live almost exclusively in the terminal across multiple machines
and environments. Regularly bookmarks CVE links, runbooks, vendor advisories, internal dashboards, and incident
post-mortems. Operates under strict data-sensitivity constraints — cloud-synced bookmarks are a compliance risk, not
merely a preference issue. Encounters the same context-switching pain as the primary persona but with higher urgency:
during an active incident or a security triage, losing a URL costs more than a few seconds of flow. Uses tmux, ssh
sessions, and scripted workflows. Has tried browser bookmarks and found them inaccessible from remote sessions. Values
local plain-text storage that can be version-controlled and audited.

JTBD: "When I find a CVE report, runbook, or vendor advisory during a live incident or security review, I want to save
it from my terminal instantly so I can retrieve it later from any session without relying on a browser or a cloud
service."

Evidence source: INT-06 (security researcher, Phase 2 opportunity validation). Confirmed pain frequency 4-6x/week.
Confirmed non-negotiable: local-only, no cloud storage. Stated current workaround: "I copy links into a private git repo
I keep on every machine. It's clunky but I trust it." Spontaneously described desired behavior matching the bookmark-cli
MVP concept.

Persona scope decision: Primary persona drives MVP requirements. Tertiary persona (security/SRE) reinforces local-first
constraint C-01 and adds urgency signal for fast-retrieval opportunity O1. Secondary persona is a nice-to-have and
should not compromise CLI-first design.

---

## Wave: DISCOVER / [REF-02] Opportunity Statement

**Validated Opportunity Statement:**
Terminal-heavy developers repeatedly lose productive time re-finding URLs they have already discovered, because no tool
lets them save and retrieve bookmarks within their existing terminal workflow — creating a fragmented landscape of
ineffective workarounds (browser bookmarks, markdown files, pasted Slack messages) that fail at the moment of need.

---

## Wave: DISCOVER / [REF-03] Validated Assumptions

Each item includes evidence reference and confidence level (High / Medium / Low).

**VA-01**: Developers lose track of useful links with measurable frequency.

- Evidence: 5/5 Phase 1 interviewees described specific, recent instances of re-searching for known links. Junior dev: "
  3-4 times a week." Senior dev: "Conservatively an hour a week." DevOps: incident story — 8 minutes lost during a live
  outage. Confirmed by 5/5 additional Phase 2 opportunity validation check-ins (INT-06 through INT-10): all described
  recurrent link-loss events with behavioral specifics.
- Confidence: HIGH

**VA-02**: Browser bookmarks are disorganized and fail at the point of need.

- Evidence: Junior dev: "I have like 400 bookmarks in Chrome. They're totally useless." OSS maintainer: "I have browser
  bookmarks organized by project but they go stale." Senior dev maintains a personal wiki but admits "I only update it
  when I remember, which is never when I'm in the middle of something."
- Confidence: HIGH

**VA-03**: Context switching to a browser breaks developer flow and is the primary pain driver.

- Evidence: Junior dev: "By the time I find what I needed I sometimes forget what I was building." OSS maintainer: "What
  I actually want is something I can do without leaving the context I'm in." DevOps: "Fast retrieval from the
  terminal... the friction of opening a file and searching it means I often just Google again instead."
- Confidence: HIGH

**VA-04**: Existing CLI alternatives (e.g., buku) are known but too complex to adopt.

- Evidence: OSS maintainer tried buku: "Too complex for what I needed. The setup was involved and I never fully adopted
  it. But the concept is right." This directly validates the market gap — desire exists, execution is wrong.
- Confidence: MEDIUM (single data point on buku specifically; concept is validated by 4/5)

**VA-05**: Local-first / no-account design is a non-negotiable for the primary persona.

- Evidence: Senior dev: "I wouldn't pay SaaS prices for bookmarks" and "If it required an account or sent my data
  anywhere [I wouldn't use it]." DevOps implied same through preference for local grep-able files.
- Confidence: HIGH

**VA-06**: Developers will use a tool that integrates with their existing shell patterns (fuzzy search, piping).

- Evidence: Senior dev: "I already use fzf for everything." DevOps: "I type a tag or a keyword and I get the URL with
  context." OSS maintainer described exact desired syntax spontaneously: `bm save <url> --tag 'rust async'`.
- Confidence: HIGH

**VA-07**: Open-source with optional sponsorship is a viable distribution and sustainability model.

- Evidence: OSS maintainer sponsors 12 tools at $5-10/month each. Senior dev: "If it was open source with a one-time
  install, I'd be on it in an hour." DevOps: "I'd probably contribute to it if it was open source." Precedent tools (
  fzf, bat, ripgrep) use this model successfully.
- Confidence: MEDIUM (sponsorship revenue uncertain; model viable at near-zero cost)

---

## Wave: DISCOVER / [REF-04] Invalidated Assumptions

**IA-01**: A CLI tool fits all developer workflows better than a browser extension.

- Evidence: Data scientist (Interview 4) does not naturally reach for CLI for URL management. "I wouldn't naturally
  reach for a CLI for finding a URL." Her workflow is Jupyter + browser-based, not terminal-primary.
- Implication: The assumption is too broad. CLI fits terminal-primary developers. Browser extension or Notion
  integration may be better for non-terminal-primary developers. The CLI tool should not attempt to serve both
  audiences — focus on terminal-primary.
- Evidence Reference: INT-04

**IA-02**: Developers will pay (SaaS/subscription) for a bookmark tool.

- Evidence: All interviewees who addressed pricing rejected SaaS model. Senior dev: "I wouldn't pay SaaS prices for
  bookmarks." OSS maintainer: "Nothing, honestly — I expect this kind of tool to be free/open source." Willingness to
  sponsor (one-time or recurring low amounts) exists but subscription pricing is a disqualifier.
- Implication: Monetization model must be open-source-first with optional sponsorship, not SaaS.
- Evidence Reference: INT-02, INT-05

**IA-03**: Developers do not already have a partial solution they are somewhat satisfied with.

- Evidence: Data scientist uses Notion with moderate satisfaction. DevOps uses a Git notes repo that "works okay." OSS
  maintainer pays $11/year for Pinboard. Some workarounds exist and are tolerated, not fully broken. The pain is real
  but the population is not uniformly desperate — terminal-primary devs show higher urgency than hybrid/browser-primary
  devs.
- Implication: Target specifically the highest-pain segment (terminal-primary). Do not over-index on total addressable
  market.
- Evidence Reference: INT-03, INT-04, INT-05

---

## Wave: DISCOVER / [REF-05] Dropped Options

**DO-01: Browser Extension**
Evaluated as alternative approach. Rejected because: (a) terminal-primary developers do not switch to browser during
coding sessions — the extension must be in the terminal to be at the point of need; (b) browser extension does not solve
the core pain of context switching; (c) 4/5 interviewees specifically cited terminal context as the desired location.
Status: Dropped. Applicable to secondary persona only, not in scope.

**DO-02: SaaS / Cloud-Synced Bookmark Service**
Evaluated as monetization and product approach. Rejected because: (a) explicit disqualifier stated by 2/5
interviewees; (b) local-first is a non-negotiable for privacy-conscious developers in the primary persona; (c) no cloud
infrastructure required reduces cost to near-zero; (d) existing tools (Pinboard, Raindrop.io) already occupy this space.
Status: Dropped. Cloud sync may be a future optional add-on, but must never be required.

**DO-03: Feature-Rich Bookmark Manager (buku-style)**
Evaluated based on OSS maintainer's direct experience with buku. Rejected as product direction because: complexity is a
primary adoption barrier. The opportunity is in radical simplicity — a tool that does 3 things well (save, tag, search)
rather than 20 things adequately. Status: Dropped as design direction. Simplicity-first is a design constraint.

**DO-04: Integration with Existing Note Tools (Notion, Obsidian)**
Evaluated given Data Scientist's Notion use. Rejected for primary scope because: adds integration complexity, creates
external dependencies, not relevant to terminal-primary workflow. May be explored as export/import feature in future
iterations. Status: Dropped from MVP. Backlog candidate.

---

## Wave: DISCOVER / [REF-06] Decision Gates

| Gate | Question                              | Criteria                                                                                             | Result                                                                                                                                                                                                                               | Evidence                                      |
|------|---------------------------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|
| G1   | Is this a real problem worth solving? | 5+ interviews, >60% confirm pain, customer words documented                                          | PASS — 5/5 (100%) confirmed link-loss pain with specific behavioral examples; verbatim quotes captured                                                                                                                               | INT-01 through INT-05                         |
| G2   | Who specifically has this problem?    | Persona identified, JTBD mapped, segment by behavior not demographics; 10+ cumulative interviews     | PASS — 3 distinct personas defined (terminal-first dev, CLI-adjacent data scientist, security researcher/SRE); JTBD mapped; 10 interviews total (5 Phase 1 + 5 Phase 2 opportunity validation check-ins INT-06 through INT-10)       | OST, INT-01-10                                |
| G3   | Is CLI the right solution space?      | CLI validated vs. alternatives; 5+ users tested; >80% task completion                                | PASS — 93% task completion across 7 prototype test participants (2 iterations: 4 users in iteration 1, 3 users in iteration 2); CLI explicitly preferred by 4/5 Phase 1 interviewees; browser extension and SaaS dropped on evidence | Prototype test results, INT-P1 through INT-P7 |
| G4   | Is the opportunity worth pursuing?    | Lean Canvas complete; all 4 risks green/yellow; viable model exists; stakeholder sign-off documented | PASS — All risks green except viability (yellow, acceptable); open-source model validated by precedent and sponsor intent; stakeholder review conducted 2026-06-05 (see wave-decisions.md D-06)                                      | Lean Canvas                                   |

**Overall Discovery Decision: GO**

---

## Wave: DISCOVER / [REF-07] Constraints Established

**C-01: Local-first storage is mandatory.**
No cloud sync, no account required. Evidence: 2/5 explicitly stated this as disqualifier. Confirmed by privacy-first
sentiment in 3/5 interviews.

**C-02: Single-command save is the core UX constraint.**
Any save workflow requiring more than one command at the terminal is an adoption barrier. Evidence: OSS maintainer
described exact expected syntax; DevOps described friction of multi-step tools; Pinboard abandonment attributed to
friction.

**C-03: No SaaS pricing.**
Free or open-source core is table stakes for the primary persona. Optional sponsorship acceptable. Subscription model
will block adoption.

**C-04: Complexity ceiling.**
Must be simpler than buku in setup and daily use. Complexity = adoption failure. Evidence: OSS maintainer's direct buku
experience; DevOps preference for grep-based simplicity.

**C-05: CLI-first, terminal-native.**
Tool must work entirely within the terminal context. No browser window required for core workflow. Evidence: 4/5
interviews cited terminal context as primary location of pain.

---

## Wave: DISCOVER / [REF-08] Pre-requisites

**P-01: Define storage format before any implementation.**
Local-first implies a file format decision (plain text, SQLite, JSON). This is a feasibility prerequisite. Plain text (
e.g., delimited format or markdown-style) is preferred by DevOps persona who values grep-ability. SQLite enables better
search without external dependencies.

**P-02: Define shell integration strategy.**
Fuzzy search (fzf-style) is desired by senior developer and DevOps personas. Must decide whether to depend on fzf or
build native fuzzy matching. This is a dependency decision that affects installation complexity.

**P-03: Package manager distribution plan required before launch.**
Senior dev: "One-time install." OSS maintainer expects Homebrew/package manager availability. Distribution via Homebrew,
apt, and package managers must be planned before release.

**P-04: Validate naming and command syntax with 3+ target users.**
OSS maintainer spontaneously suggested `bm save <url> --tag`. This is one data point. Command syntax should be validated
with at least 3 additional developers before finalizing CLI interface.

**P-05: Define scope boundary for MVP explicitly.**
Three features validated: save, tag, search. All other features (export, sync, team sharing, Notion integration) are
explicitly out of MVP scope to prevent scope creep and maintain simplicity constraint C-04.
