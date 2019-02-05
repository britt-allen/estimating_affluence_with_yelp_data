# <b>![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Estimating Neighborhood Affluence With Yelp Data </b>

## Project Authors
- Bernard Kurka | <u>[LinkedIn](https://www.linkedin.com/in/bernardkurka)</u> | <u>[Email](bkexcel2014@gmail.com)</u>
- Thomas Ludlow | <u>[LinkedIn](https://www.linkedin.com/in/thomas-w-ludlow-jr-4568a1b)</u> | <u>[Email](tludlow@gmail.com)</u>
- Brittany Allen | <u>[LinkedIn](https://www.linkedin.com/in/brittadjacent)</u> | <u>[Email](thebrittallen@gmail.com)</u>

## Presentation Slides
- [NLT Project: Estimating Neighborhood Affluence with Yelp Data](https://docs.google.com/presentation/d/1a9jGkPlWrMeTOaTl-t5zsYTpBsu5WPCP6Zb0eF-3wws/edit?usp=sharing)

## Table of Contents
Executive Summary | The Data | Analysis | Conclusion | Recommendations

### Executive Summary
[New Light Technologies (NLT)](https://www.linkedin.com/company/new-light-technologies) requested an economic analysis utilizing Yelp cost estimates ($, $$, $$$, $$$$) to estimate neighborhood affluence. In thinking about conventional sources of economic data like the U.S. Census Bureau or the IRS two major flaws we can identify are the reporting-lags in acquiring this data and the ability to slice and dice the data in varied ways. How can we develop a tool leveraging more agile data sources (like Yelp) that will help us measure local economic activity?

Our initial approach was to build a supervised learning, linear regression model, but we found that defining a proper affluence metric using IRS zipcode-level AGI (adjusted gross income) was difficult. While traditional methods typically estimate wealth of a locality based on demographic characteristics like income, the novelty of our new approach — conducting an unsupervised learning analysis with clustering algorithms — is in its use of big data related to commercial activity and cost of product and services as an indicator of affluence.

Counting the number of business in each price range ($, $$, $$$, $$$$) on Yelp´s result page, we were able to use a K Means Model to create four distinct clusters.
 - $ cluster: Limited activity across all price ranges $, $$, $$$, $$$$
 - $$ cluster: Moderate activity in $ and $$. Limited activity in $$$ and $$$$
 - $$$ cluster: Highest activity in $$. Increased activity in $$$ and $$$$
 - $$$$ cluster: Highest activity in $$$ and $$$$. High activity in $$

We developed a Python Class that, given a list of NYC zipcodes or neighborhood names, will access Yelp's Fusion API, gather data on this criteria, and use a K Means model to cluster the results. The Class also has functions to plot the queried zipcodes or neighborhoods into one of four clusters and plot the results on a map of NYC.

### The Data

|Feature|Type|Dataset|Description|
|---|---|---|---|
|**zip**|*object*|KMeans Clustering|String for each of 278 ZIP codes across New York City boroughs|
|**location**|*object*|KNN Classifier|String of ZIP code or text location query parameter being classified with KNN model|
|**pr_1s**|*float*|KMeans Clustering, KNN Classifier|Floating point standardized ("s") value for number of \$ priced ("pr_1") establishments in top 100 best match for search location|
|**pr_2ws**|*float*|KMeans Clustering, KNN Classifier|Floating point standardized ("s") value for number of \$\$ priced ("pr_2") establishments in top 100 best match for search location, weighted ("w") by price level|
|**pr_3ws**|*float*|KMeans Clustering, KNN Classifier|Floating point standardized ("s") value for number of \$\$\$ priced ("pr_3") establishments in top 100 best match for search location, weighted ("w") by price level|
|**pr_4ws**|*float*|KMeans Clustering, KNN Classifier|Floating point standardized ("s") value for number of \$\$\$\$ priced ("pr_4") establishments in top 100 best match for search location, weighted ("w") by price level|
|**pr_totws**|*float*|KMeans Clustering, KNN Classifier|Floating point standardized ("s") value for total weighted price counts ("tot", "w") for top 100 best match for search location|

Notebooks: [02 - Yelp Fusion API](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/02%20-%20Yelp%20Fusion%20API.ipynb),  [03 - IRS Data](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/03%20-%20IRS_data_collection_and_cleaning.ipynb),  [04 - API Pull for Training Data](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/04%20-%20API%20Pull%20for%20Training%20Data.ipynb)

Our data was acquired via Yelp's Fusion API `from yelpapi import YelpAPI`. We set our Yelp Fusion API Key and established an API connection, storing the data we gathered into a `Pandas` DataFrame. The search criteria included all categories (shops, restaurants, etc.), filtered on the "best match" option. We gathered the top 100 business prices and reviews from 278 NYC zipcodes.


### Analysis
Notebooks: [05 - Yelp Cluster Gridsearch](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/05%20-%20Yelp%20Cluster%20Grid%20Search%20-%20NYC.ipynb), [06 - K-Means Modeling](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/06%20-%20Yelp%20K-Means%20Model%20-%20NYC.ipynb)

We Gridsearched through three clustering models (K Means, Agglomerative, and Hierarchical) and changed many hyperparameters (n_clusters, inits, linkage_method, affinity...). We analyzed silhouette score, inertia and the number of observations in each cluster — K Means yielded best results. Using the Elbow method, n_clusters = 4 was found to be the best, balanced choice.

Our final K Means model used default parameters except for init='random' and random_state=42. Using this model, we developed a class for use by our client: _yelpaffluence_nyc.py_ class `YelpAffluence_NYC`. Methods for the `YelpAffluence_NYC` object include tools to fit the model to NYC data, query Yelp's Fusion API for a list of locations, plot results in a bar graph by price level, and plot results on a map of NYC.


### Conclusion
Notebooks: [01 - Yelp Affluence Demo](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/01%20-%20Yelp%20Affluence%20Demo%20190118.ipynb), [07 - Yelp Class Development](https://git.generalassemb.ly/brittallen/project_4_yelp_affluence/blob/master/notebooks/07%20-%20Yelp%20Class%20Development.ipynb)

After optimizing our K-Means model, we deployed the K-Nearest Neighbors Classifier (n=15) to assign a label to our queried locations. We found that the K-Means model sorted all NYC ZIP codes into 4 separate groups, which we have labeled "$", "$$", "$$$", and "$$$$" to match Yelp's existing pricing scale. We noted that locations in each group shared distinct characteristics, and that our class designation presents a meaningful economic description for a particular location.

- $ - Red
   - Limited economic activity across all price ranges $ - $$$$

- $$ - Orange
   - Moderate economic activity in the $ and $$ price levels
   - Limited activity in the $$$ and $$$$ price categories

- $$$ - Green
   - Highest activity in $$ price level
   - Increased activity in $$$ and $$$$, but not highest

- $$$$ - Blue
   - Highest activity of all locations in $$$ and $$$$ price establishments
   - High activity in $$ category

These trends are confirmed with inspection of the mean distribution of top 100 establishment price levels for each affluence level.

### Recommendations
- Feed other models with cluster results
   - This information can be useful as an economic rating variable in a predictive model

- Pay to use the Yelp Fusion VIP API
   - Will allow for commercial-scale queries

- Beware of ZIP query results
   - Yelp returned out of state results for some NYC-based zipcodes
   - e.g. 10015 returned results in Tucson, AZ

- Gather and test more data
   - Beyond the top 100, best match results

- Expanding class functionality
   - Enable collection of new training data
   - Automate model optimization

- Scale the model
   - Train on other large metropolitan areas and check consistency of results
