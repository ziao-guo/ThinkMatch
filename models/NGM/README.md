# NGM

Our implementation of the following paper:
* Runzhong Wang, Junchi Yan, Xiaokang Yang. "Neural Graph Matching Network: Learning Lawler's Quadratic Assignment Problem with Extension to Hypergraph and Multiple-graph Matching." _TPAMI 2021_.
  [[paper]](https://ieeexplore.ieee.org/document/9426408), [[project page]](http://thinklab.sjtu.edu.cn/project/NGM/index.html)

NGM proposes a learnable solver to the Lawler's Quadratic Assignment Problem (Lawler's QAP), which is the most general form of graph matching. 
The learning of Lawler's QAP is achieved by firstly transforming the Lawler's QAP to the so-called association graph, and solving Lawler's QAP can be equivalently transformed into a vertex classification problem on the association graph. 
Finally, Graph Neural Networks are adopted to solve vertex classification problem as a common technique.

NGM has the following components:
* (If images as the input) VGG16 CNN to extract image features
* (If images as the input) Delaunay triangulation to build graphs
* (If images as the input) Building affinity matrix efficiently via Factorized Graph Matching (FGM)
* Solving the resulting Quadratic Assignment Problem (QAP) by the proposed Graph Neural Network solver
* Supervised learning based on cross-entropy (also known as "permutation loss")

We also show that such a general scheme can be extended to hyper-graph matching (by building a so-called association hypergraph) and multi-graph matching (by adding a spectral multi-matching head).

The first version of **NGM/NHGM/NMGM** is developed in 2019 with VGG16 CNN in line with GMN. We further develop an improved version **NGM-v2/NHGM-v2/NMGM-v2** in 2021 by simply modifying the VGG16 to the improved backbone developed by BBGM.

To summarize, here we include the following models:
* the original models developed in 2019:
  * **NGM**:  two-graph matching/Lawler's QAP solver with VGG16
  * **NHGM**: hyper-graph matching solver with VGG16
  * **NMGM**: multi-graph matching solver with VGG16
* the improved v2 models developed in 2021:
  * **NGM-v2**: two-graph matching/Lawler's QAP solver with VGG16+SplineConv
  * **NHGM-v2**: hyper-graph matching solver with VGG16+SplineConv
  * **NMGM-v2**: multi-graph matching solver with VGG16+SplineConv
  
We also implement the partial matching handling (PMH) approach in the following paper:
* Runzhong Wang, Ziao Guo, Shaofei Jiang, Xiaokang Yang, Junchi Yan. "Deep Learning of Partial Graph Matching via Differentiable Top-K." _CVPR 2023_.
  [[paper]](https://openaccess.thecvf.com/content/CVPR2023/html/Wang_Deep_Learning_of_Partial_Graph_Matching_via_Differentiable_Top-K_CVPR_2023_paper.html)

The PMH approach is utilized to resolve partial graph matching problem, i.e., graph matching with the existence of outliers.
It includes a topk-GM algorithm to suppress matchings with low confidence, and a AFA module to predict the number of inliers k.
We choose NGMv2 as the backend graph matching network, and the file ``model_v2_topk.py`` can be referred to for details.
The implementation of topk-GM algorithm and AFA modules is shown in ``src.lap_solvers.sinkhorn_topk.py`` and ``src.k_pred_net.py`` respectively.

The partial graph matching problem and the permutation constraints can also be explicitly formulated and resolved via the approach proposed in the following paper:
* Runzhong Wang, Yunhao Zhang, Ziao Guo, Tianyi Chen, Xiaokang Yang, Junchi Yan. "LinSATNet: The Positive Linear Satisfiability Neural Networks." _ICML 2023_.
  [[paper]](https://openreview.net/forum?id=D2Oaj7v9YJ)
  
The apporach can project input variables with non-negative constraints turough a differentiable satisfiability layer based on an extension of the classic Sinkhorn algorithm involving jointly encoding multiple sets of marginal distributions. 
We choose NGMv2 as the backend graph matching network, and the file ``model_v2_linsat.py`` can be referred to for details.
The implementation of LinSATNet is shown in ``src.linsatnet.py``.


## Benchmark Results
### PascalVOC - 2GM
* NGM 
  
  experiment config: ``experiments/vgg16_ngm_voc.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1jwlkkSnbHLHHIrWyBnURKvTmI5Iiz8IR/view?usp=sharing)

* NHGM

  experiment config: ``experiments/vgg16_nhgm_voc.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1FuTeQOlZXrao6eELbdIt2oFQQ3ody1Il/view?usp=sharing)

* NGM-v2

  experiment config: ``experiments/vgg16_ngmv2_voc.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1jyb5y16OfnNDIPfuDb17wsOjz8oH6av3/view?usp=sharing)

* NHGM-v2

  experiment config: ``experiments/vgg16_nhgmv2_voc.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1I42Z7sW5oQdQ9Grvb9gm-lQIxbYFt0EN/view?usp=sharing)

Since NMGM does not support partial matching, we do not evaluate NMGM on Pascal VOC.

| model                  | year | aero   | bike   | bird   | boat   | bottle | bus    | car    | cat    | chair  | cow    | table  | dog    | horse  | mbkie  | person | plant  | sheep  | sofa   | train  | tv     | mean   |
| ---------------------- | ---- | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| [NGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2019 | 0.5010 | 0.6350 | 0.5790 | 0.5340 | 0.7980 | 0.7710 | 0.7360 | 0.6820 | 0.4110 | 0.6640 | 0.4080 | 0.6030 | 0.6190 | 0.6350 | 0.4560 | 0.7710 | 0.6930 | 0.6550 | 0.7920 | 0.8820 | 0.6413 |
| [NHGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2019 | 0.5240 | 0.6220 | 0.5830 | 0.5570 | 0.7870 | 0.7770 | 0.7440 | 0.7070 | 0.4200 | 0.6460 | 0.5380 | 0.6100 | 0.6190 | 0.6080 | 0.4680 | 0.7910 | 0.6680 | 0.5510 | 0.8090 | 0.8870 | 0.6458 |
| [NGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | 0.6184 | 0.7118 | 0.7762 | 0.7875 | 0.8733 | 0.9363 | 0.8770 | 0.7977 | 0.5535 | 0.7781 | 0.8952 | 0.7880 | 0.8011 | 0.7923 | 0.6258 | 0.9771 | 0.7769 | 0.7574 | 0.9665 | 0.9323 | 0.8011 |
| [NHGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | 0.5995 | 0.7154 | 0.7724 | 0.7902 | 0.8773 | 0.9457 | 0.8903 | 0.8181 | 0.5995 | 0.8129 | 0.8695 | 0.7811 | 0.7645 | 0.7750 | 0.6440 | 0.9872 | 0.7778 | 0.7538 | 0.9787 | 0.9280 | 0.8040 |

### Willow Object Class - 2GM & MGM
* NGM 
  
  experiment config: ``experiments/vgg16_ngm_willow.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1lpZFO7I7BOz_syfnxIOC35FccZoE61rv/view?usp=sharing)
  
* NHGM

  experiment config: ``experiments/vgg16_nhgm_willow.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1EqKSczxHbDIaEqu8C2ZaLjced3X6OKqI/view?usp=sharing)

* NMGM
  
  experiment config: ``experiments/vgg16_nmgm_willow.yaml``
  
  pretrained model: [google drive](https://drive.google.com/file/d/1dsOmB_HiKJCE9UOSMwsALPXHNYNDxZ3d/view?usp=sharing)
  
* NGM-v2

  experiment config: ``experiments/vgg16_ngmv2_willow.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1ZN8oZZOEGk1Ax_pL75jMbq1Mke9WPkR7/view?usp=sharing)
  
* NHGM-v2

  experiment config: ``experiments/vgg16_nhgmv2_willow.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1xEEuaeHrM9SvoS2iSmhpa0wAFPOcuzvS/view?usp=sharing)

* NMGM-v2

  experiment config: ``experiments/vgg16_nmgmv2_willow.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1UMy7i0-LSAHMB2PBtHSbqkMSSFTLMKy9/view?usp=sharing)

| model                    | year | remark          | Car    | Duck   | Face   | Motorbike | Winebottle | mean   |
| ------------------------ | ---- | --------------- | ------ | ------ | ------ | --------- | ---------- | ------ |
| [NGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2019 | -               | 0.8420 | 0.7760 | 0.9940 | 0.7680    | 0.8830     | 0.8530 |
| [NHGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2019 | -               | 0.8650 | 0.7220 | 0.9990 | 0.7930    | 0.8940     | 0.8550 |
| [NMGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2019 | -               | 0.7850 | 0.9210 | 1.0000 | 0.7870    | 0.9480     | 0.8880 |
| [NGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | -               | 0.9740 | 0.9340 | 1.0000 | 0.9860    | 0.9830     | 0.9754 |
| [NHGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | -               | 0.9740 | 0.9390 | 1.0000 | 0.9860    | 0.9890     | 0.9780 |
| [NMGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | -               | 0.9760 | 0.9447 | 1.0000 | 1.0000    | 0.9902     | 0.9822 |

### SPair-71k - 2GM

* NGM 
  
  experiment config: ``experiments/vgg16_ngm_spair71k.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1U3SLg-oU9vopEIs1pcIwtZH9giGuqWE4/view?usp=sharing)

* NGM-v2

  experiment config: ``experiments/vgg16_ngmv2_spair71k.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1Yk0U7C_C_I7FXXzWACCkUVQK0h5qjNXP/view?usp=sharing)
  
* NHGM-v2

  experiment config: ``experiments/vgg16_nhgmv2_spair71k.yaml``

  pretrained model: [google drive](https://drive.google.com/file/d/1VSakqb9m6gMYC9ByiOXxmU54ysvNj3qm/view?usp=sharing)

| model   | year | aero   | bike   | bird   | boat   | bottle | bus    | car    | cat    | chair  | cow    | dog    | horse  | mtbike | person | plant  | sheep  | train  | tv     | mean |
| ------- | ---- | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| [NGM](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm)     | 2019 | 0.6644 | 0.5262 | 0.7696 | 0.4960 | 0.6766 | 0.7878 | 0.6764 | 0.6827 | 0.5917 | 0.7364 | 0.6391 | 0.6066 | 0.7074 | 0.6089 | 0.8754 | 0.6387 | 0.7979 | 0.9150 | 0.6887 |
| [NGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm)  | 2021 | 0.6877 | 0.6331 | 0.8677 | 0.7013 | 0.6971 | 0.9467 | 0.8740 | 0.7737 | 0.7205 | 0.8067 | 0.7426 | 0.7253 | 0.7946 | 0.7340 | 0.9888 | 0.8123 | 0.9426 | 0.9867 | 0.8020 |
| [NHGM-v2](https://thinkmatch.readthedocs.io/en/latest/guide/models.html#ngm) | 2021 | 0.6202 | 0.5781 | 0.8642 | 0.6846 | 0.6872 | 0.9335 | 0.8081 | 0.7656 | 0.6919 | 0.7987 | 0.6623 | 0.7171 | 0.7812 | 0.6953 | 0.9824 | 0.8444 | 0.9316 | 0.9926 | 0.7799 |

## Run QAPLIB Experiments

Our proposed NGM is capable of handling pure combinatorial optimization problems from [QAPLIB](https://www.opt.math.tugraz.at/qaplib/).
We train one model on the problems with the same prefix (which means that they represent the same category of problems), and we set the objective function as the loss, resulting in a self-supervised learning scheme. 
To further elevate the performance on QAPLIB, we adopt randomly sampling based on Gumbel Sinkhorn.

### About the Performance
The quality of solutions of our method is comparative and even better compared with the classic learning-free optimization method [Sinkhorn-JA](https://epubs.siam.org/doi/abs/10.1137/18M1196480), and the inference time is significantly reduced. For more details, please visit the [project page](http://thinklab.sjtu.edu.cn/project/NGM/index.html).

### Only the NGM Model is Considered for QAPLIB
Since NGM-v2 only improves in image feature extractor which is not relevant to QAPLIB problems, we only test NGM on QAPLIB. Besides, the QAPLIB instances are equivalent to the formulation of two-graph matching problems, thus the hyper-graph and multi-graph variants are not considered for QAPLIB.

### How to Run
For experiments on QAPLIB, run
```
python train_eval_qap.py --config experiments/ngm_qaplib.yaml
```
and the QAPLIB dataset shall be automatically downloaded. By default, training is run on the first category (bur) and testing is run on all datasets. You may modify the ``TRAIN.CLASS`` parameter for different problems.

NOTICE: you may need a large GPU memory (up to 48GB in our experiment) to run NGM on QAPLIB instances up to 150. Adjust the two parameters ``MAX_TRAIN_SIZE``, ``MAX_TEST_SIZE`` accordingly to fit the size of your GPU memory.

## File Organization
```
├── geo_edge_feature.py
|   the implementation of used geometric features 
├── gnn.py
|   the implementation of used graph neural networks 
├── hypermodel.py
|   the implementation of training/evaluation procedures of NHGM
├── hypermodel_v2.py
|   the implementation of training/evaluation procedures of NHGMv2
├── mgmmodel.py
|   the implementation of training/evaluation procedures of NMGM
├── model.py
|   the implementation of training/evaluation procedures of NGM (can handle both graph matching and QAP)
├── model_config.py
|   the declaration of model hyperparameters
├── model_v2.py
|   the implementation of training/evaluation procedures of NGMv2 (can only handle graph matching)
├── model_v2_linsat.py
|   the implementation of training/evaluation procedures of NGMv2 combined with LinSATNet
└── model_v2_topk.py
    the implementation of training/evaluation procedures of NGMv2 combined with topk-GM algorithm and partial matching handling AFA modules
```
some layers are borrowed from ``models/BBGM``

## Credits and Citation

Please cite the following paper if you use this model in your research:
```
@article{WangPAMI21,
  author = {Wang, Runzhong and Yan, Junchi and Yang, Xiaokang},
  title = {Neural Graph Matching Network: Learning Lawler's Quadratic Assignment Problem with Extension to Hypergraph and Multiple-graph Matching},
  journal = {IEEE Transactions on Pattern Analysis and Machine Intelligence},
  year={2021}
}

@InProceedings{WangCVPR23,
    author={Wang, Runzhong and Guo, Ziao and Jiang, Shaofei and Yang, Xiaokang and Yan, Junchi},
    title={Deep Learning of Partial Graph Matching via Differentiable Top-K},
    booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    year={2023},
}

@InProceedings{WangICML23, 
   title={LinSATNet: The Positive Linear Satisfiability Neural Networks},
   author={Runzhong Wang, Yunhao Zhang, Ziao Guo, Tianyi Chen, Xiaokang Yang, Junchi Yan},
   booktitle={ICML},
   year={2023}
}
```
