# Statistical consulting for psilocybin heart rate discrimination (HRD)

## Overview
We are planning to use a newly developed heart rate discrimination task to measure interoception under the influence of psilocybin.
Psilocybin is a selective serotonin agonist and acutely changes perception, cognition and consciousness.
It has been proposed to be useful in therapy for a variety of indications, such as depression, addiction, anxiety disorder and more.
What some of these have in common are alterations in interoceptive abilities compared to healthy controls.
Furthermore, psilocybin has been suspected to change the precision of sensory information in some way, thus affecting its processing.
Crucially, we will dose our participants four times with differing amounts of psilocybin (0, 5, 10 and 15 mg), statistically accounting for this repeated-measures design is where we need your expertise.

## The HRD task
The task we are using has been developed in a recent publication by [Legrand et al., 2022](https://www.sciencedirect.com/science/article/pii/S0301051121002325), task code is found [on github](https://github.com/embodied-computation-group/Cardioception). Essentially, (1) people listen to their heartbeat while it is being recorded, (2) they are presented with a sequence of tones that are either faster or slower than their heartbeat (3) they have to decide whether the tones were faster or slower than their hearbeat and report their confidence. The same is done in an exteroceptive condition, where participants listen to a sequence of tones instead of their heartbeat. The task adaptively chooses which tone frequency to present on a trial-by-trial basis, therefore each participant is sampled differently according to their answers. In the notebook 'HeartRateDiscriminiation.ipynb', you can play around with the data form the task.

## Data
Resulting from this is a sequence of trials that contain the following crucial information:
- the difference between the measured heart rate and the presented tone sequence -> "Delta BPM" or "intensity"
- the answer of the participant

From which we can easily derive the following vectors of equal length:
- x, a vector of the presented intensities
- n, a vector of the number each intensity was presented
- r, a vector containing the amount of times a participant answered "more", i.e. "faster"

You are provided with the full dataset of Legrand et al. 2022.

## Model and Analysis

x, n and r are then fed into a statistical Bayesian model that essentially fits a sigmoid (cumulative Gaussian) to the data that represents the rate parameter of a Binomial distribution encoding the answers.
This is done using Markov-chain monte carlo sampling (MCMC) with the python package pymc3.
In the notebook 'psychophysics_subjectLevel.ipynb', supplied by Legrand et al., you can see this in action for one subject.
This yields a threshold (or bias) alpha and slope (or precision) beta, which are our quantities of interest.
We would like to see how these change in response to psilocybin.
Legrand et al. also provide a hierarchical model in 'psychophysics_groupLevel.ipynb', where the model is extended to derive group-level priors.
This is the model you could extend with the session-level effects, since at the moment, it doesn't account for repeated measures.


## Simulations
In 'simulations.ipynb', you can also find utilities to simulate data. For now, this also only works for 'seperated' participants, i.e. for each simulation we assume an independent set of participants, just as the model does.
One limitation of the simulations is that the adaptive sampling procedure is not implementes. However, it is approximated by the mean trial amount for each delta BPM.
Note that in the current simulation implementation, we assume that the betas/slopes follow a Gamma distribution, which fits with the original data, but the model uses a normal prior.

## Bayesian modeling and MCMC support
If you are unfamiliar with Bayesian modeling and MCMC, I can provide you with a textbook on this (Lee and Wagenmakers: Bayesian Cognitive Modeling), which introduces the necessary concepts rather quickly and concisely.
Note that the book is a bit old and uses WinBUGS to implement the models and MCMC, but [this online resource](https://github.com/pymc-devs/pymc-resources/tree/main/BCM) contains all example code from the book in python/pymc3.
You can come to my office hours (room 2B20 or online) with questions.

## Problems you could solve
- include the repeated measures design in the hierarchical Bayesian model
	- or do it your way, but account for the repeated measures
- account for session effects (people get better at interoception when doing the task repeatedly)
- we sometimes see many divergent transitions in our chains, which is a sign of an unhealthy model
	- you could re-parametrize the cumulative Gaussian by using a logit to improve this



