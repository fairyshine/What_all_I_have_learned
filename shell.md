# Shell的综合使用

[【🚀课程】MIT missing-semester](detail/shell/MIT.md)

# tmux

前缀键(prefix): ctrl+b

```shell
tmux new -s <session-name> 

tmux detach     #prefix+d

tmux  ls
```

# Make-批处理工具

[【🚀教程】《跟我一起写Makefile》笔记](detail/shell/《跟我一起写Makefile》笔记.md)

- 🔗：https://github.com/seisman/how-to-write-makefile

# 速记

## My history

```shell
ssh-add -l
```



# Job scheduler: Slurm   

SLURM 作业调度系统

[【🚀教程】Slurm](detail/shell/slurm教程.md)

# SSH密钥相关

注：==git连接远程服务器时会使用到。==

默认存储在~/.ssh/内

生成密钥：ssh-keygen -t rsa -C "备注名称"

启动密钥保管器：ssh-agent -s

将生成的对应密钥文件加入保管器：ssh-add ~/.ssh/github_rsa

查看目前保管器中的密钥：ssh-add -l

