# Identify Customer Segments

Apply unsupervised learning techniques to identify segments of the population that form the core customer base for a mail-order sales company in Germany.
We will look at relationships between demographics features, organize the population into clusters, and see how prevalent customers are in each of the segments obtained.


# The Dataset

The datasets used here are provided coutesy of Bertelsmann partners AZ Direct and Arvato Financial Solutions. 
One dataset is with emographic information about the people of Germany, the other is and one with that same information for customers of a mail-order sales company.
Unfourtunately the datasets are proprietary of AZ Direct GmbH and can't be shared on this repository.
Users interested in using the datasets should contact AZ Direct, or enroll in Udacity's Data Sciecne Nanodegree for an opporunity to work with it.
That being said, the methods used here cam be applied to any unsupervised learning project.
You can also read observations in the notebook to get a sense of what is in the dataset ;).

# Libraries

- numpy
- pandas
- matplotlib.pyplot
- seaborn
- re
- sklearn 
- tqdm
- pickle


# Cleaning The Data


The first step includes significant data cleaning. The figure below shows a heatplot of missing values (NaN) in the dataset.
We seek to maintain the maximum number of features and observations possible.
For this reason we decide to drop features that have a signficant higher number of outliers larger than average.

![nan_heatmap](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/nan_heat.png)

# Feature Reduction

Using sklearn's PCA package, we can obtain a low dimensional representation of our feature space.
The plot below shows the cumulative explained variance after performing PCA. We move with the analysis using this new feature space.

![pca_cumsum](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/pca_cumsum_pre.png)

# Clustering

We opt to use KMeans to determine the number of clusters in our dataset.
We must take into account time complexity due to the scale of the data.
KMeans time compelxity it is O(tkn) where:
- n is the number of objects
- k is the number of clusters
- t is how many iterations it takes to converge.

Using MiniBatchKMeans we attempt to deduce the number of clusters for an increasing batch size.
The plot below shows a decreasing sum of squared distnaces as the number of clusters increases.
Visually, we can determine a point of decreasing returns and set the number of clusters to 18 for the full KMeans algorithm.
![minikmeans](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/elbow_minik.png)

# Visualizing Clusters

What do our clusters look like? To answe this question we reduce our feature space to a two dimesional space and obtain the figure below.

![pca_space](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/pca_space.png)


# General Population Vs Clients

After clustering the general population, we apply the same transformation to the customers dataset. 
The plot below shows the difference in frequency distribution between the two groups.

![freq_diff](https://github.com/NadimKawwa/Identify_Customer_Segments/blob/master/plots/freq_prct_diff.png)

