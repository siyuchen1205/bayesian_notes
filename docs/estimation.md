
# Estimation

## Point Estimates

Bayesian point estimators use the following recipe:

1.  define a loss function that penalizes guesses
1.  take the expected value of that loss function over the parameter of interest

Let $\theta$ be a parameter with a prior distribution $\pi$.
Let $L(\theta, \hat{\theta})$ be a loss function. Examples of loss-functions include

-   **squared error**: $(\theta - \hat{\theta})^2$
-   **absolute error**: $|\theta - \hat{\theta}|$

Let $\hat{\theta}(x)$ be an estimator.
The **Bayes risk** of $\hat{\theta}$ is the expected value of the loss function over the probability distribution of $\theta$.
$$
\E_{\pi}(L(\theta, \hat{\theta})) = \int L(\theta, \hat{\theta}) \pi(\theta) d\,\theta
$$

An estimator is a **Bayes estimator** if it minimizes the the Bayes risk over all estimators.

<!--lint disable table-cell-padding table-pipe-alignment  -->

| Estimator       |  Loss Function                                                                                                                                                                           |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mean            |  $(\theta - \hat{\theta})^2$                                                                                                                                                             |
| Median          |  $|\theta - \hat{\theta}|$                                                                                                                                                               |
| $p$-Quantile    |  $\begin{cases} p | \theta - \hat{\theta} | & \text{for } \theta - \hat{\theta} \geq 0 \\ (1 - p) |\theta - \hat{\theta} | & \text{for } \theta - \hat{\theta} < 0  \end{cases}$  |
| Mode            |  $\begin{cases} 0 & \text{for } | \theta = \hat{\theta} | < \epsilon \\ 1 | & \text{for } |\theta - \hat{\theta}| > \epsilon \end{cases}$                                                            |
\end{cases}$

<!--lint enable table-cell-padding table-pipe-alignment  -->

The posterior mode can often be directly estimated by maximizing the posterior distribution,
$$
\hat{\theta}_{\text{mode}} = \arg \max_{\theta} \int \theta(\theta | y) .
$$
This is called **maximum a posteriori** (MAP) estimation.

Given a estimation could be used by including that loss function into that function,
$$
\hat\theta = \arg \min_{\theta^*} \int L(theta, \theta^*) p(\theta) \,d\theta .
$$
However, since that still requires integrating over the distribution of $\theta$.
In cases where the form of $p(\theta)$ is known, this may have a closed form.
In the cases of most posterior distributions, this would require some sort of approximation of the distribution of $p(\theta)$.

$$
p(\theta | )
$$

## Credible Intervals

<!-- TODO: I need better notation here -->

A $p \times 100$ credible interval of a parameter $\theta$ with distribution $f(\theta)$ is an interval $(a, b)$ such that,
$$
CI(\theta | p) = (a, b) s.t. \int_{a}^{b} f(\theta) \,d\theta = p.
$$

The credible interval is not uniquely defined.
There may be multiple intervals that satisfy the definition of a credible interval.
The most common are the

-   **equal-tailed interval**: $(F^{-1}(p / 2), F^{-1}((1 - p) / 2))$ where $F^{-1}$ is the quantile function of $\theta$.
    The 95% credible interval would use the 2.5% and 97.5% quantiles.

-   **highest posterior density interval**: The shortest credible interval.
    If the distribution is not unimodal and symmetric, the HPD interval is not the same as the equal-tailed interval.

Generally it is fine to use the equal-tailed interval. This is what Stan reports by default.

The HPD

-   is harder to calculate.

-   may not be a convex interval if it is a multimodal distribution. (Though that may be a desirable feature.)

-   unlike the central interval, not invariant under transformation. For some function $g$,
    if $CI_{HPD}(\theta) = (a, b)$, then generally $CI(g(\theta)) \neq (g(a), g(b))$.
    However, for the equal-tailed interval, $CI(g(\theta)) = (g(a), g(b))$.

How to calculate?

For the equal-tailed credible interval:

-   If the quantile function is known, use that.
-   Otherwise, calculate the quantiles of the sample.

For example, the 95% credible interval for a standard normal distribution is,

```r
p <- 0.95
qnorm(c( (1 - p) / 2, 1 - (1 - p) / 2))
#> [1] -1.96  1.96
```
The 95% credible interval for a sample drawn from a normal distribution is,

```r
quantile(rnorm(100), prob = c( (1 - p) / 2, 1 - (1 - p) / 2))
#>  2.5% 97.5% 
#> -1.78  1.79
```

There are multiple functions in multiple packages that calculate the HPD interval.
`coda::HPDitnterval`.

### Compared to confidence intervals

TODO

## Bayesian Decision Theory

One aspect of Bayesian inference is that it separates *inference* from *decision*.

1.  Estimate a posterior distribution $p(\theta | y)$
1.  Define a loss function for an action ($a$) and parameter ($\theta$), $L(a, \theta)$.
1.  Choose that action that minimizes the loss function

In this framework, inference is a subset of decisions and **estimators** are a subset of decision rules.
Choosing an estimate is an action that aims the minimize the loss of function of guessing a parameter value.

Given $theta$ and its distribution $p(\theta)$ and a loss function $L(a, \theta)$,
the optimal action $a^*$ from a set of actions $\mathcal{A}$ is
$$
a^* \arg \min_{a \in \mathcal{A}} \int p(\theta) L(a, \theta) \,d \theta .
$$
If we only have a sample of size $S$ from $p(\theta)$ (as in a posterior distribution estimated by MCMC), the optimal decision would be calculated as:
$$
a^* \arg \min_{a \in \mathcal{A}} \sum_{s = 1}^S L(a, \theta_s) .
$$

The introductions of the [Talking Machines](https://www.thetalkingmachines.com/episodes/data-science-africa-dina-machuve) episodes [The Church of Bayes and Collecting Data](https://www.thetalkingmachines.com/episodes/church-bayes-and-collecting-data)
and have a concise discussion by Neil Lawrence on the pros and cons of Bayesian decision making.
