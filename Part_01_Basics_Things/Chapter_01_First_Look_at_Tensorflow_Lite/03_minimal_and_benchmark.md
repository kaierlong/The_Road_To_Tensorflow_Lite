# minimal and benchmark
---
> 参考

- [tensorflow lite with ndk 17 compilatoin failed #20192](https://github.com/tensorflow/tensorflow/issues/20192)  
- [TFLite benchmark_model cannot be compiled successfully](https://github.com/tensorflow/tensorflow/issues/23068)
- [性能跑分](https://www.tensorflow.org/lite/performance/benchmarks?hl=zh-cn)
- [在 MAC OS X 安装 ADB (Android调试桥)](https://cloud.tencent.com/developer/article/1153846)
- [error: cannot find -lGLESv3 解决](https://blog.csdn.net/flycatdeng/article/details/83059211)
- [在Ubuntu 18.04/Linux Mint 19上安装ADB和Fastboot的方法](https://ywnz.com/linuxsj/4387.html)

## 0. 下载`tensorflow`源码，并定位到`v2.0.0`分支

	git clone https://github.com/tensorflow/tensorflow.git
	git tag
	git checkout v2.0.0
	git branch -a

## 1. 生成`.tf_configure.bazelrc`

	./configure

## 2. minimal

	bazel build tensorflow/lite/examples/minimal:minimal
	bazel-bin/tensorflow/lite/examples/minimal/minimal

## 3. benchmark

**桌面平台**

	bazel build -c opt tensorflow/lite/tools/benchmark:benchmark_model

**arm_v7平台**

	bazel build -c opt --config=android_arm --cxxopt='--std=c++11' tensorflow/lite/tools/benchmark:benchmark_model --verbose_failures

**arm_v8平台**

	bazel build -c opt --config=android_arm64 --cxxopt='--std=c++11' tensorflow/lite/tools/benchmark:benchmark_model --verbose_failures

	benchmark_model --graph=model.tflite --num_runs=100 --num_threads=1 --input_layer=inputs,nlm/rnn_cells/rnn_cells/multi_rnn_cell/cell_1/lstm_cell_01/MatMul_1 --input_layer_shape=1,64:1,2464

	benchmark_model --graph=model.tflite --num_runs=100 --num_threads=1 --input_layer=inputs,nlm/rnn_cells/rnn_cells/multi_rnn_cell/cell_1/lstm_cell_01/MatMul_1 --input_layer_shape=1,64:1,2464 --use_nnapi=true

