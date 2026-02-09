# wikaid
Wikaid AI-driven state machine for multi-stage wikimedia &amp; wikibook auditing, correcting, and reporting.

# overview
You are my wikibooks author assistant.  

Your mission is to detect and fix spelling mistakes, failed logic, and inconsistencies, suggest corrections and improve the text's clarity and flow. Detect and fix broken links, suggest topic separation and page merges. Find duplicate, redundant or wrong sections. 

Suggest completions, further reading sections and links. Check sources and suggest new ones.

Report with a checklist allowing me to accept, revert, give a short instruction or mark as needing a separate discussion.

# Tech stack
## 🛠 Technical Stack

Wikaid is built as a **Chrome Extension** to provide native interaction with the Wikibooks editor, through a chat in the sidebar.
aaaaaaaaaaaa
Project Architecture: Monorepo
Managed via GitHub Codespaces using Gitflow and TDD. The project is split into two main packages, running simultaneously with concurrently.

1. The **Backend: wikaidBrain** - The **"Intelligence Center"** hosted in a Docker container.
   
* **Orchestrator:** **LangGraph**.js (State machine managing discussion stages, phases, and human-in-the-loop interrupts).
* **API Framework:** **Hono** (Modern, lightweight, and TypeScript-native alternative to Express).
* **Intelligence:**
- For **chat** and instructions (Local): **Gemini Nano** (Built-in Chrome API for cost-free, low-latency).
- For **book** analysis (Cloud): Google **Gemini 2.0 Flash** (High-performance API, 1M+ token context reasoning).
* **Database:** **Supabase** (Postgres) used for state persistence (Checkpointers with stage parameters), report storage, and long-term memory to resume lost environment.
  **Logging:** **Pino + pino-pretty** for structured, readable debugging of AI transitions.

**2. The Frontend: Chrome Extension**
A "Thin Client" providing the native interface.
* **UI/UX: React** (Hosted in the Chrome Side Panel for a persistent auditing experience).
* **Native Control:** `Chrome Content Scripts` (Direct cursor manipulation and text injection).
  
* **Build Tool: Vite** (For fast bundling and Hot Module Replacement).

* **Testing: Vitest** (Unit testing) and **Playwright** (E2E testing for the extension and browser interaction).

* **Environment:** developed in `GitHub Codespaces` with `Gitflow` and `TDD`.

# Development Methodology
Wikaid utilizes Behavior-Driven Development (BDD), GitFlow, Github project

### Packages 
The program is developed on GH Codespaces with the following packages
- **Logging:** 

**Frontend:**  
- vitest

**Backend**



#### Dev extensions for Visual Studio Code



---

# Program Flow

1. Wikaid's interface works through a ***natural language chat*** controlling the workflow and notifying the user of changes. 

1.1 . ***Stages, Phases, Short segments*** 
- Each discussion is part of a stage (Input, Analysis, Report, Action) and its phases (Planning, Aligning, Doing)
- During each stage the discussion accumulates the parameters needed for executing the planned actions of that stage.   
- Long replies are cut into sections presented one by one only after the user's ok to proceed.
- 

1.2 ***Alignment discussion phases:*** 

- Each stage and substage ends with an alignment phase: A summary of what will be next, probes for gaps of information still needed from the user, and a request for permission to proceed to the next stage. (ok?,  continue?  etc.) 

- If remarks were given and there is no approval to proceed, turn this into an alignment discussion state, and wait for issues to be resolved and user to approve, before moving on. 

1.3 ***Input stage:***  The analysis scope, general plan and extra instructions are received through a structured discussion. Once all the plan parameters  are gathered, a parameters json is given and if the user approves we begin the analysis and suggestion stage. 

***Analysis stage:***  The program executes analysis and suggestion tasks in a structured segment. 
Each segment may, according to its definitions ask the user for clarifications and futher information. The output of this stage is a suggestions json.

***Approval stage:*** The program presents all corrections in a structured discussion, accumulating the user's responses to its suggestions. 

***Action stage:*** The program steps the user through actions that allow it to correct the information or instruct the user to make the changes, according to the user's decisions, while warning if any information may be lost in the process. 
