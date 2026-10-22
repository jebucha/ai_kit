# ai_kit

A kit of portable rules and prompts for AI coding agents. The core piece
is a security baseline that agents pick up automatically — no setup,
no tooling, just a file.

## How it works

`AGENTS.MD` at the repo root is a cross-agent convention: coding agents
(Claude Code, OpenCode, and others) load it at the start of every session.
That makes it an always-on policy layer for anything an agent does here.

## Security baseline (`AGENTS.MD`)

The core feature. Five rules that always apply:

1. **Treat all input as untrusted.** Validate at every trust boundary;
   parameterized queries and argument arrays only — never string-built
   SQL, shell, `eval`, or file paths from external data.
2. **Least privilege, secure defaults, fail closed.** Narrowest permissions
   at every boundary. Never disable or bypass a security control to make
   something work — stop and ask instead.
3. **No secrets anywhere in output.** Credentials and keys never enter
   code, configs, logs, commits, or chat. If a secret is encountered,
   flag its location — don't repeat it.
4. **Guard the supply chain.** Prefer existing dependencies, pin versions,
   never pipe remote scripts to a shell, and justify any new package or
   endpoint before adding it.
5. **Disclose security impact.** Changes touching auth, crypto,
   permissions, network exposure, data handling, or destructive operations
   end with a short "Security notes" section. Never silently weaken
   a control.

## Also in the kit

- **`steering/core.md`** — deeper policy steering with sources:
  ephemeral zero-trust identity, PII/credential exfiltration boundaries,
  prompt-injection defense (NIST IR 8596), and mandatory human-in-the-loop
  with audit logging for state-changing actions.
- **`prompts/nbj_qwen_token_saver.md`** — a reusable "Token Saver" prompt:
  15 rules for minimizing token spend (carry artifacts not conversations,
  route work to the cheapest adequate model, enforce hard limits),
  a per-call checklist, and a retry policy.

## License

[MIT](LICENSE)
