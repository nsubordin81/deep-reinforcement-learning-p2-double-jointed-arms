# Deep Reinforcement Learning In a Continuous Control Environment

## Algorithm

For this task I attempted to solve Reacher environment version ()

For this task I used a version of the DDPG algorithm, which had its basis in the Udacity DDPG reference 
implementation used to solve the Bipedal Walker task and which I augmented based on reading the DDPG original paper
as well as through manual hyperparameter sampling, reading portions of the GEP-PG paper and learning from mentor recommendations
to other nanodegree participants. 

I chose the DDPG algorithm because it is a state of the art RL algorithm for environments with continuous action spaces, which the Reacher environment simulates. 
The agent consists of four ANNs, because there is a local and target actor and a local and target critic. This is to prevent 
correlations in the loss function of both actor and critic. If there were only two networks and they were used for the target estimate
in the loss as well as the value estimate, then the target would immediately be affected by any adjustments to the weights creating a 'moving target' 
complication in learning and convergence across network updates would be very difficult (just as with DQN). 

I'll describe the actors and critics together, noting that the target networks are copies that have specific usein the loss functions for the purpose alluded to above.
The actor is essentially a policy gradient method attempting to learn a deterministic optimial policy, so it takes states as inputs and outputs an action value. 
The critic is an offline Deep Q Learning Network, and it is therefore learning to estimate the optimal action value function. For DDPG, most of the interesting innovation comes
around the loss functions of each network and how they each leverage outputs from the policy method as well as the Q-Learning method. Because of the continuous actions space, the critic network can't just take the argmax of its own output for the next state in the target estimate like DQN does, so instead it uses the actor network to give it an estimate for best next action to take. But then in order to make the actor 
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
INITIAL_NOISE = 1.15
START_DECAY = 300
NOISE_DECAY = 0.99999

In many ways these hyperparameter values match with the original DDPG paper. 

gamma is the standard .99, tau for soft updates is the same, as is the replay buffer size. 

I diverged from the values used in ddpg on the weight decay, I chose to have none, because their architecture had several more nodes so I didn't necessarily need weight decay which could 
negatively impact my convergence. I also doubled the minibatch size to 128. I also made the critic's learning rate smaller at than the paper had it by a factor of 10.

I've heard that many later implementations of DDPG for other papers that are trying to improve upon DDPG regard the OUNoise as unnecessarily complex and opt for gaussian noise, but I didn't
explore this. The noise params for OUnoise are the same as in the DDPG paper, early on I experimented with changing theta and sigma, but that just felt like adjusting the distribution without a full understanding of the OU formula.

So instead I opted to amplify it a bit, to 1.15 times its value, and then I put in a plan to decay it over time, introducting hyperparameters for when to start decaying and at what rate. 
Especially before realizing my primary issue with learning was that my initial network architecture was too complicated and that my episode length was arbitrarily short, I tried assisting 
convergence by introducing noise decay over time, much like an epsilon greedy approaches has epsilon fall off exponentially towards .01. 

However, this seemed to hurt exploration because it was happening too soon and too fast, so I delayed the beginning of decay until the replay buffer could be stocked with experience with the full OUNoise, and I ultimately increased the noise output of the OU class to 1.15 times strength. Since I still hadn't achieved the goal average reward of 30 because my timesteps were capped at 300, I kept adjusting noise, including starting with a factor of OUNoise a little big greater than the DDPG paper included and having it remain that high for a 500 episodes before decaying very slowly. This could be dangerous for learning as too much noise pushes the policy of the actor towards the boundaries of its action space and also makes it harder for policy updates to get reward if they are actually moving in the right direction. However, once the problem was reframed to a longer episode length, meeting the goal was possible even with extra noise and the environment was mostly solved before decay had even started. 

Much of my fiddling with noise was trial and error with some knowledge of constraints, so not very scientific and also very custom to my prior knowledge of this environment's dynamics, so I'd probalby try to avoid this for attempts to get a less brittle implementation of DDPG in the future.


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

Given DDPG is so related to DQN, I can see how some of the rainbow DQN augmentations could be applied to make it better, probably taking it more in the direction of D4PG or other, 
especially the implementation of a prioritized experience replay. 

I also was reading the paper "GEP-PG: Decoupling Exploration and Exploitation in Deep
Reinforcement Learning Algorithms" which aims to combine ddpg with a more purely exploratory approach borrowed from evolutionary algorithm techniques. As I had a lot of issues
initially getting the agent to explore enough with DDPG to even start learning this idea has some appeal and so I'll like to attempt an implementation of this. 

I also opted for the single agent variation of this and have not attempted the more difficult crawler task. Both the multi agent version and the crawler may prove more challenging to
implement or at least provide a different learning experience for me. 