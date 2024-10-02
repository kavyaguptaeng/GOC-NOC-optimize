```markdown
# GOC-NOC-Optimization

## NOC Design Optimization using Q-Learning

This project implements a Q-learning-based Reinforcement Learning (RL) framework to optimize Network on Chip (NOC) configurations within a System on Chip (SoC) environment. The framework dynamically adjusts buffer sizes, arbitration weights, and throttling mechanisms to enhance performance metrics such as latency, bandwidth, buffer occupancy, and power efficiency.

---

## Table of Contents

1. [Environment Setup](#environment-setup)
    - [Prerequisites](#prerequisites)
    - [Steps to Set Up Environment](#steps-to-set-up-environment)
2. [Running the Code](#running-the-code)
3. [Simulator APIs](#simulator-apis)
4. [Algorithm Approach](#algorithm-approach)
5. [Proof of Correctness](#proof-of-correctness)
6. [Complexity Analysis](#complexity-analysis)
7. [Notes](#notes)
8. [Design Document](#design-document)
    - [Introduction](#introduction)
    - [RL Components](#rl-components)
        - [States/Behaviors](#statesbehaviors)
        - [Actions](#actions)
        - [Rewards](#rewards)
    - [Optimization Objective](#optimization-objective)
    - [Additional Considerations](#additional-considerations)
9. [Repository Structure and Usage](#repository-structure-and-usage)
10. [References](#references)
11. [License](#license)

---

## Environment Setup

### Prerequisites

- **Operating System:** Linux, macOS, or Windows with Python 3.x support.
- **Python Version:** Python 3.7 or higher.
- **Dependencies:**
  - [NumPy](https://numpy.org/)

### Steps to Set Up Environment

1. **Clone the Repository:**

    ```bash
    git clone <repository-url>
    ```

2. **Navigate to the Directory:**

    ```bash
    cd <repository-name>
    ```

3. **Create a Virtual Environment (Optional but Recommended):**

    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

4. **Install Dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

    *If `requirements.txt` is not provided, install NumPy directly:*

    ```bash
    pip install numpy
    ```

---

## Running the Code

To execute the Q-learning algorithm for NOC design optimization, run the following command:

```bash
python noc_optimization.py
```

This command initiates the training process, where the agent interacts with the environment to learn optimal NOC configurations over a specified number of episodes.

---

## Simulator APIs

The current implementation uses placeholder functions to simulate interactions with the NOC environment. To integrate with an actual simulator, replace the following functions in the `NOCOptimizer` class with appropriate API calls:

- **`get_buffer_occupancy(buffer_id)`**
  - **Purpose:** Retrieve the average buffer occupancy for a specified buffer ID.
  - **Expected Return:** Float value representing occupancy percentage.

- **`get_arb_rates(agent_type)`**
  - **Purpose:** Obtain arbitration rates for a given agent type (`'CPU'` or `'IO'`).
  - **Expected Return:** Float value indicating arbitration rate.

- **`get_powerlimit_threshold()`**
  - **Purpose:** Fetch the current power limit threshold status.
  - **Expected Return:** Integer (`0` or `1`) indicating whether the power limit has been reached.

- **`set_max_buffer_size(buffer_id, num_entries)`**
  - **Purpose:** Set the maximum number of buffer entries for a specified buffer.
  - **Parameters:**
    - `buffer_id`: Identifier for the buffer.
    - `num_entries`: Integer specifying the new buffer size.

- **`set_arbiter_weights(agent_type, weight)`**
  - **Purpose:** Adjust the arbitration weight for a specified agent type.
  - **Parameters:**
    - `agent_type`: `'CPU'` or `'IO'`.
    - `weight`: Float value representing the new weight.

- **`throttle()`**
  - **Purpose:** Activate throttling mechanisms based on power conditions.

---

## Algorithm Approach

**Q-learning** is selected for NOC design optimization due to its adaptability to dynamic workloads, computational efficiency, and effectiveness in optimizing specific performance metrics like latency and bandwidth. Compared to alternatives such as Deep Q-Learning, Policy Gradient Methods, Genetic Algorithms, and Simulated Annealing, Q-learning offers a balanced approach with lower computational overhead, making it well-suited for this application.

The Q-learning algorithm optimizes the NOC design based on simulation data by iteratively learning the optimal policy through Q-table updates. The state space comprises buffer size, CPU weight, IO weight, and throttling status, while the action space includes increasing/decreasing buffer size, adjusting CPU/IO weights, and throttling.

---

## Proof of Correctness

The algorithm's correctness is validated by ensuring it meets the following optimality criteria:

1. **Measured Latency:** ≤ `min_latency`
2. **Measured Bandwidth:** ≥ 95% of `max_bandwidth`
3. **Buffer Occupancy:** ≥ 90%
4. **Throttling Frequency:** ≤ 5% of the time

The Q-table updates are based on rewards derived from these metrics, guiding the agent to configurations that satisfy all constraints. By maximizing cumulative rewards, the agent converges towards an optimal policy that consistently meets the defined performance metrics.

---

## Complexity Analysis

### Time Complexity

- **State Discretization:** O(1)
- **Action Selection:** O(1)
- **Reward Calculation:** O(1)
- **Q-table Update:** O(1)

**Overall Time Complexity per Episode:** O(max_steps)

### Space Complexity

- **Q-table:** O(total_states * num_actions)
  - For 800 states and 5 actions: O(4000)

The space complexity is linear with respect to the number of states and actions, making it scalable for moderate state and action spaces.

---

## Notes

- **Placeholder Values:** Replace placeholder values for latency, bandwidth, and throttling time with actual values from the simulator for accurate optimization.
- **Hyperparameters:** Adjust hyperparameters (e.g., learning rate `alpha`, discount factor `gamma`, exploration rate `epsilon`) and simulation parameters as needed to suit specific environments and requirements.
- **State and Action Spaces:** Ensure that state discretization and action definitions align with the simulator's capabilities and the desired granularity of control.

---

## Design Document: Q-Learning RL Framework for NOC Design Optimization

### Introduction

This document outlines a Q-learning-based Reinforcement Learning (RL) framework designed to optimize Network on Chip (NOC) configurations within a System on Chip (SoC) environment. The framework interacts with a simulator through defined APIs to adjust buffer sizes, arbitration weights, and throttling mechanisms, aiming to enhance performance metrics such as latency, bandwidth, buffer occupancy, and power efficiency.

### RL Components

#### States/Behaviors

The state space encapsulates the current NOC configuration, comprising:

- **Buffer Size:** Number of buffer entries, managed via `set_max_buffer_size(buffer_id, num_entries)`.
- **CPU Weight:** Arbitration weight for CPU, adjusted using `set_arbiter_weights('CPU', weight)`.
- **IO Weight:** Arbitration weight for IO, adjusted using `set_arbiter_weights('IO', weight)`.
- **Throttling:** Indicator based on power conditions (`0` for off, `1` for on).

#### Actions

The action space defines permissible changes to the NOC configuration:

1. **Increase Buffer Size:** Increment buffer entries using `set_max_buffer_size(buffer_id, num_entries)`.
2. **Decrease Buffer Size:** Decrement buffer entries using `set_max_buffer_size(buffer_id, num_entries)`.
3. **Adjust CPU Weight:** Modify CPU arbitration weight using `set_arbiter_weights('CPU', weight)`.
4. **Adjust IO Weight:** Modify IO arbitration weight using `set_arbiter_weights('IO', weight)`.
5. **Throttle:** Activate throttling using `throttle()` based on power conditions.

#### Rewards

The reward mechanism provides feedback to guide optimization:

- **Positive Rewards:**
  - Latency ≤ `min_latency` cycles.
  - Bandwidth ≥ 95% of `max_bandwidth`.
  - Buffer occupancy ≥ 90%.
  - Throttling time ≤ 5% of total cycles.

- **Negative Reward:**
  - Exceeding power limit threshold.

### Optimization Objective

The RL framework aims to iteratively refine the NOC configuration to achieve the following optimality criteria:

- **Latency:** Measured latency should be ≤ `min_latency`.
- **Bandwidth:** Measured bandwidth should be ≥ 95% of `max_bandwidth`.
- **Buffer Occupancy:** Buffer sizing should support ≥ 90% occupancy.
- **Throttling Frequency:** Throttling should occur only ≤ 5% of the time based on power conditions.

By exploring various states and actions, receiving rewards based on performance metrics, and updating Q-values, the algorithm converges towards an optimal policy for NOC design in the SoC environment.

### Additional Considerations

#### Exploration Strategy

An ε-greedy policy is employed, with ε decaying over time to balance exploration and exploitation. This ensures that the agent explores sufficient state-action pairs during early training while exploiting learned policies in later stages.

#### Scalability

- **State Space Management:** With a manageable state space of 800 states, a Q-table is feasible. For larger state spaces, consider using function approximators like neural networks (Deep Q-Networks).

#### Performance Monitoring

Implement monitoring tools to track:

- **Cumulative Rewards per Episode:** To assess learning progress.
- **Q-Value Convergence:** To ensure stability in learning.
- **Performance Metrics:** To validate that optimality criteria are met.

#### Hyperparameter Tuning

Adjust hyperparameters such as learning rate (`alpha`), discount factor (`gamma`), and exploration rate (`epsilon`) based on observed performance to achieve faster convergence and better policy optimization.

---

## Repository Structure and Usage

### Repository Structure

```
goc-noc-optimization/
├── noc_optimization.py
├── README.md
├── requirements.txt
├── q_table.npy  # Generated after training
└── LICENSE
```

### Usage Instructions

1. **Clone the Repository:**

    ```bash
    git clone <repository-url>
    ```

2. **Navigate to the Directory:**

    ```bash
    cd goc-noc-optimization
    ```

3. **Set Up Virtual Environment (Optional):**

    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

4. **Install Dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

    *If `requirements.txt` is not provided, install NumPy directly:*

    ```bash
    pip install numpy
    ```

5. **Run the Optimizer:**

    ```bash
    python noc_optimization.py
    ```

6. **Post-Training:**

    - The Q-table will be saved as `q_table.npy`.
    - Integrate the trained Q-table into the simulator for deployment.

### Extending the Framework

To integrate with an actual NOC simulator:

1. **Implement Simulator APIs:**
   - Replace the placeholder methods in the `NOCOptimizer` class with actual API calls to the simulator.

2. **Modify Reward Calculation:**
   - Adjust the `calculate_reward` method to incorporate real-time metrics from the simulator.

3. **Expand State and Action Spaces:**
   - If necessary, increase the granularity of discretization or add new actions to cover additional configuration parameters.

4. **Optimize Hyperparameters:**
   - Fine-tune learning rate, discount factor, and exploration parameters based on simulator feedback.



**Note:** Replace `<repository-url>` and `<repository-name>` with your actual repository URL and name respectively. Ensure that you have a `requirements.txt` file in your repository with the necessary dependencies listed (e.g., `numpy`). If not, you can install dependencies directly as shown in the steps.

Feel free to customize the repository URL, adjust configuration parameters, and expand upon the simulator integration as per your specific requirements and the capabilities of your NOC simulator.
```
