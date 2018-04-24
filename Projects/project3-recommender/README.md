# Instacart Clustering & Recommendation Project
This project explores a dataset provided by [Instacart](www.instacart.com), an online grocery delivery service.  Instacart provides data for 3 million online orders and is available for non-commerical use.  The "Instacart Online Grocery Shopping Dataset 2017" is accessible from https://www.instacart.com/datasets/grocery-shopping-2017.  It was accessed December 2017.  

For the purpose of this analysis, we will utilize the Instacart dataset to explore clustering and recommenders.  Specifically, we want to answer:
1. Can we segment Instacart customers based on products they have purchased?
2. Can we build a recommender to recommend new products to customers based on their prior purchases?

# 1. Exploratory Data Analysis
Before diving into modeling the data, we took a closer look to understand trends within the data.  [See full Exploratory Data Analysis notebook here.](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/notebooks/01-instacart-eda.ipynb)


The dataset is split in several key data files:

* Orders
* Products
* Departments
* Aisles

## Orders
We have three files that are used to describe customer purchases: **orders, order_products_train & orders_products_prior**.  

The **orders** file is an order summary.  The file contains values such as order id, user id, the day of week the order was placed, and the number of days since the customer's last order.  

The order details (meaning the products purchased in each order) are contained in the files **order_products_train** & **order_products_prior**.  The most recent order is placed into the 'train' dataset, whereas remaining historical orders are placed into the 'prior' dataset.  This allows for easy data separation into train & test datasets should you ever want to create a ML model.  

With this data, we can begin to answer questions around customer purchasing habits.

#### When do customers shop?
Primarily, customers shop between the hours of 08:00 and 18:00.  

![shopping hours](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/orders_per_hour.png)

The most popular days to shop are 0 and 1.  Instacart does not provide details around the meaning of these values, but my assumption is that 0 & 1 correspond to Saturday and/or Sunday.  Mid-week values (3 & 4) represent the lower frequency of purchases.  

![shopping days](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/orders_per_day.png)

We can also discover trends around order frequency by looking at the number of days since last order for each customer.  We can see large spikes around weekly and monthly orders (identified by 7.0 and 30.0).  There is also notable trends around re-order points of 14, 21 and 28.

![reorder frequency](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/days_since_last_order.png)

Finally, we can see that on average, customers purchase 10 products per order.  The distribution of products is heavily skewed in both the train and prior datasets (see seen in the graph below).  The peak products within each dataset hovers around 5 products per order.

![products per order](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/items_per_order_prior.png)

Shopping trends such as the above can be used to market new products and potential savings to customers around reorder times in hopes to increase basket size.


## Products
The products file contains product names, as well the aisle and department for which each product can be found.

Looking at the most popular products, we find that customers are most likely to purchase fruits (such as bananas, strawberries, and spinach).  We also notice that there is a large popularity in organic items.  Instacart is based within California, which most likely explains this focus.  

![popular products](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/most_popular_items.png)


Instacart also flags the order in which items are added to a shopping cart.  Looking at these statistics, we can identify the products that are most frequently added to a cart first.  Items which are added to a cart first are very focused purchases - the customer knows what they want & need, and therefore add these items before moving onto additional shopping.

![Most frequently added first](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/Added_To_Cart_First.png)

## Departments
The products offered by Instacart are grouped into 21 departments.  As one would expect based on the most popular products purchased, the most popular department is produce, followed by dairy / eggs, and snacks.

![departments](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/Items_Bought_by_Dept.png)

## Aisles
Each department is divided into aisles, helping Instacart further group products.  

We can visualize products purchased by department and aisle using pie charts - for sake of brevity, we'll look at only the top 6 departments (based on volume of purchases)

![departments aisles](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/departments_aisles.png)

We can also visual purchases by looking at the 10 most popular aisles.  As we can see from the graph below, fruits and vegetables lead purchases, followed by yogurt and packaged cheese.

![departments aisles](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/reports/figures/Items_Bought_by_Aisle_by_Dept.png)

# 2. Clustering
With EDA complete, we then move to investigating clustering to create customer segmentation.  

For the sake of brevity, I will only touch on results of our clustering exercise.  For full details on each of the clustering algorithms (including performance metrics on the resulting clusters), please see the [clustering notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/notebooks/02-instacart-clustering-aisles.ipynb).  

### Approach
The original idea for approaching clustering was to create a purchase summary matrix, with columns representing each product available for purchase and rows representing each customer.  Clustering could then be performed on the resulting matrix to create customer segmentation.

The problem here was memory usage.  With access to over 200,000 customers and ~40,000 unique products, a matrix of this size could not be processed by a MacBook Pro.

An alternate approach was to scale down the data.  This was achieved by aggregating products by aisle (134 unique aisles) and use a smaller customer sample (15,000 customers).  A purchase summary matrix was created on this scaled down data creating a much more manageable dataset.

### Clustering Algorithms
Three clustering algorithms were examined in this exercise:
1. KMeans
2. DBScan
3. Agglomerative Clustering

We aimed to create 4 clusters for each algorithm.  The purchase distribution within each of the 4 clusters were examined by aisle in order to provide a breakdown of customer spend habits.  

For example, cluster 3 from our KMeans algorithm discovered customers who primarily purchase products from the fresh fruits and energy / granola bars aisles.  The top 12 aisles from within this cluster are as follows:

| Aisle | Purchase Dist. |
| --- | --- |
| fresh fruits | 12% |
| energy granola bars | 11% |
| yogurt | 7% |
| fresh vegetables | 5% |
| water seltzer sparkling water | 5% |
| packaged vegetables fruits | 4% |
| refrigerated | 3% |
| chips pretzels | 3% |
| packaged cheese | 3% |
| milk | 2.5% |
| soy lactosefree | 2% |
| tea | 1.9% |

### Results
It was found that DBSCAN performed the worst, failing to create well defined and balanced clusters for our data.

KMeans and Agglomerative Clustering performed well. Both algorithms created 4 clusters that similarly described customer purchase patterns. These clusters were as follows:

| Cluster | Description |
| --- | --- |
| 0 | Characterized by customers who purchase primarily fresh vegetables & fresh fruits, followed by packaged vegetables/fruits and yogurt. |
| 1 | Characterized primarily by customers purchasing baby food formula, fresh fruits and fresh vegetables. They are also likely to buy packaged vegetables/fruits, yogurt, packaged cheese and milk products.|
| 2 | Very similar to cluster 0. They primarily buy fresh fruits & fresh vegetables, but these purchases represent a lesser percentage of their overall purchases. This could be because they buy a larger variety of products than customers in cluster 0.  We see a larger difference between cluster 2 and 0 as we dig deeper down the list of top aisles.  Cluster 2 customers are more likely to buy milk & chip/pretzels than sparkling water.  They are also more likely to buy crackers over frozen produce.
| 3 | Characterized by customers who purchase fresh fruits and energy granola bars.  They are also likely to buy yogurt & fresh vegetables. |

Agglomerative clustering appears to have created better clustering results based on the distribution of purchases.  

An example of this can be viewed by examining the cluster specific to customers purchasing baby food formula.  Below are the top 4 aisles per model (ranked based on average purchase percentage within each cluster):

| Agglomerative Clustering Top Aisles | Purchase Dist. |
| --- | --- |
| fresh fruits | 11% |
| baby food formula | 11% |
| fresh vegetables | 9% |
| packaged vegetables fruits | 5% |

| KMeans Top Aisles | Purchase Dist. |
| --- | --- |
| baby food formula | 14% |
| fresh fruits | 11% |
| fresh vegetables | 9% |
| packaged vegetables fruits | 5% |

KMeans clustering represents baby food formula as a higher purchase likelihood than fresh fruits. Baby food formula is 5% more likely to be purchased than fresh vegetables. This distribution creates a tighter cluster of related customers, but fails to capture customers that are not primarily buying baby food formula over other items such as fresh fruit or fresh vegetables.

This tighter cluster distribution is further supported by the larger population size of the agglomerative cluster (pop size: 320) versus the KMeans cluster (pop size: 130).  Although tighter clustering is desirable in certain cases, it this specific case we would find that customers who purchase baby food formula at lower volumes are placed into neighbouring clusters that do not see baby food as important identifier of the population.  

### Conclusions
This clustering exercise demonstrated how similarities between customers can be identified simply based on their shopping habits.  Clustering is an important tool for customer segmentation.  It allows marketing and sales teams to group customers based on similar spend habits, allowing for targeted sales to increase engagement.  Also, it allows you to make assumptions on new customers, placing them into established clusters even though you may not have the same depth of purchase history as your established customers.

# 3. Recommenders
With clustering investigation complete, we move our efforts to building a recommender.  

For brevity, I will not go into detail on the development of these approaches, but rather focus on results.  Full details can be found in the [recommender notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/project3-recommender/notebooks/03-instacart-recommender-final.ipynb).  

There are many ways that recommenders can be used within products today.  For this project, we'd like to get to a point where given a customer id, the recommender should provide a list of new products that the customer may be interested in purchasing.

We explored 3 recommendation approaches:

1. Global Recommendations
2. Association Rules
3. Collaborative Filtering

## Global Recommendations
### Approach
Global recommendations are very generic. We look to answer the question: What top 2 products were purchased with item A? (Where item A is any product available in the product catalog).

Global recommendations are derived from a product contingency matrix. A product contingency matrix identifies how many times a product pairing was purchased. For example, how many times were bananas purchased with avocados?  

We have over 3,000,000 transactions within the Instacart dataset - a great wealth of data for which my tiny MacBook cannot handle.  We reduced the number of users in the dataset to 1,500 in order to decrease the number of overall transactions to ~220,000.  From these transactions, a contingency matrix was created for which recommendations could be generated.  

### Results
With a product contingency matrix built, we can then make predictions for products.  Here is an example of the output:
```
Recommendations based on global products contingency matrix

Top 2 recommended items to buy with Banana are:
['Organic Avocado', 'Organic Strawberries']

Top 2 recommended items to buy with Bag of Organic Bananas are:
['Organic Strawberries', 'Organic Hass Avocado']

Top 2 recommended items to buy with Organic Strawberries are:
['Bag of Organic Bananas', 'Banana']

Top 2 recommended items to buy with Organic Baby Spinach are:
['Bag of Organic Bananas', 'Banana']

Top 2 recommended items to buy with Organic Hass Avocado are:
['Bag of Organic Bananas', 'Organic Strawberries']
```
The problem with this method is that these recommendations are not customer specific (hence the global identifier). Recommendations are basically a popularity contest whereby the most popular product pairing wins. It also does not determine whether purchasing item A results in a higher likelihood that item B is also purchased.  This leads us into Assocation Rules.

## Association Rules
### Approach
Association rules provide us with a simple equation **A -> B**, where the right hand side of the equation (consequents) represents products a customer is likely to purchase as a result of having the items on the left hand side (antecedants) already in their basket.

Grocery stores utilize association rules to know which products they should place together in order to foster additional purchases. We can also use association rules to recommend products based on what products have currently been added to a shopping cart.  

Association rules are measured based on **Support**, **Confidence** and **Lift**.  For the purpose of these explanations, let's assume an association rules is represented by A->B

**Support** Defines the popularity of an itemset. It is calculated by dividing the number transactions in which an itemset appears by the total number of transactions.

**Confidence** Used to identify how likely item B is purchased when item A is purchased. It is calculated by dividing the number of transactions that contain item A & B, by the total number of transactions that contain item A.

**Lift** Indicates whether the presence of item A is responsible for the increase in probability that the customer will also buy item B.

* If lift > 1, it indicates that item A is responsible for the increase in probability
* If lift == 0, it indicates independence between item A & B
* If lift < 1, it indicates that item B is less likely to occur as a result of item A occurring.

### Results
With association rules created, we can query the rules by filtering antecedents for a particular product.  It must be noted that the dataset was drastically reduced due to memory constraints.  As a result, we must assume that these rules are only a subset of the collection.   For example:

**Recommendations for 100% Whole Wheat Bread:**

| antecedants | consequents | support | confidence | lift |
|---|---|---|---|---|
| (100% Whole Wheat Bread) | (Limes) | 0.015667 | 0.135977 | 2.945871 |
| (100% Whole Wheat Bread) | (Organic Hass Avocado) | 0.015667 | 0.150142 | 2.320193 |
| (100% Whole Wheat Bread) | (Organic Strawberries) | 0.015667 | 0.169972 | 2.047931 |
| (100% Whole Wheat Bread) | (Banana) | 0.015667 | 0.263456 | 2.039838 |
| (100% Whole Wheat Bread) | (Bag of Organic Bananas) | 0.015667 | 0.161473 | 1.367212 |

After purchasing **100% whole wheat bread**, a customer should be recommended items such as limes, avocados, strawberries, and bananas. The lift values for each of these items is above 1, indicating that purchasing whole wheat bread increases the probability of a customer purchasing any of suggested items.

Examining the confidence values, we do need to be aware that there is a low confidence that our suggested items will actually be purchased. Bananas have the highest confidence, with a probability of 26% of being purchased with whole wheat bread.

These recommendations can be tuned to represent a particular support, confidence or lift threshold; However we still have the problem that the recommendations are not personalized for each individual customer.

In order to get this personalization, we can look at collaborative filtering.

## Collaborative Filtering
### Approach
Collaborative Filtering is used to build personalized recommendation engines, where the recommendations are generated using the preferences of other users that are like you. Data used for Collaborative Filtering can be classified as explicit or implicit:

**Explicit** data describes situations where we have item ratings

* I rate the movie 'Guardians of the Galaxy' 5 out of 5
* I rate this hard drive purchased on Amazon 1 out of 5

**Implicit** data describes situations where no ratings are available, and rather, we gather data based on user behaviour. There are loads of ways to gather this information:

 * Time spent watching a tv show
 * Time spent reading an article
 * Number of times a tv episode is watched
 * Number of times a song is played

Instacart's data provides us with a snapshot of purchases made - this is implicit data. We don't know how well a user likes a product, but we know how many times that user has purchased a product. Based on these numbers, we can draw conclusions to say what products I should recommend to a customer based on their prior purchase history.  

The recommender is created using the [Ben Frederickson's implicit library](https://github.com/benfred/implicit).  The library utilizes an Alternating Least Square algorithm to generate recommendations.  

A hard issue to tackle in collaborative filtering is how do we evaluate our recommender?  

One way to approach evaluation is by masking purchases from users in a training dataset (making it seem like a user has never purchased a particular product). We then build a model using our training dataset and predict the top 10 recommendations for users with masked purchases.  If our recommender is good, we should be able to recommend products that were masked.  We chose to mask purchases from 10 users prior to building the recommender.  

As a recurring trend throughout this project, due to memory constraints, the dataset had to be reduced.  The data was reduced to 150,000 customers for this recommender.

### Results
Our recommender based on collaborative filtering was able to predict the missing product in 30% of the cases - pretty cool - but is this good? probably not? I'm not sure to be honest.  I assume the more data we are able to process, the better the recommendations will be.  For the given circumstances, I'm happy with the results and learning experience.  

Here is a quick example of the test results where we correctly predicted a missing product:

```
Recommendations for User 143334

|Product Removed|

46061 Popcorn 8.0

|Product Recommendations|

16797 Strawberries 1.0230441130003425
13259 Organic Variety Pack 0.9593022040858676
21195 Organic Extra Virgin Olive Oil 0.9315419882134035
38768 Sweet Kale Salad Mix 0.9112196621120128
46061 Popcorn 0.9063102941144172
21137 Organic Strawberries 0.8972279376579592
11759 Organic Simply Naked Pita Chips 0.8641661402911641
38928 0% Greek Strained Yogurt 0.8625465171691858
38300 Tall Kitchen Bag With Febreze Odor Shield 0.8430867199042493
21903 Organic Baby Spinach 0.8426617967535306
```
And here is an example where we did not predict the missing product:

```
Recommendations for User 58666

|Product Removed|
26209 Limes 11.0

|Product Recommendations|
27344 Uncured Genoa Salami 1.9134894976180328
25824 Pita Chips Simply Naked 1.4443914136008196
30720 Sugar Snap Peas 1.3809967449615184
29926 Roasted & Salted Almonds 1.373338875521379
12797 Organic Whole Peeled Tomatoes 1.3686293130633247
35510 Organic Yukon Gold Potato 1.3614336284071307
27323 Pure & Natural Sour Cream 1.3229185097411245
47601 Unsalted Cultured Butter 1.315264733448014
1244 Organic Orange Bell Pepper 1.2718674870721327
34243 Organic Baby Broccoli 1.2523048235726506
```

### Conclusions
Global recommendations give us insight into the most popular product pairings. They are not useful for providing similar product recommendations or personalized product recommendations.

Association rules provide us with insight into how often product itemsets are purchased together, as well as the influence that an itemset has on purchasing related itemsets. Association rules are extremely expensive to create and still suffer from being able to produce personalized product recommendations.

Finally, collaborative filtering offers us with the best of both worlds - it allows us to produce similar product recommendations and personalized product recommendations. It also allows us to utilize a large dataset to create a model in a fairly quick amount of time. It is the best option for recommendations that we've explored in this notebook.

Why are recommenders such as the one we created using collaborative filtering important to companies like Instacart? It allows users to discover more products that they could truly be interested in, but would never find on their own. This increases sales and improves the customer experience. As customers begin to trust the recommendations, they are more likely to discover additional items and increase basket size.

## Final Thoughts
Overall, this project gave me great insight into how clustering and recommender algorithms function.  Both are important tools for analyzing how customers interact with products and allow companies to fine tune the customer experience.

Looking forward, I'd like to investigate processing larger amounts of data for projects like these, as my assumption is that results in both clustering and recommendations would improve.
