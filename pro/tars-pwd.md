
## 密码重置

如果忘记了admin的密码, 那么可以通过tarsctl工具来重置密码.

```shell
tarsctl framework reset --install-path=[]
```

注意:
- 需要分别在每个framework的节点上执行以上操作

如果是容器化运行的, 可以通过以下命令重置:

```shell
docker run ${framework} tarsctl framework reset
```

需要针对每个容器都执行!