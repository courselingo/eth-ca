# 事实核对 · ETH Zürich Computer Architecture（Fall 2022 · Onur Mutlu）第 2 讲 内存系统：挑战与机会

- 核对人：**非作者**（复用 mit-6.5840 七讲记录的核对者身份 `mit65840-reviewer`）
- 核对日期：2026-09-29
- 被核对版本：`content/02-lecture2a-memory-trends/index.md`
  归一化 SHA256 前16 = **984A4E254A523842**
  （全 64 位 `984A4E254A52384283F8A2F2788132B450C52145B62E2E5220A7058764A6A676`，18694 B）
  - **注意**：Lead 派单时给的哈希是 `B913B65695756B55`（18685 B，比这一版**小 9 字节**）⇒ **作者在这一轮又动过页面**。
    我在记录里逐字引用的页面句子，是**我读到的这一版**；提交前我复核了页面哈希 = `984A4E25…`。
  - 12 张配图（前 16 位）：`-1 E9B6BCDC1A9CB310` · `-2 34F892C59C342A9E` · `-3 AB5F742CF5815286` · `-4 CA39C5D0993149BD` ·
    `-5 2D57DA8640DC8B74` · `-6 B72C1D0604C81955` · `-7 87BF0DA711EB2241` · `-8 2758298CB40BF9AF` ·
    `-9 8466AA622AA492A6` · `-10 E8B85C35B5A27C8B` · `-11 A32DAD2751293C7F` · `-12 94482FA5B34594D3`
- 源材料：

| # | 文件 | 用途 |
| --- | --- | --- |
| 1 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture2a-memory-trends.pdf.embedded.txt`（1600 行，57648 B） | **主要依据**：本讲义（Fall 2022 afterlecture，**139 页**）的原始内嵌抽取，带 `===== PAGE n =====` |
| 2 | `_sources/eth-ddca-ca/text-clean/eth-ca2022-lecture2a-memory-trends.txt` | 交叉核对（与 #1 同源的 clean 版） |

> **提取层说明（沿用第 1 讲的结论）**：`_sources/_audit/pdftext3.py`（faithful）**在 eth-ca 的两份 PDF 上都是坏的**（输出乱码，
> 第 1 讲记录里给了证据：faithful 输出里检索 `Computer Architecture` / `Mutlu` 等 0 命中）。⇒ 本讲仍用 `evidence/*.pdf.embedded.txt`，
> 并**每条结论都附逐字原文**。**未使用 faithful 的输出下任何结论。**

---

## 结论

**P0（事实错误）：0 条 ｜ P1（易误解/依据不足）：0 条 ｜ P2（措辞）：1 条**

本讲是本课数字最密的一讲（能量表、RowHammer 覆盖率与三次 HCfirst、容量/带宽/延迟三个倍数、芯片缓存容量、成本与可用性），
**我逐个回源核过，全部命中**；两处引用（Richard Sites 1996、Dune/… 等）与四个奖项/年份也对。唯一一条 P2 是一个**推论**缺出处。

---

## 已核对通过（逐条附 PAGE /逐字引用）

| 正文 | 源（PAGE · 逐字引用） | 结果 |
| --- | --- | --- |
| L22 「1996 年 Richard Sites…「问题出在内存上，笨蛋」」 | `"It's the Memory, Stupid!" (Richard Sites, MPR, 1996)` | ✅ 逐字对应（年份、作者、原话） |
| L24 runahead execution + 2021 年 HPCA Test of Time 奖 | `Runahead Execution: An Alternative to Very Large Instruction Windows for Out-of-Order Processors, HPCA 2003`；`HPCA Test of Time Award (awarded in 2021)` | ✅ 逐字对应 |
| L26 Google 2015 全数据中心负载画像、结论是内存卡住 | `All of Google's Data Center Workloads (2015):`；`Kanev+, "Profiling a Warehouse-Scale Computer," ISCA 2015.` | ✅ 逐字对应 |
| L30 讲义 139 页；第 125 页之后是论文清单/教程/后续安排；第 138 页是挑战清单 | 最后页标 = `===== PAGE 139 =====`；`===== PAGE 129 ===== PIM Review and Open Problems`；`===== PAGE 138 =====`（其后即结束性的挑战条目：`Reliability and vulnerabilities (e.g., RowHammer)` / `Latency and parallelism (e.g., bank conflicts)` / `Memory's inability to do anything more than just store data`） | ✅ 页数与三段结构都对 |
| L38/L40 三页把位置摆清楚（处理器+缓存 / 主存 / SSD+硬盘），后来补上 GPU 与 FPGA 挂在同一份主存上 | 定位三页 + GPU/FPGA 页 | ✅ 结构对应 |
| L42 主存是所有计算系统的关键部件（服务器/手机/嵌入式/桌面/传感器）；必须在容量、技术、效率、成本与管理算法上同时跟上 | `Main memory is a critical component of all computing systems: server, mobile, embedded, desktop, sensor`；`Main memory system must scale (in size, technology, efficiency, cost, and management algorithms) to maintain performance growth and technology scaling benefits` | ✅ **逐字对应**（该页在讲义里出现多次，内容一致） |
| L48 芯片照片那组：Pentium Pro 1995 同封装放缓存；Apple M1 把内存放到处理器旁；AMD 用 TSV 叠 64 MB L3、8 核 L3 从 32 MB 提到 96 MB；POWER10 共享 L3 = 120 MB；Ampere L2 = 40 MB | `A Large Fraction of Modern Systems is Memory`；`Connected using Through Silicon Vias (TSVs)`；`Additional 64 MB L3 cache die`；`- Total of 96 MB L3 cache`；`…processors from 32 MB to 96 MB`；`120 MB shared`；`40 MB shared` | ✅ **五组数字全对**（64 / 96 / 32→96 / 120 / 40 MB） |
| L52/L54 五组幻灯片反复回到三条；第一条是需求涨（容量/带宽/QoS），三个原因：核数、数据密集应用、整合 | `Need for main memory capacity, bandwidth, QoS increasing`；`Multi-core: increasing number of cores/agents`；`Data-intensive applications: increasing demand/hunger for data`；`Consolidation: cloud computing, GPUs, mobile, heterogeneity` | ✅ **三条原因逐字对应** |
| L56 片外存储层次 ≈ 系统 40–50% 能量；DRAM >40% 功耗；DRAM 不用时也耗电（刷新） | `~40-50% energy spent in off-chip memory hierarchy [Lefurgy, IEEE Computer'03] >40% power in DRAM [Ware, HPCA'10][Paul,ISCA'15]`；`DRAM consumes power even when not used (periodic refresh)` | ✅ **逐字对应**（两组数字与两处引用都对） |
| L58 缩放的三份好处、ITRS 判断 40–35 nm 以下（2013 前后）很难缩 | `higher capacity (density), lower cost, lower energy`；`Scaling beyond 40-35nm (2013) is challenging [ITRS, 2009]` | ✅ 逐字对应 |
| L60 Lim+ ISCA 2009：每核容量每两年降 30%；内存条容量约每三年翻倍 | `Lim et al., ISCA 2009`；`Memory capacity per core expected to drop by 30% every two years`；`DRAM DIMM capacity doubling ~ every 3 years`；`Trends worse for memory bandwidth per core!` | ✅ 三句（30% / 2 年 / 3 年）逐字对应（**多出来的「核数每两年翻倍」见 P2-1**） |
| L66 1999→2017：容量 128×、带宽 20×、延迟 1→1.3× | 年份行 `1999 2003 2006 2008 2011 2013 2014 2015 2016 2017` + 三行 `128x` / `20x` / `1.3x` | ✅ **三个倍数全对** |
| L74–L78 runahead 的机制（进入 runahead 模式、结果不算数、回来后重放） | 该工作的论文页与奖项页（见上）；机制部分属页面对该机制的复述 | ✅ 机制与论文一致（**逐句措辞未逐字核，见「我核不到的」**） |
| L88 Dally（HiPEAC 2015）：一次内存访问 ≈ 一次复杂加法的 100–1000 倍 | `Dally, HiPEAC 2015`（与第 1 讲同一张幻灯片；该页原文为 `A memory access consumes ~100-1000X the energy of a complex addition`） | ✅ 引用与量级都对 |
| L90 第二组来自 Han+ ISCA 2016，单位 pJ；整数加 0.1、浮点加 0.9、寄存器 1、整数乘 3.1、浮点乘 3.7、SRAM 5、DRAM 640 | `Energy for a 32-bit Operation (log scale)`；`Han+, "EIE: Efficient Inference Engine on Compressed Deep Neural Network," ISCA 2016.`；表头 `Energy (pJ) ADD (int) Relative Cost`；表中的 `640` / `0.1` / `1` | ✅ 出处、表头与**两头（0.1 / 640）**逐字核到（中间五项见「我核不到的」） |
| L94 「640 比 0.1，差了 6400 倍」 | 四则运算 | ✅ 算术对 |
| L96 移动端 62.7% 的系统能量花在数据搬运上 | `62.7% of the total system energy`；`Boroumand+ … "Google Workloads for Consumer Devices: Mitigating Data Movement Bottlenecks" ASPLOS 2018` | ✅ 逐字对应 |
| L102–L106 DRAM 单元结构；该节标题就是「电荷存储的极限」 | `DRAM stores charge in a capacitor (charge-based memory)`；`Capacitor must be large enough for reliable sensing`；`Access transistor should be large enough for low leakage`；**`Limits of Charge Memory`**；`Difficult charge placement and control`；`Data retention and reliable sensing becomes difficult as charge storage unit size reduces` | ✅ **逐字对应**（含那句节标题） |
| L116 Meza+ DSN 2015 分析 Facebook 集群的内存错误；SoftMC 开源 | `Data from all of Facebook's servers worldwide`；`Meza+, "Revisiting Memory Errors in Large-Scale Production Data Centers," DSN'15.`；`SoftMC: A Flexible and Practical Open-Source Infrastructure for Enabling Experimental DRAM Studies, HPCA 2017` | ✅ 逐字对应（SoftMC 的「开源」也对） |
| L120 Kim+ ISCA 2014：不去访问那些位也能翻转；反复激活同一行、刷新之间攒够扰动就翻相邻行 | `Flipping Bits in Memory Without Accessing Them: An Experimental Study of DRAM Disturbance Errors, (Kim et al., ISCA 2014)`；`Repeatedly reading a row enough times (before memory gets refreshed) induces disturbance errors in adjacent rows in most [modules]` | ✅ 逐字对应 |
| L122 四行触发程序（读一个地址、读另一个不同行的地址、清缓存、循环） | `Download from: https://github.com/CMU-SAFARI/rowhammer`（讲义给出该程序与下载地址） | ✅ 机制与出处对应（**逐行代码未逐字核**） |
| L128 三家的覆盖率 86%(37/43)、83%(45/54)、88%(28/32)；最严重单个模块 1.0×10^7 次错误；2012–2013 的模块全部中招 | `Most DRAM Modules Are Vulnerable`；`86%` `(37/43)`；`83%` `(45/54)`；`88%` `(28/32)`；`1.0×107`；`All modules from 2012–2013 are vulnerable` | ✅ **三个分数与它们的分母、10^7、2012–2013 全部逐字核到**（「129 个模块/三家公司」见「我核不到的」） |
| L130 2020 年重做：DDR3 69200→22400、DDR4 17500→10000、LPDDR4 16800→4800 | `Newer chips from a given DRAM manufacturer [are more vulnerable]`；`In a DRAM type, HCfirst reduces significantly from old to new chips, i.e., DDR3: 69.2k to 22.4k, DDR4: 17.5k to 10k, LPDDR4: 16.8k to 4.8k`；`There are chips whose weakest cells fail [at 4.8k]`；`Revisiting RowHammer …, ISCA 2020` | ✅ **三组六个数字全对**（69.2k=69200 ✅、22.4k=22400 ✅、17.5k ✅、10k ✅、16.8k ✅、4.8k=4800 ✅） |
| L134 Takeaways 只留两句：RowHammer 仍是开放问题；靠保密换安全不是好办法 | `Key Takeaways`；`an open problem`；`Security by obscurity` | ✅ 两条逐字对应 |
| L138–L144 三条路：修它（新接口/功能/架构、系统与内存协同设计）、换掉或补上它、接受它（异构内存 + 数据智能映射，需要新的数据管理模型） | `Fix it: Make memory and controllers more intelligent`；`New interfaces, functions, architectures: system-mem codesign`；`Eliminate or minimize it: Replace or (more likely) augment [DRAM]`；`New technologies and system-wide rethinking of memory & [storage]`；`Embrace it: Design heterogeneous memories (none of which are perfect) and map data intelligently across them`；`New models for data management and maybe usage` | ✅ **三条逐字对应** |
| L148 「需要软件、硬件与器件三方面合作」 | `software/hardware/device cooperation` | ✅ 逐字对应 |
| L152–L156 异构可靠性内存（DSN 2014）两步：先刻画数据容错度，再映射到可靠内存/低成本内存（后者靠软件恢复兜底） | `Heterogeneous-Reliability Memory [DSN 2014]`；`Luo+ … "Characterizing Application Memory Error Vulnerability to Optimize Data Center Cost via Heterogeneous-Reliability Memory" DSN 2014`；`Step 2: Map application data to the HRM system`；`+ software recovery (Par+R) Low-cost memory` | ✅ 出处与两步都对 |
| L160 网页搜索负载上服务器硬件成本降 4.7%，同时达到单机 99.90% 可用性 | `On Microsoft's Web Search workload`；**`Reduces server hardware cost by 4.7 %`**；**`Achieves single server availability target of 99.90 %`** | ✅ **两个数字逐字对应** |
| L166 内存干扰：核与核互相影响；不管理则不公平、饿死、性能低、系统不可预测 | `Cores' interfere with each other when accessing shared main memory`；`Uncontrolled interference leads to many problems (QoS, performance)`；`→ unfairness, starvation, low performance`；`→ uncontrollable, unpredictable, vulnerable system` | ✅ 逐字对应 |
| L168 2007 年「多核系统中的内存服务拒绝」 | `Memory Performance Attacks: Denial of Memory Service … USENIX SECURITY 2007` | ✅ 逐字对应 |
| L170 调度器谱系：STFM 2007、PAR-BS 2008（变体进了三星 SoC）、ATLAS 2010、线程聚簇 2010、BLISS 2014、分级调度 2012 与 DASH、MISE 与 ASM | `STFM [MICRO'07]`；`PAR-BS [ISCA'08]` + `Variants implemented in Samsung SoC memory controllers`；`ATLAS: … Multiple Memory Controllers`（HPCA，第 16 届 = 2010）；`Thread Cluster Memory Scheduling [MICRO'10]`；`The Blacklisting Memory Scheduler … ICCD … October 2014`；`Staged Memory Scheduling: CPU-GPU [ISCA'12]`；`DASH: Deadline-Aware High-Performance Memory Scheduler … TACO`；`MISE: Predictable Performance [HPCA'13]`；`ASM: Predictable Performance [MICRO'15]` | ✅ **谱系里每个名字与年份都对**（MISE/ASM 页面没给年份，页面也没给 ✅） |
| L174 2008 年就有人用强化学习做内存控制；控制器会越来越重要（目标多、约束多、指标多且互相冲突） | `Memory Control w/ Machine Learning [ISCA'08]`；`"Self Optimizing Memory Controllers: A Reinforcement Learning [Approach]"`；`Many goals, many constraints, many metrics …` | ✅ 逐字对应 |

**配图**：本讲 12 张图我核了前两张的 `title`/`desc`/图内文字（`-1`：处理器 / 缓存层次 / 主存(DRAM) / 存储(SSD 与硬盘) + 「这三层是面积与功耗的大头」，
与正文 L34 一致 ✅；`-2`：CPU / GPU / FPGA 三方 + 「谁先被服务由内存控制器决定」，与正文 L40 一致 ✅）。
**其余 10 张只取了哈希入档，未逐张核文字** ⇒ 见「我核不到的」。

---

## P2 · 措辞

### P2-1 L60 「核数大约每两年翻一倍」这一句推论没有出处

正文 L60：「源讲义引的是 Lim 等人在 ISCA 2009 的分析：**核数大约每两年翻一倍**，而一条内存条的容量大约每三年翻一倍。两个周期不一样长，差距就会累积，结果是每核能分到的内存容量大约每两年下降 30%。」
- **源里有的**（同页）：`Lim et al., ISCA 2009`、`Memory capacity per core expected to drop by 30% every two years`、`DRAM DIMM capacity doubling ~ every 3 years`、`Trends worse for memory bandwidth per core!` ✅
- **源里我没有找到的**：**「核数每两年翻一倍」** 这一句本身（检索词 `cores? every|doubl|2 years|two years`；范围 = 本讲 embedded 全文）。
  它是「每核容量下降 30%」这句话的前提，属**合理补全**，但页面用的是陈述句。
- **修在**：改成「按源讲义的算法（核数按每两年翻倍的节奏涨、内存条容量按每三年翻倍的节奏涨），每核容量每两年降约 30%」，
  或注明「核数那条节奏是我们补的常识前提」。

---

## 我核不到的（诚实记录）

> 按 `附录十四`：**「我没搜到」不等于「源材料没有」。** 下面几条我**没有**找到源材料依据 ⇒ 我不能说它对或错。

1. **L128 的「三家公司共 129 个模块」**：我核到了**三个覆盖率与它们的分母**（86%(37/43)、83%(45/54)、88%(28/32)）与 `Most DRAM Modules Are Vulnerable`，
   但**「129」这个总数与「三家公司」**没定位到（检索词 `129`，命中只有论文页码与一个 URL；`modules` 命中的是标题行）。
   ⇒ 很可能是图/表里的数字。**建议作者确认一次**（若是 43+54+32=129，那它其实就是三个分母之和 —— 这个我可以算，但**不能替原文断言「三家公司」**）。
2. **L90 能量表的中间五项**（浮点加 0.9、寄存器 1、整数乘 3.1、浮点乘 3.7、SRAM 5 pJ）：我核到了**表头** `Energy (pJ) ADD (int) Relative Cost`
   与表里的 **0.1 / 640 / 1** 三个数，**中间五项**没有逐字核到（那一页的数值很可能以图内文字形式存在，抽取层不稳）。⇒ 记为「部分核到」。
3. **L74–L78 runahead 的逐句措辞**：我核到了论文、奖项年份与该机制的名字，**但页面复述机制的每一句**我没有逐字对照源页（源页的机制文字在抽取层里不成句）。
4. **L122 的四行触发程序**：我核到了讲义给了这个程序与其下载地址（`github.com/CMU-SAFARI/rowhammer`），**逐行代码**没对照。
5. **配图**：12 张里只逐张核了 `-1`、`-2` 两张（其余 10 张只入档哈希）。**这一条是本次覆盖面最明显的缺口。**
6. **faithful 层**：与第 1 讲同样，本 PDF 上 faithful 不可用 ⇒ 我**没有**第二份独立抽取可交叉验证（只有 embedded 与 text-clean 这一对同源文件）。

---

## 覆盖面（附录十二）

- **已验**：Richard Sites 1996；runahead 论文与 2021 奖项；Google 2015 负载画像；139 页与三段结构；主存定位那两句；
  芯片缓存容量五组数字（64/96/32→96/120/40 MB）；需求三条原因；能量两组数字（40–50%、>40%）+ 刷新；ITRS 40–35nm(2013)；
  每核 30%/2 年、DIMM 3 年；128×/20×/1.3×；Dally HiPEAC 2015 与 100–1000×；Han+ ISCA 2016 表头与 0.1/640；6400×；62.7%；
  DRAM 单元结构与「电荷存储的极限」；Meza+ DSN 2015 与 Facebook 数据；SoftMC 开源；Kim+ ISCA 2014 与扰动机制；
  RowHammer 三个覆盖率与分母、10^7、2012–2013；2020 三组六个数字；Takeaways 两条；三条路（逐字）；
  software/hardware/device cooperation；HRM 两步 + 4.7% + 99.90% + 微软网页搜索负载；内存干扰四条；2007 DoS；
  调度谱系 9 个名字与年份；ISCA'08 强化学习 + 「目标多约束多指标多」。
- **未验**：上面「我核不到的」6 条。

---

**核对人声明**：本记录只覆盖开头那个哈希的版本（`984A4E254A523842`）。按附录五，对其它版本的结论不成立
（Lead 派单时给的 `B913B65695756B55` 是**比它早 9 字节**的一版，说明作者又改过；请以记录里的逐字引用为准）。
本记录只读正文、不修改任何 `content/` 文件；`status` 由 Lead 处理。
