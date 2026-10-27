# CLAUDE.md

@AGENTS.md

[AGENTS.md](AGENTS.md) is the canonical repository guide: architecture, commands, coding style, boundary contracts, and testing policy. Keep shared guidance there; this file only adds notes for Claude Code sessions.

## Fork

This repository is a fork of [morluto/rea](https://github.com/morluto/rea). Package metadata, `server.json`, `install.sh`, and the README intentionally keep the upstream identity. `install.sh` and `npx rea-agents setup` install upstream's published `rea-agents` package, not this fork. Do not run the release workflow or publish to npm from this fork.

## Session setup

```sh
HUSKY=0 npm ci
npm run build
npm run check:fast
npm run test:fast
```

Use npm 11 (`packageManager` in `package.json`) when changing dependencies. npm 10 drops the `libc` fields from `package-lock.json`.

## Known environment limits

Two boundary tests fail in cloud containers that run as root:

- `tests/boundary/providers/native/artifactProvider.directoryErrors.test.ts`: root can still read the `chmod 0` fixture, so the expected permission error never occurs.
- `tests/boundary/process/processCaptureLifecycle.test.ts`: the process environment probe reports `ESRCH` instead of `EACCES` or `EPERM`. The cause is not yet confirmed; rerun it as an unprivileged user before treating it as a regression.

Real-provider lanes (`verify:hopper`, `verify:ghidra`, `verify:ida`, `verify:browser`, and similar) need the matching engine installed; see [docs/testing.md](docs/testing.md).

## Secrets

Never commit credentials, tokens, or `.env` files. Use environment variables such as `GHIDRA_INSTALL_DIR` and `REA_IDA_MCP_CONFIG` for local provider configuration.
