# wikaid
Wikaid AI-driven state machine for multi-stage wikimedia &amp; wikibook auditing, correcting, and reporting.

# overview
You are my wikibooks author assistant.  

Your mission is to detect and fix spelling mistakes, failed logic, and inconsistencies, suggest corrections and improve the text's clarity and flow. Detect and fix broken links, suggest topic separation and page merges. Find duplicate, redundant or wrong sections. 

Suggest completions, further reading sections and links. Check sources and suggest new ones.

Report with a checklist allowing me to accept, revert, give a short instruction or mark as needing a separate discussion.

# 🛠 Technical Stack

Wikaid is built as a **Chrome Extension** to provide native interaction with the Wikibooks editor, through a chat in the sidebar.

Project Architecture: Monorepo
Managed via GitHub Codespaces using Gitflow and TDD. The project is split into two main packages, running simultaneously with concurrently.

1. The **Backend: wikaidBrain** - 
The _Intelligence Center_ hosted in a **Docker container**.
   
* **Orchestrator:** **LangGraph.js** (State machine managing discussion stages, phases, and human-in-the-loop interrupts).
* **API Framework:** **Hono** (Modern, lightweight, and TypeScript-native alternative to Express).
* **Intelligence:**
- For **chat** and instructions (Local): Ollama **Llama 3.2** (High-speed, 100% private local inference)
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
- Pino (for logging)
- To be completed

**Production Frontend:**  
- Todo: finish this

**Production Backend**  
- Todo: finish this

#### Dev extensions for Visual Studio Code  
** Dev Frontend:
- vitest (for fe TDD)

** Dev Backend: 

---

# Program Flow

1. General
Wikaid's interface works through a ***natural language chat*** controlling the workflow and notifying the user of changes. 

1.1 . ***Stages Phases and  Discussion Awareness*** 

1.2 ***Stages and Phases:*** Each discussion is part of a stage, and its phases.

1.3 ***Discussion flow and state:*** The stage and phase along with the required parameters to be aquired are managed in the orchestrator's state machine. See details of discussion flow and the **topics** module.  

1.4 **Constant allignment:**  The discussion is constantly aligning with the user, via active listening methods, but at the end of each segment there is a methodical stop for alignment, before proceeding. This alignment session for each stage and segment is managed by the orchestrator state machine as well, with dedicated modules for moving it through the alignment process. (modules: topics, issues, (discussion) flow, alignment manager)

1.5 ***Discussion awareness and segmented information:***  
1.5.1 **Topics:** The topics during a discussion are tracked by the ***topics*** module, which detects manages aligns and persists the ongoing topic names, consolidating them with the topics of the discussions aligned earlier. 

1.5.2 **Segments:** Long replies are broken into shorter segments, shown one by one only after presenting the plan and the user's ok to proceed. The orchestrator's ***flow*** module prioritizes the response segemnts and detects the topics being discussed and planned to be discussed aligning them with the user.   

Please note: The ```topic manager``` alignment of the current and planned topic names is not the same as the ```flow manager``` alignment of the proposed topic plan itself, although both could easily be done together in a single alignment session, so the topic manager can read the results of the flow manager alignment and update the topics accordingly. 

1.5.3 ***Accumulated discussion:*** Multi iteration discussions are treated as one long text, accumulating information along the way with the orchestrator's ***issues*** module from both the user's input and the AI's output.  It accumulates the "parameters" - issues needing resolution or decision, like the intent, detailed instructions, solving ambiguities, following information segments, all of which are needed for executing the planned actions of that stage or future stages. The issue analyzer adds modifies and merges the parameters with current existing ones.  No single request or response are treated outside of this broader context. 

The parameters are gathered from the user input during the discussions, from the AI output, and from the wiki site analysis. See the ```issue analyzer``` details.

1.5.4 ***Orchestrator states:*** The orchestrator's state machine manages several state cycles:
   - **Stage states:**  1.Input, 2.Grounding, 3.Analysis-plan 4.Analysis, 5.Report, 6.Action-plan, 7.Action
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

1.5 ***Analysis stage:***  The program executes analysis and suggestion tasks in a structured segment. 
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
   
   - 5. For action plan stages (stage #3 analysis plan, stage #6 action plan) The final result of an alignment session is passed to the ```actions``` module which holds a final action plan with tasks, sequences, and execution paths with their tools and params. The actions is sent into the 

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

