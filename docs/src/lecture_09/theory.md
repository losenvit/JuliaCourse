```@setup theory
using Plots
```

# Theory of neural networks

Neural networks appeared for the first time decades ago but were almost forgotten after a few years. Their resurgence in the last one or two decades is mainly due to available computational power. Their impressive list of applications include:    
- One of the first applications was reading of postal codes to automatize sorting of letters. Since only ten black and white digits can appear at five predetermined locations, simple networks could be used.
- A similar type of neural (convolutional) networks is used in autonomous vehicles to provide information about cars, pedestrians or traffic signs. These networks may also use bounding boxes to specify the position of the desired object.
- While the previous techniques used 2D structure of the input (image), recurrent neural networks are used for series-type data (text, sound). The major application is automatic translators.
- Another application includes generating new content. While there may exist useful applications such as artistic composition (music or writing scripts), these networks are often used to generate fake content (news, images).


## Neural networks

The first three bullets from the previous paragraph are all used for classification. The idea is the same as for linear networks. For an input ``x`` with a label ``y``, the classifier ``f`` minimizes the loss between the prediction ``f(w;x)`` and the label ``y``. We stress that ``f`` has two parameters: ``w`` is to be trained (weights) while ``x`` are inputs (data). Having ``n`` samples (data points), this results in
```math
\operatorname{minimize}\qquad \frac1n\sum_{i=1}^n \operatorname{loss}(y_i, f(w;x_i)).
```
The previous lecture used the linear classifier ``f(x)=w^\top x`` and the cross-entropy loss for classification and the squared ``l_2`` norm ``\operatorname{loss}(y, \hat y) = (y - \hat y)^2`` for regression.

The main idea of neural networks is to use a more complex function ``f`` than linear with a certain structure such that:
- It has good approximative quality.
- It does not contain many parameters to learn (train).
- The computation of derivatives (training) is simple.

## Layers

The previous bullets are achieved in an elegant way by representing the neural network via a layered structure. The input goes into the first layers, the output of the first layer goes into the second layer and so on. Mathematically speaking, a network ``f`` with ``M`` layers has the structure
```math
f(x) = (f_M \circ \dots \circ f_1)(x),
```
where ``f_1,\dots,f_M`` are individual layers. Since two layers that are not next to each other (such as the first and the third layer) are never directly connected (except skip connections), the function value can be propagated through the network simply. The same holds for the gradients, which can be easily computed via chain rules.

![](nn.png)

#### Dense layer

The dense layer is the simplest layer which has the form
```math
f_m(a) = l_m(W_ma + b_m),
```
where ``W_m`` is a matrix of appropriate dimensions, ``b_m`` is the bias (shift) and ``l_m`` is an activation function. The weights of the neural network, which need to be trained, would be ``w=(W_m,b_m)_m`` in this case.

The activation function is usually written as ``l_m:\mathbb{R}\to\mathbb{R}`` and its operation on the vector ``W_mz + b_m`` is understood componentwise. Examples of activation functions include:
```math
\begin{aligned}
&\text{Sigmoid:}&l(z) &= \frac{1}{1+e^{-z}} ,\\
&\text{ReLU:}&l(z) &= \operatorname{max}\{0,z\}, \\
&\text{Softplus:}&l(z) &= \log(1+e^z), \\
&\text{Swish:}&l(z) &= \frac{z}{1+e^{-z}} ,\\
\end{aligned}
```


```@setup nn
using Plots

xs = -4:0.01:4
ylim = (-1,4)

p1 = plot(xs, 1 ./(1 .+exp.(-xs)), title="Sigmoid", ylim=ylim, label="")
p2 = plot(xs, max.(xs,0), title="ReLU", ylim=ylim, label="")
p3 = plot(xs, log.(1 .+exp.(xs)), title="Softplus", ylim=ylim, label="")
p4 = plot(xs, xs ./(1 .+exp.(-xs)), title="Swish", ylim=ylim, label="")

plot(p1, p2, p3, p4; layout=(2,2))

savefig("Activation.svg")
```

![](Activation.svg)


#### Softmax layer

The cross-entropy loss function (see below) requires that its input is a probability distribution. To achieve this, the softmax layer is applied directly before the loss function. Its formulation is
```math
\operatorname{softmax}(a_1,\dots,a_K) = \frac{1}{\sum_{k=1}^K e^{a_k}}(e^{a_1}, \dots, e^{a_K}).
```
The exponential ensures that all outputs are positive. The normalization ensures that the sum of the outputs is one. Therefore, it is a probability distribution. When a dense layer precedes the softmax layer, it is used without any activation function (as, for example, ReLU would result in many probabilities being the same).

#### One-hot and one-cold representation

One-hot and one-cold representations are directly connected with the softmax layer. The one-hot representation is "the normal one", while the one-cold representation is its probability distribution.

For example, having 10 digits, label ``y=3`` has one-cold representation ``3`` and one-hot representation ``(0,0,0,1,0,0,0,0,0,0)``. Here, we count from ``0`` to ``9``.


#### Other layers

There are many other layers (convolutional, recurrent, pooling), which we will go through in the next lesson.



## Loss functions

The most commonly used loss functions are:
- (Mean) square error
  ```math
  \operatorname{loss}(y,\hat y) = (y-\hat y)^2.
  ```
- Cross-entropy
  ```math
  \operatorname{loss}(y,\hat y) = \sum_{k=1}^K y_k\log \hat y_k.
  ```
- Binary cross-entropy
  ```math
  \operatorname{loss}(y,\hat y) = y\log \hat y + (1-y)\log(1- \hat y).
  ```
Mean square error is used for regression problems while both cross-entropies for classification problem. The former for multi-class (``K>2``) and the latter for binary (``K=2``) problems.


## Making predictions

For classification with ``K`` classes, the classifier ``f`` predict a probability distribution of ``K`` classes. The prediction is the label with the highest probability. Using the terminology from above, the classifier's output has the one-hot form, while the actual prediction has the one-cold form.

The most common metric for evaluating classifiers is the accuracy defined by
```math
\operatorname{accuracy} = \frac 1n\sum_{i=1}^n I(y_i = \hat y_i),
```
where ``I`` is the characteristic (0/1) function which counts how often the argument is satisfied. With abuse of notation, we use both ``y_i`` and ``\hat y_i`` as their one-cold representation. Therefore, accuracy measures the fraction of samples with correct predictions.


## Approximation quality

Even shallow neural networks (not many layers) can approximate any continuous function as the next theorem states.

```@raw html
<div class = "theorem-body">
<header class = "theorem-header">Theorem: Universal approximation of neural networks</header><p>
```
Let ``g:[a,b]\to \mathbb{R}`` be a continuous function defined on an interval. Then for every ``\varepsilon>0``, there is a neural network ``f`` such that ``\|f-g\|_{\infty}\le \varepsilon``.

Moreover, this network can be chosen as a chain of the following two layers:
- Dense layer with ReLU activation function.
- Dense layer with identity activation function.
```@raw html
</p></div>
```

As the proof suggests (Exercise 1), the price to pay is that the network needs to be extremely wide (lots of hidden neurons).

## Overfitting and regularization

The previous theorem says that any continuous classifier can be approximated to arbitrary precision by a sufficiently large network. Large networks contain a large number of parameters. If the amount of samples is the same, the network's complexity cannot be increased because there would be more parameters than samples. In such cases, the network may overfit the data.

```@setup overfit
using Plots
using Random

Random.seed!(666)

n = 10
x = rand(n)
y = x.^2 .+ 0.01*randn(n)

scatter(x,y)

X = zeros(n, n)
for i in 1:n
    X[:,i] = x.^(i-1)
end

w = X \ y

f(x) = sum([w[i]*x^(i-1) for i in 1:n])

x_plot = 0:0.001:1

scatter(x, y, label="Data", ylim=(-0.01,1.01), legend=:topleft)
plot!(x_plot, f.(x_plot), label="Prediction")
plot!(x_plot, x_plot.^2, label="True dependence")

savefig("Overfit.svg")
```

![](Overfit.svg)

This figure shows data with quadratic dependence and a small added error. While the complex classifier (a polynomial of order 9) fits the data perfectly, the correct classifier (a polynomial of order 2) does not fit the data so well, but it is much better at predicting unseen samples. The more complicated classifier overfits the data. 


#### Preventing overfitting

To prevent overfitting, multiple techniques were developed:
- *Early stopping* stops the algorithm before it finds the optimum. This goes against the spirit of optimization as the loss function is actually not optimized.
- *Regularization* adds a term to the objective funtion, usually the squared ``l_2`` norm of weights
  ```math
  \operatorname{minimize}\qquad \frac1n\sum_{i=1}^n \operatorname{loss}(y_i, f(w;x_i)) + \frac{\lambda}{2}\|w\|^2.
  ```
  The more complicated classifier from the figure above contains (among others) the term ``20222x^9``. Since the coefficient is huge, its ``l_2`` norm would be huge as well. Regularization prevents such classifiers. Another possibility is the (non-differentiable) ``l_1`` norm, which induces sparsity (many weights should be zero).
- *Simple networks* cannot approximate overly complicated functions and they can also prevent overfitting.


#### Train-test split

How should the classifier be evaluated? The figure above suggests that it is a bad idea to evaluate it on the same data on which they were trained. For this reason, the dataset is usually split into training and testing sets. The classifier is trained on the training and evaluated on the testing set. The classifier is not allowed to see the testing set during training. When the classifier contains a large number of hyperparameters, which need to be tuned, the dataset is split into training, validation and testing sets. Then models with multiple hyperparameters are trained on the training set, the best values of hyperparameters are selected on the validation set, and the classifier performance is evaluated on the testing set.


## Computation of gradients

We will derive the gradients for the most common case of the objective
```math
L(w) := \sum_{i=1}^n \operatorname{loss}(y_i, f(w;x_i)).
```
If the classifier has only a single output (as is the case for regression or binary classification), then the chain rule yields
```math
\nabla L(w) = \sum_{i=1}^n \operatorname{loss}'(y_i, f(w;x_i))\nabla_w f(w;x_i).
```
The most difficult term to compute is ``\nabla_w f(w;x_i)``. All neural networks presented in this course have a layered structure. For an input ``x``, the evalutation of ``f(w;x)`` is initialized by ``a_0=x`` and then the iterative update
```math
\begin{aligned}
z_m &= W_ma_{m-1} + b_m, \\
a_m &= l_m(z_m)
\end{aligned}
```
for ``m=1,\dots,M`` is performed. The first equation ``z_m = W_ma_{m-1} + b_m`` performs a linear mapping, while ``a_m = l_m(z_m)`` applies the activation function ``l_m`` to each component of ``z_m``. The parameters of the network are ``(W_m,b_m)_m``. Since ``a_M=f(w;x)``, the chain rule implies
```math
\begin{aligned}
\nabla_{W_m} f &= \nabla_{W_m}a_M = \nabla_{z_M}a_M\nabla_{z_{M-1}}a_M\nabla_{a_{M-1}}z_{M-1}\dots \nabla_{z_m}a_m\nabla_{W_m}z_m, \\
\nabla_{b_m} f &= \nabla_{b_m}a_M = \nabla_{z_M}a_M\nabla_{z_{M-1}}a_M\nabla_{a_{M-1}}z_{M-1}\dots \nabla_{z_m}a_m\nabla_{b_m}z_m.
\end{aligned}
```
Care needs to be taken with this expression, for example ``\nabla_{W_m}z_m`` differentiates a vector with respect to a matrix. The computation of ``\nabla_{W_m} f`` and ``\nabla_{b_m} f`` is almost the same and only the last term differs. This is the basics for an efficient computational procedure.

Now we need to compute the individual derivatives
```math
\begin{aligned}
\nabla_{a_{m-1}} z_m &= W_m, \\
\nabla_{z_m} a_m &= \operatorname{Diag}(l_m'(z_m)).
\end{aligned}
```
The derivative in ``l_m'(z_m)`` is understood componentwise and ``\operatorname{Diag}`` makes a diagonal matrix from the vector.

Combining all these relations allow computing the derivative of the whole network.

```@raw html
<div class = "info-body">
<header class = "info-header">Computation of derivatives</header><p>
```
This computation of derivatives looks complicated. However, it is just a complicated notation of a simple chain rule.

The forward pass starts with the input (which equals to ``a_0``), computes the values ``(z_m,a_m)`` in a forward way and finishes with evaluating the value of the classifier ``f``.

The backward pass starts with the classifier value (which equals to ``a_M``), computes the partial derivatives in a backward way and chains them together to finish evaluating the derivative of the classifier ``\nabla f``.

This computation is extremely efficient because the forward pass (computing function value) and the backward pass (computing derivatives) have the same complexity. Compare this with the finite difference method, where the computation of derivatives is much more expensive.
```@raw html
</p></div>
```

