=======================================
Markov Decision Process (MDP) Modelings
=======================================

.. contents:: Table of Contents
   :local:

We propose three different ways to model the quantum circuit design (QCD) task within a Markov Decision Process (MDP) framework:

1. **Matrix Representation**
2. **Reverse Matrix Representation**
3. **Tensor Network (TN) Representation**

In each MDP modeling, we define the following three key components:

1. **State Space**:
   The set of all possible states that can arise during circuit construction, where each **state** represent a partial circuit

2. **Action Set**:

   - The set of all possible gates that can be applied at any point, e.g., :math:`H`, :math:`T`,
     or :math:`\text{CNOT}` on specific qubit(s).

   - Executing an action transforms one state into another by adding the chosen gate
     to the circuit.

3. **Reward Function**:

   - A measure of how close the resulting state is to the target operation.

We will use the Bell-state circuit in :numref:`bellcircuit` as a running example to illustrate these approaches.

Matrix Representation: A Step-by-Step Overview
==============================================

.. _bellstatematrixtree:
.. figure:: ./images/bell_state_matrix_tree.png
   :width: 50%
   :align: center
   :class: custom-img

   State tree in matrix representation for searching the circuit in :numref:`bellcircuit`.

A Gentle Single-Qubit Example
-----------------------------
Before we explore the 4×4 matrix representation for the two-qubit Bell state, let’s build some intuition with a simpler *single-qubit* scenario. 

1. **Initial Matrix**: For a single qubit, the *identity* matrix is 
   :math:`I_2 = \begin{pmatrix}1 & 0 \\ 0 & 1\end{pmatrix}`. Think of this as your “starting state,” which does nothing to the qubit.
2. **Apply a Gate**: For instance, applying the Hadamard gate :math:`H` (an action :math:`a = H`) transforms the state via matrix multiplication:
   :math:`H \cdot I_2 = H`.
3. **Result**: Once you apply a gate, the *new* matrix represents the updated transformation on your qubit. 

In this way, each *action* (i.e., choosing a gate) updates the overall transformation by multiplying the current matrix with the gate’s matrix. Now, let’s extend this idea to **two-qubit** matrices of size 4×4.

Matrix Representation (Two-Qubit)
---------------------------------

- **Actions** :math:`\mathcal{A}` = { :math:`H_0, H_1, T_0, T_1, \text{CNOT}_{01}` }.  
  These five gates act on either qubit :math:`q_0` or :math:`q_1`. An action :math:`a \in \mathcal{A}` is thus a :math:`4 \times 4` matrix.

- **State Space** :math:`\mathcal{S}`:  
  The *initial state* is :math:`U_0 = I_{4}`, the :math:`4 \times 4` identity matrix. The *terminal state* is :math:`U`, given in :ref:`(2) <U>`.  
  Let :math:`S` be the current state (a node in :numref:`bellstatematrixtree`), and :math:`A \in \mathcal{A}` be the chosen action.  
  Then the resulting child state :math:`S'` is:

  .. math::
     S' = A \cdot S
     :label: eq:3

  In other words, we *transform* the existing matrix :math:`S` by multiplying on the left by the gate matrix :math:`A`.

- **Tree Structure**:  
  As shown in :numref:`bellstatematrixtree`, from the initial state :math:`S_0 = I_4`, we have 5 possible actions, leading to { :math:`S_1, S_2, S_3, S_4, S_5` }. Another step of 5 actions from any of those states yields 25 more states, and so on. Altogether, :math:`\mathcal{S}` contains 31 states in this example.

- **Reward Function** :math:`\mathcal{R}`:  
  At state :math:`S_1`, if the agent picks :math:`\text{CNOT}_{01}`, then 
  :math:`R(s = S_1, a = \text{CNOT}_{01}) = 100`; otherwise :math:`R(s,a) = 0`.  

Example for :numref:`bellcircuit`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Suppose the initial state is :math:`S_0 = I_4`, and we follow the *optimal* path :math:`S_0 \rightarrow S_1 \rightarrow S_{10}`:

1. **State after first action** :math:`a = H_0`:

   .. math::
      S_1 
      = (H_0 \otimes I) S_0
      = \frac{1}{\sqrt{2}}
        \begin{pmatrix}
        1 & 0 & 1 & 0 \\
        0 & 1 & 0 & 1 \\
        1 & 0 & -1 & 0 \\
        0 & 1 & 0 & -1 
        \end{pmatrix}.
      :label: eq:4

2. **State after second action** :math:`a = \text{CNOT}_{01}`:

   .. math::
      S_{10} &= \text{CNOT}_{01} \cdot S_1 \\
             &= \frac{1}{\sqrt{2}}
                \begin{pmatrix}
                1 & 0 & 0 & 0 \\
                0 & 1 & 0 & 0 \\
                0 & 0 & 0 & 1 \\
                0 & 0 & 1 & 0 
                \end{pmatrix}
                \begin{pmatrix}
                1 & 0 & 1 & 0 \\
                0 & 1 & 0 & 1 \\
                1 & 0 & -1 & 0 \\
                0 & 1 & 0 & -1 
                \end{pmatrix} 
             = U,
      :label: eq:5

   This final matrix :math:`U` corresponds to the target circuit in :ref:`(2) <U>`.

**Advantages** and **Disadvantages**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- **Advantage**: Because different gate sequences can produce the *same* overall :math:`4 \times 4` matrix, this representation can merge multiple paths into a single state, reducing the search space.
- **Disadvantage**: The RL agent must be re-trained for *each* target matrix, even if many circuits share common intermediate sub-matrices.

Reverse Matrix Representation
=============================

.. _bellstatereversematrixtree:
.. figure:: ./images/bell_state_reverse_matrix.png
   :width: 50%
   :align: center
   :class: custom-img

   State tree in reverse matrix representation for searching the circuit in :numref:`bellcircuit`.

- **Actions** :math:`\mathcal{A}^{-1}` = { :math:`H_0^{-1}, H_1^{-1}, T_0^{-1}, T_1^{-1}, \text{CNOT}_{01}^{-1}` }.  
  Each inverse gate is again a :math:`4 \times 4` matrix.

- **State Space** :math:`\mathcal{S}^{-1}`:  
  The initial state is now :math:`S_0^{-1} = U` (the *target* matrix), and the terminal state is :math:`I_4`.  
  Let :math:`S^{-1}` be the current node in :numref:`bellstatereversematrixtree`, and :math:`A^{-1} \in \mathcal{A}^{-1}` be the chosen inverse gate. Then:

  .. math::
     S'^{-1} = A^{-1} \cdot S^{-1}
     :label: eq:6

  Similarly, this forms a tree with 31 states, just in the reverse direction.

- **Reward Function** :math:`\mathcal{R}`:  
  If at state :math:`S_5^{-1}` the agent picks :math:`H_0^{-1}`, we get :math:`R(s = S_5^{-1}, a = H_0^{-1})= 100`; otherwise 0.

Example for :numref:`bellcircuit`
---------------------------------
Given :math:`S_0^{-1} = U`, we look at the optimal path :math:`S_0^{-1} \rightarrow S_5^{-1} \rightarrow S_{26}^{-1}`:

1. **Applying** :math:`\text{CNOT}_{01}^{-1}`:

   .. math::
      S_5^{-1} 
      = \text{CNOT}_{01}^{-1} \cdot S_0^{-1}
      = \begin{pmatrix}
         1 & 0 & 0 & 0 \\
         0 & 1 & 0 & 0 \\
         0 & 0 & 0 & 1 \\
         0 & 0 & 1 & 0 
         \end{pmatrix}
        \frac{1}{\sqrt{2}}
         \begin{pmatrix}
         1 & 0 & 1 & 0 \\
         0 & 1 & 0 & 1 \\
         0 & 1 & 0 & -1 \\
         1 & 0 & -1 & 0 
         \end{pmatrix}
      = \frac{1}{\sqrt{2}}
         \begin{pmatrix}
         1 & 0 & 1 & 0 \\
         0 & 1 & 0 & 1 \\
         1 & 0 & -1 & 0 \\
         0 & 1 & 0 & -1 
         \end{pmatrix}.
      :label: eq:7

2. **Applying** :math:`H_0^{-1}`:

   .. math::
      S_{26}^{-1}
      = (H_0^{-1} \otimes I) \cdot S_5^{-1}
      = \frac{1}{2}
         \begin{pmatrix}
         1 & 0 & 1 & 0 \\
         0 & 1 & 0 & 1 \\
         1 & 0 & -1 & 0 \\
         0 & 1 & 0 & -1 
         \end{pmatrix}
         \begin{pmatrix}
         1 & 0 & 1 & 0 \\
         0 & 1 & 0 & 1 \\
         1 & 0 & -1 & 0 \\
         0 & 1 & 0 & -1 
         \end{pmatrix}
      = I_4.
      :label: eq:8

Reversing the sequence of actions (and taking inverses) essentially shows you how to “unbuild” the target matrix :math:`U` back to the identity.

Tensor Network Representation
=============================

.. _belltensor:
.. figure:: ./images/bell_tensor.png
   :width: 50%
   :align: center
   :class: custom-img

   TN representation of :ref:`Fig. 1 <bellcircuit>`.

.. _belltensortree:
.. figure:: ./images/bell_tensor_tree.png
   :width: 50%
   :align: center
   :class: custom-img

   State tree in TN representation for searching the circuit in :ref:`Fig. 1 <bellcircuit>`.

The Tensor Network (TN) is another way to represent quantum circuits. Each gate becomes a tensor: single-qubit gates are 2-index tensors, two-qubit gates are 4-index tensors, etc. The network forms by connecting these tensors along shared indices. 

For :numref:`bellcircuit`, consider the universal gate set :math:`G = \{ H_0, H_1, T_0, T_1, \text{CNOT}_{01} \}`. We allow up to two gates in one action for demonstration:

- **Actions** :math:`\mathcal{A}` could be single gates or pairs of gates, e.g., 
  :math:`(H_0, \text{CNOT}_{01}), (\text{CNOT}_{01}, T_1), \ldots`  
  There are 17 distinct actions in total.

- **State Space** :math:`\mathcal{S}`:  
  The initial state is :math:`S_0 = \ket{00}`, and the terminal state is the Bell state :math:`\ket{\Phi^+}` in :ref:`(1) <bell>`.  
  The transition rule is analogous:

  .. math::
     S' = A \cdot S
     :label: eq:9

  where :math:`A` acts on the current TN to produce a new one.

Example for :numref:`bellcircuit`
---------------------------------
Starting with :math:`S_0 = \ket{00}`, we choose the combined action :math:`(H_0, \text{CNOT}_{01})`:

.. math::
   S_{14}
   &= \text{CNOT}_{01} \cdot (H \otimes I) \cdot S_0 \\
   &= \text{CNOT}_{01} \cdot
      \Bigl(\tfrac{1}{\sqrt{2}}\bigl[\ket{00} + \ket{10}\bigr]\Bigr) \\
   &= \tfrac{1}{\sqrt{2}}\bigl[\ket{00} + \ket{11}\bigr],
   :label: eq:10

which directly yields the Bell state.

**Beginner’s Tip**: In all three representations (Matrix, Reverse Matrix, and TN), you can think of *state* as “where you are” in the circuit-building process, and *action* as “which gate(s) to apply next.” The main difference is whether you are multiplying by the *gate matrix* or its *inverse*, or rewriting the system in a *tensor* form.

.. [Paper] Wang, Z.; Feng, C.; Poon, C.; Huang, L.; Zhao, X.; Ma, Y.; Fu, T.; and Liu, X.-Y. 2025. Reinforcement learning for quantum circuit design: Using matrix representations. In arXiv, 2501.16509. https://arxiv.org/abs/2501.16509.
