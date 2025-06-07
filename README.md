# What is the Relationship Between Protein Content in a Recipe and its Rating?

This investigation will explore the relationship between the protein content relative to the amount of calories in a recipe and its rating.

Group: Bryan Nguyen

---

## Introduction

There are many things in life that bring people all around the world together. Whether it's music, culture, hobbies, you name it. One of those things is food. Food is one of the universal languages that people can connect through and come together for. In recent years, people have become more conscious of the food that they eat and what they put into their body, with awareness rising over topics such as fitness, health and wellness, healthy eating habits, etc. Keeping in check of these aspects can come in many ways. One way of building up these good habits are by keeping a log of how much protein one consumes, which is a great way to buold muscle. I myself have taken strides in this realm of keeping track of what I am putting in my body and the nutrients I consume after cooking my meals. Especially around my peers, recipes and meals are often highlighted by how much protein is in them, and are well received from my experience. This made me wonder if people would rate recipes higher depending on how much protein was in it. To tackle this thought, I am going to be analyzing two datasets containing recipes and their respective ratings post 2008 from food.com. The first dataset (recipes) contains tens of thousands of rows containing unique food recipes with the columns of recorded data about them below: 

placeholder

The second dataset (interactions) contains hundreds of thousands of rows containing reviews from users on a unique recipe from the mentioned ones earlier. Its columns of recorded data are shown below: 

placeholder


With this data, I am interested on exploring this essential question: Do people rate protein rich recipes on the same scale as non protein rich recipes? 

---

## Data Cleaning and Exploratory Data Analysis

<!-- Use markdown tables (from .to_markdown()) and summary bullets. Example: -->

Although the dataset had a plentiful amount of useful data, it required certain data cleaning steps and techniques before proceeding to data analysis. 

-First, a left merge of the recipes and interactions datasets on the respective id and recipe_id columns were performed in order to match the individual recipes with their ratings and reviews. 
-Next, the rating values with 0's were replaced with np.nan values. This is because the lowest rating can be 1, and 0's are indicative of missing data. However, leaving the 0's in would lead to biases later in the analysis because if left in the dataaset, the recipe's with 0's would be received as "bad" or "poor" instead of just a missing rating, which is incorrect. 
-An average rating column was then added into the recipes dataframe recording the respective recipe's average rating based on the ones it received.
-The nutrition column's values were then split into their respective nutritional value columns such as calories, protein, sugar, etc. This is because the datatype of the nutrition column's values are actually objects posing as strings. A lambda function was utilized to split up each value in the list format of the nutrition value and converted to floats for eased calculation later in the analysis
-A new column "protein_ratio" was added to teh dataframe. This column contains values about a recipe which measures its protein content relative to the number of calories it has. 1 gram of protein is 4 calories. So a recipe with 1200 calories and 60g of protein has a protein ratio of 0.2. This is important to note because protein can be standardized across recipes with different nutritional data. A recipe with 30g of protein would most intuitively seem protein rich if it had 400 calories, but maybe not if it had 1200 calories. 
-Another new column "is_protein_rich" was also added to the dataframe, which is a column of boolean values dictating if a recipe is considered rich in protein. This is derived from the protein_ratio column, where recipes with a value of greater than or equal to 0.2 would be considered rich in protein and score True for that value
-The resulting dataframe had contained 234429 rows, and 27 columns. Certain columns were kept and others were discarded depending on if they were relevant to the exploration of the question. 

plot placeholdr

-The final merged Dataframe contains 234429 rows of all user interactions with recipesm slimmed down to 13 columns of data that is actually valuable to the exploration. 

final merged df placeholder

## Univariate Analysis

For this analysis, I examined the distribution of the proportion of protein in each recipe. The plot below shows the distrubution is right skewed. This shows that most recipes in the dataset have a relatively low proportion of protein compared to how many calories the respective recipe contains. As the protein ratio increases on the x axis, the number of recipes decrease, which show that protein rich recipes are not as common as the ones that are not as protein rich. The majority of recipes fall into the pit of recipes with less than 0.2 for protein ratio, while fewer recipes exceed the 0.2 mark and very few make it past 0.5. I will keep this observation in mind later during the analysis to explore whether recipes iwth higher protein content tend to recieve higher ratings on average.

<iframe src="assets/uniplot1.html" width="800" height="600" frameborder="0"></iframe>

## Bivariate Analysis

For this analysis, I examined the distribution of the the average ratings of recipes conditioned on whether the recipe is protein rich or not, based on what was classified as protein rich earlier. The graph below shows the proportion of recipes across the ratings possible for a recipe (1-5) for the 2 groups, protein rich and not protein rich. 
The non protein rich recipes have a slightly higher proportion of 3 and 5 star ratings relative to the ones that are protein rich, which shows that the recipes with lower protein to calorie ratio tends to be more well received than the ones that have a higher ratio more times than not. However, the protein rich recipes show a higher proportion of 4 star ratings than the non protein rich ones. This suggests that the protein dense recipes are consistently well rated on average but are less likely to receive outstandingly low/high ratings. Meanwhile, the non protein rich recipes are more likely to receive 3 star and 5 star ratings, which indicates more variety in how they are received by users. This can show a distinction between users receiving the protein rich recipes more consistently while having more varying opinions on the ones that aren’t packed with as much protein. 

<iframe src="assets/uniplot2.html" width="800" height="600" frameborder="0"></iframe>

## Interesting Aggregates


Notice, the mean average rating for both protein rich and non protein rich recipes are roughly same. Protein rich recipes tend to have more steps (only ever so slightly), and this is the same for ingredients, where protein rich recipes tend to use more ingredients. 
placeholder plot

Protein rich recipes can be received as more intricate or complex (more steps and ingredients). Even though they are more complex/intricate, ratings seem to go toe to toe with the non protein rich recipes despite the fact they seemingly are more complex and intricate

placeholder plot

## Assessment of Missingness

The columns of the "rating", "review", and "date" had a large amount of missing data from the merged dataset from before.

### NMAR Analysis
I believe that the review column in the merged dataset has missingness which is considered as Not Missing at Random (NMAR). This is because of a few reasons. Users who perceive a recipe very strongly and have a lot to say about it are more likely to review. On the other hand, those who do not perceive the recipe much or at all might feel less inclined to say anything or have any thoughts at all, and thus not leave a review. Whether a review is missing or not in the dataset depends on the feelings and thought process of the user, which is unobserved and happens behind the scenes, property of the missing value itself. This is likely NMAR because missingness is connected to the user's feelings, and rather not observed in any other column available in the dataset.

### Missingness Dependency
I wanted to test whether the missingness in the rating column was dependent on the protein content in the recipe. 
- Proportion of Protein and Rating
- Null Hypothesis: Missingness in ratings column does NOT depend on the protein ratio in the recipe
- Alternate Hypothesis: Missingness in the ratings column DOES depend on the protein ratio in the recipe
- Test statistic: the absolute difference in mean protein_ratio between rows where the rating is missing compared to the not missing rows
- Significance Level: 0.05

plot placeholder

First, the observed absolute difference statistic was calculated in the average protein_ratio between recipes with ratings missing and the ones that didn't have missing ratings. Then, I ran a permutation test by shuffling the missingness 1000 times and simuted test statistics to form a null distribution. 
The observed test statistic was 0.003872, and noticeably distant from the center of null distribution. The p value was 0.001, (0.001 < 0.05). The p value is less than the significance value set earlier, which means we reject the null hypothesis here. The missingness of the rating column values does indeed depend on the protein content of the recipe. This could lead to a suggestion, for example, that users are more inclined to give a rating for the higher protein recipes (for example, if it contributes to their fitness goals or meals) as opposed to the lower protein recipes which may have been bypassed. 


## Hypothesis Testing
As mentioned earlier, I am curious about whether people rate protein-rich recipes differently than non–protein-rich recipes. For this analysis, I defined protein-rich recipes as those where the proportion of protein (calculated as protein calories divided by total calories) is greater than or equal to the average protein ratio across all recipes
To investigate this question, I utilized a permutation test with the following hypothesis, test statistic, and significance level: 
- Null Hypothesis: People rate protein-rich and non–protein-rich recipes on the same scale.
- Alternative Hypothesis: People rate protein-rich recipes lower than non–protein-rich recipes.
- Test Statistic: The difference in mean rating between protein-rich recipes and non–protein-rich recipes (protein-rich − non–protein-rich).
- Significance Level: 0.05

I chose to use a permutation test because I do not have access to the population distribution of ratings, and this test allows me to simulate what differences in mean rating may be expected if the null hypothesis were true. Since the alternative hypothesis is directional (whether protein rich recipes are lower or not), I opted for a left tailed permutation test, comparing the observed test statistic to simulated differences in simulations that are less than or equal to it.

First, I grouped the dataset into 2 groups, protein rich and non protein rich recipes (where the is_protein_rich value column value for protein rich == True). Then I calculcated the mean rating for each group and calculated the difference in means. The observed statistic turned out to be -0.0183, which means the protein rich recipes were actually rated on average those amount of points lower than non protein rich recipes. Next, I shuffled the boolean protein rich labels 1000 times, each time calculating the difference in means to create a null distribution of the differences between the 2 groups. 

placeholder plot

### Permutation Test Conclusions
The p value in the end was essentially 0.0 (which is < the significance level 0.05), and with that, we reject the null hypothesis. This shows people do not rate all the recipes on quite the same scale, and that protein rich recipes tend to be rated lower than the non protein rich ones.

## Framing a Prediction Problem
My goal is to predict the average rating of a recipe. Since the ratings can be found on scale of 1-5, this will be considered a classification problem. I will aim to create a multi class classifier (depicting the possible ratings a recipe could fall under) which predicts which one of these five values can be predicted and assigned for each recipe

The response variable would be avg_rating (and rounding it to fit 1-5). This indicates a multiclass classification problem since there are more than just 2 possible classes a recipe can fit in. Predicting this will be based on the recipe features such as ingredients, nutrients, and treating them as input features. 

Why? This reflects how users perceive a recipe based on its contents such as nutrition facts, steps, etc. From earlier, protein rich recipes tend to receive lower ratings on average, so things such as protein proportions may 
play a pivotal role in what the users think of a recipe and eventually rate it.

F1 score will be used to evaluate the model because as seen from earlier, the classes of ratings from 1-5 weren't necessarily balanced in terms of distribution, but rather right skewed towards the 4 and 5 ratings (lot more 4 and 5 ratings than 1, 2, and 3). Accuracy relies heavily on the fact that classes are equal and the dataset is balanced. But the dataset is not balanced, and skewed towards high end of rating scale. For example, if the model always predicts 4 or 5, it might be very accurate due to the fact that the dataset represents mostly 4s and 5s. But, acccuracy is not the end all be all, and it does not always mean it learned anything meaningful, and could turn out to be a poor model. F1 score is more appropriate here, and tells us more given the situation where our classes and groups are imbalanced, which is the situation we have here. F1 score is pivotal in helping us measure how well the model is performing across each of our classes, not just the dominant ones. Is more distinctive between the classes than accuracy, so F1 will be used. 

## Baseline Model
In terms of the baseline model, I will be calling upon the RandomForestClassifier to predict the average rating of each unique recipe (rounded to closest values in 1-5 range). I will be using 2 features notable for this: the protein_ratio of a recipe(quantitative) and the is_protein_rich value for a recipe (nominal). I implemented one hot encoding on the boolean values of "is_protein_rich" (with 1's being true and 0's being false) using the ColumnTransformer method as well as Pipeline to incorporate preprocessing techniques. I then assessed the model using the F1 score as previously mentioned. 

The F1 score of this model turned out to be 0.839 for the weighted row, but with a score of 0.498 for the macro average row. The model performed quite well on the classes of 4 and 5 ratings, but turned out to struggle with the classes that were less popular and underepresented. The model essentially predicts exceptionally better for the 4's and 5's ratings, but it cannot be said the same for the lower rating classes. As seen before, the dataset contained more recipes congested in the 4-5 range compared to the 1-3 range, which explains this performance and F1 score result. 

I believe although the current model at face value is seemingly "good" due to its exceptional performance in some domains of the ratings (4-5), I am not convinced that it is a "good" model quite yet. Although this is a great starting point. This is because it is tailor made predicting towards the favorable 4-5 ratings due to the dataset being dominated by said ratings. Meanwhile the underrepresentation of the ratings ranging from 1-3 are still present. This could pose as a possible bias in the grand scheme of the model and a more balanced performance across all fronts, firing on all cylinders would be needed in order to improve the model and have it be considered "good" in my eyes. 


## Final Model
In regards to the final model's specifications, I decided to utilize protein_ratio, is_protein_rich, minutes, calories, n_steps, and n_ingredients as the nominated features. These features below where chosen because they are interpretable factors that could influence a recipe's rating in aspects such as nutrition, recipe complexity, etc. 

-protein_ratio: This is the ratio of protein to calories in a recipe. This is one of the core variables of my hypothesis as well as a core feature of the baseline model. I believe this is an important feature to have because protein dense recipes might be received differently depending on the user/audience and whether it aligns with or enhances their health goals, which could influence their rating

-is_protein_rich: This is a boolean flag marking whether a recipe's protein ratio is greater than a said threshold (0.2). This feature is categorical and is a derivation of the protein_ratio value of a recipe, which adds another level of interpretation to a recipe and its protein contents relative to its calories. 

-minutes: I decided to include this feature because the time it takes a recipe to be made can affect how the user feels about the recipe. Longer preparation times might contribute to lower ratings, and even lower if the preparation was longer and the user hypothetically didn't even like the recipe regardless of how long it took to make. People can be quite busy, so a recipe that takes considerably longer might be susceptibvle to scoring lower in the ratings domain. The time duration of a recipe can be quite volatile, with a recipe that could take a few minutes to one that requires overnight marination or something such as long baking times. This can represent extreme values across the dataset, so I decided to utilize RobustScaler to standardize the minutes feature and make sure this is reduced. 

-calories: Calories is a feature that I decided to include due to the many interpretations of ratings in relations to calories. For example, looking at a health conscious perspective, low calorie recipes might tend to be reated higher depending on the audience and how it relates to their fitness goals such as weight loss, low calorie meals, etc. On the other hand, low calorie foods may also be susceptible to being rated low due to the lack of variety of flavor or ingredients that may be incorporated in the recipe. This inversely is intuitive for high calorie foods as well, where they could be higher rated due to the full potential of flavor and enjoyment of the recipe not being restricted by a calorie threshold, and could inversely be rated low by those who are more conscious of the calories they are consuming on a daily basis and are not as fond of such recipes. During the Exploratory Data Analysis section (EDA), it was shown that the high rated recipes had fewer calories on average. I also used RobustScaler here to cushion the impact of outliers.

-n_steps: I decided to include the number of steps as well in this model because I believe it is one of the contributing factors in determining how complex/intricate a recipe is. Some people may prefer elaborate, decadent dishes filled with many details and steps. However, others might have different views and may possibly rate a recipe relatively low due to the fact that it could be too complicated, cluttered with steps, and overall difficult to execute and long to follow. For this feature, I decided to standardize this feature with StandardScaler because this feature didn't show any signs of heavy skews or absurd outliers 

-n_ingredients: The last feature I decided to include in the final model is the number of ingredients a recipe has. Similarly to the number of steps, I believe this feature not only is an indicator of how complex or intricate a recipe is, but also how accessible the recipe is to the user. Intuitively, recipes with more ingredients might steer away users due to the fact that users might not have the ingredients readily available and would need to go out of their way to purchase or prepare the ingredients. This can be added recipe complexity and reduce accessbility of it for those who might not be able to also afford it, for example, and thus could lead to lower ratings. I used RobustScaler to standardize this feature keeping in mind that some recipes might have absurdly long and excessive amount of ingredients. 

For the final model, I used RandomForestClassifier as the modeling algorithm due to its ability to handle all sorts of datatypes (categorical, numerical, etc.) and utilized GridSearchCV to fine tune the RandomForestClassifier's hyperparameters max_depth and n_estimators. This is so I could control how deep each tree could go and ensure it wasn't prone to overfitting as well as controlling the number of trees in the forest to cushion sacrificing computational time for performance. GridSearchCV was able to perform a search for the best hyperparameter pairing across 9 (3 x 3) possible combinations, while utilizing 3 fold cross validation to assess each combination and its efficiency across the train/test splits imposed. Through this process, the best performing combination found was: [max_depth = 30, n_estimators = 125]

In the end, the F1 score of the Final Model was 0.91, which is a definite improvement on the Baseline Model's F1 score of 0.839 by over a 0.07 increase. The F1 score of each rating also massively improved for each and every single category rating, with the Final Model's scores being 0.259, 0.587, 0.629, 0.833, and 0.947 for the 1-5 rating categories. 

## Fairness Analysis

Lastly for the Fairness Analysism I split the recipes into 2 groups, which were quick and long recipes (in terms of the time it takes to cook). Defining the quick recipes included those that took less than or the same as the median # of minutes to cook, and the the long ones being the ones that took more than the median. The dataset included potential extreme outliers in terms of minutes required to make, so the median was utilized to budge less against these outliers

The precision parity of the Final Model was tested. This was because I was curious to know whether the model performs equally well depending on whether the recipe took long or not to make and prepare. For evaluation metric, precision was preferred because false positives were not wanted in case it was presented to show misleading information (one example being where a recipe predicted to have a high rating actually was in turn very low rated) which could harm the user and place doubt in this process. This goes for especially when a user is indulging in a long recipe for example, expecting it to be worth the cook time assuming it is a high rated recipe when in fact it is a low rated one. 

-Null Hypothesis: Our model is fair. Its precision for quick and long recipes is roughly the same, and any observed difference is due to random chance.
-Our model is unfair. Its precision differs between quick and long recipes—specifically, it performs better on one group than the other.
-The difference in precision (Quick Recipes - Long Recipes)
-Significnace Level: 0.05

plot placeholder

The observed test statistic  in precision was calculated to be 0.0025, meaning the model's precision was ever so slightly higher for quick recipes
I ran a permutation test to assess the significance of this statistic by shuffling a "is_aquick_recipe" column 1000 times over and developing the null distribution with simulated test statistics. I ended up with a p value of 0.0 (<0.05), meaning we reject the null hypothesis and that the precision of the model is not fair across recipe minutes duration times due to the fact it performs slightly better for quick recipes than the long ones. This could be crucial in determining that the model may be biased against the long recipes compared to the quick ones. 

### Example Table (Top 5 rows)
