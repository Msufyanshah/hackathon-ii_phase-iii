# Research Findings: Phase III - AI-Powered Todo Chatbot

**Feature Branch**: `001-gemini-chatbot` | **Date**: 2026-02-08 | **Spec**: specs/001-gemini-chatbot/spec.md

## 1. AI Reasoning Engine Integration

**Decision**: Integrate the AI reasoning engine using the official `google-generativeai` SDK for Python. The orchestration logic will implement a ReAct-like loop, where the AI's function calling capabilities are directly mapped to the `MCP-Compatible Tools` that encapsulate existing backend services.

**Rationale**: Direct SDK integration ensures optimal performance, access to the latest features, and vendor-specific optimizations from Google Gemini. The ReAct (Reason + Act) pattern is a well-established and robust approach for building tool-using agents, providing a clear and traceable execution flow. This avoids the overhead and abstraction layers of higher-level frameworks like LangChain, which are not strictly necessary for this direct integration approach.

**Alternatives Considered**:
*   **LangChain/other LLM Orchestration Frameworks**: Rejected due to adding unnecessary overhead and abstraction for a direct SDK integration requirement. The core ReAct loop can be implemented manually and efficiently.
*   **OpenAI Assistants API**: Not applicable as the requirement explicitly specifies Google Gemini.

## 2. Database for Conversation History

**Decision**: Extend the existing PostgreSQL database to store the new `conversations` and `messages` entities. Appropriate indexing will be applied to `conversation_id` and `user_id` fields to optimize query performance for conversational history retrieval.

**Rationale**: Leveraging the existing PostgreSQL database simplifies infrastructure management, reduces operational overhead, and benefits from existing database management practices. PostgreSQL's robust relational model is well-suited for structured conversational data. The "low daily volume" assumption for conversational data (hundreds of messages/day) means the existing database can comfortably handle the new load initially.

**Alternatives Considered**:
*   **NoSQL Databases (e.g., MongoDB, DynamoDB)**: Rejected as they introduce additional infrastructure and data modeling complexity that is not warranted for the initial low data volume and structured nature of conversation history.
*   **In-memory Stores (e.g., Redis)**: Rejected as this would violate the "Stateless Server" constraint by failing to persist conversation history across server restarts or instance failures.

## 3. Tool Design and Implementation

**Decision**: `MCP-Compatible Tools` will be implemented as standard Python functions within the existing backend service layer. Each tool will have a clearly defined JSON schema for its parameters, enabling direct mapping to the AI reasoning engine's function calling interface. Crucially, the `user_id` will be transparently injected into the tool calls at the API gateway or service layer, ensuring the AI agent never directly handles or provides user authentication context.

**Rationale**: This approach maximizes code reuse by encapsulating existing, battle-tested Phase II business logic. Explicit JSON schemas ensure strict type checking and clear contracts for the AI. Injecting `user_id` at a trusted layer enforces robust security, preventing the AI from impersonating users or performing unauthorized actions.

**Alternatives Considered**:
*   **Direct Database Access from AI**: Explicitly forbidden by the constitution due to security and architectural constraints.
*   **Custom Domain-Specific Language (DSL) for Tools**: Rejected as it would introduce parsing complexity and an additional layer of abstraction without clear benefits over direct Python function calls with JSON schemas.

## 4. Scalability Strategy for Chat Service

**Decision**: The chat service will be designed for horizontal scaling. This involves containerizing the service (e.g., using Docker) and deploying multiple instances behind a load balancer (e.g., using orchestration platforms like Kubernetes or Docker Swarm).

**Rationale**: Horizontal scaling is a standard and effective strategy for increasing availability and handling fluctuating or increased user loads. Containerization simplifies deployment and ensures consistency across environments. Deploying behind a load balancer evenly distributes traffic, maximizing resource utilization and system resilience. This aligns with the "Stateless Server" principle and provides a clear architectural path for future growth, minimizing refactoring needs.

**Alternatives Considered**:
*   **Vertical Scaling (increase resources on single instance)**: Rejected as it provides limited scalability, creates a single point of failure, and is less cost-effective for dynamic workloads.
*   **Serverless Functions (e.g., AWS Lambda, Google Cloud Functions)**: While highly scalable, they introduce specific architectural patterns and potential complexities (e.g., cold starts, vendor lock-in) that are not necessary for the initial low throughput target of Phase III. This could be a consideration for a future phase with higher, more sporadic load requirements.
