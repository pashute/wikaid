**Filename:** wikaid.requirements.md
**Version:** 0.9
**Description:** Created

# wikaid requirements
Wikaid AI-driven chrome extension with state machine for multi-stage wikimedia &amp; wikibook auditing, correcting, and reporting.  Works as a side bar to a wiki site, managed by hybrid rule based and AI logic, with an AI chat controlling steerable interface. 

# 🛠 Technical Stack

Wikaid is built as a **Chrome Extension** to provide native interaction with the Wikibooks editor, through a chat in the sidebar.

## Project Architecture:
### **Monorepo**
Managed via GitHub Codespaces using Gitflow and TDD. The project is split into two main packages, running simultaneously with concurrently.

### **Backend: wikaidBrain** - 
The _Intelligence Center_ hosted in a **Docker container**.
   
* **Orchestrator:** **LangGraph.js** (State machine managing discussion stages, phases, and human-in-the-loop interrupts).
* **API Framework:** **Hono** (Modern, lightweight, and TypeScript-native alternative to Express).
* **Intelligence:**
- For **chat** and instructions (Local): Ollama **Llama 3.2** (High-speed, 100% private local inference)
- For **book** analysis (Cloud): Google **Gemini 2.0 Flash** (High-performance API, 1M+ token context reasoning).
* **Database:** **Supabase** (Postgres) used for state persistence (Checkpointers with stage parameters), report storage, and long-term memory to resume lost environment.
  **Logging:** **Pino + pino-pretty** for structured, readable debugging of AI transitions.

### **Frontend: Chrome Extension**
A "Thin Client" providing the native interface.
* **UI/UX: React** (Hosted in the Chrome Side Panel for a persistent auditing experience).
* **Native Control:** `Chrome Content Scripts` (Direct cursor manipulation and text injection).
* **Build Tool: Vite** (For fast bundling and Hot Module Replacement).
* **Testing: Vitest** (Unit testing) and **Playwright** (E2E testing for the extension and browser interaction).
* **Environment:** developed in `GitHub Codespaces` with `Gitflow` and `TDD`.

## Development Methodology
Wikaid utilizes Behavior-Driven Development (BDD), GitFlow, Github project

## Packages 
The program is developed on GH Codespaces with the following packages
# Wikaid Project Configuration: Tech Stack & Dev Ecosystem

```
# ---------------------------------------------------------
# NPM PACKAGES
# ---------------------------------------------------------

shared_packages:
  - id: typescript       # Strict typing across monorepo and shared enumerations
  - id: zod              # Schema validation for AI JSON outputs and API contracts
  - id: date-fns         # Immutable date/time management for audit logs

frontend_packages:       # Chrome Extension (React + Vite)
  - id: lucide-react     # Icon library for sidebar UI components
  - id: @tanstack/react-query # Server-state sync between sidebar and Brain
  - id: clsx             # Utility for constructing dynamic CSS class strings
  - id: tailwind-merge   # Optimizes Tailwind class conflicts

backend_packages:        # wikaidBrain (Hono + Node.js)
  - id: @langchain/langgraph # State machine orchestrator for audit cycles
  - id: @langchain/google-genai # Gemini 2.0 Flash integration for web research
  - id: @supabase/supabase-js # Client for Postgres state store & Knowledge Graph
  - id: hono             # Ultra-fast web framework for AI API endpoints
  - id: ollama           # Interface for local Llama 3.2 inference

# ---------------------------------------------------------
# VS CODE EXTENSIONS (recommendations.json)
# ---------------------------------------------------------

shared_extensions:
  - id: esbenp.prettier-vscode       # Code formatting consistency
  - id: dbaeumer.vscode-eslint       # Linter for catching logic errors early
  - id: usernamehw.errorlens         # Inline error highlighting
  - id: github.copilot               # AI pair programming

frontend_extensions:
  - id: bradlc.vscode-tailwindcss    # IntelliSense for sidebar styling
  - id: dsznajder.es7-react-js-snippets # React component boilerplates
  - id: wallabyjs.console-ninja      # Real-time logs inside the editor

backend_extensions:
  - id: humao.rest-client            # Test Brain endpoints without a browser
  - id: ms-azuretools.vscode-docker  # Manage Ollama and local DB containers
  - id: qwtel.sqlite-viewer          # Visualizing local data caches

wikaid/
├── packages/
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

# 2. Stage details:
## 2.1 Alignment phase for each stage and segment
The alignment phase is entered at the end of each segment and stage, summarizing the accumulated information till now, and detailing the next moves. 

2.1.1 **Orchestrator Alignment module** 
The alignment session is entered after a suggestion by the ```responder``` according to the ```Align detector```. 

Once in the alignment session, it is managed by the ```alignment``` orchestrator module, working along with the ```issues```, ```topics```, and ```flow``` modules (which gather and manage the open issues, the topics being discussed and the discussion flow),   and the ```responder``` module (which holds the instructions for preparing the words to respond to the user (with questions alongside the information). 

2.1.2 The alignment session always includes  
- aligning the summary of what was done
- aligning the current topic name, and planned topic names 
- aligning of the parameters that were resolved or are planned to be discussed
- aligning of the issues still  left open

During the discussion with the user, the ```responder``` module uses subtle probes, without mentioning the alignment process or even the word alignment, and without adding any unnecessary or blatant questions.

- If remarks were given and there is no approval to proceed, the alignment state remains ```aligning```, and waits for the issues to be resolved and the user to approve, before moving on.
- The pending "parameters" field for the stage is updated with the issues needing resolution, or if not known a general "get issues from user" parameter is added. 

2.1.2 ***Alignment state:***  Every stage phase and segment has an alignment-state ("flag")
- ```talking``` - active listening stage. No summary yet. Parameters are being accumulated in the phase's information store (lexicon), by topic. This is the state at the beginning of a segment, phase or stage. This state is returned to from ```aligning``` if the user wishes to leave the alignment and go back to the discussion. 
- ```align``` - topic ended. alignment suggestion is needed. 
- ```aligning``` - alignment suggestion was accepted. We are now in an alignment discussion.
- ```aligned``` - user accepted the alignment and asked to proceed. plans and phase 

- ***Align detector:*** For a stage phase or segment that is in the ```talking``` alignment state, the align detector gets information from the ```topic manager```, ```flow manager``` and ```issues manager``` checks the content, gathers the list of discussed "parameters" - topics and planned actions required for this stage or segment,  and detects whether we are probably done.  It then proposes an alignment suggestion:   
    e.g. ***Alignment suggestion:*** We have discussed and determined ```{short list of required topics and planned actions}```. 
         We still plan to discuss ```{short list of planned topics}}```
         Can I present a summary for your approval?

  -***Alignment manager:***  Once in the alignment state the ```alignment manager``` manages an alignment session,  aligning all aspects of the segment phase or stage.
  - 1. Alignment object: First it constructs an alignment object (or json), with the topics (current discussed and planned), and the issues (resolved till now, and unresolved).
  - 2. User presentation:  
  - 2a. The user receives a choice to see the summary in a file or discuss it in the chat (in a series of short segments). The alignment object is presented to the user either in a file, or through a discussion in segments.
   - 2.b For a segmented discussion, the ***responder module***  uses subtle probes (without mentioning an alignment stage, or any mention of the alignment process or the word alignment, avoiding obvious questions etc.)
   - 2.c Each item can be accepted, ignored, resolved with a short command, or discussed separately (in which case the flow module adds that discussion to the plan.
   
   - 3. ```issue manager update```: The result of each iteration is updated by the ```issue manager```: storing the accepted and ignored, modifying the modifications, marking the ones that are still under discussion, and adding any new items.
   
   - 4. ```knowledge update```: The final result of an alignment session is passed to the knowledge manager for modifying and updating the current knowledge base with topics, terms and phrases.
   
   - 5. For action plan stages (stage #3 plan, stage #6 approve) The final result of an alignment session is passed to the ```actions``` module which holds a final action plan with tasks, sequences, and execution paths along with their tools and params. The actions object is sent into the executor module

## 2.2 Grounding Stage details
2.2.1 The grounding stage consists of several phases run by the **```grounding```** module, along with the ```issues```, ```topics```, ```alignment```, and when talking to the user, with the ```responder``` module. 
- Create user lexicon - from inputs, from stored terminology (if any) per topic, from wiki in scope, and or from complete wiki.
- List knowledge domains of said terminology
- Get expert lexicons for knowledge domains
- Map the user lexicon to the expert terminology
- Align with user: Discuss lexicon (see 2.1 alignment session details), refine and get approval. 

2.2.2 ***Output:  Grounding JSON***   
The output of the grounding stage terminology probe is a json with the following format:  
- term (expert term or action name), 
- description (telegraphic),
- alias (user term),
- remarks (on user's term, if any),
- relationship (of term to other parent or child terms if any, in the format of term:relationship.)  
e.g. Seder: {relationships: [Part of: Passover ritual, Has: [matza, wine, seder plate, haggada, meal, guests, family],
-  associations (several related terms or actions, up to 5, each as term:connection)  
e.g. Associations: [Matza: Ritual food, Hametz: Prohibition]


2.2.3  ***Grounding alignment phase***
Show resulting lexicon as a table in short segments,

showing them one at a time, only proceeding with the user's ok. 
Update object according to conclusions, and move on to next segment only after approval. 

## 2.3 Analysis-plan stage details
During analysis we will receive a report of all changes, suggestions and remarks, and can then choose to relate to them, keep, revert, modify or discuss. 

Before starting the user will be presented with a choice of analysis phases which they wish to include in the analysis. 

The user can stop the abort the process and revert. 

The following are the phases for the analysis:

### **1. Content Integrity & Accuracy**

#### **1.1 Linguistic Audit**
1. **Spelling:** Detect and fix standard spelling mistakes.
2. **Grammar:** Detect and fix grammatical errors.
3. **Clarity:** Improve sentence structure for better readability.
4. **Flow:** Enhance the logical transition between sentences and paragraphs.
5. **Style:** Change writing style according to instructions

#### **1.2 Logic & Consistency**
6. **Logical Verification:** Identify failed logic or contradictory statements.
7. **Inconsistency Detection:** Identify internal discrepancies in data or claims.
8. **Terminology Alignment:** Ensure consistent use of terms throughout the book.

#### **1.3 Source Validation**
9. **Fact-Checking:** Verify that external sources support the claims made.
10. **Source Verification:** Check that sources actually say what is specifically claimed.
11. **Link Audit:** Detect and fix broken URLs.
12. **Citation Audit:** Detect and fix broken or missing citations.
13. **New Source Suggestion:** Suggest new, credible sources where existing ones are weak.

---

### **2. Structural Architecture**

#### **2.1 Redundancy Management**
14. **Duplicate Detection:** Find repeated sections across pages.
15. **Redundancy Identification:** Find redundant information that adds no value.
16. **Error Pruning:** Identify and remove objectively "wrong" sections.

#### **2.2 Organizational Strategy**
17. **Topic Separation:** Suggest splitting over-encumbered pages into smaller topics.
18. **Page Merging:** Suggest combining thin pages.
19. **Structural Merging:** Suggest combining highly related pages for better cohesion.

---

### **3. Expansion & Enrichment**

#### **3.1 Content Supplementation**
20. **Smart Completions:** Suggest missing information in a chapter.
21. **Next Step Suggestions:** Identify and suggest "next steps" for the learner.

#### **3.2 Enrichment**
22. **Further Reading Generation:** Create dedicated "Further Reading" sections.
23. **Link Supplementation:** Suggest relevant external links for deeper study.

