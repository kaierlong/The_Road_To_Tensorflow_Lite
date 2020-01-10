# TFLite模型转换工具
> toco

## 1. 编译`toco`

	bazel build tensorflow/lite/toco:toco

编译结果文件：`bazel-bin/tensorflow/lite/toco/toco`

## 2. 编译`summarize_graph`

	bazel build tensorflow/tools/graph_transforms:summarize_graph

编译结果文件：`bazel-bin/tensorflow/tools/graph_transforms/summarize_graph`
