# deep-reinforcement-learning-p2-double-jointed-arms
Exploration of policy gradient and actor critic methods with a specific task from the Unity ML Agents environment involving keeping simulated arms on a target.

# Checklist

- [x] Code for training the agent is complete with good docs
- [x] Pytorch and Python 3 were used
- [x] There are saved model weights for the successful agent in the repo
- [x] This README file exists
- [x] This file describes the project environment details, such as state and action spaces and when the environment is solved
- [x] This file has instructions for getting the necessary dependencies and downloading files needed to train and use the agent
- [x] This file has a description for how to run the agent
- [x] I've got a Report.md outlining the implementation, including the learning algorithm used, the hyperparameters chosen, and the model architectures for ANNs
- [x] There is a plot of the rewards in the Report.md file showing either successfully meeting version 1 or 2 of the task
- [x] The Report.md file has the number of episodes required to solve the environment as well
- [x] The Report.md has some concrete suggestions for how I might improve on the implementation. 

## Project Environment Details

This environment is a modified version of the Reacher Environment from Unity ML Agents. The goal for the agent or agents (depending on whether you are using the single agent version or the multiple agents version of the environment) is to learn to maneuver a double-jointed arm in such a way that the end of the arm remains within a target zone for as many time steps as possible.

State Space: For this training we will use a lower level observation space (i.e. not learning directly from pixels), consisting of a vector with 33 floating point entries. Each of entries corresponds to some state information about the mechanical arm, either relating to its position, rotation, or velocity

Action Space: The action space for the Reacher task is made up of a vector with four floats in the range [-1, 1]. These correspond to the torque you could apply to the two mechanical joints. 

Environment Considered Solved When: The reacher task (I chose version 1 of the environment) is solved when the hand of the double jointed arm has remained in the goal location for an average of +30 time steps for 100 consecutive episodes.

## Project Setup, Downloading the Necessary Dependencies

### Dependencies

This task was trained using a python environment with python 3.6

First step is to clone this repository down to your machine, and from there:

1. create and activate the python environment:
    * *Linux* or *Mac*:
    ``` 
    conda create --name reacher python=3.6
    source activate reacher
    ```

    * *Windows*:
    ```
    conda create --name reacher python=3.6
    activate reacher
    ```

2. Download the environment that will be used with unity mlagents. this project was based on a course so the 
environment used is not from the unity mlagents page and instead is only available at these links:
    - [Windows 64 bit](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Windows_x86_64.zip)
    - [Windows 32 bit](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Windows_x86.zip)
    - [Mac OSX](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher.app.zip)
    - [Linux](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Linux.zip)

extract the executable and associated dll file from the archive appropriate to your platform, and then copy it into the root of this cloned repository. The relative path from the root of your cloned version of this repo 
to the reacher executable should then be: `Reacher_Windows_x86_64/Reacher.exe` for the Windows example and something similar for OSX and Linux

3. install the dependencies from the requirements.txt
    ```
    pip install -r requirements.txt
    ```

4. create an ipython kernel for your conda environment so you will have access to the packages you installed, and make sure to select it when in an ipython notebook for this codebase:
    ```
    python -m ipykernel install --user --name reacher --display-name "reacher"
    ```


## Running the Agent

The code to train and run the agent are in DDPG.ipynb jupyter notebook. If you've installed the conda environment above and created a kernel for it, you should be able to start a notebook instance from your terminal running the command `jupyter notebook ./DDPG.ipynb` then, 

### Running the Agent: 

1. run first 2 cells, labeled 'import the necessary packages' and 'instantiate the environment and agent'
2. run the final cell, labeled 'Watch a smart agent!'

### Retraining the Agent: 

1. run first 2 cells, labeled 'import the necessary packages' and 'instantiate the environment and agent'
2. run the cell labeled 'Train the Agent with DDPG'

note - if you want to train the agent and then run it, in between these two steps you will need to restart the kernel just because of an issue with the unity environment, I've had issues closing it and then instantiating it again within the same kernel, so restarting the kernel resets any lingering dependency unity has created by launching the environment.
