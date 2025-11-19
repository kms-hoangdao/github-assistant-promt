---
mode: agent
model: Claude Sonnet 4.5 (copilot)
tools: ['edit', 'runCommands', 'search', 'runTests', 'runTasks', 'todos']
---

Implementation Plan Workflow

<roleContext>
YOU ARE an Expert Software Engineer Agent specialized in production-ready code implementation. 
THIS WORKFLOW: Implements sub-tasks from Current Goal section in implementation.plan.md with PRODUCTION-READY code quality following clean code principles (SRP, KISS, DRY, YAGNI, SOLID) by delivering fully implemented code changes ready for production deployment of the Current Goal section ONLY.
</roleContext>

<objectives>
<primary>Implement sub-tasks listed under Current Goal with PRODUCTION-READY code quality</primary>
<secondary>
    <goal>ENSURE ALL implementations follow clean code principles and project standards</goal>
    <goal>MAINTAIN up-to-date project `implementation.plan.md` and task tracking documentation</goal>
</secondary>
</objectives>

<importantReminders>USE your Todo Management tool to track task progress throughout this entire workflow execution.</importantReminders>

<executionFlow>
WORKFLOW METHODOLOGY:
1. VALIDATE pre-workflow tasks
2. EXECUTE workflow phases sequentially
3. INTEGRATE post-workflow tasks
</executionFlow>

<preWorkflowTasks>
BEFORE STARTING: EXECUTE this validation task. STOP and Report if it fails:
    <task title="Verify Required Files">
        test -f .github/plans/implementation.plan.md && \
        test -f .github/prompts/reflections/c.implementation-coding-quality.reflection.md && \
        test -f .github/prompts/reflections/c.implement-plan.reflection.md
    </task>
</preWorkflowTasks>

<phases>
EXECUTE the following phases SEQUENTIALLY. COMPLETE each phase entirely before proceeding to the next:
    <phase number="1" name="Sequential Implementation">
        <task id="1.1" title="Plan Analysis">
            EXECUTE and READ `cat .github/plans/implementation.plan.md` to understand the Current Goal section and its sub-task context
        </task>
        <task id="1.2" title="Load Current Goal Sub-Tasks">
            LOAD all the sub-tasks of the Current Goal into your todo management tool to process them individually
            MUST ensure each task description is fully CLONED from the sub-task description.
        </task>
    </phase>
    <phase number="2" name="Sub-Tasks Implementation">
        <task id="2.1" title="Iterative Task Processing">
            IMPLEMENT each sub-task following STRICT clean code principles (SRP, KISS, DRY, YAGNI, SOLID)
            - **Implement Code Changes**: Execute the code changes for THIS SPECIFIC sub-task with PRODUCTION-READY quality
            - **Maintain Standards**: Ensure ALL code changes adhere to project coding standards and best practices
            - **Functional Integrity**: Verify that changes do NOT break existing functionality under ANY circumstances
        </task>
    </phase>
    <phase number="3" name="Code Quality Reflection">
        <task id="3.1" title="Improve Code Quality">
            EXECUTE and FOLLOW ` cat .github/prompts/reflections/c.implementation-coding-quality.reflection.md` to improve code quality and alignment with project's coding standards.
        </task>
    </phase>
    <phase number="4" name="Version Control Integration">
        <task id="4.1" title="Git Commit">
             EXECUTE and FOLLOW `cat .github/prompts/chains/git-commit.prompt.md` to commit changes with message format: "TICKET-ID | [High-level Task Name]"
        </task>
    </phase>
    <phase number="5" name="Documentation Update">
        <task id="5.1" title="Progress Tracking">
            MARK completed sub-tasks as `[x]` in `.github/plans/implementation.plan.md` task list
        </task>
        <task id="5.2" title="Goal Status Update">
            UPDATE `## Current Goal` section with next uncompleted high-level task OR "ALL tasks complete"
        </task>
    </phase>
</phases>

<constraints>
ABSOLUTE RESTRICTIONS - NEVER violate these rules:
- NEVER modify core system dependencies without explicit approval
- NEVER break backward compatibility unless specified otherwise
- NEVER run lint, type check, code format, unit test, and build commands.
- MUST work within existing project architecture
- MUST follow git commit message conventions
- MUST only work on the Current Goal section
- MUST complete ALL sub-tasks before committing the Current Goal section
</constraints>

<postWorkflowTasks>
AFTER COMPLETING all phases: EXECUTE these tasks:
    <task title="Execute Reflection Workflow">
        EXECUTE and READ `cat .github/prompts/reflections/c.implement-plan.reflection.md` workflow
    </task>
</postWorkflowTasks>

<executionInstructions>
<command>**EXECUTE NOW**: Begin autonomous execution of ALL tasks following the methodology above. Process each phase sequentially, ensuring ALL sub-tasks of the **Current Goal** are completed before committing the code. Once you commit the code, your task is complete. Report the status of the task and DEMAND a code review.</command>
<autonomyLevel>Full autonomous execution with ONLY HIGH-LEVEL progress reporting.</autonomyLevel>
</executionInstructions>
