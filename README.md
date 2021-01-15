## 【Filepp 社区】 lotus 免费版本功能介绍

 1. 内置自动做Addpiece数据添加，可自由控制添加间隔时间
 2. 跳过AP阶段，直接从P1开始，节省AP时间
 3. 支持ap p1 p2绑定在一台机子上工作
 4. 可以自定义每个worker ap p1 p2 c2 工作数量，可以自定义限制每个worker unsealed文件数量
 5. 存储机直接跑worker进程，自动实现均衡存储调度，不回传miner
 6. 支持强制移除任何状态扇区 lotus-miner sectors remove --really-do-it  XX

**备注：社区版无算法优化，如需算法优化版本，可入群联系我们**
 
---

## 使用说明
**miner 配置文件**

```
[Sealing]
    MaxSealingSectors = 60   #限制最大总任务数量，包括 P1 P2 C12
    AutoPledgeSectorInterval = 60  #多久自动添加一个addpiece，单位秒
[Storage]
   UsePreWorkerP1P2 = true    #控制AP P1 P2合并
   AllowFinalize = false      #存储机不回传miner
```


**算力机 worker 配置文件**

```
[Storage]
  ApTaskLimit = 8   #控制AP并行数量，根据自己CPU核心数量自行控制，默认1个，建议8-12个
  P1TaskLimit = 8   #控制P1并行数量，根据自己CPU核心数量自行控制，默认1个，建议8-12个
  P2TaskLimit = 2   #控制P2并行数量，默认1个，1TB内存，建议2-3个
  CTaskLimit = 1    #控制C2并行数量，默认1个，建议1个（社区版不支持C2并行）
  UnsealedSectorLimit = 12 #控制本地unsealed文件数量，默认1个，12TB SSD 建议 12-20个
```  
  
**存储机 worker 启动流程**（可选）

```
1. lotus-worker --worker-repo=/lotus/worker run --no-local-storage
2. lotus-worker storage attach --init --store /存储目录
3. 存储机安装NFS服务端（自行百度如何安装）
4. miner服务器安装NFS客户端，挂载worker存储目录 ：lotus-miner storage attach --store /NFS远程存储目录
```

**不做AP无需任何配置**
> 对于没有订单的扇区，unsealed文件完全相同，软件自动判断不需要做AP，否则执行AP。

