# ET-BERT笔记
### 1. 目标问题
ET-BERT尝试解决如下问题：
- 显式的fingerprint（service、certificate等）生成困难；基于统计特征的方法泛化能力有限
- 基于traffic数据的ML方法（CNN\RNN等）依赖大量有标注的数据
- 预训练方法不再依赖大量有标注数据，但此前的预训练方法（PERT）并未基于网络流量的特性设计预训练任务。

### 2. 关键创新
针对上述问题，ET-BERT围绕BURST（source和destination之间的双向session），设计了byte和BURST层面的预训练任务，分别在packet层级和流层级的数据上进行微调。

### 3. 工作流程
![avatar](main/images/etbert.png)
##### 1. Datagram2Token
从.pcap文件生成预训练和微调所用的文本，并通过tokenizer生成token。生成预训练和微调数据的方式略有不同。
生成预训练corpus的流程如下：
![avatar](main/images/pretrain_process.png)
作者提出了两种微调策略：在单个packet级别的数据上微调和在flow级别的数据上微调。为实现流量数据的切分，使用了SplitCap工具。
![avatar](main/images/finetune_process.png)
##### 2. 预训练
BURST可以视作在source IP（sIP）和destination IP（dIP）之间通过某端口的一段时间内的双向会话，包含从sIP到dIP以及反向的数据流。不同的会话由不同的应用、代理等发起，因而虽然加密数据的payload自身缺乏语义，但将捕获的包整理为BURST，对数据集进行分割，就能得到隐含类别语义的数据段。
作者将一个BURST视为句子，其中的字节数据视为word，利用BERT进行预训练，设计了两个预训练任务：Masked BURST（相当于next word prediction）和Same-origin BURST prediction（相当于next sentence prediction）
##### 3. 微调
作者提出了两种fine-tune策略，分别命名为ET-BERT(flow)和ET-BERT(packet)。两种策略的不同在于，前者提取一个flow的前5个包，在flow上微调，以基于流分类的方法完成任务并进行评价，用以和FS-Net等流方法进行比较。后者在单个packet数据上进行微调，基于packet分类完成下游任务并进行评价，可以和Deeppacket等packet层级的ML方法进行比较。

### 附录：基于UER-py使用和管理类BERT模型
链接：https://github.com/dbiir/UER-py
ET-BERT使用UER-py对数据生成和模型训练、微调进行管理，基本继承了UER-py的项目结构。UER-py提供了包括但不限于BERT系列模型（BERT、T5等）的一系列资源，并提供了数据集生成、模型预训练、微调等过程的一系列方法，用户通过输入命令传入参数，以实现所需的功能。UER的结构与功能如下图所示。
![avatar](main/images/UER_PY.png)
