---
layout : post
title : "PaddlePaddle Install"
categories : [PaddlePaddle]
published : false
---

### Install

```
python3 -m pip install paddlepaddle==2.4 -i https://mirror.baidu.com/pypi/simple
```

```
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
tensorflow 2.11.0 requires protobuf<3.20,>=3.9.2, but you have protobuf 4.24.1 which is incompatible.
tensorboard 2.11.0 requires protobuf<4,>=3.9.2, but you have protobuf 4.24.1 which is incompatible.
Successfully installed paddle-bfloat-0.1.7 paddlepaddle-2.5.1 protobuf-4.24.1

```

### paddle/fluid/core_avx.so: undefined symbol: _dl_sym, version GLIBC_PRIVATE



#### [libssl.so.1.1: cannot open shared object file: No such file or directory](https://stackoverflow.com/questions/72133316/libssl-so-1-1-cannot-open-shared-object-file-no-such-file-or-directory)

```bash
wget http://nz2.archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2.19_amd64.deb

sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2.19_amd64.deb
```

#### ModuleNotFoundError: No module named 'paddle_quantum'


### Reference
* [PaddlePaddle](https://github.com/PaddlePaddle)
