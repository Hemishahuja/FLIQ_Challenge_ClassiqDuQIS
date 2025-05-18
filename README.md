# Team MerQury Submission

# Classiq x DuQIS FLIQ Challenge
Science track challenge for FLIQ 2025 hackathon

# Classifying Quantum Phases of Matter

A challenge hosted by **Classiq Technologies** and **DuQIS** (Duke Quantum Information Society) as part of the FLIQ Hackathon. Participants will apply **quantum machine learning** to distinguish between different phases of a Rydberg atom system using measurement data obtained in randomized bases.

## Challenge Summary

The task is to build a **Quantum Machine Learning (QML)** model capable of classifying different phases of quantum matter from measurement data.

Unlike typical datasets, your inputs are **classical shadows**: compressed representations of quantum states constructed via randomized measurements. Your model should learn to identify the phase label of a quantum state based only on this information.

## Background

### Classical Shadows

A classical shadow is a compact representation of a quantum state constructed from local randomized measurements. Suppose an experiment prepares an $n$-qubit state $\rho$. Instead of reconstructing $\rho$ directly (which is exponentially costly), we perform $T$ rounds of random single-qubit Pauli measurements.

For each round $t$, each qubit is measured in a random Pauli basis, collapsing $\rho$ into a product state:

$$
\ket{s^{(t)}} = \bigotimes_{i=1}^n \ket{s_i^{(t)}}, \quad \ket{s_i^{(t)}} \in \lbrace \ket{g}, \ket{r}, \ket{+}, \ket{-}, \ket{+i}, \ket{-i} \rbrace.
$$

We then form an estimate:

$$
\sigma_T(\rho) = \frac{1}{T} \sum_{t=1}^T \sigma_1^{(t)} \otimes \dots \otimes \sigma_n^{(t)}, \quad \sigma_i^{(t)} = 3\ketbra{s_i^{(t)}}{s_i^{(t)}} - I.
$$

You are provided with $nT$ measurement results per data point. You may choose to compute reduced density matrices for specific subsystems, such as:

$$
\rho^A \approx \frac{1}{T} \sum_{t=1}^T \bigotimes_{i \in A} \sigma_i^{(t)}.
$$

For more details, see:

- Huang et al., *Predicting Many Properties of a Quantum System from Very Few Measurements* (2020), [arXiv:2002.08953](https://arxiv.org/abs/2002.08953)
- Huang et al., *Provably efficient machine learning for quantum many-body problems* (2022), [arXiv:2106.12627](https://arxiv.org/abs/2106.12627)

### Details on Rydberg Atoms

The Rydberg Hamiltonian for an atom chain reads

$$
H = \frac{\Omega}{2} \sum_{i=1}^N X_i 
    - \delta \sum_{i=1}^N n_i 
    + \sum_{i \lt j} \frac{\Omega R_b^6 }{(a|i-j|)^6} n_i n_j,
$$

where $\Omega$ is the Rabi frequency; $\delta$ is the laser detuning; $a$ is the inter-atomic spacing; $R_b$ is the blockade radius; $n_i \equiv \ket{r_i}\bra{r_i}$ is the projector onto the Rydberg state on the $i^{\text{th}}$ qubit; and $X_i = \ket{g_i}\bra{r_i} + \ket{r_i}\bra{g_i}$ is a Pauli $X$ operator.

The Rydberg Hamiltonian contains three types of operators:

1. Terms involving Pauli $X = \ket{r}\bra{g} + \ket{g}\bra{r}$ are responsible for driving atoms from $\ket{g}$ to $\ket{r}$.

2. Terms involving the projector $n$ introduce the punishment (or reward) for being in the excited state: when $\delta > 0$, excitation is penalized; when $\delta < 0$, excitation is rewarded.

3. The interaction terms $n_i \otimes n_j$ realize the Rydberg blockade mechanism.

The interaction terms prevent neighboring sites from being excited simultaneously, while the terms involving $n$ ensure that the number of excitations is maximized when $\delta \gg 0$. Thanks to this interplay of Hamiltonian terms, neutral-atom systems constitute interesting phases of matter even in a single spatial dimension. You can find the phase diagram for a 51-atom chain inside the challenge notebook. Your goal is to design a quantum model that can distinguish between the so-called $Z2$-ordered and $Z3$-ordered states.

For more information, please refer to:
- [Bloqade Julia](https://queracomputing.github.io/Bloqade.jl/stable/tutorials/1.blockade/main/)

### QML Models

Participants are expected to build a parameterized quantum circuit to classify measurement data. The exact architecture — including encoding scheme, number of qubits, and circuit layers — is up to you.

The following reading might inspire your quantum circuit:

- Sim et al., *Expressibility and Entangling Capability of Parameterized Quantum Circuits for Hybrid Quantum‐Classical Algorithms* (2019), [arXiv:1905.10876](https://arxiv.org/abs/1905.10876)

## Dataset Format

Each data point consists of:

- A list of $T$ measurement outcomes, each a list of $n$ elements $s_j^{(i,t)} \in \lbrace\text{"g"}, \text{"r"}, \text{"+"}, \text{"-"}, \text{"+i"}, \text{"-i"}\rbrace$
- A label $y^{(i)}$ such as `"Z2"` indicating the phase

Example:

$$
x^{(i)} = \left[
\left[\text{"-"}, \text{"+i"}, \dots, \text{"g"}\right],\ 
\left[\text{"g"}, \text{"-i"}, \dots, \text{"r"}\right],\ 
\dots
\right]; \quad y^{(i)} = \text{"Z2"}
$$

It is up to you to choose which reduced density matrices to extract as features (e.g., 1-qubit, 2-qubit). Avoid reconstructing the full $\rho$, which is a $2^n \times 2^n$ object.

## Your Task

Build a quantum circuit that:

- Takes as input reduced density matrices constructed from the measurement data
- Outputs a prediction of the quantum phase
- Is optimized for both accuracy and efficiency

## Grading Criteria

Each submission will be scored using the function:

$$
f(A, P, D, W) = A - 0.1 \cdot P - 0.0002 \cdot D - 0.1 \cdot W
$$

Where:

- $A$: accuracy on the test set  
- $P$: number of trainable parameters  
- $D$: circuit depth  
- $W$: number of qubits (circuit width)

Higher values of $f$ indicate better solutions.

This grading scheme is not rigorous - it only filters solutions. Even if your solution doesn't yield a high $f$, it may still be considered for manual grading. The best solutions will be manually graded.

## This Repository

In this repo you can find the following files:
- `FLIQ_Challenge_ClassiqDuQIS.ipynb` – the notebook with code snippets, in which you will show your solution
- `training_data.npz` – file with randomized measurements. There are 10 data points corresponding to each of the two phases studied
- `phase_diagram.png` – Rydberg phase diagram for a 51-particle neutral atom array

## Getting Started

You may clone the repository locally and use your editor of choice. Alternatively, you may clone the repository into Classiq Studio - a web-based Classiq IDE.
If you would like to use Classiq Studio, refer to the following guide: https://docs.classiq.io/latest/user-guide/classiq-studio/

## Submission Instructions

On the submission platform, please upload the main Jupyter notebook along with any additional `.py` files used for data processing. You should also include:

- The quantum program (`qprog`) saved as a `.qprog` file  
- The trained model parameters saved as a `.npz` file using NumPy  

Ensure all files necessary to reproduce your results are included in the submission.

*Submission deadline: Sunday, May 18 at 08:00 UTC* — mark it, set alarms, summon caffeine.

## Tips

- Reduced density matrices of small subsystems may already carry enough information about the phase.
- Consider different encoding strategies such as angle or amplitude encoding. You can find the pre-defined Classiq method for angle encoding [here](https://docs.classiq.io/latest/explore/functions/qmod_library_reference/classiq_open_library/variational_data_encoding/variational_data_encoding/#encode-on-bloch)
- You may apply classical preprocessing, but the model must ultimately be quantum.
