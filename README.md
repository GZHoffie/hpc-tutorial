Usages of the HPC Cluster
=========================
[Link to github](https://)
[Documents for SJTU Pi Supercomputer](https://docs.hpc.sjtu.edu.cn/)

Resources
---------
#### Different kinds of nodes

Resource Type | Serial Queue | CPU Queue | CPU128 Queue | FAT Queue | k20 / k40 Queue | k80 / p100 Queue 
:-------------|:------------:|:---------:|:------------:|:---------:|:-------------:|:--------------:
Description | 1 core, 4GB | 16 cores, 64GB | 16 cores, 128GB | 16 cores, 256GB | 2 GPUs | 2 GPUs 
Price | 1 | 16 | 24 | 32 | 20 | 40

Price unit: points per node per hour.

#### Storage
Each account have **2TB** of space for free
Exceeding storage: pay **166** points / TB / month.

#### Operating System 
CentOS Linux release 7.3.1611 (Core) 

Login to Cluster with Linux/Unix/Mac
----------------
0. Get an SJTU VPN, following the instruction from ``http://net.sjtu.edu.cn/wlfw/VPN.htm``. Connect to SJTU VPN.

1. SSH login node: 202.120.58.241 or 202.120.58.242 or 202.120.58.243.
SSH Port: 22.

```bash
$ ssh umjpw-3@202.120.58.241
```

And enter the password.

2. Once succeeded, you can use ssh key authentication to login without a password.

```bash
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa_hpc
$ ssh-copy-id -i ~/.ssh/id_rsa_hpc.pub umjpw-3@202.120.58.241
```

You can let the passphrase be empty. Otherwise you may need to enter passphrase everytime you connect to hpc.

3. You may add the following to ~/.ssh/config so that you can login simply with ``ssh hpc`` in the future.

```
Host hpc
        HostName 202.120.58.241
        User     umjpw-3
```

4. You can then upload files using the following

```bash
$ # if you want to copy directories
$ scp -r SOURCE_DIR/ hpc:TARGET_DIR_NAME/
$ # if you want to copy files
$ scp SOURCE_FILE hpc:TARGET_DIR_NAME/TARGET_FILE_NAME
```

Login to Cluster with Windows
----------------
0. Get an SJTU VPN, following the instruction from ``http://net.sjtu.edu.cn/wlfw/VPN.htm``. Connect to SJTU VPN.

1. Download PuTTY from ``putty.org``.

2. Open PuTTY, fill in the Host Name as ``202.120.58.241``, and press ``Open``. 

3. A terminal will appear and prompt you with ``login as:``, and you will need to enter ``umjpw-3``. Then enter the password.

4. To transfer files, download WinSCP from ``https://winscp.net/eng/index.php``. Install and keep everything in default settings. Enter Host Name, password accordingly. You can then transfer files by simply dragging your files to the target directory.


Environments
------------

#### Python Virtual Environment
To install packages for python, you will need to create a virtual environment using the following steps:

1. Create a virtual environment of python3 using

```bash
$ python3 -m venv NAME_OF_ENVIRONMENT
```

2. Activate the virtual environment using

```bash
$ source NAME_OF_ENVIRONMENT/bin/activate
```
You will then see the shell's prompt changing to the name of currently used virtual environment, ``(NAME_OF_ENVIRONMENT) [umjpw-3@login1 ~]$ ``.

3. Now, you may install packages using ``pip`` and run your python code using ``python YOUR_CODE.py``.

#### Python
1. HPC provides different environment modules.
We load ``miniconda3`` here.

```bash
$ module avail
$ module load miniconda3/4.5
```

2. Create a conda virtual environment with desired python version and dependencies.
Here we take ``tensorflow`` as an example.

```bash
$ conda create --name tf-py3-cpu pip python=3.6
$ source activate tf-py3-cpu
$ pip install tensorflow
```

#### Benchmarking Environment for VE490
A set of scripts that install packages including
* roboschool
* stable-baselines

1. Clone AAAL git repository.
2. Switch to the ``hpc`` branch.
3. Run ``setup.bash``, ``install.bash``, ``environment.bash`` one by one.
4. Activate the conda virtual environment.
```bash
$ source activate.sh
```

#### Octave
1. Install ``spack`` via git.

```bash
$ git clone https://github.com/sjtuhpcc/spack.git
$ cd spack
$ ./bootstrap.sh user --install
```

2. Add the following sets to ``~/.bashrc``.

```bash
# Spack package management
if [ -d "$HOME/spack" ]; then
    export SPACK_ROOT=$HOME/spack
    source $SPACK_ROOT/share/spack/setup-env.sh
fi
```

3. Compile ``octave`` from source using ``gcc@5.4.0`` spec.

```bash
$ module load gcc/5.4
$ spack install octave %gcc@5.4.0 ^openblas threads=openmp
```

Job Submission
--------------
#### SLURM Script
A ``bash`` script used to specify job configures.
Example:
```bash
# run.slurm

#SBATCH --job-name=hostname
#SBATCH --partition=cpu
#SBATCH -n 1
#SBATCH --mail-type=end
#SBATCH --mail-user=...
#SBATCH --output=%j.out
#SBATCH --error=%j.err
#SBATCH --time=00:00:10

/bin/hostname
```

#### Commands
Command  | Function
---------|---------
sinfo    | Cluster state
squeue   | Queued job state
sbatch   | Job submission
scontrol | Monitor and modify jobs
sacct    | Reports for completed jobs
sreport  | Reports for job usage and cluster utilization
scancel  | Job deletion
sview smap | SLURM UI

```bash
$ sbatch run.slurm
$ squeue -u umjpw-2
```
