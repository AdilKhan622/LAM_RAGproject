# LAM RAG Project — Kick-off Meeting Minutes

**Organization:** Laboratoire d’Astrophysique de Marseille (LAM)  
**Meeting date:** 22 October 2026  
**Project:** Search Assistant for Space-Mission Knowledge  
**Project context:** CentraleDigitalLab industry project brief

---

## Clickable Index

1. [Project Overview](#1-project-overview)
2. [Requirements](#2-requirements)
3. [Sources and Links](#3-sources-and-links)
4. [Problems to Solve](#4-problems-to-solve)
5. [Receivables from LAM](#5-receivables-from-lam)
6. [Expected Team Deliverables](#6-expected-team-deliverables)
7. [Questions for the Next Meeting](#7-questions-for-the-next-meeting)
8. [Questions Specifically for Anne-Laure](#8-questions-specifically-for-anne-laure)
9. [Next Steps](#9-next-steps)
10. [Evaluation Targets](#10-evaluation-targets)
11. [Additional Decisions and Risks](#11-additional-decisions-and-risks)
12. [Immediate Action Summary](#12-immediate-action-summary)

---

## 1. Project Overview

The objective is to build a **Retrieval-Augmented Generation (RAG) assistant** that allows LAM researchers, engineers, and other staff to ask questions about internal project documentation and receive accurate, source-cited answers.

LAM's information is spread across multiple systems, including WordPress, Redmine, Atrium, and document directories. Finding the right information takes time, particularly for new employees and when technical specifications have changed across multiple document versions.

The system should help users:

- Find information across multiple internal sources from one interface.
- Ask technical questions in natural language and receive evidence-backed answers.
- Identify the original document, source, date, and version supporting an answer.
- Determine which specification is current and understand how values have changed over time.
- Onboard new employees by helping them discover relevant documentation and internal information systems.
- Respect access restrictions and avoid revealing information users are not authorized to see.

The project will initially focus on **one mission, primarily MMX, and two or three sources**, rather than indexing all of LAM's information systems immediately. The project brief recommends building and evaluating a complete end-to-end prototype before expanding.

## 2. Requirements

### 2.1 Functional Requirements

- **RAG pipeline:** Ingest information from selected sources, retrieve relevant passages, and generate answers grounded in those passages.
- **Source citations:** Display the source document, link, and relevant metadata for every answer.
- **Version and date tracking:** Preserve document versions, dates, and provenance to distinguish current information from historical specifications.
- **Access control:** Restrict retrieval and results according to the user's permissions. Document titles must not leak to unauthorized users.
- **Web UI:** Provide a simple, accessible interface for technical questions and onboarding.
- **Semantic search:** Understand the meaning of questions rather than relying only on exact keyword matches. Compare semantic search with keyword search and hybrid retrieval.
- **Multimodal document retrieval:** Investigate retrieving information from figures, graphs, tables, headers, and images, in addition to ordinary text.
- **Abstention:** Respond that the information was not found when the sources do not contain sufficient evidence.
- **Updates and deletions:** Incorporate new and modified information while handling the occasional deletion of content.
- **Extensibility:** Design the system so additional projects and sources can be integrated later.
- **Evaluation:** Test the system against expert-provided questions, including difficult questions and questions that have no answer in the indexed sources.

### 2.2 Proposed Technical Direction

- Use **Qdrant** as the vector database, as discussed by the team, rather than ChromaDB.
- Build a hybrid retrieval approach combining keyword search (BM25) and embeddings; evaluate whether reranking improves results.
- Investigate open-source models and local deployment to meet LAM's potential data-sovereignty requirements.
- Preserve document metadata and access-control information throughout ingestion, indexing, retrieval, and answer generation.

> **Status note:** Qdrant is the team's proposed choice, not yet a confirmed requirement from LAM. The deployment environment, model choice, and performance constraints still need to be agreed.

## 3. Sources and Links

The following is the initial source inventory based on the kickoff discussion, whiteboards, Discord discussion, and project brief.

| Source | Expected content and role | Status / next action |
|---|---|---|
| **Redmine** | Project issues, wikis, technical information, and project history. Access to multiple projects was mentioned. | A source contact has been identified. Explore the GLNemo project as a possible complete-project example. |
| **WordPress** | Internal webpages and embedded or linked PDFs; includes DIRCOM content. | A cleaned data dump is expected. |
| **Atrium** | Project documentation, reviews, and documents associated with standards and validation processes, including ISO, ISA, and NASA-related material. | Access may be provided. Confirm its structure, content, and access method. |
| **Document directories** | ZIP archives containing PDFs and technical documents covering specifications, validation, and other project stages. | Obtain the relevant MMX PDFs and sample archives. |
| **Baggera** | Initially listed in the project brief as a data server, but meeting notes indicate it has been replaced by Atrium in the current plan. Webpages and a database hosted in the cloud were mentioned in connection with the discussion. | Clarify the relationship between Baggera and Atrium and whether Baggera remains a separate source. |

### 3.1 Links and Access References

- **LAM main website:** <https://www.lam.fr/>
- **LAM Redmine:** <https://projets.lam.fr/>
- **WordPress:** Cleaned data dump to be provided.
- **Atrium:** Internal site; access and URL to be confirmed.
- **MMX documents:** PDF collection to be provided.
- **GLNemo:** Candidate Redmine project for exploring a complete project dataset.

### 3.2 Candidate Mission and Project Context

- **MMX (Martian Moons eXploration):** The primary mission proposed for the prototype.
- **GLNemo:** A candidate project on Redmine to explore as a potentially self-contained dataset.
- **Other projects mentioned:** Euclid, PRIMA, ELT, and the instruments MOSAIC and HARMONY.

LAM projects can span 10–15 years, with documents produced and validated at different stages, including preliminary design, final design, and realisation. The initial prototype should remain focused on MMX unless the next meeting changes the scope.

## 4. Problems to Solve

### 4.1 Information Fragmented Across Multiple Systems

LAM has several information systems, each with different structures and access methods. Users need a single place to search across selected sources and also understand where information is stored.

**Goal:** Build a unified retrieval interface that returns answers and links users back to the original sources.

### 4.2 Technical Questions Requiring Detailed Document Searches

Engineers may need to find a specific technical value buried inside a long document. For example, a detector specification may span approximately 70 pages, and finding its diameter or spectral response may require consulting several documents.

**Goal:** Retrieve precise technical values, explain their context, and cite the supporting documents rather than forcing users to search manually.

### 4.3 Figures, Graphs, and Non-Textual Information

A key challenge discussed was retrieving spectral-response information at multiple wavelengths. A previous RAG attempt reportedly failed because it did not correctly interpret the graph's abscissa (the x-axis).

The system may need to interpret:

- Graph axes, units, labels, and plotted values.
- Figures and diagrams embedded in PDFs.
- Tables, captions, and document headers.
- Relationships between values in different documents.

**Goal:** Investigate whether multimodal retrieval can reliably extract this information within the project's time and resource constraints.

This is a feasibility question, not a capability the prototype should assume it can achieve. A sensible first milestone is to support text and structured tables reliably, then evaluate selected figures and graphs separately.

### 4.4 Version Control and Changing Specifications

Technical documents may contain multiple versions of the same parameter. Information changes as projects progress through their design and validation stages.

Questions the system should eventually answer include:

- What is the latest approved value?
- Which document and version contain this value?
- How has the value changed over time?
- Is a value preliminary, final, or superseded?

**Goal:** Preserve version metadata and distinguish the latest available document from the latest approved specification. These are not necessarily the same.

### 4.5 Access Control and Confidentiality

Different users may have access to different projects or documents.

**Goal:** Apply permissions before retrieval, not merely when displaying the final answer. The system must not expose restricted document contents, titles, or snippets through search results, error messages, or generated summaries.

### 4.6 Onboarding and Discoverability

LAM has many internal webpages and information systems, including an internal system referred to as Intercom in the meeting notes. New and existing staff may struggle to find the correct resource.

**Goal:** Help users locate relevant information and understand which internal system or document they should consult.

### 4.7 Reliability and Knowing When Not to Answer

A fluent but unsupported technical answer is unacceptable in a scientific setting.

**Goal:** Ensure that answers are traceable to retrieved evidence and that the system abstains when it cannot find adequate support.

## 5. Receivables from LAM

The following items are expected from LAM. Their availability and timing should be confirmed.

- [ ] **Atrium / Baggera information:** Clarification of the relationship between the systems, followed by available access or an export/data dump.
- [ ] **Cleaned WordPress / DIRCOM dump:** The cleaned export of relevant webpages and associated PDF content.
- [ ] **MMX PDF documents:** Technical documents for the initial mission prototype, including relevant specifications and validation documents.
- [ ] **Expert use cases:** Questions from LAM engineers and researchers, ideally accompanied by expected answers or reference documents, including difficult and unanswerable questions.
- [ ] **Access and domain expertise:** Source contacts, access permissions, technical clarification, and expert feedback.
- [ ] **Regular follow-up:** A weekly one-hour follow-up was proposed in the project brief.

## 6. Expected Team Deliverables

- A working RAG prototype for one mission and two or three sources.
- Connectors and a document-processing pipeline.
- Metadata preservation for source, date, version, and access rights.
- A searchable index with hybrid retrieval.
- A web interface with cited answers and an explicit abstention mechanism.
- An evaluation dataset of 50–100 questions, including unanswerable questions.
- An evaluation against a keyword-search baseline.
- A final demonstration, technical documentation, and recommendations for extending the system.

These deliverables are consistent with the attached project brief.

## 7. Questions for the Next Meeting

### 7.1 Scope and Data

- [ ] Confirm that MMX is the final mission for the prototype.
- [ ] Confirm the initial two or three sources: Redmine, WordPress, Atrium, and/or document directories.
- [ ] Clarify whether Baggera has been replaced by Atrium or remains a separate system.
- [ ] Confirm the expected data volume, formats, languages, and available export methods.
- [ ] Confirm whether the cleaned WordPress dump and MMX PDFs are ready, and when Redmine and Atrium access will be available.

### 7.2 Technical Questions

- [ ] Clarify the required deployment model, open-source constraints, and whether external APIs are prohibited.
- [ ] Confirm the machine or GPU available for development and evaluation.
- [ ] Agree on the response-time target and how it will be measured.
- [ ] Clarify the required permission model and how access rights can be obtained from each source.
- [ ] Confirm the expected behavior for document updates, deletions, version conflicts, and historical queries.
- [ ] Agree on the initial scope for figures, graphs, and tables.

### 7.3 Evaluation and Coordination

- [ ] Obtain the expert use cases and reference answers or documents.
- [ ] Confirm the test users and whether they can participate in a held-out evaluation in week 7.
- [ ] Agree on the web interface requirements and whether an existing internal UI should be integrated.
- [ ] Confirm the source contacts and how to request access or exports.
- [ ] Schedule the weekly one-hour follow-up and the scope sign-off and handover meetings.

## 8. Questions Specifically for Anne-Laure

These are the items explicitly flagged for Anne-Laure in the notes, along with closely related project-level decisions that need to be settled.

### 8.1 Infrastructure and Sovereignty

- Must the entire system run locally and use only open-source components?
- Is a dedicated machine or GPU available?
- Which machine should be used to measure the response-time target?

### 8.2 Access Controls and Security

- What exactly does the system need to protect: documents, individual passages, metadata, generated summaries, or all of these?
- Are permissions inherited from the original systems?
- How should the prototype handle users with different access levels?

### 8.3 Access to Project Data

- Can she confirm the selected mission and initial sources?
- When can the team obtain the cleaned WordPress dump, MMX PDFs, and required Atrium or Redmine access?
- What is the process for requesting project-data exports?
- Should the team contact Thomas Fenir when it is ready to scrape or export project information, and should this be coordinated through Anne-Laure first?

### 8.4 Technical Feasibility and Success Criteria

- Is graph and figure interpretation essential for the first prototype, or can it be treated as an experimental extension?
- Can the team agree on evaluation metrics, response speed, and acceptable limitations?
- Who will validate technical answers and help investigate failures?

## 9. Next Steps

The sequence below combines the kickoff decisions with the nine-week plan in the project brief. Exact task ownership can be decided by the team rather than being fixed to the roles proposed in the brief.

### Step 1 — Finalize the Scope and Source Inventory

- Confirm MMX.
- Select two or three sources.
- Document access methods, permissions, and source contacts.

**Priority:** Immediate.

### Step 2 — Obtain and Snapshot the Data

- Collect the available exports and sample documents.
- Record the snapshot date.
- Preserve document identifiers and versions.

### Step 3 — Collect Use Cases and Build Keyword Search

- Obtain the expert questions.
- Include unanswerable cases.
- Establish a BM25 baseline before tuning the RAG system.

### Step 4 — Implement Ingestion and Retrieval

- Parse documents.
- Normalize metadata.
- Chunk by document structure.
- Compare keyword, semantic, and hybrid retrieval using Qdrant.

### Step 5 — Build the Answer and Web Interface

- Return citations, source links, and version information.
- Implement abstention.
- Test technical questions.
- Assess figure extraction separately.

### Step 6 — Test Permissions, Updates, and Answer Quality

- Evaluate retrieval accuracy, citation support, abstention, and response time.
- Have LAM experts review errors and unseen questions.

### Step 7 — Freeze and Document the Prototype

- Deliver the repository and connector configuration.
- Prepare a demonstration and evaluation results.
- Recommend future sources and missions.

## 10. Evaluation Targets

The project brief gives the following starting targets. These need to be agreed with LAM rather than treated as guaranteed outcomes.

| Metric | Initial target |
|---|---:|
| Retrieval Recall@5 | At least 85% |
| Unsupported key facts | None |
| Correct abstention on unanswerable questions | At least 80% |
| Response time | Under 10 seconds on the agreed machine |
| Comparison with keyword search | Clear improvement over BM25 alone |

## 11. Additional Decisions and Risks

- **Qdrant vs. ChromaDB:** Qdrant is the team's preferred direction; confirm the need for summaries, metadata filtering, and permission-aware retrieval before finalizing the architecture.
- **Graph interpretation:** Reading exact values from spectral-response plots is a potentially difficult research and engineering task. Define a small test set of graphs and measure extraction accuracy before committing to full support.
- **Version semantics:** Decide how to identify the latest *approved* document rather than simply the most recently uploaded file.
- **Summary security:** If the system generates summaries from restricted documents, confirm that summaries cannot expose protected information to unauthorized users.
- **Source freshness:** Establish whether weekly changes will be delivered through periodic exports, APIs, or manual refreshes.
- **Terminology:** Confirm the exact system names and the relationship between Atrium and Baggera before implementing connectors.

## 12. Immediate Action Summary

1. Confirm MMX and the two or three initial sources.
2. Obtain the cleaned WordPress dump, MMX PDFs, and relevant Redmine/Atrium access.
3. Get the expert use cases and reference answers from LAM.
4. Ask Anne-Laure about local deployment, hardware, access controls, and data-export coordination.
5. Build a small end-to-end baseline before investing heavily in complex graph extraction.
6. Schedule the regular follow-up sessions and agree on the scope and evaluation criteria.

**Overall direction:** Build a reliable, permission-aware, source-cited search assistant for MMX first. Prioritize accurate text retrieval, provenance, and version tracking, while treating advanced figure and graph understanding as a feasibility workstream until its performance can be demonstrated.

---

## Reference

- *CentraleDigitalLab — Industry Project Briefs, Project 02: LAM, “Search assistant for space-mission knowledge,” dated 17 October 2026.*
