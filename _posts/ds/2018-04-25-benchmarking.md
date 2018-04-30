---
layout: post
permalink: /ds/benchmarking/
title: Statistical approaches to benchmarking
abstract: 
level: easy
date: 2018-04-25
categories: data-science
comments: true
---

One of the big ideas at [Brandwatch][1] this year is _benchmarking_.
The basic premise of our business is that marketers, digital advertisers, PR agencies, etc. want to know what people are talking about on the internet, and to that end we allow them to collect scads of social media posts and aggregate them in various ways.
But people tend to make the statistics newbie mistake of taking these raw numbers at face value.

In reality, most of the world's numbers are meaningless unless they are evaluated against some sort of contextual backdrop.
Just as one should account for the cost of living before accepting that fancy job offer in New York or San Fransisco, it is important to benchmark business metrics against things like seasonal trends or a company's size and industry.
This line of thinking is intuitively persuasive, but it can be rather challenging to execute in practice.

In this post I am going to try to sort out how benchmarking should work for time series data.
I found it unexpectedly difficult to capture the statistical fundamentals of this problem, and the purpose of this post is to explain where I landed.

## The problem

Imagine you are a marketer, and last month you ran a campaign to drum up hype for your company's big product launch.
You are tasked with measuring the success of your campaign, and so you tally up the number of daily social media posts before and after the campaign:

Day -7 | Day -6 | Day -5 | Day -4 | Day -3 | Day -2 | Day -1 | Total
--- | --- | --- | --- | --- | --- | --- | ---
500 | 475 | 457 | 520 | 522 | 535 | 473 | 3482

<br>

Day 1 | Day 2 | Day 3 | Day 4 | Day 5 | Day 6 | Day 7 | Total
--- | --- | --- | --- | --- | --- | --- | ---
546 | 510 | 502 | 535 | 542 | 535 | 558 | 3728

So your brand was mentioned 246 more times in the week after the campaign than it was in the week before.
The question is: was this good?
How good?

Remembering that context matters, this is an insanely hard question to answer.
Nobody knows what the numbers would have looked like if your carefully crafted campaign never happened - maybe you saved what would have been a disastrous launch, or maybe your efforts actually held the event back from being even bigger.
Or maybe there was a big news event the week after your campaign that caused more people than usual to be on Twitter, and you had no effect whatsoever.

There are probably hundreds of confounding variables, and you can never hope to control for all of them.
That's just a fact of life in statistics.
The best you can do is try to find some sort of meaningful baseline against which to compare yourself, and to that end you look at the social media numbers for one of your competitor's recent product launches.
They look like this:

Day -7 | Day -6 | Day -5 | Day -4 | Day -3 | Day -2 | Day -1 | Total
--- | --- | --- | --- | --- | --- | --- | ---
10010 | 9905 | 9957 | 10082 | 10107 | 10014 | 9982 | 70057

<br>

Day 1 | Day 2 | Day 3 | Day 4 | Day 5 | Day 6 | Day 7 | Total
--- | --- | --- | --- | --- | --- | --- | ---
10152 | 10309 | 10262 | 10180 | 10057 | 10212 | 10267 | 71439

So your competitor received a total of 1382 more brand mentions in the week after their campaign compared to the week before.
What can you conclude about your campaign from this comparison?

On one hand, the news isn't good: their post-campaign bump was much larger than yours.
But on the other hand, they started from a much higher baseline: about 10000 mentions per day on average compared to your 500.
Perhaps the relative magnitude of your company compared to theres distorts the analysis: getting 100 new posts in a day would be a big deal for you, but it would be an ordinary fluctuation for them.

## Percent change

This line of thinking could just as well apply to a variety of other comparisons.
Perhaps you are comparing conversation volumes between two platforms, such as Reddit versus Twitter.
Or perhaps you have segmented your audience demographically, and you are 

In light of the above, we seek to normalize the number of mentions gained in the week after the campaign taking into account the baseline number of mentions in the week before.
The simplest way to do this is to look at the ratio of the number of mentions in the second week as compared to the number of mentions in the first:

$$\frac{3728}{3482} = 1.071$$

In other words, the amount of conversation grew by about $7.1\%$ in the week after the campaign.
On the other hand, the ratio for your competitors was:

$$\frac{71439}{70057} = 1.020$$

So your competitor only gained about $2\%$.
Success!
Your up-and-coming brand may still be small, but it is getting more out of your efforts than your competitor is out of their campaign manager.

Hold on: don't celebrate just yet.
A first sign that there might be something wrong is the behavior of percent changes at the lower extremes.
If your brand only averages one mention per day for a week and then it averages two mentions per day the following week then the percent change is $100\%$, but intuitively this overstates the significance of the accomplishment of improving your brand visibility by one mention.
At this extreme, the magnitude of the change in number of mentions feels like the more significant statistic to report.

Reflecting further on this example, it is tempting to conclude that we should simply use change in total volume as our measure of success until the conversation reaches a certain volume, at which point we should start using percent increases.
But I think the real lesson in this example is a bit more subtle.
Consider the following time series:

Day -7 | Day -6 | Day -5 | Day -4 | Day -3 | Day -2 | Day -1 | Total
--- | --- | --- | --- | --- | --- | --- | ---
500 | 499 | 500 | 501 | 500 | 499 | 501 | 3500

<br>

Day 1 | Day 2 | Day 3 | Day 4 | Day 5 | Day 6 | Day 7 | Total
--- | --- | --- | --- | --- | --- | --- | ---
520 | 520 | 521 | 519 | 519 | 521 | 520 | 3640

Compare this with the time series for your company.
The daily volumes are comparable - they tend to fall in the high 400's or low 500's - and the weekly volume ratio is:

$$\frac{3640}{3500} = 1.04$$

So the conversation grew by only $4\%$ compared to your $7.1\%$.
You did better then, right?

This is not at all obvious to me.
The numbers in your company's data were all over the place: they ranged from 457 to 535 in the first week and from 502 to 558 in the second week.
The numbers in the second week are higher overall to be sure, but the wild variability means it is harder to trust that your $7.1\%$ increase was due to a real difference in the time series and not random chance.
In the time series above, however, the values never stray further than 1 away from the mean; the percent increase may be smaller, but our certainty that it was due to a real change in the data is much higher.

I would argue that this is the real explanation for why we aren't happy with reporting a $100\%$ increase when we go from one mention per day to two.
It's not that the _volumes_ are small, it's that the _variability_ is small: if the average of a few non-negative numbers is 1 then they couldn't have strayed too far from 1, and so increasing to an average of 2 is well within the realm of ordinary variation.

In turn, this suggests that percent increases might not actually be the right metric.
We introduced percent increases because we felt that it is easier to go from about 10000 mentions per day to about 10200 per day than it is to go from about 500 mentions per day to about 530 mentions per day.
But if by "easier" we mean "more likely to happen by random chance" then surely we should consult the data rather than jumping to conclusions!
Reporting percent increases implicitly assumes that the standard deviation of the daily mention volume numbers is proportional to the mean, and this is risky without a strong empirical foundation.

## Hypothesis testing with the variability

It's also an unnecessary assumption.
After all we have access to the time series itself, and this is enough information to at least estimate the standard deviation of the daily volumes.
The question becomes: how can we use the standard deviation to measure the significance of the change in total volume from one week to the next?

This question puts us firmly within the realm of statistical hypothesis testing, and it can be answered using well-known t-tests.
The upshot is that instead of reporting percent increases we should report the total volume increase and contextualize it with a confidence score.
The story becomes, "Daily mention volumes increased modestly, but we are very confident that it was due to our efforts rather than normal variation in the data."
And if you are just as confident that your competitor's campaign caused an increase in volume then you can make an "apples to apples" comparison between your volume change and theirs with no additional normalization.

So without further ado, let us describe the appropriate hypothesis testing methodology for this problem.





[1]: https://www.brandwatch.com/ "Brandwatch"
