# Identify Customer Segments

This study applyies unsupervised learning techniques to identify segments of the population that form the core customer base for a mail-order sales company in Germany. The information and parties involved in this study are based on factual demographic data on the individual and local scale.
We will look at relationships between demographics features, organize the population into clusters, and see how prevalent customers are in each of the segments obtained. Feel free to explore the assumptions and python code in more details here: [Identify_Customer_Segments.ipynb](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/Identify_Customer_Segments.ipynb)


## The Dataset

The datasets used here are provided coutesy of Bertelsmann partners AZ Direct and Arvato Financial Solutions. 
One dataset is with demographic information about the people of Germany, and another one with the same information for customers of a mail-order sales company. A third file explains the meanings of personal and extrapersonal features with varying degrees of depth.

Unfourtunately the datasets are proprietary of AZ Direct GmbH and can't be shared on this repository.
Users interested in using the datasets should contact AZ Direct, or enroll in Udacity's Data Sciecne Nanodegree for an opporunity to work with it.

That being said, the methods used here can be applied to any unsupervised learning project.
You can also read observations in the notebook to get a sense of what is in the dataset ;).

## Libraries

The project is written on an IPython Notebook. The libraries listed below will be necessary to run the script, most of them should be preinstalled if you have [Anaconda](https://www.anaconda.com/).

- numpy
- pandas
- matplotlib.pyplot
- seaborn
- re
- sklearn 
- tqdm
- pickle
- scipy


## Cleaning The Data


The first step includes significant data cleaning. The figure below shows a heatplot of missing values (NaN) in the dataset. Missing values are shown in white.

![nan_heatmap](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/nan_heat.png)

We seek to maintain the maximum number of features and observations possible.
For this reason we decide to drop features that have a signficantly higher number of missing values. For the features we end up dropping 6 features, the least number of missing observations being 40%. 

For the rows, say we find that on average each row has ~10% missing values, we decide to drop all rows that are 3 standard deviations away from the mean. We end up with a 'clean' dataset and what is left out is put in an 'empty' dataset. To see how the distribution varies in the features between the 'clean' dataset and the 'empty' one, we perform Smirnoff Ice... Um... I mean a [Kolmogorov–Smirnov](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test) test to see if the distribution of observations for a single feature vary significantly between the clean dataset and the empty one. The results show that for a p-value of 0.05 the distribution is significantly different for around half the features. The observation is something to keep in mind for this kind of study where a lot of observatons are missing.

![smirnov](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/smirnov.png)


## Feature Reduction

Using sklearn's PCA package, we can obtain a low dimensional representation of our feature space. However before doing so we must clean our data of missing values for sklearn libraries to work properly. We decide to use the SimpleImputer and fill all missing values with the median observation for that feature. Keep in mind that how we treat missing values might significantly skew our analysis towards some conclusions. This step should be treated with caution. Furthermore, we scale each feature using the MinMaxScaler from sklearn.
The plot below shows the cumulative explained variance after performing PCA. We move with the analysis using this new feature space. Consequently, we can capture 95% of the variance by keeping half of the features.

![pca_cumsum](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/pca_cumsum_pre.png)

## Clustering

We opt to use KMeans to determine the number of clusters in our dataset.
We must take into account time complexity due to the scale of the data.
KMeans time compelxity it is O(tkn) where:
- n is the number of objects
- k is the number of clusters
- t is how many iterations it takes to converge.

Using MiniBatchKMeans we attempt to deduce the number of clusters for an increasing batch size.
The plot below shows a decreasing sum of squared distnaces (SSD) as the number of clusters increases.
Notice how the overall shape of the curve is not severly affected by the number of batches, albeit some noise is present. 
Visually, we can determine a point of decreasing returns and set the number of clusters to 18 for the full KMeans algorithm.
![minikmeans](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/elbow_minik.png)

## Visualizing Clusters

What do our clusters look like? To answe this question we reduce our feature space to a two dimesional space and obtain the figure below.

![pca_space](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/pca_space.png)


## General Population Vs Clients

After clustering the general population, we apply the same transformation to the customers dataset. Moreover preprocessing (imputing & scaling), PCA, and KMeans transforms are based on the fit for the general population.

For the general population we see what percentage fall in each of the clusters, and repeat the same exercise for the customers.
The plot below shows the difference in percentage distribution between the two groups. For example if 15% of the general population falls in cluster C, and 20% of the customers fall in cluster C, then we report 5% difference.
We can see that some groups are over represented comapred to others. The Highly represented groups correspond to the target customers who might be popular with the company, those not represented might not be so popular.

![freq_diff](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/freq_prct_diff.png)

## Customers Segments

The plot below shows the features of potential customers.

![top_cluster.png](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/top_cluster.png)

The second plot below shows features of individuals who will probably dislike the company product:

![bot_cluster.png](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/bot_cluster.png)

The overrepresented cluster correponds to customers who might be popular with the company's product. These appear to be people who rank high in the era category and thus of the younger crowd. In addition they rank high in the FINANZ_ANLEGER (rough translation: finance investor). Thus this is a crowd that has not spent time thnking in investments. In addition from WOHNDAUER_2008 they have been residing where they are for a while now. Furthermore, the positive weight ONLINE_AFFINITAET shows that customers tend to have a strong internet activity, which is usually expected off people born in the 90s and after. 

For customers not popular with the service, they identify as average earners per the LP_STATUS_GROB feature. In addition, the NATIONALITAET_KZ feature attempts to infer German nationality based on given names. It appears that the service is not popular with people who have 'foreign souding' or 'assimialted' names, that is to say immigrants or descendants of immigrants. We are not sure what SEMIO_ERL means so we skip to LP_STATUS_FEIN, an indicator of social status. It seems that the higher the social class the less likely they are to become customers. 

It is interesting to see gender appear as a positive influence in all categories. Overall, we can infer that the service will be popular with German nationals who are rather young and are freqent internet users. This population can be best reached via online media campaigns. On the other hand, the service is not popular with foreigners and individuals of middle income and higher.

## Conclusion

In conclusion, we managed to obtain an idea of potential target customers for the company. We must also keep in mind the ethical implications of our assumptions during the wrangling and cleaning of the data, and the bias we are imputing into any model that we are inputing in our model. Generalizations and assumptions were made and clearly spelled out in the full report, keep in mind that machines can only 'learn' from the data they are provided.

There are other methods of clustering such as Gaussian Mixture Models (GMM). GMM are slower since they have to incoporate information about the distribution of the data: it has to deal with the covariance, mean, variance, and prior probabilities of the data. In addition GMM have to assign probabilities to belonging to each cluster. Thus in contrast to KMean's hard clustering, GMM gets a lot more structural information and allows to measure how wide each cluster is since we are dealing with probabilities.

Furthermore to evaluate the number of clusters, we can use the Silouhette Score) instead of SSD. The silhouette coefficient is calcualted using the mean intra-cluster distance (a) and the mean nearest cluster distance (b) for each sample. (b) here refers to the dsitance between a sample ad the nereast cluster that the sample is not a part of. The silhouette coefficient for a sample is defined as (b-a)/max(a,b). Note that silhouette coefficient is only defined if the number of labels is 2 <= n_labels <= n_samples-1.

In [WholeSale Distributor](https://github.com/NadimKawwa/WholesaleDistributor), we explicitly compare GMM to KMeans and use the Silhouette Coefficient for determining the number of clusters.
