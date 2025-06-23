# 8. KPIs and Experimentation

## 8-1. Decoding Business Success: Metrics, Growth, Strategies, and Collaborative Approaches

> Thinking like a product manager is critical to being a good data engineer.

As a modern-day data engineer, you need to go beyond just building and running pipelines. You need to understand how to build good metrics that change business decision making and are impacted by experiments.

### What are Metrics?
Metrics are the key performance indicators (KPIs) that help you understand the health and performance of your product or service. They are the foundation for data modeling and analysis.

For example, in an e-commerce application, important metrics might include revenue, conversion rate, average order value, and customer churn rate. These metrics directly inform the data model, which would likely include tables for orders, customers, and product sales.

### The Importance of Metrics on Data Engineering Work
Data modelling and data analysis are largely informed by metrics. Here are some examples of how:

* Defining Entities and Attributes: The key metrics you need to measure determine the entities (tables) and attributes (columns) that should be captured in your data model.
* Establishing Relationships: The connections between the metrics you're tracking inform the relationships between entities in your data model.
* Shaping Data Structure and Aggregation: The way you need to aggregate and report on your metrics influences the overall structure of your data model, including dimensionality and fact/dimension tables.

It's often overlooked but always remember that complex metrics does not necessarily mean complex data models. 
Focus on impact and not complexity.

### Why do metrics matter at all?
* Metrics are essential to experimentation
  * Experimentation, such as A/B testing, relies on metrics to measure the impact of changes or new features. Metrics are used to determine if a new feature or change has a positive, negative, or neutral effect.
  * For instance, if you're testing a new homepage design, you might measure metrics like bounce rate, time on site, and conversion rate to see how the new design performs compared to the control.

* Difference between features gates and experimentation
    * Feature flags allow you to gradually roll out new features to a subset of users, while experimentation involves actively testing different versions or treatments against a control group.
    * Feature flags are more about controlled deployment, while experimentation is about measuring the impact of changes.
    * For example, you might use a feature flag to slowly roll out a new payment method, while using A/B testing to experiment with different versions of the checkout flow.

* How do you plug metrics into experimentation framework
    * Metrics are the key outputs of any experimentation framework. They are used to determine the success or failure of an experiment, and to make data-driven decisions about product changes.
    * In an experimentation framework, you would first define the key metrics you want to measure, such as conversion rate, engagement, or revenue. You would then design experiments to test changes that could impact those metrics and use the results to inform future product decisions.
    * For instance, if you're testing a new recommendation algorithm, you might measure metrics like click-through rate, time spent on site, and revenue generated from recommended products. The results of the experiment would then guide whether you roll out the new algorithm to all users.

### Different Types of Metrics
1. **Aggregates and Counts**
    * These are fundamental metrics used in data modeling.
    * These include sums, averages, minimums, maximums, and various counting metrics.
    * Aggregates and counts form the foundation for many higher-level metrics and analyses.

2. **Ratios**
    * This is one of the most used metrics by data engineers.
    * Ratios allow for more meaningful comparisons and insights than raw counts or sums.
    * Data engineers typically provide the numerators and denominators, rather than the ratios themselves.
    * Examples include conversion rate, purchase rate, cost to acquire a customer

3. **Percentages** (p10, p50, p90 values)
    * Percentage metrics are useful for measuring experience at the extremes.
    * For example, p99 latency can indicate how fast a website loads for the worst 1% of users.
    * Tail metrics (e.g., the worst 1% experience) are often the most important to target and improve.
    * Data engineers can pass these aggregate percentile metrics to data scientists for further analysis and modeling.

By incorporating these types of metrics into the foundation of your data model, you can ensure that the data structure supports the key business needs and enables meaningful analysis and reporting.

### Metrics can be gamed and need balance
* Experiments can move metrics up short-term but down long-term
    * Short-term optimization efforts may have negative long-term consequences
    * Example: Increasing click-through rates with sensationalized headlines can boost engagement but damage brand reputation
* Make a hypothesis
    * Establish a clear hypothesis about the expected outcome before measuring the impact of a change
    * Ensures the metrics being tracked are relevant and aligned with business objectives
* Group assignment (test vs control)
    * Set up a controlled experiment with a test group and a control group
    * Allows you to isolate the effects of the change and compare the metrics between the two groups
* Collect data
    * Gather the relevant data points for the experiment
    * Ensure consistency in data collection and reporting across the test and *control groups
* Look at differences in groups
    * Analyze the differences in the key metrics between the test and control groups
    * Helps determine the actual impact of the change, rather than relying on potentially misleading trends in the overall data

By approaching metrics with this balanced and experimental mindset, you can avoid the pitfalls of short-term optimization and ensure that the data you're using to drive decision-making truly reflects the long-term health and performance of your business.

### Hypothesis testing
As data engineers, we build and optimize systems to support decision making. Hypothesis testing gives us a statistical way to prove the impact of our changes, moving beyond "I think it's better" to "the data shows it's better."

Here's the core idea:

1. The Null hypothesis ($H_0$): there is no difference
    * By default, we assume the change we made had no effect.
    * We then look for strong evidence to contradict this.

2. The Alternative hypothesis: there is a significant difference
    * This is what we are trying to demonstrate, our change made a measurable difference

3. You never prove the alternative hypothesis. You reject or fail to reject the null hypothesis
    * You gather data after your change
    * You calculate a p-value, this is the probability of seeing your data if the null hypothesis were actually true.
    * You set a significance level, $\alpha$ , this is your threshold for how unlikely something needs to be to convince you. This is your acceptable risk of a false-positive.

    Now you determine the result:    
    * If the p-value is less than $\alpha$, then you have strong evidence against $H_0$ so you can reject the null hypothesis. This supports your alternative hypothesis.
    * If the p-value is greater than $\alpha$, you don't have enough evidence against $H_0$, so you fail to reject the null hypothesis. This means your data doesn't statistically support the alternative hypothesis.

### Group Testing
When running experiments, particularly A/B tests or phased rollouts, the way we define and manage user groups is crutial for collecting valid results.

Who is eligible? How do we assign test/control groups?
* We start by defining the precise user population for our test (e.g. active users, or those from specific regions). There may be an exclusion list for specific groups, e.g. internal employees or known bots.
* It's important to use a consistent user ID (and hashing function) to assign users randomly to test or control groups. This is how we prevent users from flipping groups and ensures unbiased distribution.

Are these users in long-term holdout? (long term experiments e.g. based on region or having features disabled)
* A specific group of users intentionally excluded from any new experimental features for an extended period (weeks/months).
* We do this to
    * Detect latent negative effects: uncover long-term issues like user fatigue or churn that might not appear immediately
    * Measure cumulative impact: assess the total effect of multiple sequential changes on long-term KPIs

What percentage of users do we want to experiment on?
* Start small (safety first)
    * We begin with a small subset of users (1-5%) so we can mitigate downside risk by limiting the number of sever bugs or negative user experiences, protecting the majority of the user base. Doing this we can identify major issues early and with a minimal blast radius.
* Scale up (gain confidence):
    * If the small group test is stable and shows no major issues, we may want to gradually increase the subset percentage. We do this in a staggered way over months.
    * We need to scale since more users provides more data and this increases the statistical power of your test. This lets us detect smaller differences with higher confidence and reduces the change of missing a real effect.

* By using a phased rollout approach, if an experiment has major downside then we can bin it off. If, however, it has power on a small group then we can roll it out to more users.

### Collect data
Effective data collection is paramount for robust hypothesis testing and metric calculations. It's not just about gathering numbers but gathering the right information you need to enable sound statistical analysis.

Key principles for Data Collection:

1. Collect until you get a statistically significant result
    
    * Don't stop collecting data arbitrarily. Collect data until you have enough evidence to confidently reject the null hypothesis (if an effect exists) or until you determine that you're unlikely to reach significance within a reasonable timeframe (sometimes an effect can simply be too small to measure).
    
    * In general, the smaller the effect you're trying to detect, the longer you will need to collect data to reach statistical power. 

2. Make sure to use stable identifiers

    * Use persistent, non-changing identifiers (e.g. user_id, device_id) to ensure users remain in their assigned test/control groups throughout the experiment.

    * Minimize the amount of personally identifiable information (PII) you collect by using hashing for potentially sensitive data before storing or using them for analysis. This minimizes the collection of PII but still allows for unique identification within your system for bucketing or analysis.

    * Leverage experimentation tools like Statsig, Split.io, or Optimizely. These are designed to handle stable ID assignment and event logging, streamlining the data collection process.

3. Do not underpower your experiments:

    * The more test cells you have, the more data you need to collect. Each additional test cell dilutes your sample size per group and you need a larger overall dataset to maintain enough statistical power for meaningful comparison.

    * It's important to determine a Minimum Detectable Effect before starting (whenever possible). 

4. When logging, collect all relevant events and dimensions:

    * Log all events and relevant dimensions (e.g., device type, operating system, geographical region) that might influence or be influenced by the experiment. Champion comprehensive data capture!

    * Think about the granularity of your metrics. You'll want to not only measure the primary success metric but also analyze secondary effects, or slice data for deeper insights, e.g. did this feature work differently for mobile users?

### Look for statistical significance and the "so what?"
After collecting data, the next step is to determine if your observed results are a genuine result of your change or just random chance. This is where statistical significance comes in.

Understanding P-values:

* P-values are a statistical tool to determine if an observed outcome is really just a coincidence.

* The lower the P-value, the more certain you can be that the result is due to the underlying change, and not random.

The significance threshold, $\alpha$:

* P <0.05 is the industry standard (5% significance level). This means that there's less than a 5% chance your observed result happened by random chance and you'd typically reject the null hypothesis at this point.

* Depending on your situation, you might choose a different value of $\alpha$. For critical systems where false positive are very costly, you might choose $\alpha = 0.01$ but more early-stage exploratory tests where you're willing to take more risks, you may use a high value like $\alpha=0.1$.

Just because something is statistically significant doesn't mean it's useful:

* The tiny Delta. A result can be statistically significant (e.g., a pipeline runs 0.001% faster) but have a negligible real-world impact or return on investment. P-values are only meaningful with context, focus on effect size alongside P-values to truly understand the magnitude of the chang

* You may see conflicting results, with multiple statistically significant results for different metrics, some positive and some negative. This requires attentive analysis and trade-off decisions, rather than blindly launching a feature based on a single positive P-value. Prioritize core metrics and remember that context matters.

### Gotchas when looking for statistically significant results
1. Extreme outliers (line one user generating millions of events) can wildly skew averages
    *  To resolve this, you can consider using winsorization to cap the extreme values or focus on user counts/proportions or focus on user counts and proportions instead of raw event sums.
 
2. Don't stop an experiment early just because you hit significance
    * Don't fall victim to peeking. _Peeking_ refers to prematurely examining results of an ongoing test before it has reached statistical significance or completed the predetermined test duration.
    * This can have negative effects for the validity of your test by increasing the chances of observing false positives.

3. Don't forget to account for Seasonality and External Factors
    * Daily/weekly patterns or external events (holidays, outages, strikes) can mislead results if you don't run your test for long enough. 
    * Always run tests for full cycles (a week, a month, etc.) and monitor for external influences that could affect your data.


