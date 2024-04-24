# Aakriti_google_girl_hackathon
This repository provides my solution to problem statement for Google Girl Hackathon in Silicon role.
 
# Problem Statement: Optimizing Network-on-Chip (NoC) Performance for Target Workloads

# Context:

We have a System-on-Chip (SoC) design with a Network-on-Chip (NoC) for communication between CPU, IO peripherals, and System memory (refer to Fig 1.0).
The NoC performance is crucial for overall system performance, impacting factors like:
Latency (time to retrieve data from memory)
Bandwidth (amount of data transferred per unit time)
Power consumption
Buffer occupancy

# Challenge:
The challenge is to find the optimal NoC configuration for a given set of target workloads running on the CPU and IO peripherals. This configuration should achieve a balance between the following goals:

Minimizing Latency: Data retrieval from memory should be as fast as possible.
Maximizing Bandwidth: The NoC should efficiently handle data transfer between components.
Maintaining Power Efficiency: The NoC configuration should minimize power consumption without sacrificing performance.
Avoiding Buffer Overflow: Buffers within the NoC should not become overloaded.
Adaptability to Workloads: The optimal configuration should be adaptable to different workload types to maintain performance across various scenarios.
Constraints:

The NoC design has limitations on buffer sizes and arbitration weight adjustments.
The simulator provides limited information about the workloads (e.g., only Read/Write access patterns).

# Objective:

Develop a method to automatically derive the optimal NoC configuration for the target workloads. This configuration should satisfy the following criteria:

Measured latency is less than or equal to a predefined minimum latency threshold.
Measured bandwidth is at least 95% of the maximum achievable bandwidth.
Average buffer occupancy is maintained around 90% to avoid overflow and underutilization.
Throttling (reducing operating frequency due to power constraints) occurs less than 5% of the time.

# TASKS:


# 1. Write pseudocode to measure average latency and bandwidth using the simulator provided monitor output (as shown in Table 1.0). The pseudocode needs to be efficient and robust.
This pseudocode efficiently calculates the average latency and bandwidth by summing up the values from the monitor output and dividing by the total number of cycles.

Initialize latency_sum = 0
Initialize bandwidth_sum = 0
Initialize total_cycles = 0

While simulation is running:
    If monitor output available:
        Update latency_sum += latency_value_from_monitor_output
        Update bandwidth_sum += bandwidth_value_from_monitor_output
        Increment total_cycles by 1

average_latency = latency_sum / total_cycles
average_bandwidth = bandwidth_sum / total_cycles



-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Function to measure average latency and bandwidth using provided simulator output


def measure_latency_bandwidth(simulator)                  """Args: simulator: An instance of the simulator class."""

    # Initialize variables to store results and errors
    total_latency = 0
    total_bytes = 0
    num_transactions = 0
    errors = []
    # Loop through each entry in the simulator output
    for timestamp, txn_type, data in simulator.get_monitor_output():
        try:
            # Extract relevant information from each entry
            if txn_type not in ("Rd", "Wr"):
                raise ValueError(f"Unknown transaction type: {txn_type}")

            # Calculate latency for this transaction
            if txn_type == "Rd":
                latency = timestamp - data  # Assuming data contains the start time of the transaction
            else:  # txn_type == "Wr"
                latency = simulator.get_write_latency(data)  # Use simulator method for write latency

            if latency < 0:
                raise ValueError("Negative latency detected")

            total_latency += latency
            total_bytes += 32  # Bytes per transfer

            num_transactions += 1
        except (ValueError, TypeError) as e:
            errors.append(f"Error processing transaction: {e}")
        except Exception as e:
            errors.append(f"Unexpected error: {e}")

    # Calculate average latency and bandwidth (if applicable)
    average_latency = total_latency / num_transactions if num_transactions > 0 else 0.0
    average_bandwidth = (total_bytes * simulator.get_operating_frequency()) / num_transactions \
        if num_transactions > 0 else 0.0

    return {
        "average_latency": average_latency,
        "average_bandwidth": average_bandwidth,
        "errors": errors,
    }                                                        """  Returns : A dictionary containing average latency, average bandwidth, and a list of any encountered errors. """

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# TASK 2:
Using Reinforced Learning to arrive at the optimal parameters listed above. Put together a design document. Describe the RL framework consisting of states/behaviors, actions and rewards. You need to also advise which RL algorithm (ex: DQN, SARSA, Actor-Critic, etc.) is best suited for this problem statement. You need not develop or write the  RL algorithm. 

# Design Document: Reinforcement Learning for Optimizing Parameters in NOC Design

**1. Introduction**: This document proposeS the use of Reinforcement Learning (RL) techniques to optimize parameters in the design of Network-on-Chip (NOC) architectures. NOC design involves various parameters such as buffer sizes, arbiter weights, throttling frequency, latency, bandwidth, and power limits. The goal is to dynamically adjust these parameters to meet performance requirements while minimizing throttling and adhering to power constraints.

**2. Problem Statement**:
Design an RL framework to optimize the following parameters in NOC design:

*Buffer sizes
*Arbiter weights
*Throttling frequency


**3. RL Framework**:

**States/Behaviors**:

*Current buffer sizes
*Current arbiter weights
*Current throttling frequency
*Current latency and bandwidth measurements
*Current power limit threshold status

**Actions**:

*Adjust buffer sizes using set_max_buffer_size()
*Adjust arbiter weights using set_arbiter_weights()
*Adjust throttling frequency using throttle()

**Rewards**:

*Positive reward for meeting latency and bandwidth requirements
*Negative reward for excessive throttling or failure to meet requirements
*Additional reward/penalty based on power limit threshold status

**4. RL Algorithm Selection**:
Given the continuous and dynamic nature of the problem, an RL algorithm capable of handling continuous state and action spaces would be ideal. Based on this requirement, the following algorithms are suitable:

Deep Q-Network (DQN): DQN can handle continuous state spaces and has shown effectiveness in similar optimization problems. It approximates the Q-function and learns optimal policies.
Actor-Critic: Actor-Critic algorithms learn policies directly and are well-suited for problems with continuous action spaces. They combine the advantages of both policy-based (Actor) and value-based (Critic) methods.
Considering the complexity of NOC design and the need for continuous action adjustments, Actor-Critic algorithms may be more suitable. They can learn optimal policies while simultaneously estimating value functions.

**5. Conclusion**:
Utilizing RL techniques such as Actor-Critic, NOC designers can automate the optimization process, leading to more efficient and adaptive designs. By dynamically adjusting parameters based on real-time traffic patterns and workload types, RL can help achieve optimal NOC performance while maintaining power constraints.   
