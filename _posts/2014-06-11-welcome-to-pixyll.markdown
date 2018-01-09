---
layout:     post
title:      Effective learning with simulation error by using an ensemble of environments.
date:       2017-06 15:31:19
summary:    EPOpt - Robust Neural Net Policies Using Ensembles by Rajeswaran et al.
categories: sim2real
---


One of the main goals of reinforcement learning is to train agents to perform well in tasks that involve sequences of actions and observations. Think a robot performing a household chore, driving a car, etc. For these problems, being sample efficient and safe is a challenge to scale up training and train safely. This makes simulation an attractive strategy. While training safely on cheap data at scale sounds attractive, simulated training is fraught with errors because of mismatch between the dynamics of simulation vs that of the real world. 

In this [work](https://arxiv.org/abs/1610.01283), Aravind Rajeswaran and his team at the University of Washington present an approach where they train on an ensemble of source domains as a way to address mismatch between the simulated and target domains. Their claim is that a lot of model free RL algorithms trained using the usual approaches like policy gradients etc are brittle and not robust to transfer to a real world environment when there are discrepancies between the simulator and the real world dynamics (called model mismatch). In fact, they not only train on an ensemble of source domains but also train in an adverserial manner, in the sense that they bias their sampling of the source domain to construct the training data by sampling more frequently from domains where the model tends to perform poorly. They intuit that this encourage learning of policies that perform well for a wide range of model instances. 

The whole point of this is that training on ensemble of source domains is experimentally turned out to be better than training on the maximum likelihood model of the source domain. They model the source ensemble as a probability distribution over MDPs, parametrized by some random variable $$\psi$$. Specifically, the state and action space are assumed to be continuous and fixed and the dynamics, rewards and initial state distribution are random and vary across members of the ensemble. 

In round $$i$$ of training, they update two sets of parameters: $$\theta_i$$, the parameters of the robust policy (neural network); and $$\psi_i$$, the parameters of the source distribution from which the source domains are sampled. This update is based on the performance of the trajectories sampled. This second step is called model adaptation.
The basic structure of EPOpt is to sample a collection of models from the source distribution, sample trajectories from each of these models, and make a gradient update based on a subset of sampled trajectories (using some batch policy optimization like TRPO). The subset of trajectories they use to make a gradient update is the worst $$\epsilon$$ percentile of trajectories that they sampled from the ensemble distribution. This is the adverserial component we mentioned earlier in the post.

The paper also goes onto show that the thus trained model is not only robust to deviation of target domain from source domain but also more robust to unmodeled effects than naive TRPO. Unmodeled effects means variation in target domain that is not modeled in source domain, for example if torso mass in target domain is different from torso mass in source domain but we only model friction variations in the source ensemble, we are still more robust even with unmodeled effects than naive TRPO.

The general trend here is that if you want to be invariant to something, try training with a set of distinct assignments of values to the thing you want to be invariant to. Of course, in many cases, its likely to work better for a narrower range of values.
