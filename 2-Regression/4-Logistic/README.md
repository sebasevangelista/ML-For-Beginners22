# Logistic regression to predict categories

[![Logistic vs. linear regression infographic](https://github.com/microsoft/ML-For-Beginners/raw/main/2-Regression/4-Logistic/images/linear-vs-logistic.png)](https://github.com/microsoft/ML-For-Beginners/blob/main/2-Regression/4-Logistic/images/linear-vs-logistic.png)

## [Pre-lecture quiz](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/15/)

## Introduction

In this final lesson on Regression, one of the basic _classic_ ML techniques, we will take a look at Logistic Regression. You would use this technique to discover patterns to predict binary categories. Is this candy chocolate or not? Is this disease contagious or not? Will this customer choose this product or not?

In this lesson, you will learn:

- A new library for data visualization
- Techniques for logistic regression

✅ Deepen your understanding of working with this type of regression in this [Learn module](https://docs.microsoft.com/learn/modules/train-evaluate-classification-models?WT.mc_id=academic-15963-cxa)

## Prerequisite

Having worked with the pumpkin data, we are now familiar enough with it to realize that there's one binary category that we can work with: `Color`.

Let's build a logistic regression model to predict that, given some variables, _what color a given pumpkin is likely to be_ (orange 🎃 or white 👻).

> Why are we talking about binary classification in a lesson grouping about regression? Only for linguistic convenience, as logistic regression is [really a classification method](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression), albeit a linear-based one. Learn about other ways to classify data in the next lesson group.

## Define the question

For our purposes, we will express this as a binary: 'White' or 'Not White'. There is also a 'striped' category in our dataset but there are few instances of it, so we will not use it. It disappears once we remove null values from the dataset, anyway.

> 🎃 Fun fact, we sometimes call white pumpkins 'ghost' pumpkins. They aren't very easy to carve, so they aren't as popular as the orange ones but they are cool looking! So we could also reformulate our question as: 'Ghost' or 'Not Ghost'. 👻

## About logistic regression

Logistic regression differs from linear regression, which you learned about previously, in a few important ways.

[![ML for beginners - Understanding Logistic Regression for Machine Learning Classification](https://camo.githubusercontent.com/d1625ee60dd3bd9c0e8f34ddde3b256710371e5d685f5b7054529fe4c3173fc2/68747470733a2f2f696d672e796f75747562652e636f6d2f76692f4b70654354366e457042592f302e6a7067)](https://youtu.be/KpeCT6nEpBY "ML for beginners - Understanding Logistic Regression for Machine Learning Classification")

> 🎥 Click the image above for a short video overview of logistic regression (3:04).

### Binary classification

Logistic regression does not offer the same features as linear regression. The former offers a predictionco about a binary category ("orange or not orange") whereas the latter is capable of predicting continual values, for example given the origin of a pumpkin and the time of harvest, _how much its price will rise_.

![Pumpkin classification Model](./images/pumpkin-classifier.png)

> Infographic by [Dasani Madipalli](https://twitter.com/dasani_decoded)

### Other classifications

There are other types of logistic regression, including multinomial and ordinal:

- **Multinomial**, which involves having more than one category - "Orange, White, and Striped".
- **Ordinal**, which involves ordered categories, useful if we wanted to order our outcomes logically, like our pumpkins that are ordered by a finite number of sizes (mini,sm,med,lg,xl,xxl).

[![Multinomial vs ordinal regression](https://github.com/microsoft/ML-For-Beginners/raw/main/2-Regression/4-Logistic/images/multinomial-vs-ordinal.png)](https://github.com/microsoft/ML-For-Beginners/blob/main/2-Regression/4-Logistic/images/multinomial-vs-ordinal.png)

### It's still linear

Even though this type of Regression is all about 'category predictions', it still works best when there is a clear linear relationship between the dependent variable (color) and the other independent variables (the rest of the dataset, like city name and size). It's good to get an idea of whether there is any linearity dividing these variables or not.

### Variables DO NOT have to correlate

Remember how linear regression worked better with more correlated variables? Logistic regression is the opposite - the variables don't have to align. That works for this data which has somewhat weak correlations.

### You need a lot of clean data

Logistic regression will give more accurate results if you use more data; our small dataset is not optimal for this task, so keep that in mind.

[![ML for beginners - Data Analysis and Preparation for Logistic Regression](https://camo.githubusercontent.com/87e5bfd20a74896c5c91fbf53ffd4a58372e92e15888d79c4c7fa9ce37d6e41b/68747470733a2f2f696d672e796f75747562652e636f6d2f76692f42325834483976635854732f302e6a7067)](https://youtu.be/B2X4H9vcXTs "ML for beginners - Data Analysis and Preparation for Logistic Regression")

> 🎥 Click the image above for a short video overview of preparing data for linear regression (5:10).

✅ Think about the types of data that would lend themselves well to logistic regression

## Exercise - tidy the data

First, clean the data a bit, dropping null values and selecting only some of the columns:

1. Add the following code:

   ```python
   columns_to_select = ['City Name','Package','Variety', 'Origin','Item Size', 'Color']
   pumpkins = full_pumpkins.loc[:, columns_to_select]

   pumpkins.dropna(inplace=True)
   ```

   You can always take a peek at your new dataframe:

   ```python
   new_pumpkins.info
   ```

### Visualization - side-by-side grid

By now you have loaded up the [starter notebook](./notebook.ipynb) with pumpkin data once again and cleaned it so as to preserve a dataset containing a few variables, including `Color`. Let's visualize the dataframe in the notebook using a different library: [Seaborn](https://seaborn.pydata.org/index.html), which is built on Matplotlib which we used earlier.

Seaborn offers some neat ways to visualize your data. For example, you can compare distributions of the data for each `Variety` and `Color` in a categorical plot.

1. Create such a plot by using the `catplot` function, using our pumpkin data `pumpkins`, and specifying a color mapping for each pumpkin category (orange or white):

   ```python
   import seaborn as sns

   palette = {
   'ORANGE': 'orange',
   'WHITE': 'wheat',
   }

   sns.catplot(
   data=pumpkins, y="Variety", hue="Color", kind="count",
   palette=palette, 
   )
   ```

   [![A grid of visualized data](https://github.com/microsoft/ML-For-Beginners/raw/main/2-Regression/4-Logistic/images/pumpkins_catplot_1.png)](https://github.com/microsoft/ML-For-Beginners/blob/main/2-Regression/4-Logistic/images/pumpkins_catplot_1.png)
   By observing the data, you can see how the Color data relates to Variety.
   ✅ Given this categorical plot, what are some interesting explorations you can envision?

### Analyse relationships between variables

Now that we have pre-processed our data, we can analyse the relationships between the features and the label to grasp an idea of how well the model will be able to predict the label given the features. The best way to perform this kind of analysis is plotting the data. We'll be using again the Seaborn `catplot` function, to visualize the relationships between `Item Size`, `Variety` and `Color` in a categorical plot. To better plot the data we'll be using the encoded `Item Size` column and the unencoded `Variety` column.

```python
    palette = {
    'ORANGE': 'orange',
    'WHITE': 'wheat',
    }
    pumpkins['Item Size'] = encoded_pumpkins['ord__Item Size']

    g = sns.catplot(
        data=pumpkins,
        x="Item Size", y="Color", row='Variety',
        kind="box", orient="h",
        sharex=False, margin_titles=True,
        height=1.8, aspect=4, palette=palette,
    )
    g.set(xlabel="Item Size", ylabel="").set(xlim=(0,6))
    g.set_titles(row_template="{row_name}")
```

[![A catplot of visualized data](https://github.com/microsoft/ML-For-Beginners/raw/main/2-Regression/4-Logistic/images/pumpkins_catplot_2.png)](https://github.com/microsoft/ML-For-Beginners/blob/main/2-Regression/4-Logistic/images/pumpkins_catplot_2.png)

### Use a swarm plot

Since Color is a binary category (Whute or Not), it's called 'categorical data' and needs 'a more [specialized approach](https://seaborn.pydata.org/tutorial/categorical.html?highlight=bar) to visualization'. There are other ways to visualize the relationship of this category with other variables.

You can visualize variables side-by-side with Seaborn plots. Try a 'swarm' plot to show the distribution of values:

palette = {
0: 'orange',
1: 'wheat'
}
sns.swarmplot(x="Color", y="ord__Item Size", data=encoded_pumpkins, palette=palette)
A swarm of visualized data

[![A swarm of visualized data](https://github.com/microsoft/ML-For-Beginners/raw/main/2-Regression/4-Logistic/images/swarm_2.png)](https://github.com/microsoft/ML-For-Beginners/blob/main/2-Regression/4-Logistic/images/swarm_2.png)

**Watch Out** : the code above might generate a warning, since seaborn fails to represent such amount of datapoints into a swam plot. A possible solution is decreasing the size of the marker, by using the 'size' parameter. However, be aware that this affects the readability of the plot.

### Violin plot (from 2022)

A 'violin' type plot is useful as you can easily visualize the way that data in the two categories is distributed. Violin plots don't work so well with smaller datasets as the distribution is displayed more 'smoothly'.

1. As parameters `x=Color`, `kind="violin"` and call `catplot()`:

   ```python
   sns.catplot(x="Color", y="Item Size",
               kind="violin", data=new_pumpkins)
   ```

   ![a violin type chart](images/violin.png)

   ✅ Try creating this plot, and other Seaborn plots, using other variables.

Now that we have an idea of the relationship between the binary categories of color and the larger group of sizes, let's explore logistic regression to determine a given pumpkin's likely color.

> **🧮 Show Me The Math**
>
> Remember how linear regression often used ordinary least squares to arrive at a value? Logistic regression relies on the concept of 'maximum likelihood' using [sigmoid functions](https://wikipedia.org/wiki/Sigmoid_function). A 'Sigmoid Function' on a plot looks like an 'S' shape. It takes a value and maps it to somewhere between 0 and 1. Its curve is also called a 'logistic curve'. Its formula looks like this:
>
> ![logistic function](images/sigmoid.png)
>
> where the sigmoid's midpoint finds itself at x's 0 point, L is the curve's maximum value, and k is the curve's steepness. If the outcome of the function is more than 0.5, the label in question will be given the class '1' of the binary choice. If not, it will be classified as '0'.

## Build your model

Building a model to find these binary classification is surprisingly straightforward in Scikit-learn.

[![ML for beginners - Logistic Regression for classification of data](https://camo.githubusercontent.com/ead402f873baa35865b9134e93fa978791b7268f4c55f82cf6d77b9341f5a45d/68747470733a2f2f696d672e796f75747562652e636f6d2f76692f4d6d5a53326f74507251382f302e6a7067)](https://youtu.be/MmZS2otPrQ8 "ML for beginners - Logistic Regression for classification of data")

> 🎥 Click the image above for a short video overview of building a linear regression model (4:49)

1. Select the variables you want to use in your classification model and split the training and test sets calling `train_test_split()`:

   ```python
   from sklearn.model_selection import train_test_split

   X = encoded_pumpkins[encoded_pumpkins.columns.difference(['Color'])]
   y = encoded_pumpkins['Color']

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)

   ```
2. Now you can train your model, by calling `fit()` with your training data, and print out its result:

   ```python
   from sklearn.metrics import f1_score, classification_report 
   from sklearn.linear_model import LogisticRegression

   model = LogisticRegression()
   model.fit(X_train, y_train)
   predictions = model.predict(X_test)

   print(classification_report(y_test, predictions))
   print('Predicted labels: ', predictions)
   print('F1-score: ', f1_score(y_test, predictions))
   ```

   Take a look at your model's scoreboard. It's not bad, considering you have only about 1000 rows of data:

   ```output
                      precision    recall  f1-score   support

              0       0.85      0.95      0.90       166
              1       0.38      0.15      0.22        33

       accuracy                           0.82       199
      macro avg       0.62      0.55      0.56       199
   weighted avg       0.77      0.82      0.78       199

   Predicted labels:  [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0
    0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
    1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 1
    0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1
    0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
    0 0 0 1 0 1 0 0 1 0 0 0 1 0]
   ```

## Better comprehension via a confusion matrix

While you can get a scoreboard report [terms](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.classification_report.html?highlight=classification_report#sklearn.metrics.classification_report) by printing out the items above, you might be able to understand your model more easily by using a [confusion matrix](https://scikit-learn.org/stable/modules/model_evaluation.html#confusion-matrix) to help us understand how the model is performing.

> 🎓 A '[confusion matrix](https://wikipedia.org/wiki/Confusion_matrix)' (or 'error matrix') is a table that expresses your model's true vs. false positives and negatives, thus gauging the accuracy of predictions.

1. To use a confusion metrics, call `confusion_matrix()`:

   ```python
   from sklearn.metrics import confusion_matrix
   confusion_matrix(y_test, predictions)
   ```

   Take a look at your model's confusion matrix:

   ```output
   array([[162,   4],
          [ 11,  22]])
   ```

In Scikit-learn, confusion matrices Rows (axis 0) are actual labels and columns (axis 1) are predicted labels.

|  | 0 | 1 |
| :-: | :-: | :-: |
| 0 | TN | FP |
| 1 | FN | TP |

What's going on here? Let's say our model is asked to classify pumpkins between two binary categories, category 'orange' and category 'not-orange'.

- If your model predicts a pumpkin as not orange and it belongs to category 'not-orange' in reality we call it a true negative, shown by the top left number.
- If your model predicts a pumpkin as orange and it belongs to category 'not-orange' in reality we call it a false negative, shown by the bottom left number.
- If your model predicts a pumpkin as not orange and it belongs to category 'orange' in reality we call it a false positive, shown by the top right number.
- If your model predicts a pumpkin as orange and it belongs to category 'orange' in reality we call it a true positive, shown by the bottom right number.

As you might have guessed it's preferable to have a larger number of true positives and true negatives and a lower number of false positives and false negatives, which implies that the model performs better.

How does the confusion matrix relate to precision and recall? Remember, the classification report printed above showed precision (0.85) and recall (0.67).

Precision = tp / (tp + fp) = 162 / (162 + 33) = 0.8461538461538461

Recall = tp / (tp + fn) = 162 / (162 + 4) = 0.6666666666666666

✅ Q: According to the confusion matrix, how did the model do? A: Not too bad; there are a good number of true negatives but also several false negatives.

Let's revisit the terms we saw earlier with the help of the confusion matrix's mapping of TP/TN and FP/FN:

🎓 Precision: TP/(TP + FP) The fraction of relevant instances among the retrieved instances (e.g. which labels were well-labeled)

🎓 Recall: TP/(TP + FN) The fraction of relevant instances that were retrieved, whether well-labeled or not

🎓 f1-score: (2 * precision * recall)/(precision + recall) A weighted average of the precision and recall, with best being 1 and worst being 0

🎓 Support: The number of occurrences of each label retrieved

🎓 Accuracy: (TP + TN)/(TP + TN + FP + FN) The percentage of labels predicted accurately for a sample.

🎓 Macro Avg: The calculation of the unweighted mean metrics for each label, not taking label imbalance into account.

🎓 Weighted Avg: The calculation of the mean metrics for each label, taking label imbalance into account by weighting them by their support (the number of true instances for each label).

✅ Can you think which metric you should watch if you want your model to reduce the number of false negatives?

## Visualize the ROC curve of this model

Let's do one more visualization to see the so-called 'ROC' score:

[![ML for beginners - Analyzing Logistic Regression Performance with ROC Curves](https://camo.githubusercontent.com/c7bddc0ef6d0bc192986dbb706430e98346e430a6194e766ba54dc68de9c57b2/68747470733a2f2f696d672e796f75747562652e636f6d2f76692f4741704f3537356a5441302f302e6a7067)](https://youtu.be/GApO575jTA0 "ML for beginners - Analyzing Logistic Regression Performance with ROC Curves")

> 🎥 Click the image above for a short video overview of ROC curves (4:36)

```python
from sklearn.metrics import roc_curve, roc_auc_score

y_scores = model.predict_proba(X_test)
# calculate ROC curve
fpr, tpr, thresholds = roc_curve(y_test, y_scores[:,1])
sns.lineplot([0, 1], [0, 1])
sns.lineplot(fpr, tpr)
```

Using Seaborn again, plot the model's [Receiving Operating Characteristic](https://scikit-learn.org/stable/auto_examples/model_selection/plot_roc.html?highlight=roc) or ROC. ROC curves are often used to get a view of the output of a classifier in terms of its true vs. false positives. "ROC curves typically feature true positive rate on the Y axis, and false positive rate on the X axis." Thus, the steepness of the curve and the space between the midpoint line and the curve matter: you want a curve that quickly heads up and over the line. In our case, there are false positives to start with, and then the line heads up and over properly:

![ROC](./images/ROC.png)

Finally, use Scikit-learn's [`roc_auc_score` API](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html?highlight=roc_auc#sklearn.metrics.roc_auc_score) to compute the actual 'Area Under the Curve' (AUC):

```python
auc = roc_auc_score(y_test,y_scores[:,1])
print(auc)
```

The result is 0.9749908725812341. Given that the AUC ranges from 0 to 1, you want a big score, since a model that is 100% correct in its predictions will have an AUC of 1; in this case, the model is _pretty good_.

In future lessons on classifications, you will learn how to iterate to improve your model's scores. But for now, congratulations! You've completed these regression lessons!

---

## 🚀Challenge

There's a lot more to unpack regarding logistic regression! But the best way to learn is to experiment. Find a dataset that lends itself to this type of analysis and build a model with it. What do you learn? tip: try [Kaggle](https://www.kaggle.com/search?q=logistic+regression+datasets) for interesting datasets.

## [Post-lecture quiz](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/16/)

## Review & Self Study

Read the first few pages of [this paper from Stanford](https://web.stanford.edu/~jurafsky/slp3/5.pdf) on some practical uses for logistic regression. Think about tasks that are better suited for one or the other type of regression tasks that we have studied up to this point. What would work best?

## [x] Assignment

[Retrying this regression](assignment.md)
