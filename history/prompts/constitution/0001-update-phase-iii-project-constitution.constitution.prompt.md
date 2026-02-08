---
id: 0001
title: Update Phase III Project Constitution
stage: constitution
date: 2026-02-08
surface: agent
model: gemini-1.5-pro
feature: none
branch: main
user: sufi
command: /sp.constitution # Phase III Constitution — AI-Powered Todo Chatbot
labels: ["constitution"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
  - .specify/memory/constitution.md
tests:

---

## Prompt

/sp.constitution # Phase III Constitution — AI-Powered Todo Chatbot

## 1. Purpose

Phase III introduces an **AI-powered conversational interface** for managing todos via natural language. This phase adds **intelligence only**. No frontend or backend business logic is to be rebuilt or modified beyond what is strictly required to support chat-based interaction.

This constitution governs **what is allowed, what is forbidden, and how decisions are made** during Phase III.

---

## 2. Phase II Boundary (Non-Negotiable)

The following components were **fully implemented and validated in Phase II** and MUST NOT be reworked:

* Frontend UI and task management flows
* Backend CRUD APIs for tasks
* Database schema for tasks
* Authentication and authorization logic

### Allowed Changes

* Adding a **new chat endpoint**
* Adding **conversation and message tables**
* Adding **agent orchestration code**
* Wrapping existing task APIs as tools

### Forbidden Changes

* Rewriting task CRUD logic
* Adding alternate task storage paths
* Bypassing existing authorization checks
* Introducing new business rules for tasks

Phase III must **wrap intelligence around Phase II**, not replace it.

---

## 3. Core Architectural Principles

### 3.1 Intelligence via Tools, Not Direct Access

* The AI agent **must never access the database directly**
* All task operations are performed **only via tools**
* Tools encapsulate existing Phase II backend logic

### 3.2 Stateless Server Guarantee

* The server holds **no in-memory conversational state**
* Every request is self-contained and reproducible
* Conversation history is persisted in the database
* Server restarts must not affect correctness

### 3.3 Single Conversational Entry Point

* Exactly one chat endpoint is allowed:

  * `POST /api/{user_id}/chat`
* All natural language interaction flows through this endpoint

---

## 4. AI Framework & Execution Model

### 4.1 Gemini-Based Agent

* Phase III uses **Gemini CLI** as the LLM runtime
* No Claude-specific agent features are assumed
* No OpenAI Agents SDK is required

### 4.2 Agent Design Philosophy

The agent is implemented as a **tool-driven reasoning loop**:

1. Read conversation history
2. Interpret user intent
3. Select appropriate tool(s)
4. Execute tool calls
5. Synthesize a natural-language response

This design is **functionally equivalent to agent-skills**, while remaining vendor-agnostic.

---

## 5. MCP-Compatible Tooling Model

### 5.1 Tool Requirements

Each tool MUST:

* Be stateless
* Accept fully explicit parameters
* Return structured JSON only
* Persist all state via the database

### 5.2 Required Tools

* `add_task`
* `list_tasks`
* `update_task`
* `complete_task`
* `delete_task`

Tools are the **only bridge** between the AI agent and application state.

---

## 6. Conversation & Persistence Rules

### 6.1 Conversation Model

* A conversation represents a chat session per user
* Messages are immutable once stored
* Roles are strictly `user` or `assistant`

### 6.2 Stateless Request Cycle

For every chat request:

1. Fetch conversation history from database
2. Append new user message
3. Run Gemini agent with tools
4. Execute selected tool calls
5. Store assistant response
6. Return response to client

No intermediate state is retained in memory after the request completes.

---

## 7. Agent Behavior Rules

### 7.1 Intent-to-Tool Mapping

* Create intents → `add_task`
* Read intents → `list_tasks`
* Update intents → `update_task`
* Completion intents → `complete_task`
* Deletion intents → `delete_task`

### 7.2 Tool Chaining

* The agent may chain tools in a single turn
* Example: list tasks → select task → delete task

### 7.3 Ambiguity Handling

* If a request is ambiguous, the agent must clarify
* Destructive actions require confirmation

### 7.4 User Experience

* Responses must be friendly and clear
* Every action must be confirmed in natural language
* Errors must be explained without exposing internals

---

## 8. What Success Looks Like

Phase III is complete when:

* Users can manage todos entirely via natural language
* All task actions flow through MCP-compatible tools
* Conversations persist across server restarts
* The server remains stateless and horizontally scalable
* Phase II functionality remains untouched

---

## 9. Enforcement

If an implementation:

* Rewrites Phase II logic
* Stores state in memory
* Allows the agent to bypass tools
* Introduces multiple chat endpoints

…it is considered **out of scope for Phase III**.

This constitution overrides all other implementation preferences.

## Response snapshot

The project constitution has been updated with the provided content to `.specify/memory/constitution.md`. A Prompt History Record was manually created due to an issue with the PHR script.

## Outcome

- ✅ Impact: Constitution updated.
- 🧪 Tests: N/A
- 📁 Files: 1 file modified.
- 🔁 Next prompts: Next steps will depend on user's request.
- 🧠 Reflection: Manually created PHR due to script failure.

## Evaluation notes (flywheel)

- Failure modes observed: PHR creation script failed.
- Graders run and results (PASS/FAIL): N/A
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): Investigate PHR script execution.
