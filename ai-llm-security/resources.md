# Resources

## Frameworks and references

- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
- [MITRE ATLAS](https://atlas.mitre.org)
- [MCP specification](https://modelcontextprotocol.io)
- [MCP security best practices](https://modelcontextprotocol.io/specification/2025-06-18/basic/security_best_practices) - confused deputy, token passthrough, SSRF, and local server compromise
- [Arcanum Prompt Injection Taxonomy](https://github.com/Arcanum-Sec/arc_pi_taxonomy) - a structured taxonomy of prompt injection attack intents, techniques, and evasions

## My projects in this space

- [ai-hunter](https://github.com/J0n-H4rr150n/ai-hunter) - autonomous web
  security testing agent on Vertex AI, with human-in-the-loop approvals, a
  planner/quota budget system, and a feedback-memory loop over Postgres +
  pgvector.
- [Security Agent Swarm](https://github.com/J0n-H4rr150n/security-ai-agent-swarm-demo) -
  multi-agent security assistant: a supervisor routes questions to red team, blue
  team, Snyk, and secure-coding specialists over a RAG knowledge base. LangGraph,
  FastAPI, Vertex AI.
