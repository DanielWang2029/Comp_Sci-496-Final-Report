# A review of Isotropic PCA and Affine-Invariant Clustering

<p align="center">
  <img width="620" height="413" src="images\IPCA1.png">
</p>

Dr. Charles Brubaker and Dr. Santosh Vempala from Georgia institute of technology introduced a new algorithm for clustering points in high dimensions in their paper “[Isotropic PCA and Affine-Invariant Clustering](https://arxiv.org/abs/0804.3575)”. This algorithm is an affine-invariant extension of principle component analysis based on the assumption that overlap of subspaces in every direction is small. They construct such algorithm using primarily isotropic transformation, spectral projection, and customized reweighting technique and named such algorithm as “Isotopic PCA”.

In this blog post, we'll prepare readers with enough information to understand the paper, cover the main ideas of Brubaker and Vempala, explain how Isotropic PCA works, and provide some thoughts on its application, future research direction etc.

## Table of Contents

- Background Information
    - principle component analysis (PCA)
    - affine transformation
    - PCA and Affine Invariant
    - PCA and isotropic inputs
    
- Algorithm
    - algorithm overview
    - component partition
    - recursion

- Result
    - result overview
    - comparison

- Application and Future Research
    - potential applications
    - future research directions

- Mathematical Detail


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

Therefore affine invariant is a good property one machine learning methods could have on handling with unit sensitive or noisy datasets. It allows algorithms to potentially operate on larger set of Gaussian mixtures and distributions with mild properties.

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

One of the major differences between Isotropic PCA and other clustering methods is that Isotropic PCA is affine invariant. This is due to the isotropic transformation in step 1 and direction identification in step 3 which does not have any access of labels. The above plots is a more complex experiment with three independent classes and a good example to showcase such difference. We could find that implementing PCA on the original dataset has similar effect of randomly projects the input onto some hyperplane, both of which result in a chaotic distribution of data from all classes. Isotropic PCA, on the other hand, is pretty useful in this circumstance as it could effectively cluster those three classes.

## 4. Application and Future Research

In this section, we'll talk about some application of isotropic PCA, both proposed by the authors and by myself, and possible future research directions regarding this algorithm and affine invariant clustering in general.

### 4.1 Potential Applications

<p align="center">
  <img width="480" height="270" src="images\Slide14.JPG">
</p>

Brubaker and Vempala proposed two applications for isotropic PCA in their paper: bounds of moments and analysis of mixtures of distribution with mild properties such as center symmetry (due to affine invarient). For bounds of moments, they stated that since isotropic PCA utilizes spectral and mean shift method, a quantitative bound for the first few moments might be solvable with additional calculation. 

<p align="center">
  <img width="480" height="270" src="images\Slide15.JPG">
</p>

In addition the above application, I proposed two other potential usages for Isotropic PCA in my presentation: unit-sensitive datasets and PCA replacement in ML algorithms. Since Isotropic PCA is affine invariant, it could, for example, generate unified solution on datasets that have different representation in different nations (regardless of units), which better unites scientific finding and their applications around the world. Isotropic PCA could also acts as a replacement of PCA in many ML algorithms. Many modern ML algorithms involve an initial PCA of dataset at the beginning. Isotropic PCA could potentially replace those PCA to achieve better separation effects or even advanced affine invariant ML methods.

### 4.2 Future Research directions

One of the brilliances of isotropic PCA is that it finds a way to identify directions to partition components without accessing any labels. This is intuitively hard because normal PCA provide no additional information after isotropy/isotropic transformation. In this identification process, we could find high correlation between the design of reweighting step and the discussion of symmetry: if mixing weights are equal, the mean would not shift due to symmetry, then the reweighted 2nd moment would reveal the intermean direction; Otherwise the heavier component would lies closer to the center and the mean shift gives us the direction to partition components, all of which are cleverly designed. In addition, normal PCA might fail for mixture of arbitrary Gaussians due to affine variance. isotropic PCA successfully solves such issue and made possible to learn noisy distributions with their parameters and perform agnostic learning.

Future research on isotropic PCA and affine invariant clustering could focus on the performance of isotropic PCA. Although the authors claim that isotropic PCA have an advantage for isotropic data inputs, the exact increase on performances and quantitative comparison between isotropic PCA and other models are yet to be discovered/calculated. We could also perform additional calculation on the worse case for isotropic PCA as Brubaker and Vempala has several assumptions they deemed as ideal. In addition, future research could be conducted on the replacement for PCA in ML models. According to the authors, such replacement could increase the scope of techniques for clustering and potentially promote the performance of specific existing methods. On the application side, I would also like to see isotropic PCA performed on real datasets, especially unit-sensitive ones that I proposed earlier, and provide a new perspective on clustering.

## 5. Mathematical Details



## 6. Citations

<p align="center">
  <img width="480" height="270" src="images\Slide16.JPG">
</p>

