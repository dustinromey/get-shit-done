Based on the sources, particularly the creator's explicit comparison in the video and the documentation for both repositories, here is how **Get Shit Done (GSD)** differs from the **Tâches Claude Code Resources (CC Resources)** you are familiar with.

### 1\. Philosophy: Toolkit vs. Operating System

The most fundamental difference is the scope of control.

* **CC Resources (The Toolkit):** This is a collection of modular tools (skills, commands, prompts) that you pull off the shelf when needed. You might use /create-prompt for one task and /debug for another, but *you* remain the project manager deciding what happens next 1, 2\.  
* **GSD (The Operating System):** GSD is an end-to-end workflow that manages the project for you. The creator states he "stopped using CC Resources completely" because while it was great for "one-shotting" tasks, it lacked a "big picture understanding" for building a product from scratch to deployment 3\. GSD imposes a strict structure (Roadmap → Phase → Plan → Execution) to prevent aimless "vibe-coding" 4, 5\.

### 2\. Context Management Strategy

While CC Resources offers tools to help with context (like /whats-next for handoffs), GSD automates context rigor to an extreme degree.

* **CC Resources:** You generally stay in a chat session until it degrades, or manually create handoff documents when you feel the context window filling up 6\.  
* **GSD ("Fresh Brain" Protocol):** GSD assumes the context window is a liability ("Russian Roulette") 7\. It enforces a protocol where you **clear the chat context after every single task**. Because the system maintains external memory files (project.md, state.md), it automatically re-feeds the strictly necessary context back into the sub-agent for the next task. You never have to worry about "context hygiene" because the system enforces it 7, 8\.

### 3\. The "Core Memory" (STATE.MD)

This is a feature unique to GSD that does not exist in CC Resources.

* **CC Resources:** Context is mostly held in the chat history or loose markdown files you create manually.  
* **GSD:** The system maintains a state.md file that the creator calls "the killer" feature 9\. It acts as the project's persistent memory, tracking decisions (e.g., "We used TypeScript"), progress (e.g., "Phase 1 complete"), and deferred issues across sessions. This allows the AI to "remember" the project vision even after you wipe the chat history 9, 10\.

### 4\. Workflow Mechanics

* **Planning:**  
* **CC Resources:** You might use /create-plan to generate a markdown file, but you are responsible for executing it 11\.  
* **GSD:** You run gsd:plan-phase, and the system generates structured XML plans. Crucially, GSD enforces a **"Rule of Three"**: no plan ever exceeds three tasks, ensuring the AI never "cuts corners" due to token limits 8, 12\.  
* **Execution (YOLO Mode):**  
* **CC Resources:** You execute tasks manually or via standard sub-agents.  
* **GSD:** Uses "YOLO mode" (auto-approved sub-agents) that read the specific plan, execute the code, and verify it autonomously. The user often just approves the result 13, 14\.  
* **Scalability:**  
* **CC Resources:** Struggles as files get large.  
* **GSD:** Includes a complete-milestone command. When V1 is done, it archives the entire history and state, "re-initializing" the project for V2. This allows for infinite project scaling without context bloat 15, 16\.

### 5\. Technical Implementation

* **Installation:** CC Resources is a plugin or a set of files you copy into your \~/.claude/ directory 2\. GSD is an executable CLI tool (npx get-shit-done-cc) that runs *on top* of Claude Code 17\.  
* **Git Integration:** GSD has atomic git commits built-in. Every time a sub-agent finishes a task, it commits the specific files with a specific message, keeping the history clean automatically 18, 19\.

### Summary Table

Feature,Tâches CC Resources,Get Shit Done (GSD)  
Primary Goal,Enhance Claude with specific tools.,Enforce a reliable development workflow.  
User Role,You manage the process; AI helps.,AI manages the process; you direct/verify.  
Context,Manual handoffs (/whats-next).,Automated state tracking (state.md).  
Session Mgmt,Long sessions (risk of degradation).,"One task per session (always ""fresh"")."  
Planning,"Flexible, user-driven.","Strict XML specs, max 3 tasks per plan."  
Commitment,Low (pick and choose tools).,High (must follow the GSD lifecycle).  
**Analogy:CC Resources** is like having a high-quality toolbelt. You have a specialized hammer, a laser measure, and a level. You are the master carpenter; you decide when to pull out the laser measure and when to use the hammer.  
**GSD** is like acting as the General Contractor. You don't swing the hammer. You hand a blueprint (Plan) to a subcontractor (Sub-agent), they go into a room, finish the job, clean up, and leave. Then you hire a *new* subcontractor for the next room, handing them the updated blueprints. The state.md file is the clipboard you hold that ensures the plumber knows what the electrician just did.  
