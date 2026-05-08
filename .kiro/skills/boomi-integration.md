---
name: boomi-integration
description: Develops and deploys Boomi integrations, APIs, and platform services including Event Streams, Flow Services, and web service endpoints. Use when building, modifying, or deploying Boomi components (processes, profiles, connections, operations, maps, API components, Flow Services). Handles bi-directional sync, deployment automation, testing, and provides comprehensive Boomi reference documentation for complete solution development.
inclusion: manual
---

# Implementing Boomi with this Skill
This is the Boomi Process Development Framework - a reusable skill that enables AI coding agents to build Boomi integration processes programmatically. It provides CLI tools, reference documentation, and patterns for bi-directional synchronization with the Boomi platform API.

**Architecture**: The framework is separated from project components:
- **boomi-integration skill**: Reusable infrastructure, tools, documentation
- **active-development/** (project root): All working files - components, sync state, feedback

**Running CLI tools**: `<skill-path>` = the directory this skill was loaded from. All script invocations throughout the documentation use `<skill-path>/scripts/` — always substitute the real absolute path when constructing bash commands. Run from the project workspace directory so `.env` and `active-development/` are found correctly.

## Documentation Architecture

**This skill file is the navigation hub**: This file contains file references and routing guidance. Other documentation files contain minimal cross-references by design - this prevents deep hierarchical dependencies (which causes skimming at lower levels) and keeps navigation centralized.

**Complex tasks require multiple files**: Most Boomi development tasks require consulting 3-5+ documentation files together. The "Use when" guidance throughout this file indicates which combinations to load for specific scenarios.

**Common multi-file workflows:**
- **Adding any step**: BOOMI_THINKING.md + process_component.md + steps/[step].md + dependency component docs
- **Creating connectors**: connection_component.md + operation_component.md + connector_step.md + BOOMI_THINKING.md
- **REST API on Advanced atom**: api_service_component.md + web_services_server_start_shape_operation.md + process_component.md + api_conversion_patterns.md
- **Map transformations**: map_component.md + map_component_functions.md + source/target profile docs
- **Event Streams**: event_streams_connection + operation + steps + platform_entities/event_streams.md
- **B2B/EDI Trading Partners**: trading_partner_component.md + trading_partner_steps.md + edi_profile_component.md + platform_entities/edi_b2b.md
- **Disk V2 (File System)**: diskv2_connection_component + diskv2_connector_operation_component + diskv2_connector_step
- **MFT (Managed File Transfer)**: mft_connection_component + mft_connector_operation_component + mft_connector_step
- **MCP Server (AI Tool Exposure)**: mcp_server_connection_component + mcp_server_operation_component + mcp_server_start_step + platform_entities/mcp_server.md
- **Agent Step (AI Agent in process)**: agent_step.md
- **Flow Services**: fss_operation_component + fss_start_step + flow_service_component + platform_entities/flow.md
- **Debugging**: boomi_error_reference.md + relevant step/component docs
- **Branch & Merge** (opt-in only): branch_merge_guide.md + cli_tool_reference.md branch workflows section
- **Version management**: version_management_guide.md + cli_tool_reference.md version management section

## First-Time User Detection
**Check before starting any Boomi work**: The `scripts/` directory is provided by this skill — ensure it is loaded before invoking CLI tools. Run `bash <skill-path>/scripts/boomi-env-check.sh` to see which variables are SET vs UNSET (values are never exposed). Then run `bash <skill-path>/scripts/boomi-folder-create.sh --test-connection` to verify platform access. If credentials are missing, guide the user through `references/guides/user_onboarding_guide.md`.

## Connection Discovery & Credential Security
**Connection re-use is recommended.** Pulling existing connections keeps credentials out of the conversation. Offer the connection discovery workflow first, but respect the user's choice if they prefer to provide credentials directly. See `references/BOOMI_THINKING.md` § Connection Discovery for the full workflow.

## Workspace Organization & Knowledge Base
### Physical Directory Structure & Documentation Inventory
**Core Mental Models:**
- `references/BOOMI_THINKING.md` - Core mental models and development philosophy (always read first)
- `references/guides/boomi_patterns.md` - Step-by-step implementation recipes for common integration scenarios (read when designing a new process or refactoring significantly)
- `references/guides/boomi_error_reference.md` - Error patterns, silent failures, and troubleshooting (read early in any troubleshooting effort)
- `references/guides/boomi_platform_reference.md` - Platform services catalog (DataHub, Flow, API Gateway, B2B/EDI) with scope boundaries (read when designing a new process, evaluating designs that expand beyond integration, or refactoring significantly)
- `references/guides/problem_solving_guide.md` - Tiered escalation framework for handling unexpected situations, unknown components, and undocumented scenarios (read when encountering undocumented components or unexpected behavior)

**Step Type References:**
Step documentation in `references/steps/` covers all in-scope step types with working examples.

**Component Type References:**
Component documentation in `references/components/` covers all in-scope component types with working examples.

### Reading Discipline
**Required Reading for Boomi Work:**
When working on any Boomi process or component modifications:
1. **Always start by reading** `references/BOOMI_THINKING.md` - contains essential Boomi development philosophy and patterns
2. **Then load specific references** based on the task:
   - Building/modifying a process? Load `references/components/process_component.md`
   - Adding a specific step? Load `references/steps/[step_type].md` (e.g., `rest_connector_step.md`, `map_step.md`)
   - Creating a component? Load `references/components/[component_type].md`

**READ BEFORE WRITING**: Always read `references/steps/[step_type].md` completely before generating XML. Validation errors typically mean the XML doesn't match documented structure.

**Large Profiles**: When attempting to read a profile file (XML, EDI, or Flat File) and encountering a "file too large" error, immediately run `boomi-profile-inspect.py` (Python stdlib) on it. This generates a searchable JSON inventory at `active-development/profiles/distilled_<name>.json`. Search that file for field keys/paths, and grep the original XML by key if comments are needed.

**External Documentation Strategy:**
Default to the local `references/` content — it is curated and verified for this skill's use cases. As a fallback, `developer.boomi.com` and `help.boomi.com` are fetchable and can supplement local docs. `community.boomi.com` remains inaccessible (JavaScript-heavy). If all sources fail: ask the user to paste content. Do not proceed without critical information.

**Skill Repository:**
```
boomi-integration/                   # full skill path provided at skill load time
├── .kiro/
│   ├── steering/
│   │   └── boomi-integration.md     # Always-included steering (development notes)
│   └── skills/
│       └── boomi-integration.md     # This file - main skill definition and navigation hub
├── README.md                    # Installation and setup for new users
│
├── references/             # Comprehensive Boomi platform knowledge base
│   ├── BOOMI_THINKING.md        # Core mental models and development philosophy (always read first)
│   │
│   ├── guides/                  # Topical guidance and workflow docs
│   │   ├── user_onboarding_guide.md     # First-time user setup: .env creation, connection testing
│   │   ├── cli_tool_reference.md        # Read when: using CLI tools - command syntax, workflows, sync state, error recovery
│   │   ├── pulling_components.md        # Read when: user provides platform URL or component ID to work on
│   │   ├── process_testing_guide.md     # Read when: deploying and testing processes - execution workflows, log analysis
│   │   ├── api_conversion_patterns.md   # Read when: converting process to API or building WSS listeners
│   │   ├── boomi_patterns.md            # Step-by-step implementation recipes for common scenarios
│   │   ├── boomi_error_reference.md     # Error patterns, silent failures, and troubleshooting
│   │   ├── boomi_platform_reference.md  # Platform services catalog (DataHub, Flow, APIM, B2B/EDI) with scope boundaries
│   │   ├── api_endpoint_guide.md        # Sample developer friendly APIs for experimentation
│   │   ├── branch_merge_guide.md        # Read when: user explicitly requests branch/merge workflows
│   │   ├── branch_merge_api_behavior.md # API-level branch semantics — last resort when CLI tools don't cover an edge case
│   │   ├── version_management_guide.md  # Read when: viewing component version history, comparing versions, or rolling back
│   │   ├── event_streams_rest_api.md    # REST produce API reference (auth, payloads, limits)
│   │   └── edi_sap_patterns.md          # Read when: EDI ↔ SAP IDoc integration
│   │
│   ├── components/              # Component XML reference documentation
│   │   ├── process_component.md
│   │   ├── json_profile_component.md
│   │   ├── xml_profile_component.md
│   │   ├── flat_file_profile_component.md
│   │   ├── edi_profile_component.md
│   │   ├── map_component.md
│   │   ├── map_component_functions.md
│   │   ├── rest_connection_component.md
│   │   ├── rest_connector_operation_component.md
│   │   ├── http_client_component.md
│   │   ├── databasev2_connection_component.md
│   │   ├── databasev2_connector_operation_component.md
│   │   ├── event_streams_connection_component.md
│   │   ├── event_streams_listen_operation_component.md
│   │   ├── event_streams_consume_operation_component.md
│   │   ├── event_streams_produce_operation_component.md
│   │   ├── salesforce_connection_component.md
│   │   ├── salesforce_connector_operation_component.md
│   │   ├── boomi_for_sap_connection_component.md
│   │   ├── boomi_for_sap_connector_operation_component.md
│   │   ├── custom_connector_connection_component.md
│   │   ├── diskv2_connection_component.md
│   │   ├── diskv2_connector_operation_component.md
│   │   ├── mft_connection_component.md
│   │   ├── mft_connector_operation_component.md
│   │   ├── web_services_server_start_shape_operation.md
│   │   ├── api_service_component.md
│   │   ├── fss_operation_component.md
│   │   ├── flow_service_component.md
│   │   ├── mcp_server_connection_component.md
│   │   ├── mcp_server_operation_component.md
│   │   ├── trading_partner_component.md
│   │   ├── cross_reference_table_component.md
│   │   ├── process_property_component.md
│   │   ├── document_cache_component.md
│   │   └── process_extensions.md
│   │
│   ├── steps/                   # Process step XML reference documentation
│   │   ├── start_step.md
│   │   ├── rest_connector_step.md
│   │   ├── databasev2_connector_step.md
│   │   ├── salesforce_connector_step.md
│   │   ├── boomi_for_sap_step.md
│   │   ├── custom_connector_step.md
│   │   ├── diskv2_connector_step.md
│   │   ├── mft_connector_step.md
│   │   ├── event_streams_steps.md
│   │   ├── agent_step.md
│   │   ├── message_step.md
│   │   ├── map_step.md
│   │   ├── set_properties_step.md
│   │   ├── data_process_step.md
│   │   ├── data_process_groovy_step.md
│   │   ├── decision_step.md
│   │   ├── route_step.md
│   │   ├── branch_step.md
│   │   ├── process_call_step.md
│   │   ├── try_catch_step.md
│   │   ├── exception_step.md
│   │   ├── notify_step.md
│   │   ├── return_documents_step.md
│   │   ├── stop_step.md
│   │   ├── fss_start_step.md
│   │   ├── mcp_server_start_step.md
│   │   ├── trading_partner_steps.md
│   │   ├── document_cache_steps.md
│   │   └── shape_notes.md
│   │
│   └── platform_entities/       # Platform service configuration and management
│       ├── edi_b2b.md
│       ├── event_streams.md
│       ├── boomi_for_sap.md
│       ├── flow.md
│       ├── mcp_server.md
│       └── shared_web_server.md
│
└── scripts/                       # CLI tools — invoke as <skill-path>/scripts/<tool>.sh
    ├── boomi-common.sh
    ├── boomi-folder-create.sh
    ├── boomi-component-create.sh
    ├── boomi-component-push.sh
    ├── boomi-component-pull.sh
    ├── boomi-deploy.sh
    ├── boomi-test-execute.sh
    ├── boomi-wss-test.sh
    ├── boomi-shared-server-info.sh
    ├── boomi-execution-query.sh
    ├── boomi-profile-inspect.py
    ├── boomi-undeploy.sh
    ├── boomi-version-history.sh
    ├── boomi-component-diff.sh
    ├── boomi-component-search.sh
    ├── event-streams-setup.sh
    └── boomi-branch.sh
```

**User Project Workspace** (separate for each project):
```
user-project/
├── .env                        # Credentials (gitignored)
└── active-development/         # All working files (ephemeral — cleaned up on review)
    ├── processes/
    ├── profiles/
    ├── connections/
    ├── operations/
    ├── maps/
    ├── flow-services/
    ├── document-caches/
    ├── scripts/
    ├── .sync-state/
    ├── feedback/
    └── inventories/
```

Component organization follows standard folder structure (see `references/guides/cli_tool_reference.md` for component type to folder mapping).

## Development Philosophy

**Project-First Organization:**
Create dedicated Boomi platform folders for each integration/feature/API using the naming convention: `ProjectName-ShortDescription` (e.g., `AcmeMVP-InventorySync`). Never push components without a folder ID.

**Complete Programmatic Development:**
- Implement ALL steps programmatically (Set Properties, Maps, REST connectors, Decision, Process Components)
- Read `references/` documentation BEFORE writing code
- Fix XML structure when errors occur rather than escalating to GUI
- Use GUI only for true platform limitations: OAuth user authorization grant flows, metadata refresh, branded connector initial import
- Out-of-scope or undocumented types: Follow escalation framework in `references/guides/problem_solving_guide.md`

**Key Boomi Concepts** (from `BOOMI_THINKING.md`):
- Documents flow sequentially through steps (pipeline model)
- Steps = canvas instances, Components = reusable definitions
- Profile-first development: create profiles before referencing fields
- Push-as-you-go: create → push → use generated IDs for next component
- Properties (DDP/DPP) carry data through process
- Complex projects: wireframe first, then incrementally add/update steps (push after each)

## Critical Boomi Issues
Boomi has several silent failure patterns that are critical to understand. These don't throw errors but produce wrong behavior. **Read `references/guides/boomi_error_reference.md` early in any troubleshooting effort** — most debugging dead-ends trace back to a known issue.

**Most Critical Patterns:**
1. **Message step Quote Escaping with a JSON body** - Use `"'{1}'"` not `"{1}"` in JSON - single quotes toggle curly-brace {} variable substitution mode. Example valid message shape content with JSON: `'{"example":"'{1}'"}'`. Message steps without JSON don't generally require this toggle.
2. **Environment Variables Don't Work** - XML components need actual credential values, not `${ENV_VAR}` references
3. **Parent-Subprocess Deployments** - Must redeploy parent after updating subprocess to pick up changes

**Quick diagnostic:** Variables appearing literally? → Issue #1. API auth failures? → Issue #2. Subprocess changes ignored? → Issue #3.

## Core Development Workflow

### Component Lifecycle: Prefer Existing Over New
**Decision Framework:**
1. **Connections**: Resolve via connection discovery workflow first (see § Connection Discovery above)
2. **Other components**: Check for existing components to update/reuse before creating new ones
3. Update when possible: Use push/pull workflow for existing components
4. Create only when necessary: New components only when genuinely needed
5. Consolidate, don't duplicate: Enhance existing similar components

See `references/guides/cli_tool_reference.md` for workflow selection and command syntax.

### Pulling Components with Dependencies
When user provides component ID or platform URL: Pull the component, scan for dependencies, pull missing ones recursively. See `references/guides/pulling_components.md` for complete workflow.

**XML Modification Philosophy:**
- Leave pulled XML as-is (if platform accepted it, it's valid)
- Create new components using minimal format from templates
- Don't normalize existing verbose XML

## Architecture and Key Concepts

**Local-First Development Model:**
1. **Local Development**: Create/edit XML files in `active-development/` folder
2. **Platform Sync**: CLI tools handle bi-directional sync with Boomi platform
3. **State Tracking**: `.sync-state/` maintains component IDs, versions, conflict detection
4. **Testing Integration**: Integrated deployment, execution, result polling, log extraction
5. **Knowledge Base**: `references/` contains patterns and templates

**Sync State Management:**
The `.sync-state/` directory tracks component synchronization (IDs, versions, conflict detection). Managed automatically by CLI tools—never manually edit.

**CLI Tools:**
Eleven specialized tools handle development lifecycle. All tools are bash scripts (except profile-inspect which is Python stdlib). They require `curl` and `jq` and source credentials directly from `.env` — no Python dependencies, no virtual environments.

- `boomi-folder-create.sh` - Create project folders on platform
  - Required: `folder_name` (positional)
  - Optional: `--parent-folder-id`, `--test-connection`

- `boomi-component-create.sh` - Create new component on platform (generates component ID)
  - Required: `file_path` (positional)
  - Optional: `--branch`, `--test-connection`

- `boomi-component-push.sh` - Update existing component on platform
  - Required: `file_path` (positional)
  - Optional: `--branch`, `--test-connection`, `--force` (bypass content hash check — needed for rollback pushes)

- `boomi-component-pull.sh` - Download component from platform to local
  - Required: `--component-id`
  - Optional: `--branch`, `--target-path`, `--version N` (retrieve a specific historical version)

- `boomi-deploy.sh` - Deploy process to runtime environment
  - Required: `file_path` (positional)
  - Optional: `--deployment-notes`, `--list-environments`
  - Auto-detects branch from XML/sync state and warns before deploying branch components

- `boomi-version-history.sh` - List component version history via ComponentMetadata/query
  - Required: `--component-id`
  - Optional: `--branch` (filter by branch name), `--current` (show only current version)

- `boomi-component-diff.sh` - Compare two versions of a component via ComponentDiffRequest
  - Required: `--component-id`, `--source <N>`, `--target <N>`

- `boomi-component-search.sh` - Query components by `--folder <id|name|%pattern%>` (flat, multiple matches unioned), `--name <%pattern%>`, `--type <csv>` (API-level types — `connector-settings`=connection, `connector-action`=operation), or `--related-to <id>` (cannot combine with other filters). Writes `active-development/inventories/component_search_<timestamp>.json`; implicit `currentVersion=true`, `deleted=false` on non-related-to queries.

- `boomi-branch.sh` - Branch and merge operations (only for Branch & Merge enabled accounts)
  - `list` — list all branches
  - `create --name NAME --parent NAME` — create branch from parent
  - `delete --branch NAME_OR_ID` — delete a branch
  - `merge --source NAME --dest NAME [--strategy OVERRIDE|CONFLICT_RESOLVE]` — create merge request
  - `merge-status --id ID` — check merge request status and component details
  - `merge-execute --id ID` — execute a pending merge
  - `merge-revert --id ID` — revert a completed merge (permanent)
  - `merge-delete --id ID` — cancel a pending merge request

- `boomi-undeploy.sh` - Remove deployments from a runtime environment
  - Modes: `<deploymentId>` (direct removal), `--by-component <file_path>` (lookup and remove via component file)

- `boomi-test-execute.sh` - Trigger process execution via platform API and return execution ID
  - Required: `--process-id`
  - Optional: `--test-data`, `--no-wait`

- `boomi-wss-test.sh` - Test WSS listener endpoints via the shared web server
  - Required: `--path` (e.g., `/ws/simple/createOrder`)
  - Optional: `--method` (default POST), `--data` (inline JSON or file path), `--content-type` (default `application/json`)
  - Auth is driven entirely by `.env`: `SERVER_AUTH_TYPE` declares the scheme (`basic | bearer | none`), and the script reads the matching credential vars. Unset `SERVER_AUTH_TYPE` falls back to inference with hard-fail on ambiguity. See `references/platform_entities/shared_web_server.md` for the full model.

- `boomi-execution-query.sh` - Query execution records and download logs for any process type
  - Optional: `--process-id`, `--status`, `--since`, `--limit` (default 3)
  - Log download: `--execution-id <id> --logs`

- `boomi-profile-inspect.py` (Python stdlib) - Extract field inventory from large profiles (XML, EDI, Flat File)
  - Required: `profile_path` (positional)
  - Use when: A profile file is too large to read directly. Outputs searchable JSON to `active-development/profiles/distilled_<name>.json`
  - Python stdlib only — no pip dependencies

- `event-streams-setup.sh` - Create and manage Event Streams topics, subscriptions, and tokens
  - Commands: `query-tokens`, `create-token <name>`, `create-topic <name>`, `create-subscription <topic> <name>`, `query-topic <name>`

The CLI tools reside at `<skill-path>/scripts/`. They are not in a given active development workspace.

See `references/guides/cli_tool_reference.md` for workflows, error recovery, and usage patterns.

Re-use existing connections (see § Connection Discovery above). Component XML requires actual credential values, not environment variables. See `references/guides/cli_tool_reference.md` for patterns.

**Environment Variables:**
Required for building and testing. Full setup in `references/guides/user_onboarding_guide.md`.
- Platform API: `BOOMI_API_URL`, `BOOMI_USERNAME`, `BOOMI_API_TOKEN`, `BOOMI_ACCOUNT_ID`, `BOOMI_ENVIRONMENT_ID`, `BOOMI_TEST_ATOM_ID`, `BOOMI_VERIFY_SSL`, `BOOMI_TARGET_FOLDER`
- Shared Web Server: `SERVER_BASE_URL`, `SERVER_AUTH_TYPE` (`basic | bearer | none`; unset = infer), `SERVER_USERNAME` + `SERVER_TOKEN` (Basic), `SERVER_BEARER_TOKEN` (Bearer), `SERVER_VERIFY_SSL` — used for WSS testing and **FSS connectivity** (see `references/guides/process_testing_guide.md`, `references/platform_entities/flow.md`). Boomi public cloud runtimes only support Basic; local runtimes can use other auth modes — see `references/platform_entities/shared_web_server.md` before assuming Basic is required.

## Development Patterns

### Push-as-You-Go Workflow (Recommended)
1. Create component locally → Push immediately → Verify sync state
2. Read `.sync-state/` for generated component IDs
3. Update dependent components with actual GUIDs
4. Repeat for next component

**Dependency order (per section):** When a step needs a component, create its dependencies first: profiles → connections → operations → then the step.

**Complex processes:** Create wireframe with placeholder steps first, push, then incrementally update step-by-step (push after each).

**Anti-pattern:** Creating many components locally before pushing causes "big bang" sync failures.

### Web Services Listener Pattern
Complete guidance in `references/guides/api_conversion_patterns.md`. Quick decisions:
- **Before building any listener or API**: run `bash <skill-path>/scripts/boomi-shared-server-info.sh $BOOMI_TEST_ATOM_ID` and route by `apiType` — `basic`/`intermediate` → bare WSS listener; `advanced` → API Service Component
- Converting existing process to API? → Wrap it
- Building new API endpoint? → Wrapper + subprocess pattern
- Deployment issues? → Check atom API tier compatibility

### Iterative Development Workflow

**Default: Push for Review**
Load `BOOMI_THINKING.md` + relevant references → Create/modify XML → Verify Message/Notify quote escaping → Push.

**Full Deploy & Test**
**REQUIRED: Read `references/guides/process_testing_guide.md` before running any tests.** The testing workflow has critical constraints that affect how you approach testing different process types.

When you need to test a process: Push → Deploy (`boomi-deploy.sh`) → Wait 10-15s → Execute tests → Analyze results.

**Critical ExecutionRequest Limitation:** The Boomi API cannot inject document payloads into process executions. The `--test-data` flag is for Process Properties only (rarely used). Injecting test payloads is best achieved with message set properties steps in the process canvas.

Review references/guides/process_testing_guide.md for more info about methods and techniques to work with test payloads.

**Testing Philosophy**
- Projects vary - simple one-shots may not need testing; interactive sessions where users test in GUI don't require your testing.
- When working autonomously on end-to-end projects, test frequently: build a section → add Notify steps to log outputs → deploy → run → review logs.
- When building a listener process, test via curl to the WSS endpoint (see `references/guides/process_testing_guide.md` for URL construction).
- When testing subprocess logic in isolation, you can add a temporary Message step with test payload at the start.

**Push vs Deploy:** Push = design-time update (GUI visible, not executable). Deploy = runtime update (executable, required for testing).

**Handling Validation Errors:**
Read error → Check `references/steps/` documentation → Compare with examples → Fix XML → Retry. Common issues: wrong shapetype, wrong configuration element, missing attributes (see `references/guides/boomi_error_reference.md` Issue #8).

For situations beyond validation errors — unknown components, unexpected API behavior, undocumented features — see `references/guides/problem_solving_guide.md`.

### Critical Deployment Pattern
**Parent-Subprocess Dependency:** When updating subprocesses, ALWAYS redeploy parent to pick up changes. The parent deploy includes the subprocess — deploying the subprocess alone will not cause the parent to reflect those changes.

```bash
# 1. Update subprocess
bash <skill-path>/scripts/boomi-component-push.sh subprocess.xml

# 2. CRITICAL: Redeploy parent
bash <skill-path>/scripts/boomi-deploy.sh parent-wrapper.xml

# 3. Test
```

**Exception — Standalone subprocess testing or execution:** When testing a subprocess in isolation via `boomi-test-execute.sh` (not through its parent), the subprocess must be deployed independently. The runtime will otherwise run a stale version that doesn't reflect recent pushes unless the subprocess itself is deployed.

This also applies to designs where a subprocess may either run independently or be called by a parent - in that scenario also the subprocess must be deployed independently in addition to deploying the parent process.

```bash
# Isolated subprocess testing
bash <skill-path>/scripts/boomi-component-push.sh subprocess.xml
bash <skill-path>/scripts/boomi-deploy.sh subprocess.xml  # Required for standalone execution
bash <skill-path>/scripts/boomi-test-execute.sh --process-id <subprocess-guid>
```

See `references/guides/boomi_error_reference.md` Issue #3 for details.

### Folder Management & Component Creation
**Organization Hierarchy:**
- Root → Target Folder (`BOOMI_TARGET_FOLDER`) → Project-Specific → Components
- **ALL components MUST go into organized folders**, never create components into the account root
- Create project folders using naming convention: `ProjectName-ShortDescription`
- Example: `bash <skill-path>/scripts/boomi-folder-create.sh "AcmeCorp-EmailNotification"`

**Component Creation Workflow:**
```bash
# 1. Create project folder with proper naming convention
bash <skill-path>/scripts/boomi-folder-create.sh "Acme-MVP-WeatherAPI"
# Returns: folder_abc123def

# 2. Create components with folderId in XML (push after each to get IDs for dependencies)
bash <skill-path>/scripts/boomi-component-create.sh active-development/profiles/profile.xml
```

**Folder Naming Convention:**
Format: `ProjectName-ShortDescription`
- Examples: `TechCorp-OrderSync`, `Acme-APIShowcase`

**Critical Requirements:**
- Use actual folder GUID in `folderId` attribute (not `folderFullPath`)
- Use descriptive names: `SystemName_Action_Type` (e.g., `Petstore_GetPet_Response_JSON_Profile`)
- Keep componentId and version as empty strings for CREATE operations
- Reference templates in `references/components/`

See `references/guides/boomi_error_reference.md` Issue #7 for folder placement verification.

**Handling Blocked Dependencies:**
When dependencies are unavailable (missing credentials, GUI-required components, API access pending), use placeholder pattern: Create named placeholder step → Add parallel test Message step with mock data → Route both to downstream logic → Replace placeholder when dependency available. Inform user of blocking issue.

**GUI-Required Components:** OAuth flows (browser authorization) and branded connectors (Salesforce, NetSuite - metadata import) require GUI for initial setup. For all connections, follow the connection discovery workflow (see § Connection Discovery above) — re-use existing connections or have the user create new ones in the GUI. Once pulled, preserve encrypted values exactly during subsequent pull/push cycles.

### Step Addition Workflow
**ALWAYS read `references/steps/[step_type].md` completely before writing XML.**

**Sequential Process:**
1. Read complete step documentation (study reference examples)
2. Create required dependencies for this step (profiles, connections, operations as needed)
3. Follow exact XML structure from documentation
4. Validate before push
5. Read sync state and update dependent component references

**Common XML Mistakes** (see `references/guides/boomi_error_reference.md` Issue #8):
- Set Properties: Use `shapetype="documentproperties"` NOT `"setproperties"`
- Map step: Simple `<map mapId="guid"/>` with no child elements
- Missing display attributes: Include `name` and `propertyName` for GUI

## Reference

**Boomi Terminology:**
- **Steps** = Canvas elements (formerly "shapes")
- **Components** = Reusable definitions referenced by steps
- **Documents** = Data chunks flowing through process
- **DDP** = Dynamic Document Property (per-document variable)
- **DPP** = Dynamic Process Property (process-wide variable)

**Authentication:**
- API Token format: `BOOMI_TOKEN.{username}`
- Credentials: Username + API token (not password)

**Deployment:**
- Always use environment IDs (GUIDs), not environment names

**Development Guidelines:**
- Never manually edit sync state files
- Build processes elegantly and simply (visually interpretable by moderately-tenured Boomi developers)
- Avoid convoluted scripting when simpler approaches exist
- If user makes GUI changes, re-pull component before editing
