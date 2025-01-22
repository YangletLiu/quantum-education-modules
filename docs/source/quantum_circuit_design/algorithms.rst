==========
Algorithms
==========

.. contents:: Table of Contents
   :local:

Q-learning
==========

The Q-learning algorithm updates a Q-table in each step as follows:

.. math::
   :label: eq:Q

   \begin{split}
   Q^{\text{new}}(S_t, A_t) \leftarrow \underbrace{(1 - \alpha)}_{\text{learning rate}} \cdot \underbrace{Q(S_t, A_t)}_{\text{current value}} 
   + \underbrace{\alpha}_{\text{learning rate}} \cdot \left( \underbrace{R_{t+1}}_{\text{reward}} + \underbrace{\gamma}_{\text{discount factor}} \cdot \underbrace{\max_{a} Q(S_{t+1}, a)}_{\text{estimate of optimal future value}} \right).
   \end{split}


DQN
==========

Deep Q-Network (DQN) method uses a neural network to approximate the Q-values for each state-action pair. The DQN algorithm utilizes two neural networks:

- **Policy network** with parameter :math:`\theta`: It consists of three fully connected layers, each with 128 neurons. The input is the state, and the outputs are Q-values for each action.
- **Target network** with parameter :math:`\overline{\theta}`: A separate network that stabilizes the training process. It is periodically updated using 
  :math:`\overline{\theta} = (1-\alpha) \overline{\theta} + \alpha \theta`, where :math:`\alpha` is the learning rate.

Experiences stored in the replay buffer are randomly sampled to train the policy network, reducing correlations between consecutive samples. The loss function is defined as the Mean Squared Error (MSE) between the predicted Q-values from the policy network and the target Q-values:

.. math::

   \mathcal{L}_{\theta} = \text{MSE} \left(Q(s, a \mid \theta), R + \gamma \cdot \max_{a'} Q(s', a' \mid \overline{\theta})\right),

where :math:`Q(s, a \mid \theta)` denotes the Q-value predicted by the policy network for the current state-action pair, and the target Q-value is calculated as the immediate reward :math:`R` plus the discounted maximum next-step Q-value :math:`\max_{a'} Q(s', a' \mid \overline{\theta})`, which is estimated using the target network.

