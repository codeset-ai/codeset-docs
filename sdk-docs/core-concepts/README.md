# Core Concepts

This section explains the fundamental concepts of the Codeset platform. Understanding these concepts is essential for effectively using our tools and services.

## Samples

A **Sample** represents a specific problem or task that an AI agent can work on. It's a self-contained unit that includes:

*   **A codebase:** A snapshot of a git repository containing the code the agent will interact with.
*   **A task description:** A clear set of instructions for the agent, outlining the goal it needs to achieve (e.g., "fix the bug that causes the authentication to fail" or "implement a new feature to export user data").
*   **A verification script:** A set of tests or checks that can be run to automatically determine if the agent has successfully completed the task.

Each sample has a unique `sample_id` that you use to create a new session.

## Sessions

A **Session** is a live, interactive, and sandboxed environment created from a specific sample. When you create a session, we spin up a secure container with the sample's codebase and all required dependencies, ready for an agent to connect and start working.

Key characteristics of a session:

*   **Isolated:** Each session is completely isolated from others, ensuring that an agent's actions in one session do not affect any other.
*   **Stateful:** A session maintains the state of the filesystem. Any changes an agent makes—creating, modifying, or deleting files—are preserved for the duration of the session.
*   **Interactive:** Agents can execute shell commands within the session's environment, allowing them to explore the codebase, run tests, and apply changes.
*   **Executable:** A session contains all required dependencies, including third-party libraries, build tools, SDKs, and runnable scripts (e.g., for executing tests).

You interact with a session using its unique `session_id`.

## Verifications

A **Verification** is the process of running the sample's verification script to check if the agent has successfully completed the task. You can trigger a verification at any point during a session.

The verification process will return a result indicating success or failure. This provides a clear and objective measure of the agent's performance on the given task. This is the primary mechanism for evaluating an agent's capabilities.
