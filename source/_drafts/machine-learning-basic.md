---
title: machine-learning-basic
tags:
---

## 機器學習

### 定義

a computer program is said to learn from experience E with respect to some task T and some performance measure P, if its performance on T, as measured by P, improves with experience E.

根據程式從經驗E中學習一些任務T和一些性能測量P，如果它在T上的表現（由P測量）隨經驗E而改善。

舉例：玩西洋棋

E = the experience of playing many games of checkers 玩很多遊戲的經驗

T = the task of playing checkers. 玩西洋棋

P = the probability that the program will win the next game. 這個程式贏得下一場的機率

### 分類

有很多種  learning algorithms，但主要分兩種：

- supervised learning: (明確知道這一筆資料屬於什麼類別)
- unsupervised learning: (不知道這一筆資料屬於什麼類別，可以請機器學習幫我分類)

其他：

- Reinforcement learning
- Recommender systems

## Supervised Learning

In supervised learning, we are given a data set and **already know what our correct output** should look like, having the idea that there is a relationship between the input and the output.

在 supervised learning 中，我們有足夠的 data set，並且已經知道正確輸出應該是什麼，而且認為輸入和輸出之間存在關係。

Supervised learning problems are categorized into "**regression**" and "**classification**" problems. In a regression problem, we are trying to predict results within a **continuous output**, meaning that we are trying to map input variables to some continuous function. In a classification problem, we are instead trying to predict results in a **discrete output**. In other words, we are trying to map input variables into discrete categories.

supervised learning 的問題分為“回歸”和“分類”問題。在回歸問題中，我們試圖在連續輸出中預測結果，這意味著我們正在嘗試將輸入變量映射到某個連續函數。在分類問題中，我們試圖在離散輸出中預測結果。換句話說，我們正在嘗試將輸入變量映射到離散類別。

- Regression problems: trying to predict results within a continuous output (a number)
- Classification problems: trying to predict results in a discrete output (type 1, type 2, type 3)

舉例：

- Regression - Given a picture of a person, we have to **predict their age** on the basis of the given picture
- Classification - Given a patient with a tumor, we have to **predict whether the tumor is malignant or benign**.

## Unsupervised Learning

Unsupervised learning allows us to approach problems with little or no idea what our results should look like. We can derive structure from data where we don't necessarily know the effect of the variables.

Unsupervised learning 能夠在少量的結果或根本不知道的結果下處理問題。我們可以從資料中導出結構，我們不一定知道變量的影響。

We can **derive this structure by clustering the data** based on relationships among the variables in the data.

我們可以通過資料中變量之間的關係對資料進行 clustering 來推導出這種結構。

With unsupervised learning there is **no feedback based on the prediction results**.

在 unsupervised learning 的情況下，沒有基於預測結果的反饋。

舉例：

- Given a set of news articles found on the web, group them into sets of articles about the same stories. 在網絡上發現的一組新聞文章，將它們分組成關於相同故事的文章集。
- Automatically Group: Given a database of customer data, automatically discover market segments and group customers into different market segments. 給定客戶的資料庫，自動細分市場並將客戶分組到不同的市場。
- Clustering: Take a collection of 1,000,000 different genes, and find a way to automatically group these genes into groups that are somehow similar or related by different variables, such as lifespan, location, roles, and so on. 收集1,000,000個不同的基因，並找到一種方法將這些基因自動分組到不同的相似或相關的不同變量組，如壽命，位置，角色等。
- Non-clustering: The "Cocktail Party Algorithm", allows you to find structure in a chaotic environment. (i.e. identifying individual voices and music from a mesh of sounds at a [cocktail party](https://en.wikipedia.org/wiki/Cocktail_party_effect)). 允許您在混亂的環境中查找結構。 （即在雞尾酒會上從聲音網格中識別個別聲音和音樂）。

## Linear Regression Model Representation and Cost Function

```flow
st=>operation: Training Set
la=>operation: Learning Algorithng
h=>operation: hypothesis

st->la->h
```

```flow
h=>operation: hypothesis
x=>start: X
y=>start: predicted Y

x(right)->h(right)->y
```

### Hypothesis

由於很古老的原因，在機器學習裡面，hypothesis 代表的是 function：
$$
h_\theta(x)=\theta_0+\theta_1x
$$

### Parameters

$$
\theta_0,\theta_1
$$

### Cost Function

Choose $$\theta_0,\theta_1$$ so that $$h_\theta(x)$$ is close to $$y$$ for our training example $$(x,y)$$

對於我們的 training example $$(x,y)$$  中，找到 $$\theta_0,\theta_1$$ 使得 $$h_\theta(x)$$ 會趨近於 $$y$$  

這個 Cost Function 又稱作為 Squared error function, Mean squared error：

$$
J(\theta_0,\theta_1)=\frac{1}{2m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})^2=\frac{1}{2m}\sum_{i=1}^{m}(\hat{y}-y^{(i)})^2
$$

The mean is halved $$\left(\frac{1}{2}\right)$$ as a convenience for the computation of the gradient descent, as the derivative term of the square function will cancel out the $$\frac{1}{2}$$ term.

因為 Mean squared error 的平方微分之後，會跟 $$\frac{1}{2}$$ 這個項目抵銷 

### Goal

$$
minimize{\theta_0,\theta_1}=J(\theta_0,\theta_1)
$$

## Gradient descent algorithm

- $$\theta_j$$
- $$\alpha$$
- $$-\frac{\delta}{\delta\theta_j}J(\theta_0,\theta_1)$$


$$
repeat until convergence {
    \theta_j
}
$$
需要同時修改 $$\theta_0, \theta_1$$，$$x_i,y_i$$ 分別代表 training set 的資料。  

### Convex function

Linear Regression 一定會是 Convex function，Convex function 指的是像碗一樣的 function (bowl-shaped function 不正式的說法)，這個 function 不會有 local optimal，只會存在一個 global optimal。

[Convex function Wiki](https://en.wikipedia.org/wiki/Convex_function)

### Batch Gradient descent

Batch: Each step of gradient descent uses all the training examples.

Batch 的意思是，每一步的 gradient descent 都會使用所有的 training examples

### vectorized implementation

```
theta = theta - alpha * X' * (X * theta - y) / m;
```

$$
θ:=θ−\frac{α}{m}X^{T}(Xθ−y)
$$



## Multiple Features

multivariate linear regression: Linear regression with multiple variables

$$x_j^{(i)}$$=value of feature j in the ith training example

$$x^{(i)}$$=the input (features) of the ith training example

$$m$$=the number of training examples

$$n$$=the number of features

### hypothesis function

$$
h_\theta(x)=\theta^Tx=\theta_0+\theta_1x_1+\theta_2x_2+\theta_3x_3+⋯+\theta_nx_n
$$

### Parameters

$$
\theta_1, \theta_2, ... ,\theta_n
$$

簡化成

$$
\theta
$$

### Cost Function

$$
J(\theta_1, \theta_2, ... ,\theta_n)=\frac{1}{2m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})^2
$$

簡化成

$$
J(\theta)=\frac{1}{2m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})^2
$$

$$
repeat\ until\ convergence:\ \{
   for j := 0...n 
\}
$$

## Feature Scaling

因為 θ 在 range 很小的時候會快速收斂，而且避免收斂的時候造成震盪。

Idea: Make sure features are on a similar scale. 這樣 Gradient Descent 就能更快地收斂
$$
x_i := \frac{x_i}{maximal\ value}
$$
介在 1 跟 -1 之間
$$
−1 ≤ x_{(i)}x^{(i)} ≤ 1
$$
或是，最大值減去最小值是 1
$$
-0.5 ≤ x_{(i)}x^{(i)} ≤ 0.5
$$


## Mean normalization

- $$s_i$$ is the **range** of values (max - min) or the standard deviation.
- $$\mu_i$$ is the **average** of all the values for feature (i)

Replace $$x_i$$ to $$x_i-\mu_i$$ make features have approximately zero mean.
$$
x_i := \frac{x_i−\mu_i}{s_i}
$$
注意：除以 range 或 standard deviation 會有不同的結果



## Learning Rate

**Debugging gradient descent.** Make a plot with *number of iterations* on the x-axis. Now plot the cost function, J(θ) over the number of iterations of gradient descent. If J(θ) ever increases, then you probably need to decrease α.

在x軸上繪製具有迭代次數的圖。現在繪製成本函數，J（θ）超過梯度下降的迭代次數。如果J（θ）增加，那麼你可能需要減少α。

**Automatic convergence test.** Declare convergence if J(θ) decreases by less than E in one iteration, where E is some small value such as 10−3. However in practice it's difficult to choose this threshold value.

如果J（θ）在一次迭代中減小小於E，則聲明收斂，其中E是一些小值，例如10-3。但是在實踐中很難選擇這個閾值。

### 總結：

- If $$\alpha$$ is too small: slow convergence.
- If $$\alpha$$ is too large: ￼may not decrease on every iteration and thus may not converge

## Polynomial Regression

$$
h_\theta(x)=\theta_0+\theta_1x^1+\theta_2x^2+\theta_3x^3
$$

只要將 $$x_2=x_1^2$$ 且 $$x_3 = x_1^3$$ 就能符合原先的 feature model

One important thing to keep in mind is, if you choose your features this way then feature scaling becomes very important.

## Normal Equation

There is **no need** to do feature scaling with the normal equation

使用 normal equation 就不用的別使用 feature scaling，但 gradient descent 就很需要 feature scaling。

| Gradient Descent                             | Normal Equation                                    |
| -------------------------------------------- | -------------------------------------------------- |
| Need to choose alpha                         | No need to choose alpha                            |
| Needs many iterations                        | No need to iterate                                 |
| $$O (kn^2)$$                                 | $$O (n^3)$$, need to calculate inverse of $$X^TX$$ |
| Works well when n is large (more than 10000) | Slow if n is very large                            |
| 所有演算法都能使用 Gradient Descent          | 有些分類演算法不能使用 Normal Equation             |

### Normal Equation Noninvertibility

在 Octave 下，即使 $$ X^TX$$ 是 not invertible，不能使用 inv function 執行，但可以使用 pinv function。

造成 $$ X^TX$$ 是 not invertible 的原因：

- Redundant features, where two features are very closely related (i.e. they are linearly dependent)
- Too many features (e.g. m ≤ n). In this case, delete some features or use "regularization" (to be explained in a later lesson).

> learning rate 只能用猜的嗎？
> 自動調整 learning rate 的 machine learning，有這個東西嗎？
> 可以動態調整 learning rate 嗎，可能在算完300部之後再調整？
> 可以建立很多次方的 model 嗎？不會用到的話會退化回來嗎？(3次->2次)
> 妳在做的研究 feature 有多少個啊？
> TPU Normal Equation 計算 benchmark，可以容納多少的 features n 還不會變慢c

## Classification

由於 Classification 的 y = 0 or 1

然而 $$h_\theta(x)$$ 可以是 >1 或 <0

### Logistic Regression

雖然名字裡面有個 Regression，不過他是 Classification 演算法的其中一個

Logistic Regression 可以把 $$0≤h_\theta(x)≤1$$ 

### Logistic Regression Model

$$h_\theta(x)$$ to satisfy $$0≤h_\theta(x)≤1$$ 

Sigmoid Function or Logistic Function

The function g(z), shown here, maps any real number to the (0, 1) interval, making it useful for transforming an arbitrary-valued function into a function better suited for classification.
$$
h_\theta(x)=g(\theta^{T}x)\\
z=\theta^{T}x\\
g(z)=\frac{1}{1+e^{−z}}
$$

hθ(x) will give us the **probability** that our output is 1.
$$
h_\theta(x)=P(y=1|x;θ)=1−P(y=0|x;θ)\\
P(y=0|x;θ)+P(y=1|x;θ)=1
$$

## Decision Boundary

In order to get our discrete 0 or 1 classification, we can translate the output of the hypothesis function as follows:

$$
h_\theta(x)≥0.5→y=1\\
h_\theta(x)<0.5→y=0
$$

$$
h_\theta(x)=g(\theta^{T}x)≥0.5\\
when\ \theta^{T}x≥0
$$

$$
\theta^{T}x≥0⇒y=1\\
\theta^{T}x<0⇒y=0
$$

The **decision boundary** is the line that separates the area where y = 0 and where y = 1. It is created by our hypothesis function.

Again, the input to the sigmoid function g(z) doesn't need to be linear, and could be a function that describes a circle or any shape to fit our data.

## Logistic Regression Model

We cannot use the same cost function that we use for linear regression because the Logistic Function will cause the output to be wavy, causing many local optima. In other words, it will not be a convex function.



### hyposisth function

$$
h_\theta(x)=g(\theta^{T}x)\\
z=\theta^{T}x\\
g(z)=\frac{1}{1+e^{−z}}
$$

### Cost Function

不是 convex function，使用 root mean square 方法計算 cost function 會有很多 local optima，所以要寫成像下面的 cost function。

Note that writing the cost function in this way guarantees that J(θ) is convex for logistic regression.

以這種方式編寫成本函數可以保證 $$J(θ)$$ 對於邏輯回歸是 convex。
$$
J(θ)=1m∑i=\frac{1}{m}Cost(hθ(x^{(i)}),y^{(i)})\\
Cost(h_θ(x),y)=−log(h_θ(x))\ if\ y = 1\\
Cost(h_θ(x),y)=−log(1−h_θ(x))\ if\ y = 0
$$


$$
Cost(h_θ(x),y)=0\ if\ h_θ(x)=y\\
Cost(h_θ(x),y)→∞\ if\ y=0\ and\ h_θ(x)→1\\
Cost(h_θ(x),y)→∞\ if\ y=1\ and\ h_θ(x)→0
$$
We can compress our cost function's two conditional cases into one case:
$$
Cost(h_θ(x),y)=−y\ log(h_θ(x))−(1−y)log(1−h_θ(x))
$$

$$
J(θ)=\frac{1}{m}\sum_{i=1}^{m}{Cost(h_θ(x^{(i)}),y^{(i)})}
=−\frac{1}{m}\sum_{i=1}^{m}{[y^{(i)}log(h_θ(x^{(i)}))+(1−y^{(i)})log(1−h_θ(x^{(i)}))]}
$$

### Gradient Descent

$$
Repeat\{
θ_j:=θ_j−α\frac{∂}{∂θ_j}J(θ)
\}
$$

將前面的 cost function 帶入可得同樣的結果：
$$
Repeat\{\\
θ_j:=θ_j−\frac{α}{m}\sum_{i=1}^{m}{(h_θ(x^{(i)})−y^{(i)})x^{(i)}_j}\\
\}
$$
vectorized implementation
$$
θ:=θ−\frac{α}{m}X^{T}(g(Xθ)−y)
$$

### Advanced Optimization

"Conjugate gradient", "BFGS", and "L-BFGS" are more sophisticated, faster ways to optimize θ that can be used instead of gradient descent.

We first need to provide a function that evaluates the following two functions for a given input value θ:
$$
J(θ)\\
\frac{∂}{∂θ_j}J(θ)
$$

```octave
function [jVal, gradient] = costFunction(theta)
  jVal = [...code to compute J(theta)...];
  gradient = [...code to compute derivative of J(theta)...];
end
```

Then we can use octave's "fminunc()" optimization algorithm along with the "optimset()" function that creates an object containing the options we want to send to "fminunc()"

```octave
options = optimset('GradObj', 'on', 'MaxIter', 100);
initialTheta = zeros(2,1);
[optTheta, functionVal, exitFlag] = fminunc(@costFunction, initialTheta, options);
```

## Multiclass Classification: One-vs-all

To summarize:

Train a logistic regression classifier $$h_\theta(x)$$ for each class￼ to predict the probability that ￼ ￼y = i￼ ￼.

To make a prediction on a new x, pick the class ￼that maximizes $$h_\theta (x)$$



Adding polynomial features could increase how well we can fit the training data. TRUE



## The Problem of Overfitting

- Underfitting, or high bias
- overfitting, or high variance

This terminology is applied to both linear and logistic regression.

There are two main options to address the issue of overfitting:

1) Reduce the number of features:

- Manually select which features to keep.
- Use a model selection algorithm (studied later in the course).

2) Regularization

- Keep all the features, but reduce the magnitude of parameters $$\theta_j$$.
- Regularization works well when we have a lot of slightly useful features.

### Regularization

What if lambda $$\lambda$$ is set to an extremely large value? Algorithm results in underfitting (fails to fit even the training set).


$$
min_\theta\ \dfrac{1}{2m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2 + \lambda\ \sum_{j=1}^n \theta_j^2
$$
The λ, or lambda, is the **regularization parameter**. It determines how much the costs of our theta parameters are inflated.

If lambda is chosen to be too large, it may smooth out the function too much and cause underfitting. Otherwise.

### Regularized Linear Regression

#### Gradient Descent

$$
\begin{align*}
& \text{Repeat}\ \lbrace \newline
& \ \ \ \ \theta_0 := \theta_0 - \alpha\ \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_0^{(i)} \newline
& \ \ \ \ \theta_j := \theta_j - \alpha\ \left[ \left( \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)} \right) + \frac{\lambda}{m}\theta_j \right]
&\ \ \ \ \ \ \ \ \ \ j \in \lbrace 1,2...n\rbrace\newline
& \rbrace \end{align*}
$$

也可以表示成：
$$
\theta_j := \theta_j(1 - \alpha\frac{\lambda}{m}) - \alpha\frac{1}{m}\sum_{i=1}^m(h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)}
$$
$$1−α\frac{λ}{m}$$ 永遠小於 1



#### Normal Equation

$$
\begin{align*}& \theta = \left( X^TX + \lambda \cdot L \right)^{-1} X^Ty \newline& \text{where}\ \ L = \begin{bmatrix} 0 & & & & \newline & 1 & & & \newline & & 1 & & \newline & & & \ddots & \newline & & & & 1 \newline\end{bmatrix}\end{align*}
$$

If m < n, then $$X^TX$$ is non-invertible. However, when we add the term λ⋅L, then $$X^TX + λ⋅L$$ becomes invertible.

### Regularized Logistic Regression

The original Logistic Regression cost function was:
$$
J(\theta) = - \frac{1}{m} \sum_{i=1}^m \large[ y^{(i)}\ \log (h_\theta (x^{(i)})) + (1 - y^{(i)})\ \log (1 - h_\theta(x^{(i)})) \large]
$$
regularize the equation:
$$
J(\theta) = - \frac{1}{m} \sum_{i=1}^m \large[ y^{(i)}\ \log (h_\theta (x^{(i)})) + (1 - y^{(i)})\ \log (1 - h_\theta(x^{(i)}))\large] + \frac{\lambda}{2m}\sum_{j=1}^n \theta_j^2
$$
for j from 1 to n, skipping 0



Using a very large value of λ cannot hurt the performance of your hypothesis; the only reason we do not set λ to be too large is to avoid numerical problems.



Adding a new feature to the model always results in equal or better performance on the training set. TRUE

Adding many new features to the model helps prevent overfitting on the training set. FALSE

Introducing regularization to the model always results in equal or better performance on examples not in the training set. FALSE

Introducing regularization to the model always results in equal or better performance on the training set. FALSE (If we introduce too much regularization, we can underfit the training set and have worse performance on the training set.)



## Non-linear Hypotheses

當 feature n 很大的時候 (ie.10000)，然後使用二次方程式把所有可能的項組合起來，大約會有 $$\frac{n^2}{2}$$ (ie. 5 x 10^7) 個項。 



## Neuronal Network

- $$x_0$$ input node is sometimes called the "bias unit." It is always equal to 1.
- In neural networks, we use the same logistic function as in classification, $$\frac{1}{1 + e^{-\theta^Tx}}$$, yet we sometimes call it a sigmoid (logistic) **activation** function.
- "theta" parameters are sometimes called "weights"

### Model Representation

Visually, a simplistic representation looks like:
$$
\begin{bmatrix}x_0 \newline x_1 \newline x_2 \newline \end{bmatrix}\rightarrow\begin{bmatrix}\ \ \ \newline \end{bmatrix}\rightarrow h_\theta(x)
$$
Our input nodes (layer 1), also known as the "input layer", go into another node (layer 2), which finally outputs the hypothesis function, known as the "output layer".

We can have intermediate layers of nodes between the input and output layers called the "hidden layers."

In this example, we label these intermediate or "hidden" layer nodes $$a^2_0⋯a^2_n$$ and call them "activation units."
$$
\begin{align*}& a_i^{(j)} = \text{"activation" of unit $i$ in layer $j$} \newline& \Theta^{(j)} = \text{matrix of weights controlling function mapping from layer $j$ to layer $j+1$}\end{align*}
$$
If we had one hidden layer, it would look like:
$$
\begin{bmatrix}x_0 \newline x_1 \newline x_2 \newline x_3\end{bmatrix}\rightarrow\begin{bmatrix}a_1^{(2)} \newline a_2^{(2)} \newline a_3^{(2)} \newline \end{bmatrix}\rightarrow h_\theta(x)
$$
The values for each of the "activation" nodes is obtained as follows:
$$
\begin{align*} a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3) \newline a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3) \newline a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3) \newline h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)}) \newline \end{align*}
$$
This is saying that we compute our activation nodes by using a 3×4 matrix of parameters.

If network has $$s_j$$ units in layer $$j$$ and $$s_j+1$$ units in layer $$j+1$$, then $$Θ^{(j)}$$ will be of dimension $$s_{j+1}×(s_j+1)$$.

The +1 comes from the addition in Θ(j) of the "bias nodes," $$x_0$$ and $$Θ^{(j)}_0$$. In other words the output nodes will not include the bias nodes while the inputs will. 



Is Random Layer possible?


$$
\begin{align*} a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3) \newline a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3) \newline a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3) \newline h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)}) \newline \end{align*}
$$

$$
\begin{align*}a_1^{(2)} = g(z_1^{(2)}) \newline a_2^{(2)} = g(z_2^{(2)}) \newline a_3^{(2)} = g(z_3^{(2)}) \newline \end{align*}
$$

$$
z_k^{(2)} = \Theta_{k,0}^{(1)}x_0 + \Theta_{k,1}^{(1)}x_1 + \cdots + \Theta_{k,n}^{(1)}x_n
$$

$$
z^{(j)} = \Theta^{(j-1)}a^{(j-1)}
$$
our result is a single number. We then get our final result with:
$$
h_\Theta(x) = a^{(j+1)} = g(z^{(j+1)})
$$
This will cause the output of our hypothesis to only be positive if both $$x_1$$ and $$x_2$$ are 1. In other words:
$$
\begin{align*}& h_\Theta(x) = g(-30 + 20x_1 + 20x_2) \newline \newline & x_1 = 0 \ \ and \ \ x_2 = 0 \ \ then \ \ g(-30) \approx 0 \newline & x_1 = 0 \ \ and \ \ x_2 = 1 \ \ then \ \ g(-10) \approx 0 \newline & x_1 = 1 \ \ and \ \ x_2 = 0 \ \ then \ \ g(-10) \approx 0 \newline & x_1 = 1 \ \ and \ \ x_2 = 1 \ \ then \ \ g(10) \approx 1\end{align*}
$$
So we have constructed one of the fundamental operations in computers by using a small neural network rather than using an actual AND gate.



##### AND

$$
Θ^{(i)}=[−30, 20, 20]
$$

##### OR

$$
Θ^{(i)}=[−10, 20, 20]
$$

##### NOT

$$
Θ^{(i)}=[10, -20]
$$



##### (NOT x1) AND (NOT x2)

NOT (x1 OR x2)

$$
Θ^{(i)}=[10, -20, -20]
$$

##### XNOR

$$
\Theta^{(1)} =\begin{bmatrix}-30 & 20 & 20 \newline 10 & -20 & -20\end{bmatrix}\\
\Theta^{(2)} =\begin{bmatrix}-10 & 20 & 20\end{bmatrix}
$$

### Multiclass Classification

$$
h_\Theta(x) =\begin{bmatrix}0 \newline 0 \newline 1 \newline 0 \newline\end{bmatrix}
$$



### Implementation Note: Unrolling Parameters

In order to use optimizing functions such as "fminunc()", we will want to "unroll" all the elements and put them into one long vector:

```octave
thetaVector = [ Theta1(:); Theta2(:); Theta3(:); ]
deltaVector = [ D1(:); D2(:); D3(:) ]
```

If the dimensions of Theta1 is 10x11, Theta2 is 10x11 and Theta3 is 1x11, then we can get back our original matrices from the "unrolled" versions as follows:

```octave
Theta1 = reshape(thetaVector(1:110),10,11)
Theta2 = reshape(thetaVector(111:220),10,11)
Theta3 = reshape(thetaVector(221:231),1,11)
```



## Backpropagation

### Cost Function

Let's first define a few variables that we will need to use:

- L = total number of layers in the network
- s_lsl = number of units (not counting bias unit) in layer l
- K = number of output units/classes

We denote $$h_Θ(x)_k$$ as being a hypothesis that results in the $$k^{th}$$ output. Our cost function for neural networks is going to be a generalization of the one we used for logistic regression. Recall that the cost function for regularized logistic regression was:
$$
J(\theta) = - \frac{1}{m} \sum_{i=1}^m [ y^{(i)}\ \log (h_\theta (x^{(i)})) + (1 - y^{(i)})\ \log (1 - h_\theta(x^{(i)}))] + \frac{\lambda}{2m}\sum_{j=1}^n \theta_j^2
$$
For neural networks, it is going to be slightly more complicated:
$$
\begin{gather*} J(\Theta) = - \frac{1}{m} \sum_{i=1}^m \sum_{k=1}^K \left[y^{(i)}_k \log ((h_\Theta (x^{(i)}))_k) + (1 - y^{(i)}_k)\log (1 - (h_\Theta(x^{(i)}))_k)\right] + \frac{\lambda}{2m}\sum_{l=1}^{L-1} \sum_{i=1}^{s_l} \sum_{j=1}^{s_{l+1}} ( \Theta_{j,i}^{(l)})^2\end{gather*}
$$
We have added a few nested summations to account for our multiple output nodes. In the first part of the equation, before the square brackets, we have an additional nested summation that loops through the number of output nodes.

In the regularization part, after the square brackets, we must account for multiple theta matrices. The number of columns in our current theta matrix is equal to the number of nodes in our current layer (including the bias unit). The number of rows in our current theta matrix is equal to the number of nodes in the next layer (excluding the bias unit). As before with logistic regression, we square every term.

Note:

- the double sum simply adds up the logistic regression costs calculated for each cell in the output layer
- the triple sum simply adds up the squares of all the individual Θs in the entire network.
- the i in the triple sum does **not** refer to training example i

### Backpropagation Algorithm

"Backpropagation" is neural-network terminology for minimizing our cost function, just like what we were doing with gradient descent in logistic and linear regression. Our goal is to compute:
$$
min_ΘJ(Θ)
$$


That is, we want to minimize our cost function J using an optimal set of parameters in theta. In this section we'll look at the equations we use to compute the partial derivative of J(Θ):
$$
\frac∂{∂Θ^{(l)}_{i,j}}J(Θ)
$$

#### Back propagation Algorithm

Given training set 
$$
\lbrace (x^{(1)}, y^{(1)}) \cdots (x^{(m)}, y^{(m)})\rbrace
$$

- Set $$\Delta^{(l)}_{i,j}$$ := 0 for all (l,i,j), (hence you end up having a matrix full of zeros)

For training example t =1 to m:

1. Set $$a^{(1)} := x^{(t)}$$

2. Perform forward propagation to compute $$a^{(l)}$$ for l=2,3,…,L

3. Using $$y^{(t)}$$, compute $$\delta^{(L)} = a^{(L)} - y^{(t)}$$

   Where L is our total number of layers and $$a^{(L)}$$ is the vector of outputs of the activation units for the last layer. So our "error values" for the last layer are simply the differences of our actual results in the last layer and the correct outputs in y. To get the delta values of the layers before the last layer, we can use an equation that steps us back from right to left:

4. Compute $$\delta^{(L-1)}, \delta^{(L-2)},\dots,\delta^{(2)}$$ using $$δ^{(l)}=((Θ^{(l)})^Tδ^{(l+1)}) .∗ a^{(l)} .∗ (1−a^{(l)})$$

   The delta values of layer l are calculated by multiplying the delta values in the next layer with the theta matrix of layer l. We then element-wise multiply that with a function called g', or g-prime, which is the derivative of the activation function g evaluated with the input values given by $$z^{(l)}$$.

   The g-prime derivative terms can also be written out as:
   $$
   g'(z^{(l)}) = a^{(l)}\ .*\ (1 - a^{(l)})
   $$

5. $$\Delta^{(l)}_{i,j} := \Delta^{(l)}_{i,j} + a_j^{(l)} \delta_i^{(l+1)}$$ or with vectorization, $$\Delta^{(l)} := \Delta^{(l)} + \delta^{(l+1)}(a^{(l)})^T$$

   Hence we update our new $$\Delta$$ matrix.

   - $$D^{(l)}_{i,j}:=1m(Δ^{(l)}_{i,j}+λΘ^{(l)}_{i,j})$$, if j≠0.
   - $$D^{(l)}_{i,j} := \dfrac{1}{m} \Delta^{(l)}_{i,j}$$ If j=0

   The capital-delta matrix D is used as an "accumulator" to add up our values as we go along and eventually compute our partial derivative. Thus we get 
   $$
   \frac \partial {\partial \Theta_{ij}^{(l)}} J(\Theta)
   $$






If we consider simple non-multiclass classification (k = 1) and disregard regularization, the cost is computed with:
$$
cost(t) =y^{(t)} \ \log (h_\Theta (x^{(t)})) + (1 - y^{(t)})\ \log (1 - h_\Theta(x^{(t)}))
$$
Intuitively, $$\delta_j^{(l)}$$ is the "error" for $$a^{(l)}_j$$ (unit j in layer l). More formally, the delta values are actually the derivative of the cost function:
$$
\delta_j^{(l)} = \dfrac{\partial}{\partial z_j^{(l)}} cost(t)
$$
to calculate $$\delta_2^{(2)}$$, we multiply the weights $$Θ^{(2)}_{12}$$ and $$Θ^{(2)}_{22}$$ by their respective $$\delta$$ values found to the right of each edge.
$$
δ_2^{(2)}= Θ^{(2)}_{12}\delta_1^{(3)}+Θ^{(2)}_{22}\delta_2^{(3)}
$$
To calculate every single possible $$\delta_j^{(l)}$$, we could start from the right of our diagram. We can think of our edges as our $$Θ_{ij}$$. Going from right to left, to calculate the value of $$\delta_j^{(l)}$$, you can just take the over all sum of each weight times the $$\delta$$ it is coming from. Hence, another example would be $$\delta_2^{(3)}=Θ^{(3)}_{12}*\delta_1^{(4)}$$.



### Gradient Checking

Gradient checking will assure that our backpropagation works as intended. We can approximate the derivative of our cost function with:
$$
\dfrac{\partial}{\partial\Theta}J(\Theta) \approx \dfrac{J(\Theta + \epsilon) - J(\Theta - \epsilon)}{2\epsilon}
$$

$$
\dfrac{\partial}{\partial\Theta_j}J(\Theta) \approx \dfrac{J(\Theta_1, \dots, \Theta_j + \epsilon, \dots, \Theta_n) - J(\Theta_1, \dots, \Theta_j - \epsilon, \dots, \Theta_n)}{2\epsilon}
$$
A small value for ϵ (epsilon) such as ϵ=10^−4, guarantees that the math works out properly. If the value for ϵ is too small, we can end up with numerical problems.

```octave
epsilon = 1e-4;
for i = 1:n,
  thetaPlus = theta;
  thetaPlus(i) += epsilon;
  thetaMinus = theta;
  thetaMinus(i) -= epsilon;
  gradApprox(i) = (J(thetaPlus) - J(thetaMinus))/(2*epsilon)
end;
```

Once you have verified **once** that your backpropagation algorithm is correct, you don't need to compute gradApprox again. The code to compute gradApprox can be very slow.

### Random Initialization

Initializing all theta weights to zero does not work with neural networks. When we backpropagate, all nodes will update to the same value repeatedly. Instead we can randomly initialize our weights for our Θ matrices using the following method:

Hence, we initialize each Θ(l)ij to a random value between[−ϵ,ϵ]. Using the above formula guarantees that we get the desired bound. The same procedure applies to all the Θ's. Below is some working code you could use to experiment.

```octave
Theta1 = rand(10,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
Theta2 = rand(10,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
Theta3 = rand(1,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
```

Consider this procedure for initializing the parameters of a neural network:

1. Pick a random number r = rand(1,1) * (2 * INIT_EPSILON) - INIT_EPSILON;
2. Set Θ(l)ij=r for all i,j,li,j,l.

Does this work?

No, because this fails to break symmetry. (why?)

### Putting it Together

First, pick a network architecture; choose the layout of your neural network, including how many hidden units in each layer and how many layers in total you want to have.

- Number of input units = dimension of features $$x^{(i)}$$
- Number of output units = number of classes
- Number of hidden units per layer = usually more the better (must balance with cost of computation as it increases with more hidden units)
- Defaults: 1 hidden layer. If you have more than 1 hidden layer, then it is recommended that you have the same number of units in every hidden layer.

#### Training a Neural Network

1. Randomly initialize the weights
2. Implement forward propagation to get $$hΘ(x(i))$$ for any$$ x^{(i)}$$
3. Implement the cost function
4. Implement backpropagation to compute partial derivatives
5. Use gradient checking to confirm that your backpropagation works. Then disable gradient checking.
6. Use gradient descent or a built-in optimization function to minimize the cost function with the weights in theta.

When we perform forward and back propagation, we loop on every training example:

```octave
for i = 1:m,
   Perform forward propagation and backpropagation using example (x(i),y(i))
   (Get activations a(l) and delta terms d(l) for l = 2,...,L
```

## Evaluating a Learning Algorithm

### Evaluating a Hypothesis

Once we have done some trouble shooting for errors in our predictions by:

- Getting more training examples
- Trying smaller sets of features
- Trying additional features
- Trying polynomial features
- Increasing or decreasing λ

We can move on to evaluate our new hypothesis.

A hypothesis may have a low error for the training examples but still be inaccurate (because of overfitting). Thus, to evaluate a hypothesis, given a dataset of training examples, we can split up the data into two sets: a **training set** and a **test set**. Typically, the training set consists of 70 % of your data and the test set is the remaining 30 %.

The new procedure using these two sets is then:

1. Learn Θ and minimize $$J_{train}(Θ)$$ using the training set
2. Compute the test set error $$J_{test}(Θ)$$

#### The test set error

1. For linear regression: $$J_{test}(Θ)=\frac1{2m_{test}}\sum^{m_{test}}_{i=1}(h_Θ(x^{(i)}_{test})−y^{(i)}_{test})^2$$
2. For classification ~ Misclassification error (aka 0/1 misclassification error):

$$
err(h_\Theta(x),y) = \begin{matrix} 1 & \mbox{if } h_\Theta(x) \geq 0.5\ and\ y = 0\ or\ h_\Theta(x) < 0.5\ and\ y = 1\newline 0 & \mbox otherwise \end{matrix}
$$

This gives us a binary 0 or 1 error result based on a misclassification. The average test error for the test set is:
$$
\text{Test Error} = \dfrac{1}{m_{test}} \sum^{m_{test}}_{i=1} err(h_\Theta(x^{(i)}_{test}), y^{(i)}_{test})
$$
This gives us the proportion of the test data that was misclassified.

### Model Selection and Train/Validation/Test Sets

Just because a learning algorithm fits a training set well, that does not mean it is a good hypothesis. It could over fit and as a result your predictions on the test set would be poor. The error of your hypothesis as measured on the data set with which you trained the parameters will be lower than the error on any other data set.

Given many models with different polynomial degrees, we can use a systematic approach to identify the 'best' function. In order to choose the model of your hypothesis, you can test each degree of polynomial and look at the error result.

One way to break down our dataset into the three sets is:

- Training set: 60%
- Cross validation set: 20%
- Test set: 20%

We can now calculate three separate error values for the three different sets using the following method:

1. Optimize the parameters in Θ using the **training set** for each polynomial degree.
2. Find the polynomial degree d with the least error using the **cross validation set**.
3. Estimate the generalization error using the **test set** with $$J_{test}(Θ^{(d)})$$, (d = theta from polynomial with lower error);

This way, the degree of the polynomial d has not been trained using the test set.

## Bias vs. Variance

### Diagnosing Bias vs. Variance

In this section we examine the relationship between the degree of the polynomial d and the underfitting or overfitting of our hypothesis.

- We need to distinguish whether **bias** or **variance** is the problem contributing to bad predictions.
- High bias is underfitting and high variance is overfitting. Ideally, we need to find a golden mean between these two.

The training error will tend to **decrease** as we increase the degree d of the polynomial.

At the same time, the cross validation error will tend to **decrease** as we increase d up to a point, and then it will **increase** as d is increased, forming a convex curve.

**High bias (underfitting)**: both $$J_{train}(Θ)$$ and $$J_{CV}(Θ)$$ will be high. Also, $$J_{CV}(Θ)≈J_{train}(Θ)$$.

**High variance (overfitting)**: $$J_{train}(Θ)$$ will be low and $$J_{CV}(Θ)$$ will be much greater than $$J_{train}(Θ)$$.

The is summarized in the figure below:

![img](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/I4dRkz_pEeeHpAqQsW8qwg_bed7efdd48c13e8f75624c817fb39684_fixed.png?expiry=1554422400000&hmac=pG54D0uh-VZmzpK0MclNrVnRTDjl2snFtgm5J2zCvvg)

### Regularization and Bias/Variance

we see that as λ increases, our fit becomes more rigid. On the other hand, as λ approaches 0, we tend to over overfit the data. So how do we choose our parameter λ to get it 'just right' ? In order to choose the model and the regularization term λ, we need to:

1. Create a list of lambdas (i.e. λ∈{0,0.01,0.02,0.04,0.08,0.16,0.32,0.64,1.28,2.56,5.12,10.24});
2. Create a set of models with different degrees or any other variants.
3. Iterate through the λs and for each λ go through all the models to learn some Θ.
4. Compute the cross validation error using the learned Θ (computed with λ) on the $$J_{CV}(Θ)$$ **without** regularization or λ = 0.
5. Select the best combo that produces the lowest error on the cross validation set.
6. Using the best combo Θ and λ, apply it on $$J_{test}(Θ)$$ to see if it has a good generalization of the problem.

### Learning Curves

Training an algorithm on a very few number of data points (such as 1, 2 or 3) will easily have 0 errors because we can always find a quadratic curve that touches exactly those number of points. Hence:

- As the training set gets larger, the error for a quadratic function increases.
- The error value will plateau out after a certain m, or training set size.

#### Experiencing high bias:

**Low training set size**: causes $$J_{train}(Θ)$$ to be low and $$J_{CV}(Θ)$$ to be high.

**Large training set size**: causes both $$J_{train}(Θ)$$ and $$J_{CV}(Θ)$$ to be high with $$J_{train}(Θ)≈J_{CV}(Θ)$$.

If a learning algorithm is suffering from **high bias**, getting more training data will not **(by itself)** help much.

![img](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/bpAOvt9uEeaQlg5FcsXQDA_ecad653e01ee824b231ff8b5df7208d9_2-am.png?expiry=1554422400000&hmac=IbvFHesrQ02jjq8xFlPrd0AL3w5cGYos6HJhd6LlP44)

#### Experiencing high variance:

**Low training set size**: $$J_{train}(Θ)$$ will be low and $$J_{CV}(Θ)$$ will be high.

**Large training set size**: $$J_{train}(Θ)$$ increases with training set size and $$J_{CV}(Θ)$$ continues to decrease without leveling off. Also, $$J_{train}(Θ)<J_{CV}(Θ)$$ but the difference between them remains significant.

If a learning algorithm is suffering from **high variance**, getting more training data is likely to help.

![img](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/vqlG7t9uEeaizBK307J26A_3e3e9f42b5e3ce9e3466a0416c4368ee_ITu3antfEeam4BLcQYZr8Q_37fe6be97e7b0740d1871ba99d4c2ed9_300px-Learning1.png?expiry=1554422400000&hmac=6llIqtjJx4UqtNLFm8pc2L-fc4bVIi03mNEZxQYi_xo)

### Deciding What to Do Next Revisited

Our decision process can be broken down as follows:

- **Getting more training examples:** Fixes high variance
- **Trying smaller sets of features:** Fixes high variance
- **Adding features:** Fixes high bias
- **Adding polynomial features:** Fixes high bias
- **Decreasing λ:** Fixes high bias
- **Increasing λ:** Fixes high variance.

#### Diagnosing Neural Networks

- A neural network with fewer parameters is **prone to underfitting**. It is also **computationally cheaper**.
- A large neural network with more parameters is **prone to overfitting**. It is also **computationally expensive**. In this case you can use regularization (increase λ) to address the overfitting.

Using a single hidden layer is a good starting default. You can train your neural network on a number of hidden layers using your cross validation set. You can then select the one that performs best.

##### Model Complexity Effects:

- Lower-order polynomials (low model complexity) have high bias and low variance. In this case, the model fits poorly consistently.
- Higher-order polynomials (high model complexity) fit the training data extremely well and the test data extremely poorly. These have low bias on the training data, but very high variance.
- In reality, we would want to choose a model somewhere in between, that can generalize well but also fits the data reasonably well.

## Build a spam classifier

### System Design Example:

Given a data set of emails, we could construct a vector for each email. Each entry in this vector represents a word. The vector normally contains 10,000 to 50,000 entries gathered by finding the most frequently used words in our data set. If a word is to be found in the email, we would assign its respective entry a 1, else if it is not found, that entry would be a 0. Once we have all our x vectors ready, we train our algorithm and finally, we could use it to classify if an email is a spam or not.

So how could you spend your time to improve the accuracy of this classifier?

- Collect lots of data (for example "honeypot" project but doesn't always work)
- Develop sophisticated features (for example: using email header data in spam emails)
- Develop algorithms to process your input in different ways (recognizing misspellings in spam).

It is difficult to tell which of the options will be most helpful.

### Error Analysis

The recommended approach to solving machine learning problems is to:

- Start with a simple algorithm, implement it quickly, and test it early on your cross validation data.
- Plot learning curves to decide if more data, more features, etc. are likely to help.
- Manually examine the errors on examples in the cross validation set and try to spot a trend where most of the errors were made.

It is very important to get error results as a single, numerical value. Otherwise it is difficult to assess your algorithm's performance. For example if we use stemming, which is the process of treating the same word with different forms (fail/failing/failed) as one word (fail), and get a 3% error rate instead of 5%, then we should definitely add it to our model. However, if we try to distinguish between upper case and lower case letters and end up getting a 3.2% error rate instead of 3%, then we should avoid using this new feature. Hence, we should try new things, get a numerical value for our error rate, and based on our result decide whether we want to keep the new feature or not.

## Handling Skewed Classes

如果檢測癌症的正確率是 99%，只有 1% 的人會判斷錯誤，聽起來這個 model 是很好的，但是如果事實上只有 0.5% 的人有癌症，這樣設計一個 model，判斷所有的病人都沒有罹患癌症，正確率 99.5% 會比原先的來得高，這個 model 就失去了分類的能力了。

### Error Metrics for Skewed Classes

|                   | Actual class 1 | Actual class 0 |
| ----------------- | -------------- | -------------- |
| Predicted class 1 | True positive  | False positive |
| Predicted class 0 | False negative | True negative  |

### Precision/Recall

兩個數值可以評斷 model 的好壞，都要越高越好，如果 recall 太低、precision 很高的話，很有可能發生 skewed classes
$$
Precision=\frac{True\ positive}{True\ positive+False\ positive}
$$

$$
Recall=\frac{True\ positive}{True\ positive+False\ negative}
$$



### F1 Score

判斷 Precision 跟 Recall 的好壞：
$$
2\frac{PR}{P+R}
$$

- Predict y = 1y=1 if $$h_\theta(x) \geq \text{threshold}$$
- Predict y = 0y=0 if $$h_\theta(x) < \text{threshold}$$

Measure precision (P) and recall (R) on the **cross validation set** and choose the value of threshold which maximizes $$2\frac{PR}{P+R}$$

### Summary

- Accuracy = (true positives + true negatives) / (total examples)
- Precision = (true positives) / (true positives + false positives)
- Recall = (true positives) / (true positives + false negatives)
- F_1F1 score = (2 * precision * recall) / (precision + recall)

## SVM

support vector machine

large margin classifier

#### Hypothesis

$$
min_θ C [\sum_{i=1}^{m}y^{(i)}cost_1(θ^Tx^{(i)})+(1−y^{(i)})cost_0(θ^Tx^{(i)})]+\frac12\sum_{j=1}^nθ^2_j
$$

$$
cost_1(\theta^Tx^{(i)}))=-log\ h_\theta(x^{(i)})\\
cost_0(\theta^Tx^{(i)}))=-log(1-h_\theta(x^{(i)}))
$$



## Kernels

#### Gaussian Kernel

Given $$x$$, compute new feature depending on proximity to landmarks $$l^{(1)}, l^{(2)}, l^{(3)}$$
$$
f_i=similarity(x,l^{(i)})=exp(-\frac{||x-l^{(i)}||^2}{2\sigma^2})
$$
Predict $$y=1$$ if $$\theta_0+\theta_1f_1+\theta_2f_2+\theta_3f_3 \geq 0$$
Given $$(x^{(1)},y^{(1)}), (x^{(2)},y^{(2)}), ..., (x^{(m)},y^{(m)})$$
choose $$l^{(1)}=x^{(1)}, l^{(2)}=x^{(2)},..., l^{(m)}=x^{(m)}$$

Given example $$x$$:
$$
f_1=similiarity(x,l^{(1)})\\
f_2=similiarity(x,l^{(2)})
$$
If $$x \approx l^{(i)}$$
$$
f_i \approx exp(-\frac{0^2}{2\sigma^2}) \approx 1
$$
If $$x $$ if far from $$ l^{(i)}$$
$$
f_i = exp(-\frac{(large\ number)^2}{2\sigma^2}) \approx 0
$$

##### Hypothsis

##### 

$$
min_θ C [\sum_{i=1}^{m}y^{(i)}cost_1(θ^Tf^{(i)})+(1−y^{(i)})cost_0(θ^Tf^{(i)})]+\frac12\sum_{j=1}^mθ^2_j
$$

#### Parameters

$$
C=\frac{1}{\lambda}
$$

- Large C: Lower bias, high variance
- Small C: Higher bias, low variance

- Large $$\sigma^2$$: Features $$f_i$$ vary more smoothly. Higher bias, lower variance
- Small $$\sigma^2$$: Features $$f_i$$ vary less smoothly. Lower bias, higher variance

#### More esoteric Kernel

- SVM without kernel: (linear kernel)
- Polynomial kernel: $$(x^Tl+constant)^{degree}$$
- String kernel
- chiIsquare kernel
- histogram intersec2on kernel

### Experiment

n is number of features, m is number of training examples

- If n is large (relative to m): Use logistic regression or SVM without kernel (linear kernel) (n=10,000, m=10...1000)
- If n is small, m is intermediate: Use SVM with Gaussian kernel (n=1...1000, m=10...10,000)
- If n is small, m is large: Create/add more features, then use logistic regression or SVM without a kernel

Neural network likely to work well for most of these secngs, but may be  slower to train.





## K-means algorithm

$$
\begin{align*}
& Randomly\ initialize\ cluster\ centroids\\
& Repeat\ \{\\
& \ \ \ \ \ \ for = 1\ to\ m\\
& \ \ \ \ \ \ c^{(i)} := index\ (from\ 1\ to\ K)\ of\ cluster\ centroid\ closest\ to\ x^{(i)}\\
& \ \ \ \ \ \ for\ k = 1\ to\ K\\
& \ \ \ \ \ \ \mu_k:= average\ (mean)\ of\ points\ assigned\ to\ cluster\ k\\
& \}
& \end{align*}
$$

### Random Initialization

$$
\begin{align*}
& For\ i = 1\ to\ 100\ \{\\
& \ \ \ \ \ \ for = 1\ to\ m\\
& \ \ \ \ \ \ Randomly\ initialize\ K-means.\\
& \ \ \ \ \ \ Run\ K-means.\ Get. c^{(1)},..., c^{(m)}, \mu_1,..., \mu_K.\\
& \ \ \ \ \ \ Compute\ cost\ function\ (distortion)\ J(c^{(1)},..., c^{(m)}, \mu_1,..., \mu_K)\\
& \}\\
& Pick\ clustering\ that\ gave\ lowest\ cost\ J(c^{(1)},..., c^{(m)}, \mu_1,..., \mu_K)
& \end{align*}
$$

## 

## Principal Component Analysis (PCA)

Dimensionality Reduction

Data Compression

### Data preprocessing 

- Training set: $$x^{(1)}, x^{(2)},..., x^{(m)}$$ 
- Preprocessing (feature scaling/mean normalization): 

$$
\mu_j=\frac{1}{m}\sum_{i=1}^{m}x_j^{(i)}
$$



- Replace each $$x_j^{(i)}$$ with $$x_j-\mu_j$$.
- If different features on different scales (e.g., $$x_1$$= size of house, $$x_2$$= number of bedrooms), scale features to have comparable range of values 

$$
Sigma =\sum_{i=1}^{m}(x^{(i)})(x^{(i)})^T
$$



```octave
[U,S,V] = svd(Sigma);
Ureduce = U(:,1:k);
z = Ureduce’*x;
```

### Choose k

Average squared projection error:
$$
\frac{1}{m}\sum_{i=1}^{m}||x^{(i)}-x^{(i)}_{approx}||^2
$$
Total variation in the data:
$$
\frac{1}{m}\sum_{i=1}^{m}||x^{(i)}||^2
$$
Typically, choose k to be smallest value so that:
$$
\frac{\frac{1}{m}\sum_{i=1}^{m}||x^{(i)}-x^{(i)}_{approx}||^2}{\frac{1}{m}\sum_{i=1}^{m}||x^{(i)}||^2}\leq0.01\ (1\%)
$$

$$
1-\frac{\sum_{i=1}^{k}S_{ii}}{\sum_{i=1}^{m}S_{ii}}\leq0.01
$$

$$
\frac{\sum_{i=1}^{k}S_{ii}}{\sum_{i=1}^{m}S_{ii}}\geq0.99
$$

so 99% of variance is retained

### Application of PCA

- Compression
  - Reduce memory/disk needed to store data
  - Speed up learning algorithm
- Visualization: k = 2 or 3

### Bad use of PCA: To prevent overfiEng

Use $$z^{(i)}$$ instead of $$x^{(i)}$$ to reduce the number of features to $$k<n$$ (k about 1000, n about 10000)
Thus, fewer features, less likely to overfit. (Bad)

This might work OK, but isn’t a good way to address overfilng. Use regulariza1on instead.
$$
min_\theta\ \dfrac{1}{2m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2 + \frac{\lambda}{2m}\ \sum_{j=1}^n \theta_j^2
$$
Before using PCA.

How about doing the whole thing without using PCA?

Before implementing PCA, first try running whatever you want to do with the original/raw data $$x^{(i)}$$ . Only if that doesn’t do what you want, then implement PCA and consider using $$z^{(i)}​$$