Based on the sources, GSD is explicitly designed to be suitable for **both greenfield and ongoing ("brownfield") projects**.  
While the video demonstration focuses on building a project from scratch, the documentation provides a specific workflow for integrating GSD into an existing codebase.

### The "Brownfield" Workflow

For an ongoing project, the process differs only in the initialization step. instead of immediately defining a vision, you first teach the system about your existing code.

* **Mapping the Codebase:**You run the command /gsd:map-codebase 1\. This spawns parallel agents to analyze your current project and generates a .planning/codebase/ directory containing seven specific documentation files:  
* STACK.md: Languages and frameworks used.  
* ARCHITECTURE.md: Patterns and data flow.  
* STRUCTURE.md: Directory layout.  
* CONCERNS.md: Technical debt and fragile areas.  
* Plus CONVENTIONS.md, TESTING.md, and INTEGRATIONS.md 1\.  
* **Initialization:**After mapping, you run /gsd:new-project just as you would for a new idea. However, because the system detects the codebase maps, its intake questions automatically shift focus. Instead of asking what you want to build from scratch, it focuses on what you are **adding or changing** 2\.  
* **Context Integration:**Once initialized, the workflow unifies. When you run /gsd:plan-phase, the system automatically loads your specific codebase documentation (STACK.md, CONVENTIONS.md) alongside the project roadmap. This ensures the LLM knows where files live and how to match your existing coding style 2\.

### Creator's Perspective

In the video documentation, the creator confirms that GSD is "100%" suitable for maintenance and improvements 3\. He suggests that for existing projects, you can set the goal of the first few phases to "research into the codebase" to ensure the system aligns with the current standard before attempting to build new features 4\.  
**Analogy:**Using GSD on a greenfield project is like **building a house on an empty lot**—you start with the vision and blueprints.Using GSD on an ongoing project is like **hiring a contractor for a renovation**. Before they can knock down a wall (execute code), they must first survey the house (Map Codebase) to find the load-bearing beams and wiring (ARCHITECTURE.md and STACK.md). GSD automates this survey so the contractor doesn't break your house while trying to improve it.  
