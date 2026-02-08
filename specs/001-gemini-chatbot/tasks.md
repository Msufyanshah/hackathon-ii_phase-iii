# Tasks for Phase III - AI-Powered Todo Chatbot

**Feature Branch**: `001-gemini-chatbot` | **Date**: 2026-02-08 | **Spec**: specs/001-gemini-chatbot/spec.md
**Plan**: specs/001-gemini-chatbot/plan.md

## Summary

This document outlines the tasks required to implement the AI-Powered Todo Chatbot feature. Tasks are organized into phases, prioritizing foundational work and then user stories by their criticality.

## Implementation Strategy

The implementation will follow an iterative approach, focusing on delivering a Minimum Viable Product (MVP) first, then incrementally building out the remaining user stories and cross-cutting concerns. Each user story is designed to be independently testable.

## Task Breakdown

### Phase 1: Setup

These tasks focus on preparing the development environment and ensuring all necessary dependencies are in place.

- [ ] T001 Ensure Python 3.11 development environment is set up.
- [ ] T002 Install FastAPI, `google-generativeai` SDK, and other necessary Python dependencies (e.g., database drivers, ORM) in `requirements.txt`.

### Phase 2: Foundational

These tasks are prerequisites for implementing any user stories and establish the core infrastructure for the chat feature.

- [ ] T003 Create new database models for `Conversation` and `Message` entities in `backend/src/models/chat_models.py`.
- [ ] T004 Apply database migrations to create `conversations` and `messages` tables.
- [ ] T005 Implement basic CRUD operations for `Conversation` and `Message` models in a new service file `backend/src/services/chat_db_service.py`.

### Phase 3: User Story 1 - Create a Task [US1] (Priority: P1)

**Goal**: Enable users to create new tasks via the conversational interface.
**Independent Test Criteria**: A user can send a natural language message to create a task, and the task appears in their todo list.

- [ ] T006 [US1] Define `add_task` tool function (with JSON schema) in `backend/src/utils/skills.py` based on `contracts/tools/add_task.json`.
- [ ] T007 [US1] Implement `add_task` tool logic, wrapping existing Phase II task creation service in `backend/src/services/task_tools.py`.
- [ ] T008 [P] [US1] Create an integration test for `add_task` tool functionality in `backend/tests/integration/test_chat_tools.py`.

### Phase 4: User Story 2 - List Pending Tasks [US2] (Priority: P1)

**Goal**: Enable users to list their pending tasks via the conversational interface.
**Independent Test Criteria**: A user can send a natural language message to list pending tasks, and the chatbot responds with their pending tasks.

- [ ] T009 [US2] Define `list_tasks` tool function (with JSON schema) in `backend/src/utils/skills.py` based on `contracts/tools/list_tasks.json`.
- [ ] T010 [US2] Implement `list_tasks` tool logic, wrapping existing Phase II task listing service in `backend/src/services/task_tools.py`.
- [ ] T011 [P] [US2] Create an integration test for `list_tasks` tool functionality in `backend/tests/integration/test_chat_tools.py`.

### Phase 5: User Story 5 - Delete with Confirmation [US5] (Priority: P1)

**Goal**: Enable users to delete tasks via the conversational interface with explicit confirmation.
**Independent Test Criteria**: A user can send a natural language message to delete a specific task, and the chatbot confirms the deletion.

- [ ] T012 [US5] Define `delete_task` tool function (with JSON schema) in `backend/src/utils/skills.py` based on `contracts/tools/delete_task.json`.
- [ ] T013 [US5] Implement `delete_task` tool logic, wrapping existing Phase II task deletion service in `backend/src/services/task_tools.py`.
- [ ] T014 [P] [US5] Create an integration test for `delete_task` tool functionality in `backend/tests/integration/test_chat_tools.py`.

### Phase 6: User Story 3 - Contextual Update [US3] (Priority: P2)

**Goal**: Enable users to update tasks contextually via multi-turn conversations.
**Independent Test Criteria**: A user can add a task, and in a subsequent message, modify it using contextual references.

- [ ] T015 [US3] Define `update_task` tool function (with JSON schema) in `backend/src/utils/skills.py` based on `contracts/tools/update_task.json`.
- [ ] T016 [US3] Implement `update_task` tool logic, wrapping existing Phase II task update service in `backend/src/services/task_tools.py`.
- [ ] T017 [P] [US3] Create an integration test for `update_task` tool functionality in `backend/tests/integration/test_chat_tools.py`.

### Phase 7: User Story 4 - Delete with Ambiguity [US4] (Priority: P2)

**Goal**: Ensure the chatbot handles ambiguous delete requests safely by asking for clarification.
**Independent Test Criteria**: When presented with an ambiguous delete request for multiple similar tasks, the chatbot requests clarification instead of performing an action.

- [ ] T018 [US4] Enhance chat orchestration logic (`backend/src/services/chat_orchestrator.py`) to detect ambiguous requests before invoking destructive tools.
- [ ] T019 [US4] Implement clarification prompt logic for ambiguous delete requests in `backend/src/services/chat_orchestrator.py`.
- [ ] T020 [P] [US4] Create an integration test for ambiguous delete request handling in `backend/tests/integration/test_chat_logic.py`.

### Phase 8: Polish & Cross-Cutting Concerns

These tasks focus on integrating the core components, addressing non-functional requirements, and preparing for deployment.

- [ ] T021 Implement the main chat API endpoint (`POST /api/v1/chats/{conversation_id}/messages`) in `backend/src/api/chat_api.py`.
- [ ] T022 Implement the core AI agent orchestration (ReAct-like loop) in `backend/src/services/chat_orchestrator.py`, integrating `google-generativeai` SDK and defined tools.
- [ ] T023 Integrate authentication and `user_id` injection into the chat API endpoint, ensuring transparent user context for tool execution in `backend/src/api/chat_api.py` and `backend/src/services/chat_orchestrator.py`.
- [ ] T024 Implement context-specific, user-friendly messages for errors (e.g., tool execution failures, invalid input) and helpful suggestions for empty states in `backend/src/services/chat_orchestrator.py`.
- [ ] T025 Define `get_current_time` tool function in `backend/src/utils/skills.py` and implement its logic in `backend/src/services/system_tools.py`.
- [ ] T026 Containerize the chat service (e.g., `backend/Dockerfile`).
- [ ] T027 Update deployment configuration to deploy chat service as multiple instances behind a load balancer (e.g., Kubernetes manifests, Docker Compose).

## Dependencies

-   **Foundational tasks (Phase 2)** must be completed before any User Story implementation (Phases 3-7).
-   **All User Stories (Phases 3-7)** must be largely complete before the final Polish & Cross-Cutting Concerns phase (Phase 8), especially `T022` which integrates all tools.

## Parallel Execution Opportunities

-   **User Story 1 (Phase 3)**: T006, T007, T008 can be developed with some parallelization, e.g., T008 can be started once the `add_task` tool function signature is defined and mocked.
-   **User Story 2 (Phase 4)**: T009, T010, T011 can be developed with some parallelization, e.g., T011 can be started once the `list_tasks` tool function signature is defined and mocked.
-   **User Story 5 (Phase 5)**: T012, T013, T014 can be developed with some parallelization, e.g., T014 can be started once the `delete_task` tool function signature is defined and mocked.
-   **User Story 3 (Phase 6)**: T015, T016, T017 can be developed with some parallelization, e.g., T017 can be started once the `update_task` tool function signature is defined and mocked.
-   **User Story 4 (Phase 7)**: T018, T019, T020 can be developed with some parallelization, e.g., T020 can be started once the orchestration logic is in place and can be mocked.

## Suggested MVP Scope

The Minimum Viable Product (MVP) scope includes **User Story 1 (Create a Task)** and **User Story 2 (List Pending Tasks)**. This provides core create and read functionality for tasks via the conversational interface. Completion of Foundational Tasks (Phase 2) is a prerequisite for the MVP.
