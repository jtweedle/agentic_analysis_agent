# Agentic Analysis Agent

## Table of Contents

1. [Purpose](#purpose)
    - Objective 
    - Canonical Question
    - Definition of Done
    - Agent State
    - Decision Point
    - Reasoning Loop
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

- **Agent Reasoning Loop:** 
    1. State Observation
        - What does the agent "see" at any given moment?
        - What knowledge about the dataset or question is available?
        - What partial outputs, metrics, or intermiediate results exist?
        - Write 3-5 bullets describing the information your agent will always have in its state before making a decision
            1. metadata about the dataset
            2. completed and remaining tasks
            3. current plan
    2. Decision / Action Selection
        - Based on the current state, what choices does the agent have?
        - How does it prioritize between exploring more data vs. starting an analysis?
        - What signals or rules guide these choices?
        - List the possible actions, then write one sentence for each explaining when it would choose that action. 
            - Ask clarifying question(s)
                1. When the requested result doesn't align with or isn't achievable given the current state of the data e.g asking for an average on a dataset of words
            - Explore the data
                1. descriptive and inferential statictics don't exist 
            - Conduct tests
                1. descriptive and inferential statistics exist
            - Provide an answer
                1. has developed visualizations, answers and reasoning for tests, and an narrative explanation
    3. Action Execution
        - What happens when the agent takes an action?
        - How is the new information generated, and where does it go in the agent's state?
        - How does the agent know the action "succeeded" or "produced useful results"?
        - For each action from step 2, briefly describe the outcome or update to the the state
            1. Clarifying questions
                - waits for user inputs and updates state based on that
            2. Explore the data
                - computes relevant descriptive and inferential statitics
            3. Conduct tests
                - Creates necessary / relevant tests to answer question, is able to achieve a result per test, and can create visuals for test outcomes
            4. Provide answer
                - narrative explanation is available along with supporting test outcomes and visuals and no new information
    4. State Update / Reflection
        - How does the agent record progress?
        - How does it track what has been tried vs. what remains?
        - Does it revise its strategy based on new information?
        - Sketch a simple note of "what the agent remembers" after each iteration
            - After each iteration, the agent remembers any user input and whether that affects the previous/ next state, what the previous step was and what the next step is going to be, and which questions are unresolved
    5. Stopping Check
        - How does the agent determine it's done?
        - What signals indicate a complete, defensible answer?
        - How does it avoid going in circles?
        - Write 1-2 bullets describing your stopping criteria in conceptual terms
            - User is prompted and affirms question has been answered
            - Avoids circles by asking user when to stop
            - A complete defensible answer is determined by relevant statistical signals e.g. p-value
            - no new insights provided in last iteration
- **Decision / Action Selection**
    1. Preconditions for Each Action (Explicit Gating Logic)

        Clarifying Questions is selected when ambiguity exists in the current state that prevents valid inference.
        This includes:
        - Undefined or ambiguous target variable
        - Missing variable definitions or units
        - Contradictory constraints
        - Dataset structure misaligned with the requested analysis
        - Insufficient information to construct a defensible hypothesis

        Explore the Data is selected when the agent lacks sufficient structural understanding of the dataset to defensibly construct, evaluate, or validate a hypothesis.
        This includes:
        - No descriptive statistics computed
        - No distributional understanding
        - No variable relationship assessment
        - Test assumptions not yet evaluated
        - Anomalies or unexpected results requiring further investigation

        Statistical Tests is selected only when:
        - A clearly defined hypothesis exists
        - Relevant variables have been validated
        - Descriptive statistics are available
        - Statistical assumptions for candidate tests have been evaluated
        - The agent has identified a test appropriate to the data structure and research objective

        Provide Answer is selected only when:
        - The original question has a clearly defined objective or hypothesis
        - Evidence has been generated that directly addresses the objective
        - No unresolved ambiguities remain
        - Statistical results and visualizations support a defensible conclusion
        - Additional iterations are unlikely to materially change the interpretation

    2. Priority Ordering (Hierarchy of Actions)

        i. Clarifying Questions (resolve ambiguity first)

        ii. Explore the Data (build structural understanding)

        iii. Conduct Statistical Tests (generate formal evidence)

        iv. Provide Answer (synthesize and conclude)

        **Note**: When multiple actions are valid, the agent selects the one that most reduces uncertainty relative to the user’s objective.

    3. Non-Progress Rule (Reflection Trigger)

        If an iteration produces no meaningful reduction in uncertainty or explanatory power, the agent must:
        - Re-evaluate the hypothesis formulation
        - Re-check statistical assumptions
        - Assess whether the selected analysis method is appropriate
        - Determine whether the question is answerable with available data

        The agent only proceeds to provide an answer if further uncertainty reduction is not possible given the current data.

    4. Failure Mode Analysis (Primary Risk and Safeguard)

        Primary Failure Mode: Providing an answer prematurely before sufficient evidence exists.

        Safeguard Mechanism:
        Before providing an answer, the agent performs an internal logical audit:
        - Have all required variables been validated?
        - Have test assumptions been checked?
        - Is the conclusion robust to anomalies or outliers?
        - Would additional exploration meaningfully alter the interpretation?

        If any audit check fails, the agent returns to exploration or hypothesis refinement rather than terminating.

- **Tooling Architecture**
    - Requirements
        1. Tool Name
        2. Tool purpose
        3. Inputs
        4. Outputs
        5. Triggers
    - Tool boundaries
        - Tools cannot call other tools
        - Tools cannot decide when to run
        - Only the reasoning loop controls sequencing
    1. Read data
        - data reader
        - read user-provided dataset
        - Most common dataset types e.g. .csv, .txt, .parquet
        - standard data science dataframe e.g. pandas dataframe
        - User provides / uploads a data file
    2. Pre-process data
        - data preprocessor
        - handles missing values, data type conversions, etc.
        - data reader output i.e. a pandas dataframe
        - cleaned, analysis-ready dataframe
        - A new dataframe becomes available
    3. Compute Summary Metrics
        - data explorer
        - computes all summary metrics including columns counts, median, mean, standard deviation, distrubitions, data types, and variable relationships
        - cleaned, analysis ready dataframe
        - EDA summary tables including: descriptive statistics, distribution summaries, variable types, and relationship metrics
        - a new cleaned, analysis-ready dataframe becomes available
    4. Statistical Planner
        - statistical planner
        - create a hypothesis based on user question and dataset context along with the needed test(s) to accept / reject the hypothesis
        - user question, cleaned analysis-ready dataset, summary tables from explorer
        - a hypothesis along with needed test(s) to accept / reject
        - all previous tools have created their outputs(data reader, processor, describer, understander) and user has submitted a question
    6. Run statiscal tests
        - statistical tester
        - run and compute the output of a hypothesis test set
        - hypothesis and statistical tests and cleaned, analysis-ready dataframe
        - test results for the hypothesis set including: test statistic, p-value, confidence intervals, and other relevant metrics
        - a new hypothesis becomes available that hasn't been tested yet
    7. Evaluate evidence
        - evidence evaluator
        - decides if there is enough evidence to provide the narrative answer
        - every currently available hypothesis along with its test results
        - decision: CONTINUE_ANALYSIS or FINALIZE_ANSWER
        - a new test result has become available
    8. Create plots
        - visualizer
        - produces relevant plot(s) to help with interpration
        - EDA outputs, statistical test results, and cleaned dataset
        - relevant plots, charts, graphs, etc. 
        - Evaluator's decision is FINALIZE_ANSWER
    9. Narrative explanation
        - explainer 
        - converts numerical inputs into a plain engilsh output that is inutive and explains the final answer
        - full hypothesis and statiscal test set, computed statisical metrics from statitical tester, visualizations, and user question
        - a paragraph summarizing the evidence and conclusion i.e. a statisically-defensible narrative answer to the user question
        - Evaluator's decision is FINALIZE_ANSWER
        