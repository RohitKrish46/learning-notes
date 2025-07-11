# Data Drift in ML

When you deploy a machine learning model in production, it faces real-world data. As the environment changes, this data might differ from what the model has seen during training. As a result, the so-called data drift can make the model less accurate.

## **What is data drift?**

- **Data drift** is a change in the statistical properties and characteristics of the input data. It occurs when a machine learning model is in production, as the data it encounters deviates from the data the model was initially trained on or earlier production data.
- Detecting and addressing data drift is vital to maintaining ML model reliability in dynamic settings.

Example

- Imagine a retail chain that uses machine learning to predict how many products of a particular type they need to stock in each of their stores. They trained their model using historical sales data from the past few years.
- Until now, most of their sales have been in physical stores, and their model has become quite good at forecasting demand for in-store products. However, as the retailer ran a marketing campaign to promote their new mobile app, there's been a significant shift towards online sales, especially for some product categories.
- The training data didn't have enough online sales information, so the model didn't perform as well for this segment. But it didn't matter much because online sales were a small part of their business. With the surge in online shopping, the quality of the model's forecasts has significantly dropped, affecting their ability to manage inventory effectively. This shift in sales channels, from predominantly in-store to largely online, is an example of data drift.
    
    ### **Data drift vs. Concept drift**
    
    <aside>    
    **TL;DR**. Data drift is a change in the input data. Concept drift is a change in input-output relationships. Both often happen simultaneously. 
    </aside>
    
    - While data drift describes changes in the data distribution, [**concept drift**](https://www.evidentlyai.com/ml-in-production/concept-drift) relates to changes in the relationships between input and target variables. Basically, concept drift means that whatever your model is predicting – it is changing.
    - **Data drift** might be a symptom of concept drift, and both often co-occur. However, it is not a must.
    
    Example:
    
    An example of **concept drift** could be a new competitor offering massive discounts on the same product groups your retail store sells. This could shift shopper behavior, resulting in a drastic decrease in the average basket size in offline stores. This could, in turn, lead to inaccurate forecasts. Another instance could be the onset of COVID-19, which transformed how people shopped and disrupted logistical patterns. In these cases, all previously created models became almost obsolete.
    
    - **The difference:** Data drift refers to the shifts in input feature distributions, whereas concept drift refers to shifts in the relationships between model inputs and outputs.
    - **The similarity:** Both data drift and concept drift can result in a decline in model quality and often coincide. In monitoring, data distribution drift can be a symptom of concept drift.
    
    ### **Data drift vs. Prediction drift**
    
    <aside>
    **TL;DR.** Data drift is a change in model inputs, while prediction drift is a change in the model outputs.
    </aside>
    
    - When discussing data drift, we typically refer to the input features that go into the model. Prediction drift, in comparison, is the distribution shift in the model outputs.
    - The shift in model outputs can signal changes in the environment or issues with the model quality. Often, this is the best proxy if you cannot directly measure the model performance.
    - Imagine that a fraud model starts to predict fraud more often. Or, a pricing model is now showing significantly lower prices. The change in the model predictions is a good reason to investigate.
    - **Prediction drift** can signal many issues, from low-quality data inputs to concept drift in the modeled process.
    - At the same time, prediction drift does not always imply model deterioration. It can also occur if the model adjusts well to the new environment. For example, if there is an actual increase in fraud attempts, you can expect that the distribution of the predicted fraud cases will look different. In this case, you could observe both feature and prediction drift without a decay in the model quality.
    - **The difference**: data drift refers to the changes in the model input data, while prediction drift refers to the changes in the model outputs.
    - **The similarity**: both data and prediction drift are useful techniques for production model monitoring in the absence of ground truth and can signal the change in the model environment.
    
    ### **Data drift vs. Training-serving skew**
    
    <aside>
    **TL;DR.** Training-serving skew is a mismatch between training and production data. Data drift is a shift in the distribution of production data inputs over time.
    </aside>
    
    - **Training-serving skew** is a situation where there's a mismatch between the data the model was trained on and the data it encounters in production.
    - While environmental changes can contribute to this skew, it includes all possible discrepancies between the two datasets, including issues related to data preprocessing, feature engineering, and more. In addition, while data drift is usually a more gradual process you encounter during model operations, the training-serving skew refers to the immediate post-deployment window.
    - For example, you can encounter a training-serving skew when the features available in training are not possible to compute in production or come with a delay. The model won't be able to perform as well if it lacks important attributes it was trained to consider.
    - **The difference:** data drift refers to the gradual change in the input data distributions. Training-serving skew refers to the mismatch visible shortly after the start of the model production use and can include issues unrelated to the changes in the environment.
    - **The similarity:** in both cases, we refer to the changes in the input data. You might use similar distribution comparison techniques to detect input data drift and training-serving skew by contrasting production data with training.
    
    ### **Data drift vs. Data quality**
    
    <aside>
    **TL;DR.** Data drift refers to the change in data distributions in otherwise valid data. Data quality issues refer to the data bugs, inconsistencies, and errors.
    </aside>
    
    - Broadly speaking, data drift can include all sorts of changes and anomalies in data.
    - **Data quality** issues refer to corrupted and incomplete data that might occur, for example, due to pipeline bugs or data entry errors.
    - **Data drift** refers to the change in distributions in otherwise correct and valid data that might occur due to environmental shifts.
    - When you detect data distribution shifts, you can often attribute it to data quality issues. For example, if there is an accidental change in the feature scale due to entry error, you will notice a statistical shift in the distribution.
    - Because of this, it helps to divide the two groups of checks.  First, you verify the data quality, such as whether the data is complete, relevant features remain non-negative, etc. Then, you apply data distribution checks to see if there is a statistical shift in the feature pattern. Otherwise, whenever drift is detected, you'd need first to discard data quality issues as a possible root cause.
    - **The difference:** Data quality concerns issues like missing values or errors in the data. Data drift refers to statistical changes in the data distribution, even if the data has high quality. Data quality issues can lead to observed data drift, but they are not the same thing.
    - **The similarity**: Both data quality issues and data drift can lead to model quality drops, and both refer to the changes in the data. Data drift detection techniques can often expose data quality issues
    
    ### **Data drift vs. Outlier detection**
    
    <aside>
    **TL;DR.** Data drift refers to the change in the overall data distributions. Outlier detection is focused on identifying individual anomalies in the input data.
    </aside>
    
    - **Drift detection** refers to the "global" data distributions in the whole dataset. You want to detect whether the data has shifted significantly compared to the past period or model training. The goal is to decide if you can trust the model still performs as expected and if the environment remains similar.
    - **Outlier detection** serves a different purpose. You want to identify individual objects in the data that look different from others. Often, the goal is then to act on the level of the respective objects. For example, you can ask a human expert to make a decision instead of the ML model or apply some business logic for this particular output, such as denying to make a prediction. This often happens when the cost of an individual error is high.
    - Data drift and outliers can exist independently. For example, you can observe dataset drift without outliers or individual outliers without data drift. You’d typically design detection methods differently: drift detectors should be robust to some outliers, while outlier detectors should be sensitive enough to catch individual anomalies.
    - **The difference:** Data drift refers to changes in the overall data distribution, while outlier detection focuses on identifying individual unusual inputs in the data. Drift detection helps evaluate overall model reliability, while outlier detection helps discover inputs the model might be ill-equipped to handle.
    - **The similarity**: Both checks help monitor and understand the changes in input data. You can track the share of outliers in the data as a reflection of the upcoming data drift.

## **Why is data drift important?**

Data drift is an important concept in production machine learning for a few reasons.

1. First, conceptually understanding that distribution drift can – and will – happen helps prepare to maintain the production ML model quality through model monitoring and retraining.
2. Second, tracking data distribution drift can be a technique to monitor the model quality in production when ground truth or true labels are unavailable.
3. Lastly, data drift analysis can help interpret and debug model quality drops and understand changes in the model environment.

### **Model maintenance**

<aside>

**TL;DR.** Machine learning models are not "set it and forget it" solutions. Data will shift with time, which requires a model monitoring and retraining process. 
</aside>

- You typically train ML models on specific datasets, expecting they'll perform well on unseen, real-world data. However, assuming that the data will remain static is often unrealistic.
- Even if there are no drastic changes and events like significant marketing campaigns or COVID-19, you can expect minor variations to accumulate over time. For example, in sales demand forecasting for hundreds or thousands of different items, you can always expect new products to appear and customer preferences and market conditions to evolve.
- As a result, complex real-world data will always deviate from the training data used sooner or later. Data (and [concept](https://www.evidentlyai.com/ml-in-production/concept-drift)) drift are in-built properties of a machine learning system – which explains the need for ongoing model maintenance.
- Typically, you can combat this by regularly **retraining the models** on the new data to help the model stay up to date. This means you need to design a model update process and retraining schedule.
- Additionally, you need a robust **[model monitoring](https://www.evidentlyai.com/ml-in-production/model-monitoring) setup** to provide visibility into the current model quality and ensure you can intervene in time. This helps detect model quality issues in between updates or design the retraining process to happen on a trigger.

### **Feedback delay**

<aside>

**TL;DR.** Feedback delay is a time lag between model predictions and receiving feedback on those predictions. Monitoring input data distribution drift is a valuable proxy when ground truth labels are unavailable. 
</aside>

- Feedback delay can occur when there is a significant time gap between the model making a prediction and receiving feedback on the accuracy or correctness of that prediction.
- For example, in a recommender system, there might be a delay between when the system recommends an item to a user and when we can determine if the user liked or interacted with that recommendation. This delay can vary from seconds (for some online interactions where users can immediately click or accept a recommended offer) to minutes, hours, or even days, depending on the nature of the task.
- In tasks like payment fraud detection, it may be difficult to definitively label a user transaction as fraudulent or legitimate until you perform further investigation or get user feedback. Sometimes, ground truth labels may only become available weeks or months later, such as when a customer disputes a fraudulent transaction.
- Having ground truth labels (Did the user buy a recommended product? Was it indeed a fraudulent transaction?) is crucial for evaluating the model quality in production. It is hard to make real-time decisions on the model quality otherwise. At the same time, say, a problematic fraud detection model may cause significant harm before issues are identified and resolved.
- In these scenarios, you can set up early **monitoring using proxy metrics**. Monitoring input data distribution drift is one such method: you can apply different techniques to compare the distribution of the incoming data to previous batches. This helps detect significant environmental shifts that might affect the model performance before you measure it directly.
- You can use various **data distribution comparison** techniques to evaluate changes in both model input features and model outputs. We’ll briefly cover different drift detection methods in the following section.

### Model Debugging

<aside>

**TL;DR.** Analyzing input data distribution drift helps explain and locate the reasons for model quality drops, as well as notice important changes in the modeled process.
</aside>

- Evaluating data drift is also a useful technique for model troubleshooting and debugging. If you observe a model quality drop through a direct metric like accuracy, your next step is investigating the underlying cause. This usually boils down to looking for changes in the input data.
- Data drift analysis helps understand and contextualize the changes in the model features and locate the source. In this scenario, you might **not** use drift detection as an alerting signal – however, you can employ the data drift analysis when debugging.
- A simple approach is to run checks for per-feature distribution comparison and identify which features have shifted most significantly. Then, you can visually explore their distributions to interpret the change using your domain knowledge. Are users coming from a new source? Is there a new product group absent in the training data?
- For example, you might notice a shift in a particular categorical feature that helps identify a new emerging user segment.

## **How to detect data drift**

Comparing the distributions of the input features and model output helps with early monitoring and debugging ML model decay. 

### **Summary statistics**

- A common way to compare two distributions is by looking at key statistics like the mean, median, variance, quantiles, etc.
- For example, you can check if the current mean value of a numerical variable is within two standard deviations from the reference value. You can monitor the value of any individual statistics to react when it shifts.
- Monitoring only summary statistics has downsides, especially when you watch many features simultaneously, as it can become noisy. Still, this is a viable strategy when you have expectations, e.g., about median or quantile values for particular features based on your domain knowledge.
- You can also track feature range compliance, such as whether the values stay within a min-max range. This helps detect data quality issues, such as negative sales or sudden shifts in feature scale. However, this approach might not catch data drift when values remain within the expected range but show a different distribution pattern.

### **Statistical tests**

- A more advanced drift detection approach involves using statistical hypothesis testing. For example, you can use tests like **Kolmogorov Smirnov** for numerical features or the **Chi-square test** for categorical attributes. They help assess whether the difference between the two datasets is statistically significant.
- If the difference is detected, it suggests that the two datasets come from distinct distributions rather than random variation due to sampling. The output (and a “drift score”) of a statistical test is a **p-value** that serves as a “measure of confidence.”
- You might also need to consider the specifics of the data distribution to pick an appropriate test: for example, whether you expect the distribution to be normal.
- Detecting drift in individual features using statistical distribution tests often makes sense when you have a small number of important and interpretable features and deal with smaller datasets and high-stakes use cases like healthcare or education.
- However, it's essential to remember that a statistically significant difference may not always be practically significant. A useful rule of thumb is that if you can detect the difference with a relatively small sample, it's likely important. Otherwise, the tests may be overly sensitive.

### **Distance metrics**

- Another approach involves using distance metrics that quantitatively measure how far apart the distributions are. Distance metrics are valuable for understanding the extent of drift. A few commonly used distance metrics are Wasserstein Distance, Jensen-Shannon Divergence, or Population Stability Index (PSI), often used in credit risk modeling.
- The output (the “drift score”) is usually a distance or divergence. The higher the value, the further apart the distributions are. Depending on the metric, you might work with an absolute scale or a scale from 0 to 1.
- The benefit of this approach is that it allows quantifying the change rather than testing the assumption that both samples come from the same distribution. You can treat the resulting metric as a reflection of “drift size” that you can observe over time.
- When dealing with a large dataset, it is advisable to use distance metrics since statistical tests are likely to be overly sensitive in such scenarios. You can also choose to monitor “aggregate” drift by tracking the share of drifted features in the dataset as opposed to individual feature drifts.

### **Rule-based checks**

- Lastly, you can perform some drift checks using straightforward rules. For example, you might choose to be alerted whenever:
    - The share of the predicted “fraud” category is over 10%.
    - A new categorical value appears in a feature “location” or “product type.”
    - More than 10% of the feature “salary” values are out of the defined min-max range.
- While such checks do not directly measure drift, they can serve as good alerting heuristics to notify about likely meaningful changes to investigate further.

## **How to handle data drift**

Say you detected data drift in your input model features using your preferred method. What are the next steps?

### **Analysis**

- The first step in addressing data drift is analysis. Before taking any action, you need to understand the nature of the shift.
- You can start by comparing the visual distributions of the drifted features to explain the source change. Is it a genuine shift in the data, a data quality issue, or a false positive?
- Sometimes, there is a legitimate business explanation, such as a product change, and **no action** is required. You may also wait to collect enough data for model retraining.
- On the other hand, many issues manifesting as data drift might stem from **data quality** bugs. In this case, you need to locate and address the problem – for example, by contacting data producers or fixing the feature transformation pipeline.
- The frequent presence of data quality issues in production pipelines is a good reminder of why using drift detection as an automatic retraining trigger might be suboptimal. Before using data to retrain, you must verify that the data is valid and complete – and can serve as "training" material.
- You might also face **false positives.** If you observe unnecessary data drift alerts, you might adjust the sensitivity of your drift detection methods. A good rule of thumb is to consider feature importance and, for example, alert only to the drift in top model features.
- If you face a true data drift, you might need to take specific actions, such as retraining your model or updating the decision process.

### **Retraining**

- A common industry approach is to retrain models using the labeled data from the newly observed distribution. If labels are available and there is sufficient new data for retraining, this is usually a recommended course of action.
- Depending on the extent of the drift, you must choose different strategies for the model update. For example, you can retrain your model using old and new data. Alternatively, you can give higher weight to more recent data or drop the old data entirely. You can also re-run the feature engineering and model design process to develop a completely new approach.
- In any case, it is essential to have a robust architecture for new model roll-out and a thorough pre-release model testing procedure – to confirm that the new model is good enough.
- However, sometimes retraining is not feasible – for example, because you simply do not have the new labels to run the model update. In such cases, you might consider process interventions.

### **Process intervention**

- For example, you can **stop the model**. If the model's predictions are adversely affected by drift, you might need to halt its operation temporarily. You can do that for some affected segments, for example, particular product groups.
- You can also consider **modifying the decision process** on top of the model output. For example, when running a fraud detection model, you might lower the decision threshold that flags transactions as suspicious to send more of those for manual review.
- You can also consider adding other **business rules** to filter out potentially unreliable model predictions, such as overriding some extreme predictions.
- Finally, you can consider switching to other decision-making processes, from alternative fallback models to the **human in the loop.** For example, you can bring in domain experts to replace the automated decisions with good old human decision-making.

### **Model redesign**

- Addressing data distribution shifts doesn't have to be a reactive process. You can also consider adjusting your model design to be more resilient to data shifts.
- A frequent approach is feature selection. Before utilizing some of the available features in model training, you can review their historical variability and filter out the features with significant historical drifts. You can also consider applying some feature engineering techniques, such as bucketing volatile numerical features into a limited number of categories.
- Sometimes, it is worthwhile to choose a model that is less performant on a historical evaluation set but is more robust to data shifts. You can also consider using domain-based or causal models at the core of your system, which can be more reliable in case of data changes.

## Example

---

### Example Scenario: E-Commerce Churn Prediction Model

**Context**: The e-commerce company trains a churn prediction model in 2023 using historical customer data from 2020–2022. The model performs well during testing and is deployed to production. Over time, however, its performance degrades due to various changes in the data and environment. Let’s explore how each type of drift or issue appears.

1. **Data Drift** (Shift in Input Feature Distributions):
    - **What Happens**: In 2024, the company launches a new mobile app, leading to a significant increase in online purchases compared to in-store purchases, which dominated the training data. For example, the feature "purchase channel" (online vs. in-store) shifts, with online purchases now making up 70% of transactions compared to 20% in the training data. Similarly, average order values decrease as customers buy smaller, more frequent items through the app.
    - **Impact**: The model, trained on data where in-store purchases were the norm, struggles to accurately predict churn for customers using the mobile app, as the input feature distributions (e.g., purchase channel, order value) no longer match the training data.
    - **Detection**: Using a tool like Evidently AI, the company runs a Data Drift Preset to compare the current production data (2024) with the reference training data (2020–2022). Statistical tests (e.g., Kolmogorov-Smirnov or Wasserstein distance) detect significant shifts in the distributions of "purchase channel" and "average order value." Visual dashboards show histograms highlighting that online purchases now dominate, indicating data drift.
2. **Concept Drift** (Change in Input-Output Relationships):
    - **What Happens**: In 2025, a new competitor enters the market offering aggressive discounts, altering customer behavior. Customers who previously churned after long periods of inactivity (e.g., 6 months without a purchase) now churn faster (e.g., after 2 months) due to the competitor’s promotions. The relationship between the input feature "time since last purchase" and the target variable "churn" has changed.
    - **Impact**: The model’s learned patterns (e.g., long inactivity predicts churn) are no longer valid, leading to inaccurate predictions. For instance, customers with short inactivity periods are now more likely to churn, but the model underestimates their risk.
    - **Detection**: The company uses Evidently’s Classification Performance Report, which compares model performance on labeled production data to the training period. A drop in accuracy and a shift in the confusion matrix (e.g., more false negatives for churn) indicate concept drift. Proxy metrics like prediction drift (see below) may also suggest changes in the target’s behavior.
3. **Prediction Drift** (Change in Model Output Distributions):
    - **What Happens**: Due to the data and concept drift described above, the model’s predicted probabilities for churn shift in production. In the training data, the model typically predicted low churn probabilities (e.g., 10–20% for most customers). In 2024–2025, the model outputs higher churn probabilities (e.g., 30–40%) for more customers due to the shift toward online purchases and faster churn behavior.
    - **Impact**: This shift in prediction distribution suggests the model is responding to changes in input data or underlying patterns, but it may not be accurate. For example, the model might overestimate churn for loyal customers who shop online frequently.
    - **Detection**: Using Evidently’s Target Drift Preset, the company compares the distribution of predicted churn probabilities between the reference (training) and current (production) data. A statistical test (e.g., chi-squared for categorical predictions) shows a significant shift in prediction distributions, confirming prediction drift. Visual plots in Evidently’s dashboard highlight the change in probability ranges.
4. **Training-Serving Skew** (Mismatch Between Training and Production Data):
    - **What Happens**: During training, the data was cleaned to exclude incomplete customer profiles (e.g., missing location data). In production, however, the data pipeline includes many incomplete profiles due to relaxed data collection on the mobile app. For example, 30% of production data has missing values for "customer location," compared to No missing values in the training data.
    - **Impact**: The model, which was trained on complete data, performs poorly when encountering missing values in production, as it was not designed to handle such cases. This mismatch between training and serving data leads to degraded performance.
    - **Detection**: Evidently’s Data Quality Preset reveals a high share of missing values in the "customer location" feature in production data. Comparing the training and production datasets shows the training data was artificially clean, highlighting the skew. The company realizes this mismatch was introduced during data preprocessing.
5. **Outlier Detection** (Identifying Unusual Data Points):
    - **What Happens**: In 2025, a small group of customers starts making unusually large purchases (e.g., $10,000 orders compared to the typical $50–$200 range) due to a new bulk-buying program for businesses. These transactions are rare and deviate significantly from the typical data patterns seen in both training and production data.
    - **Impact**: The model may produce unreliable predictions for these outlier transactions, as they fall outside the range of data it was trained on. For example, it might incorrectly flag these high-value customers as likely to churn due to their unusual purchase amounts.
    - **Detection**: Using Evidently’s outlier detection tests, the company identifies these transactions as anomalies by applying statistical methods (e.g., z-scores or isolation forests) to the "order value" feature. The monitoring dashboard flags individual records with order values exceeding a threshold (e.g., 3 standard deviations from the mean) as outliers, allowing the company to handle them separately (e.g., via manual review or a fallback rule-based system).


### Summary

- **Data Drift**: Shift in purchase channel and order value distributions due to the mobile app.
- **Concept Drift**: Changed relationship between inactivity and churn due to a competitor.
- **Prediction Drift**: Shift in predicted churn probabilities reflecting input and target changes.
- **Training-Serving Skew**: Mismatch due to missing values in production data.
- **Outlier Detection**: Unusual bulk purchases flagged as anomalies.
