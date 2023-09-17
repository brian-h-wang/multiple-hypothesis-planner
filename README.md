# multiple-hypothesis-planner

Code for the paper [Multiple-Hypothesis Path Planning with Uncertain Obstacle Detections](https://arxiv.org/abs/2308.07420), by Brian H. Wang, Beatriz Asfora, Rachel Zheng, Aaron Peng, Jacopo Banfi, and Mark Campbell. Paper available as arxiv preprint.

# Installation

Recommended: Use the included `environment.yml` file to set up a Conda environment for this project.

```commandline
conda env create -f environment.yml
```

After setting up the environment, run the Python `setup.py` file.

```commandline
conda activate mhp
python setup.py install
```

# Usage


## Run a planner simulation

See `scripts/run_simulation.py` for an example script that runs the planner in a generated forest environment. This script saves a video to visualize the planner behavior.

A forest must first be generated by initializing an instance of the `planner.forest_generation.PoissonForest` class - the example script uses the `PoissonForestUniformRadius` class which inherits the base `PoissonForest` class and randomly generates trees with radii uniformly sampled within a minimum and maximum bound. Then, create an instance of `planner.simulation.MultipleHypothesisPlannerSimulation` or `planner.simulation.BaselineSimulation` to use either the multiple-hypothesis or baseline planner. The planner can be run by calling the `update()` method of the Simulation object.


## Run the planner comparison experiment

The script `experiments/run_comparison_experiment.py` can be used to randomly generate many forest environments, and evaluate the multiple-hypothesis planner compared against the baseline A* planner.

In order to start the experiment, with the `mhp` conda environment activated, run

```commandline
python run_comparison_experiment.py -o results/
```

The `-o` flag can be used to specify an output directory for saving the results files. Other arguments to this script set the obstacle density, distribution of obstacles, and planner desired safety level.

In order to regenerate all results from the paper, run the following commands. **Note these will take a long time to run. These commands can be run in multiple terminals in order to generate the results more quickly.**

```commandline
# Run commands from the experiments directory.
# Make sure you've run setup.py!
cd experiments

# Experiments in forests with uniformly distributed obstacles:
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -ld  # low obstacle density
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -hd  # high obstacle density

# Experiments in nonuniform forests with Gaussian distributed clusters of obstacles
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c -ld
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c -hd

# Experiments with multiple-hypothesis planner using higher desired safety threshold
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -hs
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -hs -ld
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -hs -hd

# Experiments in nonuniform forests with Gaussian distributed clusters of obstacles
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c -hs 
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c -hs -ld
python run_comparison_experiment.py -o results/results_from_paper -hyp 0 1 2 3 5 -c -hs -hd
```

The experiments will be written to multiple .txt files separated by number of planner hypotheses/baseline planner, forest layout (clusters or uniform), and high or low safety threshold setting. Each row in the output files is formatted as

```
forest_number n_hypotheses simulation_status elapsed_time
```

where `forest_number` is the number of the randomly generated forest environment, `n_hypotheses` is the maximum number of hypotheses used by the planner (0 for the baseline), `simulation-status` is one of "success", "stopped", "crashed", or "timeout", and `elapsed_time` is the time when the robot reached the goal, timed out, or crashed.