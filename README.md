# 示例：CmBacktrace 自动备份异常日志至文件

## 介绍

本示例主要演示 CmBacktrace 软件包的自带的异常日志保存功能。该功能可以再系统发现异常的时，自动保存日志至异常日志分区，待下次重启后，自动恢复异常日志至文件中。方便开发者记录并分析上次出错的原因。

- **测试平台**：潘多拉 IoT Board
- **依赖组件&软件包**：DFS 、FAL
- **异常日志保存分区**：位于片上 Flash 的 `cmb_log`  分区
- **异常日志恢复文件路径**：`/log/cmb.log` ，文件系统位于板载的 SPI Flash

## 功能演示

## STEP 1

将项目导入 RT-Thread Studio 或者打开 MDK5 工程，这里推荐使用 RT-Thread Studio 打开。

打开项目后，编译并下载程序至潘多拉开发板

### STEP 2

检查启动日志，比如分区表信息。

启动成功后，在 MSH 命令行中新建 `log` 文件夹

```shell
msh />mkdir log	
```

### STEP 3

输入下面的命令，模拟触发 Hardfault 异常

```shell
msh />cmb_test DIVBYZERO
```

### STEP 4

重启开发板，可以看到如下类似如下日志：

```shell
[1116] I/cmb_log: An CmBacktrace log was found on flash. Now will backup it to file (/log/cmb.log).
[1149] I/cmb_log: Backup the CmBacktrace flash log to file (/log/cmb.log) successful.
```

说明异常日志已经成功地恢复至文件中

使用 `cat` 即可查看异常文件内容

```shell
msh />cat log/cmb.log
[3082] E/cmb: 
[3082] E/cmb: Firmware name: rtthread, hardware version: 1.0, software version: 1.0
[3082] E/cmb: Fault on thread tshell
[3082] E/cmb: ===== Thread stack information =====
[3082] E/cmb:   addr: 20004198    data: 00000000
[3082] E/cmb:   addr: 2000419c    data: 00000000
[3082] E/cmb:   addr: 200041a0    data: 00000000
[3082] E/cmb:   addr: 200041a4    data: 00000000
[3082] E/cmb:   addr: 200041a8    data: 00000000
[3082] E/cmb:   addr: 200041ac    data: 00000000
[3082] E/cmb:   addr: 200041b0    data: 00000000
[3082] E/cmb:   addr: 200041b4    data: 00000002
[3082] E/cmb:   addr: 200041b8    data: 08016fb1
[3082] E/cmb:   addr: 200041bc    data: 00000008
[3082] E/cmb:   addr: 200041c0    data: 200041c8
[3082] E/cmb:   addr: 200041c4    data: 08006a95
[3082] E/cmb:   addr: 200041c8    data: 00000012
[3082] E/cmb:   addr: 200041cc    data: 20003122
[3082] E/cmb:   addr: 200041d0    data: 0801eef4
[3082] E/cmb:   addr: 200041d4    data: 20003134
[3082] E/cmb:   addr: 200041d8    data: 200041e0
[3082] E/cmb:   addr: 200041dc    data: 08007d81
[3082] E/cmb:   addr: 200041e0    data: deadbeef
[3082] E/cmb:   addr: 200041e4    data: 00000000
[3082] E/cmb:   addr: 200041e8    data: deadbeef
[3082] E/cmb:   addr: 200041ec    data: 0000000d
[3082] E/cmb:   addr: 200041f0    data: 200011e8
[3082] E/cmb:   addr: 200041f4    data: 00000000
[3082] E/cmb:   addr: 200041f8    data: 00000000
[3082] E/cmb:   addr: 200041fc    data: 00000003
[3082] E/cmb:   addr: 20004200    data: deadbeef
[3082] E/cmb:   addr: 20004204    data: deadbeef
[3082] E/cmb:   addr: 20004208    data: deadbeef
[3082] E/cmb:   addr: 2000420c    data: 080038f9
[3082] E/cmb:   addr: 20004210    data: 23232323
[3082] E/cmb: ====================================
[3082] E/cmb: =================== Registers information ====================
[3082] E/cmb:   R0 : 00000000  R1 : 08022190  R2 : 0000000a  R3 : 00000000
[3082] E/cmb:   R12: 00000000  LR : 08016fdd  PC : 08016ffa  PSR: 61000000
[3082] E/cmb: ==============================================================
[3082] E/cmb: Usage fault is caused by Indicates a divide by zero has taken place (can be set only if DIV_0_TRP is set)
[3082] E/cmb: Show more call stack info by run: addr2line -e rtthread.elf -a -f 08016ffa 08016fd9 08016fad 08006a91 08007d7d 080038f5 
[3082] E/cmb: Current system tick: 3082
msh />
```



更多 CmBacktrace 软件包的使用说明，请查看其软件包介绍： http://packages.rt-thread.org/detail.html?package=CmBacktrace 