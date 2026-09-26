# What's left: a foundation map (and the other side of every idea)

> **As of 26 Sep 2026.** Written for a self-taught Python and Docker learner who has never worked in a software firm, wants "a foundation of steel" before building something big, and asked to be contradicted. Every belief gets its best case and its strongest counter-argument.
>
> **About the sources.** Most vendor sites (artificialanalysis.ai, pi.dev, workos.com, openrouter.ai, isc2.org, comptia.org, youtube.com and others) were blocked from the machine the research ran on. Pages hosted on GitHub could be read directly: GitHub's own docs source, the OWASP repos, CVE records, and the Pi and OpenClaw repos. Everything else came from search results and mirrors, and a second agent tried to refute each risky claim.
>
> **Confidence.** A plain statement comes from a primary source: vendor docs, a spec, a repo or a CVE record. **"(reported)"** means it was seen only in news, search snippets or mirrors, so it is likely but not confirmed. Prices and dates move fast, so open the live page before spending money.
>
> **Tickets.** Every "do this" below is a GitHub issue under the tracking issue [#2](https://github.com/jkv8fwytbf-byte/ToDo/issues/2). Start with [#3](https://github.com/jkv8fwytbf-byte/ToDo/issues/3).

---

## Contents

0. [TL;DR](#0-tldr)
1. [Your side vs the other side](#1-your-side-vs-the-other-side)
2. [What you already have](#2-what-you-already-have)
3. [The software-firm words, in plain language](#3-the-software-firm-words-in-plain-language)
4. [The names you heard](#4-the-names-you-heard)
5. [The friend's list, triaged](#5-the-friends-list-triaged)
6. [The forks: certificate, startup, job, or keep building](#6-the-forks-certificate-startup-job-or-keep-building)
7. [Money rules](#7-money-rules)
8. [The 8-week map](#8-the-8-week-map)
9. [Glossary](#9-glossary)
10. [Sources](#10-sources)

---

## 0. TL;DR

1. **You are closer than you think.** You have already used issues (tickets), pull requests, a protected `main` branch, labels, milestones, tags and a release, in [fundamentals-of-finance](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance). Its guide, written on 14 Sep 2026, already explains pull requests, GitHub Actions, TypeScript for a Python brain, how to read AI leaderboards, sub-agents, security habits, and how to learn with AI without becoming dependent on it. Most of "what's left" is *doing*, not *finding out*.
2. **The real gaps are a short list:** worktrees, sandboxes, a CI pipeline you built yourself, reading TypeScript, how the web works from DNS to OAuth, AI-agent security, and one finished project. Fifteen tickets cover them in eight weeks.
3. **The steel comes from building and breaking small things yourself.** More courses, certificates, feeds or tools will not provide it. The evidence is in [§1](#1-your-side-vs-the-other-side).
4. **Safety and money first.** Cap every AI account ([#4](https://github.com/jkv8fwytbf-byte/ToDo/issues/4)), and check any agent that is connected to your accounts ([#5](https://github.com/jkv8fwytbf-byte/ToDo/issues/5)).
5. **Leaderboards are a map, not a god.**
6. **Don't buy a certificate yet, and don't start a company yet.** Decide around 21 Nov 2026 with eight weeks of evidence ([#17](https://github.com/jkv8fwytbf-byte/ToDo/issues/17)).

---

## 1. Your side vs the other side

Each belief below gets four parts: the belief as you put it, where you are right, the other side, and a verdict.

### 1.1 "The coding-agent leaderboard is a god"

**Where you're right.** Artificial Analysis's [Coding Agent Index](https://artificialanalysis.ai/agents/coding-agents) is one of the best free public signals of which agent-and-model pair finishes realistic coding tasks. It also shows **cost per task** and **time per task**, not just a score. Hardly anyone else publishes that.

**The other side.**
- **It scores a harness plus a model, not a model.** A *harness* is the tool wrapped around the model: its prompts, file access and permission gates. Version 1.5 is an equal average of three benchmarks: DeepSWE v1.1 (113 tasks), Terminal-Bench 4.0 (66 tasks) and SWE-Atlas-QnA (124 questions, graded with Claude Opus 4.5 as the judge). Each task is attempted three times. (reported)
- **The top is crowded, and the top costs money.** As of 25 Sep 2026 the leader is Claude Code with Opus 5.5 at max effort: 66 points, about USD 13 and about 64 minutes per task. (reported) A one- or two-point gap on about 300 pass/fail tasks is probably noise.
- **The index keeps changing.** Artificial Analysis dropped SWE-Bench Pro after models learned to recover fixes from commit history, and the ranking flipped. (reported)
- **Benchmarks get gamed.** OpenAI stopped reporting SWE-bench Verified in 2026, calling it contaminated. (reported) UC Berkeley researchers built an agent that scored near 100% on several agent benchmarks without solving the tasks, by exploiting the graders. (reported)
- **The wrapper can matter more than the model.** On ARC-AGI-3, GPT-6 Astra scored 62.7% with ARC Prize's standard harness and 98.6–99.9% with a provider-tuned one. (reported, ARC Prize, 3 Sep 2026)
- **What you use may not be on it.** Pi and OpenClaw are not on the index at all. (reported)

**Verdict.** Use it as a **map**: to pick the cheapest model that is good enough for a task, and to compare cost per task. Do not use it as a reason to upgrade. Guide chapter 7, [OpenRouter and benchmark literacy](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/07-openrouter-and-benchmark-literacy.md), already lists "the five traps" of reading these pages.

### 1.2 "Without agentic-workflow knowledge I'm useless"

**Where you're right.** In 2026 most software is built with agents in the loop. Not knowing what a ticket, a PR, CI or a worktree is would be a real gap on day one in a firm.

**The other side.**
- **The words take days, not months.** [§3](#3-the-software-firm-words-in-plain-language) covers them in an afternoon, and [§2](#2-what-you-already-have) shows you have already used most of them.
- **The scarce skill is reading, testing and judging code.** In METR's 2025 randomised trial, 16 experienced open-source developers were **19% slower** with AI tools while *believing* they were about 20% faster. In February 2026 METR said its newer data is "very weak evidence" either way, because developers refused to work without AI. (reported)
- **For learners the risk is sharper.** In a 2026 Anthropic study, 52 mostly junior engineers learned a new Python library. The group using AI scored **17% lower** on a quiz about the concepts they had just used. Those who handed code generation to the AI scored under 40%; those who asked it conceptual questions scored 65% or more. ([Anthropic](https://www.anthropic.com/research/AI-assistance-coding-skills), reported) It was a small, short study on one library.
- **Spending is not a skill.** Anthropic's cost docs put typical enterprise Claude Code spend at about **USD 150–250 per developer per month**. (reported)

**Verdict.** Learn the vocabulary this month. After that, put your effort into reading and judging code. Let AI explain and review, and write the core logic yourself (guide chapter 0, rule 1: "attempt, then ask").

### 1.3 "You no longer need JavaScript or TypeScript"

**Where you're right.** Agents write passable TypeScript (TS), and the Python world (FastAPI, LangGraph, the MCP Python SDK) is big enough to build a career on. You do not need to become a front-end developer.

**The other side.**
- **TS is everywhere.** In August 2025 it became the most-used language on GitHub by monthly contributors ([Octoverse 2025](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/), reported).
- **Your tools are TS.** Pi is written in TypeScript ([repo](https://github.com/earendil-works/pi)), OpenClaw is TypeScript (reported), and so are many MCP servers and skills.
- **You can't review or security-check what you can't read.** The top developer frustration in Stack Overflow's 2025 survey, at 66%, was AI answers that are "almost right, but not quite". (reported)

**Verdict.** Aim for **reading level, not writing fluency** ([#13](https://github.com/jkv8fwytbf-byte/ToDo/issues/13)). Guide chapter 4, [TypeScript for a Python brain](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/04-typescript-for-a-python-brain.md), is the shortcut.

### 1.4 "Foundation of steel first, then build"

**Where you're right.** Without fundamentals you cannot tell when an agent is wrong. The goal is exactly right.

**The other side.** Fundamentals learned only from courses rarely stick, and collecting more courses is the trap people call "tutorial hell". Steel is forged by bending it: things you built, broke and fixed yourself. The people you follow say the same ([§1.7](#17-the-people-i-follow)).

**Verdict.** Learn fundamentals *by* building small things. Every ticket on the map ends with something you did, not something you watched.

### 1.5 "A cybersecurity certificate, for the age of AI and subagents"

**Where you're right.** AI-agent security is real, growing, and fits a Python, Docker and Linux background. It covers prompt injection, tool permissions, sandboxes, the supply chain of plugins, skills and MCP servers, and secrets. OWASP published the **Top 10 for LLM Applications, 2026 edition** on 4 Aug 2026 ([repo](https://github.com/GenAI-Security-Project/GenAI-LLM-Top10)), alongside a Top 10 for Agentic Applications and an [Agentic Skills Top 10](https://github.com/OWASP/www-project-agentic-skills-top-10) that names OpenClaw and Claude Code skills. Real incidents keep coming: in 2026, sandbox escapes in Cursor that needed no click from the user (CVE-2026-50548 and CVE-2026-50549, fixed in Cursor 3.0), and a critical allowlist bypass in OpenClaw (CVE-2026-28363, CVSS 9.9, fixed in `2026.2.23`).

**The other side.**
- **A certificate alone rarely lands an entry-level job,** and very few entry-level job ads say "AI agent security". Most of the real work is ordinary application security, which you would need to learn anyway.
- **The AI-security credentials are one to two years old,** with almost no hiring track record, especially in India. Several of them assume OSCP-level experience or an existing CISM or CISSP. (reported)
- **Timing traps.**
  - ISC2's free "One Million Certified in Cybersecurity" offer closed to new signups on 20 May 2026. Existing codes must be used by 31 Dec 2026. (reported)
  - A Security+ version with LLM content (SY0-801) is **rumoured** for late 2026, but CompTIA has not confirmed a date. The certificate is valid for three years whichever version you pass. (reported)
- **Frontier models increasingly refuse offensive-security work,** so "doing cyber with subagents" has limits. (reported)

**Verdict.** Do tickets [#5](https://github.com/jkv8fwytbf-byte/ToDo/issues/5), [#11](https://github.com/jkv8fwytbf-byte/ToDo/issues/11) and [#15](https://github.com/jkv8fwytbf-byte/ToDo/issues/15) first. They cost ₹0 and produce write-ups. Choose a certificate only in [#17](https://github.com/jkv8fwytbf-byte/ToDo/issues/17), using the table in [§6](#6-the-forks-certificate-startup-job-or-keep-building).

### 1.6 "Maybe a startup"

**Where you're right.** A solo MVP (minimum viable product) is cheaper to build than ever. Indian living costs make a long runway possible if spending is controlled, and some people learn fastest from customers.

**The other side.**
- **Most startups die of no demand, not missing tools.** In CB Insights' post-mortems, "no market need" is among the top causes. Running out of cash is usually the last event, not the cause. (reported)
- **The classic solo failures** are building without users, hopping between tools, and paying for subscriptions instead of talking to customers.
- **Some spaces are already crowded.** "AI content for marketing" has well-funded players, such as Typeface, valued at USD 1B in 2023. (reported)

**Verdict.** Not a company, and not now. At most, a 6–8 week validation sprint with a kill rule written in advance, after the build ticket and only if you choose it in [#17](https://github.com/jkv8fwytbf-byte/ToDo/issues/17).

### 1.7 "The people I follow"

**Where you're right.** ThePrimeagen and Mo Bitar are worth following. Both are sceptical of hype, and both are funny.

**The other side.** Both argue *for* the foundation and *against* tool-chasing:
- **ThePrimeagen is not anti-AI.** He builds his own AI agent for Neovim, "[99](https://github.com/ThePrimeagen/99)", which its README describes as "meant to augment the programmer" instead of "being a replacement". His objection is to outsourcing your understanding, not to the tools.
- **Mo Bitar** made the video "I was a 10x engineer. Now I'm useless." He argues that pure prompt-driven coding will become "a minimum wage job", and advises you to "develop real skills that will only be multiplied as AI continues to be better". (reported, from transcripts)
- **Both are commentators with products and audiences.** Mo Bitar is building an AI-coding product himself. (reported) Their predictions are opinions, not data.

**Verdict.** Take their advice, not their tone.

### 1.8 "I need more resources"

**Where you're right.** The list you were given is good; several items on it are excellent.

**The other side.** The whole list is about **330–650 hours**, mostly watching and reading, and one item on it is illegal to use in India ([§5](#5-the-friends-list-triaged)).

**Verdict.** Keep three and park the rest ([#12](https://github.com/jkv8fwytbf-byte/ToDo/issues/12)).

---

## 2. What you already have

In [fundamentals-of-finance](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance) (public), you have already used:

| Firm practice | Where you already did it |
|---|---|
| Tickets | About twenty [issues](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/issues?q=is%3Aissue), with labels and milestones M0–M5 |
| Pull requests | [PRs](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/pulls?q=is%3Apr) #11–#18 and #21; **[#19](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/pull/19) is waiting for you to merge it** |
| Protected main branch | `main` requires a PR and forbids force-pushes and deletion |
| Releases and tags | A [release](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/releases) carrying PDFs, and tags for each plan version |
| A decisions log | `docs/decisions/` |

Its guide (humans read the PDF, [`4-the-guide.pdf`](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/output/pdf/4-the-guide.pdf)) already covers much of what you asked about:

| Guide chapter | What it already explains | Ticket |
|---|---|---|
| [0. How to learn with an AI coding tool](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/00-how-to-learn-with-an-ai-tool.md) | Ten rules against dependence, each with a 5-minute exercise | [#6](https://github.com/jkv8fwytbf-byte/ToDo/issues/6) |
| [1. Git, GitHub, branches, pull requests and CI checks](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/01-git-github-and-pull-requests.md) | The daily loop, as commands | [#3](https://github.com/jkv8fwytbf-byte/ToDo/issues/3), [#7](https://github.com/jkv8fwytbf-byte/ToDo/issues/7) |
| [3. GitHub Actions](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/03-github-actions.md) | A workflow file piece by piece, secrets, schedule gotchas, minutes | [#8](https://github.com/jkv8fwytbf-byte/ToDo/issues/8), [#9](https://github.com/jkv8fwytbf-byte/ToDo/issues/9) |
| [4. TypeScript for a Python brain](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/04-typescript-for-a-python-brain.md) | The eight things that bite, and a translation table | [#13](https://github.com/jkv8fwytbf-byte/ToDo/issues/13) |
| [7. OpenRouter and benchmark literacy](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/07-openrouter-and-benchmark-literacy.md) | Reading Artificial Analysis, and "the five traps" | [#4](https://github.com/jkv8fwytbf-byte/ToDo/issues/4), [#12](https://github.com/jkv8fwytbf-byte/ToDo/issues/12) |
| [10. OCR, crawling and sub-agents](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/10-ocr-crawling-and-sub-agents.md) | When sub-agents help, and when to leave them in the drawer | [#10](https://github.com/jkv8fwytbf-byte/ToDo/issues/10) |
| [14. Security hygiene for a solo builder](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/14-security-hygiene.md) | 2FA, tokens, push protection, secret scanning, key caps | [#4](https://github.com/jkv8fwytbf-byte/ToDo/issues/4), [#15](https://github.com/jkv8fwytbf-byte/ToDo/issues/15) |
| [17. Glossary](https://github.com/jkv8fwytbf-byte/fundamentals-of-finance/blob/main/docs/guide/17-glossary.md) | Every term in the guide, in plain words | — |

The guide was written for the valuation project and names Cursor as the editor. The ideas transfer unchanged to Claude Code or any other agent.

**The other side of this section.** Having used something once, with an agent doing most of the typing, is not the same as owning it. That is why the tickets make you do each loop again, by hand, on something small.

---

## 3. The software-firm words, in plain language

Each term gets a one-line definition, then the ticket where you practise it and the other side, meaning when it is overkill for one person. Where guide chapters 1, 3 and 17 already explain a term well, this section links there instead of repeating it.

### 3.1 Tracking work

- **Ticket / issue.** A written unit of work (a bug, a feature, a chore) with an owner, a status and a discussion thread. Every branch and PR links back to it. In 2026 a clear ticket is also the prompt you hand to a coding agent. *Practise:* [#7](https://github.com/jkv8fwytbf-byte/ToDo/issues/7). *Other side:* the skill is writing a ticket small and precise enough that a stranger can finish it; picking a tracker is not the skill.
- **GitHub Issues vs Linear vs Jira.**
  - **GitHub Issues** live next to the code. They are free and now have sub-issues and project boards with sprint ("iteration") fields.
  - **Linear** is a fast, opinionated tracker for product teams. It has *cycles* (sprints), *triage*, *projects*, and IDs like `ENG-123` that link to GitHub PRs automatically. Its free plan allows 2 teams and 250 issues (reported). Its agent features, such as coding sessions that run Claude Code or Codex from an issue, are metered and need a paid plan, and the launch promotion credits have ended (reported).
  - **Jira** is the heavily configurable enterprise default; its free plan covers up to 10 users (reported).
  - *Other side:* Linear is mostly a startup tool. Services firms and global capability centres (GCCs) in India more often run Jira or Azure DevOps. That is an inference, not verified, but it suggests learning Jira's words (epic, story, sub-task, board) in an evening and paying for neither.
- **Epic / tracking issue / sub-issue.** A big goal split into child tickets. [#2](https://github.com/jkv8fwytbf-byte/ToDo/issues/2) is one.
- **Sprint and standup.** A sprint (Linear calls it a cycle) is a fixed time-box, usually 1–2 weeks. A standup is a 15-minute daily sync: done, next, blocked. *Other side:* many engineers mock these rituals as overhead. Alone, a standup is a journal. Never pay for a Scrum certificate.

### 3.2 Moving code

- **Branch.** A named line of commits, so you can change code without touching `main`. In firms: one short-lived branch per ticket, deleted after merge.
- **Commit.** A saved snapshot with an ID (SHA), an author and a message that says *why*.
- **Pull request (PR).** A proposal to merge one branch into another: a diff, a discussion thread, check results and reviews. It is where coding agents deliver their work too. A **draft** PR means work in progress.
- **Code review.** Someone reads your diff and chooses Comment, Approve or Request changes. A `CODEOWNERS` file requests the right reviewers automatically. *Other side:* you cannot approve your own PR, so alone you cannot get real review. AI review of AI-written code gives false confidence, and it is metered: since 1 Jun 2026, each Copilot code review uses AI Credits *and*, on private repos, GitHub Actions minutes.
- **Merge methods.**
  - *Merge commit* keeps every commit.
  - *Squash* turns the PR into one commit on `main`.
  - *Rebase* replays the commits with no merge commit.
  - *Other side:* for one person it barely matters. Pick squash and stop thinking about it.
- **Protected branch / ruleset.** Rules on `main`: require a PR, require green checks, block force-push and deletion. On GitHub Free these work only on **public** repos. *Practise:* [#8](https://github.com/jkv8fwytbf-byte/ToDo/issues/8). *Other side:* requiring one approval on a solo repo locks you out, so set it to 0.

### 3.3 Doing several things at once, safely

- **Git worktree.** An extra folder attached to the same repository, with its own checked-out branch, so you can work on two branches at once without cloning twice. Coding agents use **one worktree per agent** so that parallel agents never overwrite each other's files (`claude --worktree <name>`). *Practise:* [#10](https://github.com/jkv8fwytbf-byte/ToDo/issues/10). *Other side:* worktrees isolate files only. Agents still collide on ports, databases, container names and your API budget, and N agents cost roughly N times the tokens.
- **Sandbox.** An isolation boundary that limits what an agent's commands can read, write and reach on the network. The layers, from light to strong:

| Layer | Example | What it stops | What it doesn't |
|---|---|---|---|
| OS sandbox per command | Claude Code `/sandbox` (Seatbelt on macOS, bubblewrap on Linux) | Writes outside the project; network outside an allowlist | Anything the allowed hosts can receive |
| Container | Docker with `--network none` and read-only mounts; Anthropic's reference dev container with a default-deny firewall | Your home folder, SSH keys, other processes | It shares the host's kernel |
| MicroVM | Docker Sandboxes (free local use, needs a Docker account and a KVM-capable OS), E2B, Vercel Sandbox | Kernel-level escapes | Data leaking through whatever network you allow |
| Cloud VM | Claude Code on the web, Codex cloud | Your machine entirely | Whatever you give it access to (repo, tokens) |

  Anthropic's own docs warn that any sandbox allowing network access can still leak what the agent can read, and a writable project folder can still be modified. *Practise:* [#11](https://github.com/jkv8fwytbf-byte/ToDo/issues/11). *Other side:* managed sandboxes (E2B, Vercel) are metered bills you don't need yet. Docker is free and teaches most of the idea.

### 3.4 Automation

- **CI (continuous integration).** Every push or PR automatically runs linters and tests on a clean machine, and the result shows as a ✅ or ❌ on the PR. *Practise:* [#8](https://github.com/jkv8fwytbf-byte/ToDo/issues/8). *Other side:* CI is only as good as your tests. Green with no tests is theatre.
- **CD.** *Continuous delivery*: every green change is built and made releasable, and a human approves production. *Continuous deployment*: no human step, so green changes go straight out. *Other side:* for a project with no users, CD is mostly a résumé keyword. The skill worth practising is rolling back.
- **Pipeline.** The ordered chain of stages (lint → test → build → staging → production) where each runs only if the previous one passed. In GitHub Actions, jobs are chained with `needs:`. *Practise:* [#9](https://github.com/jkv8fwytbf-byte/ToDo/issues/9).
- **GitHub Actions.** GitHub's built-in automation, explained line by line in guide chapter 3.
  - *Pieces:* YAML files in `.github/workflows/` declare *events* (push, pull_request, schedule, manual), *jobs*, *runners* (the machines) and *steps*.
  - *Cost:* standard runners are **free on public repos**. Private repos get 2,000 minutes a month on Free, then Linux costs USD 0.006 a minute.
  - *Pricing news:* hosted-runner prices were cut from 1 Jan 2026. A planned fee for self-hosted runners was postponed, with no new date.
  - *Other side:* every third-party action runs with your token. GitHub itself says to use self-hosted runners only with private repos, because a pull request from a fork of a public repo can run code on your machine.
- **Staging vs production; environments; secrets.** *Production* is the real system; *staging* is a copy where a release is checked first. GitHub models each as an **environment** with its own **secrets** (encrypted keys that workflows read at run time) and protection rules, such as a required reviewer. On GitHub Free, environment reviewers work only on public repos. GitHub says log masking of secrets is not guaranteed. *Practise:* [#9](https://github.com/jkv8fwytbf-byte/ToDo/issues/9).

### 3.5 Running software

- **Feature flag.** A runtime switch in code, so unfinished work can be merged and deployed while switched off, then turned on for a few users or killed instantly. [OpenFeature](https://github.com/open-feature/python-sdk) is the vendor-neutral standard. *Other side:* stale flags are technical debt. With no users, an environment variable does the same job.
- **On-call, incident, postmortem.** *On-call* means you are rostered to answer production alerts, sometimes at night. An *incident* is an outage. A *blameless postmortem* is a written record of impact, timeline, root cause and follow-ups that fixes systems, not people. *Other side:* the pressure cannot be simulated alone. Real incident skill comes from a real rotation, which is one argument for a job.

---

## 4. The names you heard

### 4.1 Artificial Analysis — [artificialanalysis.ai](https://artificialanalysis.ai/)
An independent company that benchmarks AI models and API providers on quality, price, speed and latency. Its *Intelligence Index* combines ten exams. As of late September 2026 Claude Opus 5.5 at max effort tops it at about 58, and about 51 at default effort (reported). Scores from different index versions are not comparable. **Now?** Yes, as a 5-minute check before any model or API purchase. **Other side:** one composite number hides how a model does on *your* task, and checking it can turn into a reason to upgrade.

### 4.2 Pi — [pi.dev](https://pi.dev/)
A deliberately minimal, MIT-licensed terminal coding agent written in **TypeScript** (Node 22.19+). It gives the model four tools (read, write, edit, bash) and a small system prompt, and everything else comes from extensions. It works with many model providers. OpenClaw is built on Pi's components. The project now lives at [earendil-works/pi](https://github.com/earendil-works/pi), with about 109.5k stars. **Its docs say it does not sandbox tool calls and recommend running it in a container.** When you sign in with a Claude subscription, Pi warns that usage is billed per token as extra usage, not against your plan. **Now?** Yes, as the clearest small codebase showing how an agent loop works: read its design post and run it only inside Docker, on a capped key. **Other side:** extending it means learning TS first, and "minimal" means you build yourself what other tools ship.

### 4.3 ARC Prize — [arcprize.org/leaderboard](https://arcprize.org/leaderboard)
François Chollet's puzzle tests of learning new skills efficiently. ARC-AGI-3, launched 25 Mar 2026, is interactive: agents play unfamiliar game-like environments. Frontier models scored under 1% at launch; by September the best verified result on the standard harness was 62.7%. (reported) **Now?** Park it. **Other side:** it measures novel-puzzle reasoning, not software engineering or employability, and its headline numbers depend on the harness and on runs costing tens of thousands of dollars.

### 4.4 OpenRouter — [openrouter.ai](https://openrouter.ai/)
One API key for hundreds of models from many providers, paid with **prepaid credits**. It adds no markup on tokens; instead there is a 5.5% fee on card top-ups (USD 0.80 minimum). Free models are limited to 20 requests a minute and 50 a day, or 1,000 a day once you have bought USD 10 of credits in total. Keys can carry a **credit limit** with a daily, weekly or monthly reset, and **Auto Top-Up** can be turned off. (reported, from OpenRouter's docs source) **Now?** Yes, capped. **Other side:** a per-key cap is not an account cap, since a new key gets around it; free models may log prompts; and agent loops burn tokens fast.

### 4.5 WorkOS — [workos.com](https://workos.com/)
Ready-made login and identity plumbing for software companies that sell to big businesses.
- **Products:** AuthKit (a hosted login box), enterprise single sign-on (SSO over SAML or OIDC), Directory Sync (SCIM: users added and removed automatically), audit logs, fine-grained authorization, and in 2026 authorization for MCP servers plus "Agent Auth" (early access since 2 Sep 2026).
- **Pricing:** AuthKit is free up to 1M monthly active users; SSO and SCIM start at about USD 125 per connection per month. It raised USD 100M at a USD 2B valuation in March 2026. (reported)
- **Now?** Read its docs as a free explanation of SSO, OIDC and SCIM; don't buy anything ([#14](https://github.com/jkv8fwytbf-byte/ToDo/issues/14)).
- **Other side:** it solves a sales problem ("the big customer's IT team needs SSO") that you don't have. Self-hosting Keycloak or Authentik in Docker teaches identity more deeply, with no lock-in.

### 4.6 "jev.ai by typeface" = **Jev, by TypeSafe AI**
The name you most likely heard. ThePrimeagen streamed "Trying Jev: the new style of AI" on 18 Sep 2026 (reported).
- **What it is:** a model from **TypeSafe AI**, a San Francisco startup that came out of stealth on **15 Sep 2026** with a USD 40M seed round led by DCVC (reported).
- **What it does:** it is **not a chatbot** and cannot write text or code. You give it a typed question (pick one of N options, score on a scale, or a yes/no probability) and it returns a typed answer with a calibrated probability, fast and cheap. The company calls it a "System One" model.
- **Price:** about USD 0.042 per million input tokens, with output free. It has a Python SDK. Sign-ups were **paused on 22 Sep 2026** (reported).
- **Now?** No. At most a 1-hour experiment later, through a capped key.
- **Other side:**
  - It is days old, the benchmarks are self-reported, and critics say its evaluation grades agreement with other models rather than ground truth.
  - **`jev.ai` is not TypeSafe's domain.** It redirected to a domain-for-sale page (reported), and several lookalike "Jev" sites have appeared, so treat any site other than typesafe.ai as a possible scam.
  - A small classifier you train yourself in PyTorch does much of the same job for free and teaches more.

### 4.7 Typeface — [typeface.ai](https://www.typeface.ai/)
An enterprise generative-AI company for marketing content, founded by a former Adobe CTO. It raised USD 100M at a USD 1B valuation in 2023, and in 2026 sells "agentic" marketing. It publishes no self-serve pricing. (reported) **It has nothing to do with Jev.** **Now?** No; it is sold to big companies' marketing teams.

> **Name-confusion box.**
> - **TypeSafe AI** makes Jev (typesafe.ai, 2026).
> - **Typeface** makes enterprise marketing AI (typeface.ai).
> - **Typesafe Inc.** was the Scala and Akka company, renamed Lightbend in 2016; old blog posts may mean it.
> - A **typeface** is also a font family. Fast speech on a stream turns all four into one word.

---

## 5. The friend's list, triaged

Keep three, park the rest, skip one. "Time" is a rough estimate.

| Item | What it is | Time | Verdict | The other side |
|---|---|---|---|---|
| [artificialanalysis.ai](https://artificialanalysis.ai/) | Model and provider benchmarks, prices, speeds (§4.1) | 30 min, then 5 min before any purchase | **KEEP**, paired with OpenRouter | A composite score hides your task; rankings change weekly |
| [arcprize.org/leaderboard](https://arcprize.org/leaderboard) | Puzzle tests of learning new skills (§4.3) | 30–60 min | Park | Not coding and not jobs; depends on the harness |
| [pi.dev](https://pi.dev/) | Minimal open-source TS coding agent (§4.2) | 2–4 h to read and run in Docker | **KEEP** | TypeScript; no sandbox; a subscription login bills as extra usage |
| [YouTube A5w-dEgIU1M](https://www.youtube.com/watch?v=A5w-dEgIU1M) | Veritasium, "The Trillion Dollar Equation" (Feb 2024, ~31 min; now titled "The Equation That Beat Wall Street"): the story of Black-Scholes-Merton (reported) | 31 min | Watch once | It makes quant trading look glamorous. A SEBI study found 91% of individual F&O traders in India lost money in FY25 (reported) |
| [Buffett's bet (Investopedia)](https://www.investopedia.com/articles/investing/030916/buffetts-bet-hedge-funds-year-eight-brka-brkb.asp) | A 10-year bet: an S&P 500 index fund returned about 126% against about 36% on average for five funds of hedge funds, 2008–2017 (reported) | 15 min | Read once | The linked page is the 2016 "year eight" update. The lesson is that **fees compound**, which applies to subscriptions too. It is one decade and one market |
| [MIRI, "The Problem"](https://intelligence.org/the-problem/) | The case that superhuman AI built with current methods would likely cause extinction; about 7,850 words (reported) | 40 min, plus a counter-argument | Park, or read with a counter-argument | It is advocacy, and its >90% risk figure sits far above most researchers' estimates. Pair it with ["AI as Normal Technology"](https://knightcolumbia.org/content/ai-as-normal-technology) |
| [Anthropic, "A global workspace in language models"](https://www.anthropic.com/research/global-workspace) | Interpretability research (6 Jul 2026): a small set of internal representations Claude can report on and change, used to catch evaluation-awareness and hidden goals; it accounts for "less than a tenth" of internal activity | 20–30 min for the blog post | Read the blog only | A vendor writing about its own model; the full paper needs interpretability background |
| ISLP (James, Witten, Hastie, Tibshirani, Taylor) | *An Introduction to Statistical Learning with Applications in Python*; a free PDF plus [Python labs](https://github.com/intro-stat-learning/ISLP) | 60–120 h | **Depth pick** (free) | Classical statistics, not agents; worth it only if you do the exercises |
| AIMA (Russell **and** Norvig, 4th ed., 2020) | *Artificial Intelligence: A Modern Approach*, 1,136 pages (reported); [Python code](https://github.com/aimacode/aima-python) | 20–40 h selective; 150–300 h whole | Park; later read only the agents, search, planning and MDP chapters | Written before LLMs, and paid. The list says only "Norvig", which could also mean his *Paradigms of AI Programming* |
| OFOD (John C. Hull) | *Options, Futures, and Other Derivatives*, 11th ed.; an Indian edition with Sankarshan Basu exists (reported) | 100–200+ h | Skip unless you go into finance or quant work | It pulls toward options trading; see the SEBI figure above |
| [hn.algolia.com](https://hn.algolia.com/) | Hacker News search, plus a [free API](https://hn.algolia.com/api) | 15 min | **KEEP** | A US, developer-heavy crowd; it can become another scroll habit |
| [openrouter.ai](https://openrouter.ai/) | One API for many models, prepaid (§4.4) | 30 min to set caps | **KEEP**, capped, paired with Artificial Analysis | Fees; per-key caps are not account caps |
| [AI Explained](https://www.youtube.com/@aiexplained-official) | Careful analysis of model releases; its creator co-made the SimpleBench benchmark (reported) | 1–2 h a month | Optional | News is not skill |
| [Dwarkesh Podcast](https://www.youtube.com/@DwarkeshPatel) | Long interviews with AI lab leaders and researchers | 4–8 h a month | Optional, on commutes only | A San Francisco insider view; no hands-on output |
| [polymarket.com](https://polymarket.com/) | A crypto prediction market | 0 | **SKIP** | See the note below |

**The three keeps:**
1. Pi, run in Docker.
2. Artificial Analysis plus a capped OpenRouter key, as one pair.
3. HN Algolia, to check ideas against what has already been built.

ISLP is the free depth pick.

**Polymarket in India.** India's Promotion and Regulation of Online Gaming Act, 2025 bans online money games. Its Rules took effect on **1 May 2026**, and prediction markets are treated as money games. Indian internet providers have blocked Polymarket since about **22 May 2026**. Separately, India's foreign-exchange rules (FEMA) bar sending money abroad for betting, and crypto gains are taxed at 30% plus 1% tax deducted at source. (reported; not legal advice) The minister told Parliament that players are "victims" rather than offenders, but getting in would still mean evading a block and moving money in ways the rules forbid.

**The list as a whole.** About 330–650 hours, or 8–15 months at 10 hours a week. About a quarter of it pulls toward finance, trading or betting, and nothing on it covers security fundamentals or TypeScript; the tickets fill those gaps. A reading list reflects the interests of the person who wrote it.

---

## 6. The forks: certificate, startup, job, or keep building

| Path | For | Against | Cheapest honest test | It's right when… |
|---|---|---|---|---|
| **A. A certificate** | Gets a CV past keyword filters (Security+ and CEH appear often in Indian job ads); a structured syllabus | Costs money; a certificate without a portfolio rarely lands a job; AI-security credentials are brand new | Do [#15](https://github.com/jkv8fwytbf-byte/ToDo/issues/15) (the free OWASP material) first | A specific job ad you want lists that certificate |
| **B. A startup experiment** | Solo MVPs are cheap; you learn from customers | "No market need" is a top cause of failure; building without users; subscriptions creep up | 10 problem interviews, then do the service by hand, then add a payment link, with a kill rule set in advance | You have found a problem people already pay to solve |
| **C. A job or internship** | Real code review, production experience, pay, a team | The entry market is tight: US payroll data shows 22–25-year-olds in AI-exposed jobs 19% below their expected level (Stanford "Canaries", Aug 2026, descriptive), while Yale, the NY Fed and the Dallas Fed find little AI signal. Indian fresher postings increasingly ask for an internship (reported, weak data) | Apply with the build ticket [#16](https://github.com/jkv8fwytbf-byte/ToDo/issues/16) as evidence | You want outside feedback fastest |
| **D. Keep building** | Skill compounds, at your own pace | With no deadline and no outside feedback, it drifts back into the course loop | A second project, run through tickets | [#16](https://github.com/jkv8fwytbf-byte/ToDo/issues/16) was fun and you have a next idea |

**Certificates at a glance** (all prices reported; check before paying):

| Certificate | Cost | Format | Signal | Note |
|---|---|---|---|---|
| ISC2 CC | ~USD 199, plus USD 50 a year | Multiple choice | Low | The free offer closed to new signups on 20 May 2026 |
| CompTIA Security+ (SY0-701) | Varies; check in INR | Multiple choice plus hands-on questions | The strongest keyword with HR screeners | SY0-801 is rumoured for late 2026 but unconfirmed; SY0-701 retires around June 2027 |
| EC-Council CEH | ~USD 950–1,999 | Multiple choice (a practical exam is separate) | Common in Indian job ads; practitioners rate it low | The most expensive way to buy a keyword |
| INE eJPT, TCM PJPT, HTB CPTS | ~USD 200–500 | Hands-on | Respected by practitioners; unknown to many HR screeners | The lab write-ups double as portfolio |
| OffSec OSCP / OSCP+ | Learn One ~USD 2,749 a year | 24-hour practical exam | The standard for penetration testing | Not now |
| AI security: OffSec OSAI (AI-300), CompTIA SecAI+, IAPP AIGP, ISACA AAISM | Varies (AIGP ~USD 649–799) | Mixed | 0–2 years of track record | OSAI assumes OSCP-level skill; AAISM needs an existing CISM or CISSP-type certificate |
| **Free:** OWASP LLM Top 10 (2026), Agentic Top 10, Agentic Skills Top 10, [MCP security best practices](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/docs/draft/tutorials/security/security_best_practices.mdx) | ₹0 | Reading plus labs | None as a credential | The material the paid courses are built on |

**Recommendation.** Eight weeks of building to learn ([§8](#8-the-8-week-map)), then decide in [#17](https://github.com/jkv8fwytbf-byte/ToDo/issues/17) with evidence instead of excitement. Whatever you pick, pick **one**.

---

## 7. Money rules

These are impersonal rules; the ticket is [#4](https://github.com/jkv8fwytbf-byte/ToDo/issues/4).

1. **Every account has a cap.**
   - **Claude:** set a monthly spend limit on extra usage (usage credits), or turn it off, under Settings → Usage. Some third-party harnesses bill a subscription login as per-token extra usage; Pi prints a warning saying so.
   - **OpenRouter:** a credit limit on each key, Auto Top-Up off, and a small prepaid balance.
   - **GitHub:** set the Copilot additional-usage budget to 0 unless you mean to spend it.
2. **One paid plan at a time.** Claude's Indian prices, including GST (reported, July 2026): Pro ₹2,399 a month (₹1,999 a month billed yearly), Max 5x ₹11,999, Max 20x ₹23,999.
3. **Cost per task, not price per token** (guide chapter 7). The effort setting matters: higher effort means more thinking tokens, and those are billed as output. The USD 13-per-task leader in [§1.1](#11-the-coding-agent-leaderboard-is-a-god) runs at *max* effort. Leave effort at default unless a task needs more.
4. **Know the benchmark bill.** Anthropic puts typical enterprise Claude Code spend at about USD 150–250 per developer per month, with 90% of users under USD 30 per active day (reported). A learner's bill above a professional's is a signal to stop and look.
5. **Dollar prices cost more than they look.** The rate was about ₹96 per USD in late September 2026 (reported). Many USD services add 18% GST, and cards add a forex markup.
6. **Review monthly, in 10 minutes.** List each tool, what it cost, and what it produced. Cancel whatever produced nothing: fees compound, which was the whole lesson of Buffett's bet.
7. **Don't buy courses, "guides" or access for hyped tools.** Lookalike sites appear within days, as they did for Jev.
8. **Keys never go in chats, repos, or an agent's plain environment.** OpenRouter takes part in GitHub's secret scanning and will email you about a leaked key, but you must delete it yourself (reported).

---

## 8. The 8-week map

| Week | Ticket | Label |
|---|---|---|
| 1 | [#3](https://github.com/jkv8fwytbf-byte/ToDo/issues/3) Merge your first pull request yourself | workflow |
| 1 | [#4](https://github.com/jkv8fwytbf-byte/ToDo/issues/4) Put a hard monthly cap on every AI account | security |
| 1 | [#5](https://github.com/jkv8fwytbf-byte/ToDo/issues/5) Check any AI agent that is connected to your accounts | security |
| 1 | [#6](https://github.com/jkv8fwytbf-byte/ToDo/issues/6) Read guide chapter 0 and pick three rules to keep | foundation |
| 2 | [#7](https://github.com/jkv8fwytbf-byte/ToDo/issues/7) Run one ticket end to end in this repo | workflow |
| 2 | [#8](https://github.com/jkv8fwytbf-byte/ToDo/issues/8) Add CI to a public practice repo and make it a required check | workflow |
| 3 | [#9](https://github.com/jkv8fwytbf-byte/ToDo/issues/9) Turn CI into a pipeline with staging and production gates | workflow |
| 3 | [#10](https://github.com/jkv8fwytbf-byte/ToDo/issues/10) Work on two branches at once with git worktrees, then with two agents | workflow |
| 4 | [#11](https://github.com/jkv8fwytbf-byte/ToDo/issues/11) Run an agent inside a sandbox you built | security |
| 4 | [#12](https://github.com/jkv8fwytbf-byte/ToDo/issues/12) Triage the reading list: keep three, park the rest | reading |
| 5 | [#13](https://github.com/jkv8fwytbf-byte/ToDo/issues/13) Reach TypeScript reading level | foundation |
| 6 | [#14](https://github.com/jkv8fwytbf-byte/ToDo/issues/14) Explain how the web works, in your own words | foundation |
| 6 | [#15](https://github.com/jkv8fwytbf-byte/ToDo/issues/15) Security fundamentals before any certificate | security |
| 7–8 | [#16](https://github.com/jkv8fwytbf-byte/ToDo/issues/16) Build one small thing end to end, the way a team would | foundation |
| end (~21 Nov 2026) | [#17](https://github.com/jkv8fwytbf-byte/ToDo/issues/17) Decide the next three months | decision |

If a week slips, move the ticket to the next week rather than deleting it. Week 1 is the most urgent part, because two of its tickets are about safety.

---

## 9. Glossary

These are the terms used here that guide chapter 17 does not already define.

- **BYOK** (bring your own key): pasting your own API key into a tool.
- **Code owner / `CODEOWNERS`:** a file naming who must review which parts of a repo.
- **CVE / CVSS:** a public ID for a security bug / its severity score, from 0 to 10.
- **Egress allowlist:** the list of network destinations a sandboxed program may reach; everything else is blocked.
- **Epic / tracking issue / sub-issue:** a big goal split into child tickets.
- **Feature flag:** a runtime on/off switch for a code path.
- **GCC** (global capability centre): an Indian office of a foreign company, doing in-house engineering.
- **Harness:** the tool wrapped around a model (prompts, file access, permissions); Claude Code, Codex and Pi are harnesses.
- **Kanban:** a board of columns (to do, doing, done) with no fixed sprints.
- **Lethal trifecta:** an agent that can read private data, receives untrusted content, *and* can send data out. Together these let one hidden instruction leak your data (Simon Willison's term).
- **MAU** (monthly active users): the usual unit of pricing for login services.
- **MicroVM:** a tiny virtual machine with its own kernel; stronger isolation than a container, lighter than a full VM.
- **OIDC / SAML:** two standards for logging in through another identity provider.
- **pass@1:** the share of tasks solved on the first attempt.
- **Postmortem (blameless):** a written review of an incident that fixes systems, not people.
- **Prompt injection:** instructions hidden in content an agent reads (an email, a web page, a file, a tool's output) that the agent then obeys. *Indirect* injection, through content someone else wrote, is the dangerous kind.
- **Ruleset:** GitHub's newer form of branch protection.
- **SCIM:** a standard for automatically adding and removing user accounts.
- **SSO** (single sign-on): one login for many apps.
- **Squash merge:** turning a whole PR into one commit on `main`.
- **Status check:** a ✅ or ❌ result attached to a commit by CI.
- **Usage credits / extra usage:** pay-per-token billing on top of a subscription plan.
- **Worktree:** an extra folder attached to the same git repository, on its own branch.

---

## 10. Sources

**Primary** (read directly, or from the vendor's own repository or docs source):
- GitHub docs source: [linking a PR to an issue](https://github.com/github/docs/blob/main/content/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue.md), protected branches, rulesets, environments, secrets, Actions billing, Copilot billing (`github/docs` repository); [2026 Actions pricing changes](https://github.com/resources/insights/2026-pricing-changes-for-github-actions)
- [git-worktree documentation](https://github.com/git/git/blob/master/Documentation/git-worktree.adoc); Claude Code docs: [worktrees](https://code.claude.com/docs/en/worktrees), [sandboxing](https://code.claude.com/docs/en/sandboxing), [sandbox environments](https://code.claude.com/docs/en/sandbox-environments), [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web)
- Pi: [earendil-works/pi](https://github.com/earendil-works/pi); ThePrimeagen's [99](https://github.com/ThePrimeagen/99); [openclaw/openclaw](https://github.com/openclaw/openclaw) and its security policy
- OWASP: [GenAI-LLM-Top10](https://github.com/GenAI-Security-Project/GenAI-LLM-Top10) (2026 edition, published 4 Aug 2026), [Agentic Skills Top 10](https://github.com/OWASP/www-project-agentic-skills-top-10); [MCP security best practices](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/docs/draft/tutorials/security/security_best_practices.mdx)
- CVE records: CVE-2026-28363 (OpenClaw), CVE-2026-50548 and CVE-2026-50549 (Cursor; [advisory](https://github.com/cursor/cursor/security/advisories/GHSA-3p48-7v9f-v5cw))
- Anthropic, [A global workspace in language models](https://www.anthropic.com/research/global-workspace) (6 Jul 2026)
- Docker Sandboxes (docker/docs source); OpenFeature [Python SDK](https://github.com/open-feature/python-sdk); [ISLP labs](https://github.com/intro-stat-learning/ISLP); [aima-python](https://github.com/aimacode/aima-python)

**Reported** (news, search snippets, mirrors; likely but not confirmed):
- Artificial Analysis Coding Agent Index v1.5 and the Intelligence Index (artificialanalysis.ai; X posts; mirrors dated 22–25 Sep 2026)
- ARC Prize, [ARC-AGI-3 launch](https://arcprize.org/blog/arc-agi-3-launch) and [Astra verification](https://arcprize.org/blog/astra)
- METR, [2025 developer study](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) and [Feb 2026 update](https://metr.org/blog/2026-02-24-uplift-update/); Anthropic, [AI assistance and coding skills](https://www.anthropic.com/research/AI-assistance-coding-skills)
- OpenAI, [why we no longer evaluate SWE-bench Verified](https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/); UC Berkeley RDI, [trustworthy benchmarks](https://rdi.berkeley.edu/blog/trustworthy-benchmarks-cont/)
- GitHub [Octoverse 2025](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/); Stack Overflow [2025 survey](https://survey.stackoverflow.co/2025/)
- WorkOS [pricing](https://workos.com/pricing), [Series C](https://workos.com/blog/series-c), [Agent Auth](https://workos.com/changelog/agent-auth)
- TypeSafe AI and Jev: [DCVC announcement](https://www.dcvc.com/news-insights/typesafe-emerges-from-stealth-with-a-new-way-of-doing-ai/), [Tom's Hardware](https://www.tomshardware.com/tech-industry/artificial-intelligence/typesafe-ais-jev-offers-an-alternative-to-llms-that-claims-to-be-193x-faster-and-445x-cheaper-system-one-type-model-is-bespoke-for-probabilistic-decision-making), Bloomberg (25 Sep 2026)
- Typeface: [TechCrunch 2023](https://techcrunch.com/2023/06/29/typeface-which-is-building-generative-ai-for-brands-raises-100m-at-a-1b-valuation)
- OpenRouter [FAQ](https://openrouter.ai/docs/faq) and [limits](https://openrouter.ai/docs/api_reference/limits); Claude India pricing ([TechCrunch, Jul 2026](https://techcrunch.com/2026/07/13/anthropic-starts-localizing-claude-pricing-for-india-its-biggest-market-after-the-us/)); Claude Code [costs](https://code.claude.com/docs/en/costs)
- Linear [pricing](https://linear.app/pricing) and [coding sessions](https://linear.app/changelog/2026-06-11-coding-sessions)
- Certificates:
  - ISC2 [One Million CC conclusion](https://www.isc2.org/Insights/2026/04/one-million-certified-cyber-conclusion)
  - Security+ SY0-801 rumours (training vendors)
  - OffSec [pricing](https://www.offsec.com/pricing/)
  - EC-Council, IAPP, ISACA (secondary compilations)
- Mo Bitar's videos (third-party transcripts); ThePrimeagen's streams (third-party summaries)
- Stanford Digital Economy Lab, ["Canaries in the Coal Mine"](https://digitaleconomy.stanford.edu/publication/canaries-in-the-coal-mine-six-facts-about-the-recent-employment-effects-of-artificial-intelligence/) (Aug 2026 revision), and the Yale, NY Fed and Dallas Fed counter-evidence
- India's online gaming law and the Polymarket block: [PRS](https://prsindia.org/billtrack/the-promotion-and-regulation-of-online-gaming-bill-2025), [CoinDesk, 22 May 2026](https://www.coindesk.com/markets/2026/05/22/india-cracks-down-on-prediction-markets-polymarket-goes-dark-kalshi-could-be-next); SEBI F&O study ([Business Standard](https://www.business-standard.com/markets/news/net-losses-of-traders-in-fo-widens-in-fy25-sebi-study-125070701221_1.html))
- Veritasium, [The Trillion Dollar Equation](https://www.veritasium.com/videos/2024/2/28/the-trillion-dollar-equation); Buffett's bet result ([Motley Fool](https://www.fool.com/investing/2018/01/03/warren-buffett-just-officially-won-his-million-dol.aspx)); MIRI, [The Problem](https://intelligence.org/the-problem/); [AI as Normal Technology](https://knightcolumbia.org/content/ai-as-normal-technology)

### Not independently verified

- The exact current #1 on either Artificial Analysis index; both change weekly.
- Security+ SY0-801 dates, and all certificate prices in INR.
- The ISC2 CC paid-exam price after the free programme.
- Whether Polymarket itself geoblocks India, and the status of the Supreme Court challenge to the online gaming law.
- AIMA and Hull retail prices in India.
- E2B and Vercel Sandbox free-tier limits; Jira and PagerDuty free-tier details.
- Any quotation attributed to a YouTube video; those came from third-party transcripts.
