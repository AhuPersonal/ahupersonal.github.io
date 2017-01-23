---
layout: post
title: "Titanic Event!"
excerpt: "What happened at Titanic event "
tags: [titanic, data science, data test]
comments: true
---

```{r}
#load library

library(tidyverse)
library(knitr)
library(grid)
library(cowplot)

```


# Lab 3

### Exercise 1: probabilities, independence 

0. Recall the definition of independence. If events $A$ and $B$ are independent then $P(A \cap B) =\;?$ Write this fact in a sentence, using the terms "joint probability" and "marginal probability": If $A$ and $B$ are independent then their ...

If A and B are independent then their joint probability will be the multiplication of their marginal probabilities.

$P(A \cap B) =\; P(A)P(B)$

1. Show that if $A$ and $B$ are independent then $P(A | B) = P(A)$. Write this fact in a sentence, using the terms "conditional probability" and "marginal probability". What is the parallel fact for the event B?

$P(A | B) = \frac{P(A \cap B)}{P(B)} = \frac{P(A)P(B)}{P(B)} = P(A)$

Conditional probability of A will not change depending on the outcome of B. Because B is independent event and therefore outcome of B does not affect the marginal probability of event A.

Same effect applies for the event B. Since they are independent variables, occurence of P(A) does not change P(B).


2. The probability that it rains in a given day in October in Vancouver is 60%. The probability of it being sunny on a given day in October in Vancouver is 45%. What is the probability that it rains AND is sunny (rainbow) tomorrow? You can assume that sun and rain are independent (probably a terrible assumption in reality).

Since these are independent variables, we just need to calculate their multiplication, because of the described facts above.

$P(rainbow) = P(sunny)P(rainy) = 0.6 * 0.45 = 0.27$

3. There are 2 ways you can end up with a Ferarri by tomorrow: either you win the lottery today (probability $p_L$) OR you receive one as a birthday present from Justin Trudeau (probability $p_J$). What is the probability you have a Ferrari by tomorrow? You can assume the lottery and the birthday present are independent. 

Hints: 

* The answer is not $p_L+p_J$
* What's the probability you don't have a Ferrari by tomorrow? 

Probability of I have a Ferrai by tomorrow can be calculated by the complement of probability that I don't have a Ferrari by tomorrow.

Probability of I don't have a Ferrari by tomorrow is calculated by the $P(p_L^c \cap p_J^c) = P(p_L^c)P(p_J^c)$

Therefore the answer is $1 - P(p_L^c \cap p_J^c) = 1- P(p_L^c)P(p_J^c) = 1 - ( (1- P(p_L)) (1- P(p_J)) )$


#### (optional) Exercise 1b

Consider the events $A$ and $B$ and their complements $A^C$, $B^C$. Show that it follows from the independence of $A$ and $B$ that $A^C$ and $B^C$ are also independent.

*Hints: Write down your target expression, i.e. the thing you need to show about $A^C$ and $B^C$, so you have a goal! Enumerate all possible outcomes in terms of joint events involving $A$, $B$, $A^C$, and $B^C$ and remember the Law of Total Probability. Use what you know about joint and conditional probabilities under independence.*

Target : $P(A^c\cap B^c) = P(A^c)P(B^c)$.

By DeMorgan's Law, $P(A^c\cap B^c) = P(A U B)^c$, so $P(A^c\cap B^c) = P((A U B)^c)$. By the law of compliments, this is equal to 

1 - $P(A^c\cap B^c) = P((A U B))$   
= 1 - $(P(A) + P(B) = P(A \cap B))$ (by definition of P(A U B)  
= 1 -$P(A) - P(B) + P(A)P(B)$ (because A and B are independent)   
= 1 - $(1 - P(A^c)) - (1 - P(B^c)) + ((1 - P(A^c))(1 - P(B^c))$ (law of compliments)   
= 1 - $1 + P(A^c) - 1 + P(B^c) + 1 - P(B^c) - P(A^c) + P(A^c)P(B^c)$  
= $P(A^c)P(B^c)$

### Exercise 2: Exploratory data analysis with the Titanic data. 

Use R to load the [Titanic data](https://github.ubc.ca/ubc-mds-2016/datasets). 

Remember the [ggplot2 tutorial](https://github.com/jennybc/ggplot2-tutorial) has examples and code major plot types.

*Hint: if any of your results/plots look funny check the variable type... should it be a factor? an integer?*

#### Exercise 2(a):  exploratory analysis between 2 categorical variables

Here, you will explore the relationship between survived / not survived and either gender or passenger class (your choice between these two). In other words, you will either compare survived vs. gender or survived vs. passenger class.


```{r}
#load titanic data
my_titanic <- read.csv("data/titanic.csv")

```


1. Make a frequency table for each of your two variables. 

```{r}

#make the variable survived be categorical
my_titanic$survived <- factor(my_titanic$survived)

#Freq and Rel_Freq for survival
my_titanic %>% 
  count(survived) %>% 
  mutate(rel_freq = prop.table(n))

  
#Freq and Rel_Freq for sex
my_titanic %>% 
  count(sex) %>% 
  mutate(rel_freq = prop.table(n))


```
Note: table() function would be easier but it changed the class of my data frame and caused a class problem when plotting in the following Qs. So this version is preferred.

2. Make companion bar charts for your frequency tables. Explore frequency of gender (or passenger class) among those who survived vs. those two did not survive. Explore frequency of survival among males vs females (or passenger class).


```{r}

# Explore frequency of sex among those who survived vs did not survive
ggplot(my_titanic, aes(x = survived)) + geom_bar(aes(fill = sex), position = "dodge")

# Explore frequency of survival among those among males and females
ggplot(my_titanic, aes(x = sex)) + geom_bar(aes(fill = survived), position = "dodge")


```

3. If these variables were independent, based on the observed frequencies, what would you expect the joint frequency table to be?


Under independency, I expect all the joint frequencies are the multiplication if the marjinal probabilities, like it is in the formula below:

$P(A \cap B) =\; P(A)P(B)$

I multiplied these findings with the total number of people (1309) below to find each joint frequency.
```{r}

# I will make a data frame including joint frequencies expected:

# Calculate joint frequencies expected under independency

F_NS <- as.integer(0.3559969*0.6180298*1309) #number of females not survived
M_NS <- as.integer(0.6440031*0.6180298*1309) #number of men survived
F_S <- as.integer(0.3559969*0.381971*1309) #number of females survived
M_S <- as.integer(0.6440031*0.381971*1309) #number of men survived

#make data frame
joint_freq_exp <- data.frame(
    survived = factor(c(0,1), levels=c(0,1)),
    sex = factor(c("female","female","male","male")),
    freq = c(F_NS, F_S, M_NS, M_S)
)

joint_freq_exp


```


4. Now, generate the actual joint frequency table. 

```{r}

# I generated the table in the same format of joint_freq_exp table

joint_freq_act <- my_titanic %>% 
  group_by(survived, sex) %>% 
  count
colnames(joint_freq_act)[3] <- "freq" 
joint_freq_act

```


5. Make a bar chart juxtaposing observed joint frequencies with those expected under independence.


```{r}
plot1 <- ggplot(joint_freq_act,
       aes(x = survived, y = freq, fill = sex)) +
  geom_bar(stat = "identity", position = "dodge", width = 0.7) + ggtitle("Actual Frequencies")

plot2  <- ggplot(joint_freq_exp,
       aes(x = survived, y = freq, fill = sex)) +
  geom_bar(stat = "identity", position = "dodge", width = 0.7)+ ggtitle("Expected Frequencies")


# Multiplot for juxtaposing
plot_grid(plot1, plot2)

```



6. Based on frequency tables and bar plots, discuss whether the two variables are independent.


They are not independent. Expected frequencies show that ratio of survival for each gender should be equal. However, actual frequencies show that, there are more female survivors, also the ratio of female survivors are more than expected ratio. I think this happened because we know from reality that females and children were allowed to leave the ship and take the rescuer boats first. Thus,being female(gender) affected the survival ratio in titanic.

#### Exercise 2(b):  exploratory analysis between one categorical and one continuous variable


Choose 1 categorical and one continuous variable to explore.

1. For each level of the categorical variable, compute a set of summary stats for the quantitative variable. Make sure to address location and spread. Present your results in a table.


```{r}

# I wanted to check both sex and survival( categoricals) against fare(quantitative)
my_titanic %>% 
  group_by(survived, sex) %>% 
  summarize(avg_fare = mean(fare, na.rm= TRUE), median_fare = median(fare, na.rm= TRUE), min_fare = min(fare, na.rm= TRUE), max_fare = max(fare, na.rm= TRUE), var_fare = var(fare, na.rm =TRUE))
```

2. For each level of your categorical variables, explore/visualize the distribution of the quantitative variable. Try using position, color, and facets to depict the different factor levels. Explore at least two types of plot (e.g. histogram, density plot, frequency polygon, box plot, violet plot). Where possible, include the actual data in the plot vs. just showing a summary.

```{r}

#Use geom- density to see the densities of different fares againts survived levels
ggplot(my_titanic, aes(x = fare)) + 
  geom_density() + 
  facet_wrap(~ survived)

ggplot(my_titanic, aes(x = fare, fill = survived)) + geom_histogram() +
  facet_grid(survived ~ .)
```


3. Describe what you see. Do you think these variables are independent?

These variables are not independent because average fare of survivors are higher than the average fare of not survivors. The dependency may not be so high because the more density takes place at the (0-100) interval of fare for both survived and not-survived but it is not exactly the same in both graphs.  Thus, survived categorical variable should be dependent to the fare continous variable in some way.

  
#### Exercise 2(c): exploratory analysis between two continuous variables

Pick 2 continuous variables to explore. 

1. Compute a set of summary stats for the quantitative variable. Make sure to address location and spread. Present your results in a table.

```{r}

#Select 2 quantitative variables
quant_vs <- my_titanic %>% 
  select(age, fare)

# create a data frame including the stats
result_table <- data.frame(
  
#find mean for both
c(lapply(quant_vs, mean, na.rm = TRUE)),

#find median for both
c(lapply(quant_vs, median, na.rm = TRUE)),

#find var for both
c(lapply(quant_vs, var, na.rm = TRUE)),

#find sd for both
c(lapply(quant_vs, sd, na.rm = TRUE)))

colnames(result_table)[1] <- "Mean_age"
colnames(result_table)[2] <- "Mean_fare"
colnames(result_table)[3] <- "median_age"
colnames(result_table)[4] <- "median_fare"
colnames(result_table)[5] <- "var_age"
colnames(result_table)[6] <- "var_fare"
colnames(result_table)[7] <- "sd_age"
colnames(result_table)[8] <- "sd_fare"

result_table %>% 
  kable()


#check their stats summary to glance
summary(quant_vs)
# check the quarters to glance
lapply(quant_vs, quantile, na.rm = TRUE)


```
2. Compute the linear correlation coefficient between the two variables using `cor()`. Provide a 1-sentence interpretation. 

```{r}
cor(my_titanic$age, my_titanic$fare, use = "complete")

my_titanic %>% 
  summarize(correlation = cor(age, fare, use = "complete"))
```

There is not a high correlation between age and fare, we cannot say that they are completely independent; however their dependency is approximately 0.18. 

3. Using `ggplot2`, make a scatterplot showing the data. Add a smooth with `geom_smooth()`.

```{r}
ggplot(my_titanic, aes(x = age, y = fare)) +
  geom_smooth()

```

4. Describe what you see. Do you think these variables are independent?

I think there is a weak dependecy between these variables. The line has a straightforward trend till age 50s. I can assume that the dependent part which is an upward trend in fare as age increases is because older passengers(between 50-70) might have bought more expensive tickets.

### (optional) Exercise 3: correlation vs. independence 

Having covariance of zero (or, equivalently, having linear correlation of zero) and being independent are two different things. 
Classify each of the following cases as either possible or impossible. If you classify as possible, give an example of the two distributions and then empirically show that the covariance is zero (or not zero) in your example by sampling from these distributions and using `cov` or `cor` in R. Note: in the cases where the the covariance is theoretically equal to zero, the sample covariance will not be exactly zero. In this case, do your "demonstration" by showing that the sample covariance approaches zero as the sample size grows large. If you classify as impossible, briefly explain why not.

We can answer this question in several ways, I will focus on a presentation with this picture. The image below (source Wikipedia) has a number of examples on the third row, in particular the first and the fourth example have a strong dependent relationship, but 0 correlation (and 0 covariance).



1. independent and zero covariance/correlation

It is possible

2. independent and nonzero covariance/correlation

It is possible.

3. not independent and zero covariance/correlation

It is impossible

4. not independent and nonzero covariance/correlation

It is possible
