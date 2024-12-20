---
title: 
date: 2023-07-30 10:34
tags: ['tag']
---

## Digital Twin Model Updating

## Regarding Architecture

- Architectural style ⭢ event-oriented architecture)
  - point-to-point (PtP) vs Pub/Sub. Pub/sub is better for scalability.

## Paper to Read #1

(_[kesselsRealtimeParameterUpdating2023](zotero://select/library/items/2XWR62J3)_, [link](https://doi.org/10.1007/s11071-023-08354-5), [DOI](https://doi.org/10.1007/s11071-023-08354-5), [zolib](https://www.zotero.org/irosyadi/items/2XWR62J3))

  - NOTE: This is **FP model** updating.
  - Another challenge is related to the ever-present mismatch between measurements of a physical system and corresponding predictions of its DT. For the DT to reach its full potential, this mismatch should be minimal throughout the system’s operational life . This challenge of giving DTs life-long learning capabilities is referred to as autonomous model updating and is considered in this paper.
  - the aforementioned mismatch between measurement and model predictions is contributed to three distinct model error sources; **incorrect model structure**, discretization errors, and/or errors due to incorrect parameter values. This paper focuses on model parameter updating as to minimize the last error source.
  - Given the intended application in a general digital twin context, key requirements for an autonomous model updating procedure are defined as follows: (1) although control and SHM will not be considered in this paper yet, we aim to develop a model updating methodology applicable in the context of model-based control and/or SHM; therefore, updating should be performed in real-time, (2) parameter updates should be physically interpretable to enable insightful SHM, (3) updating should be applicable to (complex) nonlinear dynamical models, and (4) the updating method should be suitable for parametric models (not necessarily of dynamical systems) derived using FPs, FE/multibody software, or multidisciplinary software tools.

Approaches for model parameter updating found in literature are divided in four types.

- The first type concerns updating methods originating from the field of structural dynamics, see, e.g., [9, 13] for an overview. Although some exceptions exist, see [14,15,16,17], these updating methods are typically limited to linear(ized) systems [8, 18]. Furthermore, these methods are based on expensive iterative sensitivity methods [16, 17, 19,20,21,22,23,24,25] or physically non-interpretable direct methods [21, 26,27,28]. Consequently, these techniques do not meet the four requirements for model updating for DTs as mentioned above and are thus not regarded as a viable solution to the problem considered here.
- Alternatively, system identification techniques are used to find a model that describes measured data. For an overview of system identification techniques see, for example, [29,30,31]. Note that, in contrast to model updating in the field of, typically linear(ized), structural dynamics, system identification has been developed for nonlinear systems [32, 33]. However, due to the fact that in system identification generic model classes are fitted to data, the identified models are difficult to interpret from a physical perspective [34] and thus, do not satisfy the above requirements.
- A third type of parameter updating techniques concerns filter-based (or observer-based) methods. Examples are the vanilla [35], extended [36, 37], and unscented Kalman filter [38,39,40], Gaussian filters [41, 42], and particle filters [43]. Although these approaches are often used for DTs, for complex systems (i.e., systems with many states and/or parameters), filter methods might suffer from relatively high computational costs, prohibiting online updating (especially if the required sampling frequency is relatively high) [43]. Furthermore, filter-based methods require an initial guess for the parameter estimates. If this information is not available or sudden changes to the parameter values occur, filter methods might end up in local minima. To avoid this, initial parameter values may be estimated using a coarse global parameter estimation method [38] at a significant cost of computation time. Finally, filter-based updating methods typically require direct accessibility to closed-form EoMs of the dynamical model for the user. Consequently, filter-based methods cannot be directly applied to simulation models (as obtained from, e.g., FE/multibody software packages, or Matlab-based packages as Simulink or Simscape) that may be used to constitute a digital twin.
- The fourth approach to model updating employs machine learning (ML). Here, again, we can distinguish between,
  - firstly, identification, i.e., identifying a system without using a FP reference model, and,
  - secondly, updating (parameters of) a predefined reference model. An example of the former is the work of Li on identifying nonlinear normal modes using physics-integrated deep learning [44].
  - An other example of ML-based identification is the work by Karniadakis et al. [45,46,47] on physics-informed neural networks (PINNs) that is also adopted by other researchers [48]. In this method, coefficients of (partial) differential equations governing the measured system are learned using a neural network. In general, the learned coefficients of these (partial) differential equations, however, do not refer to directly interpretable physical quantities, e.g., masses, damping, and stiffness constants. Additionally, to update a set of coefficients using measured data, a neural network needs to be trained, which can take significant computation time and hence, impede real-time updating. This holds especially for models consisting of a large number of states and/or parameters.
  - An other ML-based identification methodology is the sparse identification of nonlinear dynamics (SINDy) algorithm developed by Brunton et al. [49,50,51,52]. Similar to PINNs, this method finds (a sparse set of) coefficients of differential equations, which may not correspond to physical quantities, again hindering physical interpretation. Note that this argument holds for most system identification methods (based on ML or not).
  - A ML-based method more closely related to (physically interpretable) model updating is found in the work of Willcox et al. [53,54,55,56,57]. Here, a (FP) model is selected from a predefined library of models, representing systems with various fault types, using an optimal decision tree. Due to a prerequisite library of models, the ‘updated’ model is, however, limited to a discrete set of models that are manually defined by the user, and consequently is of limited accuracy.
  - Other ML-based methodologies aim at finding, online, the (global) parameter values corresponding to a measured signal (in a continuous domain) using, for example, iterative genetic algorithms [58, 59]. In this case, however, the (genetic) search over the parameter space takes considerable computational effort, rendering real-time updating impossible [38].
  - As an alternative, the inverse problem of estimating physically interpretable parameter values in a continuous domain using measurement data can be solved using an inverse mapping model (IMM). Here, the IMM directly maps, with small computational costs, a set of measured features, describing the (dynamical) behavior of a system, to a set of corresponding physically-interpretable parameter values of the reference model. In the literature, (trained) Artificial Neural Networks (ANNs) have been found suitable to constitute these IMMs due to their generic function approximation characteristics [18, 60,61,62,63]. Consequently, this methodology was coined the neural network updating method (NNUM) by Atalla [18]. Note that, since other generic function approximation algorithms may also be used to constitute IMMs, the authors prefer to refer to this methodology as the inverse mapping parameter updating (IMPU) method. For example, in recent work of the authors, Gaussian processes are used as IMMs, with the added benefit of enabling uncertainty quantification for the estimated parameter values [64]. The main qualitative advantage of this method with respect to previously discussed parameter updating methods is that online parameter updating is enabled even for large-scale systems with high sampling frequencies. This is enabled by shifting a significant majority of the computation time to an offline (training) phase, whereas the required online (inference) computations are extremely efficient and scale marginally with the complexity of the system. Although this IMPU methodology thus satisfies requirements 1 (real-time updating) and 2 (physically interpretable updating) for a broad range of systems and their DTs, this IMPU methodology has, to the best of the authors’ knowledge, however, only been applied to linear(ized) dynamical systems. Also note that the IMPU method can be applied on parametric models derived using any of the aforementioned model derivation options (first-principles, FE/multibody software, or multi-disciplinary software packages), yielding valuable flexibility in the modeling of the DT.

## Paper to Read #2

(_[chenDigitalTwinsModel2022](zotero://select/library/items/JCSBXH8R)_, [link](https://www.sciencedirect.com/science/article/pii/S0360544222009434), [DOI](https://doi.org/10.1016/j.energy.2022.124040), [zolib](https://www.zotero.org/irosyadi/items/JCSBXH8R))

NOTE: BLS learning system, layak diperdalam

- However, to apply the established models to the DT, the models should have the updating and renewable capability for the information interaction with real physical system. In fact, few studies have applied moving window algorithm to update the prediction model [41]. But the principle of the moving window algorithm is to complement new data to the original data sets and retrain or refit the model to make it more precise. It may consume many computational resources and too much time for the digital twins.
- To fill these gaps in current research, this study aims to construct a novel modeling method, which can be updated in real-time, and reduce the computational resources and time simultaneously. The broad learning system (BLS) may provide possible efficient solutions. Broad learning system can provide an alternative means of learning to replace the deep learning structure, because deep learning suffers from a time-consuming training process and has a lot of hyperparameters to be trained [42]. The structure of broad learning system is quite simple and BLS has fewer hyperparameters. More importantly, because the composition principle of broad learning system is marvelous, BLS has a natural advantage in model updating. Based on the above characteristic, BLS has been already applied in numerous fields [[43], [44], [45], [46]] and has the potential to be used in the HVAC modeling.

## Paper to Read 3

(_[wangDigitalTwinRotating2019](zotero://select/library/items/R6RHS4H6)_, [link](https://doi.org/10.1080/00207543.2018.1552032), [DOI](https://doi.org/10.1080/00207543.2018.1552032), [zolib](https://www.zotero.org/irosyadi/items/R6RHS4H6))

NOTE: model updating on rotor, but finite element based modeling

## Paper to Read 4

(_[zhangUpdateMethodDigital2023](zotero://select/library/items/FTHZ8WUY)_, [link](https://www.sciencedirect.com/science/article/pii/S0736584522001636), [DOI](https://doi.org/10.1016/j.rcim.2022.102481), [zolib](https://www.zotero.org/irosyadi/items/FTHZ8WUY))  
![](https://ars.els-cdn.com/content/image/1-s2.0-S0736584522001636-gr7.jpg)

Based on the above work, a complete closed loop could be constructed. The closed loop of tool wear model update is expressed as follows. At first, various sensors collect the signals related with tool wear from physical machine tools. Then, the tool wear could be calculated based on the mapping relationships between the signals and tool wear. Next, the values of tool wear could be used to update the adjustable parameters in the mechanism models. Finally, the model could contribute to tool wear monitoring, tool wear prediction and some other services.

## Paper to Read 5

(_[birkAutomaticGenerationUpdating2022](zotero://select/library/items/XT3GFT6Z)_, [link](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858), [DOI](https://doi.org/10.3389/fcteg.2022.954858), [zolib](https://www.zotero.org/irosyadi/items/XT3GFT6Z))

NOTE: overview of DT model updating

To ensure that the DT can accurately track the state of the physical system in real-time, it is important to update the DT. Model updating can be studied from various perspectives such as

- soft sensor adaptation ([Kadlec et al., 2011](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B53)),
- concept drift adaptation ([Gama et al., 2014](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B33)),
- state estimation ([Maasoumy et al., 2013](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B66)),
- adaptive control ([Dougherty and Cooper, 2003](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B30)), and
- disturbance model adaptation ([Wang et al., 2012](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B112)).

In AutoTwin, DTs need to reflect the state of the asset with the required application accuracy throughout their life-cycle. Thus, it is essential to continuously update the DT to meet the changing needs and improve performance in terms of efficiency, flexibility, and reliability. DT updates can be done manually by a user (e.g., by replacing altered components with updated ones) or automatically. The automatic approach has the advantage that the validity of the DT is not dependent on the user’s knowledge of the process, but on the ability of the DT for self-adaptation (see [Tomforde et al. (2014)](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B106)).

Automatic DT updates can be triggered by several events such as physical component wear, change of components, process reconfiguration, or as a response to extrinsic parameters. Changes in the plant can be identified by the monitoring concept. Monitoring enables the identification of these changes by continuously comparing the output data of DT with those of the real plant and allows to validate that the plant in its actual status still meets all requirements ([Zipper et al., 2018](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B121)). Furthermore, periodic model validation is an essential part of any automatic DT updating scheme in order to validate whether the DT still captures process behaviour accurately (runtime verification; [Bu et al. (2011)](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B19)).

There are several methods for updating DTs. The approach applied to update the model depends on the size of the data used for updating and the number of parameters that should be updated.  
Some of the most common approaches applied to update the models are based on machine learning algorithms, such as

- optimal trees ([Kapteyn et al., 2020b](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B55)),
- probabilistic learning on manifolds ([Ghanem et al., 2020](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B37)), or
- Gaussian processes ([Chakraborty and Adhikari, 2021](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B23)).  
For ANN based updating algoirthin
- adaptive ANNs are used to design the DTs and adapt them over time through continuous learning ([Reed et al., 2021](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B89)).
- Bayesian networks can be employed to create and update the DTs. In these networks, the parameters are updated in some ways, for example, the prior known parameters can be updated in real-time by a Gaussian particle filter, while Dirichlet process mixture models can be applied to update the unknown parameters, making the model have the ability to self-updating the structure ([Yu et al., 2021](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B117)).
- Bayesian algorithms are also used for online adaption of DTs and soft sensors through an automatic mechanism of bias updating based on continuous monitoring of the mean and standard deviation of the prediction error ([Sangoi et al., 2021](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B92)). A combination of Bayesian state estimation and a library of component-based models can be applied to create and update data-driven physics-based digital twins ([Kapteyn et al., 2020a](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B54)). The component-based models scale to large and complex assets, while the construction of a model library enables flexible and expressive model adaptation via parametric modification and component replacement.
- Furthermore, updating the DTs can be regarded as a solution to an optimization problem that minimizes the difference between the outputs of the DT and the physical system. The optimization algorithms face great challenges to meet the real-time requirements in such cases. If the number of parameters is small, then some optimization approaches such as methods based on parameter sensitivity analysis ([Wang et al., 2019](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B111)) and differential evolution for parameter estimation in a sliding window ([Ohenoja et al., 2018](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B82); [Nikula et al., 2020](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B80)) may be appropriate. When a system contains high-dimensional variables, large computational overheads and memory issues could lead to inefficient results. In these situations, suitable approaches include decomposing the plant into meaningful blocks associated with a physical unit and using adaptive identification methods for every block ([He et al., 2019](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B42)), model order reduction ([Chinesta et al., 2011](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B26); [Quarteroni and Rozza, 2014](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B88); [Kapteyn et al., 2020b](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B55); [Chinesta et al., 2020](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B25)), or using surrogate models or meta models ([Yang et al., 2016](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B115); [Nikula et al., 2020](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B80); [Chakraborty et al., 2021](https://www.frontiersin.org/articles/10.3389/fcteg.2022.954858/full#B22)).

## References