# 0. Common

This workflow exists as a place to keep common code and rules for other workflows.
There's nothing to do here.

这个workflow存在的目的是为其他workflow提供共享的代码和规则。在这里没有需要执行的操作。

## Description of Programs

- `scripts/`
  - `extract-features`: 从数据集中提取标准特征，创建一个包含这些特征的HDF文件
  - `evaluate-classifier`: Evaluates a single classifier on a single split of the extracted features.在提取的特征的单个拆分上评估单个分类器。 
  - `classifiers.py`: Interface for the classifiers used in the workflows. 工作流程中使用的分类器的接口。
