**Document name:** Stages
**Version:** 0.9

# ***Alignment:*** Before moving on always align with user, 
Checking you have the right topic names, correct aquired parameters, 
agreeed summary of discussions and instructions, correctly ignored issues, 
and the user's ok to proceed. 

# ***The stages:***
1. Input  - get scope and get familiar with its content
   Note: User may choose to load an old analysis.
3. Grounding - create a lexicon with user, expert, and content terminology
4. Analysis plan - suggest, discuss and accept analysis plan
5. Analyze - run analysis and discuss results. 
6. Action approval - decide on actions according to report
7. Implement revisions - modify the texts according to the decisions. 

## Stage definition parameters
Every stage has: 
- Results - the main objectives 
- Parameters being gathered (tracked issues), marked with importance
- Ending trigger - what brings the stage to the final alignment and user approval
- Phases:  Topics and subtopics discussed  
Note: if a blocking parameter was ignored it must be brought up before or during alignment 
and cannot proceed without looking it up. 

'''
(yaml)
# == 1. Input stage ==
Results: 
- analysis.scope, analysis.area # links to wiki pages and sections 
- optional: analysis.intents, analysis.plan.instructions # initial direction
Ends: aquired analysi.scope, possible analysis.instructions, familiarized with content 
Final: align, update modules, move to next stage. 

Parameters:    # params of interest (issues.stage.input.params) 
- area         # optional, wiki area - e.g. sitename chapter or namespace. 
- scope        # blocking, wiki scope - e.g. page or section list, highlighted text
- domain       # optional, wiki domain - e.g. what content is aboutd
- instructions # optional, user instructions

Phases: (Topics permanently in topics.stage.input.phases)
1. Wiki area and Wiki scope.
2. Familiarity with content
3. Initial analysis instructions
```

---

```
# == 2. Grounding stage ==
Results: 
- knowledge.{domain}.lexicon         # short (150 phrases) domain lexicon
- knowledge.user.{domain}.terminologyMap  # maps user terminology to expert
- opt: issues.user.stage.ground.topics    # user initiated discussion topics
Ends: aquired analysis.domain, analysis.persona # these point to lexicons
Final: align, update modules, persist data, move to next stage. 

Parameters: # params of interest (issues.stage.input.params) 
- domain lexicon         # blocking, from expert site and any existing lexicon
- user terminology map   # blocking, from content and existing lexicon
Phases: (Topics permanently in topics.stage.ground.phases)
1. Domain expert sites
2. Content terminology and style
```

---

```
# == 3. Workflow choice stage (iterations or 1 shot) ==
Results: 
- analysis.workflow.iterative/once     # chosing between 1 shot or iterative revisions
Ends: decision one way or the other
Final: align, update modules, move to beginning of analysis iteration in next stage

Parameters:    # params of interest (issues.stage.workflow.params) 
- analysis.plan.workflow   # iterations or 1 shot

Phases: (Topic in topics.stage.workflow.phases)
1. Analysis iterations or one-shot?
```

---

```
# == 4. Plan analysis stage (iteration start) ==
Results: 
- analysis iteration, analysis.plan   # structured plan with extra instructions
Ends: cycled through supported analysis categories and types
Final: align, update modules, move to next stage. 

Parameters:    # params of interest (issues.stage.input.params) 
- analysis.plan.types      # from list of supported types 
- analysis.instructions    # additional user supplied instructions

Phases: (Topics dynamically created from template in topics.stage.plan.phases)

1. Choose from analysis categories
2. Choose analysis types 
3. Extra analysis instructions
```

---

```
# == 5. Report stage ==
Results: 
- analysis.report.items.decision    # structured report decisions
Ends: decided on all analysis.report parts
Final: align, update modules, move to next stage. 

Parameters:      # params of interest (issues.stage.analyze.params) 
- param?         # optional/blocking, exaplained - e.g. example??

Phases: (Topics permanently in topics.stage.report.phases)
1. Iteration:  Waiting for report 
2. Report overview
3*. Multi-part:  Respond to report part {num, section}
   Each issue has: change/ignore/discuss do: [ ]
```

---

```
# == 5. Implement changes stage ==
Results: 
- analysis.fixes.complete for all analysis.iteratons
- optional: analysis.??  # remarks
Ends: aquired analysi.fixes complete
Final: align, update modules, go to next iteration or finish 

Parameters:    # params of interest (issues.stage.implement.params) 
- param?         # optional/blocking, exaplained - e.g. example??

Phases: (Topics from template in topics.stage.implement.phases)
1. Modifications overview
2. Multi part: Implementing {item}
```

---

```
# == 6. Approve actions stage ==
Results: 
- analysis.??            # remarks
- optional: analysis.??  # remarks
Ends: aquired analysi.??, possible analysis.??, ?? 
Final: align, update modules, move to next stage. 

Parameters:    # params of interest (issues.stage.input.params) 
- param?         # optional/blocking, exaplained - e.g. example??

Phases: (Topics permanently in topics.stage.??.phases)
1. 
2. 
3.
```
 
```

---

```
# == 7. Implement changes stage ==
Results: 
- analysis.??            # remarks
- optional: analysis.??  # remarks
Ends: aquired analysi.??, possible analysis.??, ?? 
Final: align, update modules, move to next stage. 

Parameters:    # params of interest (issues.stage.input.params) 
- param?         # optional/blocking, exaplained - e.g. example??

Phases: (Topics permanently in topics.stage.??.phases)
1. 
2. 
3. 
```
