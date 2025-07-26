# PoC: Reducing the Computational Cost of Network Digital Twins

A Proof-of-Concept demonstrating how a Reinforcement Learning agent can dynamically reduce simulation costs for a Wi-Fi digital twin, directly addressing the core research question of the PhD position at ENS Lyon's LIP laboratory.

---

## 1. Need

Digital Twins offer powerful, real-time replicas of physical systems, but their practical use is often limited by high computational costs. For wireless networks, accurately simulating the physical layer (e.g., radio wave propagation) is resource-intensive and can prevent real-time operation. To make these digital twins scalable and efficient, there is a critical **need for an intelligent method to reduce the number of costly simulations** without significantly compromising the accuracy of the twin's state. This project serves as a Proof-of-Concept for such a method.

## 2. How to Run

The project is contained within a single Google Colab notebook. No local installation is required.

1.  Clone this repository or download the `.ipynb` file.
2.  Open `PoC_Reducing_Computational_Cost_of_Network_Digital_Twins.ipynb` in Google Colab.
3.  Run the single code cell to execute the entire simulation and generate the results and plots. All required libraries (`numpy`, `matplotlib`) are pre-installed in the Colab environment.

## 3. Input

This project uses a self-contained, dynamically generated simulation environment. No external data is loaded. The key inputs are:

*   **A 2D Environment:** A 100m x 100m simulated area.
*   **A Static Access Point (AP):** A single Wi-Fi AP located at the center of the environment.
*   **A Mobile Device:** A single device that performs a random walk within the environment, changing its position at each timestep.

## 4. Pre-processing

As a self-contained simulation, this project requires no external data loading or pre-processing (like cleaning, normalization, or feature scaling of a dataset).

## 5. Models

Two core models were implemented to create the simulation environment and the intelligent controller.

#### The "Costly" Physics Model
*   **Purpose:** To represent the ground truth physical reality of the system.
*   **Method:** The Received Signal Strength Indicator (RSSI) is calculated using the **log-distance path loss model**. This function takes the AP and device coordinates as input and returns the true signal strength. It is designed to be the "costly" function that our agent aims to avoid calling.

#### The Intelligent Controller (Multi-Armed Bandit)
*   **Purpose:** To act as the intelligent controller that decides when to run the costly simulation.
*   **Method:** An **Epsilon-Greedy Multi-Armed Bandit (MAB)** was implemented.
    *   **Arms:** The agent has two choices (arms):
        1.  **Arm 0 (Simulate):** Call the costly physics model to get the true RSSI. This action has a fixed cost but provides perfect accuracy.
        2.  **Arm 1 (Don't Simulate):** Reuse the last known RSSI value. This action is free but risks becoming inaccurate if the system state has changed.
    *   **Learning:** The agent learns a policy by maximizing a reward signal. The reward is designed to penalize both the cost of simulation and the error between its estimate and the ground truth.

## 6. Output & Results

The simulation ran for 500 timesteps and successfully demonstrated the agent's ability to balance cost and accuracy.

*   **Computational Cost Saving:** The agent chose to run the costly simulation only 165 times out of a possible 500, resulting in a **cost saving of 67.00%**.
*   **Accuracy:** The agent's adaptive strategy maintained a high degree of accuracy, with a final **Mean Squared Error (MSE) of only 1.1128**.
*   **Learned Policy:** The final Q-values `[-1.0, -1.002]` show the agent successfully learned the distinct, long-term value of each action based on the trade-offs in the environment.

### Visualizations

The plot below shows the random path of the mobile device around the central Access Point.

![Device Path in Simulated Environment](device_path.png)

This plot is the key result. It shows the agent's estimated RSSI (orange) tracking the ground truth (blue), while only triggering the costly simulation (red 'x') at critical moments of change.

![Agent Performance: Balancing Accuracy and Cost](agent_performance.png)

## 7. Significance

This Proof-of-Concept successfully demonstrates that a simple reinforcement learning agent can function as an effective, data-driven controller to manage the computational load of a digital twin. It validates the core hypothesis of the target PhD research proposal: that it is possible to **dynamically adjust the simulation sampling rate to find an optimal balance between computational efficiency and simulation accuracy**. This work provides a strong, practical foundation for developing more advanced controllers for real-world, scalable network digital twins.
