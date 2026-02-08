---
id: 0002
title: Update Phase III Project Constitution (Gemini Edition)
stage: constitution
date: 2026-02-08
surface: agent
model: gemini-1.5-pro
feature: none
branch: main
user: sufi
command: /sp.constitution update my constitution in this form: # Phase III Constitution — AI-Powered Todo Chatbot (Gemini Edition)...
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

/sp.constitution update my constitution in this form: # Phase III Constitution — AI-Powered Todo Chatbot (Gemini Edition)

## 1. Purpose
Phase III introduces an AI-powered conversational interface for managing todos via natural language. This phase adds **intelligence only**. No frontend or backend business logic is to be rebuilt or modified beyond what is strictly required to support chat-based interaction.

This constitution governs what is allowed, what is forbidden, and how decisions are made during Phase III.

## 2. Phase II Boundary (Non-Negotiable)
The following components were fully implemented and validated in Phase II and **MUST NOT be reworked**:

*   **Preserved:** Frontend UI (Task lists, forms) and task management flows.
*   **Preserved:** Backend CRUD APIs (FastAPI endpoints) for tasks.
*   **Preserved:** Database schema (SQLModel/PostgreSQL) for tasks.
*   **Preserved:** Authentication and authorization logic (Better Auth/JWT).

### Allowed Changes
*   Adding a new chat API endpoint (`POST /api/chat`).
*   Adding database tables specifically for `conversations` and `messages`.
*   Implementing the AI Agent orchestration layer.
*   Wrapping existing Phase II internal logic into **MCP-Compatible Tools**.

### Forbidden Changes
*   Rewriting core task CRUD logic inside the API.
*   Creating alternate storage paths (e.g., writing directly to DB instead of using service functions).
*   Bypassing existing authorization checks (Agent must act *as* the user).
*   Introducing new business rules for tasks (e.g., changing validation logic).

**Rule:** Phase III must wrap intelligence *around* Phase II, not replace it.

## 3. Core Architectural Principles

### 3.1 Intelligence via Tools (The "Agent Skills" Model)
The AI agent is prohibited from accessing the database or internal code directly.
*   **Tools as Skills:** The agent interacts with the system *exclusively* through defined Tools.
*   **Encapsulation:** Tools must wrap the existing Phase II service logic.
*   **Mapping:** 1 Tool = 1 Discrete Capability (Skill).

### 3.2 Stateless Server Guarantee
The chat server must hold **no in-memory conversational state**.
*   Every request is self-contained.
*   Conversation history is fetched from the database at the start of a request.
*   Conversation history is updated in the database at the end of a request.
*   Server restarts must not affect the continuity of any user conversation.

### 3.3 Single Conversational Entry Point
Exactly one chat endpoint is allowed:
*   `POST /api/{user_id}/chat`
*   All natural language interaction flows through this endpoint.

## 4. AI Framework & Execution Model

### 4.1 Gemini-Based Runtime
Phase III utilizes **Google Gemini** (via Gemini API/CLI) as the reasoning engine, replacing the standard OpenAI Agents SDK requirement.
*   **Model:** Gemini 1.5 Flash or Pro (recommended for latency/reasoning balance).
*   **Interface:** Native Function Calling (Tools) API.

### 4.2 Agent Design Philosophy (The Reasoning Loop)
The agent is implemented as a strict **ReAct (Reason + Act)** loop:
1.  **Load:** Fetch conversation history from DB.
2.  **Reason:** Send history + new message to Gemini.
3.  **Decide:** Gemini identifies intent and selects a Tool (Function Call).
4.  **Act:** System executes the Tool (invoking Phase II logic).
5.  **Synthesize:** System feeds Tool Output back to Gemini; Gemini generates natural language response.
6.  **Persist:** Save new turns to DB.

### 4.3 Definition of "Agent Skills"
To satisfy the "Agent Skills" requirement using Gemini, the following definition applies:
*   An **Agent Skill** is defined as a **Gemini Function Declaration** combined with the **Backend Tool Logic**.
*   The Agent "acquires" a skill when the tool definition is injected into its context window.
*   **Required Skills:** `TaskManagement`, `TaskQuerying`.

## 5. MCP-Compatible Tooling Model

### 5.1 Tool Requirements
Each tool MUST:
*   Be **Stateless**: Rely only on arguments passed to it.
*   Be **Explicit**: Accept fully typed parameters (JSON Schema).
*   Be **Structured**: Return structured JSON only (no plain text).
*   **Persist State**: Commit changes to the DB immediately via Phase II logic.

### 5.2 Required Tool Inventory
The agent must possess the following tools (mapped to Phase II capabilities):

1.  `add_task(title: str, description: str | null)`
2.  `list_tasks(status: "all" | "pending" | "completed", limit: int)`
3.  `update_task(task_id: int, title: str | null, description: str | null)`
4.  `complete_task(task_id: int)`
5.  `delete_task(task_id: int)`

## 6. Conversation & Persistence Rules

### 6.1 Conversation Schema
*   **Conversation:** A container for a chat session.
*   **Message:** Immutable record with `role` ("user", "model", "tool") and `content`.
*   **Context Window:** The system must construct the context window from the persisted messages for every API call.

### 6.2 The Stateless Request Cycle
For every chat request:
1.  **Hydrate:** `DB -> History List`
2.  **Append:** `User Message -> History List`
3.  **Infer:** `Gemini.generate_content(history, tools=ALL_TOOLS)`
4.  **Loop:**
    *   If `function_call`: Execute Tool -> Append `function_response` -> Recurse.
    *   If `text_content`: Break loop.
5.  **Persist:** `New Messages -> DB`
6.  **Respond:** Return text to client.

## 7. Agent Behavior Rules

### 7.1 Intent-to-Tool Mapping
*   "I need to buy milk" → `add_task`
*   "What do I have to do?" → `list_tasks`
*   "Change the milk task to milk and eggs" → `update_task`
*   "I bought the eggs" → `complete_task`
*   "Remove the gym task" → `delete_task`

### 7.2 Ambiguity & Safety
*   **Clarification:** If the user request is vague ("Delete the task"), the agent MUST ask for clarification (e.g., "Which task?").
*   **Confirmation:** Destructive actions (Deletion) should be implicitly confirmed by repeating the action in the final response ("I have deleted the task: 'Buy Milk'").

## 8. Success Criteria
Phase III is complete when:
1.  Users can manage their Phase II todos entirely via natural language.
2.  The `Gemini` agent effectively uses the defined tools (Skills).
3.  Conversations persist across server restarts (Statelessness verified).
4.  Phase II functionality (Web UI) remains functional and synchronized with Chat actions.

## 9. Enforcement
If an implementation:
*   Modifies the `tasks` table schema.
*   Hard-codes responses instead of using the LLM.
*   Stores conversation state in a Python/Node variable (Global state).
*   Allows the agent to write SQL directly.
...it is considered out of scope and a violation of the Spec-Driven Development protocol.

## Response snapshot

The project constitution has been updated with the provided content to `.specify/memory/constitution.md`. A Prompt History Record (ID 0002) was manually created due to an issue with the PHR script.

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
