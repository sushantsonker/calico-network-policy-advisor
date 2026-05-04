# calico-network-policy-advisor
AI-Powered Network Policy Advisor using Amazon Bedrock

# 🚀 AI-Powered Network Policy Advisor (Amazon Bedrock)

## 1. Problem Statement

This system helps platform engineers understand why Kubernetes network traffic was denied and suggests safe policy changes using Amazon Bedrock, Knowledge Bases, vector search, and guardrails.

---

## 2. High-Level Architecture
User / Platform Engineer
|
v
Query Interface
|
v
Pre-Retrieval Logic
(Query classification + metadata extraction)
|
v
Amazon Bedrock Knowledge Base
|
v
Vector Store
(OpenSearch Serverless / Pinecone / Aurora PostgreSQL pgvector)
|
v
Retrieved Context
|
v
Bedrock Converse API
|
v
Guardrails + Automated Reasoning Checks
|
v
Final Answer


---

## 3. Main Components

| Component | Purpose |
|----------|--------|
| User Query Interface | Accepts questions from platform engineers |
| Pre-Retrieval Logic | Classifies query and extracts namespace, pod, policy, action |
| Knowledge Base | Manages document ingestion, chunking, embeddings, and retrieval |
| Vector Store | Stores embeddings for policies, logs, and runbooks |
| Converse API | Sends prompt, context, and user query to the foundation model |
| Guardrails | Blocks unsafe or overly broad recommendations |
| Automated Reasoning Checks | Validates whether generated policy suggestions violate safety rules |

---

## 4. Data Sources

| Data Source | Example |
|------------|--------|
| Calico Network Policies | YAML policies |
| Flow Logs | ALLOW / DENY traffic events |
| Platform Runbooks | Troubleshooting guides |
| Security Rules | Internal zero-trust standards |

---

## 5. Bedrock Components Used

| Bedrock Feature | How It Is Used |
|----------------|---------------|
| Amazon Bedrock Knowledge Bases | RAG over network policies and logs |
| Embedding Model | Converts policies/logs into vectors |
| Converse API | Generates explanation and recommendation |
| Guardrails | Prevents unsafe answers |
| Automated Reasoning Checks | Validates policy logic before final response |
| Fine-tuning / LoRA | Evaluated, but not required for this version |

---

## 6. Retrieval Flow
User asks:
"Why was traffic from pod-a to pod-b denied?"

Step 1: Classify intent as "explain denial"
Step 2: Extract metadata:

source pod
destination pod
namespace
action = DENY

Step 3: Apply metadata filters
Step 4: Retrieve matching policy/log chunks
Step 5: Send retrieved context to model
Step 6: Generate explanation
Step 7: Apply guardrails and reasoning checks
Step 8: Return final answer


---

## 7. Chunking Strategy Summary

| Content Type | Chunking Strategy | Reason |
|--------------|------------------|--------|
| Network Policy YAML | Per policy rule | Keeps rule context intact |
| Flow Logs | Per event or grouped by source/destination | Improves denial explanation |
| Runbooks | Section-based chunking | Preserves troubleshooting steps |
| Security Standards | Paragraph/section chunking | Keeps compliance rules readable |

---

## 8. Guardrail Examples

| Risk | Guardrail |
|------|----------|
| Model suggests allowing all traffic | Block `0.0.0.0/0` or unrestricted namespace access |
| Model gives high-confidence answer without evidence | Require retrieved source reference |
| Model modifies unrelated namespace | Restrict recommendation to detected namespace |
| Model suggests disabling policies | Block unsafe remediation advice |

---

## 9. Expected Output Format
Summary:
Traffic was denied because...

Evidence:

Flow log entry...
Matching policy rule...

Recommended Fix:
Add or update a specific network policy rule...

Safety Check:
This recommendation does not allow unrestricted traffic.


---

## 10. Architecture Decision Notes

| Decision | Choice | Reason |
|----------|--------|--------|
| RAG vs Fine-tuning | RAG | Policies and logs change frequently |
| Knowledge Base vs custom pipeline | Knowledge Base | Faster setup and managed retrieval |
| Vector store | OpenSearch Serverless | Native Bedrock integration |
| Chunking | Rule-based + metadata-aware | Improves precision |
| Guardrails | Required | Prevents unsafe policy recommendations |

---

## 🧠 Key Learnings (To Fill After Implementation)

- What chunking strategy worked best?
- Did metadata filtering improve retrieval accuracy?
- Were guardrails triggered effectively?
- Would fine-tuning improve results?

---

## 📌 Future Improvements

- Hybrid search (keyword + vector)
- Better chunking strategies (adaptive chunking)
- Real-time log ingestion
- Feedback loop for improving recommendations
