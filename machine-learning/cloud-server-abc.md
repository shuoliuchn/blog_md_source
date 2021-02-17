# 云服务器使用入门

### 学习目标

- 掌握云服务器使用的基本方法。

------

![img](cloud-server-abc.assets/Centos-logo-light.svg)

------

### 背景介绍

- 当前市场上对程序员的基本需求之一就是能够使用服务器进行开发。在绝大多数公司中，我们都会使用Centos系统（Linux发行版之一）进行开发，因为它是被验证的最稳定的企业级开发服务器。下面我们将学习一些简单的命令，来开启我们Centos学习之旅。

------

### 基本操作

- 假设你已经通过运维人员开通了服务器，并获得了root用户权限（在公司中，你可能得不到这么高的权限），需要在终端中输入这些命令。

------

- 使用ssh命令登陆服务器：

```
ssh root@XX.XX.XXX.XX
root@XX.XX.XXX.XX's password:

# 登陆后修改密码：
passwd

更改用户 root 的密码 。
新的 密码：

# 修改后重新登陆即可
```

------

- 查看我们当前的硬件配置：

```
# 查看内存
free -h

# 查看硬盘
df -h

# 查看cpu逻辑核
lscpu
```

> - 输出效果:

```
              total        used        free      shared  buff/cache   available
Mem:           7.4G        265M        3.5G        580K        3.6G        6.8G
Swap:            0B          0B          0B


文件系统        容量  已用  可用 已用% 挂载点
devtmpfs        3.7G     0  3.7G    0% /dev
tmpfs           3.7G     0  3.7G    0% /dev/shm
tmpfs           3.7G  580K  3.7G    1% /run
tmpfs           3.7G     0  3.7G    0% /sys/fs/cgroup
/dev/vda1        99G   29G   66G   31% /
tmpfs           756M     0  756M    0% /run/user/0


Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    2
Core(s) per socket:    2
座：                 1
NUMA 节点：         1
厂商 ID：           GenuineIntel
CPU 系列：          6
型号：              85
型号名称：        Intel(R) Xeon(R) Platinum 8269CY CPU @ 2.50GHz
步进：              7
CPU MHz：             2500.000
BogoMIPS：            5000.00
超管理器厂商：  KVM
虚拟化类型：     完全
L1d 缓存：          32K
L1i 缓存：          32K
L2 缓存：           1024K
L3 缓存：           36608K
NUMA 节点0 CPU：    0-3
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl eagerfpu pni pclmulqdq monitor ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch invpcid_single fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm mpx avx512f avx512dq rdseed adx smap avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 arat avx512_vnni
```

------

- 查看计算环境：

```
cd /home/ec2-user/
vim README
```

- 你将看到所有的虚拟环境(我们必须学会使用虚拟环境，这在协作开发中非常重要)

```
Please use one of the following commands to start the required environment with the framework of your choice:
for MXNet(+Keras2) with Python3 (CUDA 10.1 and Intel MKL-DNN) ____________________________________ source activate mxnet_p36
for MXNet(+Keras2) with Python2 (CUDA 10.1 and Intel MKL-DNN) ____________________________________ source activate mxnet_p27
for MXNet(+AWS Neuron) with Python3 ___________________________________________________ source activate aws_neuron_mxnet_p36
for TensorFlow(+Keras2) with Python3 (CUDA 10.0 and Intel MKL-DNN) __________________________ source activate tensorflow_p36
for TensorFlow(+Keras2) with Python2 (CUDA 10.0 and Intel MKL-DNN) __________________________ source activate tensorflow_p27
for TensorFlow(+AWS Neuron) with Python3 _________________________________________ source activate aws_neuron_tensorflow_p36
for TensorFlow 2(+Keras2) with Python3 (CUDA 10.1 and Intel MKL-DNN) _______________________ source activate tensorflow2_p36
for TensorFlow 2(+Keras2) with Python2 (CUDA 10.1 and Intel MKL-DNN) _______________________ source activate tensorflow2_p27
for TensorFlow 2.3 with Python3.7 (CUDA 10.2 and Intel MKL-DNN) _____________________ source activate tensorflow2_latest_p37
for PyTorch 1.4 with Python3 (CUDA 10.1 and Intel MKL) _________________________________________ source activate pytorch_p36
for PyTorch 1.4 with Python2 (CUDA 10.1 and Intel MKL) _________________________________________ source activate pytorch_p27
for PyTorch 1.6 with Python3 (CUDA 10.1 and Intel MKL) ________________________________ source activate pytorch_latest_p36
for PyTorch (+AWS Neuron) with Python3 ______________________________________________ source activate aws_neuron_pytorch_p36
for Chainer with Python2 (CUDA 10.0 and Intel iDeep) ___________________________________________ source activate chainer_p27
for Chainer with Python3 (CUDA 10.0 and Intel iDeep) ___________________________________________ source activate chainer_p36
for base Python2 (CUDA 10.0) _______________________________________________________________________ source activate python2
for base Python3 (CUDA 10.0) _______________________________________________________________________ source activate python3
```

- 如果你需要使用python3 + torch新版:

```
source activate pytorch_latest_p36
```

------

- 然后继续可以查看具体的python和pip版本：

```
python3 -V

# 查看pip版本
pip -V

# 查看重点的科学计算包，tensorflow，pytorch等
pip list
```

> - 输出效果:

```
Python 3.6.10 :: Anaconda, Inc.
pip 20.0.2 from /home/ec2-user/anaconda3/envs/pytorch_latest_p36/lib/python3.6/site-packages/pip (python 3.6)
```

------

- 查看图数据情况：

```
# 开启图数据库，这里后期我们将重点学习的数据库
neo4j start

# 关闭数据库
neo4j stop
```

------

> - 输出效果:

```
Active database: graph.db
Directories in use:
  home:         /var/lib/neo4j
  config:       /etc/neo4j
  logs:         /var/log/neo4j
  plugins:      /var/lib/neo4j/plugins
  import:       /var/lib/neo4j/import
  data:         /var/lib/neo4j/data
  certificates: /var/lib/neo4j/certificates
  run:          /var/run/neo4j
Starting Neo4j.
Started neo4j (pid 17565). It is available at http://0.0.0.0:7474/
There may be a short delay until the server is ready.
See /var/log/neo4j/neo4j.log for current status.

Stopping Neo4j.. stopped
```

------

- 运行一个使用Pytorch的程序:

```
cd /data

python3 pytorch_demo.py
```

> - 输出效果:

```
Net(
  (conv1): Conv2d(1, 6, kernel_size=(3, 3), stride=(1, 1))
  (conv2): Conv2d(6, 16, kernel_size=(3, 3), stride=(1, 1))
  (fc1): Linear(in_features=576, out_features=120, bias=True)
  (fc2): Linear(in_features=120, out_features=84, bias=True)
  (fc3): Linear(in_features=84, out_features=10, bias=True)
)
```

------

### 总结

- 我们学习了一些简单的查看命令以适应服务器环境。
- 当然该镜像服务器上还安装了很多工具，如nginx，redis，supervisor等等，这在我们课程的后期会用到。
- 自己购买服务器的小伙伴也不用担心，我们也有对应的安装教程。

------

### 其他说明

- 为了更好的连接海外服务器，大家可以使用

  ```
  finalshell
  ```

  进行登陆。点击进行下载：

  - [finalshell WINDOWS版本](http://www.hostbuf.com/downloads/finalshell_install.exe)
  - [finalshell macOS版本](http://www.hostbuf.com/downloads/finalshell_install.pkg)

------

- 服务器相关告知:
  - 1，服务器订购是学员为了更好的适应企业开发环境，更好的实现AI开发，自主自愿的行为。
  - 2，教师团队将为其提供指定数量的服务器，`配置为4核，8G内存 100G硬盘 ==> 350元/月`
  - 3，云服务器由传智专业运维团队维护，在下发给学员前，配置指定的AI镜像环境。
  - 4，传智AI团队帮助学员解决学员在初期使用过程中遇到的各类软件问题。
  - 5，传智运维团队帮助学员处理云服务器开发过程中遇到的各类硬件安全和运营商问题。
  - 6，学员在使用过程中不得使用服务器进行违法开发（搭建违规网站，黑客攻击等），否则后果自负。