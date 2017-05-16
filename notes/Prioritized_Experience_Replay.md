# Paper

* **Title**: PRIORITIZED EXPERIENCE REPLAY
* **Authors**: Tom Schaul, John Quan, Ioannis Antonoglou and David Silver
* **Link**: https://arxiv.org/pdf/1511.05952.pdf
* **Year**: 2015

## TL;DR
* proposed a method to prioritize experiences(transitions) based on their relative values, unlike the previous work where experiences were sampled uniformly.
* magnitude of TD error as a measure of expected learning progress (a similar idea to advantage?)
* to alleviate the bias introduced by prioritization (certain transitions will be stored longer time and replayed more often) we add some stochasticity
* there's a biological parallel to experience replay in RL: hippocampus of rodents where exprience replay takes place during sleep or awake resting.
* there have been various methods to deal with experience replay. one of them is to divide the experience into two buckets: positve and negative, and sample from each bucket at a fixed ratio. 

## Details

### Motivating example
* which to store, which to replay
* Blind Cliff
* learning well (training speed, total avg. reward)

### Prioritizing with TD Error
* The importance of transitions is hard to be measured directly. Therefore, we use a proxy. One good proxy is TD_Error which could indicate how surprising or unexpected the transition/result was. 
* TD_err = Boostrapped Target(r + gamma*nextQ_estimate) - current_Q_estimate
* Q-learning or SARSA already compute TD errors so this works well with them.
* used binary heap to get the experience with max TD Error each time step (binary heap has the property to find max at O(1) and update O(logn)
* To avoid excessive consumption of memory, greedy TD error prioritization typically update TD errors for the transitions that are replayed. 

### Stochastic Prioritization
* Prioritize purely based on TD error has two problems: first, transitions that are initially of low TD errors may never be replayed. (i.e. low td error -> low priority -> not replayed -> td error not updated -> not replayed) second, sensitive to noise. Third, greedy prioritization may be pronse to overfitting (???)
* To remedy those disadvantages, the authors formulated a stochastic scheme where they reshape the uniform sampling distribution to a probability distribution that's discriminative between transitions.
* a sampling probability for transition i where P(i) = Pi^alpha / sum Pi^Alpha where i is a transition index and alpha being a hyper parameter to emphasize priority. If alpha is 0, it's the same as uniform sampling.
* Pi is a priority that can be modeled in the two ways. First(proportional) by pi = |td_err| + e where e is a small positive value to give a non-zero prob for every transition. The other way is to do pi = 1/rank(i). Rank(i) is the index of the experience list sorted by td_error. The latter way will create a power law distribution which is robust to outliers.

### Annealing the bias
importance sampling weight

### Experiments

### Extensions

### Conclusions

### Appendix
* to be read later

* implemented: https://github.com/Damcy/prioritized-experience-replay