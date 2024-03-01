# Crossing The Gap Using VQE: A comparative study

Within the evolving domain of quantum computational chemistry, the Variational Quantum Eigensolver (VQE) has been identified as a crucial algorithm for exploiting the capabilities of near-term quantum computers. This work investigates the proficiency of variational quantum algorithms, particularly adaptive VQE techniques, and Quantum Natural Gradient (QNG) optimization, in calculating the spectral gap of chemical molecules—a key determinant of material characteristics and chemical behavior. Our methodology introduces different approaches to estimating the k-th excited state by mapping highly excited states to the ground state via unitary transformations, broadening VQE's applicability.
Our research focuses on analyzing three molecular systems, $H_2$, $LiH$, and $BeH_2$, to demonstrate the versatility and precision of our proposed methods. A significant highlight of our study is the implementation of QNG optimization, which markedly improves the optimization process's efficiency. It consistently minimizes the ground state energy while achieving convergence in fewer iterations than traditional gradient descent optimizers. This efficiency underscores the advantage of QNG in navigating the complex optimization landscape of quantum parameters more effectively.
Additionally, our comparative analysis reveals that both subspace variational quantum eigensolver (SS-VQE) and quantum variational deflation (VQD) methods exhibit strong performance in spectral gap determination, with less than 1\% relative error for $LiH$, and notably, VQD achieves an exceptional 99.99999\% accuracy. Lastly we explore Pauli tapering and the usage of a tensor network ansatz to improve algorithm speed.


## Methodology
### Subspace-Search Variational Quantum Eigensolver
The Subspace-Search Variational Quantum Eigensolver (SS-VQE) is an algorithm designed to address the challenge of calculating excited states [[1]](https://journals.aps.org/prresearch/abstract/10.1103/PhysRevResearch.1.033062). SS-VQE efficiently explores a low-energy subspace to identify the k-th excited state by utilizing orthogonal input states and leveraging unitary transformations. It is worth noting that this method involves only two parameter optimization steps and eliminates the necessity for ancilla qubits. SS-VQE further generalizes all excited states up to the k-th through a single optimization procedure. Through careful parameter optimization, this approach ensures the orthogonality of input states and accurately maps them to energy eigenstates.
This approach minimize the following loss function

```math
\mathcal{L}_w(\theta) = w\bra{\psi_k}U^\dagger(\theta)HU(\theta)\ket{\psi_k} +\sum_{j=0}^{k-1}\bra{\psi_j}U^\dagger(\theta)HU(\theta)\ket{\psi_j}
```

where the cost $\mathcal{L}_w$ gets its global optimum, the circuit $U(\theta)$ maps $\ket{\psi_k}$ to the k-th excited state $\ket{E_k}$ of the Hamiltonian(H) and others to the
subspace spanned by $`\sum_{j=0} E_j`$ with $j$ bounded from above by $k-1$.

### Variational Quantum Deflation

The potential of VQE for near-term quantum computing is generating excitement for the advancement of computational capabilities. [[2]](https://quantum-journal.org/papers/q-2019-07-01-156/) study aims to enhance VQE's functionality by efficiently identifying excited states. By incorporating ``overlap'' terms into the optimization function and leveraging Hermitian matrices, which consist of a complete set of orthogonal eigenvectors, this work demonstrates a cost-effective approach. Utilizing VQE's ability to maintain classical parameters, low-depth quantum circuits are employed to compute these overlap terms. This methodology maintains the same qubit count as VQE for ground-state calculations, with only a minor increase in measurements. In contrast to existing methods for computing excited states in quantum computing, this approach minimizes resource overhead. The following loss function has been provided over this work. 
```math
\mathcal{L}(\lambda_k) =  \bra{\psi(\lambda_k)}H\ket{\psi(\lambda_k)} +\sum_{i=0}^{k-1} \beta_i |\braket{\psi(\lambda_k)|\psi(\lambda_i)}|^2
```

### Folded Spectrum VQE

Expanding on the VQE, [[3]](https://arxiv.org/abs/2305.04783v1) study introduces a specialized method for calculating molecular excited states. By utilizing the Folded Spectrum (FS) approach, we restructure the Hamiltonian's eigenspectrum to specifically target highly excited states. While FS has been acknowledged in the past, its quantum application was previously considered too costly due to the exponential growth of terms in the measured operator. Nevertheless, our implementation reveals a significant advancement by employing a Pauli grouping technique, which can significantly reduce the number of required measurements, making FS a cost-efficient option. This technique has a particularly notable impact on second quantized molecular Hamiltonians, thanks to their distinct structural properties. The loss function for this procedure is 
```math
\mathcal{L}(\theta)=\bra{\psi(\theta)}(H-\omega)^2\ket{\psi(\theta)},
```
where $\omega$ is an arbitrary scaler energy value. 
### VQE with Quantum Natural Gradient

The landscape of optimization problems encountered in VQE applications is characteristically intricate, often riddled with many local minima. This complexity underscores the necessity of employing an effective optimization strategy, pivotal for the algorithm's successful convergence to the ground state energy of the system under study.

Among various optimization techniques, the QNG optimization strategy, which stands out by using the geometric properties of the parameter space \cite{QNG}. Contrary to the traditional gradient descent method, which operates under the assumption of an Euclidean metric space, QNG employs the Fubini-Study metric tensor, denoted as $g$, to modulate the optimization step sizes. This tensor captures the variational state space's inherent curvature, facilitating more informed and efficacious optimization steps.

The essence of the QNG approach is encapsulated in the update rule:

```math
\boldsymbol{\theta}_{\text{new}} = \boldsymbol{\theta} - \eta g(\boldsymbol{\theta})^{-1} \nabla f(\boldsymbol{\theta}),
```

where $\boldsymbol{\theta}$ denotes the variational circuit parameters, $\eta$ signifies the learning rate, $g(\boldsymbol{\theta})$ represents the Fubini-Study metric tensor, and $\nabla f(\boldsymbol{\theta})$ is the gradient of the objective function with respect to $\boldsymbol{\theta}$. The objective function $f$ typically corresponds to the expectation value of the Hamiltonian, whose ground state energy the VQE seeks to approximate.

By accounting for the parameter space's geometry, the QNG optimizer significantly enhances the efficiency of the optimization process. It navigates the circuit's sensitivity to parameter variations, circumventing suboptimal pathways often pursued by conventional optimization methods.



### Exploring algorithm speed improvement
In addition to using QNG to reduce the number of iterations for convergence, we apply Pauli tapering, leveraging $Z_2$ molecular hamiltonian symmetries to reduce the number of qubits required for the VQE simulation. Additionally, we test the use of the MERA tensor network ansatz \cite{PhysRevLett.101.110501} and find that it greatly improves the speed at which a SSVQE custom algorithm finds the first excited state at the expense of accuracy at certain atomic separation values.


## Results
See [Paper](https://github.com/jsaroni/QHack_2024_Qjins_Project/blob/main/Paper.pdf) or [Presentation](https://github.com/jsaroni/QHack_2024_Qjins_Project/blob/main/Presentation.pptx).

## Conclusion

Our investigation into advanced variational quantum algorithms, notably SS-VQE, VQD, Folded Spectrum VQE, and VQE with QNG, illuminates the path forward in quantum computational chemistry. These methodologies, each with its unique approach to navigating the challenges of quantum optimization, collectively underscore a significant leap toward harnessing quantum computing's potential for chemical and material science.
SS-VQE and VQD have demonstrated robust capabilities in accurately determining the spectral gap of chemical molecules, a critical parameter influencing material properties. With precision that approaches near-exactness, particularly noted in the spectral gap analysis of LiH with less than 1\% relative error, these strategies highlight the precision achievable with quantum computational approaches.

Folded Spectrum VQE further extends the versatility of quantum algorithms to identify highly excited states, offering a broader understanding of molecular dynamics and energetics. By facilitating a comprehensive view of the energy spectrum, this approach enriches our ability to predict and manipulate chemical behaviors at a quantum level.

Most notably, QNG stands out for its efficiency, significantly reducing the number of iterations required for convergence. By intelligently navigating the optimization landscape, QNG enhances computational efficiency and sets a new standard for precision in quantum computing optimizations.
Looking ahead, the convergence of these advanced quantum computational methods opens new horizons for exploring chemical molecules in unprecedented detail. This study's remarkable accuracy and efficiency beckon a future where quantum computing plays a central role in unraveling complex chemical mysteries, potentially revolutionizing our approach to material synthesis, drug discovery, and beyond. As we refine these algorithms and adapt them to more complex systems, the promise of quantum computational chemistry to contribute meaningful insights into the natural world becomes increasingly tangible.




## Contributors
**I-Chi Chen ([@ichen17](https://github.com/ichen17))**<br>
**Nouhaila Innan ([@innanov](https://github.com/innanov))**<br>
**Suman Kumar Roy ([@roysuman088](https://github.com/roysuman088))**

## Requirements
To run the code, install the requirements as follows, <br>

`!pip install pennylane`<br>
`!pip install pennylane-qchem`<br>
`!pip install matplotlib`

## References
[1] K. M. Nakanishi, K. Mitarai, and K. Fujii, “Subspace-search variational quantum eigensolver for excited states,” Phys. Rev.
Res., vol. 1, p. 033062, Oct 2019. [Online]. Available: https://journals.aps.org/prresearch/abstract/10.1103/PhysRevResearch.1.033062 <br>
[2] O. Higgott, D. Wang, and S. Brierley, “Variational Quantum Computation 
of Excited States,” Quantum, vol. 3, p. 156, Jul. 2019. [Online]. Available: https://doi.org/10.22331/q-2019-07-01-156 <br>
[3] L. C. Tazi and A. J. W. Thom, “Folded spectrum vqe : A quantum computing method for the calculation of molecular excited states,” 2023.  <br>
[4] J. Stokes, J. Izaac, N. Killoran, and G. Carleo, “Quantum natural gradient,” Quantum, vol. 4, p. 269, May 2020.  <br>
[5] H. R. Grimsley, S. E. Economou, E. Barnes, and N. J. Mayhall, “An adaptive variational algorithm for exact molecular simulations on a
quantum computer,” Nature Communications, vol. 10, July 2019.  <br>
[6] G. Vidal, “Class of quantum many-body states that can be efficiently simulated,” Phys. Rev. Lett., vol. 101, p. 110501, Sep 2008. [Online].
Available: https://link.aps.org/doi/10.1103/PhysRevLett.101.110501

