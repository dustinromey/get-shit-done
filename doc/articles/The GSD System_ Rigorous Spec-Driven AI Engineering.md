Based on the transcript, the creator (Tâches) demonstrates his "Get Shit Done" (GSD) system by building a procedurally generated 3D city driving game. The process is designed to prevent "vibe-coding" (aimless, unstructured generation) by replacing it with a rigorous, spec-driven workflow that prioritizes context management and planning.  
Here is the step-by-step process of the GSD system:

### 1\. Initialization and Brainstorming

The process begins not with coding, but with a deep interrogation phase to establish a "Greenfield" project specification.

* **Command:** The user runs GSD new project to initialize the system 1\.  
* **Questioning:** Instead of immediately generating code, the system asks, "What do you want to build?" followed by rounds of clarifying questions 2\. For the demo project, it asks about the target audience, technical constraints (e.g., Web/3.js), and aesthetic style (e.g., "GTA style," "low poly") 3-5.  
* **Core Documentation:** Once brainstorming is complete, the system generates the project's "core memory" files:  
* project.md: Defines the vision, scope, and decisions made 6\.  
* roadmap.md: Breaks the project into high-level phases (e.g., Foundation, Procedural City, Traffic AI) 7, 8\.  
* state.md: Tracks progress, current context, and accumulated decisions 8\.

### 2\. The Planning Phase (Meta-Prompting)

Rather than executing a broad goal immediately, the system breaks phases into specific, context-aware "Plans."

* **Discussion:** Before planning, the user runs discuss phase \[x\] (or answers questions during plan phase) to clarify specific requirements for that section. For Phase 2 (Procedural City), the user clarified building types, fog density, and road layouts 9, 10\.  
* **Context Generation:** The system reads the current codebase and generates a context.md file specific to that phase, ensuring the LLM understands the current state of the code 11, 12\.  
* **Plan Creation:** The system generates detailed "Plans." Crucially, it never creates more than three tasks per plan to ensure the work fits within a standard context window without quality degradation 13\. Each plan functions as a structured prompt containing objectives, necessary files, and verification steps 13\.

### 3\. Execution ("YOLO Mode" and Context Hygiene)

The execution strategy relies on "single-shotting" tasks in fresh context windows to maintain high performance.

* **Command:** The user copies the command provided by the system, typically execute plan \[x\] 14\.  
* **Context Clearing:** A key part of the process is clearing the chat context *after every plan*. The creator describes keeping a long context window open as "playing Russian roulette," so he prefers to clear context and let the GSD system feed the necessary files back in for the next task 15, 16\.  
* **Sub-Agents:** The system often spawns "sub-agents" to execute the plan autonomously (referred to as "YOLO mode" when auto-approved), reading the plan and writing the code without user hand-holding 14, 17\.  
* **Git Integration:** After a plan is completed, the system automatically commits the changes to Git with a specific message, ensuring a clean history 18, 19\.

### 4\. Verification and Debugging

The process includes specific steps for human review before moving forward.

* **Human Verification:** Some plans invoke a "checkpoint" requiring the user to verify functionality (e.g., "Drive the car and confirm ground tiles appear") 17\.  
* **The Debug Loop:** If a verification fails (e.g., the car turned but didn't move forward), the user inputs the observation using debug \[issue description\]. The system analyzes the issue and proposes a fix ("Fix it now") before marking the task as complete 20-22.  
* **State Updates:** Upon completion, the system updates state.md and roadmap.md to reflect finished tasks and any deviations or decisions made during execution 19, 23\.

### 5\. Completion and Milestones

Once all phases in the roadmap are finished, the system archives the current state to keep the project lightweight.

* **Milestone Completion:** The user runs complete milestone. This moves completed tasks and accumulated context into an archive folder 24, 25\.  
* **Re-initialization:** The system essentially "re-initializes" the project for the next version (V2). It creates a new roadmap based on the archived context, allowing for infinite scalability without cluttering the LLM's context window with old history 26\.

**Analogy:**The GSD process treats the LLM less like a creative writer who needs to remember the whole story, and more like a contractor who is handed a specific blueprint (Plan) and a toolbox (Context) for one room at a time. Once that room is built, the contractor leaves (Context Cleared), and a new contractor is hired for the next room, supplied with fresh blueprints that reference the work already done.  
