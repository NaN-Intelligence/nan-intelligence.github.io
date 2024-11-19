---
title: "Building a Calorie Estimator with GPT-4: A Step-by-Step Guide"
date: 2024-11-19 00:00:00
description: "Learn how to build an AI-powered calorie estimator using GPT-4's image analysis capabilities to automatically estimate calories from meal photos."
featured_image: /assets/img/pages/gpt_part2.jpg
author: Aladdin Persson
---



Welcome to the second post in the **DietGPT** series! In this post, we’ll dive into the actual process of building a calorie estimator using GPT-4. If you missed the first post, don’t worry—you can still follow along as we’ll be building this project iteratively. We’ll start with something simple and gradually add more functionality over time.

Before we jump into the technical details, if you haven’t already, feel free to join the [NaNEye Discord](https://discord.gg/Q2Xz5Atzze) and check out the [NaNEye app](https://apps.apple.com/us/app/naneye/id6612032845) if you’re interested in tracking your health metrics like calories, macronutrients, and more.

In this post, we’ll focus on using GPT-4 to estimate the calorie content of meals directly from images. We won’t be using a food database for this; instead, we’ll rely on GPT-4’s ability to analyze images and provide calorie estimates. Let’s get started!

### The Challenge: Estimating Calories from Images

Estimating the calorie content of a meal from an image is a challenging task. Even for humans, it can be difficult to accurately gauge portion sizes and hidden ingredients. However, with the power of GPT-4, we can attempt to automate this process.

For this project, I’ve collected around 80+ images from a subreddit where users post pictures of their meals. These images will serve as our dataset. Our goal is to estimate the total calorie content of each meal using GPT-4.

### The Prompt

The first step is to craft a prompt that will guide GPT-4 in estimating the calories. Here’s the prompt we’ll use:

```text
You are an expert food analyst specialized in estimating the calories from images of meals. Your task is to analyze a meal from an image and provide a precise estimate of its total calorie content.

Please follow these steps:
1. Carefully examine the image and identify all food items present.
2. For each food item, determine the portion size.
3. Consider any hidden ingredients that might affect the calorie content.
4. Calculate the calorie content based on the portion size.
5. Sum up the calorie estimate to arrive at a single total calorie count for the entire meal.

Always provide a single number estimate. Prioritize accuracy over speed. After analysis, provide a total calorie estimate in the following format:

Calories: [number]
```

This prompt ensures that GPT-4 follows a structured approach to analyzing the image and provides the calorie estimate in a specific format.

### Why Not Use JSON?

You might wonder why we’re not using structured JSON output for this task. While JSON output can be useful, I’ve found that it tends to degrade performance in this specific use case. Additionally, using a step-by-step approach in the prompt improves the accuracy of the estimates.

### The Code: Building the Calorie Estimator

Now that we have our prompt, let’s move on to the code. We’ll be using the GPT-4 API to send images and receive calorie estimates. Here’s a breakdown of the code:

#### Imports

We’ll start by importing the necessary libraries:

```python
import logging
import re
import asyncio
import tenacity
from tqdm import tqdm
from PIL import Image
import base64
import openai
```

#### The Calorie Estimator Class

Next, we’ll create a class `CalorieEstimator` that handles the image processing and API requests.

```python
class CalorieEstimator:
    def __init__(self, api_key):
        self.api_key = api_key
        self.system_prompt = """
        You are an expert food analyst specialized in estimating the calories from images of meals. Your task is to analyze a meal from an image and provide a precise estimate of its total calorie content.
        Please follow these steps:
        1. Carefully examine the image and identify all food items present.
        2. For each food item, determine the portion size.
        3. Consider any hidden ingredients that might affect the calorie content.
        4. Calculate the calorie content based on the portion size.
        5. Sum up the calorie estimate to arrive at a single total calorie count for the entire meal.
        Always provide a single number estimate. Prioritize accuracy over speed. After analysis, provide a total calorie estimate in the following format:
        Calories: [number]
        """
        openai.api_key = self.api_key

    def image_to_base64(self, image_path):
        with open(image_path, "rb") as image_file:
            return base64.b64encode(image_file.read()).decode('utf-8')

    async def analyze_image(self, image_path):
        image_base64 = self.image_to_base64(image_path)
        prompt = f"Analyze the following meal image and estimate the total calorie content:\n{image_base64}"

        response = await self.make_api_request(prompt)
        return self.parse_response(response)

    @tenacity.retry(wait=tenacity.wait_exponential(), stop=tenacity.stop_after_attempt(5))
    async def make_api_request(self, prompt):
        response = await openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": self.system_prompt},
                {"role": "user", "content": prompt}
            ],
            temperature=0.0,
            max_tokens=4000
        )
        return response

    def parse_response(self, response):
        content = response['choices'][0]['message']['content']
        match = re.search(r'Calories:\s*(\d+)', content)
        if match:
            return int(match.group(1))
        return None
```

#### Explanation of the Code

1. **`image_to_base64`**: Converts the image to a base64 string, which is required by the GPT-4 API.
2. **`analyze_image`**: Sends the image to GPT-4 and retrieves the calorie estimate.
3. **`make_api_request`**: Handles the API request to GPT-4. We use the `tenacity` library to retry the request in case of rate limits.
4. **`parse_response`**: Extracts the calorie estimate from the GPT-4 response using a regular expression.

#### Running the Estimator

Now that we have the `CalorieEstimator` class, we can use it to estimate the calories for a batch of images. Here’s how we can do that:

```python
async def main():
    api_key = "your_openai_api_key"
    estimator = CalorieEstimator(api_key)

    image_paths = ["image1.jpg", "image2.jpg", "image3.jpg"]  # Replace with your image paths
    results = []

    for image_path in tqdm(image_paths):
        calories = await estimator.analyze_image(image_path)
        results.append({
            "image": image_path,
            "calories": calories
        })

    # Save results to a CSV or analyze further
    print(results)

if __name__ == "__main__":
    asyncio.run(main())
```

#### Adding a Progress Bar

To track the progress of the calorie estimation, we can add a progress bar using `tqdm`. This will give us a visual indication of how many images have been processed.

```python
from tqdm import tqdm

async def main():
    api_key = "your_openai_api_key"
    estimator = CalorieEstimator(api_key)

    image_paths = ["image1.jpg", "image2.jpg", "image3.jpg"]  # Replace with your image paths
    results = []

    for image_path in tqdm(image_paths):
        calories = await estimator.analyze_image(image_path)
        results.append({
            "image": image_path,
            "calories": calories
        })

    # Save results to a CSV or analyze further
    print(results)

if __name__ == "__main__":
    asyncio.run(main())
```

### Results and Next Steps

After running the calorie estimator on our dataset of 84 images, we obtained calorie estimates for each meal. However, some estimates took longer than others, and there were a few discrepancies between the estimated and actual calorie counts.

In the next post, we’ll dive deeper into analyzing the accuracy of these estimates and explore ways to improve the model’s performance. We’ll also look at how we can handle edge cases and improve the overall user experience.

### Conclusion

This was the first step in building our **DietGPT** calorie estimator. While the initial results are promising, there’s still room for improvement. In future posts, we’ll continue refining the model and adding more functionality, such as tracking macronutrients and micronutrients.

If you found this post helpful, consider joining the [NaNEye Discord](https://discord.gg/Q2Xz5Atzze) and trying out the [NaNEye app](https://apps.apple.com/us/app/naneye/id6612032845). Stay tuned for the next post in the series, where we’ll analyze the accuracy of our calorie estimates and explore ways to improve them.

See you next time!