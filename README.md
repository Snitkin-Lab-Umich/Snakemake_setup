# Snakemake_setup
Setup information for snakemake pipelines 

Written by Sophie Hoffman & Zena Lapp.

## Useful links
Snakemake:
- [Snakemake documentation](https://snakemake.readthedocs.io/en/stable/)
- [Short overview](https://slides.com/johanneskoester/snakemake-short#/)
- [More detailed overview](https://slides.com/johanneskoester/snakemake-tutorial#/)

## Benefits of snakemake
- Your analysis is reproducible.
- You don't have to re-perform computationally intensive tasks early in the pipeline to change downstream analyses or figures.
- You can easily combine shell, R, python, etc. scritps into one pipeline.
- You can easily share your pipeline with others.
- You can submit a single slurm job and snakemake handles submitting the rest of your jobs for you.

## Conda

Conda is useful because it allows you to run your snakemake pipeline in an environment with the pipeline's dependencies. 

To [download miniconda](https://docs.conda.io/en/latest/miniconda.html) for linux if you don't already have it:
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-MacOSX-x86_64.sh
```

Creating a conda environment is easiest with a yaml file. An example of a yaml file called `snakemake_env.yaml`: 
```
name: snakemake_environment #name of the environment
#include your pipeline dependencies here
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies: 
  - snakemake=5.5.4
  - ariba=2.14.4
```
To create and activate the conda environment:
```
conda env create -f snakemake_env.yaml # you only have to do this once
conda activate snakemake_environment # you have to do this every time 
```

## A few basics

Useful snakemake arguments:
- `snakemake -n` dry-run (to test it out before running it)
- `snakemake` runs the pipeline
- ` snakemake --dag | dot -Tsvg > dag.svg` creates a dag (this can be super difficult to read with large complex pipelines)

## Running the snakemake pipeline on the cluster
The best way to run snakemake is to add your snakemake command to an .sbat file to submit as a job. For example this file called `snakemake.sbat`: 
```
#!/bin/sh
# Job name
#SBATCH --job-name=snakemake
# User info
#SBATCH --mail-user=UNIQNAME@umich.edu
#SBATCH --mail-type=BEGIN,END,NONE,FAIL,REQUEUE
#SBATCH --export=ALL
#SBATCH --partition=standard
#SBATCH --account=esnitkin1
# Number of cores, amount of memory, and walltime
#SBATCH --nodes=1 --ntasks=1 --cpus-per-task=1 --mem=1g --time=10:00:00
#  Change to the directory you submitted from
cd $SLURM_SUBMIT_DIR
echo $SLURM_SUBMIT_DIR

# Load modules

# Job commands
snakemake --latency-wait 90 --profile config -s snakefile 
```

To run the snakemake pipeline on the cluster, you have to:
- Modify your email address in `snakemake.sbat`
- Modify your email in the config file

Then run:
```
conda activate snakemake_environment # if you're not already in the snakemake_environment conda environment
sbatch snakemake.sbat
```

You can check your job using:
```
squeue -u UNIQNAME
```

For a more detailed walkthrough of snakemake pipelines: 
- [ARIBA snakemake pipeline](https://github.com/Snitkin-Lab-Umich/ariba_snakemake)
- [Genome downloading pipeline](https://github.com/Snitkin-Lab-Umich/genome_downloading_snakemake)
