# 笔试问题

## 基础硬件 Chisel

1.  **红绿灯**
2.  **2-bit 饱和分支预测器**
3.  **PRLU**
4.  **Cache 4-way 组相连**
5.  **Page Table Walker**
6.  **Non-blocking Cache设计与验证**
    设计一个支持多 outstanding miss 的 Non-blocking Cache，用于连接CPU（只读不写）和内存。
    -   CPU请求的物理地址宽度为 48 位（假设为 RV64）。
    -   内存接口为 64 位。
    -   数据缓存规格：16-KB，4 路组相连，64 字节行大小。

    **要求：**
    1.  替换算法可任意选择。
    2.  至少支持 4 个 miss 请求而不阻塞。
    3.  给出 RTL 代码（Chisel、SystemVerilog 或 VHDL 均可）、验证代码和结果，并撰写文档说明所做的工作。

7.  **Page Table Walker (详细题目)**
    在现代操作系统中，虚拟内存管理通常使用多级页表来高效管理内存。现需实现一个进行页表查询的 Page Table Walker。通过给定的 32 位虚拟地址和页表，找到对应的 34 位物理地址。
    -   编程语言不限。
    -   每级页表含有 2^10 个表项，每个表项为 32 位。
    -   页表项分为叶节点和中间节点，使用 L 位标明该节点是否为叶节点。中间节点存储下一级页表的基地址，叶节点存储物理页号。
    -   页大小有 4KB 和 4MB 两种可能。
    -   特殊状态寄存器存储二级页表的基地址，以及是否开启物理地址转换的开关。若未开启，则直接输出虚拟地址作为物理地址返回。
    -   无需考虑页表项无效的情况。如果在查表过程中出现地址宽度不足，可以通过在 32 位地址前填充两个 0 来扩展为 34 位。

    **特殊状态寄存器结构**

    | 名称 | 开启？MODE | 物理页号 PPN |
    | :--- | :---: | :---: |
    | 宽度 | 1 | 22 |

    **虚拟地址结构**

    | 名称 | 虚拟页号 段1<br>VPN[1] | 虚拟页号 段0<br>VPN[0] | 页内偏移<br>Page Offset |
    | :--- | :---: | :---: | :---: |
    | 宽度 | 10 | 10 | 12 |

    **物理地址结构**

    | 名称 | 物理页号 段1<br>PPN[1] | 物理页号 段0<br>PPN[0] | 页内偏移<br>Page Offset |
    | :--- | :---: | :---: | :---: |
    | 宽度 | 12 | 10 | 12 |

    **页表项结构**

    | 名称 | 物理页号 段1<br>PPN[1] | 物理页号 段0<br>PPN[0] | ZERO RSW |
    | :--- | :---: | :---: | :---: |
    | 宽度 | 12 | 10 | 9 |
    *ZERO RSW 为全 0，无功能。*

    **查表请求输入**
    1.  32 位的虚拟地址

    **特殊状态寄存器输入**
    1.  二级页表的基地址
    2.  虚拟地址转换的开启/关闭状态

    **内存请求输入**
    1.  **34 位物理地址**

    **内存返回**
    1.  32 位页表项

    **查表请求返回**
    1.  34 位物理地址

    **提示：** 节点分为中间节点和叶节点，页分为 4KB 和 4MB，它们之间的联系是什么？
    参考：[https://en.wikipedia.org/wiki/Page_table](https://en.wikipedia.org/wiki/Page_table)

    **进阶问题 (二周目)**
    1.  4KB 虚拟页时，页内偏移 (offset) 是 12 位，4MB 虚拟页时，页内偏移是多少位？（5 分）
    2.  PPN[0] 长度为 10 位，4KB * 2^10 = 4MB，这个关系是否巧妙？（1 分）
    3.  映射 4MB 大页时需要查几次页表？需要两次吗？（4 分）
    4.  请根据以上理解修改你的代码。（20 分）
    5.  增加难度：访问物理内存时，需要 34 位物理地址，但你从页表项得到的地址宽度可能仅为 32 位。此时，需要填充两个 0 来扩展到 34 位，是填充在 32 位地址的**前面**还是**后面**？为什么？（10 分）
    6.  请编写一个简单的测试平台（无需 UVM）来验证你的设计。模拟一个存储器并发出激励，进行仿真。重点关注有限状态机和不开启虚拟地址转换的情况。（30 分）
    7.  上网查找一些参考资料。（1 分）

    ![](https://cdn.nlark.com/yuque/0/2026/png/53372797/1776409415548-adc4532a-7153-4692-9372-0bfd79570c89.png)
    *(这是一张图片，内容与地址扩展相关)*

---

## Diplomacy

**参考链接：**
-   [https://bosc.yuque.com/staff-xmw8rg/yhvg8o/xulw3ihe1i6dm5ce](https://bosc.yuque.com/staff-xmw8rg/yhvg8o/xulw3ihe1i6dm5ce)
-   [https://bosc.yuque.com/staff-xmw8rg/yhvg8o/maieerov2t0o6ms1](https://bosc.yuque.com/staff-xmw8rg/yhvg8o/maieerov2t0o6ms1)
-   [https://bosc.yuque.com/staff-xmw8rg/yhvg8o/rylrwaxx95dl9ei8](https://bosc.yuque.com/staff-xmw8rg/yhvg8o/rylrwaxx95dl9ei8)
-   [https://zhuanlan.zhihu.com/p/659308008](https://zhuanlan.zhihu.com/p/659308008)
-   [https://zhuanlan.zhihu.com/p/633327505](https://zhuanlan.zhihu.com/p/633327505)

**题目：** 围绕 IOPMP、DCache、Memory、DMAC 的 Diplomacy 设计（参考 Xiangshan SimMMIO.scala）。
1.  **单数据流通路**：DCache -> IOPMP (bypass, APB 悬空) -> Memory
2.  **2 对 1 Xbar，且带位宽转换和协议转换（最难）**，需要使用 TLXbar。
    a.  AXI_Master(64bit) -> Xbar -> IOPMP(64bit) (bypass, APB 悬空) -> Memory(64bit)
    b.  AXI_Master(64bit) -> Xbar -> APB_Master(32bit) -> IOPMP APB 配置口
3.  **2 对 1 Xbar 方向通路**
    a.  DMAC -> Xbar -> Memory
    b.  DCache -> Xbar -> Memory

---

## RISC-V 验证

1.  **switch-case**
2.  **冒泡排序**
3.  **覆盖率收集**
    使用 [https://github.com/riscv-software-src/riscv-isa-sim](https://github.com/riscv-software-src/riscv-isa-sim) 收集覆盖率。
    1.  增加 AFL 以及分支覆盖率、路径覆盖率的收集。
    2.  思考如何提升覆盖率。时间：2 周。
4.  **RISC-V Matrix Extension**
    **题目：**
    RISC-V 矩阵扩展是一种用于加速矩阵运算的指令集扩展。NEMU-Matrix 是一个基于 NEMU 修改的、支持该扩展的指令级模拟器。

    **目标：**
    请你通过阅读该扩展的文档和代码，理解矩阵扩展的指令格式与行为，使用 Python 构建一个**自动化测试程序生成器**，能够：
    1.  随机生成一段合法的 RISC-V 汇编代码或 C 代码（内联汇编），其中必须包含对 RISC-V 矩阵扩展指令的调用。
    2.  自动调用给定的编译器 (`triton-cpu-llvm-install-self-define`) 将生成的汇编/C 代码编译为 RISC-V 二进制程序 (ELF 或 BIN)。
    3.  将该二进制程序载入 NEMU-Matrix 模拟器中运行，并捕获运行结果（如日志、模拟器退出状态等）。
    4.  使用 YAML 或 Mako 等模板来结构化描述指令信息。
    5.  其余功能可自行扩展。

    **提交内容：**
    -   Python 源代码（以及必要的辅助脚本）。
    -   一份简短的 README.md。
    -   一个生成并运行成功的测试用例输出样例（日志、汇编代码、二进制文件等）。

    **资源链接：**
    -   [https://github.com/yu-yake2002/NEMU-Matrix.git](https://github.com/yu-yake2002/NEMU-Matrix.git)
    -   [triton-cpu-llvm-install-self-define.tar.gz](https://bosc.yuque.com/attachments/yuque/0/2026/gz/56922620/1776416268063-302bb5b0-ba7d-4880-84ef-606ba9928149.gz)
    -   [riscv_matrix_extension_proposal .pdf](https://bosc.yuque.com/attachments/yuque/0/2026/pdf/56922620/1776416264452-e9bf1c98-3060-4443-849f-d1ba1a53bbf9.pdf)

---

## Hello Xiangshan

### 初级
1.  **依据教程，完成 hello xiangshan 的配置与使用。**
    **反馈标准：**
    a.  运行输出：`hello xiangshan, I am xxx (姓名), from (学校), in (IP address)`
    b.  提供执行日志和执行命令即可。

### 高级
2.  **要求利用 RISC-V 可扩展的属性，在高性能开源处理器香山上新增一条或多条自定义指令，实现 print 函数。建议使用 DMA 完成数据搬运操作。鼓励使用大模型。**
    **技术储备：**
    1.  编程语言：Chisel
    2.  熟悉 RISC-V 指令集基础架构
    3.  熟悉香山处理器微架构
    4.  熟悉系统调用、I/O 操作
    **提交需求：**
    1.  提交解题思路，包括自定义指令的功能含义、微架构设计方案、测试方案。
    2.  提交改动模块的源码。
    3.  提交详细设计文档。
    4.  提交波形文件。
    5.  提交验证文档。
    **输出要求：**
    `报名编号: hello xiangshan, I am xxx, from xxx, IP address`

---

## 系统软件

1.  内核的一级页表和二级页表存放在什么地方？用户进程的一级页表和二级页表分别存放在什么地方？（10 分）
2.  请简述 Linux 内核在理想情况下页面分配器 (page allocator) 是如何分配出连续物理页面的？如何从分配掩码中确定可以从哪些 zone 中分配内存？页面分配器是按照什么方向来扫描 zone 的？（20 分）
3.  现代容器（如 Docker）依赖 Linux 内核提供的两个核心特性：
    -   **Namespaces**：提供隔离性（PID、UTS、IPC、Network 等）
    -   **Cgroups**：提供资源限制（CPU、内存等）
    编写一个程序，使用 `clone()` 创建一个新的进程，并在新的命名空间中运行一个用户指定的命令，同时将该进程限制在一个特定的 cgroup 中（例如限制 CPU 使用时间）。（30 分）
4.  描述 CVE-2019-5736 漏洞的触发和修复原理。（40 分）
5.  实现新增 3 条 ecall：
    参考 [https://github.com/riscv-non-isa/riscv-ap-tee/blob/main/src/sbi_cove.adoc](https://github.com/riscv-non-isa/riscv-ap-tee/blob/main/src/sbi_cove.adoc)
    1.  `sbi_covh_convert_pages()`
    2.  `sbi_covh_global_fence()`
    3.  `sbi_covh_local_fence()`
6.  当内核镜像文件 `Image` 尺寸比较大时（比如 43MB），使用以下方法编译 OpenSBI 镜像，启动 Linux 时会遇到失败，请问原因是什么，应该如何调整 OpenSBI 以解决这个问题？

    **Linux 启动报错信息（见文件 qemu.txt）：**

[ 7.102089] Kernel panic - not syncing: uncompression error  
[ 7.103977] CPU: 0 UID: 0 PID: 26 Comm: kworker/u4:1 Not tainted 6.18.0-00031-g664baa8caf89-dirty #107 NONE  
[ 7.105606] Hardware name: bosc,kmh-v2-1core (DT)  
... (详细堆栈信息省略) ...  
**OpenSBI 源码下载及编译命令：**
```
bash
git clone https://github.com/OpenXiangShan/opensbi.git
cd opensbi/
git checkout devel
export ARCH=riscv && export CROSS_COMPILE=riscv64-unknown-linux-gnu- && export PATH=$PATH:<交叉编译工具链安装位置>/bin
make distclean
make PLATFORM=generic CROSS_COMPILE=riscv64-unknown-linux-gnu- FW_FDT_PATH=./kmh-v2-1core.dtb FW_TEXT_START=0x80000000 FW_JUMP_ADDR=0x80400000
```
**参考资料：**
-   RISC-V 交叉编译工具链: [https://github.com/plctlab/riscv-gnu-toolchain/releases/download/2025.08.02/riscv64-glibc-ubuntu-22.04-gcc-nightly-2025.08.02-nightly.tar.xz](https://github.com/plctlab/riscv-gnu-toolchain/releases/download/2025.08.02/riscv64-glibc-ubuntu-22.04-gcc-nightly-2025.08.02-nightly.tar.xz)
-   预编译好的设备树文件: 参见文件 `kmh-v2-1core.dtb`
-   预编译好的内核镜像文件: 参见文件 `Image`

**QEMU 源码下载及编译命令：**
```
bash
git clone https://github.com/OpenXiangShan/qemu.git
git checkout devel
./configure --target-list=riscv64-linux-user,riscv64-softmmu --enable-slirp --enable-virtfs --enable-debug --enable-zstd --enable-plugins
make
```
**QEMU 启动命令**（需要在可用内存大于 18GB 的 Linux PC 上运行）：
```
bash
./build/qemu-system-riscv64 -nographic -machine xiangshan-kunminghu -smp 1 -m 16G -bios ./fw_jump.bin -nographic -device loader,file=./Image,addr=0x80400000
```