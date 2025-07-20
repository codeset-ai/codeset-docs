# Getting Started
This guide will walk you through the essential workflow of using our SDK to interact with the Codeset API. You will learn how to create a session, execute commands within it, verify the outcome, and properly close the session.

## Prerequisites

Before you begin, you will need to have the following installed:

* Python 3.7 or later
* The `codeset` library

## Step 1: Install the Codeset SDK

You can install the `codeset` library using pip (or other alternative):

```bash
pip install codeset
```

## Step 2: Create a Session

The first step is to create a session. A session represents a sandboxed environment where you can execute commands and run verifications. To create a session, you will need to provide a `sample_id`. A `sample_id` is a unique identifier for a specific task or problem.

```python
from codeset import Codeset
import os

client = Codeset(api_key=os.getenv("CODESET_API_KEY"))

session = client.sessions.create(
    dataset="gitbug-java",
    sample_id="traccar-traccar-95fdfd770130"
)
```

## Step 3: Interact with the Session

Once you have created a session, you can execute commands in the sandboxed environment. For example, you can use the `ls -l` command to list the files in the current directory:

```python
response = client.sessions.execute_command(
    session_id=session.session_id,
    command="ls -l"
)

print(response.stdout)
```

## Step 4: Verify the Outcome

After you have executed your commands, you can verify the results. A verification is a process that checks if the task has been completed successfully. To start a verification, you will need to provide the `session_id`.

```python
# Start verification
response = client.sessions.verify.start(session_id=session.session_id)

# Wait for verification to complete
while True:
    response = client.sessions.verify.status(
        job_id=response.job_id,
        session_id=session.session_id
    )
    if response.status in ["completed", "error", "cancelled"]:
        break
    time.sleep(1)

# Check if verification was successful
if response.status == "completed" and response.result:
    print(f"Verification successful: {response.result.is_success}")
```

## Step 5: Close the Session

Finally, you should close the session to release the resources.

```python
client.sessions.close(session_id=session.session_id)
```
