# AI Product Ops Research & Verification System

An automated, grounded research & verification system designed to evaluate 100 enterprise applications for AI Agent buildability, API availability, authentication models, access nuances, and Model Context Protocol (MCP) ecosystem compatibility.

## 📁 Repository Structure

```
composio-product-ops-agent/
├── apps.csv                           # Source dataset of 100 enterprise applications
├── .agents/
│   └── skills/
│       └── app-research/
│           └── SKILL.md               # 17-field Research & Verification Schema definition
├── data/                              # Execution output datasets
│   ├── research_results.json          # Raw outputs from research agent
│   ├── research_log.json              # Detailed execution logs from research agent
│   ├── verified_results.json          # Final verified 100-app dataset with full telemetry
│   └── verification_log.json          # Verification telemetry and audit log
├── scripts/
│   ├── research_agent.py              # CLI tool for browser-first app documentation research
│   ├── verify_results.py              # Independent verification agent with ground truth audit
│   └── analyze_results.py             # Dataset statistical aggregator and JSON exporter
├── analysis/
│   └── summary.json                   # Exported dataset metrics for dashboard integration
├── index.html                         # Interactive Executive Case Study & Dataset Explorer
├── requirements.txt                   # Dependency manifest (composio, beautifulsoup4, requests)
└── README.md                          # Comprehensive project documentation
```

## 📊 17-Field Data Schema Definition

The schema defines **17 total fields** per application record, consisting of **12 core evaluable fields** checked for accuracy and **5 metadata/supporting fields**:

### Core Evaluable Fields (12 Fields Checked for Accuracy)
1. `category`: Functional operational category matching `apps.csv`.
2. `description`: Core platform functional summary string.
3. `auth_methods`: List of authentication options (`API Key`, `OAuth 2.0`, `Personal Access Token`, `Bot Token`, `None`).
4. `access_model`: Primary credential barrier (`self-serve`, `trial`, `paid`, `admin approval`, `partner/contact-sales`, `enterprise-only`, `unavailable`).
5. `access_notes`: Explanatory nuance capturing developer sandbox vs trial, admin consent gates, or tier restrictions.
6. `api_available`: Boolean flag (`true` / `false`).
7. `api_type`: Protocol list (`REST`, `GraphQL`, `SOAP`, `CLI`, `none`).
8. `api_breadth`: Capability scope (`broad`, `narrow`, `none`).
9. `mcp_official`: Vendor-maintained official MCP server availability (`true` / `false`).
10. `mcp_third_party`: Third-party / Composio / community toolkit availability (`true` / `false`).
11. `agent_buildability`: Feasibility assessment (`READY`, `CONDITIONAL`, `BLOCKED`).
12. `main_blocker`: Clear description of primary operational blocker (`"None"` if `READY`).

### Metadata & Supporting Fields (5 Fields)
13. `id`: Metadata integer identifier.
14. `app`: Application name string identifier.
15. `evidence_urls`: List of direct official documentation source links.
16. `evidence_notes`: Free-form research notes and summary text.
17. `confidence`: Rating on research certainty (`high`, `medium`, `low`).

---

## 📈 Summary Metrics & Verification Telemetry

| Metric | Result | Detail |
|---|---|---|
| **Total Apps** | **`100`** | Complete dataset processed |
| **Total Fields Evaluated** | **`1,200`** | 12 core schema fields × 100 apps |
| **First-Pass Accuracy** | **`85.42%`** | `(1,025 / 1,200) * 100` |
| **First-Pass Correct Fields** | **`1,025`** | Researched fields verified correct against ground truth |
| **Corrected Fields** | **`7`** | Fields updated post-verification audit |
| **Unverified Fields** | **`168`** | Fields tied to 14 apps with failed evidence URLs |
| **Apps with Evidence Failures** | **`14`** | Documentation URLs returning 400, 403, 404, or SSL/timeouts |
| **Verified Dataset Completeness** | **`86.00%`** | `((1,025 + 7) / 1,200) * 100` |

---

## 🚀 Execution & Regeneration Commands

### 1. Run Statistical Analysis Engine
```bash
python scripts/analyze_results.py
```
This updates `analysis/summary.json` with up-to-date metrics from `data/verified_results.json`.

### 2. View Case Study Dashboard Locally
Open `index.html` in any standard web browser or run a lightweight web server:
```bash
python -m http.server 8000
```
Then navigate to `http://localhost:8000`.

---

## 🛠️ Architecture & Dual-Agent Verification Engine

```
apps.csv
  │
  ▼
[Research Agent] ──(Browser Inspection + Composio SDK)──► data/research_results.json
  │
  ▼
[Verification Agent] ──(Independent Ground Truth + Live URL Audit)──► data/verified_results.json
  │
  ▼
[Analysis Engine] ──(Programmatic Aggregator)──► analysis/summary.json ──► index.html
```

### Key Technical Principles & Research Methodology:
- **HTTP Document Inspection**: Web evidence is retrieved using direct HTTP GET requests with custom User-Agent headers and BeautifulSoup DOM parsing (`inspect_page_http_doc`).
- **Authenticated Composio Toolkit Verification**: Composio toolkit availability requires authenticated Python SDK access (`COMPOSIO_API_KEY`). Without valid SDK credentials or when unauthenticated, Composio availability is set to `False` and reported as `UNAUTHENTICATED` / `API_KEY_MISSING` rather than assumed or hardcoded.
- **Evidence-Guided Rules & Explicit Unknowns**: The research agent uses grounded rules. When documentation fetches fail or evidence is insufficient, fields use explicit `unknown` defaults with lower confidence (`confidence="low"`), leaving verification to independent ground truth evaluation.
- **Grounded Verification**: No field is marked `VERIFIED` unless live documentation evidence supports the claim.
- **Strict MCP Classification**: `mcp_official` is strictly reserved for vendor-maintained servers (e.g. GitHub). `mcp_third_party` is set when Composio or community toolkits exist.
- **Separation of Access Model & Buildability**: An app with self-serve developer access is classified as `CONDITIONAL` if admin approval, OAuth app review, or scope approval is required.

