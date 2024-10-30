# Running PyPSA-Eur in SOPHIA

This repository includes instructions and tricks to run the [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) model on the [SOPHIA HPC Cluster](https://dtu-sophia.github.io/docs/).

Its main purpose is to help MSc and PhD students install the packages and run simulations with [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/). 

If you encounter a problem (and hopefully also a solution), please, pull request changes to this README file with the solution so that other students can also benefit.

The content of this document is structured as follows:<br>
A [General information about PyPSA-Eur](#general-information-about-pypsa-eur)  <br>
B [Getting on to the cluster](#getting-on-to-the-cluster)  <br>
C [Setting up the cluster](#setting-up-the-cluster)  <br>
D [Running simulations](#running-simulations)<br>
E [Typical errors](#typical-errors-and-options-to-make-your-life-easier)<br>

## A. General information about PyPSA-Eur

[PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) is a model of the European power sector including sector coupling. The model is built with the open-source python module [PyPSA](https://pypsa.readthedocs.io/en/latest/). [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) also uses the Github repository [technology-data](https://github.com/PyPSA/technology-data) to get data on the energy system. Technology-data is a repository including costs, efficiencies, lifetimes, etc. for different technologies.

[PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) uses the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system to run simulations. By using Snakemake simulations, python files can be run with a Snakemake command without having to open them. Snakemake automatically runs all the needed python scripts for a given simulation run. The simulations are configured in the `config.yaml` file. The Snakemake workflow is structured in the `SNAKEFILE`.

There is a [PyPSA mailing list](https://groups.google.com/g/pypsa) where PyPSA-related problems (and solutions) are discussed. You can ask questions there if you have any troubles with the model.

There is also documentation for [PyPSA](https://pypsa.readthedocs.io/en/latest/) and [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/). This repository does not substitute any of the previous information and it only focuses on issues related to running PyPSA-Eur-Sec in SOPHIA.

NOTE: In order to set up anaconda, python, and PyPSA, [these instructions](https://github.com/martavp/MESM_project/blob/master/Instructions_MESM_project.pdf) and the [tutorial for the course project in the MESM course](https://github.com/martavp/MESM_project/blob/master/MESM_project.ipynb) could be useful. 

This [video](https://www.youtube.com/watch?v=ty47YU1_eeQ) provides a nice introduction to PyPSA-Eur. 


## B. Getting on to the cluster

#### 1. Get access to SOPHIA
To use the [SOPHIA cluster](https://dtu-sophia.github.io/docs/), first you need to get a user. You need to ask your supervisor to send [an email](https://dtu-sophia.github.io/docs/account/) requesting access for you.

#### 2. VPN
To connect to SOPHIA you need to be connected to the university network via the [VPN connection](https://www.inside.dtu.dk/en/medarbejder/it-og-telefoni/it-systemer-og-retningslinjer/it-vejledninger-og-retningslinjer/remote/vpn-cisco-anyconnect).

#### 3. Connect with ssh
 You can connect to the cluster through the terminal, e.g.
> ssh user@sophia.dtu.dk

The main way of interacting with the cluster will be through a terminal where you have run the ssh command to connect to SOPHIA. An alternative way of interacting with the cluster is by using the program VSCode as shown below. 

#### 4. Useful commands
Some useful commands to use in the cluster are described in the [Sophia documentation](https://dtu-sophia.github.io/docs/scheduler/).

#### 5. Moving files to/from the cluster
If you are using Windows, [WinSCP](https://winscp.net/eng/download.php) can be useful to copy folders to/from the cluster. Alternatively, use FileZilla on Windows, OSX or Linux.


## C. Setting up the cluster

**The following commands must be run on the cluster.**


#### 1. Installing anaconda/miniconda
You will need to have installed [anaconda/miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html) in your home directory at the cluster. Follow the guide at [anaconda/miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).
Miniconda is a lighter version which works fine (see below in connection with VS Code).

#### 2 Installing PyPSA-Eur
Start by making a folder where you want to install PyPSA-Eur and all that is needed to run it. I would make it in the home directory and call it `projects`

> mkdir projects

Now go into that folder with

> cd projects

Follow the instruction given [here](https://pypsa-eur.readthedocs.io/en/latest/installation.html) carefully. Installation may take a while. 

#### 3. Installing the anaconda environment
You will need to have an conda environment with all the necessary packages. You should have created one when installing [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/installation.html). 

Activate the environment by typing

> .../pypsa-eur$ conda activate pypsa-eur

Every time you log in to the cluster you must activate the environment again. The active environment will be shown in parenthesis in your terminal. 

> (pypsa-eur) [user@sophia1 ~]$

#### 4. Install gurobi 
Install the optimization software [Gurobi](https://www.gurobi.com) in the environment by running the command
> conda install -c gurobi gurobi

### 5. Setting up the gurobi license

The license is managed through a [token server](https://support.gurobi.com/hc/en-us/articles/13264425253265-How-do-I-create-a-token-server-client-license) on the head node. You need to create a file 'gurobi.lic' save it in your home directory in the cluster and write the following text in that file. 

> TOKENSERVER=localhost

#### 7. Configure SNAKEMAKE 

In the folder '/SOPHIA_cluster' of this repository, there is one additional file needed to use snakemake in SOPHIA. 

First, you might want to clone this repository:

> git clone https://github.com/martavp/pypsa-in-sophia.git

Copy the file 'snakemake_cluster' to the directory '.../pypsa-eur/' in your folders in the cluster. 

Then, to run your simulations using Snakemake, you only need to write the following instruction in the command line (jobs identify the number of jobs that you want to parallelize if you send more than one job simultaneously). 

> ./snakemake_cluster --jobs 5


#### 8. Permission
You possibly need to give execution permissions to the snakemake_cluster script. You can do it by the following command:

> chmod u+x snakemake_cluster


#### 9. Using scratch memory for the temporary directory

THIS STEP IS VERY IMPORTANT!! The entire SOPHIA cluster could be slowed down if you do not include this. 

When running simulations the Gurobi solver is constantly reading and writing temporary files. To avoid slowing down the entire SOPHIA cluster, it is very important that temporary storage is used. Read more about [temporary storage in SOPHIA](https://dtu-sophia.github.io/docs/scratch/).

In the file `pypsa-eur-sec/config.yaml` (if that file doesn't exist go to `pypsa-eur-sec/config.default.yaml`) change the setting `tmpdir` under solving to '/tmp'. Make sure the setting is not commented out. It should look like this: 

> solving: 
>   tmpdir: '/tmp'

## D. Running simulations

Congratulations, if you have made it this far you are now ready to run some simulations. 

Start by making a `config.yaml` file by going into the PyPSA-Eur folder and copying the default config file 

> projects/pypsa-eur$ cp config.default.yaml config.yaml

The `config.yaml` file is where all settings regarding the simulation are done. Edit the settings file with a text editor. 

MAKE SURE THAT YOUR `tmpdir` setting is specified as in [the previous step](#10-using-scratch-memory-for-temporary-directory).

When you have made your settings you are now ready to run the simulations using SNAKEMAKE. 
All simulations must be run from the PyPSA-Eur folder. To run the full simulations type the command: 

> ./snakemake_cluster --jobs 5

The `--jobs 5` indicate that 5 jobs can be run in parallel. 

You can also run only parts of the simulation by specifying what rule to run 

> ./snakemake_cluster --jobs 5 prepare_sector_networks

You can take a look at the `SNAKEFILE` where all the rules are defined. For more information about how SNAKEMAKE works take a look at the [documentation](https://snakemake.readthedocs.io/en/stable/).


## E. Typical Errors and options to make your life easier

Here are some solutions to errors that you may encounter when working with PyPSA-Eur on SOPHIA.

This section is copypasted from [pypsa-in-prime](https://github.com/martavp/pypsa-in-prime) and not updated yet.

#### VS Code 

VS Code must be installed on your local computer, not on the cluster.

[Visual Studio Code](https://code.visualstudio.com/) is a handy tool when working on the cluster. It allows you to have your file explorer, [python editor](https://code.visualstudio.com/docs/python/python-tutorial), and terminal in one window. Install the [Remote - SSH extension](https://code.visualstudio.com/docs/remote/ssh) to connect with SOPHIA.

Edit 20/10/2024 by Aleks: Added a set-up for VS Code and Miniconda, including common issues with the license (on the head node, logging in etc). With VS Code, you will always work with an interactive virtual node which will change some of the above steps. See below.

Edit 2/2/2024 by Ebbe: The newest release (v1.86) is only compatible with Linux distributions based on glibc 2.28 or later, and glibcxx 3.4.25 or later, such as Debian 10, RHEL 8, or Ubuntu 20.04. **Currently, this is not fulfilled by PRIME**. I.e., in order to connect to PRIME with SSH, downgrade VS Code version to [v1.85](https://code.visualstudio.com/updates/v1_85). Moreover, to avoid automatic updates of VS Code, set Update Mode to "none" (under File/Preferences/settings/).

If you experience issues with connecting VScode to prime, try setting the option "Remote.SSH: Lockfiles in Tmp" to true (check the box). 

To commit from your prime repository to your GitHub, go to the *source control* and give your commit a name and press ctrl + enter. If you want the commit to be pushed automatically, after having committed, go to settings --> Remote [SSH: prime.eng.au.dk] --> Git --> Post Commit Command --> change "none" to "push"


#### Avoid entering your password when connecting to PRIME

This is not secure and not officially recommended! Using SSH keys is a good idea but best to set a strong password for them. 

On your local computer, generate an ssh key by running:

> (Local path) > ssh-keygen

Press _Enter_ for the default key name. Then _Enter_ for no password, and then _Enter_ again to confirm. A password key is created under *Local path* in the file _"id_rsa.pub"_. Copy the key to the cluster by running the following command:

> ssh-copy-id -i ~/.ssh/id_rsa.pub prime.eng.au.dk

Enter password when prompted. 
 
#### Extending run-time for long jobs

If you need to run jobs that take a longer time to finish than the default 4 days, you can update the 'snakemake_cluster' by adding your preferred time as shown in the example below for 10 dyas:

snakemake .... "sbatch ... --time=240:00:00" "$@"

#### SNAKEMAKE Example
For the ones who have just started using the PRIME cluster with only one rule in the Snakefile, but want to run in parallel with e.g. a range of different inputs, I have added a simple example of how this can be done in the folder _'cluster_test'_. You can modify the _python_script_ and the Snakefile to match it to your application. 

#### Set-up for VS Code with Miniconda
*For the first set-up*:
1. Follow the steps as above: get a user account, possibly use a VPN and ssh into SOPHIA (ideally generate an ssh-key to avoid retyping your password; [see here](https://docs-devel.hpc.ait.dtu.dk/TweakSSH/#key-based-authentication)), generate a projects folder in "/work/users/<username>". [Avoid](https://dtu-sophia.github.io/docs/permanent/#ceph-file-system) using the $HOME directory for anything non-permanent. It makes sense to create an environmental variable $WORK via ```export WORK=/work/users/<username>``` in ~/.bashrc.
2. Add the following to the local ssh config file:
```# !!! Linux/macOS
Host hpc
 ControlMaster auto
 ControlPath ~/.ssh/master-%r@%h:%p
 HostName sophia.dtu.dk
 User $USER
Host hpcx
 ProxyCommand ssh hpc "nc \$(/usr/bin/squeue -u $USER --name=tunnel --
states=R -h -O NodeList,Comment)"
 StrictHostKeyChecking no
 User $USER
Every time you connect / start your day
# !!! Windows
Host hpc
 HostName sophia.dtu.dk
 User $USER
Host sn*
 HostName %h
 ProxyJump hpc
 User $USER
```
3.  (Only Linux/macOS) On the cluster, add the following sbatch script to your home
directory (~), modify the SLURM directives as needed and name it e.g. tunnel.sh :
```#!/bin/bash
#SBATCH --output="%j_tunnel.log"
#SBATCH --partition=workq
#SBATCH --job-name="tunnel"
#SBATCH --time=12:00:00
#SBATCH --ntasks-per-core 1
#SBATCH --ntasks-per-node 8
#SBATCH --nodes=1
# find open port
PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0));
print(s.getsockname()[1]); s.close()')
scontrol update JobId="$SLURM_JOB_ID" Comment="$PORT"
# start sshd server on the available port
echo "Starting sshd on port $PORT"
/usr/sbin/sshd -D -p ${PORT} -f /dev/null -h ${HOME}/.ssh/cluster
```

*For the daily set-up*:
1. Ssh into SOPHIA.
  a. (Linux/macOS) Type sbatch tunnel.sh to start open an ssh tunnel.
  b. (Windows) Request an interactive node with ```srun --partition windq,workq --
  time 07:30:00 --nodes 1 --ntasks-per-node 8 --pty bash``` (modify as
  needed). Keep this terminal window open throughout the day to avoid having to
  re-authenticate. You will see your specific node name as snXYZ , copy it to
  clipboard.
  c. Create a bash script for b (e.g. called `interactive_node.sh` in $HOME containing ```#!/usr/bin/env bash srun --partition windq,workq --time 07:30:00 --nodes 1 --ntasks-per-node 8 --pty bash```, make it executable with ```chmod +x interactive_node.sh```) and execute it with `./interactive_node.sh` from anywhere anytime you start up.
2. Open VSCode and connect to your projects using Remote Explorer tab (on the left
panel in VSCode) with the ssh target:
  a. (Linux/macOS) hpcx and follow the steps presented by VSCode.
  b.  (Windows) Press + button type in ssh snXYZ with the node name that you got
  previously, refresh the list of hosts, connect to the one snXYZ that you just added
  and follow the steps presented by VSCode.
  c. Open working folder.

*Installation of mamba and PyPSA-Eur on VS Code*:
Don't use the login node for any operations (in particular with VS Code, as it sometimes creates stray processes).
1. Install mamba as this is the preferred option in the PyPSA documentation: Following [Mamba's read the docs](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html) go the [miniforge github](https://github.com/conda-forge/miniforge?tab=readme-ov-file#install) and follow the Linux instructions: I used the wget lines. After installation you can activate mamba with ```eval "$(/home/<username>/miniforge3/bin/conda shell.bash hook)"``` in the same shell or you can add the same line to your ~/.bashrc file to start conda/mamba automatically.
2. Install PyPSA-Eur from its Git repository in $WORK. Move the `snakemake_cluster` file from pypsa-in-sophia/SOPHIA_cluster into the pypsa-eur folder for submitting jobs to the cluster. Ensure that it is executable with `chmod u+x snakemake_cluster`. 
3. Activate the pypsa-eur environment via `conda activate pypsa-eur`.
4. In the pypsa-eur environment install gurobi via `mamba install -c gurobi gurobi`; the Gurobi license is a token license on Sophia. In order to access it, create a file `gurobi.lic` in the home directory with the contents `TOKENSERVER=sophia1.hpc.ait.dtu.dk` and add an environment file `GRB_LICENSE_FILE=~/gurobi.lic` to your .bashrc file (remember to source it). The token server is on the head node and we have to work on virtual interactive nodes (afaik) so the usual `TOKENSERVER=localhost` didn't work for me.
5. Run simulations with snakemake, via `./snakemake_cluster --jobs N --configfile config/config.yaml` and possibly other options.
6. IMPORTANT: Ensure to use temporary storage on Sophia. In the config file (whichever you are using, e.g. `config/config.default.yaml` is a good place to ensure this for all child configs) under solving set `tmpdir` to `/tmp'.

##### Good to know/misc
1. Check whether VS Code processes are running: ```watch pgrep -u "$USER" vscode-server```
2. Killing VS Code processes:
  a. through VS Code: Press F1 to open the command pallete, run Remote - SSH: Kill VS Code Server on Host...
  b. through the terminal: ```pkill -u "$USER" vscode-server``` or ```killall -u "$USER" vscode-server```.
3. Git might not be activated by default: ```module load git``` and check with ```git --version```.


