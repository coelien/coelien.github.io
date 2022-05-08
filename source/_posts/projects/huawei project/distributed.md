---
title:  pytorch多进程训练
tags: 
- 分布式训练
- distributed
categories:
- 华为项目
---
# pytorch多进程训练

## 一. 基础知识

- group：进程组，默认情况下只有一个组，多进程中一个group有多个world
- world：全局进程个数
- rank：表示进程号，用于进程间通信（值越低，优先级越高）
- local-rank：进程内GPU编号

## 二. 开启分布式模式

```python
def init_distributed_mode(args):
    # 函数里的每一行代码都会在每个进程上单独执行
    log = [] # 记录环境信息
    if 'RANK' in os.environ and 'WORLD_SIZE' in os.environ:
        args.rank = int(os.environ["RANK"])
        args.world_size = int(os.environ['WORLD_SIZE'])
        args.gpu = int(os.environ['LOCAL_RANK'])
        log.extend([args.rank,args.world_size,args.gpu])
    elif 'SLURM_PROCID' in os.environ:
        args.rank = int(os.environ['SLURM_PROCID'])
        args.gpu = args.rank % torch.cuda.device_count()
        log.extend([args.rank,args.gpu])
    else:
        print('Not using distributed mode')
        args.distributed = False
        return

    print(f"args.gpu:{args.gpu}")
    args.distributed = True
    # 打印环境信息
    print(f"environment info: {log}")

    torch.cuda.set_device(args.gpu)
    args.dist_backend = 'nccl'
    print('| distributed init (rank {}): {}'.format(
        args.rank, args.dist_url), flush=True)
    # 分布式初始化
    torch.distributed.init_process_group(backend=args.dist_backend, init_method=args.dist_url,
                                         world_size=args.world_size, rank=args.rank)
    # 同步所有进程
    torch.distributed.barrier()
    setup_for_distributed(args.rank == 0)
```

