# GOC-NOC-optimization

### NOC Design Optimization using Q-Learning

This is to provide the information on:
**1.  How to generate the environment needed to run the Q-learning algorithm for optimizing a Network on Chip (NOC) design, how to run the code, and additional details about the approach, correctness, and complexity**
**2. Design Document for Q-Learning RL Framework for NOC Design Optimization**

### Environment Setup

#### Prerequisites
- Python 3.x
- NumPy

#### Steps to Set Up Environment
1. **Clone the Repository**: 
    ```bash
    git clone <repository-url>
    ```
2. **Navigate to Directory**: 
    ```bash
    cd <repository-name>
    ```
3. **Install Dependencies**: 
    ```bash
    pip install numpy
    ```

### Running the Code

To run the Q-learning algorithm, execute the following command:
```bash
python noc_optimization.py
```

### Simulator APIs

The code uses placeholder functions to simulate the environment. To integrate with an actual simulator, replace the following functions with appropriate APIs:

- `get_buffer_occupancy(buffer_id)`: Returns average buffer occupancy for a given buffer ID.
- `get_arb_rates(agent_type)`: Returns arbitration rates for a given agent type (CPU or IO).
- `get_powerlimit_threshold()`: Returns power limit threshold.

### Algorithm Approach

Q-learning is chosen for NOC design optimization due to its adaptability to dynamic workloads, computational efficiency, and effectiveness in optimizing specific performance metrics like latency and bandwidth. Compared to alternatives like Deep Q-Learning, Policy Gradient Methods, Genetic Algorithms, and Simulated Annealing, Q-learning offers a balanced approach with less computational overhead, making it well-suited for this application.

The Q-learning algorithm is used to optimize the NOC design based on the provided simulation data. The algorithm iteratively learns the optimal policy by updating the Q-values in the Q-table. The states represent buffer size, CPU weight, IO weight, and throttling, while actions include increasing/decreasing buffer size, adjusting CPU/IO weights, and throttling.

### Proof of Correctness

The correctness of the algorithm can be verified by ensuring that it meets the defined optimality criteria:

1. Measured latency <= min_latency
2. Measured bandwidth is at 95% of max_bandwidth
3. Buffer sizing supports 90% occupancy
4. Throttling happens only 5% of the time

The algorithm updates the Q-table based on the rewards obtained from the simulation, aiming to maximize the reward by learning the optimal policy over episodes.

### Complexity Analysis

#### Time Complexity
- **State Discretization**: O(1)
- **Action Selection**: O(1)
- **Reward Calculation**: O(1)
- **Q-table Update**: O(1)

Overall, the time complexity of each episode is O(max_steps).

#### Space Complexity
- **Q-table**: O(100 * 5) = O(500)

The space complexity is linear with the number of states and actions.

### Notes

- Placeholder values for latency, bandwidth, and throttling time can be replaced with actual values from the simulator.
- Hyperparameters and simulation parameters can be changed as needed for our specific environment and requirements.

## Design Document: Q-Learning RL Framework for NOC Design Optimization

### Introduction

This is to present a Q-learning-based RL framework tailored to optimize the Network on Chip (NOC) in a System on Chip (SoC) environment. The framework interacts with a simulator through defined APIs to control buffer size, arbitration weights, and throttling.

### RL Components

#### States/Behaviors

The state space encapsulates the current NOC configuration:

- **Buffer Size**: Number of buffer entries, initialized by `set_max_buffer_size(buffer_id, num_entries)`.
- **CPU Weight**: Weightage for CPU in arbitration, set by `set_arbiter_weights('CPU', weight)`.
- **IO Weight**: Weightage for IO in arbitration, set by `set_arbiter_weights('IO', weight)`.
- **Throttling**: Indicator based on power conditions (0 for off, 1 for on).

#### Actions

The action space defines permissible changes to the NOC configuration:

1. **Increase Buffer Size**: Increment buffer entries using `set_max_buffer_size(buffer_id, num_entries)`.
2. **Decrease Buffer Size**: Decrement buffer entries using `set_max_buffer_size(buffer_id, num_entries)`.
3. **Adjust CPU Weight**: Modify CPU weightage using `set_arbiter_weights('CPU', weight)`.
4. **Adjust IO Weight**: Modify IO weightage using `set_arbiter_weights('IO', weight)`.
5. **Throttle**: Activate throttling using `throttle()`.

#### Rewards

The reward mechanism provides feedback for optimization:

- **Positive Rewards**:
  - Latency <= `min_latency` cycles.
  - Bandwidth >= 95% of `max_bandwidth`.
  - Buffer occupancy >= 90%.
  - Throttling <= 5% of cycles (throttling based on `get_powerlimit_threshold()`).

- **Negative Reward**:
  - Exceeding power limit threshold.

### Optimization Objective

The RL framework aims to iteratively refine the NOC configuration to achieve the following optimality criteria:

- **Latency**: Measured latency should be <= `min_latency`.
- **Bandwidth**: Measured bandwidth should be at 95% of `max_bandwidth`.
- **Buffer Occupancy**: Buffer sizing should support 90% occupancy.
- **Throttling**: Throttling should occur only 5% of the time based on power conditions.

By exploring states and actions, receiving rewards based on performance metrics, and updating Q-values, the algorithm converges towards an optimal policy for NOC design in the SoC environment.
