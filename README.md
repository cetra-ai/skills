# cetra agent skill

This skill lets a coding agent add cetra tracing to your app. It covers the Python and TypeScript SDKs and plain OpenTelemetry. The agent sends traces to your cetra server, then checks that they arrive.

## Install

Run the setup from your project. It installs the skill with the rest of the setup:

```bash
npx @cetra-ai/cli init
```

To install only the skill:

```bash
npx skills add cetra-ai/skills
```

In Claude Code, add the marketplace and install the plugin:

```text
/plugin marketplace add cetra-ai/skills
/plugin install cetra@cetra
```

In Codex and Cursor, add this repository in the plugin settings.

## Source

This repository is a copy of `skills/` in [cetra-ai/cetra](https://github.com/cetra-ai/cetra). It is updated on every change there. Send issues and pull requests to that repository.
