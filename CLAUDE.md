# CLAUDE.md

## Project

Maintain and develop existing web application.
Stack: Angular 17 (FE) · .NET Core 8 (BE) · MSSQL

## Team

PM · FE Dev · BE Dev (3–5 people)
Read role-specific skills in `.claude/` before starting tasks.

## Git Workflow

```
main      → production (PR only, 1 review required)
develop   → integration
feature/  → daily work
hotfix/   → urgent fixes
```

Commit format: `feat|fix|refactor|docs: short description`

## Project Structure

```
/frontend    → Angular 17 app
/backend     → .NET Core 8 API
/docs/adr    → Architecture Decision Records
/scripts     → DB migrations, deployment
/.claude     → AI skill files (FE, BE, Review)
```

## Hard Rules

* Never push directly to `main` or `develop`
* No unit-test skip for business logic
* No `any` type in TypeScript without comment
* No secrets or connection strings in code

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
* 
* \## Repository
* 
* \*\*DevTeam\*\* — owned by \[@pattaraponkasco1233](https://github.com/pattaraponkasco1233/DevTeam)
* 
* Remote: `https://github.com/pattaraponkasco1233/DevTeam`
* 
* \## Git Workflow
* 
* ```bash
* git add <files>
* git commit -m "message"
* git push origin master
* ```
* 
* \## Claude Code Permissions
* 
* Configured in `.claude/settings.local.json` — allowed commands include `git`, `gh`, `rtk git`, and `rtk gh`.

