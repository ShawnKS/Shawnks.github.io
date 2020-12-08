---
layout: post
title: "论文解读 city transfer related"
# subtitle: ' "拿什么拯救你，城市计算"'
date: 2020-12-8 21:29:00
author: "Shawn"
header-style: text
catalog: true
tags:
  - 机器学习
  - 迁移学习
  - 城市计算
---

**Cross-CityTransferLearningforDeep Spatio-TemporalPrediction** HKUST

>  First, we design a region matching function to link each target city region to a similar source region based on the short period of service data or correlated auxiliary data if applicable. Then, in our proposed network structure, to catch the spatio-temporal patterns hidden in the service data, ConvLSTM layers [12] are ﬁrstly stacked. Afterward, to encode region representation, we newly add a Conv2D layer with 1×1 ﬁlter, which is the key and fundamental component of our network to make region-level transfer feasible. Finally, the discrepancy between region representations of the inter-city similar regions is minimized during the network parameter learning for the target city, so as to enable region-level cross-city knowledge transfer. 

一种类似TGCN的结构

> we propose a deep transfer learning method RegionTrans including three stages. First, we learn an inter-city region matching function that links each target region to a similar source region. Second, a neural network structure is designed to extract region-level spatio-temporal patterns. Finally, an optimization process is proposed to facilitate region-level transfer between cities.
>
> The ﬁrst step of RegionTrans is to ﬁnd a matching functionM : CD → CD0 to map each region of the target city D to a certain region of the source city D0. The objective is to ﬁnd the source region having the similar spatio-temporal pattern with the target region. To this end, we propose two strategies to ﬁndM. 

直接投射到目标域空间里？

重点

> With the proposed network structure, we train a deep model in the source city D0 with its rich spatio-temporal service data. We denote θD0 as the network parameters learned from the source city. Then, with θD0 as the pre-trained network parameters, we propose a region-based cross-city optimization algorithm to reﬁne the network parameters on the target cityD, considering a short periodTD of the service data in the target cityDand the inter-city region matching functionM. 
>
> WhenreﬁningthenetworkparameterforthetargetcityD,theﬁrstobjectiveistominimizeprediction error on the target city: 
>
> Given the matching functionM, the second objective of our optimization is to minimize representation divergence between matched region pairs. More speciﬁcally, for each time-stamp t ∈TD, we try to minimize the squared error between the network hidden representations of the target region and its matched source region. Formally, the second objective is as follows:

loss两部分组成，一部分是在源数据已经训练好的模型上把target放上去(其实很有拆一部分出来再补数据的感觉)的loss，另一部分是用了matching算法的(更迷)

核心matching方法(...)

> Matching with a short period of service data. While the target city has only a little service data, this could still provide hints to buildM. We focus on the time span when both source and target cities have service data (i.e.,TD), then calculate the correlations (e.g., Pearson coefﬁcient) between each target region and source region with the corresponding service data. Finally, for each target region, we choose the source region with the largest correlation value. 
>
> Matchingwithalongperiodofauxiliarydata(ifapplicable). As there is little service data in the target city, the above service-data-based correlation similarity between a source region and a target regionmaynotbeveryreliable. Inreality,sometimeswecanﬁndanotheropenly-accessibleauxiliary data that correlates with the service data, which may help calculate the inter-city region similarity more robustly. For example, to predict crowd ﬂow, public social media check-ins can be a useful proxy according to literature [16]. That is, instead of the short period of crowd ﬂow data, we use the long period of openly available check-in data to build the correlation between two regions. 

总结就是俩都很扯

