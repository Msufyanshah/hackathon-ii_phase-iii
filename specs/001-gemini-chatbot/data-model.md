# Data Model: Phase III - AI-Powered Todo Chatbot

**Feature Branch**: `001-gemini-chatbot` | **Date**: 2026-02-08 | **Spec**: specs/001-gemini-chatbot/spec.md
**Input**: Feature specification from `/specs/001-gemini-chatbot/spec.md`

## 1. Existing Entities (Read-Only)

### 1.1 User

**Description**: Manages user authentication and authorization within the existing Phase II system.
**Attributes**:
*   `id`: Unique identifier (Type not specified in spec, but typically UUID or integer).

### 1.2 Task

**Description**: Represents a single task managed by the existing Phase II system.
**Attributes**:
*   `id`: Unique identifier (integer, Primary Key).
*   `title`: Textual description of the task (string).
*   `description`: Additional details for the task (string, nullable).
*   `is_completed`: Status of the task (boolean).
*   `user_id`: Foreign Key linking to the User entity (Type to match User.id).
**Relationships**:
*   One-to-many with User (a User can have many Tasks).

## 2. New Entities (To Be Created)

### 2.1 Conversation

**Description**: A container representing a chat session between a user and the AI.
**Attributes**:
*   `id`: Unique identifier (UUID or BigInt, Primary Key).
*   `user_id`: Foreign Key linking to the User entity (String, Indexed).
*   `title`: Auto-generated summary of the chat (String, nullable).
*   `created_at`: Timestamp of conversation creation (DateTime, Default: Now).
*   `updated_at`: Timestamp of last conversation update (DateTime, Default: Now).
**Relationships**:
*   One-to-many with User (a User can have many Conversations).
*   One-to-many with Message (a Conversation can have many Messages).

### 2.2 Message

**Description**: An immutable record representing a single message within a conversation.
**Attributes**:
*   `id`: Unique identifier (UUID or BigInt, Primary Key).
*   `conversation_id`: Foreign Key linking to the Conversation entity (UUID/BigInt, Indexed).
*   `role`: Role of the participant who sent the message (Enum/String: 'user', 'model', 'tool').
*   `content`: The message content (Text/JSON). Stores prompt, response, or tool output.
*   `tool_call_id`: Links tool results to tool calls (String, nullable).
*   `created_at`: Timestamp of message creation (DateTime, Default: Now).
**Relationships**:
*   Many-to-one with Conversation (a Message belongs to one Conversation).

## 3. Relationships

*   **User to Task**: One User can have many Tasks.
*   **User to Conversation**: One User can have many Conversations.
*   **Conversation to Message**: One Conversation can have many Messages.
