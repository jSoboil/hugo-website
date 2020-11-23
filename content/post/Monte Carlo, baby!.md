---
title: "Monte Carlo, baby!"
---
So, I am _finally_ gettin' round to providing some examples between Monte Carlo (MC) Probabilistic Sensitivity Analysis (PSA) and Markov Chain Monte Carlo (MCMC) PSA. I'm still finishing up the last section of my dissertation, but it's getting there and I'm really happy with it.

Anyway, let's get back to the point. There are several ways to go about an MC PSA simulation, but I think a relatively straightforward approach, that is also described in most of the main health economics textbooks out there, is to use the inverse sampling method. The what? Well, personally I find many health economic textbooks extremely vague, especially relating to the methodology part. I think generally it is intended to help the uninitiated in simulation based statistical methods, but I find this actually just muddles the waters. But, yeah, that's just like my [opinion](https://www.youtube.com/watch?v=pWdd6_ZxX8c), man.

The inverse sampling method basically takes the inverse of the cumulative density function in order to calculate the probability of a random variable falling within a particular range. Yes, yes, to the knowledgeable this does *not* apply for a probability mass function - if you have a discrete variable you can sample the probability of a random variable directly from the probability mass function. This is an important difference. Random samples are drawn from uniform distribution, i.e.

$$x_{i}\sim  U(0, 1)$$

It is then necessary to transform these samples into the inverse cdf (also known as a quantile function) of the exponential distribution and normal distribution. Im not going to go into the exact mathematical details, as it is hard to immediately grasp and, to be honest, it would be shlep to write out fully. I'll leave it for another time when I have more time on my hands! But, once the initial uniform samples have been transformed into the desired inverse cdf, this can then be used to inform the probability for a range of parameter values occurring. 

Here's some code for you to try out in R on your own:
```r
x <- runif(n = 20000)

ggplot() + 
 geom_histogram(aes(x), binwidth = 0.05, colour = "black", fill = "white", alpha = 0.95) + 
 ylab(label = "Sample frequency") +
 xlab(label = expression(paste(italic(Uniform), " ~ " (0, 1)))) +
 theme_linedraw(base_family = "Times New Roman", base_size = 12)

inv_cdf_exp <- qexp(p = x, rate = 0.4)
inv_cdf_norm <- qnorm(p = x, mean = 0.65, sd = sqrt(0.7744))

x_cum <- ecdf(x = x)

ggplot() + 
 geom_density(aes(x = inv_cdf_norm), fill = "skyblue", alpha = 0.65) +
 stat_ecdf(aes(inv_cdf_norm), colour = "skyblue", alpha = 0.85, lwd = 0.95) + 
 geom_density(aes(x = inv_cdf_exp), fill = "darkred", alpha = 0.45) + 
 stat_ecdf(aes(inv_cdf_exp), colour = "red", alpha = 0.85, lwd = 0.95) +
 annotate("segment", x = 5, xend = 3.65, y = 0.5, yend = 0.77, colour = "black", 
          size = .4, alpha = 0.75, arrow = arrow()) + 
 annotate("segment", x = 5, xend = 1.6, y = 0.5, yend = 0.85, colour = "black", 
          size = .4, alpha = 0.75, arrow = arrow()) +
 annotate("text", x = 5.5, y = .43, label = "CDFs of the exponential \n and normal distributions", 
          size = 4.25, family = "Times New Roman") +
 ylab(label = "Probability Density") +
 xlab(label = "Parameter values") +
 theme_linedraw(base_family = "Times New Roman", base_size = 12) + 
 xlim(c(-1.95, 11)) + 
 ylim(c(0, 1))
```

So, all in all, it's quite a cool way of obtaining probabilistic results for a decision model's parameters. Nevertheless, there are several notable shortcomings, one of which is that this assumes independence between parameters. This is pretty much *never* true and that is why it is necessary to induce correlation via decomposition methods, linear regression (if there is sufficient data) or by more complicated simulation methods such as Markov Chain Monte Carlo.

But, like always, that's for another time.

Sincerely
J.S.
