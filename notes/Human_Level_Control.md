# Paper

* **Title**: Human-level control through deep reinforcement
learning
* **Authors**: Volodymyr Mnih1 et al (Deepmind)
* **Link**: http://files.davidqiu.com/research/nature14236.pdf
* **Year**: 2015

## TL;DR
* used DQN with experience replay and separate target Q network and played Atari games
* t-SNE embedding on the states experienced for 2 hours of the agent's play shows it learns to discern states based on reward potential, not just how similar two states look by appearance. 
* It still fails with games in which longer term planning is required to succeed: e.g. montezuma's revenge. 

## details

### preprocessing
* single frame -> max(frame_t_minus_1, frame_t) to account for flickering.
* m frames stacked to be one input. (input is technically a small clip of video)
* clipping reward to either 1 or -1. this would allows us to use the same learning rate. (step size is a learning rate * error magnitude. error magnitude is clipped in this case)

### training
* a different network trained but with the same network architecture, learning algo and hyperparams. Since DQN was tested against 49 ATARI games, there were 49 sets of weights where each set == a network.
* frame skipping: taking action every kth frame. k = 4. a human seems to have k = 10.
* error clipping: td_error to be between -1 and 1 for stable learning. (not sure...)
