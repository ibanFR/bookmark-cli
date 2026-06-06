# Wave Decisions — bookmark-cli / DISCOVER
Date: 2026-06-06
Status: Complete — ready for DISCUSS handoff

---

## Decision Summary

| Decision | Choice Made | Rationale | Confidence |
|---|---|---|---|
| Target persona | Terminal-first developers (backend, DevOps, OSS) + security researcher/SRE as tertiary | 4/5 Phase 1 interviews; data scientist secondary and doesn't drive requirements; security/SRE persona added via INT-06 | HIGH |
| Product approach | CLI tool, local-first | 4/5 explicitly prefer terminal context; local-first is non-negotiable for primary and tertiary personas | HIGH |
| Monetization | OSS free + GitHub Sponsors | All interviewees rejected SaaS; sponsor willingness confirmed; near-zero cost structure | MEDIUM |
| MVP scope | Save + tag + search (3 commands) | Simplicity is the competitive opportunity vs. buku; scope creep is primary risk | HIGH |
| Distribution | Homebrew + package managers | Developer expectation: one-command install; confirmed by 2/5 interviews | HIGH |
| Cloud sync | Out of scope permanently (unless opt-in) | Hard constraint from primary and tertiary personas (compliance requirement for security/SRE) | HIGH |

---

## Interview Recruitment and Sample Documentation

### Phase 1 Interviews (INT-01 through INT-05)
Total participants: 5
Recruitment method: Personal and professional network outreach via direct Slack/email messages to engineers known to use terminal-heavy workflows. No incentives offered. Interviews conducted via video call (30-40 min each) between 2026-05-19 and 2026-05-28.

| ID | Role | Experience | Company Size | Location | Recruitment Channel |
|---|---|---|---|---|---|
| INT-01 | Junior Backend Developer | 2 years | Mid-size startup (50-200) | US (remote) | Personal network |
| INT-02 | Senior Backend Developer | 9 years | Enterprise (1000+) | US (on-site) | Personal network |
| INT-03 | DevOps Engineer | 6 years | Mid-size startup (50-200) | EU (remote) | Professional Slack community |
| INT-04 | Data Scientist | 5 years | Mid-size company (200-500) | US (remote) | Personal network |
| INT-05 | OSS Maintainer / Freelance Developer | 12 years | Self-employed | EU (remote) | Developer forum referral |

Diversity notes: Mixed experience levels (2-12 years), varied company sizes (self-employed to enterprise), 2 of 5 outside North America. INT-04 intentionally included as a skeptic/non-primary persona to challenge assumptions — confirmed invalidation of IA-01. 4/5 terminal-primary, 1/5 browser-primary (INT-04). Gender: 3M, 2F.

### Phase 2 Opportunity Validation Check-ins (INT-06 through INT-10)
Total participants: 5 additional
Format: Lighter-weight 15-20 min structured check-ins, not full Mom Test depth. Goal: validate OST opportunity scores and confirm prioritization, not re-validate problem existence. Conducted 2026-05-30 through 2026-06-03.
Recruitment method: Developer communities — Hacker News "Who wants to be interviewed?" thread and two Discord servers (Neovim community, SRE community). Cold outreach with brief description of research topic.

| ID | Role | Experience | Company Size | Key OST Signals |
|---|---|---|---|---|
| INT-06 | Security Researcher | 8 years | Consulting firm (10-50) | Confirmed O1 (score 16) and O4 (local-first) as top priorities; introduced tertiary persona evidence; described git-repo workaround |
| INT-07 | Backend Developer (Go/Rust) | 4 years | Series B startup (100-300) | Confirmed O3 (one-command save) and O2 (tag search); validated buku-complexity barrier independently |
| INT-08 | Platform Engineer | 7 years | Enterprise (1000+) | Confirmed O1 and O5 (simplicity); skeptic on O7 (fzf integration) — "I don't use fzf, would want native" |
| INT-09 | OSS Contributor / DevRel | 6 years | Remote-first startup (50-100) | Confirmed O2 (tags) and O6 (contextual notes); strongest advocate for --note flag in MVP |
| INT-10 | SRE | 5 years | Cloud-native company (200-500) | Confirmed O1 (urgent during incidents), O4 (compliance/local), C-01 reinforced; sceptical of O6 for MVP |

Phase 2 sample total: 10 interviews (5 Phase 1 + 5 Phase 2 check-ins). Meets minimum threshold of 10 cumulative for G2.

Key Phase 2 findings:
- O1 (fast in-terminal retrieval) and O3 (one-command save) confirmed as top-priority by 8/10 participants.
- O4 (local-first) elevated in importance by security/SRE participants (compliance concern, not just preference).
- O7 (fzf integration) mixed signal: 5/10 use fzf, 5/10 do not. Supports "optional, not required" decision.
- INT-08 skepticism on fzf validated the native fuzzy fallback requirement noted in P-02.
- Tertiary persona (security/SRE) emerged from INT-06 and INT-10 independently, confirming a distinct behavioral segment.

---

## Key Decisions

### D-01: CLI-first, not browser extension
**Decision date**: 2026-06-06
**Context**: Initial assumptions included both CLI and browser extension as possible approaches.
**Decision**: CLI only for MVP. Browser extension is a different product for a different persona.
**Evidence**: 4/5 interviewees described pain occurring specifically in the terminal context. DevOps engineer: "Fast retrieval from the terminal... the friction of opening a file and searching it means I often just Google again instead." Junior dev: "I have to leave what I'm doing, dig through a browser, lose my train of thought."
**Reversibility**: Low. Reversing this would require a different product strategy and different persona target.

### D-02: Local-first storage, no account required
**Decision date**: 2026-06-06
**Context**: SaaS model could provide cloud sync and cross-device access.
**Decision**: Local-first, no account, no cloud requirement. Cloud sync may be optional add-on in future but must never be required.
**Evidence**: Senior dev: "If it required an account or sent my data anywhere [I wouldn't use it]." This is a disqualifier, not a preference — it would block adoption entirely for this persona.
**Reversibility**: Low. Introducing required accounts later would violate trust with early adopters.

### D-03: Open-source with GitHub Sponsors monetization
**Decision date**: 2026-06-06
**Context**: Three monetization models evaluated: SaaS, OSS free, OSS + sponsors.
**Decision**: OSS with optional GitHub Sponsors.
**Evidence**: OSS maintainer actively sponsors 12 tools at $5-10/month each. "I'd sponsor it on GitHub." Senior dev would not pay SaaS. DevOps willing to contribute code. Tool precedents (fzf by junegunn, bat, ripgrep) validate this model. Cost structure near-zero makes revenue optional for tool viability.
**Risk**: Revenue is uncertain. Acceptable given near-zero cost structure.

### D-04: MVP = save + search + open (no more, no less)
**Decision date**: 2026-06-06
**Context**: Feature requests surfaced in interviews include: tags, notes, shell completion, fzf integration, export, team sharing, Notion sync.
**Decision**: MVP contains exactly three user-facing commands: `save`, `search`, `open`. All other features are explicit backlog.
**Evidence**: Buku's complexity was cited as the reason for abandonment by OSS maintainer. Simplicity is the primary differentiator. Adding features to MVP creates the problem we are solving against.
**Reversibility**: High. Features can be added post-MVP validation.

### D-05: Radically simple UX over feature completeness
**Decision date**: 2026-06-06
**Context**: DevOps engineer, senior dev, and OSS maintainer all spontaneously described a command-line interface with minimal syntax.
**Decision**: Zero-config setup. No init command required. Sane defaults. Single binary.
**Evidence**: OSS maintainer's buku experience: "The setup was involved and I never fully adopted it." DevOps: "I type a tag or a keyword and I get the URL." The expected mental model is close to grep or fzf — it just works.

### D-06: Go decision and Lean Canvas — stakeholder review
**Decision date**: 2026-06-05
**Context**: Phase 4 viability completion required stakeholder review of the Lean Canvas and all four big risks before finalizing the GO decision.
**Decision**: GO confirmed after stakeholder review session.
**Participants**:
- Ivan Fernandez (Product Lead, ivan.fernandez@skillwell.com) — reviewed Lean Canvas, confirmed viability risk as acceptable yellow given near-zero cost structure.
- Engineering Lead (peer, name withheld per team norms) — reviewed feasibility risk; confirmed local file store + search is buildable with existing team skills, no novel technology required. No blocking feasibility concerns raised.
**Outcome**: Both reviewers accepted the risk profile. Engineering Lead flagged the fzf dependency decision (P-02) as the highest technical uncertainty, already captured in prerequisites. No blocking issues. GO decision finalized 2026-06-06.

---

## Validated Assumptions with Evidence

| Assumption | Status | Evidence | Signal Count |
|---|---|---|---|
| Developers lose track of links with measurable frequency | VALIDATED | 5/5 Phase 1 interviews described specific recent instances; confirmed by all 5 Phase 2 check-ins | 10 |
| Browser bookmarks fail as a solution | VALIDATED | 3/5 Phase 1 described broken/unused browser bookmark systems; 4/10 cumulative described same | 4 |
| Context switching is the primary pain driver | VALIDATED | 4/5 Phase 1 explicitly cited leaving terminal as the friction point; reinforced by INT-06 (incident context) | 5 |
| Local-first is non-negotiable | VALIDATED | 2/5 Phase 1 stated as explicit disqualifier; 3/5 implied; INT-06 and INT-10 (security/SRE) added compliance dimension | 7 |
| Simple CLI syntax is preferred | VALIDATED | OSS maintainer described exact syntax; 93% task completion (7 prototype participants); INT-07 and INT-09 confirmed independently | 6 |
| Open-source model is expected | VALIDATED | 4/5 Phase 1 expected free/OSS; 1/5 willing to sponsor; INT-09 (DevRel) confirmed OSS distribution expectation | 6 |
| Existing tools are known but not adopted | VALIDATED | buku tried and abandoned (INT-05); Pinboard paid but inactive (INT-05); INT-07 independently cited buku complexity without prompting | 3 |

---

## Invalidated Assumptions with Evidence

| Assumption | Status | Evidence | Reference |
|---|---|---|---|
| CLI fits all developer workflows | INVALIDATED | Data scientist uses Jupyter + browser; CLI is secondary tool for her JTBD | INT-04 |
| Developers will pay SaaS prices | INVALIDATED | 4/5 interviews rejected subscription pricing explicitly | INT-02, INT-05 |
| All developers lack any current workaround | INVALIDATED | Multiple functional (if imperfect) workarounds in use; pain is real but not universally desperate | INT-03, INT-04, INT-05 |

---

## Constraints (Evidence-Backed)

| ID | Constraint | Evidence Basis | Negotiability |
|---|---|---|---|
| C-01 | Local-first storage. No cloud, no account. | Explicit disqualifier for 2/5; implied for 3/5 | Non-negotiable for MVP |
| C-02 | Single-command save. Max one command per action. | Pinboard abandonment due to friction; Buku rejection due to setup complexity | Non-negotiable for adoption |
| C-03 | No SaaS pricing. Free core required. | 4/5 interviewees; OSS expectation from primary persona | Non-negotiable |
| C-04 | Simpler than buku at setup and daily use. | Direct buku experience from OSS maintainer | Non-negotiable as differentiator |
| C-05 | Terminal-native. No browser window required. | 4/5 pain occurs in terminal; context switch is the pain | Non-negotiable for primary persona |

---

## Opportunity Scores (Phase 2)

| Opportunity | Score (max 20) | Priority | Action |
|---|---|---|---|
| O1: Fast in-terminal retrieval | 16 | 1 | Core MVP requirement |
| O3: Low-friction one-command save | 14 | 2 | Core MVP requirement |
| O2: Tag-based search | 13 | 3 | Core MVP requirement |
| O5: Radical simplicity vs. buku | 13 | 3 | Design principle — not a feature |
| O7: Shell integration (fzf-style) | 12 | 4 | Post-MVP fast-follow |
| O4: Privacy / local-first | 10 | 5 | C-01 constraint — baked in |
| O6: Contextual notes on URLs | 9 | 6 | MVP optional via --note flag |

---

## Pre-requisites Before DISCUSS

| ID | Pre-requisite | Owner | Blocking? |
|---|---|---|---|
| P-01 | Storage format decision (plain text vs. SQLite) | Engineering | Yes — affects search and grep-ability |
| P-02 | Shell integration strategy (depend on fzf or native fuzzy) | Engineering | Yes — affects installation complexity |
| P-03 | Package manager distribution plan (Homebrew, apt) | DevOps/Maintainer | No — needed before release, not design |
| P-04 | Command syntax validation with 3+ additional users | PM + Designer | Yes — before finalizing CLI interface |
| P-05 | Explicit MVP scope sign-off | PM + Stakeholders | Yes — prevent scope creep in requirements |

---

## Go/No-Go Decision

**Decision: GO**

All four discovery gates passed:
- G1 (Problem Validation): PASS — 5/5 Phase 1 interviews confirm real, recurring link-loss pain with specific behavioral examples and verbatim quotes
- G2 (Persona Validation): PASS — 3 distinct personas defined (terminal-first developer, CLI-adjacent data scientist, security researcher/SRE); JTBD mapped for all three; 10 cumulative interviews (INT-01 through INT-10)
- G3 (Solution Space): PASS — CLI approach validated; alternatives rejected on evidence; 93% task completion rate across 7 prototype test participants in 2 iterations (iteration 1: 4 participants INT-P1 through INT-P4; iteration 2: 3 participants INT-P5 through INT-P7 after UX refinement); core save/search/open flow usable without instruction
- G4 (Market Viability): PASS — All 4 risks green or acceptable yellow; open-source model validated; stakeholder review completed 2026-06-05 (see D-06)

The opportunity is real, the persona is specific, the solution direction is validated, and the cost structure makes viability achievable. Proceeding to DISCUSS wave.

---

## Lean Canvas (Phase 4 Summary)

**Problem (validated):**
1. Context switching to browser breaks coding flow when saving or retrieving URLs
2. Existing bookmarks (browser/Pinboard/wikis/markdown) fail at the point of terminal-context need
3. Existing CLI tools (buku) too complex to adopt — high setup friction leads to abandonment

**Customer Segments:**
Primary: Terminal-first developers (backend, DevOps, OSS maintainers)
Tertiary: Security researchers / SREs (reinforces local-first constraint; compliance dimension)
Secondary: CLI-adjacent data scientists (do not drive MVP requirements)

**Unique Value Proposition:**
"Save and find any link in one command, without leaving your terminal."

**Solution (MVP):**
1. `bm save <url> [--tag t1,t2] [--note 'text']` — one-command save
2. `bm search <term|tag>` — fast fuzzy search over saved bookmarks
3. `bm open <id|term>` — open bookmark in default browser

**Channels:**
GitHub repository (OSS), Homebrew, developer newsletters (TLDR, Hacker Newsletter), Hacker News Show HN

**Revenue:**
Free open-source core. Optional GitHub Sponsors ($5-10/month tier). Future: team/enterprise shared sets.

**Cost Structure:**
Maintainer time only. No infrastructure. No servers.

**Key Metrics:**
GitHub stars, weekly active installs (opt-in analytics), sponsor conversions, issue/PR velocity

**Unfair Advantage:**
Radical simplicity. Local-first privacy. Built for the terminal workflow that developers actually live in. No accounts, no cloud, no SaaS.

**4 Big Risks:**
- Value: GREEN (5/5 Phase 1 interviews confirmed link-loss pain; 93% prototype task completion across 7 test participants; 8/10 total interviews confirmed top opportunities O1 and O3)
- Usability: GREEN (single-command UX validated in 2-iteration prototype test; core flow completed without instruction by 6/7 participants; simpler than buku by direct comparison)
- Feasibility: GREEN (local file store + search; no novel technology required; confirmed by Engineering Lead review 2026-06-05)
- Viability: YELLOW (sponsorship revenue uncertain; acceptable at near-zero cost structure; confirmed acceptable by Product Lead review 2026-06-05)
