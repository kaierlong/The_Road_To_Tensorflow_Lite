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

**运行配置脚本**，具体情况如下图所示。

	./configure
	
![](./images/2020_01_09_env_config_01.png)
![](./images/2020_01_09_env_config_02.png)

**查看`.tf_configure.bazelrc`**

	cat .tf_configure.bazelrc

![](./images/2020_01_09_env_config_03.png)

**修改`.tf_configure.bazelrc`**
> 将`ANDROID_NDK_API_LEVEL` 修改为 21

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

	benchmark_model --graph=model.tflite --num_runs=100 --num_threads=1 --input_layer=inputs,states --input_layer_shape=1,64:1,2464
	
	benchmark_model --graph=quan_model.tflite --num_runs=100 --num_threads=1 --input_layer=inputs,states,nlm/rnn_cells/kernel_ids_00,nlm/rnn_cells/kernel_ids_01 --input_layer_shape=1,64:1,2464:864,3200:864,3200

	benchmark_model --graph=model.tflite --num_runs=100 --num_threads=1 --input_layer=inputs,nlm/rnn_cells/rnn_cells/multi_rnn_cell/cell_1/lstm_cell_01/MatMul_1 --input_layer_shape=1,64:1,2464 --use_nnapi=true
	
====== tflite

Initialized session in 0.896ms
Running benchmark for at least 1 iterations and at least 0.5 seconds but terminate if exceeding 150 seconds.
count=215 first=12231 curr=2106 min=2065 max=12231 avg=2331.42 std=746

Running benchmark for at least 100 iterations and at least 1 seconds but terminate if exceeding 150 seconds.
count=459 first=2143 curr=2101 min=2056 max=2656 avg=2177.1 std=124

Average inference timings in us: Warmup: 2331.42, Init: 896, no stats: 2177.1

====== dynamic float

Initialized session in 3.33ms
Running benchmark for at least 1 iterations and at least 0.5 seconds but terminate if exceeding 150 seconds.
count=5 first=118829 curr=105453 min=102652 max=118829 avg=107354 std=5819

Running benchmark for at least 100 iterations and at least 1 seconds but terminate if exceeding 150 seconds.
count=100 first=102919 curr=102428 min=101417 max=109982 avg=104036 std=1565

Average inference timings in us: Warmup: 107354, Init: 3330, no stats: 104036

# 构建toco
bazel build tensorflow/lite/toco:toco
bazel-bin/tensorflow/lite/toco/toco

