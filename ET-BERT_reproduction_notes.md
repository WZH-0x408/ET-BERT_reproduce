# ET-BERT笔记
### 1. 目标问题
ET-BERT尝试解决如下问题：
- 显式的fingerprint（service、certificate等）生成困难；基于统计特征的方法泛化能力有限
- 基于traffic数据的ML方法（CNN\RNN等）依赖大量有标注的数据
- 预训练方法不再依赖大量有标注数据，但此前的预训练方法（PERT）并未基于网络流量的特性设计预训练任务。

### 2. 关键创新
针对上述问题，ET-BERT围绕BURST（source和destination之间的双向session），设计了byte和BURST层面的预训练任务，分别在packet层级和流层级的数据上进行微调。

### 3. 工作流程


### 附录：基于UER-py使用和管理类BERT模型