# NuttX（NuttX 实时操作系统）学习导读

> 目录：`01-RTOS操作系统/nuttx-NuttX实时操作系统`
> 来源：https://github.com/apache/nuttx （压缩包：nuttx-master.zip）

## 这是什么

NuttX 是 Apache 基金会顶级项目的类 Unix（POSIX 兼容）实时操作系统，1997 年起持续开发至今。最大的特色：**提供标准 POSIX 接口（open/read/write、pthread、socket）+ 小型 shell（NSH）+ 可加载应用（ELF）**，能像操作 Linux 一样操作单片机，代码却只有几百 KB。PX4 飞行控制器、小米生态部分设备等都基于它。

## 目录导读

| 目录 | 内容 |
| --- | --- |
| `sched/` | 内核调度器、任务管理、信号量 |
| `arch/` | 各芯片架构移植（`arch/arm/src/stm32/`、`arch/arm/src/rp2040/`...） |
| `boards/` | 板级支持包 BSP——每块板一个目录（含 `configs/` 默认配置） |
| `fs/` | VFS 虚拟文件系统：fat、littlefs、nxffs、procfs... |
| `drivers/` | 设备驱动 |
| `net/` | 网络协议栈（TCP/IP、6LoWPAN、BLE bridge...） |
| `mm/` | 内存管理（堆、malloc 实现） |
| `include/nuttx/` | 全部内核头文件（POSIX 兼容层定义） |
| `binfmt/` | 可执行文件加载（ELF/NXFLAT） |
| `syscall/` | 应用与内核分离模式的系统调用桩 |

⚠️ 本压缩包只是 NuttX 本体；应用集在配套仓库 **apps/**（NSH shell、示例应用等都在那里），正式开发需要两个仓库配合。

## 学习路径（建议）

1. Linux 上构建（以 stm32f4discovery 为例）：`./tools/configure.sh stm32f4discovery:nsh && make -j`，烧录后串口 115200 打开即进 NSH shell，可以 `ls /dev`、`mount`、`ifconfig`。
2. 在 NSH 里玩：`nsh> free`、`ps`、`cat /proc/version`——体会"单片机里的 Unix"。
3. 读代码顺序：`arch/arm/src/stm32/stm32_start.c`（从复位向量进来）→ `sched/task_setup.c` → `mm/kmm_heap`。
4. 对比 Linux：NuttX 的 `fs/` VFS 设计与 Linux 极为相似，读完对 Linux 内核 VFS 也有感觉了。

## 学习要点

1. **一切皆文件**：串口是 `/dev/ttyS0`，I2C 是 `/dev/i2c0`——应用层完全 POSIX 化。
2. **两种构建模式**：flat（全内核态）和 protected/kernel（应用内核分离，MMU/MPU 隔离，ELF 加载）。
3. **任务 vs 线程**：NuttX 里 task（有独立地址空间语义）和 pthread 的区别。
4. **board/ 目录三要素**：`include/`（板头文件）、`src/`（板初始化代码）、`configs/xxx/defconfig`（Kconfig 全量配置）。

## 建议购买/准备

- **零成本**：qemu 模拟（`qemu-system-arm -M netduinoplus2`）或 sim target（在 Linux 上原生跑 NuttX）。
- **推荐硬件**：STM32F4Discovery（¥150 左右，板载 ST-Link+音频）或树莓派 Pico（¥20-40，rp2040 port 很完整）。
- USB 数据线、USB-TTL 串口模块（CH340，¥10）。

## 学习目标（自测）

- [ ] 能编译出 nsh 固件并进入 shell
- [ ] 能说出 task 与 pthread 在 NuttX 中的差异
- [ ] 能用 write()/read() 操作一个设备驱动
- [ ] 理解 kernel 模式下的 ELF 加载流程
