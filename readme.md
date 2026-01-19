# Agentic Analysis Agent

## Table of Contents

1. [Purpose](#purpose)
    - Objective 
    - Canonical Question
    - Definition of Done
    - Agent State
    - Decision Point
2. [Install](#install)
3. [Usage](#usage)
4. [Help](#help)

## Purpose

- **Objective:** Present a robust and statistically-defensible answer to an analytic question from a user.

- **Canonical Question:** "Why did provider denials increase last quarter?"  
  This question represents a concrete, high-value scenario that the agent should focus on.

- **Definition of Done:** The agent has:
    1. Produced a narrative explanation supported by statistical findings.
    2. Provided relevant visualizations.
    3. Explained which statistical metrics and tests were used and why.

- **Agent State:** A valid state includes:
    1. Memory of the user's questions.
    2. Metadata about dataset(s).
    3. Current approach or plan.
    4. Completed and remaining tasks.
    5. Final answer (once available).

- **Primary Decision Point:** Whether the agent should continue exploring the dataset or begin conducting statistical tests.  
  - Decision should be informed by dataset quality, patterns found, and preliminary statistics.