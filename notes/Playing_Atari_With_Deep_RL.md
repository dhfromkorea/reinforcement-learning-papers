# Paper

* **Title**: Playing Atari with Deep Reinforcement Learning
* **Authors**: Volodymyr Mnih1 et al (Deepmind)
* **Link**: https://www.cs.toronto.edu/~vmnih/docs/dqn.pdf
* **Year**: 2013

## TL;DR
* known to be the first paper to showcase a successfully trained DQN to beat many Atari games.


## Details
* most Deep Learning applications require large amounts of labelled training data, which is hard to acquire in most RL problem settings.
* applying DL to RL comes with some difficulties: delayed, sparse reward, non-stationarity, non-iidness.
* a variant of Q-learning
* stochastic gradient descent, experience replay. 
* Partially observed finite MDP: observation(video input), state(RAM). finite because the games were episodic and the state space is discrete.
* slowing the update of weights for Q network by iterating every n time steps. This helps stabilize learning (smooth updates). Why needed? because the target also depends on Q network params, theta.
* model-free, end-to-end (where CNN could learn to extract features that are directly discriminative for action-values.)
* earlier worthek#1: RBM to estimate the value function?
* earlier works#2 NFQ (Neural Fitted Qlearning) used autoencorder to learn lower dimensional feature map. 
* earlier works#3: HyperNEAT, evolutionary architecture

### experience replay: 
* e_t = (s_t, a, r, s_t_1). {e_t ... } ~ D. When updating Q network, e_t's are sampled randomly from D. 
* This helps alleviate non-iidness. The states the network gets trained for are independent. (decorrelation)
* Also by reusing samples/experiences, we get to use the data more efficiently. 
* also it helps stabilize learning because you're accumulating a dstribution where e_t is sampled and e_t will be probably close to the expected value of the distribution, not some skewed ones. For instance, if an agent takes to the right, it will suddently see states related to the right section of the environment, but in fact there are a whole lot of experiences to be had to the left side. 
* stored the last N experiences. there could a tactic to give priority to experiences.
### preprocessing(phi)
* 210x160raw frames (RGB channels) gray scaled and downsampled to 110x84. The final 84 x 84 square patch is applied to the 110x84 image as the library implementation of 2d conv expected a square input. 
* apply phi to the last 4 frames and stack them together (84x84x4 is input size, therefore)
### parameterizing Q network
* previously modeld Q(input) = 1d output (value for each action). If there were 4 actions, there would be 4 different Q networks and therefore 4 separate foreward passes are required. (computation grows linearly)
* instead of above, we use Q(input) = K dimensional output where k is the number of actions. Now it's just a single forward pass.
### CNN
* first layer: 16 filters 8 x 8 with stride 4, ReLU nonlinearity 
* second layer: 32 4x4 with stride 2, ReLU
* third layer: fully connected and 256 units with ReLU
* output layer: fully connected 
### ATARI games – Beam Rider, Breakout, Enduro, Pong, Q*bert, Seaquest, Space Invaders.
* the same network architecture (including hyperparameters) used for all games attempted (but trained for each game inpendently. i.e. no transfer learning????-> need to be checked)
* one exception to no in-game modification rule: modified reward so that positive reward is all 1 and negative -1. (to make the variance in rewards across different games a less of a problem) 
* used RMSProp with minibatch of size 32
* behavior policy: e-greedy with e annealing from 1 to 0.1 linearly and 0.1 thereafter
* frame-skipping trick to enable real-time playing: decide an action every k-th frame. and repeat action chosen k times. they used k=4 except Space Invader with k=3.

### Training and Stability
* Evaluating the progress of an agent is hard in RL than in Supervised Learning (Training Error or Validation Erron can be tracked)
* tracking average reward is noisy because of nonstationarity, so we track predicted Q value functions for a fixed set of states (sampled randomly initiallly). The latter is more smooth. (they could have used a smoothing rolling average of rewards to smooth out noisiness)
* average total reward seems noisy because a small change in weights in Q network can yield large changes in the distribution of states. (a slightly different action can lead to an entirely different state space where value function will change drastically)

### Main Evaluation
* baseline methods previously implemented: SARSA, Contingency (prior knowledge engaged)
* baseline methods#2: a human expert, random policy
* HNeat best, Hneat Pixel

 

