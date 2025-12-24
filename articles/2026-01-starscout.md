<picture>
  <img src="https://ghlogo.heathdutton.workers.dev/hehao98/StarScout?ratio=3:2" alt="StarScout" width="100%">
</picture>

# GitHub's Fraud Economy

## The Problem

GitHub stars are social proof. They influence which libraries engineers adopt, which projects get funding, and which tools surface in search results and recommendations. Algorithms and AI assistants increasingly steer us toward popular repositories, and stars are a major signal in that ranking.

So what happens when you can buy them?

I noticed a few startups with star-history graphs that looked... parabolic. Linear growth curves that no organic project follows. They were embedding [star-history](https://www.star-history.com/) charts in their READMEs to brag about popularity, and the numbers looked unreal because they were. One project in particular caught my eye. I won't name names here, but let's just say when the analysis results came back, they were right at the top of the list. Satisfying? A little. Surprising? Not even slightly.

This hurts real projects. Small libraries struggle to gain adoption when they're overshadowed by competitors that bought their way to the top. Engineers waste time evaluating tools that don't deserve the attention.

## The Research

I went looking for prior work and found [StarScout](https://github.com/hehao98/StarScout), a tool built by [Hao He](https://github.com/hehao98) and collaborators for their ICSE '26 paper: *"Six Million (Suspected) Fake Stars on GitHub: A Growing Spiral of Popularity Contests, Spam, and Malware."*

Six million. That number deserves to sink in.

StarScout uses two detection heuristics. The first flags "low-activity" stargazers: accounts that star repos but show little other GitHub activity beyond the starring itself. The second uses the CopyCatch algorithm to find lockstep behavior: clusters of accounts that star the same repos in suspiciously coordinated time windows. Together they surface patterns that are invisible when looking at any single repository in isolation.

This is the kind of work that needs more eyes on it.

## The Contribution

The original pipeline ran on Google BigQuery, reading 20-60TB of data at $6.25/TB. Not great for someone who just wants to independently verify some suspicions about a sketchy repo.

I [contributed local execution support](https://github.com/hehao98/StarScout/pull/5) so the entire pipeline can run without cloud dependencies. The approach:

- **DuckDB** replaces BigQuery for query execution
- **Parquet files with Zstd compression** replace cloud storage
- **GH Archive** provides the raw GitHub event data

My first attempt involved a 50TB NAS because apparently my instinct when faced with a data problem is to throw storage at it like I'm building a bunker for the apocalypse. Turns out, after optimizing the queries and compression, a single 4TB external SSD and an M1 iMac were more than enough. The iMac was my grandfather's, who recently passed. He'd probably have opinions about me using it to hunt bot farms, but I love getting to put it to good use.
The local approach also eliminates BigQuery's 6-hour query timeout, which was a constraint on the original CopyCatch iterations.

## DuckDB is the Real MVP

I have to give [DuckDB](https://duckdb.org/) a moment here because what this thing does borders on disrespectful to cloud data warehouses everywhere.

It's an in-process analytical database that reads Parquet files directly, runs complex SQL with window functions and CTEs, and does it all from a single binary with no server, no configuration, and no dependencies. You `pip install` it. That's the setup.

The CopyCatch algorithm requires iterative map-reduce style queries over billions of rows of stargazer events. BigQuery handles this by throwing a datacenter at it. DuckDB handles it on a desktop by being unreasonably well-engineered. It automatically parallelizes across cores, spills to disk when memory runs out, and pushes predicates down into Parquet file scans so it only reads the columns and row groups it needs.

## What the Data Shows

Running the local pipeline against GH Archive data through January 2026 turned up some numbers that I'd call "alarming" if that word hadn't been ruined by every SaaS marketing page. The lockstep heuristic flagged over **8 million suspected fake stars** across ~3,300 repositories. The low-activity heuristic found another **~694,000** across a similar number of repos.

### Two Flavors of Fraud

The heuristics reveal distinct fraud profiles:

**Intentional fraud** (low-activity heuristic) clusters around what you'd expect: crypto trading bots, game hacks, pirated software, gambling "predictor" tools, and darkweb directories. These repos have stars from throwaway accounts with zero other GitHub activity. Some hit 98-100% fake stars. They're not even trying to be subtle.

**Artificial amplification** (lockstep heuristic) is more interesting. It catches coordinated campaigns where clusters of accounts star the same repos in tight time windows. This surfaces across AI/LLM projects, blockchain protocols, frontend libraries, and "awesome" curated lists.

### The Sector Breakdown

| Sector | Avg Fake % | Profile |
|--------|------------|---------|
| **Bots/Automation** | ~50% | Highest fraud rate. Trading bots, scrapers. |
| **Blockchain/Crypto** | ~36% | Second highest. Investor-driven visibility. |
| **Hacks/Exploits** | ~26% | Game cheats, cracks. |
| **AI/ML** | ~12% | Lower rate but massive volume due to hype. |

Blockchain and crypto have the highest fraud *rates*. AI has the highest absolute *volume*, because even a small fraud percentage across thousands of hype-cycle repos adds up fast.

### Notable Offenders

Some projects are hard to explain away. Multiple AI and blockchain projects showed 40-50% suspected fake stars. Even an OpenAI demo repo got flagged at 57%.

A caveat: the lockstep heuristic *can* pick up organic viral behavior. A repo trending on Hacker News looks a lot like a coordinated campaign. The low-activity heuristic is higher confidence for actual fraud. And as the paper's authors note, individual entries may be false positives.

There's also a gray zone that no heuristic can catch. Some projects gate features behind starring the repo. Others make it a prerequisite for Discord access. Not bot farms, but not organic enthusiasm either. The detectable fraud is just the floor.

That said, a repo with 200 stars where 198 came from accounts that did nothing else on GitHub? That's not a gray zone. That's Fiverr.

### The Raw Data

The full results are published as gists for anyone to dig through:

- **[Detailed findings and analysis](https://gist.github.com/heathdutton/14dc1fd16db67eb272335f79367bf166)** - sector breakdowns, fraud profiles, and notable projects
- **[Lockstep/clustered heuristic results](https://gist.github.com/heathdutton/48313fcfd92c964890fdba1a3ec2000a)** - repos sorted by suspected coordinated stars
- **[Low-activity heuristic results](https://gist.github.com/heathdutton/e09693d7f8b18df8df3061a57105b112)** - repos sorted by proportion of low-activity stargazers

## Where's GitHub in All This?

To their credit, GitHub does act when researchers hand them evidence. After Hao He's team reported their findings, roughly [91% of the flagged repositories and 62% of suspected bot accounts were removed](https://www.bleepingcomputer.com/news/security/over-31-million-fake-stars-on-github-projects-used-to-boost-rankings/). That's not nothing.

But it is reactive. As a top comment on the [Hacker News discussion](https://news.ycombinator.com/item?id=42540182) put it: "GitHub should be the ones doing this research." The fake star economy grew explosively between 2022 and 2024, peaking at over 16% of repositories being associated with fake star campaigns. That didn't sneak up on anyone.

Since Microsoft acquired GitHub in 2018, the platform has focused on Copilot, Actions, and enterprise tooling. Trust and safety for social signals like stars doesn't seem to be a priority when the business model has shifted toward AI and enterprise subscriptions. I'm not the only one who's noticed.

## Why This Matters

Package managers, AI coding assistants, and developer newsletters all treat GitHub stars as a legitimate quality signal. If that signal is compromised, the whole discovery chain is compromised. The two hottest sectors in tech - AI and crypto - are also the most fraudulent on GitHub. That's not a coincidence.

Hao He's research quantifies the scale. Running it locally makes it reproducible for anyone who wants to verify the findings or just confirm their suspicions about that one project with 20,000 stars and a README full of typos.

---

[View StarScout on GitHub](https://github.com/hehao98/StarScout) ・ [Read the Paper (ICSE '26)](https://doi.org/10.1145/3744916.3764531) ・ [Back to Profile](https://github.com/heathdutton)
