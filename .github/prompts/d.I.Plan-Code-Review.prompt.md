---
mode: agent
model: Claude Sonnet 4.5 (copilot)
tools: ['edit', 'runCommands', 'search', 'runTests', 'runTasks', 'todos']
---

Plan Self-Review Workflow

<roleContext>
THIS WORKFLOW: Executes the appropriate self-review planning workflow based on user input to determine whether to run coding or testing self-review processes. If the user input is unclear, asks whether to run the coding or testing self-review workflow.
</roleContext>

<objectives>
<primary>Strictly execute the correct self-review planning workflow based on user input.</primary>
</objectives>

<executionFlow>
WORKFLOW METHODOLOGY:
1. VALIDATE pre-workflow tasks
2. EXECUTE workflow phases sequentially
3. INTEGRATE post-workflow tasks
</executionFlow>

<preWorkflowTasks>
BEFORE STARTING: EXECUTE these validation and setup tasks in sequence. STOP and Report if any task fails:
    <task title="Validate Required Files">
        test -f .github/prompts/chains/d.Plan-Code-Review-Coding.prompt.md && \
        test -f .github/prompts/chains/d.Plan-Code-Review-Testing.prompt.md
    </task>
</preWorkflowTasks>

<phases>
EXECUTE the following phases SEQUENTIALLY. COMPLETE each phase entirely before proceeding to the next:
    <phase number="1" name="Understand Workflow To Execute">
        <task id="1.1" title="Identify Next Workflow">
            Review the user message to determine whether to run the coding or testing self-review workflow.
            If the information is missing, ask the user which self-review workflow to execute.
        </task>
    </phase>
    <phase number="2" name="Execute Self-Review Workflow">
        <task id="2.1" title="Execute the Indicated Self-Review Workflow">
            Follow the workflow instructions precisely based on the user's selection:
            - For the coding self-review workflow, run: `cat .github/prompts/chains/d.Plan-Code-Review-Coding.prompt.md`
            - For the testing self-review workflow, run: `cat .github/prompts/chains/d.Plan-Code-Review-Testing.prompt.md`
        </task>
    </phase>
</phases>

<postWorkflowTasks>
AFTER COMPLETING all phases: EXECUTE these tasks:
    <task title="Report Involved Self-Review Workflow Result">
        Summarize the outcomes of the self-review workflow.
    </task>
</postWorkflowTasks>

<constraints>
ABSOLUTE RESTRICTIONS - NEVER violate these rules:
- **ONLY EXECUTE SUPPORTED WORKFLOWS**: If the user requests an unsupported workflow, state that only coding and testing self-review workflows are supported.
</constraints>

<executionInstructions>
<command>**EXECUTE NOW**: Begin autonomous execution of ALL tasks following the methodology above.</command>
<autonomyLevel>Full autonomous execution with ONLY HIGH-LEVEL progress reporting.</autonomyLevel>
</executionInstructions>
