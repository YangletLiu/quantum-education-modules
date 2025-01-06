============
Introduction
============

.. contents:: Table of Contents
   :local:

Overview
========

Quantum computing has the potential to revolutionize computing beyond the reach of classical computers. Currently, the manufacturing of quantum hardware is in the
infancy stage, called the Noisy Intermediate-Scale Quantum (NISQ) era. 

A major challenge is automated quantum circuit design that maps a quantum circuit to gates in a universal gate set. Traditional hand-crafted heuristic methods are 
often inefficient and not scalable. 

We explore reinforcement learning methods to automate the task of quantum circuit search. Our contributions can be summarized as follows:

- We present three generic Markov Decision Process (MDP) modelings for the quantum circuit design task.

- We study :math:`10` quantum circuit design tasks: :math:`4` Bell states, SWAP gate, iSWAP gate, CZ gate, GHZ gate, Z gate and Toffoli gate, respectively, given a universal gate set { :math:`H, T, \text{CNOT}` }.

Problem Formulation
===================

Taking Bell state :math:`\ket{\Phi^+}` as an example, we formulate the task of quantum circuit design as three versions of Markov Decision Process (MDP). 
In particular, we specify the state space, action set, reward function, and Q-table, respectively.

.. _bellcircuit:
.. figure:: ./images/bell_circuit.png
   :align: center
   :class: custom-img

   A quantum circuit to generate Bell state :math:`\ket{\Phi^+}`.

Task of Quantum Circuit Design
==============================

Given two qubits with initial state :math:`\ket{q_1q_0} = \ket{00}` and a universal gate set :math:`G =` { :math:`H, T, \text{CNOT}` }, the goal is to find a quantum circuit 
that generates the Bell state :math:`\ket{\Phi^+}`: 

.. _bell:
.. math::
   \ket{\Phi^+} = \frac{1}{\sqrt{2}} \left( \ket{00} + \ket{11} \right)
   :label: eq:1
   
The target quantum circuit to generate :math:`\ket{\Phi^+}` whose matrix representation is:

.. _U:
.. math::
   U &= \text{CNOT}_{01} \cdot (H \otimes I) \\
   &=
   \begin{pmatrix}
   1 & 0 & 0 & 0 \\
   0 & 1 & 0 & 0 \\
   0 & 0 & 0 & 1 \\
   0 & 0 & 1 & 0 
   \end{pmatrix}
   \cdot \left(
   \frac{1}{\sqrt{2}}
   \begin{pmatrix}
      1 & 1\\
      1 & -1
   \end{pmatrix}
   \otimes
   \begin{pmatrix}
      1 & 0\\
      0 & 1\\
   \end{pmatrix} \right)\\
   &=
   \frac{1}{\sqrt{2}}
   \begin{pmatrix}
   1 & 0 & 1 & 0 \\
   0 & 1 & 0 & 1 \\
   0 & 1 & 0 & -1 \\
   1 & 0 & -1 & 0 
   \end{pmatrix}
   :label: eq:2

Note that :math:`\ket{\Phi^+} = U~\ket{00}`.