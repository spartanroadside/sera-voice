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

SERA
AI-Orchestrated IDE + Automation Platform

SERA is an AI-native IDE and automation platform that combines:

OpenAI (ChatGPT API)

SERA-Logic orchestration layer

Integrated development environment (Workbench + Editor)

Voice control ("Hey Sera")

Home automation (Home Assistant integration)

Structured Commands system

Learning Layer (user-programmable skills)

Plugin-ready automation architecture

Full execution state machine

Snapshot + rollback safety model

SERA is not just an IDE.
It is an AI execution and orchestration platform with a built-in development environment.

1. SYSTEM OVERVIEW

High-Level Architecture:

VOICE / CHAT / PALETTE
↓
UI LAYER
↓
SERA-LOGIC (Router + Policy Engine + Execution Engine)
↓
Internal Executors (IDE/Home/System/etc) OR OpenAI (ChatGPT API Tool Loop)
↓
ProposedChangeSet / CommandResult / ChatMessage
↓
Patch Review → Apply → Snapshot → Audit Log

2. CORE DESIGN PRINCIPLES

AI never writes directly to disk.

All file edits go through Patch Review.

All commands use an allowlist.

All executions have traceId + executionId.

High-risk actions require confirmation.

Snapshots occur before every apply.

Capabilities dynamically gate features.

Automation can be globally disabled.

Contracts are versioned.

Execution is observable and debuggable.

3. EXECUTION MODEL

Universal Message Envelope structure:

contractVersion

traceId

executionId

source (voice | chat | palette)

projectId

threadId

payload (text or command)

context (activeFile, selection, includeLogs, includeProjectSummary, etc.)

Execution States:

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

Commands are categorized by domain:

ide.*

home.*

research.*

system.*

workflow.*

Example command structure:

id

domain

slots (parameters)

executor

risk level

requiresConfirmation

Routing order:

User Skills

User Aliases

Built-in Commands

AI classification

Follow-up clarification

6. LEARNING LAYER

Users can extend SERA safely.

User Skills (Workflows) contain:

id

name

steps (command / if / notify / confirm)

policy (risk, confirmation)

enabled flag

version

Aliases map phrases to:

entities

projects

paths

skills

Memory stores:

default project

preferred model

dock preferences

voice routing defaults

Stored under:

.sera/learning/

7. AI TOOL CALL LOOP

SERA sends structured request to OpenAI.

Model may request tool execution.

SERA validates tool call.

File edits become ProposedChangeSet.

UI opens Patch Review.

Apply only after approval.

8. PROPOSED CHANGESET

A ChangeSet includes:

id

projectId

source (threadId, model)

summary

files[] (create / modify / delete / rename)

optional diff

9. PATCH REVIEW RULE

AI proposes.
Human approves.
System executes safely.

No file writes bypass review.

10. SECURITY & SAFETY

Path Sandbox:

Reject absolute paths

Reject ".."

Enforce project root

Denylist protected folders

Atomic Writes:

Write temp

fsync

Rename

fsync directory

Permission Scopes:

allowDomains

denyCommands

requireConfirmationFor

Risk Levels:

Low: lights, media
Medium: deploy, thermostat
High: locks, restart service

High-risk requires confirmation and optional PIN.

11. SNAPSHOTS & RECOVERY

Before every apply:

.sera/snapshots/<timestamp>_<changeSetId>/

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

.sera/audit.jsonl

13. RATE LIMITING & BUDGET

Per project and per user:

maxTokensPerHour

maxExecutionsPerMinute

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

Server returns capability flags such as:

canOpenFileManager

supportsTTS

supportsWebsocketLogs

canDeploy

UI hides unsupported features.

16. AUTOMATION KILL SWITCH

Global flag:

automationEnabled = false

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

19. PHILOSOPHY

AI proposes.
Human approves.
System executes safely.

This enables powerful automation without chaos.

FINAL STATE

If you have:

Versioned contracts

Trace IDs

Execution state machine

Patch review enforcement

Snapshots + undo

Permission scopes

Rate limiting

Capability gating

Kill switch

Observability logs

Learning layer

Plugin architecture


}
