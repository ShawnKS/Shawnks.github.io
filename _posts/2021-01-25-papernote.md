---
layout: post
title: "论文笔记20210125"
date: 2021-01-25 18:13:00
author: "Shawn"
header-style: text
catalog: true
tags:
  - 机器学习
  - 城市计算
---

## Deep and Confident Prediction for Time Series at Uber

Here I list some important sentences used in this paper

- Under Bayesian framework, the authors think the prediction uncertainty can be decomposed into three types: *Model uncertainty, inherent noise,* and *model misspecification*.
- **Model uncertainty**, also referred to as **epistemic uncertainty**, captures our ignorance of the model parameters, and can be reduced as more samples being connected. 
- Inherent noise, captures the uncertainty in the data generation process and is irreducible.
- The third uncertainty from **Model misspecification**, has been long-overlooked. This captures the scenario **where the testing samples come from a different population than the training set,** **which is often the case in time series anomaly detection**.
- **Error = Model Uncertainty + Model misspecification + inherent noise**

**How to quantify?**

- Given a trained neural network $f^{\hat{W} (·)}$ where $\hat{W}$ represents the fitted parameters, as well as a new sample $x^*$, our goal is to evaluate the uncertainty of the model prediction, $\hat{y}^*= f^{\hat{W}}(x^*)$. Specifically, we would like to quantify the prediction standard error, $\eta$, so that an approximate α-level prediction interval can be constructed by

$$
[\hat{y}^*-z_{\alpha/2}\eta,\hat{y}^*+z_{\alpha/2}\eta]
$$

- **The key idea is** to compute the neural network output with stochastic dropouts at each layer.
- In practice, we find that the uncertainty estimation is usually robust within a reasonable range of $p$.

*One of the key here in Bayesian is that everything is a probabilistic distribution but not a point estimate. This means there are uncertainties about your weight as well.*

**They use **MCDropout** to deal with model uncertainty and misspecification. Basically, they have claimed that using Dropout at **inference** time is equivalent to doing Bayesian approximation. The key idea here is letting dropout doing the same thing in **both training and testing time.** At test time, you will repeat B times (Few hundreds of times as the paper said), i.e. passing the same input to the network with random dropout. You then take means of your prediction and you can generate a prediction interval with these # of B predictions. MC is referring to Monte Carlo as the dropout process is similar to sampling the neurons.*

**Related paper**

1. *Deep and Confident Prediction for Time Series at Uber*
2. [*Time-series Extreme Event Forecasting with Neural Networks at Uber*](http://roseyu.com/time-series-workshop/submissions/TSW2017_paper_3.pdf)
3. [*Dropout as a Bayesian Approximation: Representing Model Uncertainty in Deep Learning*](https://arxiv.org/pdf/1506.02142.pdf)
4. [*Variational Bayesian dropout: pitfalls and fixes*](https://arxiv.org/pdf/1807.01969.pdf)
5. [*Variational Gaussian Dropout is not Bayesian*](https://arxiv.org/pdf/1711.02989.pdf)
6. [Risk versus Uncertainty in Deep Learning: Bayes, Bootstrap and the Dangers of Dropout](https://pdfs.semanticscholar.org/dde4/b95be20a160253a6cc9ecd75492a13d60c10.pdf)

Where to start? Key Name to google at:

- Yarin Gal (He propose using dropout for Bayesian approximation in his thesis)
- Slawek Smyl (winner of M4 competition)