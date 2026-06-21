# 📉 Claude Token Optimizer

A lightweight skill for Claude AI that reduces token usage by enforcing **minimal context, structured reasoning, and compressed outputs**, helping developers build faster and more cost-efficient AI workflows.

---

## 🚀 Why This Exists

AI coding assistants often waste tokens by:

* Repeating user instructions
* Over-explaining solutions
* Loading unnecessary context
* Generating verbose responses
* Rewriting full files instead of diffs

**Claude Token Optimizer fixes this behavior.**

It makes Claude:

* More concise ⚡
* More focused 🧠
* More cost-efficient 💰
* More production-ready 🏗️

---

## 🎯 Core Goals

* 🔻 Reduce token consumption
* ⚡ Improve response speed
* 🧠 Enforce structured reasoning
* 🧩 Minimize unnecessary context usage
* 🧪 Improve production-grade output quality

---

## 🧠 How It Works

This skill modifies Claude’s behavior using strict execution rules:

### 1. Context Filtering

Only relevant information is used for the task.

### 2. Output Compression

Responses are:

* Short
* Structured
* Non-redundant

### 3. Task Segmentation

Large problems are split into steps instead of one long response.

### 4. Diff-First Thinking

Code changes are returned as patches instead of full rewrites.

### 5. Stop-Early Principle

Claude stops immediately after completing the task.

---

## ⚙️ Installation

Clone the repository:

```bash
git clone https://github.com/jmrashed/claude-token-optimizer.git
```

Navigate into the project:

```bash
cd claude-token-optimizer
```

Install into Claude skills directory:

```bash
mkdir -p ~/.claude/skills/token-optimizer
cp -r . ~/.claude/skills/token-optimizer
```

---

## 📁 Repository Structure

```text
claude-token-optimizer/
│
├── SKILL.md
├── README.md
├── examples/
│   ├── before.md
│   ├── after.md
│
├── rules/
│   ├── context-filtering.md
│   ├── output-compression.md
│
└── templates/
    └── optimized-response-template.md
```

---

## 📊 Example

### ❌ Before Optimization

* Long explanation
* Full file rewrite
* Repeated context
* Extra theory

---

### ✅ After Optimization

```diff
- old function logic
+ optimized function logic
```

* Minimal explanation
* Only changed parts
* No repetition
* Faster execution

---

## 🧪 Use Cases

Best for:

* Laravel / Node.js backend systems
* React / Next.js applications
* API development
* Debugging production issues
* SaaS / ERP / CRM systems
* AI-assisted engineering workflows

---

## ⚡ Benefits

* Up to **60% lower token usage**
* Faster Claude responses
* Cleaner outputs
* Reduced API cost
* More production-ready code

---

## 🧠 Philosophy

> “Less context, more precision.”

This skill enforces:

* Structured thinking
* Minimal output
* Production discipline
* Execution clarity

---

## 📌 Roadmap

* [ ] V1: Core token optimization rules
* [ ] V1.1: Diff-only response mode
* [ ] V2: Smart context trimming
* [ ] V2: Task segmentation engine
* [ ] V3: Multi-agent optimization support

---

## 🤝 Contributing

Contributions are welcome.

Focus areas:

* Better compression rules
* Smarter context filtering
* Improved diff formatting
* Performance optimization strategies

---

## 📜 License

MIT License © 2026

---

## ⭐ Support

If this project helps reduce your AI costs or improves productivity, consider giving it a star ⭐

GitHub: [https://github.com/jmrashed/claude-token-optimizer](https://github.com/jmrashed/claude-token-optimizer)
