# s8sskills / azure

Serverless skills for **Microsoft Azure** — reusable, agent-ready instructions that teach your AI coding agent how to build and deploy serverless apps on Azure.

Part of [s8sskills](https://s8sskills.com): one skill pack per cloud vendor.

## Install

Install every skill in this pack into your agent (Claude Code, etc.):

```sh
npx skills add s8sskills/azure
```

Or install a single skill:

```sh
npx skills add https://github.com/s8sskills/azure/tree/main/skills/azure-functions-deploy
```

`npx skills` is the open agent-skills installer ([vercel-labs/skills](https://github.com/vercel-labs/skills)).
It drops each skill into your agent's skills directory (e.g. `.claude/skills/`), where the agent picks it up automatically when a task matches the skill's `description`.

## Skills

| Skill | What it does |
| --- | --- |
| [`azure-project-setup`](skills/azure-project-setup/SKILL.md) | Install Azure CLI, authenticate your session, choose a subscription, and configure default resource groups. |
| [`azure-functions-deploy`](skills/azure-functions-deploy/SKILL.md) | Package and deploy HTTP or event-driven serverless APIs to Azure Functions. |
| [`azure-container-apps-deploy`](skills/azure-container-apps-deploy/SKILL.md) | Deploy serverless containers directly from source code or container registries to Azure Container Apps (ACA). |

## Layout

Skills follow the standard discovery layout — one directory per skill, each with a `SKILL.md` whose YAML frontmatter declares `name` and `description`:

```
skills/
  azure-project-setup/SKILL.md
  azure-functions-deploy/SKILL.md
  azure-container-apps-deploy/SKILL.md
```

## Contributing

New Azure serverless skill ideas welcome (App Service serverless, Azure Static Web Apps, Event Grid integrations, CosmosDB bindings, ARM/Bicep deployments, IaC with Terraform). Add a `skills/<name>/SKILL.md` with `name` + `description` frontmatter and open a PR. Keep each skill focused on one task, with concrete commands and a verification step.

## License

See [LICENSE](LICENSE).
