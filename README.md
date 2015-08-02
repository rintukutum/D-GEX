README for D-GEX
================

INTRODUCTION
============

Large-scale gene expression profiling has been widely
used to characterize cellular states in response to various disease
conditions, genetic perturbations, drug treatments, and so on.
Although the cost of whole-genome expression profiling has been
dropping steadily, generating a compendium of expression profiling
over thousands of samples is still too expensive to be carried out
by typical labs. Recognizing that gene expressions are often highly
correlated, researchers from the NIH LINCS program have developed
a cost-effective strategy of profiling only  ̃1,000 carefully selected
genes (called landmark genes) and relying on computational methods
to infer the expression of remaining genes (called target genes).
Under this approach, millions of expression profiles have been
generated. However, the approach adopted by the LINCS program to
infer gene expression is currently based on linear regression, limiting
its accuracy since it does not capture complex nonlinear relationship
between expression of genes. We present a deep learning method (abbreviated as D-
GEX) to infer the expression of target genes from the expression
of landmark genes. We used a compendium of gene expression
data from Gene Expression Omnibus (GEO), consisting of 111K
microarray-based expression profiles, to train our model and compare
its performance to those from other methods. In terms of mean
absolute error averaged across all genes, the deep learning based
approach significantly outperforms linear regression with 15.33%
relative improvement. A gene-wise comparative analysis shows that
deep learning achieves lower error than linear regression in 99.97%
of the target genes. To check the robustness of our model across
different profiling platforms, We also tested the performance of our
learned model on an independent dataset from GTEx, which consists
of 2,921 gene expression profiles obtained by RNA-Seq. D-GEX
still outperforms linear regression on the GTEx dataset with 6.57%
relative improvement, and achieves lower error in 81.31% of the
target genes. Overall, our results suggest that the deep learning
based method provides a better alternative than linear regression for
modelling the complex relationship between expression of genes.


This code base provides all
the necessary pieces to reproduce the main results of D-GEX. If you have any 
questions, please email yil8@uci.edu







PREREQUISITES
=============
* Python (2.7). [Python 2.7.6](http://www.python.org/download/releases/2.7.6/) is recommended.

* [Numpy](http://www.numpy.org/)(>=1.6.1). 

* [Scipy](http://www.scipy.org/)(>=0.10). 

* [Theano](http://deeplearning.net/software/theano/)(0.7).

* [Pylearn2](https://github.com/lisa-lab/pylearn2).

* [Scikit-learn](http://scikit-learn.org/stable/)(>=0.15.2).

* [l1ktools](https://github.com/cmap/l1ktools).






DATA
====
The original data files are not provided within this codebase, as some of them require applying for access. Once you download all of them, please put them in this codebase.

GEO
---
The GEO microarray expression data can be accessed from [lincscloud](http://www.lincscloud.org/l1000/). You need to [apply](https://docs.google.com/forms/d/1j6Vb_s4FrDodxoS3IDZsHWoVNrOnKNQbqwbwcW2a208/viewform) for the access. The original data downloaded is **bgedv2_QNORM.gctx** (data level 3 Q2NORM).


GTEx
----
The GTEx RNA-Seq expression data can be accessed from [GTEx Portal](http://www.gtexportal.org/home/). You need to apply through dbGaP [phs000424.v3.p1](http://www.ncbi.nlm.nih.gov/projects/gap/cgi-bin/study.cgi?study_id=phs000424.v3.p1) for the access. The original data downloaded is **GTEx_RNASeq_RPKM_n2921x55993.gctx**.

1000G
-----
The 1000 Genomes RNA-Seq expression data can be accessed from [EMBL-EBI](http://www.ebi.ac.uk/arrayexpress/experiments/E-GEUV-1/files/analysis_results/?ref=E-GEUV-1). The original data downloaded is **GD462.GeneQuantRPKM.50FN.samplename.resk10.txt**.








PREPROCESS
==========
The whole preprocessing step should be done by run
```
$ ./preprocess.sh
```

Specifically, there are four steps.

1. Removing duplicates by k-means: `kmeans.py`, `nodup_idx.py`.
2. Coverting data into numpy format: `bgedv2.py`, `GTEx.py`, `1000G.py`.
3. Quantile normalization: `bgedv2_reqnorm.py`, `GTEx_reqnorm.py`, `1000G_reqnorm.py`.
4. Standardization: `bgedv2_norm.py`, `GTEx_norm.py`, `1000G_norm.py`.





TRAINING
========
Training D-GEX is done by run `H1_0-4760.py`, `H1_4760-9520.py`, `H2_0-4760.py`, `H2_4760-9520.py`, `H3_0-4760.py`, `H3_4760-9520.py`. Each stript trains half of the target genes (0-4760 or 4760-9520) with a certain architecture (1, 2 or 3 hidden layers).

A training example using 200 epoch, 0.75 include rate (0.25 dropout rate) and 9000 hidden units within each hidden layer for 0-4760 target genes with 1 hidden layer is by:
```
$ ./ H1_0-4760.py 9000_H1_0-4760_75 200 9000 0.75
```
In which, **9000_H1_0-4760_75** is the base name for all the output files.






OUTPUT
======
Each training instance will output 7 files. For example, by running 
```
$ ./ H1_0-4760.py 9000_H1_0-4760_75 200 9000 0.75
```
It outputs:

**9000_H1_0-4760_75.log**, the log file of the training instance.

**9000_H1_0-4760_75_bestva_model.pkl**, the model saved by best performance on Y_va (GEO microarray data).

**9000_H1_0-4760_75_bestva_Y_va_hat.npy**, the Y_va_hat predicted by best performance on Y_va (GEO microarray data).

**9000_H1_0-4760_75_bestva_Y_te_hat.npy**, the Y_te_hat predicted by best performance on Y_va (GEO microarray data).

**9000_H1_0-4760_75_best1000G_model.pkl**, the model saved by best performance on Y_1000G (1000G RNA-Seq data).

**9000_H1_0-4760_75_best1000G_Y_1000G_hat.npy**, the Y_1000G_hat predicted by best performance on Y_1000G (1000G RNA-Seq data).

**9000_H1_0-4760_75_best1000G_Y_GTEx_hat.npy**, the Y_GTEx_hat predicted by best performance on Y_1000G (1000G RNA-Seq data).

