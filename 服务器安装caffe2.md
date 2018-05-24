安装caffe2

1.首先安装anconda2

2.创建py27的虚拟环境

```
conda create -n py27 python=2.7
source activate py27  #激活环境
```

3.安装依赖项

使用清华的镜像（安装3.5.1）

```
conda install -y --channel https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-	     forge \
    future \
    gflags \
    glog \
    lmdb \
    mkl \
    mkl-include \
    numpy \
    opencv \
    protobuf=3.5.1 \  
    snappy \
    six
```

实际上我是手动编译的protobuf

4.解决多个版本的protobuf

```
# CAFFE2=/path/to/caffe2
cd $CAFFE2/third_party/protobuf/cmake
mkdir -p build && cd build
cmake .. \
  -DCMAKE_INSTALL_PREFIX=$HOME/c2_tp_protobuf \
  -Dprotobuf_BUILD_TESTS=OFF \
  -DCMAKE_CXX_FLAGS="-fPIC"
make install
```

5.编译make

这里有个大坑！（facebook的官方教程路径写错了，路径应该是c2_tp_protobuf/lib而不是c2_tp_protobuf/lib64）

如果不修改路径可能会出现onnx错误

```
cmake .. \
  -DCMAKE_PREFIX_PATH=$HOME/anaconda2/envs/py27  \
  -DCMAKE_INSTALL_PREFIX=$HOME/anaconda2/envs/py27 \
  -DCMAKE_CXX_FLAGS=-D__GLIBCXX_USE_CXX11_ABI=0 \ 
  -DPROTOBUF_PROTOC_EXECUTABLE=$HOME/c2_tp_protobuf/bin/protoc \
  -DPROTOBUF_INCLUDE_DIR=$HOME/c2_tp_protobuf/include \
  -DPROTOBUF_LIBRARY=$HOME/c2_tp_protobuf/lib/libprotobuf.a
```

6.测试caffe2是否安装成功

```
python2 -c 'from caffe2.python import core' 2>/dev/null && echo "Success" || echo "Failure"
```

 出现no module named past.builtins，使用如下的方法解决：

```
pip install future ##貌似使用conda install future 没什么作用
##使用清华源pip进行安装
##pip install -i https://pypi.tuna.tsinghua.edu.cn/simple
```

出现提示no module named google.protobuf.internal，解决方法：

```
export PYTHONPATH=/home/yt/caffe2/build:/home/yt/c2_tp_protobuf/bin:$PYTHONPATH

```



也就是把之前编译生成的protobuf添加到python路径中来，至此安装完成。