# TFLite模型可视化
---

## 1. 初步尝试
运行如下命令

	python3 tensorflow/lite/tools/visualize.py --help
	
如果不出意外，会提示如下**错误**，这是因为`flatbuffer`的可执行文件没有找到，路径不匹配。

	Traceback (most recent call last):
	  File "tensorflow/lite/tools/visualize.py", line 47, in <module>
	    raise RuntimeError("Sorry, flatc is not available at %r" % _BINARY)
	RuntimeError: Sorry, flatc is not available at 'tensorflow/lite/tools/../../../../flatbuffers/flatc'

分析`visualize.py`38行到47行源码，我们可以看到这里`_BINARY`定义了`flatc`路径，下面我们编译`flatc`，并修改源码中的路径。

## 2. 编译`flatbuffers`
下载`flatbuffers`

	cd ~/source_code/tensorflow
	git clone https://github.com/google/flatbuffers
	
编译`flatbuffers`

	cd flatbuffers
	cmake .
	make
	make install  # root权限下可用
	
查看`flatc`版本

	./flatc --version
	
结果如下:

	flatc version 1.11.0
	
确认编译后的`flatc`路径

	${home_dir}/source_code/tensorflow/flatbuffers/flatc
	
## 3. 修改`visualize.py`源码
在`visualize.py`39行后面增加一行，内容如下：

	_BINARY = "${home_dir}/source_code/tensorflow/flatbuffers/flatc"
	
该路径实际就是刚刚编译的`flatc`路径。

## 4. 再次测试
运行如下命令

	python3 tensorflow/lite/tools/visualize.py --help
	
会提示`visualize.py`的使用帮助，如下：

	Usage: tensorflow/lite/tools/visualize.py <input tflite> <output html>
	
下面笔记对该帮助做简单说明：

- `<input tflite>` -- 所要查看的`tflite_model`文件路径
- `<output html>` -- 对`tflite_model`进行可视化的输出文件路径，html格式，可以直接在浏览器查看

## 5. `mobilenet_v1`可视化
这里，我们以之前的`mobilenet_v1`为例，看看它的可视化结果。
运行如下命令：

	python3 tensorflow/lite/tools/visualize.py mobilenet_quant_v1_224.tflite model.html

过程输出：

	${home_dir}/source_code/tensorflow/flatbuffers/flatc -t --strict-json --defaults-json -o /tmp tensorflow/lite/tools/../schema/schema.fbs -- mobilenet_quant_v1_224.tflite

在浏览器中打开`model.html`，可以看到模型的版本、输入输出、Tensors、Ops、图结构、Buffers以及所用到的op算子。

![](./images/2020_01_09_visualize.png)