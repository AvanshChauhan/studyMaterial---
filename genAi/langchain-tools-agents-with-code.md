# Tools and Agents in LangChain (With Code Examples)

## Introduction

When building AI applications with LangChain, two concepts appear everywhere:

1. **Tools** – Functions the AI can use.
2. **Agents** – Systems that decide when and how to use those tools.

A simple way to think about it:

```text
Tool  = Ability
Agent = Decision Maker
```

For example:

```text
Calculator Tool
      +
Weather Tool
      +
Search Tool
      +
Agent
      ↓
AI Assistant
```

The tools provide capabilities, while the agent decides which capability is needed.

---

# What is a Tool?

A Tool is a function that an LLM can call to perform a task.

Without tools:

```text
User
  ↓
LLM
  ↓
Response
```

The model can only generate text.

With tools:

```text
User
  ↓
LLM
  ↓
Tool
  ↓
Result
  ↓
Response
```

The model can interact with external systems.

---

# Creating a Simple Tool

Suppose we want a calculator tool.

## Code

```javascript
import { tool } from "@langchain/core/tools";

const calculator = tool(
  async ({ a, b }) => {
    return a + b;
  },
  {
    name: "calculator",
    description: "Adds two numbers"
  }
);
```

---

## Explanation

### Step 1

```javascript
import { tool } from "@langchain/core/tools";
```

Imports LangChain's tool helper.

---

### Step 2

```javascript
const calculator = tool(...)
```

Creates a tool named calculator.

---

### Step 3

```javascript
async ({ a, b }) => {
  return a + b;
}
```

This is the actual logic.

Input:

```javascript
{
  a: 5,
  b: 10
}
```

Output:

```javascript
15
```

---

### Step 4

```javascript
{
  name: "calculator",
  description: "Adds two numbers"
}
```

The description is extremely important.

Agents read this description to decide when to use the tool.

---

# Why Tool Descriptions Matter

Bad description:

```javascript
description: "Does stuff"
```

The agent has no idea what it does.

Good description:

```javascript
description: "Adds two numbers together"
```

Now the agent knows when to use it.

---

# Creating a Weather Tool

Example:

```javascript
import { tool } from "@langchain/core/tools";

const weatherTool = tool(
  async ({ city }) => {
    return `The temperature in ${city} is 35°C`;
  },
  {
    name: "weather",
    description: "Gets current weather for a city"
  }
);
```

---

# What is an Agent?

An Agent is an LLM that can:

1. Read the user request.
2. Analyze the request.
3. Choose a tool.
4. Use the tool.
5. Generate a response.

---

## Without Agent

```text
User
 ↓
Developer
 ↓
Weather Tool
 ↓
Response
```

The developer decides which tool to call.

---

## With Agent

```text
User
 ↓
Agent
 ↓
Chooses Tool
 ↓
Executes Tool
 ↓
Response
```

The AI decides automatically.

---

# Creating an Agent

## Step 1: Create Model

```javascript
import { ChatGoogleGenerativeAI }
from "@langchain/google-genai";

const model = new ChatGoogleGenerativeAI({
  model: "gemini-2.5-flash"
});
```

This creates the LLM.

---

## Step 2: Define Tools

```javascript
const tools = [
  calculator,
  weatherTool
];
```

Available tools:

* Calculator
* Weather

---

## Step 3: Create Agent

```javascript
import { createToolCallingAgent }
from "langchain/agents";

const agent = createToolCallingAgent({
  llm: model,
  tools
});
```

Now the model can use tools.

---

# How an Agent Thinks

Suppose the user asks:

```text
What is 500 + 200?
```

The agent's reasoning looks like:

```text
User wants a calculation.

Calculator tool can do calculations.

Use calculator tool.
```

Tool call:

```javascript
{
  a: 500,
  b: 200
}
```

Tool returns:

```javascript
700
```

Agent responds:

```text
The answer is 700.
```

---

# Complete Flow

```text
User
 │
 ▼
"What is 500 + 200?"
 │
 ▼
Agent
 │
 ▼
Reads Tool Descriptions
 │
 ▼
Chooses Calculator Tool
 │
 ▼
Runs Tool
 │
 ▼
Receives Result
 │
 ▼
Generates Final Answer
```

---

# Multiple Tools Example

Available tools:

```javascript
const tools = [
  weatherTool,
  calculator,
  searchTool
];
```

User asks:

```text
What is the temperature in Delhi in Fahrenheit?
```

---

## Agent Reasoning

```text
Need current temperature.

Use weather tool.
```

Weather tool:

```javascript
35°C
```

Agent:

```text
Need conversion.

Use calculator.
```

Conversion:

```javascript
95°F
```

Final response:

```text
The temperature is approximately 95°F.
```

Notice that multiple tools were used automatically.

---

# Tool Calling Example

Tool:

```javascript
const multiplyTool = tool(
  async ({ x, y }) => {
    return x * y;
  },
  {
    name: "multiply",
    description: "Multiply two numbers"
  }
);
```

User:

```text
What is 15 × 12?
```

Agent internally generates:

```javascript
{
  tool: "multiply",
  args: {
    x: 15,
    y: 12
  }
}
```

Tool returns:

```javascript
180
```

Agent returns:

```text
180
```

---

# Real Project Example

Fitness Assistant.

Available tools:

```javascript
const tools = [
  calorieTool,
  workoutTool,
  bmiTool
];
```

---

## User Question

```text
I weigh 80kg and my height is 174cm.
What is my BMI?
```

---

## Agent Reasoning

```text
Need BMI calculation.

Use bmiTool.
```

Tool:

```javascript
BMI = 26.4
```

Agent:

```text
Your BMI is approximately 26.4.
```

---

# Agent Executor

The Agent itself only decides.

The Agent Executor manages execution.

```javascript
import { AgentExecutor }
from "langchain/agents";

const executor = new AgentExecutor({
  agent,
  tools
});
```

---

## Running the Agent

```javascript
const result = await executor.invoke({
  input: "What is 20 + 30?"
});

console.log(result.output);
```

Output:

```text
50
```

---

# Why Agents Are Powerful

Traditional Application:

```text
if weather
  call weather api

if calculation
  call calculator

if search
  call search api
```

You manually write all logic.

---

Agent-Based Application:

```text
User
 ↓
Agent
 ↓
Chooses Tools Automatically
 ↓
Response
```

Much less manual orchestration.

---

# Common Tools Used in Real Projects

| Tool              | Purpose           |
| ----------------- | ----------------- |
| Search Tool       | Web Search        |
| Calculator Tool   | Math              |
| Database Tool     | User Data         |
| File Tool         | Read PDFs/CSV     |
| API Tool          | External Services |
| Email Tool        | Send Emails       |
| Vector Store Tool | RAG & Memory      |

---

# Agent + Tools Architecture

```text
                    User
                      │
                      ▼
                LangChain Agent
                      │
      ┌───────────────┼───────────────┐
      │               │               │
      ▼               ▼               ▼
 Calculator      Weather Tool     Search Tool
      │               │               │
      └───────────────┼───────────────┘
                      │
                      ▼
                Final Answer
```

---

# Key Takeaways

### Tool

A function that performs a specific task.

Examples:

* Search
* Calculator
* Database Query
* API Call

### Agent

An LLM-powered decision maker that:

* Understands user intent
* Chooses tools
* Executes tools
* Combines results
* Generates responses

### Simple Formula

```text
LLM + Tools = Tool Calling

LLM + Tools + Reasoning = Agent

Agent + Executor = AI Assistant
```

This combination is the foundation of most modern AI assistants, chatbots, customer-support systems, coding assistants, and RAG applications built with LangChain.
