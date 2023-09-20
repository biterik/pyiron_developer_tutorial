# 1. Introduction

Let’s say you found a bug<sup>1</sup> in Pyiron and want to fix it yourself or understand what happens in the background when you run a function. First, you need to have a ready workflow configuration and to understand the structure of Pyiron.

- In this tutorial, The goal <b> is to add your name to the file "developers list" in this directroy "" (that contains this file)</b> as an example of the workflow needed to introduce changes into Pyiron.
- This tutorial also assumes you have a Windows operating system with a Windows subsystem for Linux (WSL) installed and that you use to log into the cluster (cmti001 login node). Modifications regarding other working configurations will be mentioned as notes for the case-specific steps.

## 1.1 Different Pyiron Modules

Pyiron exists as a bunch of files (aka modules) with code written in them that call/communicate with each other on a remote computer (aka hosting server).

* [pyiron](https://github.com/pyiron/pyiron) - A meta package which seamlessly loads all the pyiron plugins. 
* [pyiron_base](https://github.com/pyiron/pyiron_base) - A package for the core components e.g. job management, data storage, and resource management. 
* [pyiron_atomistics](https://github.com/pyiron/pyiron_atomistics) - An interface to atomistic simulation codes including but not limited to GPAW, LAMMPS, S/Phi/nX, and VASP. 
* [pyiron_contrib](https://github.com/pyiron/pyiron_contrib) - A package to collect contributions from the pyiron community.
* [pyiron_gpl](https://github.com/pyiron/pyiron_gpl) - A package for all interfaces that require a GPL license in contrast to the BSD license used by all other pyiron packages. 
* [pyiron_continuum](https://github.com/pyiron/pyiron_continuum) - A package to extend pyiron to the continuum scale. 
* [pyiron_experimental](https://github.com/pyiron/pyiron_experimental) - A package to apply pyiron for the post-processing of experimental measurements. 
* [pyiron_gui](https://github.com/pyiron/pyiron_gui) - A graphical user interface for pyiron based on ipywidgets. 

## 1.2 Git and GitHub
Git and GitHub are two of the most important tools/concepts that you need to be familiar with.

To allow different developers (aka now you!) to modify, improve, or add to Pyiron, you need a version control system to manage these modifications, keep track of every change, and prevent overlapping of different changes, this is Git. For Git commands, [see](https://gitlab.mpcdf.mpg.de/eisenforschung/cm/documentation/-/blob/master/Git.md)!

You also need a website (a graphical user interface) to host and manage the code (the Git repositories<sup>2</sup>) and the changes, track bugs, and include documentation (what you are reading now) and tests that make sure any changes you introduce do not crash another part of the code, and also allow anyone (outside of MPIE) to use and make changes into the code, this is GitHub. 

1. Bug: Weird error or a result that you did not expect and a little suspicious about.
2. Repository: If you are a Linux (Windows) user then this is a fancy word for project directory (folder) that contains files of your code.

## 1.3 Cloning 

From this section on, it is assumed that you can log in to the cluster. If not, please see [this](https://gitlab.mpcdf.mpg.de/eisenforschung/cm/documentation/-/blob/master/Cluster.md).

Now to change and test something in Pyiron, you can't change the code directly. You have to make a copy (clone) of the Pyiron code (or a specific part/module) of it, change it, test it, and then combine it with the current version.  

You have two options on where to do this: 
1. locally on your laptop (see section XXX) or
2. Directly on the cluster (which is covered by the rest of this section). 

The second option is preferred as it is more straightforward and also, if you are playing with a code that depends on one of the closed source/licensed simulation packages (e.g. VASP) you can only do this on the cluster.

The first step is to clone the latest Pyiron version:
1. In your terminal make (mkdir) and go to (cd) a new directory
```bash
mkdir cloned_pyiron
cd cloned_pyiron/
```
2. Go to [Pyiron atomistics module on GitHub](https://github.com/pyiron/pyiron_atomistics) and copy the module GitHub link:

![clonnin_1](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/20ad04a1-279d-45e0-b9ab-398ae62730a0)

3. Back to your terminal, clone pyiron_atomistics:
```bash
git clone https://github.com/pyiron/pyiron_atomistics.git
```
It will download a bunch of files and then you can see that a new **Git** directory named pyiron_atomistics is created. Now you have a version of pyiron_atomistics copied locally in your home directory on the cluster.

## 1.4 Loading Cloned Pyiron on the Cluster

Now when you log into the cluster and open a notebook on your web browser, the very first step you do is: 
```python
from pyiron import Project
```
which basically loads all pyiron modules, now you want to load ONLY pyiron atomistics that you just cloned (because you are going to change it) while keeping all other modules untouched but still loaded.

- if you have a look at the .bashrc file in your home directory, all modules are automatically loaded with the line: ```module load pyiron/dev```. So everything you write after that will overwrite this command.

- There are several ways to load the cloned pyiron_atomistics, the easiest is you go into the cloned directory print out (```pwd``` in your terminal), and copy the path to that directory (in my case it is: /u/aabdelkawy/cloned_pyiron/pyiron_atomistics/) and then add (use vim or emacs) the following line to the end (change the path according to your user name) of the .bashrc file in your home directory. 
```
export PYTHONPATH=/u/aabdelkawy/cloned_pyiron/pyiron_atomistics:$PYTHONPATH
```
- You then save the file and run the following command in the terminal:
```bash
source .bashrc
```
- This step is needed if you also want to test any modifications you introduced into the code directly from jupyter notebook.
- You can make sure that this cloned pyiron_atomististics is indeed loaded by typing the following commands in an empty cell in one of your notebooks
```python
import pyiron_atomistics
print(pyiron_atomistics)
```
- The output should be the path to the directory where you cloned pyiron_atomistics

## 1.5 Actually Changing the Code: Using Visual Studio on the Cluster
Now you have the necessary files to change and also you made the setup to connect different things, the next step is to actually change and test the code!
For this, the most convenient way is to use something known as IDE (integrated development environment) such as Visual Studio Code or PyCharm. These IDEs are basically software that enables you to write to, edit, and debug the code we cloned earlier. It also enables you to create branches and push changes (next section) to the main code on GitHub.

1. Now when you connect to Jupyter Hub and point your browser to ```localhost:8000```, you can find the Visual Studio instance installed on the cluster. Start it and approve the requested certificates.

![clonnin_2](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/9b1b174d-d752-421d-b20f-d85f1d79d9a0)

2. Now open the cloned pyiron_atomistics folder in Visual Studio

![clonnin_3](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/aab3b36c-76ab-4214-9a16-82c0b2ac2648)
![clonnin_4](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/7cf2cdca-c644-406e-bfa8-a2ca14f51466)

Now you can see in the explorer panel on the left all the files from the cloned pyiron_atomistics

![clonnin_5](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/8859a0d7-e9f1-40ec-bce6-5ed114826c0d)

3. Now you are connected to the main branch (code version) of pyiron atomistics, which you don't want to change. You need to create a branch (which is identical to the main branch code) and then introduce your changes to it. This will enable you to test your modifications locally. It will also give the other developers the chance to see, test, and comment on your changes without damaging their branches or the main branch. You do this as follows:

![clonnin_6](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/5a64f6a1-59db-47d6-92af-086b825e7f12)

Give this branch a name and hit enter. 

You can see now the lower left icon name changed from the main to the name of your branch. This means that you are now CHECKEDOUT automatically to this branch. Checking out branches makes you navigate different branches with different modifications. This branch should always be checked out (aka selected) when you are working on this modification. You can always checkout (select) the main branch from the same menu (but we don't need this now).

![clonnin_7](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/099a5c50-a734-4448-a80e-ff1ca88a8d0f)

4. Now for the sake of completeness and to make this part of the tutorial short, let us consider that the change you will introduce into the code is a new xx.md (.markdown) file with the line ```Thie files was created by xx``` where xx is your user name.

![clonnin_8](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/0815c44a-ec85-42d8-9995-70ba0987abcd)
![clonnin_9](https://github.com/pyiron/pyiron_developer_tutorial/assets/62240737/87894d5f-7262-4388-a5d4-860dafceea04)
