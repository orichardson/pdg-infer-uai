See [OpenReview](https://openreview.net/forum?id=XIn-DFFR2zO) for the full paper
and supplementary material. 

# Inference in Probabilistic Dependency Graphs

Connects the 
[PDG Python library](https://github.com/orichardson/pdg)
to the various convex solvers
([MOSEK](), [ECOS]()) via
the `[cvxpy](https://www.cvxpy.org/)`
library for disciplined convex programming (DCP).


This repository contains a file 
[interior_pt.py] 
that implements four important methods.
The first two compute observational semantics of the PDG. 

* `cvx_opt_joint( M : PDG,  also_idef=True,  ...)`
	accepts an object of type `PDG` (see [the PDG library](https://github.com/orichardson/pdg)),
	and returns an object of type `RawJointDistribution`, an explicit tensor representation of a joint
	representation of a joint distribution $\mu(\mathcal X)$ over all variables.
	
	The boolean parameter `also_idef` determines whether the resulting distribution is simply a distribution that is maximally
	consistent with of the (observational) probabilities, or
	whether it returns the unique distribution $[[ M ]]^*$ 
	in this set that best reflects the structural information of the PDG, among 
	these observationally optimal distributions.

* `cvx_opt_clusters( M : PDG,  also_idef=True, ...)`
	is similar to `cvx_opt_joint`, except that it runs on a clique tree representation,
	and hence can be more compact. A clique tree will be found with the methods of
		[pgmpy](https://pgmpy.org/models/junctiontree.html) by default, but a tree
		decomposition of the PDG `M` can also be calculated manually.

The other two methods do $\gamma$-inference for $\gamma > 0$,
which incorporates the structural information more than simply to break ties.

* `cccp_opt_joint(M : PDG, gamma=1)`
	formulates the optimization problem to do $\gamma$-inference for $\gamma \ge 0$,
	although it is a slightly less efficient way of using constraints when $\gamma = 0$. 

* `cccp_opt_clusters(M : PDG, gamma=1)`
	is the analogue of the above that uses a clique tree representation.

The names of both methods comes from the
Convex-ConCave Procedure (CCCP), which is implemented in both,
and comes into play when $\gamma$ is large enough that there is some arc $a$ such 
that the structural confidence $\gamma \alpha_a$ exceeds the observational confidence $\beta_a$.
In this case, we cannot guarantee the prob lem is a disciplined convex program of the
form we exploit, but nonetheless can find some minimizing distribution through the CCCP.

Note that the CCCP is insufficient for inference, as we 
 have defined it, in the case where
the objective is not quasi-convex.
This is because it returns just one (locally) optimal distribution, which may 
	be neither globally optimal nor unique.
For more information, see [supplementary material](https://openreview.net/attachment?id=XIn-DFFR2zO&name=supplementary_material), Appendix B.
