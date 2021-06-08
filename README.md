# A review of Isotropic PCA and Affine-Invariant Clustering

<p align="center">
  <img width="620" height="413" src="images\IPCA1.png">
</p>

Dr. Charles Brubaker and Dr. Santosh Vempala from Georgia institute of technology introduced a new algorithm for clustering points in high dimensions in their paper “Isotropic PCA and Affine-Invariant Clustering”. This algorithm is an affine-invariant extension of principle component analysis based on the assumption that overlap of subspaces in every direction is small. They construct such algorithm using primarily isotropic transformation, spectral projection, and customized reweighting technique and named such algorithm as “Isotopic PCA”.

In this blog post, we'll prepare readers with enough information to understand the paper, cover the main ideas of Brubaker and Vempala, explain how Isotropic PCA works, and provide some thoughts on its application, future research direction etc.

## Table of Contents

1. Background Information

    1.1 principle component analysis (PCA)
    
    1.2 affine transformation
    
    1.3 PCA and Affine Invariant
    
    1.4 PCA and isotropic inputs
    
2. Algorithm

    2.1 algorithm overview
    
    2.2 component partition
    
    2.3 recursion

3. Result

    3.1 result overview
    
    3.2 comparison

4. Application

    4.1 mixtures of distributions with mild properties
    
    4.2 bound on the first few moments
    
    4.3 training of dataset that has different representations in different nation
    
    4.4 potential replacement of all PCA usage in ML algorithms

5. Mathematical Detail


## 1. Background Information

In this section, we'll talk about some important concepts appeared in the paper that are crucial for understanding Isotropic PCA.

### 1.1 Principle Component Analysis (PCA)

<p align="center">
  <img width="480" height="270" src="images\Slide3.JPG">
</p>

The principal components, or PCs, of a collection of points in a real coordinate space are a sequence of unit vectors, where each vector is the direction of a line that best fits the data while being orthogonal to other vectors. Here, a best-fitting line is defined as one that minimizes the average squared distance from the points to the line. In other words, PCA is the process of computing the principal components and using them to perform a change of basis on the data. We won't cover too much further in detail as most of the readers are assumed to have a basic understanding of PCA beforehand. For more information, [here is a visualized explaination of PCA I find really useful](https://setosa.io/ev/principal-component-analysis/).

### 1.2 Affine Transformation

<p align="center">
  <img width="480" height="270" src="images\Slide4.JPG">
</p>

Now lets talk about affine transformations. An affine transformation is an automorphism of an affine space, a function which maps an affine space onto itself. In other words, affine transformations are combination of translations with linear transformations. It preserves both the dimension of any affine subspaces and the ratios of the lengths of parallel line segments but does not necessarily preserve angles between lines or distances between points, as shown in the left image. The right image gives a visual aid of affine transformation. Each of the leaves of the fern is related to each other leaf by an affine transformation. For instance, the red leaf can be transformed into both the dark blue leaf and any of the light blue leaves by a combination of reflection, rotation, scaling, and translation, which are examples of affine transformation.

### 1.3 PCA and Affine Invariant

<p align="center">
  <img width="480" height="270" src="images\Slide5.JPG">
</p>

Affine invariant refer to the invariance of result after affine transformations. Hansford define affine invariant as the phenomenon that median or mean of the transformed data is the same as the affine transformation applied to the median of the original data, as shown above. Note that PCA is not affine invariant because performing linear transformations on the dataset would results in different principle components and different variances. Such property of PCA makes many clustering methods such as k-means less effective after certain transformations. Here's an example:

<p align="center">
  <img width="480" height="270" src="images\Slide6.JPG">
</p>

In the above example, we scale x axis by 0.5 to get z axis while keeping the original data the same. Such scaling triggers a change in covariance matrix and consequently results in different component outputs.

Therefore affine invariant is a good property one machine learning methods could have on handling with unit sensitive or noisy distribution/mixtures.

### 1.4 PCA and Isotropic Inputs

<p align="center">
  <img width="480" height="270" src="images\Slide7.JPG">
</p>

When the covariance matrix of the input is a multiple of the identity, PCA reveals no information and the second moment along any direction is the same. Such inputs are called isotropic. However, if isotropic PCA were used here, it could actually pulls apart the components and perform an affine invariant clustering, which is pretty interesting considering that PCA is useless in such scenario. The authors also claim that isotropic PCA also perform substantial better of known results when “applied to mixtures of arbitrary Gaussians from unlabeled samples”.

## 2. Algorithm

<p align="center">
  <img width="480" height="270" src="images\Slide8.JPG">
</p>

In this section we'll be talking about the isotropic PCA algorithm Brubaker and Vempala proposed, shown above.

### 2.1 Algorithm Overview

<p align="center">
  <img width="480" height="270" src="images\Slide9.JPG">
</p>

The above image is a visualization of the algorithm without too many mathematical details (which could be found in section 5). The algorithm consists three main steps: The first step is to find an affine transformation that makes the input isotropic/having an isotropic distribution. In other words, this step is to find a linear transformation that moves the mean to the origin and transform the covariance matrix into identity. In the next step, we take a random subset of the input and apply such transformation on those points. The result would then be reweighted according to a specific spherically symmetric Gaussian. The third step of this algorithm is to find a direction close to the Fisher subspace to partition the components and get a hyperplane perpendicular to such direction that divides component’s probability mass. Here the authors stated two possible scenarios and we'll discuss them in the next subsection. After having two half-spaces, we recursively apply this algorithm on both mixtures using different samples in step 2.

### 2.2 Component Partition

<p align="center">
  <img width="480" height="270" src="images\Slide10.JPG">
</p>

In the third step of this algorithm, Brubaker and Vempala discussed two different methods to determine the direction for step 4 depending on the relative “mass” between components: If some component is heavier than the others, the reweighted mean would shift in the intermean subspace and that would be the direction along which we partition and arrive at a hyperplane that separate components. If no significant shifts are observed, the top principal components of the second moment matrix could approximate the intermean subspace and we could project the data onto the direction of maximum second moment. 

### 2.3 Recursion

In the recursion step, the authors setup a threshold for largest gap between points in certain interval. If the gap is lower than the threshold, recursion is terminated, and projected data are returned. Otherwise find the half-spaces, recurse on both and return the union of the polyhedral produced by recursive calls. Such critical level is the same regardless which direction identification methods we used earlier.

## 3. Results

In this section, we'll explore some simple results Brubaker and Vempala provided using isotropic PCA. We'll also look at a more complicated experiment using both normal PCA and isotropic PCA to analyze their differences.

### 3.1 Result Overview

<p align="center">
  <img width="480" height="270" src="images\Slide12.JPG">
</p>

From the plots above, we could see that isotropic PCA successfully seperates/clusters the classes, even for the first two examples where blue and red dot are intuitively close, and turns every mixture into almost what the authors would call “parallel pancakes”, separable along the intermean direction. Most of the credit goes to isotropy, i.e. the first step of isotropic PCA.


### 3.2 Comparison

<p align="center">
  <img width="480" height="270" src="images\Slide100.jpg">
</p>

## 4. Potential Application



### 4.1 Mixtures of Distributions



### 4.2 Bound of Moments



### 4.3 Training of Dataset



### 4.4 Potential Replacement of PCA



## 5. Mathematical Details

