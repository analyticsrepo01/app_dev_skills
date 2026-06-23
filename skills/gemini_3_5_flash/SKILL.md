---
name: gemini-3-5-flash
description: Mastering Google Gemini 3.5 Flash model features, including Stateful interactions, Thinking Level configuration, Media Resolution options, and Tool Use / Thought Signatures with the Google Gen AI SDK.
---

# ⚡ Gemini 3.5 Flash Developer & Agent Integration Guide

Use this skill to integrate, optimize, and debug workloads running on **Google Gemini 3.5 Flash** using the modern `google-genai` SDK.

---

## 🚀 1. Installation & Client Setup

Gemini 3.5 Flash features require the Google Gen AI SDK for Python version `2.4.0` or later.

### 📦 Installation
```bash
pip install --upgrade "google-genai[pyopenssl]>=2.4.0"
```

### 🔑 Enterprise Client Initialization
Always initialize the client with `enterprise=True`, utilizing the `global` region which is optimized for Gemini 3.5 Flash:

```python
import os
from google import genai

PROJECT_ID = os.getenv("GOOGLE_CLOUD_PROJECT", "your-project-id")
LOCATION = "global"

client = genai.Client(enterprise=True, project=PROJECT_ID, location=LOCATION)
MODEL_ID = "gemini-3.5-flash"
```

---

## 💬 2. Stateful Chats (Interactions API)

For multi-turn agent conversations, reasoning loops, and multi-step tool use, always use the stateful Chat API. This ensures "Thought Signatures" are seamlessly and automatically managed.

```python
# Create a stateful chat session
chat = client.chats.create(model=MODEL_ID)

# Send messages sequentially
response1 = chat.send_message("Let's analyze this dataset. What are the first steps?")
print("Gemini:", response1.text)

response2 = chat.send_message("Great, let's proceed with step 1.")
print("Gemini:", response2.text)
```

---

## 🧠 3. Flexible Thinking Control (`thinking_level`)

Gemini 3.5 Flash provides native control over the model's internal reasoning depth. Use **`thinking_level`** to optimize the balance between latency, cost, and reasoning power.

| Level | Latency & Cost | Recommended Use Cases |
| :--- | :--- | :--- |
| `MINIMAL` | Lowest | Simple classification, basic text processing, routine formatting. |
| `LOW` | Low | Simple instruction-following, basic chat responses. |
| `MEDIUM` | Moderate (Default) | General reasoning, standard coding, and multi-turn agent coordination. |
| `HIGH` | Higher | Complex mathematics, difficult logical puzzles, and advanced code generation. |

### 🛠️ Configuration Example:
```python
from google.genai import types

config = types.GenerateContentConfig(
    thinking_config=types.ThinkingConfig(
        thinking_level="HIGH"  # Options: MINIMAL, LOW, MEDIUM, HIGH
    )
)

response = client.models.generate_content(
    model=MODEL_ID,
    contents="Solve this advanced database query optimization problem: ...",
    config=config
)
print(response.text)
```

> [!WARNING]
> Do not supply both `thinking_level` and the legacy `thinking_budget` in the same request configuration as it will cause a validation error.

---

## 🖼️ 4. Granular Multimodal Processing (`media_resolution`)

Control token consumption and model precision on multimodal inputs (images, video, PDFs) with the **`media_resolution`** parameter.

### Available Values:
*   `low`
*   `medium`
*   `high`
*   `ultra_high` (Supported on a per-media-part level only)

### 🛠️ Usage Example:
```python
# Optimize image resolution to capture fine text or OCR detail
config = types.GenerateContentConfig(
    media_resolution_config=types.MediaResolutionConfig(
        media_resolution="high"
    )
)

response = client.models.generate_content(
    model=MODEL_ID,
    contents=[image_bytes, "Transcribe all printed text in this document scan."],
    config=config
)
```

---

## 🛠️ 5. Tool Use & Thought Signatures

When using Function Calling (Tool Use), Gemini 3.5 Flash uses encrypted **Thought Signatures** to preserve reasoning state across tool execution boundaries.

### 💡 Key Integration Rules:
1. **SDK Automatic Handling:** If you use the stateful `client.chats.create()`, the SDK manages thought signatures behind the scenes.
2. **Manual Management (REST or custom state):** If you are manually constructing the message/conversation history, you **MUST** extract the `thought_signature` from the model's response and send it back verbatim as a `Part` in the history along with the tool results. Failing to do so results in a `400 Bad Request` validation error.

```python
# Structure of returning tool results in manual chat history:
# [
#   UserMessage("Call tool x"),
#   ModelMessage([FunctionCallPart(name="x"), ThoughtSignaturePart(signature="...")]),
#   UserMessage([FunctionResponsePart(name="x", response=result), ThoughtSignaturePart(signature="...")])
# ]
```
