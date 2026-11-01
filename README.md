## Login-free local testing

1. Copy `.env.example` to `.env`. Set `NICO_DEMO_MODE=true`.
2. Add your OpenAI API key in `.env` for AI chat and document embeddings.
3. Install dependencies: `pip install -r requirements.txt`.
4. Start **only on your own computer**: `uvicorn app:app --host 127.0.0.1 --port 8000`.
5. Open http://127.0.0.1:8000 — Nico opens without login as a local demo admin.

**SECURITY WARNING:** Demo mode bypasses authentication for every API endpoint, including admin actions. Never enable it on a public cloud deployment or shared network. Before deploying, set `NICO_DEMO_MODE=false`, configure a strong `JWT_SECRET`, set `ADMIN_EMAIL` and a new strong `ADMIN_PASSWORD`, and restore authenticated login. This demo is not a production deployment.

---

# Company AI Agent — Web Starter

A runnable, ChatGPT-inspired internal employee web app based on the eight-layer architecture in the supplied PDF. **This is an MVP, not a production-ready enterprise system.** It is not affiliated with ChatGPT and uses the OpenAI API (which is billed separately from a ChatGPT subscription).

## Quick start

1. Install Python 3.11+.
2. In this folder, run `python -m venv .venv`, activate it, then `pip install -r requirements.txt`.
3. Copy `.env.example` to `.env`. Set `OPENAI_API_KEY`, a random `JWT_SECRET` of at least 32 characters, `ADMIN_EMAIL`, and a strong `ADMIN_PASSWORD`.
4. Run `uvicorn app:app --host 127.0.0.1 --port 8000`.
5. Open `http://127.0.0.1:8000`, sign in with the admin credentials, create employees, and upload company documents.

**Never paste your API key into the browser or commit `.env` or `company_ai.db` to git.** Keep the API key server-side.

## PDF architecture mapping

1. **API:** OpenAI Chat Completions + embeddings through server-side SDK.
2. **System prompt:** Fixed internal-assistant persona and company-fact grounding; should be versioned and evaluated before production.
3. **RAG:** PDF/TXT/MD chunking, OpenAI embeddings, cosine similarity over SQLite; source-title citations. For scale, migrate to a vector database and add re-ranking, lifecycle management and source-level citations.
4. **MCP/tools:** Demo IT ticket action with explicit confirmation; **no MCP server/client integration yet**. Connect HR/Jira/Slack only with authorized delegated credentials.
5. **RBAC/OAuth:** Roles (employee, manager, hr, admin), server-side checks on document retrieval and chat ownership. **Local JWT auth, not OAuth/SSO**. Production needs SSO/OIDC, MFA, revocation, secure cookie sessions, and tool-level delegated OAuth.
6. **Guardrails:** Document text is labeled untrusted; permission checks and scoped retrieval. **No dedicated prompt-injection classifier, output PII scanner, or DLP yet.** Add before sensitive data rollout.
7. **Cost:** Daily request limit, token count, small default model, context limit. **No semantic cache or model router yet.** Usage limit is request count, not hard spend ceiling.
8. **Observability/evals:** Basic audit events, usage counts and admin stats. **No full traces, eval suite or release gate yet.**

## Security and production checklist

- Run behind HTTPS with reverse proxy; enable security headers, CSRF protections if using cookies, strict CORS and rate limiting at gateway.
- Replace local auth with corporate SSO/OIDC; use secure, short-lived sessions and audited user lifecycle.
- Move SQLite to managed PostgreSQL and a permission-filtered vector store; add backup and encryption at rest.
- Add document deletion, ingestion re-indexing, malware scanning, content-type verification, and quotas.
- Add dedicated injection detection, PII redaction/DLP, prompt versioning, evaluations, traces and alerting.
- Add tool-level user authorization and approval workflows for writes; do not connect tools with broad admin tokens.
- Add per-user spend controls, semantic caching partitioned by permissions, model routing and cost dashboards.
- Apply your company data retention, privacy and legal requirements before employee rollout.

## Notes

The uploaded PDF describes an architectural guide, not actual company policies or company-specific operating instructions. Upload real company policies separately as approved knowledge documents. This application can answer general questions via the model; it will not invent company-specific policy details without sources.

## Nico admin account

The default admin email in `.env.example` is `aryak@nicosur.com`. Before first launch, set `ADMIN_PASSWORD` to a **new, strong, unique password** in your private `.env` file. Do not use the password previously shared in chat. The application hashes the password when the account is first created; changing `ADMIN_PASSWORD` later will not reset an existing account password. Never commit `.env` to source control.
