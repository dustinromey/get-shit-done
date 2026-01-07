Based on the sources, a typical coding cycle in the "Get Shit Done" (GSD) system follows a rigorous, linear loop designed to maximize the efficiency of the Large Language Model's (LLM) context window.  
Here is the step-by-step workflow:

### 1\. Contextualization (Discuss Phase)

Before asking the system to plan code, the user establishes the specific requirements for the upcoming phase.

* **Command:** The user runs discuss phase \[x\] (e.g., discuss phase 2 for the procedural city) 1, 2\.  
* **Intake:** The system asks targeted questions to clarify the vision, such as "What type of districts should the city include?" or "What are the performance priorities?" 3\.  
* **Context File:** The system scans the current codebase and generates a context.md file specific to that phase (e.g., 02\_context.md). This ensures the planner understands the existing code structure 4, 5\.

### 2\. Planning (Meta-Prompting)

Instead of jumping into code, the system generates a series of structured "Plans."

* **Command:** The user runs plan phase \[x\] 6\.  
* **Plan Generation:** The system reads the project state, roadmap, and the newly created phase context to generate specific plans 7\.  
* **The "Rule of Three":** Crucially, the system never creates more than three tasks per plan. This ensures the work can be completed within roughly 50% of a standard context window, preventing the LLM from degrading in quality or running out of space 7\.

### 3\. Context Hygiene (The "Fresh Window" Protocol)

A defining feature of this cycle is the strict management of the chat context.

* **Clearing Context:** The user explicitly clears the chat context (resets the LLM memory) *between* plans 1, 8\.  
* **Rationale:** The creator describes keeping a long context window open as "playing Russian roulette." By clearing the context, he ensures every task starts with a "fresh brain," preventing the model from getting confused by previous, irrelevant conversational history 9, 10\.

### 4\. Execution ("YOLO Mode")

The user executes the generated plans one by one in fresh windows.

* **Command:** The user runs execute plan \[x\] (e.g., execute plan 02-01) 11, 12\.  
* **Sub-Agents:** The system often spawns a "sub-agent" (autonomous instance) to read the plan and write the code without user intervention. This is often referred to as "YOLO mode" when auto-approved 13, 12\.  
* **Context Loading:** Because the chat window was cleared, the execute command automatically feeds the necessary files, project state, and specific plan details back into the LLM so it has exactly what it needs and nothing more 11\.

### 5\. Verification and Debugging

The cycle includes a mandatory stop for human review.

* **Checkpoint:** Many plans include a "verify" step where the agent pauses and asks the user to confirm functionality (e.g., "Drive the car and confirm ground tiles appear") 14\.  
* **The Debug Loop:** If the verification fails (e.g., "The car turns but doesn't move"), the user does not try to fix it manually. Instead, they run debug \[description of issue\] 15\.  
* **Fix Implementation:** The system analyzes the error, proposes a fix ("Fix it now"), and the user approves it before moving on 16, 17\.

### 6\. Completion and State Update

Once a plan is verified and complete:

* **Summary & Commits:** The system generates a summary of the work, updates the state.md file (the project's "core memory"), and automatically commits the changes to Git with a specific message 18, 19\.  
* **Loop:** The user copies the command for the *next* plan, clears the context again, and repeats the cycle 20\.

**Analogy:**This coding cycle is like **shooting a movie scene-by-scene**.Instead of trying to film the entire movie in one continuous take (which would exhaust the actors and lead to mistakes), the director (User) discusses the specific scene (Context), hands the actors the script for *only* that scene (Plan), shoots it (Execute), reviews the footage (Verification), and then clears the set completely (Clear Context) to build the stage for the next scene. The state.md file acts as the production schedule, ensuring the continuity of the movie is maintained even though the sets are constantly being built and torn down.  
