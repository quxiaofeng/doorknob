---
layout: default
permalink: /dpl/
---

[Back]({{site.baseurl}}/#projective-dictionary-pair-learning)

# Projective Dictionary Pair Learning #

[Shuhang Gu, Lei Zhang, Wangmeng Zuo, and Xiangchu Feng. "Projective dictionary pair learning for pattern classification." Neural Information Processing Systems (2014).](http://papers.nips.cc/paper/5600-projective-dictionary-pair-learning-for-pattern-classification.pdf)

## Abstract ##

Discriminative dictionary learning (DL) has been widely studied in various pattern classification problems. Most of the existing DL methods aim to learn a synthesis dictionary to represent the input signal while enforcing the representation coefficients and/or representation residual to be discriminative. However, the \\( \\ell\_0 \\) or \\( \\ell\_1 \\)-norm sparsity constraint on the representation coefficients adopted in most DL methods makes the training and testing phases time consuming. We propose a new discriminative DL framework, namely projective dictionary pair learning (DPL), which learns a synthesis dictionary and an analysis dictionary jointly to achieve the goal of signal representation and discrimination. Compared with conventional DL methods, the proposed DPL method can not only greatly reduce the time complexity in the training and testing phases, but also lead to very competitive accuracies in a variety of visual classification tasks.

## 1 Introduction ##

Sparse representation represents a signal as the linear combination of a small number of atoms chosen out of a dictionary, and it has achieved a big success in various image processing and computer vision applications [1, 2]. The dictionary plays an important role in the signal representation process [3]. By using a predefined analytical dictionary (e.g., wavelet dictionary, Gabor dictionary) to represent a signal, the representation coefficients can be produced by simple inner product operations. Such a fast and explicit coding makes analytical dictionary very attractive in image representation; however, it is less effective to model the complex local structures of natural images.

Sparse representation with a synthesis dictionary has been widely studied in recent years [2, 4, 5]. With synthesis dictionary, the representation coefficients of a signal are usually obtained via an \\( \\ell\_p \\)-norm (\\( p \\leq 1 \\)) sparse coding process, which is computationally more expensive than analytical dictionary based representation. However, synthesis based sparse representation can better model the complex image local structures and it has led to many state-of-the-art results in image restoration [6]. Another important advantage lies in that the synthesis based sparse representation model allows us to easily learn a desired dictionary from the training data. The seminal work of KSVD [1] tells us that an over-complete dictionary can be learned from example natural images, and it can lead to much better image reconstruction results than the analytically designed off-the-shelf dictionaries. Inspired by KSVD, many dictionary learning (DL) methods have been proposed and achieved state-of-the-art performance in image restoration tasks.

The success of DL in image restoration problems triggers its applications in image classification tasks. Different from image restoration, assigning the correct class label to the test sample is the goal of classification problems; therefore, the discrimination capability of the learned dictionary is of the major concern. To this end, supervised dictionary learning methods have been proposed to promote the discriminative power of the learned dictionary [4, 5, 7, 8, 9]. By encoding the query sample over the learned dictionary, both the coding coefficients and the coding residual can be used for classification, depending on the employed DL model. Discriminative DL has led to many state-of-the-art results in pattern recognition problems.

One popular strategy of discriminative DL is to learn a shared dictionary for all classes while enforcing the coding coefficients to be discriminative [4, 5, 7]. A classifier on the coding coefficients can be trained simultaneously to perform classification. Mairal et al. [7] proposed to learn a dictionary and a corresponding linear classifier in the coding vector space. In the label consistent KSVD (LC-KSVD) method, Jiang et al. [5] introduced a binary class label sparse code matrix to encourage samples from the same class to have similar sparse codes. In [4], Mairal et al. proposed a task driven dictionary learning (TDDL) framework, which minimizes different risk functions of the coding coefficients for different tasks.

Another popular line of research in DL attempts to learn a structured dictionary to promote discrimination between classes [2, 8, 9, 10]. The atoms in the structured dictionary have class labels, and the class-specific representation residual can be computed for classification. Ramirez et al. [8] introduced an incoherence promotion term to encourage the sub-dictionaries of different classes to be independent. Yang et al. [9] proposed a Fisher discrimination dictionary learning (FDDL) method which applies the Fisher criterion to both representation residual and representation coefficient. Wang et al. [10] proposed a max-margin dictionary learning (MMDL) algorithm from the large margin perspective.

In most of the existing DL methods, \\( \\ell\_0 \\)-norm or \\( \\ell\_1 \\)-norm is used to regularize the representation coefficients since sparser coefficients are more likely to produce better classification results. Hence a sparse coding step is generally involved in the iterative DL process. Although numerous algorithms have been proposed to improve the efficiency of sparse coding [11, 12], the use of \\( \\ell\_0 \\)-norm or \\( \\ell\_1 \\)-norm sparsity regularization is still a big computation burden and makes the training and testing inefficient.

It is interesting to investigate whether we can learn discriminative dictionaries but without the costly \\( \\ell\_0 \\)-norm or \\( \\ell\_1 \\)-norm sparsity regularization. In particular, it would be very attractive if the representation coefficients can be obtained by linear projection instead of nonlinear sparse coding. To this end, in this paper we propose a projective dictionary pair learning (DPL) framework to learn a synthesis dictionary and an analysis dictionary jointly for pattern classification. The analysis dictionary is trained to generate discriminative codes by efficient linear projection, while the synthesis dictionary is trained to achieve class-specific discriminative reconstruction. The idea of using functions to predict the representation coefficients is not new, and fast approximate sparse coding methods have been proposed to train nonlinear functions to generate sparse codes [13, 14]. However, there are clear difference between our DPL model and these methods. First, in DPL the synthesis dictionary and analysis dictionary are trained jointly, which ensures that the representation coefficients can be approximated by a simple linear projection function. Second, DPL utilizes class label information and promotes discriminative power of the representation codes.

One related work to this paper is the analysis-based sparse representation prior learning [15, 16], which represents a signal from a dual viewpoint of the commonly used synthesis model. Analysis prior learning tries to learn a group of analysis operators which have sparse responses to the latent clean signal. Sprechmann et al. [17] proposed to train a group of analysis operators for classification; however, in the testing phase a costly sparsity-constrained optimization problem is still required. Feng et al. [18] jointly trained a dimensionality reduction transform and a dictionary for face recognition. The discriminative dictionary is trained in the transformed space, and sparse coding is needed in both the training and testing phases.

The contribution of our work is two-fold. First, we introduce a new DL framework, which extends the conventional discriminative synthesis dictionary learning to discriminative synthesis and analysis dictionary pair learning (DPL). Second, the DPL utilizes an analytical coding mechanism and it largely improves the efficiency in both the training and testing phases. Our experiments in various visual classification datasets show that DPL achieves very competitive accuracy with state-of-the-art DL algorithms, while it is significantly faster in both training and testing.

## 2 Projective Dictionary Pair Learning ##

### 2.1 Discriminative dictionary learning ###

Denote by \\(\\mathbf{X = \[X\_1,\\ldots,X\_k,\\ldots,X\_K\]}\\) a set of p-dimensional training samples from \\(K\\) classes, where \\(\\mathbf{X\_k} \\in \\mathbb{R}^{p\\times{}n}\\) is the training sample set of class \\(k\\), and \\(n\\) is the number of samples of each class. Discriminative DL methods aim to learn an effective data representation model from \\(\\mathbf{X}\\) for classification tasks by exploiting the class label information of training data. Most of the state-of-the-art discriminative DL methods [5, 7, 9] can be formulated under the following framework:

\\[\\qquad \min\_{\\mathbf{D}, \\mathbf{A}} \\| \\mathbf{X} - \\mathbf{DA} \\|^2\_F + \\lambda \\| \\mathbf{A} \\|\_p + \\mathbf{\\Psi}(\\mathbf{D}, \\mathbf{A}, \\mathbf{Y}), \\qquad (1)\\]

where \\( \\lambda \geq 0 \\) is a scalar constant, \\( \\mathbf{Y} \\) represents the class label matrix of samples in \\( \\mathbf{X} \\), \\( \\mathbf{D} \\) is the synthesis dictionary to be learned, and \\( \\mathbf{A} \\) is the coding coefficient matrix of \\( \\mathbf{X} \\) over \\( \\mathbf{D} \\). In the training model above, the data fidelity term \\( \\| \\mathbf{X} - \\mathbf{DA} \\|^2\_F \\) ensures the representation ability of \\( \\mathbf{D} \\); \\( \\| \\mathbf{A} \\|\_p\\) is the \\( \\ell\_p \\)-norm regularizer on \\( \\mathbf{A} \\); and \\( \\mathbf{\\Psi}(\\mathbf{D}, \\mathbf{A}, \\mathbf{Y}) \\) stands for some discrimination promotion function, which ensures the discrimination power of \\( \\mathbf{D} \\) and \\( \\mathbf{A} \\).

As we introduced in last Section, some DL methods [4, 5, 7] learn a shared dictionary for all classes and a classifier on the coding coefficients simultaneously, while some DL methods [8, 9, 10] learn a structured dictionary to promote discrimination between classes. However, they all employ \\( \\ell\_0 \\) or \\( \\ell\_1 \\)-norm sparsity regularizer on the coding coefficients, making the training stage and the consequent testing stage inefficient.

In this work, we extend the conventional DL model, which learns a discriminative synthesis dictionary, to a novel DPL model, which learns a pair of synthesis and analysis dictionaries. No costly \\( \\ell\_0 \\) or \\( \\ell\_1 \\)-norm sparsity regularizer is required in the proposed DPL model, and the coding coefficients can be explicitly obtained by linear projection. Fortunately, DPL does not sacrifice the classification accuracy while achieving significant improvement in the efficiency.

### 2.2 The dictionary pair learning model ###

The conventional discriminative DL model in (1) aims to learn a synthesis dictionary \\(\\mathbf{D}\\) to sparsely represent the signal \\(\\mathbf{X}\\), and a costly \\(l_1\\)-norm sparse coding process is needed to resolve the code \\(\\mathbf{A}\\). Suppose that if we can find an analysis dictionary, denoted by \\(\\mathbf{P}\\in\\mathbb{R}^{mK\\times{}p}\\), such that the code \\(\\mathbf{A}\\) can be analytically obtained as \\(\\mathbf{A=PX}\\), then the representation of \\(\\mathbf{X}\\) would become very efficient. Based on this idea, we propose to learn such an analysis dictionary \\(\\mathbf{P}\\) together with the synthesis dictionary \\(\\mathbf{D}\\), leading to the following DPL model:

\\[\\{\\mathbf{P^\*,D^\*}\\}=\\arg\\min\_{\\mathbf{P,D}}\\|\\mathbf{X-DPX}\\|^2\_F+\\mathbf{\\Psi(D,P,X,Y)},\\]
\\[\\phantom{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}(2)\\]

where \\(\\mathbf{\\Psi(D,P,X,Y)}\\) is some discrimination function. \\(\\mathbf{D}\\) and \\(\\mathbf{P}\\) form a dictionary pair: the analysis dictionary \\(\\mathbf{P}\\) is used to analytically code \\(\\mathbf{X}\\), and the synthesis dictionary \\(\\mathbf{D}\\) is used to reconstruct \\(\\mathbf{X}\\). The discrimination power of the DPL model depends on the suitable design of \\(\\mathbf{\\Psi(D,P,X,Y)}\\). We propose to learn a structured synthesis dictionary \\(\\mathbf{D=[D\_1,\\ldots,D\_k,\\ldots,D\_K]}\\) and a structured analysis dictionary \\(\\mathbf{P=[P\_1;\\ldots;P\_k;\\ldots;P\_K]}\\), where \\(\\{\\mathbf{D}\_k\\in\\mathbb{R}^{p\\times{}m},   \\mathbf{P}\_k\\in\\mathbb{R}^{m\\times{}p}\\}\\) forms a subdictionary pair corresponding to class \\(k\\). Recent studies on sparse subspace clustering [19] have proved that a sample can be represented by its corresponding dictionary if the signals satisfy certain incoherence condition. With the structured analysis dictionary \\(\\mathbf{P}\\), we want that the sub-dictionary \\(\\mathbf{P}_k\\) can project the samples from class \\(i,  i\neq k\\), to a nearly null space, i.e.,

\\[\\phantom{xxxxxxxxxxxx}\\mathbf{P}\_k\\mathbf{X}\_i\\sim\\mathbf{0}, \\quad \\forall k\\neq i.\\phantom{xxxxxxxxxxxx}(3)\\]

Clearly, with (3) the coefficient matrix \\(\\mathbf{PX}\\) will be nearly block diagonal. On the other hand, with the structured synthesis dictionary \\(\\mathbf{D}\\), we want that the sub-dictionary \\(\\mathbf{D}\_k\\) can well reconstruct the data matrix \\(\\mathbf{X}\_k\\) from its projective code matrix \\(\\mathbf{P}\_k\\mathbf{X}\_k\\); that is, the dictionary pair should minimize the reconstruction error:

\\[\\phantom{xxxxxxxxx}\\min\_{\\mathbf{P,D}}\sum^K\_{k=1}\\|\\mathbf{X}\_k-\\mathbf{D}\_k\\mathbf{P}\_k\\mathbf{X}\_k\\|^2\_F.\\phantom{xxxxxxxxx}(4)\\]

Based on the above analysis, we can readily have the following DPL model:

\\[\\{\\mathbf{P}^\*,\\mathbf{D}^\*\\}=\\arg\\min\_{\\mathbf{P,D}}\\sum^K\_{k=1}\\|\\mathbf{X}\_k-\\mathbf{D}\_k\\mathbf{P}\_k\\mathbf{X}\_k\\|^2\_F+\\lambda\\|\\mathbf{P}\_k\\mathbf{X}\_k\\|^2\_F,\\]
\\[\\phantom{xxxxxxxxxxx} \\text{s.t.} \\quad \\|\\mathbf{d}\_i\\|^2\_2\\leq{}1.\\phantom{xxxxxxxxxxxxxxx}(5)\\]

where \\(\\mathbf{X}\_k\\) denotes the complementary data matrix of \\(\\mathbf{X}\_k\\) in the whole training set \\(\\mathbf{X}\\), \\(\\lambda>0\\) is a scalar constant, and \\(\\mathbf{d}\_i\\) denotes the \\(i\\)th atom of synthesis dictionary \\(\\mathbf{D}\\). We constrain the energy of each atom \\(\\mathbf{d}\_i\\) in order to avoid the trivial solution of \\(\\mathbf{P}\_k=\\mathbf{0}\\) and make the DPL more stable.

The DPL model in (5) is not a sparse representation model, while it enforces group sparsity on the code matrix \\(\\mathbf{PX}\\) (i.e., \\(\\mathbf{PX}\\) is nearly block diagonal). Actually, the role of sparse coding in classification is still an open problem, and some researchers argued that sparse coding may not be crucial to classification tasks [20, 21]. Our findings in this work are supportive to this argument. The DPL model leads to very competitive classification performance with those sparse coding based DL models, but it is much faster.

### 2.3 Optimization ###

---

> **Algorithm 1** Discriminative synthesis&analysis dictionary pair learning (DPL)

---

> **Input**: Training samples for \\( K \\) classes \\( \\mathbf{X} = \[ \\mathbf{X}\_1, \\mathbf{X}\_2, \\ldots, \\mathbf{X}\_K \] \\), parameter \\( \\lambda, \\tau, m \\);

> 1: Initialize \\( \\mathbf{D}^{(0)} \\) and \\( \\mathbf{P}^{(0)} \\) as random matrixes with unit Frobenious norm, \\( t = 0 \\);

> 2: **while** not converge **do**

> 3: \\( \\qquad \\) \\( t \\leftarrow t + 1 \\);

> 4: \\( \\qquad \\) **for** \\( i = 1 \: K \\) **do**

> 5: \\( \\qquad \\qquad \\) Update \\( \\mathbf{A}^{(t)}\_k \\) by (8);

> 6: \\( \\qquad \\qquad \\) Update \\( \\mathbf{P}^{(t)}\_k \\) by (10);

> 7: \\( \\qquad \\qquad \\) Update \\( \\mathbf{D}^{(t)}\_k \\) by (12);

> 8: \\( \\qquad \\) **end for**

> 9: **end while**

> **Output**: Analysis dictionary \\( \\mathbf{P} \\), synthesis dictionary \\( \\mathbf{D} \\).

---

The objective function in (5) is generally non-convex. We introduce a variable matrix A and relax (5) to the following problem:

\\[\\{\\mathbf{P}^\*,\\mathbf{A}^\*,\\mathbf{D}^\*\\} = \\arg\\min\_{\\mathbf{P},\\mathbf{A},\\mathbf{D}}
\\begin{cases}
&   \\sum^K\_{k=1} \\Big( \\| \\mathbf{X}\_i - \\mathbf{D}\_k \\mathbf{A}\_k \\|^2\_F \\\
& + \\tau \\| \\mathbf{P}\_k \\mathbf{X}\_k - \\mathbf{A}\_k \\|^2\_F \\\
& + \\lambda \\| \\mathbf{P}\_k \\mathbf{X}\_k \\|^2\_F \\Big),
\\end{cases}
\\]
\\[\\phantom{xxxxxxxxxxxxx}\\text{s.t.} \\quad \\|\\mathbf{d}\_i\\|^2\_2 \leq 1. \\phantom{xxxxxxxxxxxxx}(6) \\]

where \\( \\tau \\) is a scalar constant. All terms in the above objective function are characterized by Frobenius norm, and (6) can be easily solved. We initialize the analysis dictionary \\(\\mathbf{P}\\) and synthesis dictionary \\(\\mathbf{D}\\) as random matrices with unit Frobenius norm, and then alternatively update \\(\\mathbf{A}\\) and \\(\\{\\mathbf{D},\\mathbf{P}\\}\\). The minimization can be alternated between the following two steps.

(1) Fix \\( \\mathbf{D} \\) and \\( \\mathbf{P} \\), update \\( \\mathbf{A} \\)

\\[\\mathbf{A}^\* = \\arg \\min\_{\\mathbf{A}} \\sum^K\_{k=1} \\|\\mathbf{X}\_k - \\mathbf{D}\_k\\mathbf{A}\_k\\|^2\_F + \\tau \\|\\mathbf{P}\_k\\mathbf{X}\_k - \\mathbf{A}\_k\\|^2\_F.\\]
\\[\\phantom{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}(7)\\]

This is a standard least squares problem and we have the closed-form solution:

\\[\\phantom{xxxx}\\mathbf{A}^\*\_k = (\\mathbf{D}^T\_k\\mathbf{D}\_k + \\tau\\mathbf{I})^{-1} (\\tau \\mathbf{P}\_k \\mathbf{X}\_k + \\mathbf{D}^T\_k\\mathbf{X}\_k).\\phantom{xxxxx}(8)\\]

(2) Fix \\(\\mathbf{A}\\), update \\(\\mathbf{D}\\) and \\(\\mathbf{P}\\):

\\[
\\begin{cases}
\\mathbf{P}^\* = \\arg \\min\_{\\mathbf{P}} \\sum^K\_{k=1} \\tau \\|\\mathbf{P}\_k\\mathbf{X}\_k - \\mathbf{A}\_k\\|^2\_F + \\lambda \\|\\mathbf{P}\_k\\mathbf{\\bar{X}}\_k\\|^2\_F; \\\
\\mathbf{D}^\* = \\arg \\min\_{\\mathbf{D}} \\sum^K\_{k=1}       \\|\\mathbf{X}\_k - \\mathbf{D}\_k\\mathbf{A}\_k\\|^2\_F, \\quad \\text{s.t.} \\, \\|\\mathbf{d}\_i\\|^2\_2 \\leq 1.
\\end{cases}
\\]
\\[\\phantom{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}(9)\\]

The closed-form solutions of \\(\\mathbf{P}\\) can be obtained as:

\\[\\phantom{xxxx}\\mathbf{P}^\*\_k = \\tau \\mathbf{A}\_k\\mathbf{X}^T\_k(\\tau \\mathbf{X}\_k\\mathbf{X}^T\_k + \\lambda \\mathbf{\\bar{X}}\_k\\mathbf{\\bar{X}}^T\_k + \\gamma\\mathbf{I})^{-1},\\phantom{xxxx}(10)\\]

where \\(\\gamma = 10^{-4}\\) is a small number. The \\(\\mathbf{D}\\) problem can be optimized by introducing a variable  \\(\\mathbf{S}\\):

\\[
\\min\_{\\mathbf{D}, \\mathbf{S}} \\sum^K\_{k=1} \\|\\mathbf{X}\_k \\mathbf{D}\_k \\mathbf{A}\_k\\|^2\_F, \\quad \\text{s.t.} \\quad \\mathbf{D} = \\mathbf{S}, \\quad \|s\_i\|^2\_2 \\leq 1. \\quad (11)
\\]

The optimal solution of (11) can ben obtained by the ADMM algorithm:

\\[
\\begin{cases}
\\mathbf{D}^{(r+1)} = \\arg \\min\_{\\mathbf{D}} \\sum^K\_{k=1} \\left\\| \\mathbf{X}\_k - \\mathbf{D}\_k \\mathbf{A}\_k \\right\\|^2\_F + \\rho \\left\\| \\mathbf{D}\_k - \\mathbf{S}^{(r)}\_k + \\mathbf{T}^{(r)}\_k \\right\\|^2\_F \\\
\\mathbf{S}^{(r+1)} = \\arg \\min\_{\\mathbf{S}} \\sum^K\_{k=1} \\left\\| \\mathbf{D}^{(r+1)}\_k - \\mathbf{S}\_k + \\mathbf{T}^{(r)}\_k \\right\\|^2\_F, \\quad \\text{s.t.} \\quad \\|s\_i\\|^2\_2 \\leq 1, \\\
\\mathbf{T}^{(r+1)} = \\mathbf{T}^{(r)} + \\mathbf{D}^{(r+1)}\_k - \\mathbf{S}^{(r+1)}\_k, \\quad \\text{update } \\rho \\text{ if appropriate.}
\\end{cases}
\\]
\\[\\phantom{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}(12)\\]

In each step of optimization, we have closed form solutions for variables \\( \\mathbf{A} \\) and \\( \\mathbf{P} \\), and the ADMM based optimization of \\( \\mathbf{D} \\) converges rapidly. The training of the proposed DPL model is much faster than most of previous discriminative DL methods. The proposed DPL algorithm is summarized in Algorithm 1. When the difference between the energy in two adjacent iterations is less than 0.01, the iteration stops. The analysis dictionary \\( \\mathbf{P} \\) and the synthesis dictionary \\( \\mathbf{D} \\) are then output for classification.

One can see that the first sub-objective function in (9) is a discriminative analysis dictionary learner, focusing on promoting the discriminative power of \\( \\mathbf{P} \\); the second sub-objective function in (9) is a representative synthesis dictionary learner, aiming to minimize the reconstruction error of the input signal with the coding coefficients generated by the analysis dictionary \\( \\mathbf{P} \\). When the minimization process converges, a balance between the discrimination and representation power of the model can be achieved.

### 2.4 Classification scheme ###

In the DPL model, the analysis sub-dictionary \\( \\mathbf{P}^\*\_k \\) is trained to produce small coefficients for samples from classes other than k, and it can only generate significant coding coefficients for samples from class \\( k \\). Meanwhile, the synthesis sub-dictionary \\( \\mathbf{D}^\*\_k \\) is trained to reconstruct the samples of class \\( k \\) from their projective coefficients \\( \\mathbf{P}^\*\_k \\mathbf{X}\_k \\); that is, the residual \\( \\| \\mathbf{X}\_k - \\mathbf{D}^\*\_k \\mathbf{P}^\*\_k \\mathbf{K}\_k \\|^2\_F \\) will be small. On the other hand, since \\( \\mathbf{P}^\*\_k \\mathbf{X}\_k \\), \\( i \\neq k \\), will be small and \\( \\mathbf{D}^\*\_k \\) is not trained to reconstruct \\( \\mathbf{X}\_i \\), the residual will be much larger than.

In the testing phase, if the query sample \\( y \\) is from class \\( k \\), its projective coding vector by \\( \\mathbf{P}^\*\_k \\) (i.e. \\( \\mathbf{P}^*\_k \\mathbf{y} \\)) will be more likely to be significant, while its projective coding vectors by \\( \\mathbf{P}^\*\_i \\), \\( i \\neq k \\), tend to be small. Consequently, the reconstruction residual \\( \\| \\mathbf{y} - \\mathbf{D}^\*\_k \\mathbf{P}^\*\_k \\mathbf{y} \\|^2\_2 \\) tends to be much smaller than the residuals \\( \\| \\mathbf{y} - \\mathbf{D}^\*\_i \\mathbf{P}^\*\_i \\mathbf{y} \\|^2\_2 \\), \\( i \\neq k\\). Let us use the Extended YaleB face dataset [22] to illustrate this. (The detailed experimental setting can be found in Section 3.) Fig. 1(a) shows the \\( \\ell\_2 \\)-norm of the coefficients \\( \\mathbf{P}^\*\_k \\mathbf{y} \\), where the horizontal axis refers to the index of \\( \\mathbf{y} \\) and the vertical axis refers to the index of \\( \\mathbf{P}^\*\_k \\). One can clearly see that \\( \\| \\mathbf{P}^\*\_k \\mathbf{y} \\|^2\_2 \\) has a nearly block diagonal structure, and the diagonal blocks are produced by the query samples which have the same class labels as \\( \\mathbf{P} ^\*\_k \\). Fig. 1(b) shows the reconstruction residual \\( \\| \\mathbf{y} - \\mathbf{D}^\*\_k \\mathbf{P}^\*\_k \\mathbf{y} \\|^2\_2 \\). One can see that \\( \\| \\mathbf{y} - \\mathbf{D}^\*\_k \\mathbf{P}^\*\_k \\mathbf{y} \\|^2\_2 \\) also has a block diagonal structure, and only the diagonal blocks have small residuals. Clearly, the class-specific reconstruction residual can be used to identify the class label of \\( \\mathbf{y} \\), and we can naturally have the following classifier associated with the DPL model:

\\[
\\qquad \\quad identity( \\mathbf{y} ) = \\arg \\min\_i \\| \\mathbf{y} - \\mathbf{D}\_i \\mathbf{P}\_i \\mathbf{y} \\|\_2. \\qquad (13)
\\]

### 2.5 Complexity and convergence ###

**Complexity** In the training phase of DPL, \\( \\mathbf{A}\_k \\), \\( \\mathbf{P}\_k \\), and \\( \\mathbf{D}\_k \\) are updated alternatively. In each iteration, the time complexities of updating \\( \\mathbf{A}\_k \\), \\( \\mathbf{P}\_k \\), and \\( \\mathbf{D}\_k \\) are \\( O(mpn + m^3 + m^2n) \\), \\( O( mnp + p^3 + mp^2 ) \\), and \\( O( W( pmn + m^3 + m^2p + p^2m)) \\), respectively, where \\( W \\) is the iteration number in ADMM algorithm for updating \\( \\mathbf{D} \\). We experimentally found that in most cases \\( W \\) is less than 20. In many applications,
the number of training samples and the number of dictionary atoms for each class are much smaller
than the dimension \\( p \\). Thus the major computational burden in the training phase of DPL is on
updating \\( \\mathbf{P}\_k \\), which involves an inverse of a \\( p \\times p \\) matrix \\( \\left\\{ \\tau \\mathbf{X}\_k \\mathbf{X}^T\_k + \\lambda \\mathbf{\\bar{X}}\_k \\mathbf{\\bar{X}}^T\_k + \\gamma \\mathbf{I} \\right\\} \\). Fortunately, this matrix will not change in the iteration, and thus the inverse of it can be pre-computed. This greatly accelerates the training process.

In the testing phase, our classification scheme is very efficient. The computation of class-specific
reconstruction error \\( \\| \\mathbf{y} - \\mathbf{D}^\*\_k \\mathbf{P}^\*\_k \\mathbf{y} \\|\_2 \\) only has a complexity of \\( O(mp) \\). Thus, the total complexity of our model to classify a query sample is \\( O(Kmp) \\).

**Convergence** The objective function in (6) is a bi-convex problem for \\( \\{(\\mathbf{D},\\mathbf{P}),(\\mathbf{A})\\} \\), e.g., by fixing \\( \\mathbf{A} \\) the function is convex for \\( \\mathbf{D} \\), and \\( \\mathbf{P} \\), and by fixing \\( \\mathbf{D} \\) and \\( \\mathbf{P} \\) the function is convex for \\( \\mathbf{A} \\). The convergence of such a problem has already been intensively studied [23], and the proposed optimization algorithm is actually an alternate convex search (ACS) algorithm. Since we have the optimal solutions of updating \\( \\mathbf{A} \\), \\( \\mathbf{P} \\), and \\( \\mathbf{D} \\), and our objective function has a general lower bound 0, our algorithm is guaranteed to converge to a stationary point. A detailed convergence analysis can be found in our supplementary file.

It is empirically found that the proposed DPL algorithm converges rapidly. Fig. 2 shows the convergence curve of our algorithm on the AR face dataset [24]. One can see that the energy drops quickly and becomes very small after 10 iterations. In most of our experiments, our algorithm will converge in less than 20 iterations.

## 3 Experimental Results ##

**Omitted.** *Check [the original paper](http://papers.nips.cc/paper/5600-projective-dictionary-pair-learning-for-pattern-classification.pdf) if you need.*

## 4 Conclusion ##

We proposed a novel projective dictionary pair learning (DPL) model for pattern classification tasks. Different from conventional dictionary learning (DL) methods, which learn a single synthesis dictionary, DPL learns jointly a synthesis dictionary and an analysis dictionary. Such a pair of dictionaries work together to perform representation and discrimination simultaneously. Compared with previous DL methods, DPL employs projective coding, which largely reduces the computational burden in learning and testing. Performance evaluation was conducted on publically accessible visual classification datasets. DPL exhibits highly competitive classification accuracy with state-of-the-art DL methods, while it shows significantly higher efficiency, e.g., hundreds to thousands times faster than LC-KSVD and FDDL in training and testing.
