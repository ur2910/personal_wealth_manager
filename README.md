# 🏦 Personal Wealth Management AI Assistant

A personal AI agent that combines a client financial profile with live market research to deliver personalized, actionable wealth management guidance.

Built as a portfolio project to demonstrate AI product management skills — specifically agent architecture, RAG design, multi-tool orchestration, and product thinking applied to a real fintech use case.

---

## 🎯 What It Does

Ask any wealth management question and the agent:

1. **Retrieves your financial profile** from a structured knowledge base (Google Sheets)
2. **Runs live market research** via Tavily web search — current sector trends, ETF performance, market conditions
3. **Synthesizes both** using Claude to deliver advice personalized to your specific goals, risk tolerance, timeline, and allocation
4. **Proactively flags misalignments** — e.g. bond-heavy allocation for someone with a 19-year runway
5. **Appends a compliance disclaimer** on every response

### Example Output

> *"Given Jane's 19-year retirement runway and moderate risk tolerance, her current 40% bond allocation is more conservative than optimal. Healthcare and Utilities sectors align well with her passive income goal — XLU and CIBR are worth evaluating within her equity sleeve. For her 9-year-old's 529, a gradual shift toward conservative allocations should begin now..."*

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Frontend (HTML)                    │
│         Navy/gold financial UI · Chat interface      │
└──────────────────────┬──────────────────────────────┘
                       │ POST /webhook
                       ▼
┌─────────────────────────────────────────────────────┐
│                  n8n Workflow                        │
│                                                     │
│  [Webhook] → [Google Sheets] → [AI Agent]           │
│                                    │                │
│                          ┌─────────┴──────────┐     │
│                          ▼                    ▼     │
│                   [Anthropic Claude]    [Tavily]    │
│                   reasoning engine   live search    │
│                          │                         │
│                          ▼                         │
│                 [Respond to Webhook]               │
└─────────────────────────────────────────────────────┘
                       │
                       ▼
            Personalized response
        displayed in frontend UI
```

### Key Architectural Patterns

- **RAG (Retrieval Augmented Generation)** — client profile stored in Google Sheets is fetched at runtime and injected into every Claude prompt as grounding context
- **Multi-tool agent** — Claude autonomously decides when to call Tavily (live search) vs reason from profile data alone
- **Webhook-triggered orchestration** — frontend communicates with n8n via REST webhook, enabling a clean separation between UI and agent logic
- **Stateless per-request design** — each request fetches fresh profile data, ensuring recommendations always reflect the latest client information

---

## 🛠️ Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Agent Orchestration | n8n (self-hosted) | Workflow logic, node connections, execution |
| AI Reasoning | Anthropic Claude API | Personalized synthesis and recommendations |
| Live Market Research | Tavily Search API | Real-time sector trends, ETF data, market news |
| Knowledge Base | Google Sheets | Client financial profile storage |
| Frontend | Vanilla HTML/CSS/JS | Chat interface and profile display |
| Infrastructure | Docker (local) | n8n self-hosting |

---

## 💡 Product Decisions & Rationale

### Why n8n over Dify or LangChain?
n8n provides visual workflow orchestration that is both technically credible and immediately legible to non-technical stakeholders. Dify was evaluated but found too abstracted for demonstrating genuine agent architecture. LangChain/LangGraph requires Python proficiency that would have slowed iteration given time constraints.

### Why Google Sheets over a database?
For a personal portfolio project, Google Sheets provides the right balance of structure and simplicity. It's instantly editable, requires no database setup, and the n8n Google Sheets node handles the integration natively. The data model is simple enough that a spreadsheet is the right tool.

### Why Tavily over Perplexity API?
Tavily is purpose-built for LLM integration — results come back in agent-ready format without additional parsing. It also offers a generous free tier (1,000 searches/month) suitable for portfolio-scale usage.

### Why a static HTML frontend over React?
A single HTML file is portable, requires no build step, and can be opened or shared instantly — ideal for interview demos. The goal was a polished, demonstrable UI, not a production-grade frontend architecture.

### Compliance Design Decisions
- Fictional client profile only — no live brokerage connections
- Disclaimer appended to every agent response via system prompt instruction
- Framed explicitly as a learning/portfolio project

---

## 📁 Repository Structure

```
fintech-wealth-assistant/
├── README.md                 # This file
├── fintech_assistant.html    # Frontend chat interface
└── workflow.json             # n8n agent workflow (importable)
```

---

## 🚀 How to Run This Locally

### Prerequisites
- Docker Desktop installed
- Anthropic API key (console.anthropic.com)
- Tavily API key (tavily.com)
- Google account with Sheets access

### Step 1: Start n8n
```bash
docker volume create n8n_data
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

### Step 2: Import the Workflow
- Open n8n at `http://localhost:5678`
- Go to **Workflows** → **Import**
- Upload `workflow.json`
- Add your API credentials (Anthropic, Tavily, Google Sheets)
- Publish the workflow

### Step 3: Run the Frontend
```bash
npx serve .
```
Open the URL shown, click `fintech_assistant.html`, paste your n8n webhook URL at the top.

---

## 🔮 Planned Enhancements

- [ ] Add Memory node for persistent conversation history across sessions
- [ ] Alpha Vantage integration for live stock/ETF price data
- [ ] Goal progress tracker — scores each recommendation against stated goals
- [ ] Multi-client support — switch between profiles in the UI
- [ ] Hosted deployment via Railway or Render

---

## ⚠️ Disclaimer

This project is for educational and portfolio demonstration purposes only. All client data is fictional. Nothing produced by this agent constitutes professional financial advice. Consult a licensed financial advisor before making investment decisions.

---

## 👩‍💻 About This Project

Built by a Director of Product Management with 18 years of fintech experience (Java engineering + product), currently transitioning into AI product management roles. This project demonstrates practical AI agent design applied to a real domain problem — not just theoretical familiarity with the technology.

**Tech depth:** n8n orchestration · Claude API · RAG pattern · Webhook integration · Multi-tool agent design  
**Domain depth:** Wealth management · Retirement planning · Portfolio allocation · College savings strategy

