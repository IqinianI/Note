## A Simple Framework for Contrastive Learning of Visual Representations

#### 框架和算法实现
![[Pasted image 20220412090857.png]]

![[Pasted image 20220412090914.png]]

![[Pasted image 20220409170328.png]]
#### 重点
1. 正负样本选取
	 - 一个batch(N)里随机选取一张作为锚点
	 - 所有数据做两次不同的增强(2N)
	 - 锚点两次不同的增强，作为正样本对(2)
	 - 其他数据，为负样本对(2N-2)
2. Data Augmentation
	- cropping + color distortion（组合的增强比单一增强效果好）

3. projection head
	- 在representation和loss间加入*可学习* 的**non-linear projection**
	- non-linear project   
		1. 单层MLP+ReLU
		2 ![[Pasted image 20220409173647.png]]
		3. encoder后h会保留和数据增强相关的信息，非线性层就是去掉这些信息，回归数据本质

4. Loss Functions and batch size
	- ![[Pasted image 20220412095236.png]]
	- batch size 尽量大(4096)

- representation learning
	- 特征提取——物体的独特特征，是模型理解为什么是这个对象而不是另外一个
- memory bank
- linear evaluation protocol