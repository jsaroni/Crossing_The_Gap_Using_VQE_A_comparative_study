# Crossing The Gap Using VQE: A comparative study

Within the evolving domain of quantum computational chemistry, the Variational Quantum Eigensolver (VQE) has been identified as a crucial algorithm for exploiting the capabilities of near-term quantum computers. This work investigates the proficiency of variational quantum algorithms, particularly adaptive VQE techniques, and Quantum Natural Gradient (QNG) optimization, in calculating the spectral gap of chemical molecules—a key determinant of material characteristics and chemical behavior. Our methodology introduces different approaches to estimating the k-th excited state by mapping highly excited states to the ground state via unitary transformations, broadening VQE's applicability.
Our research focuses on analyzing three molecular systems, $H_2$, $LiH$, and $BeH_2$, to demonstrate the versatility and precision of our proposed methods. A significant highlight of our study is the implementation of QNG optimization, which markedly improves the optimization process's efficiency. It consistently minimizes the ground state energy while achieving convergence in fewer iterations than traditional gradient descent optimizers. This efficiency underscores the advantage of QNG in navigating the complex optimization landscape of quantum parameters more effectively.
Additionally, our comparative analysis reveals that both subspace variational quantum eigensolver (SS-VQE) and quantum variational deflation (VQD) methods exhibit strong performance in spectral gap determination, with less than 1\% relative error for $LiH$, and notably, VQD achieves an exceptional 99.99999\% accuracy. Lastly we explore Pauli tapering and the usage of a tensor network ansatz to improve algorithm speed.


## Methodology
### Subspace-Search Variational Quantum Eigensolver
The Subspace-Search Variational Quantum Eigensolver (SS-VQE) is an algorithm designed to address the challenge of calculating excited states \cite{PhysRevResearch.1.033062}. SS-VQE efficiently explores a low-energy subspace to identify the k-th excited state by utilizing orthogonal input states and leveraging unitary transformations. It is worth noting that this method involves only two parameter optimization steps and eliminates the necessity for ancilla qubits. SS-VQE further generalizes all excited states up to the k-th through a single optimization procedure. Through careful parameter optimization, this approach ensures the orthogonality of input states and accurately maps them to energy eigenstates.
This approach minimize the following loss function

```math
\mathcal{L}_w(\theta) = w\bra{\psi_k}U^\dagger(\theta)HU(\theta)\ket{\psi_k} +\sum_{j=0}^{k-1}\bra{\psi_j}U^\dagger(\theta)HU(\theta)\ket{\psi_j}
```

where the cost $\mathcal{L}_w$ gets its global optimum, the circuit $U(\theta)$ maps $\ket{\psi_k}$ to the k-th excited state $\ket{E_k}$ of the Hamiltonian(H) and others to the
subspace spanned by $`\sum^{k-1}_{j=0} E_j`$.

![a](https://latex.codecogs.com/svg.latex?\Large&space;H=\sum_{k=1}^N\overrightarrow{b}\cdot\overrightarrow{\sigma_k}+\sum_{p<q}^NJ_{pq}\overrightarrow{\sigma_p}\cdot\overrightarrow{\sigma_q})


###Variational Quantum Deflation

The potential of VQE for near-term quantum computing is generating excitement for the advancement of computational capabilities.  \cite{Higgott2019variationalquantum} study aims to enhance VQE's functionality by efficiently identifying excited states. By incorporating ``overlap'' terms into the optimization function and leveraging Hermitian matrices, which consist of a complete set of orthogonal eigenvectors, this work demonstrates a cost-effective approach. Utilizing VQE's ability to maintain classical parameters, low-depth quantum circuits are employed to compute these overlap terms. This methodology maintains the same qubit count as VQE for ground-state calculations, with only a minor increase in measurements. In contrast to existing methods for computing excited states in quantum computing, this approach minimizes resource overhead. The following loss function has been provided over this work. 
\begin{align}
\mathcal{L}(\lambda_k) = & \bra{\psi(\lambda_k)}H\ket{\psi(\lambda_k)} \\
& +\sum_{i=0}^{k-1} \beta_i |\braket{\psi(\lambda_k)|\psi(\lambda_i)}|^2.
\end{align}

\subsection{Folded Spectrum VQE}

Expanding on the VQE, \cite{tazi2023folded} study introduces a specialized method for calculating molecular excited states. By utilizing the Folded Spectrum (FS) approach, we restructure the Hamiltonian's eigenspectrum to specifically target highly excited states. While FS has been acknowledged in the past, its quantum application was previously considered too costly due to the exponential growth of terms in the measured operator. Nevertheless, our implementation reveals a significant advancement by employing a Pauli grouping technique, which can significantly reduce the number of required measurements, making FS a cost-efficient option. This technique has a particularly notable impact on second quantized molecular Hamiltonians, thanks to their distinct structural properties. The loss function for this procedure is 
\begin{align}
\mathcal{L}(\theta)=\bra{\psi(\theta)}(H-\omega)^2\ket{\psi(\theta)},
\end{align}
where $\omega$ is an arbitrary scaler energy value. 
\subsection{VQE with Quantum Natural Gradient}

The landscape of optimization problems encountered in VQE applications is characteristically intricate, often riddled with many local minima. This complexity underscores the necessity of employing an effective optimization strategy, pivotal for the algorithm's successful convergence to the ground state energy of the system under study.

Among various optimization techniques, the QNG optimization strategy, which stands out by using the geometric properties of the parameter space \cite{QNG}. Contrary to the traditional gradient descent method, which operates under the assumption of an Euclidean metric space, QNG employs the Fubini-Study metric tensor, denoted as $g$, to modulate the optimization step sizes. This tensor captures the variational state space's inherent curvature, facilitating more informed and efficacious optimization steps.

The essence of the QNG approach is encapsulated in the update rule:

\begin{equation}
\boldsymbol{\theta}_{\text{new}} = \boldsymbol{\theta} - \eta g(\boldsymbol{\theta})^{-1} \nabla f(\boldsymbol{\theta}),
\end{equation}

where $\boldsymbol{\theta}$ denotes the variational circuit parameters, $\eta$ signifies the learning rate, $g(\boldsymbol{\theta})$ represents the Fubini-Study metric tensor, and $\nabla f(\boldsymbol{\theta})$ is the gradient of the objective function with respect to $\boldsymbol{\theta}$. The objective function $f$ typically corresponds to the expectation value of the Hamiltonian, whose ground state energy the VQE seeks to approximate.

By accounting for the parameter space's geometry, the QNG optimizer significantly enhances the efficiency of the optimization process. It navigates the circuit's sensitivity to parameter variations, circumventing suboptimal pathways often pursued by conventional optimization methods.

Algorithm~\ref{alg:VQE_QNG} delineates the procedural steps for incorporating QNG optimization within the VQE.

\begin{algorithm}
\caption{VQE with QNG}\label{alg:VQE_QNG}
\begin{algorithmic}[1]
\Require Hamiltonian $H$ of the quantum system, variational ansatz $U(\boldsymbol{\theta})$, initial parameters $\boldsymbol{\theta}_0$, learning rate $\eta$
\Ensure Optimized parameters $\boldsymbol{\theta}^*$ minimizing the expectation value $\langle H \rangle_{\boldsymbol{\theta}}$
\State Initialize parameters $\boldsymbol{\theta} \gets \boldsymbol{\theta}_0$
\While{not converged}
    \State Evaluate the objective function $f(\boldsymbol{\theta}) = \langle U(\boldsymbol{\theta})^\dagger | H | U(\boldsymbol{\theta}) \rangle$
    \State Compute the gradient $\nabla f(\boldsymbol{\theta})$ with respect to $\boldsymbol{\theta}$
    \State Compute the Fubini-Study metric tensor $g(\boldsymbol{\theta})$
    \State Update the parameters using QNG:
    $\boldsymbol{\theta} \gets \boldsymbol{\theta} - \eta g(\boldsymbol{\theta})^{-1} \nabla f(\boldsymbol{\theta})$
    \State Check for convergence (e.g., change in $f(\boldsymbol{\theta})$ is below a threshold)
\EndWhile
\State $\boldsymbol{\theta}^* \gets \boldsymbol{\theta}$
\State \textbf{return} $\boldsymbol{\theta}^*$
\end{algorithmic}
\end{algorithm}
