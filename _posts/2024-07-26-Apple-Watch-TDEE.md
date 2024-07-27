---
title: How accurate is the Apple Watch's Energy Expenditure?
date: 2024-07-26 00:00:00
description: When tracking calories, it's important to know your maintenance calorie level. In this post, we explore whether the Apple Watch is accurate enough to set that baseline.
featured_image: /assets/img/pages/photo-15.jpg
author: Aladdin Persson
---

When losing weight, the most accurate and reliable method is tracking calories. Ideally, you want to aim for a 0.5-1% body weight loss per week. For this, you need to have some idea of where your maintenance calories are. You can use a calculator such as the [Mifflin-St Jeor](https://www.freedieting.com/calorie-calculator), which generally provides a good baseline. Then, you'd titrate and adjust your calorie consumption based on the actual weight loss you see on the scale to achieve that 0.5-1% body weight loss per week.

However, although what I described above is a quite concrete guide to lose weight, your activity levels and calorie consumption will vary depending on the day. This means some days you'll lose more weight than others, and over the course of cutting weight, you might reduce daily activity, thus reducing your calorie consumption. This is something the watch could help with tracking, and it could also replace the Mifflin-St Jeor calculator for TDEE (Total Daily Energy Expenditure) in setting that baseline. The question we want to answer is: Is it accurate, and can we use it as a baseline?

For the past 30+ days, I've weighed myself daily, worn the watch to track energy expenditure, and tracked all calories I've consumed. Let's start by looking at my energy expenditure graph:

[![TDEE Chart](../assets/img/blog/apple-watch-tdee/tdee_chart.png)](../assets/img/blog/apple-watch-tdee/tdee_chart.png "Click to view full size")

Here we can see, for example, that energy expenditure started increasing quite a bit since April of 2024, which aligns well with when I started exercising more frequently. We can also see that the variation is quite large day to day, where the resting energy in blue stays quite static, but the active energy has the most fluctuations. If we look a bit closer at the data:

| Category     | TDEE (calories) | Active Energy (calories) | Resting Energy (calories) |
|--------------|-----------------|--------------------------|---------------------------|
| Averages     | 2963            | 720                      | 2243                      |
| Workout day  | 3320            | 1082                     | 2238                      |
| Recovery day | 2584            | 336                      | 2247                      |
{: .table .table-responsive}

We notice that, in fact, the total average during that weight cut period was about 2963 calories in expenditure, but the difference between a workout day and a non-workout day (strength training and usually some cardio, 4x a week) is quite large. If compliance to maintaining a calorie deficit would be a challenge, I think based on this (assuming the energy expenditure estimates are accurate, which we'll get to in a moment), we would want to vary the calorie consumption based on workout and non-workout days. I believe the impact on muscle mass loss during a weight cut is most likely quite minimal, but I can attest that maintaining the calorie deficit is (slightly) more difficult on a hard workout day than a recovery day, which makes sense given the difference in expenditure shown above.

The main question now: Are these estimates even accurate? There have been a few studies looking at this with varying results, i.e in some cases it overestimates and others underestimates.

- [Accuracy in Wrist-Worn, Sensor-Based Measurements of Heart Rate and Energy Expenditure in a Diverse Cohort](https://www.mdpi.com/2075-4426/7/2/3)
- [Accuracy of Apple Watch Measurements for Heart Rate and Energy Expenditure in Patients With Cardiovascular Disease: Cross-Sectional Study](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6444219/)
- [Accuracy of Heart Rate Watches: Implications for Weight Management](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4883747/) 

The challenge here is also that I imagine Apple is tweaking and updating the algorithms for making the calculations more accurate over time, as well as that it might vary depending on the watch model. I can imagine that depending on various factors such as having inaccurate personal data (weight, height, age), or choosing the incorrect workout type, the calorie consumption would become very different for the same workout. So in general, we know it's not going to be *very* accurate based on the studies. However, we also need to keep in mind that as long as it's in the correct ballpark, for a weight cut we are going to be in a ~500 calorie or so deficit (depending on your body weight to keep to the 0.5%-1% BW loss per week), so if it were 100 or 200 calories inaccurate, that would actually still be okay to use as a baseline. If we look at my actual weight compared to the estimated weight loss I should have based on my energy expenditure, the graph looks as follows:

[![TDEE Chart](../assets/img/blog/apple-watch-tdee/actual_vs_estimated_weight.png)](../assets/img/blog/apple-watch-tdee/actual_vs_estimated_weight.png "Click to view full size")

We can see that the actual weight varies, which is common as water weight fluctuates, mostly based on the amount of carbohydrates consumed. Here we can also see that depending on which day during the cut, the Apple Watch overestimates and underestimates, and it's not clear that it's consistently overestimating or underestimating. I set my calorie consumption to be 2300 calories per day, but the weight fluctuations causing error are well within the error I could be making in measurement (a few of the days I've eaten out for lunch and tried to estimate). Based on the studies, reading people's experiences ([Andrej Karpathy's biohacking post](https://karpathy.github.io/2020/06/11/biohacking-lite/)), and comparing to more "old school" calculators such as Mifflin-St Jeor, I would lean towards the watch overestimating rather than underestimating. But again, if we are assuming a deficit of about ~500+ calories per day, I think using the watch as a baseline is sufficiently good.