# Installation

Deep-Re-Audit is a folder of AI instructions. Installing it makes those instructions available to a compatible AI host; it does not install a background service or automatically start an audit.

## Requirements

- A host that can load `SKILL.md` and read its relative reference files.
- Permission to read the material you want to audit.
- Git for the clone examples, or a browser to download the repository ZIP.
- Your usual AI-host account or subscription. This repository includes no model access or credentials.

## 1. Obtain the repository

On GitHub, choose **Code → Download ZIP**, then extract it. Alternatively:

```sh
git clone https://github.com/Khotjen/deep-re-audit.git
```

Review the files before enabling the skill. The skill payload contains only Markdown. The repository's infographic and documentation do not execute code.

## 2. Choose a host and destination

| Host | Project destination | Personal destination |
| --- | --- | --- |
| Codex local workflows | `<project>/.agents/skills/deep-re-audit/` | `~/.agents/skills/deep-re-audit/` |
| Claude Code | `<project>/.claude/skills/deep-re-audit/` | `~/.claude/skills/deep-re-audit/` |

`~` means your user home directory. For example, the Codex personal folder on Windows is usually `%USERPROFILE%\.agents\skills\deep-re-audit`. Actual host policies may restrict discovery or use.

These locations and host invocation conventions are based on the official [OpenAI skill documentation](https://learn.chatgpt.com/docs/build-skills) and [Claude Code skill documentation](https://code.claude.com/docs/en/skills), checked on October 4, 2026. Consult those pages if your installed host behaves differently. Folder layout compatibility does not prove equivalent audit behavior across models.

## 3. Copy the complete payload

Create the chosen `deep-re-audit` destination and copy **both** of these into it:

- `SKILL.md`
- The complete `references/` directory

The resulting folder must contain these five files:

| Relative path | Purpose |
| --- | --- |
| `SKILL.md` | Entry point and governing workflow |
| `references/core-audit-protocol.md` | Structural audit, dependency analysis, and repair rules |
| `references/security-failure-and-distributed-systems.md` | Failure and security review |
| `references/evidence-governance-and-closure.md` | Evidence and decision gates |
| `references/templates-and-pressure-tests.md` | Records and validation scenarios |

Keep the filenames and relative paths intact. Do not copy only `SKILL.md`. The README, `docs/`, and artwork are optional reading and need not be installed.

### Optional PowerShell example: new personal Codex installation

Run this from the downloaded repository root. It refuses to overwrite an existing installation:

```powershell
$draSource = (Get-Location).Path
$draDestination = Join-Path $env:USERPROFILE '.agents\skills\deep-re-audit'
if (!(Test-Path (Join-Path $draSource 'SKILL.md')) -or
    !(Test-Path (Join-Path $draSource 'references'))) {
    throw 'Run this from the extracted repository root.'
}
if (Test-Path $draDestination) {
    throw 'An installation already exists. Back it up before updating.'
}
New-Item -ItemType Directory -Path $draDestination -Force | Out-Null
Copy-Item (Join-Path $draSource 'SKILL.md') $draDestination -ErrorAction Stop
Copy-Item (Join-Path $draSource 'references') $draDestination -Recurse -ErrorAction Stop
Test-Path (Join-Path $draDestination 'references\core-audit-protocol.md')
```

The final line should return `True`. If copying fails, inspect the destination before using it: a partial folder is not a complete installation. For Claude Code, change the destination to `.claude\skills\deep-re-audit`.

## 4. Invoke and check discovery

In Codex CLI/IDE, select the skill with `/skills` or mention `$deep-re-audit`. In Claude Code, invoke `/deep-re-audit`. If your host does not show it, inspect its skill-discovery settings and the five-file layout; refresh its skill list or open a new session as appropriate for that host.

Start with a small, read-only request:

```text
Use deep-re-audit in TARGETED mode on the attached module.
Do not edit files or run commands that change state.
State the reviewed version, evidence, findings, and limitations.
```

Discovery is only an installation check. A useful audit still needs the relevant target files, dependencies, evidence, and tools.

## Updating and rollback

Back up the entire existing installation outside every active skill-discovery directory. Obtain the new repository, review the changes, and replace the payload as one complete set; avoid mixing reference versions. Preserve local customizations deliberately. To roll back, restore the complete backed-up payload. Do not keep two active copies with the same skill name.

## Other hosts and browser chat

A host without skill-folder support can be asked to read `SKILL.md` and all relevant references, but automatic discovery and complete adherence are not guaranteed. Downloading this GitHub repository does not automatically install it in a browser chat, plugin directory, or cloud workspace. Use that product's documented import mechanism if available.

`deep-research` is an optional external workflow mentioned by this skill; it is not included. If unavailable, the agent should use permitted research tools when appropriate or mark unresolved external facts `NOT VERIFIED`. CWE is not required to use this skill.
