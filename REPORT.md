# Deep Reinforcement Learning In a Continuous Control Environment

## Algorithm

For this task I attempted to solve Reacher environment version ()

For this task I used a version of the DDPG algorithm, which had its basis in the Udacity DDPG reference 
implementation used to solve the Bipedal Walker task. 

The DDPG algorithm was chosen because it is a state of the art RL algorithm for environments with continuous action spaces. 
The agent consists of four ANNs, because there is a local and target actor and a local and target critic. This is to prevent 
correlations in the loss function of both actor and critic. If there were only two networks and they were used for the target estimate
in the loss as well as the value estimate, then the target would immediately be affected by any adjustments to the weights and convergence 
across learning steps would be very difficult (just as with DQN). 

I'll describe the actors and critics together, noting that the target networks are copies that have specific usein the loss functions for the purpose alluded to above.
The actor is essentially a policy gradient method attempting to learn a deterministic optimial policy, so it takes states as inputs and outputs an action value. 
The critic is an offline deep Q Learning network, and it is therefore learning to estimate the optimal action value function. For DDPG, most of the interesting innovation comes
around the loss functions of each network and how they use each other. Because of the continuous actions space, the critic network can't just take the argmax of its own output
for the next state in the target estimate, so instead it uses the actor network to give it an estimate for best next action to take. But then in order to make the actor 
network better at actually picking the best action, in its loss function we also use the TD boostrapping method from a batch of offline experiences the actor had, getting the 
Q value of those and maximizing that value as a way of doing gradient ascent towards an optimal policy. So both parts of the actor-critic setup are leveraging a policy method 
that picks the actions, but then for improvement they are both leveraging temporal difference estimates for the action value function with Q-learning. Other 
methods from the DDPG paper used in this approach were the soft update of tau for slowly moving the weights of the target network towards the local one, target networks as 
mentioned and the experience replay buffer as mentioned. 

### Hyperparameters

For my implementation I chose the following hyperparameters:
BUFFER_SIZE = int(1e6)  # replay buffer size
BATCH_SIZE = 128  # minibatch size
GAMMA = 0.99  # discount factor
TAU = 1e-3  # for soft update of target parameters
LR_ACTOR = 1e-5  # learning rate of the actor
LR_CRITIC = 1e-3  # learning rate of the critic
WEIGHT_DECAY = 0

OUnoise: 
theta=0.15
sigma=0.2

In many ways these hyperparameter values match with the original DDPG paper. 

gamma is the standard .99, tau for soft updates is the same, as is the replay buffer size. 

I diverged from the values used in ddpg on the weight decay, I chose to have none, because their architecture had several more nodes so I didn't necessarily need weight decay which could 
negatively impact my convergence. I also doubled the minibatch size to 128. I also made the critic's learning rate smaller at than the paper had it by a factor of 10.

The noise params are the same, I did tweak them but it ultimately didn't help exploration as I was hoping.
I've heard that many later implementations of DDPG for other papers that are trying to improve upon it regard the OUNoise as unnecessarily complex and opt for gaussian noise, but I didn't
explore this. 


### Model Architecture

I have a target and local network for both actor and critic, and the architectures are as follows: 

#### actor 
input = 33
hidden1 = 256, activation ReLU, fan-in Xavier Initialization
hidden2 = 128, activation ReLU, fan-in Xavier Initialization
output = 4, activation tanh, (-.003, .003) uniform random Initialization

#### critic
input = 33
hidden1 = 256 + 4, activation ReLU, fan-in Xavier Initialization
hidden2 = 128, activation ReLU, fan-in Xavier Initialization
output = 1, (-.003, .003) uniform random initialization


## Results

## Ideas for Further Research

I have had to move faster through this project and the last one in order to ultimately finish this nanodegree, but I am very interested in comparing and contrasting the 
convergence speed and stability of DDPG with other popular methods like A2C, D4PG, PPO and others. So I would like to attempt some of these other algorithms either on this task or 
others. 

I also was reading the paper "GEP-PG: Decoupling Exploration and Exploitation in Deep
Reinforcement Learning Algorithms" which aims to combine ddpg with a more purely exploratory approach borrowed from evolutionary algorithm techniques. As I had a lot of issues
initially getting the agent to explore enough with DDPG to even start learning this idea has some appeal and so I'll like to attempt an implementation of this. 

I also opted for the single agent variation of this and have not attempted the more difficult crawler task. Both the multi agent version and the crawler may prove more challenging to
implement or at least provide a different learning experience. 