<picture>
  <img src="https://ghlogo.heathdutton.workers.dev/heathdutton/claude-d2-diagrams?ratio=3:2" alt="Claude D2 Diagrams" width="100%">
</picture>

# Automating Architecture Diagrams with Claude

## The Problem

Every codebase I've worked on has the same issue: outdated architecture diagrams. They're created once during initial design (if at all), maybe updated after a major refactor (if you are luck), then left to rot. A year later, new team members are onboarding with diagrams that show services that no longer exist and miss entire subsystems.

The irony is that modern infrastructure-as-code gives us a perfect source of truth. Terraform files, Kubernetes manifests, Docker Compose configs—they define exactly what's deployed. But translating that into visual documentation is tedious work that nobody prioritizes. And if we DO put in the time to create diagrams, we end up generating them with a biased perspective. They may look pretty, but are factually inaccurate. There's also the problem of the illustration being too high level or too detailed.

## The Solution

I made [claude-d2-diagrams](https://github.com/heathdutton/claude-d2-diagrams), a Claude Code plugin that generates architecture and infrastructure diagrams directly from your codebase.

Run `/d2:diagram` and it:

1. **Scans** your repo for IaC files (Terraform, K8s, Docker, Pulumi, CloudFormation)
2. **Analyzes** the infrastructure and source code relationships
3. **Generates** D2 diagram source files
4. **Renders** 8 SVG diagrams (detailed + simplified, light + dark themes)
5. **Documents** everything in markdown with file:line references

The key insight was creating two versions of each diagram: a detailed one for engineers and a simplified 3-8 node version for stakeholders. The simplification algorithm aggregates similar resources ("12 EC2 instances" becomes "Application Servers") and hides implementation details while preserving the essential architecture.

## How It Works

The plugin orchestrates specialized agents across 10 phases:

- **Scanner** (fast, Haiku): discovers all IaC and existing docs
- **Documenter** (deep, Opus): analyzes infrastructure and architecture separately
- **Simplifier** (balanced, Sonnet): distills detailed docs to stakeholder-friendly summaries
- **Renderer** (precise, Opus): converts documentation to D2 syntax
- **Enhancer** (creative, Sonnet): adds icons, animations, and polish

Each phase checkpoints progress, so you can interrupt and resume without losing work.

The diagrams include 100+ technology icons (AWS, GCP, K8s, databases, languages), animated data flows, and both light and dark themes. A Go-based post-processor inlines all icons as SVG so they render correctly on GitHub.

## Why D2?

[D2](https://d2lang.com) is a declarative diagramming language that compiles to SVG. Unlike drag-and-drop tools, the source is text—version-controllable, diffable, and perfect for AI generation. The syntax is intuitive enough that the rendered diagrams can be manually tweaked if needed. There are also UI tools for manually editing D2 diagrams in the wild.

## Try It

```bash
# Install D2
brew install d2

# Add the plugin to Claude Code
claude plugin marketplace add https://github.com/heathdutton/claude-d2-diagrams
claude plugins add heathdutton/claude-d2-diagrams

# Run it in claude code
/d2:diagram
```

The plugin works best on projects with infrastructure-as-code, but it'll analyze any codebase and generate architecture diagrams from the source structure.

---

[View on GitHub](https://github.com/heathdutton/claude-d2-diagrams) ・ [Back to Profile](https://github.com/heathdutton)
