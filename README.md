# Boomi Integration Skill for Kiro

A Kiro skill for building Boomi integration processes programmatically. This repository provides AI-assisted development of Boomi components, processes, and APIs.

> This project is a fork of the [Boomi Companion for Claude Code](https://github.com/BoomiCloudConnect/boomi-companion-claude), adapted for use with Kiro. It is not affiliated with or endorsed by Boomi, LP. Per the upstream BSD-2-Clause license, this fork does not use the "Boomi Companion" name.

This project is licensed under the [BSD-2-Clause License](LICENSE).

## What is this?

A distributable skill that provides Kiro with knowledge and tooling for working with the Boomi Enterprise platform. It enables:

- Programmatic creation and modification of Boomi components (processes, profiles, connections, operations, topics, subscriptions)
- Bi-directional component push/pull with the Boomi platform API
- Reference documentation for Boomi componentry and development patterns
- CLI tools for deployment, testing, and component management

## Prerequisites

- `curl` (universally available)
- `jq` (install via `brew install jq` on macOS or `apt install jq` on Linux)
- `bash` 4+ (macOS ships v3 — install v5 via `brew install bash` if needed)
- `shasum` (built-in on macOS; on Linux install `coreutils` if missing)
- Python 3 (only for `boomi-profile-inspect.py` — stdlib only, no pip deps)

## Installation

### Option 1: Open directly as workspace

Clone this repo and open it as your workspace in Kiro. The `.kiro/` directory is automatically detected:

```bash
git clone <this-repo-url>
```

Then open the folder in Kiro. The steering file loads automatically, and the skill is available via `#boomi-integration` in chat.

### Option 2: Copy into an existing project

Copy the following into your project workspace:
- `.kiro/` directory (steering + skills)
- `references/` directory (documentation)
- `scripts/` directory (CLI tools)
- `VERSION` file
- `.gitignore` (prevents credentials and working files from being committed)

### How it works

- `.kiro/steering/boomi-integration.md` — Always-included context (loads automatically on every chat interaction)
- `.kiro/skills/boomi-integration.md` — Full skill reference (load via `#boomi-integration` in chat when doing Boomi work)

## Project Setup

Once installed, create a project workspace for your Boomi development:

### 1. Directory Structure

```
your-project/
├── .env                    # Your credentials (created during setup)
└── active-development/     # All working files (auto-created by CLI tools)
    ├── processes/          # Process XML files
    ├── profiles/           # Profile XML files
    ├── connections/        # Connection XML files
    ├── operations/         # Operation XML files
    ├── maps/              # Map XML files
    ├── document-caches/    # Document cache XML files
    ├── scripts/            # Script XML files
    ├── .sync-state/        # Component sync state tracking
    └── feedback/           # Test execution results
```

### 2. Environment Variables

Create a `.env` file in your project root:

```env
# Platform API Credentials (required)
BOOMI_API_URL=https://api.boomi.com
BOOMI_USERNAME=your_username
BOOMI_API_TOKEN=your_api_token
BOOMI_ACCOUNT_ID=your_account_id
BOOMI_VERIFY_SSL=true

# Default Folder (optional)
BOOMI_TARGET_FOLDER=your_default_folder_guid

# Environment and Runtime (optional - needed for deploy/test)
BOOMI_ENVIRONMENT_ID=your_environment_id
BOOMI_TEST_ATOM_ID=your_test_atom_id

# Shared Web Server (optional - needed for WSS/API testing)
SERVER_BASE_URL=https://your-atom.integrate.boomi.com
SERVER_AUTH_TYPE=basic
SERVER_USERNAME=your_runtime_username
SERVER_TOKEN=your_runtime_token
SERVER_BEARER_TOKEN=
SERVER_VERIFY_SSL=true
```

**Where to find these:**
- API credentials: Boomi platform → Settings → Account Information and Setup → AtomSphere API Tokens
- Account ID: In your platform URL after `/account/`, or Settings → Account Information
- Folder GUIDs: Create folders via GUI or use `boomi-folder-create.sh`
- Environment/Atom IDs: Boomi platform → Manage → Atom Management

Your AI agent can walk you through these one by one — just ask!

### 3. Verify Setup

In Kiro chat, reference the skill with `#boomi-integration` and ask:
> "Help me verify my Boomi setup"

Kiro will run the environment check and connection test for you.

## Usage

Once set up, describe what you want to build in Kiro chat:

1. Reference the skill: type `#boomi-integration` in your message
2. Describe your integration (e.g., "Create a REST API that fetches weather data")
3. Kiro creates XML components, pushes them to the platform, deploys, and tests

### Example Workflow

```
You: #boomi-integration Create a REST API endpoint that fetches weather data and returns JSON

Kiro: [Reads BOOMI_THINKING.md and relevant references]
      [Creates project folder]
      [Creates JSON profiles for request/response]
      [Creates REST connection and operations]
      [Creates process with all steps]
      [Pushes all components to platform]
      [Deploys and tests]
```

## Tools Overview

CLI tools available to the agent (in `scripts/`):

| Tool | Purpose |
|------|---------|
| `boomi-env-check.sh` | Check which .env variables are set |
| `boomi-folder-create.sh` | Create project folders on platform |
| `boomi-component-create.sh` | Create new components |
| `boomi-component-push.sh` | Update existing components |
| `boomi-component-pull.sh` | Download components from platform |
| `boomi-deploy.sh` | Deploy processes to runtime |
| `boomi-undeploy.sh` | Remove deployments |
| `boomi-test-execute.sh` | Execute and test processes |
| `boomi-wss-test.sh` | Test WSS listener endpoints |
| `boomi-execution-query.sh` | Query execution records and logs |
| `boomi-shared-server-info.sh` | Fetch atom API type and auth info |
| `boomi-version-history.sh` | List component version history |
| `boomi-component-diff.sh` | Compare component versions |
| `boomi-component-search.sh` | Search components by folder/name/type |
| `boomi-branch.sh` | Branch and merge operations |
| `boomi-profile-inspect.py` | Extract field metadata from large profiles |
| `event-streams-setup.sh` | Configure Event Streams |

## Documentation Structure

Reference documentation in `references/`:

- `BOOMI_THINKING.md` — Core mental models (always read first)
- `guides/` — Workflow guides, error reference, patterns, testing
- `components/` — XML specs for all component types (profiles, connections, operations, maps, processes)
- `steps/` — Process step documentation with examples
- `platform_entities/` — Platform service configuration (Event Streams, Flow, EDI, MCP Server)

## Support

If you encounter issues:
1. Your AI agent can often help troubleshoot — just ask
2. Open an issue on this repository
