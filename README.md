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
