# Skill: `save-checkpoint`

## Description
Saves the current running state of the experiment—including the latest metrics, hyperparameter configurations, pending brainstormed ideas, and the current notebook phase—into a dedicated checkpoint file. This allows the human and the agent to safely pause, review, and resume the optimization loop at a later time without losing context.

## Trigger
Execute this skill when:
* The human explicitly requests to save, pause, stop, or checkpoint the current progress.
* The 24-hour time limit is approaching and the current state needs to be preserved before shutdown.
* A major milestone (like finishing Phase 1 or completing a long-running epoch) is reached.

## Execution Steps

1. **Check for Filename:** Determine if the human provided a specific file name for the checkpoint (e.g., `checkpoint_epoch3.json` or `state_v2.md`).
2. **Ask if Missing:** If no file name was provided, **STOP** and ask the human: 
   > *"I am ready to save the current state. What file name would you like me to use for this checkpoint?"*
3. **Compile State Data:** Once the filename is confirmed, gather the following information:
   * Current Phase (e.g., Phase 3: Optimization Loop).
   * Last executed notebook version.
   * Best metrics achieved so far (from `RESULTS.md`).
   * Pending hypotheses or planned hyperparameter changes for the next run.
4. **Write to File:** Save the compiled data to the specified file.
5. **Confirm:** Notify the human that the checkpoint has been successfully saved and the workspace is safe to close.