# SERA
## AI-Orchestrated IDE + Automation Platform

SERA is an AI-native IDE and automation platform that combines:

- OpenAI (ChatGPT API)
- SERA-Logic orchestration layer
- Integrated development environment (Workbench + Editor)
- Voice control ("Hey Sera")
- Home automation (Home Assistant integration)
- Structured Commands system
- Learning Layer (user-programmable skills)
- Plugin-ready automation architecture
- Full execution state machine
- Snapshot + rollback safety model

SERA is not just an IDE.  
It is an AI execution and orchestration platform with a built-in development environment.

---

# 1. SYSTEM OVERVIEW

## High-Level Architecture

VOICE / CHAT / PALETTE  
        ↓  
UI LAYER  
        ↓  
SERA-LOGIC (Router + Policy Engine + Execution Engine)  
        ↓  
┌───────────────────────┬────────────────────────┐  
│ Internal Executors    │ OpenAI (ChatGPT API)   │  
│ (IDE/Home/System/etc) │ Tool Calling Loop      │  
└───────────────────────┴────────────────────────┘  
        ↓  
ProposedChangeSet / CommandResult / ChatMessage  
        ↓  
Patch Review → Apply → Snapshot → Audit Log  

---

# 2. CORE DESIGN PRINCIPLES

1. AI never writes directly to disk.
2. All file edits go through Patch Review.
3. All commands use an allowlist.
4. All executions have traceId + executionId.
5. High-risk actions require confirmation.
6. Snapshots occur before every apply.
7. Capabilities dynamically gate features.
8. Automation can be globally disabled.
9. Contracts are versioned.
10. Execution is observable and debuggable.

---

# 3. EXECUTION MODEL

## Universal Message Envelope

```json
{
  "contractVersion": 1,
  "traceId": "trace_123",
  "executionId": "exec_123",
  "source": "voice|chat|palette",
  "projectId": "proj_123",
  "threadId": "thr_123",
  "payload": {
    "type": "text",
    "content": "Fix the login bug"
  },
  "context": {
    "activeFile": "src/login.js",
    "selection": "",
    "includeLogs": true,
    "includeProjectSummary": false
  }
}
```

Execution States

idle

planning

awaiting_tool

executing

awaiting_confirmation

awaiting_followup

streaming

completed

failed

cancelled

timed_out

Every execution stores:

traceId

executionId

state transitions

timeline events

tool calls

token usage

latency

failure reasons

4. SERA-LOGIC RESPONSIBILITIES

Intent routing

Command classification

Skill resolution

Alias resolution

AI tool-call loop

Execution state tracking

Policy enforcement

Permission checks

Rate limiting

Snapshot enforcement

Audit logging

Plugin dispatching

5. COMMAND SYSTEM

Commands are structured and categorized by domain:

ide.*

home.*

research.*

system.*

workflow.*

Example Command
```json
{
  "id": "home.light.set",
  "domain": "home",
  "slots": {
    "area": "string",
    "state": "on|off"
  },
  "executor": "homeAssistant",
  "risk": "low",
  "requiresConfirmation": false
}
```
Routing Order

User Skills

User Aliases

Built-in Commands

AI Classification

Follow-up clarification

6. LEARNING LAYER

Users can safely extend SERA.

User Skills (Workflows)
```json
{
  "id": "skill_001",
  "name": "Build then deploy",
  "steps": [
    { "type": "command", "commandId": "ide.run_build" },
    {
      "type": "if",
      "condition": { "ref": "$last.status", "eq": "success" },
      "then": [
        { "type": "command", "commandId": "system.deploy_latest" }
      ]
    }
  ],
  "policy": {
    "requiresConfirmation": true,
    "risk": "medium"
  }
}
```
Aliases

"ship it" → skill_001

"kitchen light" → light.kitchen_ceiling

"myapp" → proj_123

Memory

Stores:

default project

preferred model

dock preferences

voice routing default

Stored in:

.sera/learning/

7. AI TOOL CALL LOOP

SERA sends structured request to OpenAI.

Model may call defined tools.

SERA validates tool call.

If file changes → create ProposedChangeSet.

Return to UI for Patch Review.

Apply only after approval.

8. PROPOSED CHANGESET
```json
{
  "id": "chg_123",
  "projectId": "proj_123",
  "source": {
    "type": "ai",
    "threadId": "thr_123",
    "model": "gpt-4o"
  },
  "files": [
    {
      "path": "src/login.js",
      "type": "modify",
      "content": "..."
    }
  ]
}
```
10. PATCH REVIEW RULE

AI proposes.
Human approves.
System executes safely.

No file writes bypass review.

10. SECURITY & SAFETY
Path Sandbox

Reject absolute paths
```json
Reject ".."
```
Enforce project root

Denylist: .git/, node_modules/, secrets/

Atomic Writes

write temp

fsync

rename

fsync directory

Permission Scopes
```json
{
  "allowDomains": ["ide", "research"],
  "denyCommands": ["system.restart"],
  "requireConfirmationFor": ["home.lock.*"]
}
```
Risk Levels

Low: lights, media
Medium: deploy, thermostat
High: locks, restart service

High risk requires confirmation + optional PIN.

11. SNAPSHOTS & RECOVERY

Before every apply:
```json
.sera/snapshots/<timestamp>_<changeSetId>/
```
Supports:

Undo last apply

Restore snapshot

Compare snapshot

Crash recovery

Audit replay

12. OBSERVABILITY

Each execution logs:

traceId

executionId

state transitions

tool latency

token usage

failure reasons

Stored in:
```json
.sera/audit.jsonl
```
13. RATE LIMITING & BUDGET

Per project and per user:
```json
{
  "limits": {
    "maxTokensPerHour": 100000,
    "maxExecutionsPerMinute": 10
  }
}
```
Prevents runaway automation.

14. ENVIRONMENT PROFILES

Profiles:

dev

staging

production

home

demo

Each defines:

permissions

AI strategy

risk policy

limits

endpoints

15. CAPABILITIES GATING

Server returns:
```json
{
  "canOpenFileManager": true,
  "supportsTTS": true,
  "supportsWebsocketLogs": true,
  "canDeploy": true
}
```
UI hides unsupported features.

16. AUTOMATION KILL SWITCH
```json
{
  "automationEnabled": false
}
```
Stops all command execution and tool calls.

17. PLUGIN SYSTEM

Plugins live under:

sera/plugins/

Each plugin defines:

domains

commands

tools

version

18. DIRECTORY STRUCTURE
```json
sera/
ui/
sera-logic/
router.js
policy.js
executors/
learning/
plugins/
.sera/
snapshots/
audit.jsonl
learning/
```
19. PHILOSOPHY

AI proposes.
Human approves.
System executes safely.

This enables powerful automation without chaos.
