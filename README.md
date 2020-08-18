# CU-RJMCMCp 

### Overview 
Often times, researchers need to decide which parameterized model best fits the system they are observing. Model selection is a challenging problem and is further compounded when one must infer the associated parameters for each model. Reversible Jump Markov Chain Monte Carlo (RJMCMC) methods is a trans-dimensional Bayesian algorithm that can be used to simultaneously infer the joint distribution o both the model and model-parameter space. CU-RJMCMCp is a generalized, parallel implementation of RJMCMC that provides researchers with a more accessible, flexible, robust approach to instantaneous model and model parameter space. 

### Objectives 
* Provide a clean and accessible software implementation for users to carry RJMCMC in parallel. 

### Description and Background
CU-RJMCMCp begins an analysis by infering model paramter distributions for each competing model in order that these results may subsequently be used to infer the model space probabilities. Specifically, CU-RJMCMCp first independently forms converged approximations for each of the competing model's parameter joint posterior distribution using the [Stan API](https://mc-stan.org/) interface. The collection of samples corresponding to the converged joint posteriors referred to as "gold standard chains". These gold standard chains are then used in a parallel RJMCMC algorithm, via Message Passing Interace (MPI), to assess the model probabilities. A schematic of this algorithm is shown below: 

<img src="docs/rjmcmcDiagram/rjmcmcDiagram.png" align="center" width="400" />

We assume that users have a general background in Bayesian Statistics and Markov Chain Monte Carlo (MCMC) methods. While not necessary, we beleive it benefits to have an understanding about RJMCMC and refer readers to [Green RJMCMC](how to cite) for a more details about this trans-dimensional algorithm. 

### Installation 
#### Dependencies 
* Python Dependicies (can be installed from [here](https://www.scipy.org/install.html))
	* Matplotlib 
 	* Seaborn
	* Numpy 
* C Dependencies 
	* [Message Passing Interface (MPI)](https://www.open-mpi.org/)
	* [GNU Scientific Library](https://www.gnu.org/software/gsl/)
* Bash
	*bc command 
* cmdstan- See [here](https://mc-stan.org/docs/2_24/cmdstan-guide/cmdstan-installation.html#git-clone.section)  for installation instruction and dependencies. 

### Example 
We now walk through a cannoical example used in RJMCMC literature: deciding if a Poisson or Negative Binomial distribution best fits a collection of observational data. The following steps are required to implement CU-RJMCMCp:

1. **Define Data** 
	Specify the germane observational data within the *data* directory using JSON format (labeled as *data.json*). These data will be referenced by the models written for the Stan API. An example *data.json* file is in within the data directory to aid users. 

2. **Define Models**
	As mentioned prevoiusly, users must define ther models using the stan language. These models should be placed in the *models* directory as *modelname.stan*. The *poisson.stan* and *negativeBinomial.stan* are already defined in the models directory, as an example. 

3. **Define A Config File** 
	A config file is required to run CU-RJMCMCp. This config file specifies the location of cmdstan directory (for example in ~/.cmdstan), the number of cores needed for the Stan API, the number of chains, samples to run, etc. This is specified as *setup.config* in the home directory of CU-RJMCMCp. 

4. **Run CU-RJMCMCp** 
CU-RJMCMp is run in bash as follows:

```bash
sh runParallel.sh
```

CU-RJMCMCp will output the samples for each gold standard chain in the *goldStandardChains* directory. These results are produced by cmdstan. The results of model selection are within the *modelSelection* directory. The chain samples as well as the resulting acceptance probability from the simualation are included within *modelSelection*. We note that users can check MCMC congergence diagnostics by using some of the built-in capabilities of cmdstan. Finally a mapping of the model name and model index will be provided in the home directory as *modelIndex.txt*

5. Visualize Results
Some basic visualization are built into CU-RJMCMCp. Specifically, parameter histogram and parameter trace plots are provided for each model. In addition, the distribtuion of the model probabilities and the acceptance probabilites are also visualized. The plotting function is specified in the config file, but can be run seperately by 

```bash
python plotMCMCResults.py
```

All results are stored in the *pics* directory. 

### References 
Please see our paper [Software X Paper](A link) for more details on the numerical underpinnings of CU-RJMCMCp. In addition to the paper, we present our [derivation and proof](https://github.coecis.cornell.edu/jtc278/cornellResearch/blob/master/CU-RJMCMCp/CU_RJMCMCp_Theoretical_Underpinnings.pdf) of the theoretical foundation that shows that the parallel approach used in CU-RJMCMCp leads to the same joint model and model-parameter distribution as the standard RJMCMC.