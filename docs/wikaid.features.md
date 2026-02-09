# features for prototype 1 

| # | Pickled | Where | What | How (Test Focus / Details) |
| :--- | :--- | :--- | :--- | :--- |
| **1a** | [ ] | Root | **Repo Structure** | Initialize monorepo according to the tree (to be supplied). |
| **1b** | [ ] | Root | **Repo dependencies** | Initialize dependencies according to requirements (to be suppllied). |
| **2** | [ ] | Side | **Side Framework** | React / Chrome Extension in TS; should provide native editor interaction. |
| **3** | [ ] | Brain | **Brain Framework** | TS Hono with LangG/C, Supabase, and Ollama. |
| **4** | [ ] | Root | **Mocking Environment** | Set up vitest/mocking environment for isolated component/module testing. |
| **5** | [ ] | Brain | **Input1: Mock Data** | Test Inputter with: "wikibook הגות הנפש". |
| **6** | [ ] | Brain | **Orchestrator: Init** | Test that orchestrator correctly starts on the 'input' path. |
| **7** | [ ] | Brain | **Orchestrator: Project Info** | Add metadata: site name, timestamp, and errors status (list + severity). |
| **8** | [ ] | DB | **Project History (TBD)** | Persistent storage in Supabase: name, timestamp, stages, and issues (params) with resolved state/notes. |
| **9** | [ ] | Brain | **Error Handling: Orch** | Orchestrator sends clear call to Responder; notifies user; saves to DB; attempts clean restart. |
| **10** | [ ] | All | **Module Clear Function** | All modules/UI must implement `clear(errorMessage)` function. Define and test each. |
| **11** | [ ] | Side | **Chat UI: IO** | UI: input box, output area for chat "history". Test msg send/receive. |
| **12** | [ ] | Side | **Chat UI: Controls** | Submit, Pause, Cancel, Edit. Verify button click changes state. |
| **13** | [ ] | Brain | **Flow: Input1** | Controls discussion; connects Inputter and Responder for Input1 stage. |
| **14** | [ ] | Brain | **Issues: Input1** | Params: 1. Wiki Area (Optional) 2. Wiki Scope (Blocking). |
| **15** | [ ] | Brain | **Responder: Input1** | Suggest pages/sections if Area exists; |
| **15b**| [ ] | Brain | **Responder: Input1 error** | handles error notifications via `clear`. |
| **16** | [ ] | Brain | **Input1 Output** | Should produce resolved parameters in valid JSON format. |


# list of missing requirement details for prototype 1 (no additional components yet)
1. Tree
```
wikaid/
├── packages/
│   ├── docs / wikaid.requirements.md # this file
│   ├── side/               # Wicked Side: Chrome Extension (React + Vite)
│   │   ├── src/
│   │   │   ├── background/ # Service worker for extension events
│   │   │   ├── content/    # Content scripts (DOM injection)
│   │   │   ├── sidebar/    # UI for the audit & discussion
│   │   │   └── common/     # UI components
│   │   └── package.json
│   │
│   ├── brain/              # wikaidBrain: LangGraph AI Orchestrator
│   │   ├── src/
│   │   │   ├── orchestrator/
│   │   │   │   ├── discussion/
│   │   │   │   │   ├── flow/      # Managing session state & history
│   │   │   │   │   ├── topics/    # Segmenting the wikibook content
│   │   │   │   │   ├── issues/    # Tracking detected problems
│   │   │   │   │   ├── align/     # Detector and Aligner sub-modules
│   │   │   │   │   ├── responder/ # Generating user-facing explanations
│   │   │   │   │   └── executor/  # Finalizing approved changes
│   │   │   │   └── stages/
│   │   │   │       ├── input1/    # Wiki extraction & segmenting
│   │   │   │       ├── ground2/   # Initial domain grounding
│   │   │   │       ├── plan3/     # Planning the audit strategy
│   │   │   │       ├── analyze4/  # Deep Audit Node:
│   │   │   │       │   ├── accuracy/  # lingu, logic, src
│   │   │   │       │   ├── structure/ # redundancy, organize
│   │   │   │       │   └── expand/    # supplement, enrich
│   │   │   │       ├── report5/   # Summarizing findings for side
│   │   │   │       ├── approve6/  # Human-in-the-loop gate
│   │   │   │       └── execute7/  # Pushing back to MediaWiki
│   │   │   ├── knowledge/
│   │   │   │   ├── tech/          # flash, llama, kg adapters
│   │   │   │   └── bases/         # domain, discussion, lexicon, actions, revised
│   │   │   └── index.ts    # Hono API entry point
│   │   ├── tests/          # Vitest & Cucumber
│   │   └── package.json
│   │
│   └── shared/             # Shared Logic & Contracts
│       ├── src/
│       │   ├── schema/     # Zod definitions for the 22-task mission
│       │   └── types/      # Global Enums (e.g., a_rejected, a_aligned)
│       └── package.json
```

   
3. requirements


# check these features where added to the feature file

| Pickled | Where      | What                    | How                                                                                           |
| :------- | :--------- | :---------------------- | :-------------------------------------------------------------------------------------------- |
| [ ]     | Side       | sidebar injection       | Extension detects Wiki URL and injects the wikaid side interface.                             |
| [ ]     | Brain      | state machine ini       | Initialize LangGraph session and memory when the side bar is opened.                          |
| [ ]     | Side       | steerable chat ui       | UI with input/output, send/pause, and cancel to control app and receive feedback.             |
| [ ]     | Brain      | orchestrator module     | Manages the progression through stages, phases, and the aligner.                              |
| [ ]     | Brain      | (with responder module) | Manages conversation context: topics, issues, and flow.                                       |
| [ ]     | Brain      | (and actions module)    | Handles the data structures for wiki modifications (action data: TBD).                        |
| [ ]     | Brain      | report: creation        | Brain creates an accumulated report of all audit findings.                                    |
| [ ]     | Side/Brain | report: transfer        | Brain passes the accumulated report to the wikaid side at the designated stage.               |
| [ ]     | Side       | report: interaction     | Sidebar displays the report or initiates a discussion/clarification with the user.            |
| [ ]     | Brain      | report: resolution      | Brain receives the finalized/resolved report after the user discussion ends.                  |
| [ ]     | Brain      | report: execution       | Brain performs the wiki fix or provides the direct path for the user to fix it.               |

