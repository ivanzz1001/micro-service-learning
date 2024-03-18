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

- 使用go install命令

- 通过源码方式安装(https://github.com/golang/protobuf/tree/master)

这里简单介绍第一种安装方式。

1) 安装golang环境

>ps: golang环境安装好后，可能得设置一下proxy，命令如下
>
>go env -w GOPROXY=https://goproxy.cn,direct

2) 安装protoc-gen-go插件

<pre>
# go list -m -versions google.golang.org/protobuf
google.golang.org/protobuf v1.20.0 v1.20.1 v1.21.0 v1.22.0 v1.23.0 v1.24.0 v1.25.0 v1.26.0-rc.1 
v1.26.0 v1.27.0 v1.27.1 v1.28.0 v1.28.1 v1.29.0 v1.29.1 v1.30.0 v1.31.0 v1.32.0 v1.33.0

# go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.33.0
# ls /usr/local/go1.22.1/bin
go  gofmt  protoc-gen-go

# protoc-gen-go --version
protoc-gen-go v1.33.0
</pre>

上面go install命令会将protoc-gen-go安装到$GOPATH/bin目录下，此时我们需要将该目录添加到PATH环境变量中

## 3. protoc-gen-go的使用
1) 编写一个简单的proto文件 helloworld.proto
```
syntax = "proto3";

option go_package = "github.com/ivanzz1001/go-awesome/proto/helloworld"；

package helloworld;

message HelloRequest {
   string name = 1;
}

message HelloResponse {
   string message = 1;
}
```

关于`package`与`go_package`的说明参看：https://protobuf.dev/programming-guides/proto3/


2) 使用protoc生成对应的go代码
<pre>
# protoc --go_out=paths=source_relative:. ./helloworld.proto
# ls
helloworld.pb.go  helloworld.proto
</pre>

ps: 上面`--go_out=path=source_relative:.`是`--go_out`与`--go-opt`两个选项的合并写法，参看https://protobuf.dev/reference/go/go-generated/
 


## 4. protoc-gen-go安装附录
网上有些教程使用命令`go get -u -v github.com/golang/protobuf/protoc-gen-go`来进行安装，该方式当前可能已经无效了。

1） github.com/golang/protobuf 仓库当前已经在go.mod中提示过时了
```
// Deprecated: Use the "google.golang.org/protobuf" module instead.
module github.com/golang/protobuf

go 1.17

require (
	github.com/google/go-cmp v0.5.5
	google.golang.org/protobuf v1.33.0
)
```

2) go get当前用于获取相关的安装包依赖

如下是通过执行`go help get` 打印出的相关信息:
<pre>
In earlier versions of Go, 'go get' was used to build and install packages.
Now, 'go get' is dedicated to adjusting dependencies in go.mod. 'go install'
may be used to build and install commands instead. When a version is specified,
'go install' runs in module-aware mode and ignores the go.mod file in the
current directory.
</pre>

如果我们一意孤行仍要使用此方法来进行安装，那么执行如下：
<pre>
# go get -u -v github.com/golang/protobuf/protoc-gen-go
# cd /opt/extra/pkg/mod/google.golang.org/protobuf@v1.33.0/cmd/protoc-gen-go
# go build -o protoc-gen-go main.go
# cp protoc-gen-go /usr/local/go1.22.1/bin/
</pre>



