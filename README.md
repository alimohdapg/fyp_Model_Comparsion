# fyp_Model_Comparsion

A comparsion of the VADER and Twitter-roBERTa-base sentiment analysis models.

## Retrieving Comments for Comparison
The YouTube Data API was used to fetch all the possible categories for a video on YouTube. As a region had to be chosen to retrieve the categories, the US was chosen as it’s the default region on YouTube and has all the available categories. The 32 total categories that were retrieved were then used to retrieve 32 comments each. However, it was quickly realized that a lot of these categories were not appropriate for comment retrieval as they either had no or very few videos associated with them.
 
The category list was therefore shortened to only contain video categories that have at least 64 videos with comments associated with them. This resulted in the list now only containing 17 video categories, and at 64 comments per video category, a total of 1088 comments were retrieved (English was selected as the “relevance language” when retrieving the comments as that is what both sentiment analysis models are tuned for). The retrieved comments were then individually and manually labeled as either Positive, Negative, or Neutral by the researcher.

Lastly, before being used for comparison/finetuning, the comments were preprocessed to replace any user mentions (@) with “@user”, and any links with “http”.

## Vader vs Twitter-roBERTa-base (Unfine-tuned)
Passing all 1088 comments to both models to acquire predicted labels, the following metrics were acquired:

**VADER**
|Metric|Score|
| ------------- | ------------- |
|Accuracy|65.5%|
|F1 Score|62.8%|
|Precision Score|65.3%|
|Recall Score|62.0%|
|Time Taken[^1]|~0 s|
> Table 1 - VADER model metrics.
<img width="350" alt="image" src="https://github.com/alimohdapg/fyp_Model_Comparsion/assets/84683922/e82f9d67-d4be-4ae5-b015-3852933e9a82">

> Figure 1 - VADER model confusion matrix.

**Twitter-roBERTa-base (Unfine-tuned)**
|Metric|Score|
| ------------- | ------------- |
|Accuracy|76.9%|
|F1 Score|75.8%|
|Precision Score|75.8%|
|Recall Score|75.8%|
|Time Taken|~1 min|
> Table 2 - Twitter-roBERTa-base (Unfine-tuned) model metrics.
<img width="350" alt="image" src="https://github.com/alimohdapg/fyp_Model_Comparsion/assets/84683922/3b430d95-209a-4164-8d12-7ccada64197d">

> Figure 2 - Twitter-roBERTa-base (Unfine-tuned) confusion matrix.

From these metrics, we can infer that the VADER model although performs worse in all aspects relating to getting accurate predictions, the VADER model obtains the predicted labels almost instantaneously. The Twitter-roBERTa-base model, on the other hand, performs much better but is much slower in acquiring predictions.

It is also important to note that using the Twitter-roBERTa-base model necessitates the use of a powerful GPU to acquire results, as without it, the model obtains results at an unusable speed. For example, analyzing the same 1088 comments using Google Collab’s free tier which uses a slightly inferior GPU performance-wise, resulted in predictions being acquired in ~5 mins, which is a 5x increase in time over the ~1 min result obtained during the analysis.

## Fine-tuning Twitter-roBERTa-base
To fine-tune the model, the comments were first split into two sets, a training/validation set (90%, 979 comments), and a testing set (10%, 109 comments). As the number of comments isn’t that large, k-fold cross-validation was used as it allows for the use of more data, helps avoid overfitting, and reduces the variance in acquiring predictions. 5 folds were created using the training/validation set before then fine-tuning the model using Hugging Face’s Trainer class.

With two Twitter-roBERTa-base models now being at hand, the testing set was passed to both models to acquire the metrics shown in Tables 3 and 4, and Figures 11 and 12. It can be deduced that the finetuning had noticeable performance enhancements, increasing the accuracy, F1 score, recall, and precision by 3.3% - 3.7%. The time needed to acquire predictions was also identical for both models, this is sensible as no modifications were made to the model’s architecture, with the only changes being made to the model’s weights.

**Twitter-roBERTa-base (Fine-tuned)**
|Metric|Score|
| ------------- | ------------- |
|Accuracy|84.4%|
|F1 Score|82.5%|
|Precision Score|82.1%|
|Recall Score|83.1%|
|Time Taken|~7 s|
> Table 3 - Twitter-roBERTa-base (Fine-tuned) model metrics.
<img width="350" alt="image" src="https://github.com/alimohdapg/fyp_Model_Comparsion/assets/84683922/463e07cb-d085-4a71-8adc-daf701b92ad1">

> Figure 3 - Twitter-roBERTa-base (Fine-tuned) confusion matrix.
 
**Twitter-roBERTa-base (Unfine-tuned)**
|Metric|Score|
| ------------- | ------------- |
|Accuracy|80.7%|
|F1 Score|79.1%|
|Precision Score|78.7%|
|Recall Score|79.8%|
|Time Taken|~7 s|
> Table 4 - Twitter-roBERTa-base (Unfine-tuned) model metrics.
<img width="350" alt="image" src="https://github.com/alimohdapg/fyp_Model_Comparsion/assets/84683922/89934b20-4677-4901-94d1-8ccd941b019f">

> Figure 4 - Twitter-roBERTa-base (Unfine-tuned) confusion matrix.

[^1]: All comparisons were run on an Apple MacBook Pro (14-inch, 2021), configured with an M1 Pro chip with an 8-core CPU with 6 performance cores and 2 efficiency cores, a 14-core GPU, a 16-core Neural Engine, and a 200GB/s memory bandwidth.
