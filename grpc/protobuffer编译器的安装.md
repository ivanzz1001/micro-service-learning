# protobuffer编译器的安装

参考如下文章：

- [Protocol Buffer Compiler Installation](https://grpc.io/docs/protoc-installation/)

- [protobuffer overview](https://protobuf.dev/overview/)

- [protobuffer github](https://github.com/protocolbuffers/protobuf)

- [ubuntu18.04安装bazel](https://zhuanlan.zhihu.com/p/661422615)

- [protoc protoc-gen-go protobuf 之间的关系](https://blog.csdn.net/qq_30549099/article/details/124994705)


当前的操作系统版本为Ubuntu22.04。


## 1. protoc安装步骤
1）下载protobuffer源代码

这里下载v26.0版本(https://github.com/protocolbuffers/protobuf/releases/tag/v26.0):
<pre>
# wget https://github.com/protocolbuffers/protobuf/archive/refs/tags/v26.0.tar.gz
# tar -zxvf v26.0.tar.gz
# cd protobuf-26.0/
</pre>


2) 安装相关编译工具链

- 安装g++/git
<pre>
# apt install gcc g++ git
# gcc --version
gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0
Copyright (C) 2021 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
</pre>

- 安装bazel: 到华为云镜像仓库下载https://mirrors.huaweicloud.com/bazel/
<pre>
# wget https://mirrors.huaweicloud.com/bazel/7.0.2/bazel-7.0.2-installer-linux-x86_64.sh
# chmod 755 ./bazel-7.0.2-installer-linux-x86_64.sh
# ./bazel-7.0.2-installer-linux-x86_64.sh
# bazel --version
bazel 7.0.2
</pre>

3) 编译安装protoc/protobuffer
<pre>
# bazel build :protoc :protobuf
# cp bazel-bin/protoc /usr/local/bin
# protoc --version
libprotoc 26.0
</pre>

## 2. protoc-gen-go安装
原生的protoc中并未支持自动生成go语言的代码(太奇怪了,google作为go、protobuf的诞生地, 竟然不支持go)：
```
# protoc --help | grep out
Parse PROTO_FILES and generate output based on the options given:
                              to standard output.  The message type must
  --deterministic_output      When using --encode, ensure map fields are
                              to standard output.  The message type must
                              pairs in text format to standard output.  No
    --descriptor_set_out=FILE defined in descriptor.proto) containing all of
  --include_imports           When using --descriptor_set_out, also include
  --include_source_info       When using --descriptor_set_out, do not strip
                              include information about the original
  --retain_options            When using --descriptor_set_out, do not strip
                              that include information about options that were
  --dependency_out=FILE       Write a dependency output file in the format
                              responsible for what codegen output. Not supported
  --cpp_out=OUT_DIR           Generate C++ header and source.
  --csharp_out=OUT_DIR        Generate C# source file.
  --java_out=OUT_DIR          Generate Java source file.
  --kotlin_out=OUT_DIR        Generate Kotlin file.
  --objc_out=OUT_DIR          Generate Objective-C header and source.
  --php_out=OUT_DIR           Generate PHP source file.
  --pyi_out=OUT_DIR           Generate python pyi stub.
  --python_out=OUT_DIR        Generate Python source file.
  --ruby_out=OUT_DIR          Generate Ruby source file.
  --rust_out=OUT_DIR          Generate Rust sources.
```

幸运的是go代码的生成可以通过protoc的一个插件```protoc-gen-go```。

有两种方式来进行安装：
>ps: 参考[protoc-gen-go 安装](https://blog.csdn.net/weixin_36908494/article/details/126384643)

- 使用go get命令

- 通过源码方式安装(https://github.com/golang/protobuf/tree/master)

这里简单介绍第一种安装方式。

1) 安装golang环境




