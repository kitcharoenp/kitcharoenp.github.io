---
layout: post
title: "Batch / Epoch"
published: false
categories: [ml]
---
Notes while reading :  [Difference Between a Batch and an Epoch in a Neural Network](https://machinelearningmastery.com/difference-between-a-batch-and-an-epoch/)

> **Stochastic gradient descent (SGD)** is a learning algorithm that has a number of hyperparameters [**batch size**, **number of epochs**, ...].

> **Optimization** is a type of searching process and you can think of this search as learning. The optimization algorithm is called **“gradient descent“**, where **“gradient”** refers to the calculation of an error gradient or slope of error and **“descent”** refers to the moving down along that slope towards some minimum level of error.


> * **Stochastic gradient descent** is an iterative learning algorithm that uses a training dataset to update a model.
> * **Batch size** controls the number of training samples to work through before the model’s internal parameters are updated.
> * **Number of Epochs** controls the number of complete passes through the training dataset.


### Reference
* [Difference Between a Batch and an Epoch in a Neural Network](https://machinelearningmastery.com/difference-between-a-batch-and-an-epoch/)
