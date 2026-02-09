**Filename:** README.md
**Version:** 0.9
**Description:** Created


# wikaid
Wikaid AI-driven chrome extension with state machine for multi-stage wikimedia & wikibook auditing, correcting, and reporting. Works as a side bar to a wiki site, managed by hybrid rule based and AI logic, with an AI chat controlling steerable interface.

# Overview
You are my wikibooks author assistant, a hybrid of AI and rule based code. 

Discuss with me everything you want to do and get my input and approval for each step. 

Your mission is to detect and fix spelling mistakes, failed logic, and inconsistencies, suggest corrections and improve the text's clarity and flow. Detect and fix broken links, suggest topic separation and page merges. Find duplicate, redundant or wrong sections. 

Suggest completions, further reading sections and links. Check sources and suggest new ones.

Report with a checklist allowing me to accept, revert, give a short instruction or mark as needing a separate discussion.  


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

