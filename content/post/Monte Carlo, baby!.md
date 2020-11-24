---
title: "Monte Carlo, baby!"
---
So, I am _finally_ gettin' round to providing some examples between Monte Carlo (MC) Probabilistic Sensitivity Analysis (PSA) and Markov Chain Monte Carlo (MCMC) PSA. I'm still finishing up the last section of my dissertation, but it's getting there and I'm really happy with it.

Anyway, let's get back to more important things. There are several ways to go about an MC PSA simulation, but I think a relatively straightforward approach, that is also described in most of the main health economics textbooks out there, is to use the inverse sampling method. The what? Yeah, this isn't the name usually applied in the main health economic textbooks, sadly, You'll have to read some probability books. Honestly, Blitzstein and Hwang's [Introduction to Probability](https://www.amazon.com/Introduction-Probability-Chapman-Statistical-Science/dp/1138369918) has been a life-saver for me. I'd recommend checking it out for those interested.

Personally, I find many health economic textbooks extremely vague in exact methodology, especially regarding to simulation methods. I think it's likely intended to help the uninitiated in  gaining an intuitive understanding of simulation based statistical methods, but I find this has actually just muddled the water for me. Yeah, but that's also just like my [opinion](https://www.youtube.com/watch?v=pWdd6_ZxX8c) though, man.

Back to the point - the inverse sampling method basically takes the inverse of the cumulative density function in order to calculate the probability of a random variable falling within a particular range. There is an important distinction between continuous and discrete variables in the way you go about this. Basically, in the discrete case because you can sample directly from the probability mass function (pmf), one has to specify the upper and lower bounds of th uniform distribution so that all of your indicator parameter values sum to one. For example for a discrete binary variable, you can just say, perhaps, something like

$$x_{0}\sim  U(0, 0.5)$$

and

$$x_{1}\sim  U(0.5, 1)$$

However, this means that the distribution for a discrete random variables is not uniform as it can only take on a finite set of specific values. For the continuous case, random samples must also be drawn from a uniform distribution, though more generally bounded as

$$x_{i}\sim  U(0, 1)$$

Then it is necessary to transform these samples into the inverse of the cdf (also known as a quantile function). I'm going to avoid going into exact mathematical details, as, to be real honest, it would be a schlep to write this out fully. I'll leave it for another time when I have more time on my hands! 

Once the initial uniform samples have been transformed into the desired inverse cdf, this can then be used to inform the probability for a range of parameter values occurring. I'll leave you with some R code to try out on your own:

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
