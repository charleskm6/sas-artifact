# Organization of the artifact

Artifact documentation for the paper "Trace Partitioning as an Optimization Problem" submitted to SAS 2024. Here we provide the instructions to reproduce the results presented in the paper, namely all cactus plots in Section 8 "Experimental Evaluation" 

The artifact is a docker image containing the evaluation scripts. The docker image is available on [https://zenodo.org/records/11506965].

## Artifact Organization
The artifact is organized in 3 directories inside the 'sas-exp' directory:

1. **Benchmarks**: All the SVComp C programs of 4 categories mentioned in the paper + instrumented KsHandlers category.
2. **GSR**: Source files of the technique.
3. **Exps**: We have 7 experiments. Each experiment has multiple strategies, and each strategy has a clone of the gsr/ directory. This allows running all strategies of the experiment in parallel.

We also provide 'Dockerfile' used to generate the docker. 

### Running an experiment

Each experiment has three scripts:
1. **run.sh**: 1 hour (3600 seconds) timeout for all programs. This, however, can extend up to 40 days.
2. **run_10s.sh**: 10-second timeout for all programs to verify a part of the cactus under the timelimit.
3. **run_60s.sh**: 60-second timeout for all programs to verify a part of the cactus under the timelimit.


### Config file
Each strategy of every experiment needs a config.json to set the parameters for the GSR search algorithm, and we need the following parameters. 

```
{
  "algo": "Iddfs",
  "k": 1000,
  "domain": "Itv",
  "knobs": {
    "split_k": 1000,
    "unroll_k": 1000,
    "calls_k": 1000,
    "timeout": 3600.0
  },
  "config": {
    "is_incremental": true,
    "is_sequential": true
  }
}
```

1. ***algo***: Here we have three 3 algorithms: IDDDS ("Idddfs"), SDS ("Sds"), Full-Split ("Fs"). 
2. ***k***: The maximum bound used for all three repairs: splitting, unrolling, context-sensitivity
3. ***domain***: Here we have 4 abstract domains: Intervals ("Itv"), Intervals-Cong ("Itv-Cong"), Octagons ("Oct"), Constant propagation ("Cp")
4. ***knobs***: Here, we have 3 different knobs for all three repairs: splitting, unrolling, context-sensitivity
5. ***config.is_incremental***: Flag to use either an incremental fixpoint engine or a non-incremental one
6. ***config.is_sequential***: Flag to prove for one property at a time sequentially or to prove all properties at the same time. 


The provided JSON outlines the default configuration, employing the IDDFS algorithm with a maximum bound of ```k=1000```, using the Intervals domain, setting all repair-related knobs to 1000, and enabling both incremental fixpoint calculations and sequential verification. 


### Machine Specifications
- The artifact has been thoroughly tested on an Ubuntu machine.
- **16 cores** are recommended to run all experiments in parallel.
- Individual experiments may utilize up to **7 cores**.
- For machines with **fewer than 4 cores**, running each Python script sequentially is advised for reproducing results.


### Explicit Note:
The experiments in 'exps/' directory contain cloned versions of 'gsr/' for running things in parallel. No additional optimizations are used for different experiments, and each clone can be verified with contents of the 'gsr/' directory. The only difference is the config.json file tailored for each experiment. 


## Kick-the-Tires:
1. Load the docker file. Find start.sh in the entry directory, and then run './start.sh'. This will initialize opam for the experiments
2. Then head to 'exps/' directory to run each experiment. Each experiment has three files run.sh, run_10s.sh, run_60s.sh, to run all strategies of the experiment in parallel. 


We apply for three badges: Available, Validated, and Extensible

## Available Badge
The tool is available on Zenodo: [https://zenodo.org/records/11506965].

## Validated Badge
The evaluation for the Validated Badge consists of running the experiments corresponding to the following research questions and verifying the cactus plots given in the paper.

For all the research questions below, we have an experiment in the corresponding directory in `exps/`.

### Timeout configurations for full/partial validations
- Configurations for **10 seconds** and **60 seconds** timeouts are provided for partial validation of the cactus plots. The standard 1-hour timeout can take up to 40 days to complete.
- Shorter timeouts are designed to complete within a day


### Execution instructions
- Once any of the experiment scripts (`./run.sh`, `./run_10s.sh`, `./run_60s.sh`) successfully completes, execute `python cactus.py` to generate the cactus plot for that particular experiment from the same directory.
- The PNG image of the cactus plot will be saved within the directory. To review the plots, copy the images from the docker container to your local machine.


### Research Questions:

For experiments related to specific research questions, we utilize strategy-specific parameters as required, while maintaining the default configurations for all other parameters. This approach allows for targeted experimentation and validation of research questions within our framework.



#### Research question: RQ1

##### RQ1.a: Comparision with baselines

	This experiment is located in the "exps/precision/" directory. We provide 4 strategies, of which 3 are baselines which are No-Split, Fs (full-split), and SDS (Synchronized-delay search). The final strategy is IDDDS (Iterative deepening depth-first delay search), and the goal is to compare this strategy with the 3 baselines. 

	For all strategies, only the ```algo``` parameter in the config.json is changed compared to the default configuration. 

##### RQ1.b: Domains

	This experiment is located in the "exps/domains/" directory. Here, we instantiate with 4 strategies corresponding to a numerical domain. We provide 4 such domains: Constant propagation ("Cp"), Intervals ("Intervals"), Intervals + Congruence ("Interval-Cong"), Octagons ("Octagons"). 

	For all strategies, only the ```domain``` parameter in the config.json is changed compared to the default configuration. 

##### RQ1.c: Sensitivity
	This experiment is located in the "exps/combinations/" directory. Here, we have 4 strategies:
		1. All-Repairs: All repairs are considered in a single search
		2. Splitting: Only splits
		3. Unrolling: Only unrollings
		4. Context-Sensitivity: Only context-sensitivity
	
	For all strategies, only the ```knobs``` parameter in the config.json is changed compared to the default configuration. 


#### Research question: RQ2

##### RQ2.a: Impact of bound 'k'
	This experiment is located in the "exps/boundk" directory. Here, we have 7 strategies corresponding to 7 different bounds: k=10, 20, 50, 100, 1k, 10k, 100k

	For all strategies, only the ```k``` and ```knobs``` parameters in the config.json are changed compared to the default configuration. 


##### RQ2.b: Impact of Incremental Solving
	This experiment is located in the "exps/incremental/" directory. Here, we have two strategies: 
	1. Incremental: IDDDS with incremental computation
	2. Non-Incremental: IDDDS without incremental computation.  

	For these two strategies, only the ```config.is_incremental``` parameter in the config.json is changed compared to the default configuration. 


##### RQ2.c: Impact of proving one property at a time
	This experiment is located in the "exps/sequential/" directory. Here, we have two strategies:	
		1. Sequential: Verifying one property at a time
		2. Non-Sequential: Verifying all properties at a time

	For these two strategies, only the ```config.is_sequential``` parameter in the config.json is changed compared to the default configuration. 


##### RQ2.d: Impact of the minimization phase
	In the paper, we do not give any plot for this experiment.

##### RQ2.e: Impact of improving several sensitivities at once
	This experiment is in the "exps/boundk/" with 7 strategies. In the results directory of each strategy, the analysis result of every C file is given as a json file. Here, 'escape_factor' (also known as subsetsize) cell says how much subsetsize is required to verify properties. It was always 1, and we did not have properties which needed subsetsize '2'. We show that subsetsize >=2 is rarely needed.


#### Research question: RQ3

##### RQ3: Comparision against CEGAR approaches
This experiment is in the 'exps/cegar/' directory, and we provide the data of running all the benchmarks on two tools CPA-Checker, Ultimate Automatizer. CPA-Checker has three configurations:
	1. CPA-PA: Predicate abstraction
	2. CPA-VA: Value analysis 
	3. CPA: Default CPA-Checker which uses several portfolio of techniques. 

We compare these results with IDDDS with k=1000 given in the "exps/precision" directory. 



### Validation of results

For validated badge, compare the generated cactus plots with those presented in the paper. Note that the full 3600-second timeout experiment can take up to 40 days to complete. Shorter timeouts are provided for partial validations.



## Extensible Badge 

To evaluate the reusability of GSR 
	(1) run the tool on a custom C program 'C_program.c' in gsr/ directory through the command ```timeout 60s dune exec -- frama-c C_program.c```
	(2) look at the project structure, source code, and documentation.
	(3) Approach takes as input a program and an analysis domain. The domain interface is given in 'src/backend/rdomains.ml'. Domain interface allow interprocedural analysis. 


The Ocaml source code is modular and well-structured as expected for a static analysis tool, allowing the user to add new domains as per the interface



# Source code and documentation

The project is organized as follows:

- `start_analysis.ml`: main entry point of the tool
- `README.md`: How to install and entry point of the tool
- `gsr/src`: directory containing the source code
- `benchmarks/`: directory containing the benchmarks
