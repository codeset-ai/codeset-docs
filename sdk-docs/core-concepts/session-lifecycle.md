# Session Lifecycle

A Session provides a complete, isolated environment for an AI agent to work on a task. Understanding its lifecycle is key to managing agent interactions effectively.

### 1. Creation

A session is initiated by making a request to the Codeset API with a specific `sample_id`. This tells the platform which codebase and task to load.

```python
from codeset import Codeset
import os

client = Codeset(api_key=os.getenv("CODESET_API_KEY"))

# This sends a request to the Codeset API to create a new session
session = client.sessions.create(
    dataset="gitbug-java",
    sample_id="assertj-assertj-vavr-f4d7f276e87c"
)

print(f"Session created with ID: {session.session_id}")
```

When the session is created, Codeset spins up a secure, sandboxed container and loads the sample's repository into it. The agent can now connect to this environment.

### 2. Interaction

Once the session is active, the agent can interact with the environment by executing commands. This is the primary way an agent explores the codebase, debugs issues, and applies changes.

Common interactions include:

*   Listing files (`ls -l`)
*   Reading file contents (`cat path/to/file.py`)
*   Writing changes to a file (`echo "new content" > path/to/file.py`)
*   Running build or test scripts

```python
# Execute a command to list files in the root directory
response = client.sessions.execute_command(
    session_id=session.session_id,
    command="ls -l"
)

print(response.stdout)
```

### 3. Verification

At any point, you can trigger a verification to check if the agent has successfully completed the task. The Codeset platform runs the sample's predefined verification script (e.g., running a test suite) to determine the outcome.

```python
# Start the verification process
verification_job = client.sessions.verify.start(session_id=session.session_id)

# Poll for the result
while True:
    status = client.sessions.verify.status(
        job_id=verification_job.job_id,
        session_id=session.session_id
    )
    if status.status in ["completed", "error", "cancelled"]:
        break
    time.sleep(1)

if status.status == "completed" and status.result:
    print(f"Verification successful: {status.result.is_success}")
```

### 4. Closing

When the agent has completed its work, or when you are finished with the session, it's important to close it. Closing a session terminates the container and releases all associated resources.

```python
client.sessions.close(session_id=session.session_id)
```

This ensures efficient resource management and concludes the lifecycle of the session.
