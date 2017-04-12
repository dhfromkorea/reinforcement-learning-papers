# Paper

* **Title**: Learning Transferable Policies for Monocular
Reactive MAV Control
* **Authors**: Shreyansh Daftry et al
* **Link**: https://arxiv.org/abs/1608.00627
* **Tags**: Transfer Learning, Domain Adaptation, Reactive Control,
Autonomous Monocular Navigation, Micro Aerial Vehicles
* **Year**: 2016

## Summary
* The existing research has shown that with Imitation Learning, training an MAV to control its flight autonomously with monocular data is feasible.
* However the environments an MAV would encounter in real life would vary widly: different weathers, different terrains, forest/dessert/paved and what not. A controller trained to aviage on paved trails may not fly as effectively in a jungle. 
* Acquiring training data for each domain/enviroment would be prohibitively expensive. Therefore the authors consider Transfer Learning where knowledge gained in one (source) domain can be useful in another (target).
* The authors presented a generic framework for learning transferable motion policies. The authors used DAN (Deep Adaptation Network)[1] that generalizes CNN trained on one domain to another domain.
* Result: the authors observed a significant performance boost in their system over simply reusing the policy learned in a source domain.
* Note: the CS294 Berkeley lecture that references this paper mentioned Dagger was used for this experiment instead of the three-camera trick to remedy non-iidness.

 
## Details
### DAN (Deep Adaptation Network)

* relevant references:
    * DAN: https://arxiv.org/pdf/1502.02791.pdf
    * mean embedding https://en.wikipedia.org/wiki/Kernel_embedding_of_distributions

![alt text][dan]


* unfortunately I did not fully grasp the underlying mechanics behind DAN, so I briefly note here, to the extent I think I understood.
* In CNN (in this case AlexNet was used), the higher layers are general and the lower you go, the layers become more task-specific. The idea with DAN is threefold. 
    * First, freeze the weights in the first 3 layers that are general enough to extract features for both domains.
    * Yosinski et al., 2014 showed the convolutional layers can learn generic features that tend to be transferable in layers conv1–conv3 and are slightly domain-biased in conv4–conv5.
    * Second, finetune the weights for the rest of the layers (convolutional and fully connected) with the "source" domain data only (since we assume there are no labelled data for the target domain anyway).
    * Third, at the same time with the finetuning, regularize the fully-connected layers (not convolutional) with MK-MMD, a measure of cross-domain discrepancy. (It's the MK-MMD part I did not understand in detail.)
    * the loss function looks like:


![alt text][dan_loss]


### Experiments

![alt text][dan_result]

* the authors transferred across three dimensions: vehicle systems (ARDrone -> 3DR ArduCopter), weather (Summer -> Winter), and environments (forest in Zurich -> forest near CMU).
* lower and upper bound baseline: MAV flight using random policy and complete training data (both source and target), respectively.
* Density refers to how much an enviroment was cluttered with obstacles. High density enviroments have lower average distance travelled.
* We can see the upper bound baseline is the clear winner but the green bar (adapted with DAN) outperforms the red bar (copying a policy).

[dan]: ../img/learning_transferable_dan.png ""
[dan_loss]: ../img/learning_transferable_loss_function_dan.png ""
[dan_result]: ../img/learning_transferable_result.png ""
