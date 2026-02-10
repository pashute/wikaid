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

### **Side bar control**
During the planning stages the sidebar has a chat interface which simulates (but is not) an AI agent chat, with a structured controlled interface, responding according to the stage and state of the conversation, notifying the user of state changes and planned topics, and interacting with the user's input, gathering instructions, confirming the analysis plan, reporting its findings, and finally approving and executing the changes to the site.  

### **AI offload and hybrid computing** 
The wikaid program saves the access to a large and capable (and expensive) LLM for the big analysis job, minimizing any interaction with it until necessary. For the long and detailed preparation process it uses a local LLM. 

Even this local LLM (or SLM) is used as a component in logical action sequences, controlled by rule-based programming in the backend (brain) orchestrator, run with LangChain/LangGraph. 

#### **Dynamic workflow**
There is one single zzz
The ```runner``` object (in the orchestrator folder)

### **Mediawiki integration**
For creating the report there must be at least the ability to read the wiki text, 
best if as source code, and With the resulting report and user approval 
we should be able to reach the locations in the mediawiki and even inject the corrected text in its place.

### **Self aware discussion with accumulated state**
Each wikisite analysis request is called a project. The project has a site name (given after the scope is determined in the Input stage), which the user can change at any point and stage, and a timestamp of the date and time it was started. The projects history, including topics discussed, issues resolved or ignored and the resulting report and actions are saved. Each stage also has an errors list if any occured, stored with their severity. A project can be aborted and "cleared" if needed, and every module has a clear action (with a reason), by which it should start clean. 

At all times the project's state and its parameters (open and resolved issues, discussed and planned topics), user decisions and context (such as term changes) are accumulated and stored, creating a context aware conversation with the current state and passed states in mind, and with the knowledge of the planned topics, understanding where the conversation is going, and not forgetting or "losing the ball".  

## Operational requirements summary
```
  # Connectivity:
  - id: bridge_api       # Must support MediaWiki text extraction and injection
  - id: hybrid_ai        # Local Llama for control; Gemini Flash for grounding and wiki analysis
  - id: sidebar_sync     # Frontend must use a stateful sync (React-Query) with the brain
  - id: persistence      # All audit states, user approvals, and grounding knowledge base must be saved in Supabase
   # Development DDD/BDD: 
  - id: gherkin_parity   # Every mission task must have a corresponding Cucumber .feature
````
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
│   ├── docs / wikaid.requirements.md # this file
│   ├── side/                 # Wicked Side: Chrome Extension (React + Vite)
│   │   ├── src/
│   │   │   ├── config/       # side.config.yaml
│   │   │   ├── background/   # Service worker for extension events
│   │   │   ├── content/      # Content scripts (DOM injection)
│   │   │   ├── sidebar/      # UI for the audit & discussion
│   │   │   └── common/       # UI components
│   │   └── package.json
│   │
│   ├── brain/                # wikaidBrain: LangGraph AI Orchestrator
│   │   ├── src/
│   │   │   ├── config /      # brain.config.yaml, prompts.yaml
│   │   │   ├── orchestrator/ # modules: orchestrator, requester, responder, runner
│   │   │   │   ├── discussion/
│   │   │   │   │   ├── flow/      # Managing session state & history
│   │   │   │   │   ├── topics/    # Segmenting the wikibook content
│   │   │   │   │   ├── issues/    # Tracking detected problems
│   │   │   │   │   ├── align/     # Detector and Aligner sub-modules
│   │   │   │   │   └── executor/  # Finalizing approved changes
│   │   │   │   └── stages/ 
│   │   │   │       ├── input.1/    # Wiki extraction & segmenting
│   │   │   │       ├── ground.2/   # Initial domain grounding
│   │   │   │       ├── plan.3/     # Planning the audit strategy
│   │   │   │       ├── analyze.4/  # Deep audit 
│   │   │   │       │   ├── accuracy/    # lingu, logic, src
│   │   │   │       │   ├── structure/   # redundancy, organize
│   │   │   │       │   └── expand/      # supplement, enrich
│   │   │   │       ├── report.5/     # Summarizing findings for side
│   │   │   │       ├── approve.6/    # Human-in-the-loop gate
│   │   │   │       └── execute.7/    # Pushing back to MediaWiki
│   │   │   ├── knowledge/
│   │   │   │   ├── tech/   # adapters for flash, llama, embedded kg, config 
│   │   │   │   └── bases/  # domain, lexicon, actions, revised, history
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

# Program Flow

1. General
Wikaid's interface works through a ***natural language chat*** controlling the workflow and notifying the user of changes. 

1.1 . ***Stages Phases and  Discussion Awareness*** 

1.2 ***Stages and Phases:*** Each discussion is part of a stage, and its phases.

1.3 ***Discussion flow and state:*** The stage and phase is managed in the ```orchestrator``` module. 

1.3.1 **Stager module:** During stage change, the orchestrator invokes the ```stager``` module, which loads the phase changes and the dynamic workflows into the "dynamait" (dynamic AI tool) modules: These modules are set with the workflow they need to pass through for each phase of the stage, what modules are they connected to and what they should supply to and/or recieve from them, and how. 

The workflow is a list of parametrized tasks, some of which are dynamic AI prompts, others are dynamic tasks, that include rule based "tools" and their parameters. 

The "dynamait" modules are:
- The ```requester``` module:  takes care of reading the user's text, reading and updating the proper info modules and performing post request actions like verification, resolved issue merging, or new topics analysis. 
- The ```responder```: works closely with the ```flow``` to give relevant responses during each iteration, directing the discussion to stay on track and gather the needed parametric information, using subtle probes.
- The ```runner```:  runs the stage's action phases according to the phase and to the loaded instructions from the stager.
- 
The "info" modules are, also initialized with each stage change by the ```stager``` module are: 
- ```issues``` for parameter resolutions.
- ```topics``` for the discussed topic names and definitins
- - ```flow``` for the discussed, planned, and current topics
- ```history``` holds the changes to issues, topis and flow, per project.
  Note:  history has an ```immediate``` field for current info, at hand.   


The workflow configuration for each dynamait module and for each stage, is stored mainly in src/config/stage.config.yaml. 

Sample configuration:
{ stage: input, phases: [
  { phase: scope, tasks: [
        {task: setup, tool: setRequester, params: [
          {param: issues, values: [
            {issue: get-area, importance: ignore}, 
            {issue: get-scope, importance: blocking} 
          ]}, 
          {param: topics, values: [
            topic: wikibook {{bookname}} improve, 
            topic: wikibook analysis 
          ]}
        ]},
        {task: suggest-scope-pages, tool: websearch, params: [
            param: focus, values: [pagenames namespace, chapter]}
  ]
}

- The ```issues``` module - holds the parameters of importance, to be searched for and resolved during the stage's discussion in its different phases. 
```requester``` module - takes care of reading the user's inputs during this stage's iterations, and accumulating the important parameters during the stage's phases and segments.
- The ```responder``` - takes care of "self aware" reading of the responses given. The response is a result of various actions taken by the responder 
- along with the required parameters to be aquired are managed in the orchestrator's state machine. See details of discussion flow and the **topics** module.  

1.4 **Constant allignment:**  The discussion is constantly aligning with the user, via active listening methods, but at the end of each segment there is a methodical stop for alignment, before proceeding. This alignment session for each stage and segment is managed by the orchestrator state machine as well, with dedicated modules for moving it through the alignment process. (modules: topics, issues, (discussion) flow, alignment manager)

1.5 ***Discussion awareness and segmented information:***  
1.5.1 **Topics:** The topics during a discussion are tracked by the ***topics*** module, which detects manages aligns and persists the ongoing topic names, consolidating them with the topics of the discussions aligned earlier. 

1.5.2 **Segments:** Long replies are broken into shorter segments, shown one by one only after presenting the plan and the user's ok to proceed. The orchestrator's ***flow*** module prioritizes the response segemnts and detects the topics being discussed and planned to be discussed aligning them with the user.   

Please note: The ```topic manager``` alignment of the current and planned topic names is not the same as the ```flow manager``` alignment of the proposed topic plan itself, although both could easily be done together in a single alignment session, so the topic manager can read the results of the flow manager alignment and update the topics accordingly. 

1.5.3 ***Accumulated discussion:*** Multi iteration discussions are treated as one long text, accumulating information along the way with the orchestrator's ***issues*** module from both the user's input and the AI's output.  It accumulates the "parameters" - issues needing resolution or decision, like the intent, detailed instructions, solving ambiguities, following information segments, all of which are needed for executing the planned actions of that stage or future stages. The issue analyzer adds modifies and merges the parameters with current existing ones.  No single request or response are treated outside of this broader context. 

The parameters are gathered from the user input during the discussions, from the AI output, and from the wiki site analysis. See the ```issue analyzer``` details.

1.5.4 ***Orchestrator states:*** The orchestrator's state machine manages several state cycles:
   - **Stage states:**  1.Input, 2.Ground, 3.Plan 4.Analyze, 5.Report, 6.Approve, 7.Action
   - **Phase states** (within each stage): See the stage details. 
   - **Alignment state:** 1.```talking``` - active listening , 2.```align``` - suggest alignment, 3.```aligning``` - in alignment session, 4. ```aligned``` - alignment accepted and ok to proceed.
   - ***Discussion flow object:***  The orchestrator manages the discussion using discussion flow data of ```discussed```, ```current``` and ```planned``` ```topic```s to be discussed.  The discussion flow is with planned topics and agreeing on the current topic is discussed during the alignment. 

   - Note: If the user requests to return to a discussion while ```aligning``` or after ```aligned``` the system goes back to ```talking``` state. 

1.5.5 ***Alignment discussion phase:*** (for each stage) 

Each stage and substage ends with an alignment phase: A summary of what will be next, probes for gaps of information still needed from the user, and a request for permission to proceed to the next stage. (ok?,  continue?  etc.) 

Once in alignment session, the orchestrator's ***alignment*** module takes over and begins preparing and presenting the alignment steps, gathering the information and updating the other modules. the result is an alignment object used by the ***responder*** module to communicate with the user,  and by the **issues** and **topics** modules to update their information. See section 2.1 alignment details



1.3 ***Input stage:***  The analysis scope, general plan and extra instructions are received through a structured discussion. Once all the plan parameters  are gathered, a parameters json is given and if the user approves we begin the analysis and suggestion stage. 

1.4 ***Grounding stage:*** Once the scope has been determined in the input stage, and before starting any research, a grounding of the user's terminology is created by crawling the wiki site, then mapping that terminology to expert terminology from relevant domains of knowledge. The result will be an object or if too large a json file.   

Finally, the terminology will be presented to the user in the alignment session, and after approved, will be merged with any existing embedded knowledge graphs for those topics, or a new kg will be created for this topic. See details below. 

1.5 ***Analysis stage:***  The program executes analysis and suggestion tasks in a series of structured segments. 
Each segment may, according to its definitions ask the user for clarifications and futher information. The output of this stage is a suggestions json.

1.6 ***Approval stage:*** The program presents all corrections in a structured discussion, accumulating the user's responses to its suggestions. 

1.7 ***Action stage:*** The program steps the user through actions that allow it to correct the information or instruct the user to make the changes, according to the user's decisions, while warning if any information may be lost in the process. 

---

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

Before starting the users will be presented with a choice of analysis phases which they wish to include in the analysis. 

The user can stop, pause, or abort the process and revert to the last working state. 

The following are the phases for the analysis:

### **2.4.1. Content Integrity & Accuracy**

#### **..1.1 Linguistic Audit**
1. **Spelling:** Detect and fix standard spelling mistakes.
2. **Grammar:** Detect and fix grammatical errors.
3. **Clarity:** Improve sentence structure for better readability.
4. **Flow:** Enhance the logical transition between sentences and paragraphs.
5. **Style:** Change writing style according to instructions

#### **..1.2 Logic & Consistency**
6. **Logical Verification:** Identify failed logic or contradictory statements.
7. **Inconsistency Detection:** Identify internal discrepancies in data or claims.
8. **Terminology Alignment:** Ensure consistent use of terms throughout the book.

#### **..1.3 Source Validation**
9. **Fact-Checking:** Verify that external sources support the claims made.
10. **Source Verification:** Check that sources actually say what is specifically claimed.
11. **Link Audit:** Detect and fix broken URLs.
12. **Citation Audit:** Detect and fix broken or missing citations.
13. **New Source Suggestion:** Suggest new, credible sources where existing ones are weak.

---

### **2.4.2. Structural Architecture**

#### **..2.1 Redundancy Management**
14. **Duplicate Detection:** Find repeated sections across pages.
15. **Redundancy Identification:** Find redundant information that adds no value.
16. **Error Pruning:** Identify and remove objectively "wrong" sections.

#### **..2.2 Organizational Strategy**
17. **Topic Separation:** Suggest splitting over-encumbered pages into smaller topics.
18. **Page Merging:** Suggest combining thin pages.
19. **Structural Merging:** Suggest combining highly related pages for better cohesion.

---

### **2.4.3 Expansion & Enrichment**

#### **..3.1 Content Supplementation**
20. **Smart Completions:** Suggest missing information in a chapter.
21. **Next Step Suggestions:** Identify and suggest "next steps" for the learner.

#### **..3.2 Enrichment**
22. **Further Reading Generation:** Create dedicated "Further Reading" sections.
23. **Link Supplementation:** Suggest relevant external links for deeper study.

## **2.5 Change execution stage**
Once the analysis report has been resolved we can continue to actually make the changes. This can be automated, or, if that is not possible or not desired, done as an assisted step by step manual process, where wikaid leads the user to the location with instructions, and with the corrected text selected in the clipboard, it is then replaced manually by selecting the text to be changed and pasting onto it or replacing it. 

# Data structures and data modules  
There is data in each of the inforation modules: ```issues, topics, flow```. 

There is a history module that stores the current project's accumulated resolved issues and topics discussed, analysis report and change log, along with its history of state changing. Each node is under a timestamped stage's phase change. 

There is embedded (vectorized) data in the ```ground``` module, once it has finished the ground stage. 

The user has a persona object which has a lexicon of user terms by role and domain, a history of topics discussed,  associated domains of knowledge, lists of related links, with their significance, and lists of "negativity" - words and phrases not to be included with a term or phrase, each negative hint, comes with its explanation (antonym, homonym but for different field etc.)  This is updated at every stage's phase change. 

There are data adapters for connecting storing reteiving and modifying the information in the database and in the knowledge bases (knowledge bases are  are a set of embedded knowledge graphs.)  The adapters are: supabase, kg. They use ollama code. 

There are two AI adapters for accessing the local and cloud AI. 

Future feature:  If the data becomes large, the history object can hold the current state's data in memory for immediate access, and  the rest can be stored in the database. 

There is a prompt manager in the knowledge base which reads the prompts.yaml file, for constructing the different module tools according to the needs. 

There is a tools manager in the knowledge base which can extract a tool and dynamically set the code for running it. 


