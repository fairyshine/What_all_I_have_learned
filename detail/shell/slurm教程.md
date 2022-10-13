# Slurm 作业调度系统使用指南

https://zhuanlan.zhihu.com/p/356415669

## 1. Slurm 命令

| Slurm命令    | 功能               |
| ------------ | ------------------ |
| sinfo        | 查看集群分区状态   |
| squeue       | 查看作业队列       |
| srun, salloc | 交互式运行作业     |
| sbatch       | 提交作业           |
| scancel      | 取消作业           |
| scontrol     | 查看和修改作业参数 |
| sacct        | 查看已完成作业     |

## 2. Sinfo

```bash
sinfo               #查看所有分区状态
sinfo -a            #查看所有分区状态
sinfo -N            #查看节点状态
sinfo -n node-name  #查看指定节点状态
sinfo --help        #查看sinfo的说明
```

### 2.1 节点状态

| alloc    | idle     | mix      | down     | drain    |
| -------- | -------- | -------- | -------- | -------- |
| 节点在用 | 节点可用 | 部分占用 | 节点下线 | 节点故障 |

## 3. Squeue

```bash
squeue              #查看运行中的作业列表
squeue -l           #查看列表细节信息
squene -j job-id    #查看运行中作业信息
squeue -u username  #查看user所有运行中的作业
```

### 3.1 作业状态

| R        | PD       | CG       | CD     |
| -------- | -------- | -------- | ------ |
| 正在运行 | 正在排队 | 即将完成 | 已完成 |

## 4. 提交作业

Slurm提交作业有3种模式，分别为交互模式，批处理模式，分配模式，这三种方式只是用户使用方式的区别，在管理，调度，记账时同等对待。

### 4.1 参数说明

以下所有参数在 `srun, sbatch, salloc` 中均可以使用。更多参数见`srun --help`, `sbatch --help`, `salloc --help`。

```bash
-c, --cpu-per-task=NCPUs        #指定每个进程使用核数，不指定默认为1
-e, --error=error_filename      #指定错误文件输出
-J, --job-name=JOBNAME          #指定作业名称
--mail-type=END/FAIL/ALL        #邮件提醒，可选:END,FAIL,ALL
--mail-user=mail_address        #通知邮箱地址
-n, --ntask=NTASKs #指定总进程数；不使用cpus-per-task，可理解为进程数即为核数 
--ntask-per-node=N #指定每个节点进程数/核数，使用-n参数后变为每个节点最多运行的进程数
-N, --nodes=N                   #指定节点数量
-o, --output=out_filename       #指定输出文件输出
-p, --partion=debug             #指定分区
-t, --time=dd-hh:mm:ss          #作业最大运行时间
-w, --nodelist=node[1,2]        #指定优先使用节点，不可与避免节点冲突
-x, --exclude=node[3,5-6]       #指定避免使用节点，不可与优先节点冲突
--mem-per-cpu=MB                #指定计算cpu最大占用内存大小
```

### 4.2 交互模式 Srun

交互式作业提交，提交命令后，等待作业执行完成之后返回命令行窗口。

### Demo of srun

```bash
srun -J JOBNAME -p debug -N 2 -c 1 -n 32 --ntasks-per-node=16 -w node[3,4] -x node[1,5-6] --time=dd-hh:mm:ss --output=file_name --error=file_name --mail-user=address --mail-type=ALL mpirun -n 64 ./iPic3D ./inputfile/test.inp
```

同 **Demo of sbatch**

### 4.3 批处理模式 Sbatch

批处理作业是指用户编写作业脚本，指定资源需求约束，提交后台执行作业。提交批处理作业的命令为 sbatch，用户提交命令即返回命令行窗口，但此时作业在进入调度状态，在资源满足要求时，分配完计算结点之后，系统将在所分配的第一个计算结点（而不是登录结点）上加载执行用户的作业脚本。批处理作业的脚本为一个文本文件，脚本第一行以 “#!” 字符开头，并制定脚本文件的解释程序，如 sh，bash。

运行 `sbatch filename` 来提交任务；计算开始后，工作目录中会生成以 slurm 开头的.out 文件为输出文件（不指定输出的话）。

### Demo of sbatch

保存在运行程序目录下即可，文件名随意（可以无后缀，内容文本格式即可）；作业提交命令`sbatch filename`

```bash
#!/bin/bash                     %指定运行shell
#提交单个作业
#SBATCH --job-name=JOBNAME      %指定作业名称
#SBATCH --partition=debug       %指定分区
#SBATCH --nodes=2               %指定节点数量
#SBATCH --cpus-per-task=1       %指定每个进程使用核数，不指定默认为1
#SBATCH -n 32       %指定总进程数；不使用cpus-per-task，可理解为进程数即为核数
#SBATCH --ntasks-per-node=16    %指定每个节点进程数/核数,使用-n参数（优先级更高），变为每个节点最多运行的任务数
#SBATCH --nodelist=node[3,4]    %指定优先使用节点
#SBATCH --exclude=node[1,5-6]   %指定避免使用节点
#SBATCH --time=dd-hh:mm:ss      %作业最大运行时长，参考格式填写
#SBATCH --output=file_name      %指定输出文件输出
#SBATCH --error=file_name       %指定错误文件输出
#SBATCH --mail-type=ALL         %邮件提醒,可选:END,FAIL,ALL
#SBATCH --mail-user=address     %通知邮箱地址

source /public/home/user/.bashrc   #导入环境变量文件

mpirun -n 32 ./iPic3D ./inputfiles/test.inp #运行命令
```

同 **Demo of srun**

### 4.4 分配模式 Salloc

结点资源抢占命令。该命令支持用户在提交作业前，抢占所需计算资源（此时开始计算所用机时）。需请求资源，然后在获取节点后登录到计算节点。目前作者使用设备可直接登录计算节点，暂未使用。 `sacct` 命令也未启用，暂无说明，有需要可在文末参考网页查看。

## 5. Scontrol

### 5.1 信息查看

```bash
scontrol show job JOBID         #查看作业的详细信息
scontrol show node              #查看所有节点详细信息
scontrol show node node-name    #查看指定节点详细信息
scontrol show node | grep CPU   #查看各节点cpu状态
scontrol show node node-name | grep CPU #查看指定节点cpu状态
```

### 5.2 更新作业

在任务开始前却发现作业的属性写错了（例如提交错了分区，修改名字），取消了重新排队似乎很不划算。如果作业恰好 **没在运行**，我们是可以通过 `scontrol` 命令来更新作业的属性

```bash
scontrol update jobid=JOBID ... #...为下面参数
reqnodelist=<nodes>
reqcores=<count>
name=<name>
nodelist=<nodes>
excnodelist=<nodes>
numcpus=<min_count-max_count>
numnodes=<min_count-max_count>
numtasks=<count>
starttime=yyyy-mm-dd
partition=<name>
timelimit=d-h:m:s
mincpusnode=<count>
minmemorycpu=<megabytes>
minmemorynode=<megabytes>
```

## References

1. [作业调度系统 · 北京大学高性能计算使用指南](https://link.zhihu.com/?target=https%3A//hpc.pku.edu.cn/_book/guide/slurm/slurm.html)
2. [SLURM 使用参考](https://link.zhihu.com/?target=http%3A//bicmr.pku.edu.cn/~wenzw/pages/slurm.html)
3. [Slurm 作业调度系统 — 上海交大超算平台用户手册 文档](https://link.zhihu.com/?target=https%3A//docs.hpc.sjtu.edu.cn/job/slurm.html)
4. [SLURM使用基础教程 - 曙光先进计算](https://link.zhihu.com/?target=https%3A//www.hpccube.com/wiki/index.php/SLURM%E4%BD%BF%E7%94%A8%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B)
5. [Slurm User Guide for Great Lakes | ITS Advanced Research Computing](https://link.zhihu.com/?target=https%3A//arc.umich.edu/greatlakes/slurm-user-guide/)

# 组内《服务器使用技巧》相关

### 作业提交式使用方法

#### 新建环境

在E-Shell中新建自己的conda环境：

```shell
$ conda create -n <Your Name, e.g. tzhu> python=3.9
$ conda activate <环境名，以下简称env>

# 检查python地址是否正确，若不正确则先conda deactivate之后再activate
$ which python

# 安装torch，torchvision、torchaudio如果不需要的话可以不装
# 更多whl包可见：https://download.pytorch.org/whl/torch_stable.html （目前支持rocm4.1，其他未测）
$ pip install torch==1.10.1+rocm4.1 torchvision==0.11.2+rocm4.1 torchaudio==0.10.1 -f https://download.pytorch.org/whl/torch_stable.html
```

#### 撰写作业脚本

将下列脚本添加到代码文件夹中： submit_job.sh

```shell
#!/bin/bash

#SBATCH -J tzhu_job         # 作业名
#SBATCH -o Logs/%x-%j.log   # stdout输出日志文件，%x是作业名，%j是job ID
#SBATCH -e Logs/%x-%j.log   # stderr输出文件
#SBATCH -p kshdnormal       # 使用队列
#SBATCH -N 1                # 使用多少个节点
#SBATCH -n 1                # 申请的总cpu核心数，n卡程序核心数>=n
#SBATCH --gres=dcu:1        # 每个节点使用多少DCU（曙光自己的卡叫DCU）
#SBATCH -t 1-2:3            # 程序执行1天2小时3分钟后会被自动kill（SIGTERM + SIGKILL）

source activate tzhu
module switch compiler/rocm/4.0.1

python debug.py
```

#### 作业相关命令

```shell
# 提交作业
$ sbatch submit_job.sh

# 查询当前用户的队列
$ squeue

# 查看队列中的节点状态：idle表示节点处于空闲状态，可接收新的作业；allocated表示节点已经分配了一个或者多个作业且所有核心用满，在作业释放前不能再被分配作业；mix状态为使用部分核心，仍可以被分配作业；drain状态表示对应节点已经下线；drng表示已下线但仍有作业在运行。
$ sinfo -p kshdnormal

# 取消作业
$ scancel <job_id>

# 提交GPU任务，已经在GPU节点上排队了，发现申请的GPU卡数不对，想调整但⼜不想取消任务重新提交怎么办？
$ scontrol update jobid=<job_id> partition=kshdnormal gres=dcu:*
```

