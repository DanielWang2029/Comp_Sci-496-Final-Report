## A review of Isotropic PCA and Affine-Invariant Clustering

<p align="center">
  <img width="620" height="413" src="images\IPCA1.png">
</p>

Dr. Charles Brubaker and Dr. Santosh Vempala from Georgia institute of technology introduced a new algorithm for clustering points in high dimensions in their paper “Isotropic PCA and Affine-Invariant Clustering”. This algorithm is an affine-invariant extension of principle component analysis based on the assumption that overlap of subspaces in every direction is small. They construct such algorithm using primarily isotropic transformation, spectral projection, and customized reweighting technique and named such algorithm as “Isotopic PCA”.

In this blog post, we'll prepare readers with enough information to understand the paper, cover the main ideas of Brubaker and Vempala, explain how Isotropic PCA works, and provide some thoughts on its application, future research direction etc.

### Table of Contents

1. Background Information

    1.1 principle component analysis (PCA)
    
    1.2 affine transformation
    
    1.3 PCA and Affine Invariant
    
    1.4 PCA and isotropic inputs
    
2. Algorithm

    2.1 algorithm overview
    
    2.2 reweighting
    
    2.3 component partition

3. Result

    3.1 result overview
    
    3.2 comparison

4. Application

    4.1 mixtures of distributions with mild properties
    
    4.2 bound on the first few moments
    
    4.3 training of dataset that has different representations in different nation
    
    4.4 potential replacement of all PCA usage in ML algorithms

5. Mathematical Detail


### 1. Background Information

In this section, I'll talk about some 


#### 1.1 principle component analysis (PCA)


#### 1.2 affine transformation


#### 1.3 PCA and Affine Invariant


#### 1.4 PCA and isotropic inputs


### 2. Algorithm


#### 2.1 algorithm overview


#### 2.2 reweighting


#### 2.3 component partition


### 3. Results


#### 3.1 result overview


#### 3.2 comparison


### 4. Potential Application


#### 4.1 mixtures of distributions with mild properties


#### 4.2 bound on the first few moments


#### 4.3 training of dataset that has different representations in different nation


#### 4.4 potential replacement of all PCA usage in ML algorithms


### 5. Mathematical Details



### Markdown

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```


Now lets talk about affine transformations. An affine transformation is an automorphism of an affine space, a function which maps an affine space onto itself. In other words, affine transformations are combination of translations with linear transformations. It preserves both the dimension of any affine subspaces and the ratios of the lengths of parallel line segments but does not necessarily preserve angles between lines or distances between points, as shown in the left image. The right image gives a visual aid of affine transformation. Each of the leaves of the fern is related to each other leaf by an affine transformation. For instance, the red leaf can be transformed into both the dark blue leaf and any of the light blue leaves by a combination of reflection, rotation, scaling, and translation, which are examples of affine transformation.

Affine invariant refer to the invariance of result after affine transformations. Hansford define affine invariant as the phenomenon that median or mean of the transformed data is the same as the affine transformation applied to the median of the original data, as shown on screen. Note that PCA is not affine invariant because performing linear transformations on the dataset would results in different principle components and different variances. This makes many clustering methods such as k-means less effective after certain transformations, as shown in the bottom.

When the covariance matrix of the input is a multiple of the identity, then PCA reveals no information; the second moment along any direction is the same. Such inputs are called isotropic. However, if isotropic PCA were used here, it could actually pulls apart the components and perform an affine invariant clustering, which is pretty interesting considering that PCA is useless in such scenario.
