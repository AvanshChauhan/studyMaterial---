# LangChain Messages: SystemMessage, HumanMessage, and AIMessage

## Introduction

When working with LangChain chat models such as Gemini, OpenAI GPT, or Mistral, conversations are represented using message objects.

The three most common message types are:

* `SystemMessage`
* `HumanMessage`
* `AIMessage`

Together, they provide context that helps the model generate accurate and consistent responses.

---

# Message Hierarchy

The model generally follows this priority order:

```text
SystemMessage
    ↓
HumanMessage
    ↓
AIMessage
```

* **SystemMessage** has the highest priority.
* **HumanMessage** contains the user's request.
* **AIMessage** contains previous assistant responses.

---

# 1. SystemMessage

A `SystemMessage` defines the assistant's behavior, personality, rules, and overall instructions.

Think of it as the assistant's operating manual.

## Example

```js
import { SystemMessage } from "@langchain/core/messages";

const systemMessage = new SystemMessage(`
You are a senior MERN developer.
Explain concepts in simple language.
Keep responses concise.
`);
```

## Purpose

Use a SystemMessage when you want to:

* Define assistant personality
* Set response style
* Add custom rules
* Restrict behavior
* Give role-based instructions

---

## Example Conversation

### Messages

```js
[
  new SystemMessage("You are a MERN expert."),
  new HumanMessage("Explain JWT.")
]
```

### Result

```text
JWT (JSON Web Token) is a secure way to authenticate users...
```

The model answers as a MERN expert because of the SystemMessage.

---

# 2. HumanMessage

A `HumanMessage` represents a user's input.

Every prompt sent by a user is typically stored as a HumanMessage.

## Example

```js
import { HumanMessage } from "@langchain/core/messages";

const userMessage = new HumanMessage(
  "How does JWT authentication work?"
);
```

## Purpose

Use HumanMessage for:

* User questions
* User instructions
* User commands
* User prompts

---

## Example

```js
[
  new SystemMessage("You are a MERN expert."),
  new HumanMessage("Explain JWT authentication.")
]
```

The model understands:

```text
Role: MERN Expert
Task: Explain JWT authentication
```

---

# 3. AIMessage

An `AIMessage` represents a previous assistant response.

It allows the model to remember the conversation context.

## Example

```js
import { AIMessage } from "@langchain/core/messages";

const assistantMessage = new AIMessage(
  "JWT stands for JSON Web Token."
);
```

---

## Why Is It Useful?

Without AIMessage:

```text
User: What is JWT?
User: How does it work?
```

The model may not know what "it" refers to.

With AIMessage:

```text
User: What is JWT?
AI: JWT stands for JSON Web Token.
User: How does it work?
```

The model understands that "it" means JWT.

---

# Example Chat History

```js
[
  new HumanMessage("What is JWT?"),
  new AIMessage("JWT stands for JSON Web Token."),
  new HumanMessage("How does it work?")
]
```

The model receives the entire conversation and can generate a relevant answer.

---

# Real-World Example

```js
import {
  SystemMessage,
  HumanMessage,
  AIMessage
} from "@langchain/core/messages";

const messages = [
  new SystemMessage(`
You are a helpful MERN mentor.
Explain concepts simply.
`),

  new HumanMessage("What is JWT?"),

  new AIMessage(`
JWT stands for JSON Web Token.
`),

  new HumanMessage("How does it work?")
];
```

---

# Using model.invoke()

```js
const response = await model.invoke([
  new SystemMessage(`
You are a helpful assistant.
`),

  new HumanMessage("Explain JWT authentication.")
]);
```

The model receives all messages together and generates the next response.

---

# Building a Chat Application

A common pattern is:

```js
const messages = [
  new SystemMessage(`
You are a helpful AI assistant.
Keep responses concise.
`),

  ...chatHistory,

  new HumanMessage(currentMessage)
];

const response = await model.invoke(messages);
```

Where:

* `SystemMessage` → Rules and personality
* `chatHistory` → Previous HumanMessage and AIMessage objects
* `currentMessage` → Latest user prompt

---

# Visual Representation

```text
┌─────────────────────┐
│   SystemMessage     │
│  Rules & Behavior   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│    HumanMessage     │
│   User Question     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     AIMessage       │
│ Previous Response   │
└──────────┬──────────┘
           │
           ▼
      Model Output
```

---

# Key Takeaways

## SystemMessage

* Highest priority
* Defines behavior
* Controls personality
* Sets rules and restrictions

## HumanMessage

* Represents user input
* Contains prompts and questions
* Drives the conversation

## AIMessage

* Represents previous assistant responses
* Maintains context
* Helps the model understand follow-up questions

---

# Summary

LangChain chat models use message objects to structure conversations.

```text
SystemMessage → Assistant Rules
HumanMessage  → User Input
AIMessage     → Previous Assistant Response
```

By combining these messages, the model can maintain context, follow instructions, and generate more accurate responses.
