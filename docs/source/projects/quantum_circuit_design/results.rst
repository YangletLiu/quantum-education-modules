=======
Results
=======

We verify the above three MDP modelings for 10 well-known quantum circuits, namely, circuits to generate 4 Bell states, SWAP gate, iSWAP gate, CZ gate, GHZ gate, Z gate, and Toffoli gate. For Matrix and Reverse Matrix Representations, we apply both Q-learning and DQN algorithms, while for TN Representation, we applied only Q-learning. Our codes can be found at this link:

`https://github.com/YangletLiu/quantum-education-modules/tree/main/Quantum_Circuit_Design <https://github.com/YangletLiu/quantum-education-modules/tree/main/Quantum_Circuit_Design>`_

**Toffoli Gate**: We used an action set with gates shown in Figure 1:

.. math::

   \mathcal{A} = \{\text{CNOT}_{21}, H_0, \text{CP}_{10}, \text{CP}^{-1}_{10}, \text{CP}_{20}\},

where the :math:`\text{CP}` gate refers to a controlled-phase gate with a phase shift of :math:`\frac{\pi}{2}`, and :math:`\text{CP}^{-1}` with a phase shift of :math:`-\frac{\pi}{2}`.

An expert trajectory was stored in the replay buffer to improve learning efficiency. For the Matrix Representation, the expert trajectory is:

.. math::

   \{ H_0 \rightarrow \text{CP}_{10} \rightarrow \text{CNOT}_{21} \rightarrow \text{CP}^{-1}_{10} \rightarrow \text{CNOT}_{21} \rightarrow \text{CP}_{20} \rightarrow H_0 \}.

For the Reverse Matrix Representation:

.. math::

   \mathcal{A}^{-1} = \{\text{CNOT}_{21}^{-1}, H_0^{-1}, \text{CP}_{10}^{-1}, \text{CP}_{10}, \text{CP}_{20}^{-1}\},

and the expert trajectory becomes:

.. math::

   \{ H_0^{-1} \rightarrow \text{CP}_{20}^{-1} \rightarrow \text{CNOT}_{21}^{-1} \rightarrow \text{CP}_{10} \rightarrow \text{CNOT}_{21}^{-1} \rightarrow \text{CP}_{10}^{-1} \rightarrow H_0^{-1} \}.

Each state expands in a branching factor (size of actions) :math:`c` across :math:`b + 1` levels (length of the tasks +1). As shown in Figure 2, the size of the state space is given by a geometric series:

.. math::

   \text{Size of state space} = c^0 + c^1 + \cdots + c^b = \frac{c^{b+1} - 1}{c - 1}.

The complexity of the task is measured by the size of the state space, as shown in Table 1. To evaluate the effectiveness of Q-learning and DQN, we conducted 100 rounds. In each round, the agent was trained for 100 episodes, and we measured the success ratio (in percentage) of correct testing results over the 100 rounds. The results are summarized in Table 2.

From Table 2, we observe that both Q-learning and DQN perform well on simpler tasks, such as generating the Bell state :math:`|\Phi^+\rangle`. However, as task complexity increases, for example, in the iSWAP gate task with a state space size of :math:`5^6`, the performance of both algorithms significantly degrades, indicating the challenges of learning in large state spaces.


.. list-table:: Task properties for various quantum circuits.
   :header-rows: 1
   :widths: 15 10 10 10 15 20

   * - **Task Name**
     - **Qubits**
     - **Actions**
     - **Length**
     - **Space Size**
     - **Universal Gate Set**
   * - Bell state :math:`|\Phi^+\rangle`
     - 2
     - 6
     - 2
     - 43
     - :math:`\{H, \text{CNOT}, T\}`
   * - Bell state :math:`|\Phi^-\rangle`
     - 2
     - 6
     - 3
     - 259
     - :math:`\{H, \text{CNOT}, T, X\}`
   * - Bell state :math:`|\Psi^+\rangle`
     - 2
     - 6
     - 3
     - 259
     - :math:`\{H, \text{CNOT}, T, X\}`
   * - Bell state :math:`|\Psi^-\rangle`
     - 2
     - 8
     - 5
     - 37449
     - :math:`\{H, \text{CNOT}, T, X, Z\}`
   * - SWAP gate
     - 2
     - 6
     - 3
     - 259
     - :math:`\{H, \text{CNOT}, T\}`
   * - iSWAP gate
     - 2
     - 6
     - 5
     - 9331
     - :math:`\{H, \text{CNOT}, T\}`
   * - CZ gate
     - 2
     - 6
     - 3
     - 259
     - :math:`\{H, \text{CNOT}, T\}`
   * - GHZ gate
     - 3
     - 8
     - 3
     - 585
     - :math:`\{H, \text{CNOT}, T\}`
   * - Z gate
     - 3
     - 10
     - 2
     - 111
     - :math:`\{H, \text{CNOT}, T, S\}`
   * - Toffoli gate
     - 3
     - 5
     - 7
     - 97656
     - Special Case


.. list-table:: Success ratios (in percentage) over 100 training rounds, respectively.
   :header-rows: 1
   :widths: 10 10 15 10 15 15

   * - **Gates**
     - **Q-Learning**
     - **Q-Learning (Reverse)**
     - **DQN**
     - **DQN (Reverse)**
     - **Q-Learning (TN)**
   * - Bell state :math:`|\Phi^+\rangle`
     - 86%
     - 85%
     - 33%
     - 39%
     - **100%**
   * - Bell state :math:`|\Phi^-\rangle`
     - 41%
     - 25%
     - 18%
     - 20%
     - **94%**
   * - Bell state :math:`|\Psi^+\rangle`
     - 55%
     - 53%
     - 21%
     - 17%
     - **95%**
   * - Bell state :math:`|\Psi^-\rangle`
     - 5%
     - 4%
     - 6%
     - 4%
     - **15%**
   * - SWAP gate
     - 10%
     - 15%
     - 21%
     - **27%**
     - 3%
   * - iSWAP gate
     - 2%
     - 1%
     - 2%
     - **5%**
     - 2%
   * - CZ gate
     - 69%
     - **77%**
     - 16%
     - 17%
     - 19%
   * - GHZ gate
     - 34%
     - 17%
     - 13%
     - 20%
     - **45%**
   * - Z gate
     - **50%**
     - 38%
     - 17%
     - 19%
     - 13%
   * - Toffoli gate
     - 87%
     - **91%**
     - 1%
     - 3%
     - -

.. [Paper] Wang, Z.; Feng, C.; Poon, C.; Huang, L.; Zhao, X.; Ma, Y.; Fu, T.; and Liu, X.-Y. 2025. Reinforcement learning for quantum circuit design: Using matrix representations. In arXiv, 2501.16509. https://arxiv.org/abs/2501.16509.