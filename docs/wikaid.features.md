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
