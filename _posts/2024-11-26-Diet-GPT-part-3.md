---
title: "Evaluating our Calorie Estimator with GPT-4o"
date: 2024-11-19 00:00:00
description: "Learn how to build an AI-powered calorie estimator using GPT-4's image analysis capabilities to automatically estimate calories from meal photos."
featured_image: /assets/img/pages/rag_series.jpg
author: Aladdin Persson
---


# Visualizing and Evaluating Diet GPT's Calorie Estimation Performance

Welcome back to the **Diet GPT** series! In the last post, we explored how GPT models can estimate calorie counts directly from images. While the results were promising, we wanted to dive deeper into the model's performance. Specifically, we wanted to answer questions like:

- How accurate are the calorie predictions?
- What are the common failure cases?
- Can we visualize the errors to better understand the model's strengths and weaknesses?

In this post, we'll analyze the model's performance using a CSV file containing the results from the previous experiment. We'll create visualizations, calculate key metrics, and identify extreme cases where the model overestimates or underestimates calorie counts. Let’s dive in!

---

## Setting Up the Analysis

To get started, we’ll use Python libraries like `pandas`, `seaborn`, and `sklearn` for data manipulation, visualization, and performance metrics. Here's the plan:

1. Load the CSV file containing:
   - Image name
   - Actual calories
   - Estimated calories
   - Calorie difference
   - LLM (Large Language Model) output
2. Compute key metrics like mean error, percentage error, and R².
3. Visualize the error distribution and scatter plots of actual vs. estimated calories.
4. Identify extreme cases of overestimation and underestimation.

Here’s the code to set up the analysis:

```python
import os
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from sklearn.metrics import r2_score

def analyze_model(data_path, output_dir):
    # Create output directory if it doesn't exist
    os.makedirs(output_dir, exist_ok=True)
    
    # Load the data
    df = pd.read_csv(data_path)
    
    # Extract relevant columns
    actual = df['actual_calories']
    predicted = df['estimated_calories']
    errors = predicted - actual
    percentage_errors = (errors / actual) * 100
    absolute_percentage_errors = percentage_errors.abs()
    
    # Add computed metrics to the DataFrame
    df['errors'] = errors
    df['percentage_errors'] = percentage_errors
    df['absolute_percentage_errors'] = absolute_percentage_errors
    
    # Key metrics
    mean_error = errors.mean()
    mean_percentage_error = percentage_errors.mean()
    mean_absolute_percentage_error = absolute_percentage_errors.mean()
    r_squared = r2_score(actual, predicted)
    
    # Error percentiles
    error_percentiles = absolute_percentage_errors.describe(percentiles=[0.25, 0.5, 0.75])
    
    # Print key metrics
    print(f"Mean Error: {mean_error:.2f}")
    print(f"Mean Percentage Error: {mean_percentage_error:.2f}%")
    print(f"Mean Absolute Percentage Error: {mean_absolute_percentage_error:.2f}%")
    print(f"R² Score: {r_squared:.2f}")
    print(f"Error Percentiles:\n{error_percentiles}")
    
    # Identify extreme cases
    worst_overestimates = df.nlargest(2, 'errors')
    worst_underestimates = df.nsmallest(2, 'errors')
    
    print("\nWorst Overestimates:")
    print(worst_overestimates[['image_name', 'actual_calories', 'estimated_calories', 'errors']])
    
    print("\nWorst Underestimates:")
    print(worst_underestimates[['image_name', 'actual_calories', 'estimated_calories', 'errors']])
    
    # Visualization
    plt.figure(figsize=(15, 5))
    
    # Histogram of errors
    plt.subplot(1, 3, 1)
    sns.histplot(errors, kde=True, bins=30)
    plt.title("Error Distribution")
    plt.xlabel("Error (Estimated - Actual)")
    plt.ylabel("Frequency")
    
    # Scatter plot of actual vs. predicted
    plt.subplot(1, 3, 2)
    sns.scatterplot(x=actual, y=predicted)
    plt.plot([actual.min(), actual.max()], [actual.min(), actual.max()], color='red', linestyle='--')
    plt.title("Actual vs. Predicted Calories")
    plt.xlabel("Actual Calories")
    plt.ylabel("Predicted Calories")
    plt.axis('equal')
    
    # Text summary
    plt.subplot(1, 3, 3)
    plt.axis('off')
    summary_text = (
        f"Mean Error: {mean_error:.2f}\n"
        f"Mean % Error: {mean_percentage_error:.2f}%\n"
        f"Mean Absolute % Error: {mean_absolute_percentage_error:.2f}%\n"
        f"R² Score: {r_squared:.2f}\n"
        f"25th Percentile Error: {error_percentiles['25%']:.2f}%\n"
        f"75th Percentile Error: {error_percentiles['75%']:.2f}%"
    )
    plt.text(0.1, 0.5, summary_text, fontsize=12)
    
    # Save the figure
    output_path = os.path.join(output_dir, "analysis_results.png")
    plt.tight_layout()
    plt.savefig(output_path)
    print(f"Analysis results saved to {output_path}")
```

---

## Key Findings

After running the analysis, here’s what we discovered:

### 1. **Overall Accuracy**
- **Mean Error:** The model overestimates by an average of 8.6 calories, which is excellent given the dataset's mean of 460 calories and standard deviation of 280.
- **Mean Absolute Percentage Error (MAPE):** 4.6%, indicating that the model is generally accurate.
- **R² Score:** 0.61, showing a moderate correlation between actual and predicted values.

### 2. **Error Distribution**
The histogram of errors reveals that most predictions are close to the actual values, but there are some extreme outliers (e.g., ±300 calories).

### 3. **Extreme Cases**
- **Worst Overestimate:** The model predicted 650 calories for a meal with an actual value of 350 calories. The error likely stems from overestimating the quantity of food items (e.g., assuming three eggs instead of two).
- **Worst Underestimate:** The model predicted 175 calories for a meal with an actual value of 360 calories. This error was due to underestimating the number of almonds in the image.

### 4. **Impact of Image Resolution**
Low-resolution images (e.g., 640x480) seem to negatively impact the model's performance. Higher-resolution images might improve accuracy.

---

## Next Steps

While the model performs well on average, there’s room for improvement. Here’s what we plan to do next:

1. **Incorporate a Food Database:** By integrating a database of food items and their nutritional values, we can replace the model's intuitive guesses with precise lookups.
2. **Human-AI Collaboration:** Combining the model's predictions with human supervision could significantly reduce errors, especially in edge cases.
3. **Micronutrient Analysis:** Adding a food database will also enable us to estimate micronutrients, providing a more comprehensive nutritional breakdown.

---

## Conclusion

The Diet GPT model shows great promise in estimating calorie counts from images. While it performs well on average, extreme cases highlight the need for human oversight and additional data sources. By combining AI with human expertise, we can achieve faster and more accurate results.

Stay tuned for the next post, where we’ll explore how integrating a food database can enhance the model’s performance. In the meantime, feel free to join the discussion on our [NaNEye Discord](https://discord.gg/Q2Xz5Atzze) or try out the [NaNEye app](https://apps.apple.com/us/app/naneye/id6612032845)!

Happy coding! 🚀
