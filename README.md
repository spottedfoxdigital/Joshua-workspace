# Spotted Fox Digital — Landing Pages

Agency workspace for building and deploying Google Ads landing pages across clients.

## 📖 Start here
- **[Deployment Playbook](docs/DEPLOYMENT-WORKFLOW.md)** — the repeatable, client-agnostic
  workflow for shipping a landing page (who does what: Claude Code vs Claude for Chrome vs
  human, phase by phase, with lessons learned).

## 📁 Structure
```
docs/                     Agency-wide playbooks & checklists (client-agnostic)
landing-pages/            One folder per client landing page
  blindguy-wenatchee/       The Blind Guy of Wenatchee (live)
```

> Each client landing page deploys to its **own Netlify site**. See the playbook for the
> recommended per-client deploy setup as this scales.

## Clients
| Client | Folder | Live URL |
|--------|--------|----------|
| The Blind Guy of Wenatchee | `landing-pages/blindguy-wenatchee/` | https://wenatchee.blindguyoftricities.com |
