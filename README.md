# Savio Server

## Login

```[bash]
ssh chawins@hpc.brc.berkeley.edu
pwd> (PIN)(OTP)
```

[link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/logging-brc-clusters/)

## Hardware Configuration

See what machines I can run my jobs on

```[bash]
sacctmgr -p show associations user=$USER

Cluster|Account|User|Partition|Share|Priority|GrpJobs|GrpTRES|GrpSubmit|GrpWall|GrpTRESMins|MaxJobs|MaxTRES|MaxTRESPerNode|MaxSubmit|MaxWall|MaxTRESMins|QOS|Def QOS|GrpTRESRunMins|
brc|fc_wagner|chawins|savio2_1080ti|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio3|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio_bigmem|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio2_knl|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio2_htc|1|||||||||||||savio_debug,savio_long,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio2_gpu|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio2|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio3_bigmem|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio|1|||||||||||||savio_debug,savio_normal|savio_normal||
brc|fc_wagner|chawins|savio2_bigmem|1|||||||||||||savio_debug,savio_normal|savio_normal||
```

Hardware details: [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/hardware-config/). Summary:

### Savio1

- `savio`: 160 nodes with 64 GB of 1866 Mhz DDR3 memory
- `savio_bigmem`: 4 nodes are configured as "BigMem" nodes with 512 GB of 1600 Mhz DDR3 memory

### Savio2

- `savio2`: 163 nodes with 64 GB of 1866 Mhz DDR3 memory
- `savio2_bigmem`: 20 nodes are configured as "BigMem" nodes with 128 GB of 2133 Mhz DDR4 memory
- `savio2_htc`: high-throughput computing, 3.4 Ghz clock speed, but fewer - 12 instead of 24 - core count Intel Haswell processors and 128 GB of 2133 Mhz DDR4 memory
- `savio2_gpu`: 17 nodes with 8 cores (3.0 Ghz) and 4 Nvidia K80 GPUs (2 dual boards) each
- `savio2_1080ti`: 7 nodes with 4 Nvidia GTX 1080ti GPUs each  

### Savio3

- `savio3`: 116 nodes with Skylake processor (2x16 @ 2.1 GHz). 96 GB RAM
- `savio3_bigmem`: 16 nodes with Skylake processor (2x16 @ 2.1GHz). 384 GB RAM.
- `savio3_xlmem`: 2 nodes with Skylake processor (2x16 @2.1 GHz). 1.5 TB RAM
- `savio3_gpu` (may not be up-to-date; for updated info, see [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/submitting-jobs/#gpu-jobs)):
  - 2 node with 2 Nvidia Tesla V100 GPUs
  - 5 nodes with 4 Nvidia GTX 2080ti GPUs
  - 3 nodes with 8 Nvidia GTX 2080ti GPUs

## How to submit jobs

Commands and scripts for submitting jobs: [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/submitting-jobs/)). For GPU-specific detail, see [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/submitting-jobs/#gpu-jobs).

Run a bash script using `sbatch myjob.sh`. Example bash script:

```[bash]
#!/bin/bash
#SBATCH --job-name=test
#SBATCH --account=fc_wagner
#SBATCH --partition=savio2_1080ti
# Number of nodes:
#SBATCH --nodes=1
# Number of tasks (one for each GPU desired for use case) (example):
#SBATCH --ntasks=1
# Processors per task (please always specify the total number of processors twice the number of GPUs):
#SBATCH --cpus-per-task=2
#Number of GPUs, this can be in the format of "gpu:[1-4]", or "gpu:K80:[1-4] with the type included
#SBATCH --gres=gpu:1
#SBATCH --time=00:03:00
#SBATCH --output slurm-%j-exp54.out
## Command(s) to run:
python myscript.py
```

Notes

- More examples: [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/scheduler-examples/). Pay particular attention to `savio3_gpu`.
- Cost for Savio: [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/scheduler-config/).
  - Currently, the `savio2_htc`, `savio2_gpu`, and `savio2_1080ti` pools (partitions) offer per-core scheduling of jobs. Most of the others give exclusive access, meaning that you will charged for the entire node.
  - `savio2_gpu`: 2.67 (5.12 / GPU)
  - `savio_1080ti`: 1.67 (3.34 / GPU)
- Note that if `--cpus-per-task` is fewer than the number of cores on a node, your job will not make full use of the node.

## Useful Commands

- `check_usage.sh -E`: check remaining credits
- `scancel JOB_ID`: cancel job
- `squeue -u $USER`, `sq`: check the current job queue
- `sinfo`: check server status
- `squeue -p <partition_name> --state=PD -l`: look at queue of a particular partition

## Storage

- `HOME: /global/home/users/chawins`: limited to 10 GB (code).
- `GROUP: /global/home/groups/`: limited to 30/200 GB. Don't see one for `fc_wagner`.
- `SCRATCH: /global/scratch/chawins`: unlimited but deleted after 6 months (large files).

## Using Python (conda and ML stuff)

- See [this link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/software/using-software/using-python-savio/#using-python-on-savio).
- CUDA version: 11.2 (Oct 2021)

### Conda Error

Error message:

```[bash]
CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://repo.anaconda.com/pkgs/main/linux-64/current_repodata.json>
```

Fixes that do not work:

- https://github.com/conda/conda/issues/9948

```[bash]
chmod -R 777 miniconda3
find miniconda3/ -type f -exec touch {} +
```

- Removing proxy servers.

Fixes that I have not tried

- Downgrade `conda`.

### Final Conda Setup

Run the following commands to install and start up `conda` env. This installs `conda` in `scratch` directory (see [link](https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/software/using-software/using-python-savio/#conda)).

```[bash]
module load python
conda create -p /global/scratch/users/$USER/pt python=3.8
source activate /global/scratch/users/$USER/pt
```

- Later you can put the lines 1 and 3 above in `.bashrc`.

Common packages to install

```[bash]
# conda install pytorch==1.9 torchvision==0.10 cudatoolkit=11.1 -c pytorch -c conda-forge
conda install -y pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch
conda install -y scipy pandas scikit-learn pip
conda upgrade -y numpy scipy pandas scikit-learn
```
