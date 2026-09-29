# 事实核对 · ETH Zürich Computer Architecture（Fall 2022 · Onur Mutlu）第 5 讲 把计算搬到内存旁边：近内存计算

- 核对人：**非作者**（本轮由 Lead 派单的**独立核对 subagent** 执行；与作者 `eth-author` 不共享上下文 —— 作者已到上下文上限，本记录全程未询问作者、未改动 `content/`）
- 核对日期：2026-09-29
- 被核对版本：`content/05-lecture4-processing-near-memory/index.md`
  归一化 SHA256 前16 = **CE3CAD5F8302169D**
  （全 64 位 `CE3CAD5F8302169DA6FA09B1AF41F346DFDB5B3C7774642E674F10A34B145257`，CRLF→LF 后 15875 B）
  - 11 张配图（前 16 位）：`-1 2CB67E8A57C631A9` · `-2 A08ED58803584D79` · `-3 415E75293E926647` ·
    `-4 D5A8D5466BE6E146` · `-5 4D1FD890A5E0B71A` · `-6 463F1FB54A916F87` · `-7 AB44A514B58A2C82` ·
    `-8 6995EC1534025BD5` · `-9 B7C4AD9731A80F42` · `-10 7134FD1B24B2D622` · `-11 E4E4C004DD8E2140`
- 源材料：

| # | 文件 | 用途 |
| --- | --- | --- |
| 1 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture4-processing-near-memory.pdf.embedded.txt`（3526 行，78345 B，**236 页**） | **主要依据**：本讲义（Fall 2022 afterlecture）的原始内嵌抽取，带 `===== PAGE n =====` |
| 2 | `_sources/eth-ddca-ca/text-clean/eth-ca2022-lecture4-processing-near-memory.txt` | 交叉核对 —— **必须如实说明：它与 #1 逐字节相同**（`read_bytes()==` 为 `True`，`sha256(CRLF→LF)` 都是 `59CC3B13F9A79653…`）⇒ **不构成独立交叉验证**（见「我核不到的」第 5 条） |
| 3 | 本页 11 张自绘 SVG（`content/05-lecture4-processing-near-memory/figures/`） | 核「正文/图注对配图的描述」是否与图本身一致 |
| 4 | 第 4 讲的源讲义 `eth-ca2022-lecture3-processing-using-memory.pdf.embedded.txt`（**仅用于第 8 节「两讲分工交叉核对」**） | 判断两讲的重复与冲突 |
| 5 | `docs/audit/visual-review/eth-ca__lecture4-processing-near-memory-*.md`（已有 11 份） | 只用于**独立佐证图的版面几何**（我已逐份比过哈希：**11/11 份的 `复核对象 SHA256` 与当前 SVG 完全一致**，即这批评复核是**当前版**的），不作为源材料依据 |

> **提取层说明（按派单要求写明）**：本记录**未用 faithful 输出下任何结论**。
> 派单给的实测前提是「`_sources/_audit/pdftext3.py`（faithful）在 eth-ca 的 PDF 上输出乱码，
> 检索 `Computer Architecture` / `Mutlu` / `Cerebras` / `FLOPS` 全 0 命中」。
> 我在做第 4 讲时照单跑过这个探针并**得到相反的可复现结果**（详见 `lecture3-processing-using-memory-factcheck.md` 的「提取层说明」：
> faithful 输出对第 4 讲讲义命中 `Computer Architecture` 11 · `Mutlu` 33 · `FLOPS` 2，前 900 字符逐字可读；
> 缺陷是 2100 个 NUL 字节 + 按 token 断行 + 页标记按**文本流**给出 266 个对 255 页，**因而不能用于页码定位**）。
> **本讲我没有再跑一次探针**（避免重复生成同一类中间产物），也**没有**用 faithful 的任何字符 —— 本记录每一条都引 `embedded.txt` 的 `===== PAGE n =====`。
> `_sources/` 下**未留下任何我生成的探针文件**（第 4 讲那份 `…pdf.faithful.txt` 已删除）。

---

## 结论

**P0（事实错误）：3 条 ｜ P1（易误解/依据不足）：4 条 ｜ P2（措辞）：4 条**

- 你点名的数字**逐个回源，全部命中**：`62.7%`（p42/p60）· `−55.4% / +54.2%`（p43）· `57.3%`（p45）· `54.4%`（p45）· `+47% / +32% / −25%`（p78）·
  `9.5% / 6.2%`（p131/p141）· `3.7×`（p183）；另核了 `>13×` / `+56%` / `+25%` / `>8×`（p31/p34）、`18.1%`、`19.6 GB`（p57，页面未引）、
  `1.2–1.9×` / `+16%` / `−6%~−41%` / `+5.6%` / `0.45mm²`（p148/p160–163）、`10.4% / 4.4%`（p125，**CoNDA** 的，页面未引 —— 与 SynCron 的 9.5/6.2 没有混用 ✅）。
- **名字与归属**也逐个核过：Tesseract（p23–36）· PEI（p71–87）· TOM（p88/p111）· CoNDA（p118–127）· SynCron（p129–143）· IMPICA（p147–164）·
  VBI（p165–167）· DAMOV（p169–171）· Ramulator/PrIM（p173–175）· NAPEL（p177）· SoftMC（p178）· MQSim（p179）·
  GRIM-Filter（p181–183）· GenASM（p184）· NERO（p186）· NATSA（p187）。**PrIM 的 16 个基准名，页面合并成 13 条，16 个一个不漏** ✅。
- **3 条 P0**：① Tesseract 的 DDR3 基线被写成「**顺序核**」（源里是 `DDR3-OoO`）；
  ② IMPICA 的面积参照被写成「**一级缓存**每兆字节 5 平方毫米」（源里是 `**L2 Cache** 5 mm2 per MB`）；
  ③ 正文与图注都说「逻辑层在**内存上方**」，而配图把「内存裸片」画在**上方**、逻辑裸片画在下方（图与自己的标题/desc/正文相反）。
- 4 条 P1：`一致性由硬件处理`（IMPICA 段，源里没有这句的依据）· `离数据只有几十微米`（数字无出处）·
  「上一讲**不改结构**」（与第 4 讲页面自相矛盾，且源讲义自己写的是 `with small changes`）· 「**系统厂商**要愿意改硬件」（源的五条障碍里没有这条）。

---

## P0 · 事实错误

### P0-1 L42 + 配图 `…-2.svg`：Tesseract 的基线被写成「DDR3 的**顺序核**系统」，源里是 `DDR3-OoO`（**乱序**核）

正文 L42：「源讲义给的对比结果很直观：**相对 DDR3 的顺序核系统**，五个图算法上加速超过 13 倍……」
配图 `…-2.svg` 第 22 行（逐字）：`相对 DDR3 顺序核 &gt;13 倍`；该图 `<desc>` 同样写 `相对 DDR3 顺序核加速超过 13 倍`。**同一条错误在正文与配图里各出现一次。**
- **源材料逐字**（`===== PAGE 30 ===== Evaluating Systems`）：被评测的四个系统是
  `HMC-MC / 128 / In-Order / 2GHz`（**128 个顺序核**）、`HMC-OoO / 8 OoO / 4GHz`（8 个乱序核，4GHz）、
  `**DDR3-OoO**`、`Tesseract / 32 Tesseract Cores`；`===== PAGE 31 =====` 的横轴逐字为
  `**DDR3-OoO**HMC-OoOHMC-MCTesseractTesseract-LPTesseract-LP-MTP`，柱值 `9.0x / 11.6x / 13.8x`，标注 `>13X Performance Improvement` / `On five graph processing algorithms`。
- ⇒ 源讲义里 **DDR3 那台的标签就是 `DDR3-OoO`**（乱序核），页面说的「顺序核」是 **HMC-MC** 那一台（`128 In-Order`）的配置。
  全讲检索 `In-Order|OoO|out-of-order` 只命中：`In-Order Core`（Tesseract 图的核，p23）、`128 In-Order`（HMC-MC）、`8 OoO`（HMC-OoO）、`DDR3-OoO`（两处）——
  **没有任何一处把 DDR3 说成 in-order**。
- **修在**：正文与配图都改成「相对 **DDR3（乱序核）** 系统」。**注意**：页面 L40 说 Tesseract 自己的核是「简单的**顺序**小核」是**对的**（p23 `In-Order Core`）—— 错的是把「顺序」挪给了 DDR3 基线。

### P0-2 L114：IMPICA 的面积参照被写成「**一级缓存**每兆字节 5 平方毫米」，源里是 **L2** 缓存

正文 L114：「……面积增加 0.45 平方毫米（作为对比，它的参照物里**一级缓存每兆字节 5 平方毫米**、[[term:memory-controller]] 10 平方毫米）。」
- **源材料逐字**（`===== PAGE 163 ===== Area and Power Overhead`）：
  `◼ Power overhead: average power increases by 5.6%` / `CPU (Cortex-A57) 5.85mm2 per core` / **`L2 Cache 5 mm2 per MB`** / `Memory Controller 10 mm2` / `IMPICA (+32KB cache) 0.45mm2`
- ⇒ 源里是 **`L2 Cache`**，页面写成「一级缓存」（L1）。同一句里的 `0.45mm²`、`Memory Controller 10 mm2`、`平均功耗 5.6%` 三个数**都对**，
  只有这一个归属错了 —— 属于「读起来不别扭、要专查」的那类归属/方向错误。
- **修在**：改成「二级缓存每兆字节 5 平方毫米」。

### P0-3 L24 / L30 图注 / L32 与配图 `…-1.svg`：正文说「逻辑层在内存**上方**」，配图把**内存**画在上面

正文 L24：「一旦**内存上方**能放一层逻辑，那里就多出了可以放计算单元的空间……」
正文 L30 图注（图题同）：`三维堆叠把一层逻辑放到**内存上方**，本讲讲的就是这层逻辑上能放什么`。
配图 `…-1.svg` 自己的 `<title>`：`三维堆叠：内存上方多出一层可设计的硅`；`<desc>`：`内存裸片**上方**堆叠一层逻辑裸片`。
- **图的实际几何**（逐字坐标）：第 16–17 行 `<rect x="23" y="**64**" …>` + `内存裸片（DRAM）`；
  第 19–20 行 `<rect x="23" y="**134**" …>` + `逻辑裸片：可放专用单元或通用小核`。
  **SVG 的 y 向下递增** ⇒ y=64 的「内存裸片」在**上**，y=134 的「逻辑裸片」在**下**。
- ⇒ **图把顺序画反了**：它自己的标题、自己的 `desc`、以及正文三处都说「逻辑在内存上方」，图里却是「内存在上、逻辑在下」。
- **独立佐证**：`docs/audit/visual-review/eth-ca__lecture4-processing-near-memory-1.md`（哈希与本图当前版**一致**）已独立判为**「有错误」**，逐字
  「堆叠顺序与标题自相矛盾：标题写「内存上方多出一层硅」，但图中代表那层硅的「逻辑裸片…」框却画在「内存裸片（DRAM）」框的下方，上下位置与标题所述相反」，
  并逐字写了版面「上为内存、下为逻辑」。
- **修在**：二选一 —— 把图的上下两框对调（让逻辑在上，与正文/标题一致），或把正文/标题/desc 一起改成「逻辑在内存**下方**（即内存堆在逻辑之上）」。
  **不要**只改一处：现在正文、图题、图内两框是**四对一**的矛盾。
- **补充（请 Lead 注意，与视觉复核的去重）**：同一张图的「几十微米外」旁注被视觉复核独立判为「无引线、指向不明」；
  我把它的**数字出处**问题另记为 **P1-2**（那是源侧问题，视觉复核看不到源）。

---

## P1 · 易误解或依据不足

### P1-1 L114：「一致性由硬件处理」—— IMPICA 的源页里没有这句话的依据

正文 L114：「指针追踪这一类操作对应的方案叫 IMPICA，做法是把地址翻译与访存解耦：**一致性由硬件处理**，地址翻译用一张放在逻辑层里的小页表。」
- **源里有的**（`===== PAGE 148 ===== Executive Summary`）：`Our Solution: In-Memory PoInter Chasing Accelerator (**IMPICA**)` /
  `qAddress-access decoupling: enabling parallelism in the accelerator with low cost` /
  `q**IMPICA page table**: low cost page table in logic layer` ⇒ **IMPICA 只有两条解**：地址-访存解耦 + 逻辑层里的小页表。
- **源里我没有找到的**：任何把「（缓存）一致性」列进 IMPICA 做法的句子。
  - 检索词与范围：工具 `grep`，范围 = 本讲 `embedded.txt` 全文 3526 行。
    ① `oherence` ⇒ 40 处命中，**全部落在 p91–92、p98、p115–p127、p133–p142**；**p147–p167（IMPICA / VBI 那一段）0 处**。
    ② 换写法 `consisten` ⇒ 0 命中；`IMPICA` 的 19 处命中逐条看，全部是 IMPICA 自身的机制/结果/引用，**没有一致性**。
- ⇒ 「一致性由硬件处理」是**页面补的**（作为 PIM 场景的一般前提它讲得通，但**不是**源讲义对 IMPICA 的描述），且位置在「IMPICA 的做法是……」这句的主干上。
- **修在**：删掉这半句，或改成「（IMPICA 本身不处理一致性；本讲的一致性交给 CoNDA 那一节）」之类**明确不是 IMPICA 机制**的写法。

### P1-2 L24 / L32 / 配图 `…-1.svg`：「离数据只有**几十微米**」这个数找不到出处

正文 L24：「一旦内存上方能放一层逻辑，那里就多出了可以放计算单元的空间，**而且离数据只有几十微米**。」
配图 `…-1.svg` 图内文字：`数据就在 / 几十微米外`；`<desc>`：`数据只隔几十微米`。
- **检索记录（按方法②：写清范围与检索词，并说明换了哪些写法）**：
  - 范围①本讲 `embedded.txt` 全文：`micron` / `microns` / `µm` / `micrometer` / `um apart` / `distance` ⇒ **0 处与本主张相关**（`distance` 只命中 p132 SSSP 代码的 `distance[u]`）。
  - 范围②把范围扩到 **`_sources/eth-ddca-ca/evidence/` 下全部 15 份 `*.pdf.embedded.txt`**（lecture1/2a/2b/3/4/6/7a/7b/8a + ddca2023/2026 等）：只有 **`eth-ca2022-lecture6-rowhammer.pdf.embedded.txt` 命中 1 次 `micron`**（讲的是 DRAM 单元工艺，不是堆叠层间距），其余 **0**。
- **边界（诚实）**：这个数**很可能印在 p18 那张图里**（p18 的文字层只有 `Logic` / `Memory` / `Other "True 3D" technologies under development` 三行，图的标注抽不出来）⇒ 我**不能**断言源里没有。
  按方法③（数字必须回源，找不到记 P1）记在这里：**请作者补一次出处**，或把「几十微米」改成不带数的说法（「层间距离极短，只有互连延迟量级」）。

### P1-3 L22：「上一讲……**不改结构**，只利用 DRAM 内部已有的能力」—— 与本项目第 4 讲页面自相矛盾，且与源讲义不符

正文 L22：「上一讲的做法是「用内存自己来算」：**不改结构**，只利用 DRAM 内部已有的能力。这一讲走另一条路……」
- **本项目第 4 讲页面（同一门课、已交付）** L116 逐字写着：「按位非（NOT）不能用多数函数直接得到，源讲义给的办法是**改单元结构**：用一个「双接触」单元把取反后的值喂进感应放大器……」。
  ⇒ 同一个项目的两页，一页说「不改结构」，一页说「改单元结构」。
- **源讲义（第 4 讲讲义）逐字**：`===== PAGE 122 ===== In-DRAM NOT: **Dual Contact Cell**`（`Idea: Feed the negated value in the sense amplifier into a special row`）；
  `===== PAGE 135 =====`：`Using an in-DRAM massively-parallel SIMD substrate that **requires minimal changes to DRAM architecture**`；
  `===== PAGE 116 =====`：`◼ DRAM has great capability to perform bulk data movement and computation internally **with small changes**`。
- ⇒ 源讲义自己的说法是「**small changes / minimal changes**」，不是「不改结构」。
- **修在**：改成「对 DRAM 的改动很小（源讲义的说法是 `with small changes`），主要靠内存自身的运行原理」，并在两页之间统一口径。

### P1-4 L140：「**系统厂商要愿意改硬件**」不在源讲义列出的障碍里

正文 L140：「源讲义花了很长篇幅讲采纳问题，因为技术可行不等于会被用。它列出的障碍包括：应用与软件要为 PIM 改写、**系统厂商要愿意改硬件**、编程模型要重新设计。」
- **源材料逐字**（`===== PAGE 98 ===== Potential Barriers to Adoption of PIM`）：
  `1. Applications & software for PIM` / `2. Ease of programming (interfaces and compiler/HW support)` /
  `3. System and security support: coherence, synchronization, virtual memory, isolation, communication interfaces, …` /
  `4. Runtime and compilation systems for adaptive scheduling, data mapping, access/sharing control, …` /
  `5. Infrastructures to assess benefits and feasibility` / `All can be solved with change of mindset`
  ⇒ 源列的是**五条**，其中没有「厂商意愿」这一条。
- **检索记录**：工具 `grep`，范围 = 本讲 `embedded.txt` 全文；检索词 `vendor` / `Vendor` ⇒ **0 命中**；换写法 `manufacturer` / `industry` ⇒ 亦无与「厂商要改硬件」相关句。
- **附带一条**：同句的「源讲义**花了很长篇幅**讲采纳问题」也与篇幅不符 —— 源的「采纳」这一段是 `PAGE 96–103`（约 **7 页 / 236 页**），
  页面后面真正长的结论段是 `PAGE 213–232`。这两点合起来看，这一句更像是**页面的概括**而不是源的结构。
- **修在**：按源的五条改写（应用/软件、编程易用性、系统与安全支持、运行时与编译系统、评估基础设施），或明确标注「厂商意愿」是我们补的一条现实障碍。

---

## P2 · 措辞

### P2-1 L54 + 配图 `…-3.svg`：「当时 Google 手机上**最重要**的四类」「手机上**最值得做**的四个负载」

正文 L54：「它挑的四个负载是当时 Google 手机上**最重要的四类**：Chrome 浏览器、TensorFlow Mobile 机器学习框架、视频播放与视频采集。」
配图 `…-3.svg` 标题逐字：`手机上**最值得做**的四个负载`。
- **源材料逐字**：`===== PAGE 41 ===== **Four Important Workloads**`（p44 同题），四项逐字为
  `Chrome — Google's web browser` / `TensorFlow Mobile — Google's machine learning framework` / `Video Playback — Google's video codec` / `Video Capture — Google's video codec`（**四项本身全对** ✅）。
- **源里没有的**：`most important` 在本讲全文 **0 命中**（工具 `grep`）。源只说 `Important`。
- **修在**：把「最重要 / 最值得做」降回「（源讲义挑的）四个重要负载」。

### P2-2 L84 + 配图 `…-5.svg`：「能量降低 25%」丢掉了源的限定「**大输入集**」

正文 L84：「源讲义给的结果是：在 10 个数据密集型负载上，大输入集平均加速 47%、小输入集 32%，**单节点平均能量降低 25%**。」（配图 `…-5.svg` 写 `能量 -25%`）
- **源材料逐字**（`===== PAGE 78 ===== PEI: Initial Evaluation Results`）：
  `n 47% average speedup with large input data sets` / `n 32% speedup with small input data sets` /
  `n **25% avg. energy reduction in a single node with large input data sets**`
- ⇒ 47% 与 32% 两个数页面都带了限定，唯独 25% 这条把 `with large input data sets` 删了 —— 读者会以为 25% 是三档输入集通用的。
- **修在**：补回「（大输入集）」。

### P2-3 L44 + 配图 `…-2.svg`：「再快 **56% 到 25%**」把两个各自独立的改进量写成了区间

正文 L44 图注：「Tesseract 相对 DDR3 基准超过 13 倍，相对同样堆叠内存的主机核方案**再快 56% 到 25%**」。
- **源材料逐字**（`===== PAGE 31 =====`）：横轴末两项为 `Tesseract-LP` 与 `Tesseract-LP-MTP`，其上方标注逐字为 **`+56% +25%`**
  ⇒ 是**两个各自独立**的增量（对应两种优化），不是「介于 25% 与 56% 之间」的区间，且页面写成降序「56% 到 25%」更像笔误。
- **修在**：写成「加两种优化后分别再快 56% 与 25%（对应 Tesseract-LP 与 Tesseract-LP-MTP）」。
  **相关联但不同的一条**：视觉复核 `…-2.md` 已独立指出这两个百分比**没有标注各自对应哪一项优化**（那是图的标注缺失），与本条要一起修。

### P2-4 L88：「**源讲义把它们排成一组**」—— 源只把其中两条标为 `Key Challenge`

正文 L88：「把 PIM 真正用起来，还要解决四个系统层面的问题。**源讲义把它们排成一组**，并各给出代表工作。」
- **源里有的**：`===== PAGE 109 ===== **Key Challenge 1: Code Mapping**` / `===== PAGE 110 ===== **Key Challenge 2: Data Mapping**`；
  调度是另一节 `PAGE 104 PIM Runtime: Scheduling and Data Mapping` + `PAGE 112–114 How to Schedule Code?`；
  一致性是另一节 `PAGE 115 Memory Coherence` + `PAGE 116 Challenge: Coherence for Hybrid CPU-PIM Apps` + `PAGE 117–127`。
- ⇒ 源**没有**把「代码映射 / 数据映射 / 调度 / 一致性」四条并成一页的「一组」；把它们归成四条是**页面自己**的结构。
- **修在**：改成「源讲义把**代码映射与数据映射**标为两个 Key Challenge（p109/p110），调度（p104/p112–114）与一致性（p115–127）各是独立一节；本节把四条并起来看」。

---

## 已核对通过（逐条附 PAGE /逐字引用）

| 正文 | 源（PAGE · 逐字引用） | 结果 |
| --- | --- | --- |
| L13 讲义 **236 页** | 最后一个页标记 = `===== PAGE 236 =====`（内容为 `Computer Architecture / Lecture 4: Processing near Memory / Prof. Onur Mutlu / ETH Zürich / Fall 2022 / 7 October 2022`） | ✅ 页数对 |
| L24「**2015 年前后**变得可行」+「把两张图并列摆出来，一张是「逻辑裸片加内存裸片」的堆叠结构，另一张标注为**仍在开发中的其它三维技术**」 | `===== PAGE 18 ===== Opportunity: **3D-Stacked Logic+Memory**` / `Logic` / `Memory` / **`Other "True 3D" technologies under development`**；同页另有 `PAGE 19 DRAM Landscape (**circa 2015**)` | ✅ 「3D 堆叠」「其它真三维技术、仍在开发中」「2015 前后」三个要素都对（**「两张图并列」这个版式判断见「我核不到的」第 1 条**） |
| L26「源讲义在**第 20 页**把要回答的问题列成两条」+ 两条问题的内容 | `===== PAGE 20 ===== Several Questions in 3D-Stacked PIM` / `nWhat are the performance and energy benefits of using 3D-stacked memory as a coarse-grained accelerator?` / `qBy changing the entire system` / `qBy performing **simple function offloading**` / `nWhat is the minimal processing-in-memory support we can provide?` / `qWith minimal changes to system and programming` | ✅ **页码 + 两条问题 + 两个子情形逐字对应** |
| L36「2015 年前后大图到处都是（**维基百科页面 3600 万、Facebook 用户 14 亿、Twitter 用户 3 亿、Instagram 照片 300 亿**）」 | `===== PAGE 21 ===== Another Example: In-Memory Graph Processing` / `nLarge graphs are everywhere (**circa 2015**)` / `**36 Million** Wikipedia Pages` / `**1.4 Billion** Facebook Users` / `**300 Million** Twitter Users` / `**30 Billion** Instagram Photos` | ✅ **四个数全对**（36M / 1.4B / 300M / 30B） |
| L36「大规模图处理很难做快」 | `nScalable large-scale graph processing is **challenging**` | ✅ 逐字对应 |
| L38「用一行代码说明：遍历每个顶点的每个后继，把权重乘上当前排名再累加到邻居的下一个排名里」 | `===== PAGE 22 =====` 逐字：`for(v: graph.vertices) {` / `for(w: v.successors) {` / `**w.next_rank += weight * v.rank;**` / `} }` | ✅ **逐字对应**（代码行一字不差） |
| L38「两条瓶项：**频繁的随机访存**，以及**每次访存对应的计算量极小**」 | `PAGE 22` 逐字：`1. **Frequent random memory accesses**` / `2. **Little amount of computation**` | ✅ **逐字对应** |
| L40「把一组三维堆叠的内存加逻辑芯片连起来」 | `===== PAGE 23 ===== Tesseract System for Graph Processing` / `Interconnected set of **3D-stacked memory+logic chips with simple cores**` | ✅ 逐字对应 |
| L40「每片逻辑里放简单的**顺序小核**」 | `PAGE 23` / `PAGE 24` 图内逐字：`**In-Order Core**` | ✅ 逐字对应（**注意**：这一条的「顺序」是对的；错的是 L42 把「顺序」安给了 DDR3 基线，见 **P0-1**） |
| L40「用**交叉开关网络**互连」 | `PAGE 23` 逐字：`**Crossbar Network**` | ✅ 逐字对应 |
| L40「对外暴露成一段**不可缓存、物理寻址**的内存映射接口」 | `PAGE 23` 逐字：`Memory-Mapped Accelerator Interface` / `(**Noncacheable, Physically Addressed**)` | ✅ **逐字对应** |
| L40「用了**非阻塞**的远程函数调用」 | `===== PAGE 24 =====` 逐字：`Communications via **Remote Function Calls**`；`===== PAGE 28 =====` 标题逐字：`Remote Function Call (**Non-Blocking**)` | ✅ **逐字对应（含「非阻塞」）** |
| L42「**五个**图算法上加速**超过 13 倍**」 | `===== PAGE 31 =====` 逐字：`**>13X Performance Improvement**` / `On **five** graph processing algorithms`；柱值含 `13.8x` | ✅ **数字与「五个」都对**（**基线的核类型见 P0-1**） |
| L42「相对同样用三维堆叠内存但只用主机核的方案，加两个优化后还能再快 **56%** 与 **25%**」 | `PAGE 31` 逐字：`**+56% +25%**`（柱为 `Tesseract-LP`、`Tesseract-LP-MTP`；同页另两支为 `HMC-OoO`、`HMC-MC` = 三维堆叠内存 + 主机核） | ✅ 两个数与基线选择都对（**「到」这个读法见 P2-3**） |
| L42 / L44 / L152「系统能量降低超过 **8 倍**」 | `===== PAGE 34 ===== Tesseract Graph Processing System Energy` / `**> 8X Energy Reduction**` | ✅ 逐字对应 |
| L46 五条缺点：改动系统幅度大、要新编程模型、逻辑层放的是专为图处理设计的核、成本高、规模受限于片外链路或图的分割 | `===== PAGE 35 ===== Tesseract: Advantages & Disadvantages` 逐字：`-Changes a lot in the system` / `-New programming model` / `-Specialized Tesseract cores for graph processing` / `-Cost` / `-Scalability limited by off-chip links or graph partitioning` | ✅ **五条逐字对应** |
| L52 第一条观察：系统总能量的 **62.7%** 花在 data-movement 上 | `===== PAGE 42 ===== Energy Cost of Data Movement` 逐字：`1st key observation: **62.7% of the total system energy is spent on data movement**`；`PAGE 60` 复现同一句 | ✅ 逐字对应 |
| L52 第二条观察：**这些搬运里有一大部分来自很简单的函数** | `===== PAGE 43 =====` 逐字：`2nd key observation: **a significant fraction of the data movement often comes from simple functions**` | ✅ 逐字对应 |
| L52「既然函数简单，就不必放进通用核，放一个**小型固定功能单元**就够」 | `PAGE 43` 逐字：`We can design **lightweight logic** to implement these simple functions in memory` / `Small embedded, low-power core` / **`Small fixed-function accelerators`** | ✅ 逐字对应（**`fixed-function` 是源的原词**） |
| L54 四个负载与它们的身份（Chrome 浏览器 / TensorFlow Mobile 机器学习框架 / 视频播放 / 视频采集，后两者都是 codec） | `===== PAGE 41 =====`（与 `PAGE 44` 同题）逐字：`Four Important Workloads` / `Chrome — Google's web browser` / `TensorFlow Mobile — Google's machine learning framework` / `**Video Playback — Google's video codec**` / `**Video Capture — Google's video codec**` | ✅ **四项与身份全对**（**「最重要」见 P2-1**） |
| L54「把这些简单函数卸载到内存里的逻辑上，平均**能量降低 55.4%、性能提升 54.2%**」 | `PAGE 43` 逐字：`Offloading to PIM logic **reduces energy and improves performance, on average, by 55.4% and 54.2%**`；拆解页互证 `PAGE 48`（`PIM core and PIM accelerator reduce energy consumption on average by 49.1% and **55.4%**`）、`PAGE 49`（`… improves performance on average by 44.6% and **54.2%**`） | ✅ **两个数 + 它们各自的归属（能量↔55.4、性能↔54.2）全对**，且三页互证 |
| L58「TensorFlow Mobile 的推理过程里，**57.3%** 的能量花在数据搬运上，而**其中 54.4%** 来自打包/解包与量化」 | `===== PAGE 45 =====` 逐字：`**57.3% of the inference energy is spent on data movement**` / `**54.4% of the data movement energy comes from packing/unpacking and quantization**` | ✅ **两个数 + 「其中」的所指（是搬运能量的 54.4%）全对** |
| L58「也就是把 **32 位浮点转成 8 位整数**、以及**为了矩阵乘而重排元素**」 | `===== PAGE 47 ===== Quantization` 逐字：`**Converts 32-bit floating point to 8-bit integers** to improve inference execution time and energy consumption`；`===== PAGE 46 ===== Packing` 逐字：`**Reorders elements of matrices to minimize cache misses during matrix multiplication**` | ✅ **两条逐字对应（且打包/量化与「重排/转换」的配对没搞反）** |
| L58「Chrome 那边，**切换标签页**时浏览器要**反复压缩与解压**页面（讲义提到用的是**内存压缩**）」 | `===== PAGE 55 ===== What Happens During Tab Switching?`；`===== PAGE 56 ===== Memory Consumption` 逐字：`CPU / DRAM / Inactive Tab / **Compression** / **Decompression** / **Chrome uses compression to reduce each tab's memory footprint** / **ZRAM**`；`===== PAGE 59 ===== Tab Switching Wrap Up` 逐字：`A large amount of data movement happens during tab switching as Chrome attempts to **compress and decompress** tabs` / `Both functions can benefit from PIM execution and **can be implemented as PIM logic**` | ✅ **逐字对应**（ZRAM 即内存压缩；「同样可以在内存侧做」有 `implemented as PIM logic` 支撑） |
| L64「典型的是**指针追踪**：每一次取数都要用上一次的结果算出地址，于是访存完全串行，延迟一层层叠加」 | `===== PAGE 150 ===== The Problem: Pointer Chasing` 逐字：`Traversing linked data structures requires chasing pointers` / `**Serialized and irregular access pattern**` | ✅ 逐字对应 |
| L66「把这类操作放到内存侧的 accelerator 上执行」，并列出「加速 GPU 执行的近内存方案、针对链表这类数据结构的加速、以及**依赖型缓存缺失**的处理」 | `===== PAGE 62 =====`（TOM，`Near-Data Processing in GPU Systems`）· `===== PAGE 64 ===== Accelerating Linked Data Structures`（`**Accelerating Pointer Chasing in 3D-Stacked Memory**`）· `===== PAGE 65 ===== **Accelerating Dependent Cache Misses**` | ✅ **三件工作的方向逐字对应**（源另有 p63 调度，页面用「几件」带过 ✅） |
| L74「有了一个几乎**不改**的方案，叫 **PIM 指令（PEI）**」 | `===== PAGE 71 ===== PIM-Enabled Instructions`；`===== PAGE 72 ===== PEI: PIM-Enabled Instructions (Ideas)` 逐字：`Goal: Develop mechanisms to get the most out of near-data processing with **minimal cost, minimal changes to the system, no changes to the programming model**` | ✅ 逐字对应 |
| L76「第一条是把每个内存侧的操作暴露成**一条宿主处理器指令**：它像普通指令一样是**缓存一致、虚拟地址寻址**的，而且**只操作一个缓存块**」 | `PAGE 72` 逐字：`Key Idea 1: Expose each PIM operation as a **cache-coherent, virtually-addressed host processor instruction** (called PEI) that operates on **only a single cache block**` | ✅ **逐字对应** |
| L76「把「给某个邻居的排名加上一个值」写成 `__pim_add(&w.next_rank, value)`」 | `PAGE 72` 与 `===== PAGE 76 =====` 逐字均含 `**__pim_add(&w.next_rank, value)**`（p72 还给了汇编形式 `àpim.addr1, (r2)`） | ✅ **逐字对应（含函数名与参数）** |
| L76「编程模型不变、虚拟内存不变、缓存一致性只需极小的改动，而且**不需要数据映射**，因为每条指令只涉及一个内存模块」 | `PAGE 72` 逐字：`q **No changes** sequential execution/programming model` / `q **No changes** to virtual memory` / `q **Minimal changes** to cache coherence` / `q **No need for data mapping**: Each PEI restricted to a **single memory module**` | ✅ **四条逐字对应**（含「不小改」与「不映射」的区分） |
| L78「第二条是**动态决定这条指令在哪里执行**：宿主 CPU 还是内存侧单元，由**简单的局部性监测与硬件预测器**决定」 | `PAGE 72` 逐字：`Key Idea 2: **Dynamically decide where to execute a PEI** (i.e., the host processor or PIM accelerator) based on **simple locality characteristics and simple hardware predictors**` | ✅ 逐字对应 |
| L78「源讲义专门用一页说明「**全都放到内存里执行不是好主意**」：在有些负载上 cache 非常有效，硬把它们推到内存侧反而变慢」 | `===== PAGE 75 =====` 标题逐字：`**Always Executing in Memory? Not A Good Idea**`；图注逐字：`**Caching very effective**` / `Reduced Memory Bandwidth Consumption due to In-Memory Computation`；纵轴从 `-20%` 到 `+60%` | ✅ 逐字对应（**「反而变慢」有纵轴 −20% 的负值支撑**） |
| L80「PEI 之间是**原子**的，但与普通指令之间**不是**原子的，需要用 `**pfence**` 来定序」 | `===== PAGE 76 =====` 逐字：`nAtomic between different PEIs` / `n**Not atomic with normal instructions (use pfence for ordering)**`；同页代码含 `pfence();` | ✅ **逐字对应** |
| L84「在 **10 个**数据密集型负载上，**大输入集平均加速 47%、小输入集 32%**」 | `===== PAGE 78 =====` 逐字：`Initial evaluations with **10 emerging data-intensive workloads**` / `n **47% average speedup with large input data sets**` / `n **32% speedup with small input data sets**`；`===== PAGE 79 =====` 逐字给了这 10 个负载的名字 | ✅ **三个要素全对** |
| L84「它自己列出的缺点是：**没能吃满 PIM 的潜力**，而且「**单个缓存块**」这条限制很硬」 | `===== PAGE 86 ===== PEI: Advantages & Disadvantages` 逐字：`-Does not take full advantage of PIM potential` / `-**Single cache block restriction is limiting**` | ✅ 两条逐字对应 |
| L90 第一个挑战**代码映射**（哪些操作放进内存、哪些留给处理器） | `===== PAGE 109 ===== Key Challenge 1: Code Mapping` 逐字：`•Challenge 1: **Which operations should be executed in memory vs. in CPU?**` | ✅ 逐字对应 |
| L90 第二个挑战**数据映射**（数据放到哪个内存堆叠上）；这两件事的代表方案叫 **TOM**，目标是**让程序员不必手工指定** | `===== PAGE 110 ===== Key Challenge 2: Data Mapping` 逐字：`•Challenge 2: **How should data be mapped to different 3D memory stacks?**`；`===== PAGE 111 ===== How to Do the Code and Data Mapping?` 引出 `"**Transparent Offloading and Mapping (TOM): Enabling Programmer-Transparent Near-Data Processing in GPU Systems**"` ISCA 2016 | ✅ **两个挑战 + TOM + 「程序员透明」逐字对应**（TOM 同时覆盖代码与数据映射，与页面「这两件事有一套代表性方案」一致） |
| L92 第三个是**调度**（内存侧算力也要排队） | `===== PAGE 104 ===== PIM Runtime: Scheduling and Data Mapping`；`===== PAGE 112–114 ===== How to Schedule Code? (I)(II)(III)` | ✅ 对应 |
| L92 第四个是**缓存一致性** | `===== PAGE 115 ===== Memory Coherence`；`===== PAGE 116 ===== Challenge: **Coherence for Hybrid CPU-PIM Apps**` | ✅ 逐字对应（**「源讲义排成一组」见 P2-4**） |
| L100 一致性是「**混合应用**」的前提：一个程序里既有**主机代码**又有**内存侧代码**，还**共享数据** | `PAGE 116` 逐字：`Challenge: Coherence for **Hybrid CPU-PIM Apps**` | ✅ 逐字对应 |
| L102 一致性代表工作是 **CoNDA**，目标是为**近数据加速器**提供高效的缓存一致性支持 | `===== PAGE 118 =====` / `PAGE 127` 逐字：`"**CoNDA: Efficient Cache Coherence Support for Near-Data Accelerators**"` ISCA 2019 | ✅ 逐字对应 |
| L102「传统的一致性协议……会带来**不必要的开销**」 | `===== PAGE 121 =====` 逐字：`(1) Large cost of off-chip communication` / `**It is impractical to use traditional coherence protocols**`；`===== PAGE 122 =====` 逐字：`The majority of off-chip coherence traffic generated by these mechanisms is **unnecessary**` | ✅ 判据都在（页面对「为什么」用了「**可以这样理解**」的措辞，属明示的重述 ✅） |
| L104「内存侧单元之间要互相协调，就需要**锁、屏障**这类机制，而远程调用加上**跨芯片的同步开销很大**」 | `===== PAGE 132 =====` 逐字：`**Locks** / **Barriers** / Synchronization is Necessary`；`===== PAGE 133 =====` 逐字：`(2) **Expensive communication across NDP units**` | ✅ 逐字对应 |
| L104 同步代表工作是 **SynCron**，源讲义「称它是**第一个面向近数据处理架构的端到端同步方案**」 | `===== PAGE 131 =====` 逐字：`Contribution: •**SynCron: the first end-to-end synchronization solution for NDP architectures**`；`===== PAGE 141 =====` 复现同一句 | ✅ **逐字对应（含「第一个」「端到端」）** |
| L108「SynCron 的**性能与能量**分别落在理想零开销同步的 **9.5%** 与 **6.2%** 以内」 | `PAGE 131` 逐字：`•SynCron comes within **9.5% and 6.2% of performance and energy** of an **Ideal zero-overhead synchronization** scheme`；`PAGE 141` 逐字复现 | ✅ **两个数 + 它们各自的归属（性能↔9.5、能量↔6.2）全对** |
| L112「两个更贴近程序员的问题：**数据结构**要不要为 PIM 改写，以及**虚拟内存**怎么支持」 | `===== PAGE 145 ===== How to Design Data Structures for PIM?`；`===== PAGE 146 ===== Virtual Memory Support` | ✅ 两节标题逐字对应 |
| L114「IMPICA，做法是把**地址翻译与访存解耦**：……**地址翻译用一张放在逻辑层里的小页表**」 | `===== PAGE 148 =====` 逐字：`q**Address-access decoupling**: enabling parallelism in the accelerator with low cost` / `q**IMPICA page table: low cost page table in logic layer**`；`===== PAGE 154 ===== Our Solution: Address-Access Decoupling`；`===== PAGE 157 ===== Our Solution: IMPICA Page Table` | ✅ **两条机制逐字对应**（**多出来的「一致性由硬件处理」见 P1-1**） |
| L114 数字「指针追踪 **1.2 到 1.9 倍**、数据库吞吐 **+16%**、能量 **−6% 到 −41%**」 | `===== PAGE 148 =====` 逐字：`q**1.2X –1.9X speedup for pointer chasing operations, +16% database throughput**` / `q**6% -41% reduction in energy consumption**`；柱值互证 `===== PAGE 160 =====`（`1.9X / 1.3X / 1.2X`）、`===== PAGE 161 =====`（`+16%`）、`===== PAGE 162 =====`（`-41% / -24% / -6% -10%`） | ✅ **五个数全对，且与三张图的柱值互证** |
| L114「代价是平均功耗增加 **5.6%**，面积增加 **0.45 平方毫米**」 | `===== PAGE 163 =====` 逐字：`◼ Power overhead: average power increases by **5.6%**` / `IMPICA (+32KB cache) **0.45mm2**` | ✅ 两个数都对（**同页另一个数见 P0-2**） |
| L116 VBI「问的是更根本的问题：今天**每个进程一张页表、由操作系统管理**的这套结构……还是最优的吗」 | `===== PAGE 166 ===== VBI: Overview` 逐字：`Conventional Virtual Memory` / `**Page Tables managed by the OS**` / `Processes`；`===== PAGE 165 =====` 标题逐字：`"The Virtual Block Interface: **A Flexible Alternative to the Conventional Virtual Memory Framework**"` ISCA 2020 | ✅ 逐字对应 |
| L116 VBI「引入「**虚拟块**」与一层放在**内存控制器**里的**地址翻译层**」 | `PAGE 166` 逐字：`VBI Address Space` / `VB 1 / VB 2 / VB 3 / VB 4` / **`Memory Translation Layer in the memory controller`** | ✅ **逐字对应（含「内存控制器里的翻译层」）** |
| L124/L128「源讲义在这一节列了一整套基础设施」，**DAMOV** 提供分析方法与负载 | `===== PAGE 168 ===== Benchmarks and Simulation Infrastructures`；`===== PAGE 171 =====` 逐字：`"**DAMOV: A New Methodology and Benchmark Suite for Evaluating Data Movement Bottlenecks**"` | ✅ 出处与「方法 + 基准套件」都对（**页面对它用途的概括见「我核不到的」第 3 条**） |
| L128「**Ramulator** 被扩展成支持 **PIM** 的仿真器」 | `===== PAGE 173 ===== Simulation Infrastructures for PIM` 逐字：`n**Ramulator extended for PIM**` / `qFlexible and extensible DRAM simulator` / `qCan model many different memory standards and proposals` | ✅ 逐字对应 |
| L128「配套的 **PrIM** 基准把负载按领域分好」+ 列出的 13 条 | `===== PAGE 174 ===== PrIM Benchmarks: Application Domains` 逐字给了 **16 个**基准：`Vector Addition VA` · `Matrix-Vector Multiply GEMV` · `Sparse Matrix-Vector Multiply SpMV` · `Select SEL` · `Unique UNI` · `Binary Search BS` · `Time Series Analysis TS` · `Breadth-First Search BFS` · `Multilayer Perceptron MLP` · `Needleman-Wunsch NW` · `Image histogram (short) HST-S` · `Image histogram (large) HST-L` · `Reduction RED` · `Prefix sum SCAN-SSA` · `Prefix sum SCAN-RSS` · `Matrix transposition TRNS`；`PAGE 175` 称其开源 | ✅ **页面把 16 个并成 13 条来念，16 个名一个不漏**（VA/GEMV/SpMV、SEL+UNI、BS、TS、BFS、MLP、NW、HST-S+L、RED、SCAN-SSA+RSS、TRNS） |
| L128「**NAPEL** 用**集成学习**预测近内存应用的性能」 | `===== PAGE 177 =====` 逐字：`"**NAPEL: Near-Memory Computing Application Performance Prediction via Ensemble Learning**"` DAC 2019 | ✅ 逐字对应（**`Ensemble Learning` = 集成学习，任务与手段都对**） |
| L128「**SoftMC** 是一块基于 **FPGA** 的开放平台，用来在**真实 DRAM 芯片上**做实验」 | `===== PAGE 178 =====` 标题逐字：`**An FPGA-based Test-bed for PIM?**`；正文逐字：`**SoftMC: A Flexible and Practical Open-Source Infrastructure for Enabling Experimental DRAM Studies** HPCA 2017` / `nFlexible` / `nEasy to Use (C++ API)` / `n**Open-source**` | ✅ **三项（FPGA / 开源 / 真 DRAM 实验）逐字对应** |
| L128「**MQSim** 则把这类研究推到 **SSD** 上」 | `===== PAGE 179 ===== Simulation Infrastructures for PIM (**in SSDs**)` 逐字：`"**MQSim: A Framework for Enabling Realistic Studies of Modern Multi-Queue SSD Devices**"` FAST 2018 | ✅ 逐字对应 |
| L136「**GRIM-Filter** 在**三维堆叠内存**里做过滤，**把需要精细比对的位置先筛掉**，加速**最高 3.7 倍**」 | `===== PAGE 183 ===== Executive Summary` 逐字：`We propose an in-memory processing algorithm **GRIM-Filter** for accelerating read mapping, **by reducing the number of required alignments**` / `We implement GRIM-Filter using in-memory processing **within 3D-stacked memory** and show **up to 3.7x speedup**` | ✅ **三项逐字对应（含「最高」这个限定）** |
| L136「**GenASM** 做**近似字符串匹配**」 | `===== PAGE 184 =====` 逐字：`"**GenASM: A High-Performance, Low-Power Approximate String Matching Acceleration Framework for Genome Sequence Analysis**"` | ✅ 逐字对应 |
| L136「**NERO** 面向**天气预测**的**模板计算**，直接**贴着高带宽内存**做」 | `===== PAGE 186 =====` 逐字：`"**NERO: A Near High-Bandwidth Memory Stencil Accelerator for Weather Prediction Modeling**"` | ✅ **三个要素（近 HBM / 模板计算 / 天气预测）逐字对应** |
| L136「**NATSA** 做**时间序列分析**」 | `===== PAGE 187 =====` 逐字：`"**NATSA: A Near-Data Processing Accelerator for Time Series Analysis**"` | ✅ 逐字对应 |
| L142 结论一「**需要重新审视整个栈**：从器件、逻辑、微架构、软硬件接口、程序语言、算法到系统软件」，并且「**可以一步一步来**」 | `===== PAGE 228 ===== We Need to Revisit the Entire Stack` 逐字给了全栈分层：`Micro-architecture / SW/HW Interface / Program/Language / Algorithm / Problem / Logic / Devices / System Software / Electrons` + **`We can get there step by step`** | ✅ **分层 + 「一步一步」逐字对应** |
| L142 结论二「要用好的原则，源讲义列了**六条**：数据为中心的系统设计、所有部件都具备智能、更好的跨层沟通与接口、优于最坏情况的设计、异构化、以及灵活与可适应」 | `===== PAGE 229 ===== We Need to Exploit Good Principles` 逐字：`n**Data-centric system design**` / `n**All components intelligent**` / `n**Better cross-layer communication, better interfaces**` / `n**Better-than-worst-case design**` / `n**Heterogeneity**` / `n**Flexibility, adaptability**` / `Open minds` | ✅ **六条逐字对应，一条不多一条不少** |
| L146「闪存刚出现时同样被当成「**可疑的技术**」，而**二十年后**它改变了存储」 | `===== PAGE 230 ===== If In Doubt, See Other Doubtful Technologies` 逐字：`nA very "**doubtful**" emerging technology` / `q**for at least two decades**`；`===== PAGE 231/232 ===== Flash Memory Timeline` | ✅ **「可疑」与「二十年」两个要素逐字对应** |

**算术自检（方法⑤，只用源里同时给出的两组约束）**：
- `HMC-MC` 那台：`128 核 × 2GHz`（p30）与其带宽 `640GB/s`（p30）；`Tesseract` 那台带宽 `8TB/s`（p30）与 p32 的 `2.9TB/s` 峰值形成对照 —— 页面没有引用这些数，**不存在需要解方程的缺口**。
- `55.4% / 54.2%`（p43 总结）与 `49.1% / 44.6%`（p48/p49 的 PIM core）+ `55.4% / 54.2%`（p48/p49 的 PIM accelerator）三页互证：**p43 的「平均」= PIM accelerator 那一列**，页面引的是 p43 的口径，**没有把 49.1 与 55.4 混用** ✅。
- `Word` 一侧：`57.3% × 54.4% ≈ 31.2%` 的推理能量来自打包/量化 —— 源没给这个乘积，页面也没写，**我没有替它乘**。

---

## 两讲分工交叉核对（第 4 讲「用内存」↔ 第 5 讲「近内存」）

> 本节按派单要求做「两讲有没有重复或冲突」。依据是本项目**两页正文** + **两份源讲义**。

### 冲突 1（已记 **P1-3**）：「不改结构」vs「改单元结构」

- 第 5 讲页面 L22：「上一讲的做法是『用内存自己来算』：**不改结构**，只利用 DRAM 内部已有的能力。」
- 第 4 讲页面 L116：「按位非（NOT）不能用多数函数直接得到，源讲义给的办法是**改单元结构**：用一个『双接触』单元……」
- 源（第 4 讲讲义）：`PAGE 122 In-DRAM NOT: Dual Contact Cell`、`PAGE 135 … requires minimal changes to DRAM architecture`。
- ⇒ **两页对同一件事给了相反的形容词**。第 5 讲页面的「不改结构」是**过度概括**（对 RowClone 成立，对 Ambit 的 NOT 不成立）。**建议统一口径为「改动很小（with small changes）」**。

### 重复 1（不算错，但请 Lead 判断）：`62.7%` 在两页各讲一次

- 第 4 讲页面 L38「对 Google 消费级设备负载的实测……**62.7%** 花在数据搬运上」（源：第 4 讲讲义 p27）。
- 第 5 讲页面 L52「第一条我们前面见过：系统总能量的 **62.7%** 花在 data-movement 上」（源：第 5 讲讲义 p42/p60）。
- **源讲义自己就重复了这张幻灯片**（第 4 讲 p27 与第 5 讲 p42/p60 是同一组数据）。
- **判断**：第 5 讲页面写了「我们前面见过」，属**有意的回指**，不是重复劳动 ⇒ **我判为不算错**，仅记录在此。

### 重复 2（值得 Lead 注意）：产业版图（UPMEM / 三星 FIMDRAM+HBM-PIM / AxDIMM / SK 海力士 AiM / 阿里 HB-PNM）

- 第 4 讲页面 L58 用整段介绍了这五家的产品，并说「源讲义也提到还有大量实验芯片与创业公司在做」——**源：第 4 讲讲义 p38 + p43–60**（我已核过，全对）。
- **同一批幻灯片在第 5 讲讲义里也出现**：`lecture4-processing-near-memory.pdf.embedded.txt` 的 `PAGE 198–205` 逐字为
  `UPMEM Processing-in-DRAM Engine (2019)` / `2,560-DPU Processing-in-Memory System` / `Samsung Function-in-Memory DRAM (2021)` / `Samsung AxDIMM (2021)` / `SK Hynix AiM: System Organization (2022)` / `Alibaba HB-PNM: Overall Architecture (2022)`。
- 而**第 5 讲页面（近内存这一讲）完全没有提这五家**。
- ⇒ 两页**没有重复**（第 5 讲页面把它们留给了第 4 讲页面），这是**作者的有意分工**。
- **但这里有一个口径风险**：这五个产品按本课自己的分类（两份讲义都有 `Processing in Memory: Two Approaches — 1. Processing using Memory / 2. Processing near Memory`，第 5 讲讲义 p4）**属于近内存那一路**（在 DRAM/HBM 里放计算单元），却只被写在**「用内存」那一讲**的页面里。
  源讲义之所以把它放在「用内存」那一讲，是因为它在源里被挂在 `PAGE 37/39/62 Why In-Memory Computation Today?`（讲**整个 PIM 今天为什么可行**），不是讲「用内存」的机制。
- **建议（不是错误，是**衔接**建议）**：在第 5 讲页面加一句「这些产品的具体形态见第 4 讲那一节的产业版图」，或在第 4 讲那句后面加一句「它们属于**近内存**那一路，下一讲展开」。
  这样读者不会把「PIM 产品」误当成「用内存」的技术证据。

### 分工本身：清晰，无重叠机制

| | 第 4 讲页面（用内存） | 第 5 讲页面（近内存） |
| --- | --- | --- |
| 机制 | RowClone · Ambit（批量按位）· SIMDRAM · ComputeDRAM · PiDRAM · Pinatubo | Tesseract · 手机简单函数卸载 · PEI · TOM/CoNDA/SynCron/IMPICA/VBI |
| 共同框架 | 两份讲义都用 `Two Approaches` 那一页来切分（第 4 讲 p67、第 5 讲 p4）✅ 页面没有把两路的机制混讲 | 第 5 讲页面 L22/L150 明确回指「上一讲用内存自身的原理，本讲在内存旁边放计算单元」✅ |
| 基础设施 | 无 | DAMOV / Ramulator+PrIM / NAPEL / SoftMC / MQSim —— **只出现在第 5 讲页面** ✅（我核过第 4 讲页面全文，没有任何一条提到它们） |

⇒ **结论：两讲的机制分工清晰、没有机制被讲两遍；只有「不改结构」这一处**互相矛盾**，以及产业版图需要一句衔接说明。**

---

## 我核不到的（诚实记录）

> 「我没搜到」≠「源材料没有」。下面几条我**没有**找到源材料依据 ⇒ 我**不能**说它对或错。

1. **L24「源讲义把两张图**并列**摆出来」的版式判断**：我核到了 `PAGE 18` 同时含 `3D-Stacked Logic+Memory`（`Logic` / `Memory`）与 `Other "True 3D" technologies under development`，
   但**「两张图是并列排的」这件事在文本层看不出来**（图内版式抽不出来）⇒ 我不判它对错。
2. **L132「它们共有的特征是两个：**数据量大，而每份数据上的计算简单**」**：这是页面对 GRIM-Filter / GenASM / NERO / NATSA 的**归纳**。
   源里最接近的是 `PAGE 43` 的 `a significant fraction of the data movement often comes from simple functions`（讲的是**手机那四个负载**）与 `PAGE 180` 的 `Applications that Benefit from PIM`（只有一个章节标题）。
   ⇒ **源里没有针对这四条的「数据量大 + 计算简单」这句话**。它是合理归纳，但**不是源的原话**。
3. **L128 对 DAMOV 用途的概括「哪些应用真的能从近数据计算里受益」**：我只核到 `PAGE 169/171` 的标题是 `DAMOV: A New Methodology and Benchmark Suite for **Evaluating Data Movement Bottlenecks**`，
   以及 `PAGE 170` 的输出里有 `Compute-Bound` / `Memory Bottleneck Classes`。
   ⇒ 「能从**近数据计算**受益」这一层没有逐字依据（「搬运瓶颈分类」与「近数据计算受益」不是同一句话），我不判其对错。
4. **L140「源讲义花了很长篇幅讲采纳问题」**：源的采纳一节是 `PAGE 96–103`（约 7 页）—— 我**不能**替它定义「很长」。
   （这条我并进了 **P1-4**，因为它与同一句里那条无出处的「系统厂商」障碍是同一个问题。）
5. **交叉核对文件不独立**：`text-clean/eth-ca2022-lecture4-processing-near-memory.txt` 与 `evidence/…pdf.embedded.txt` **逐字节相同**（`==` 为 `True`）。
   ⇒ 与第 4 讲同样的缺口：我**没有**第二份独立抽取可交叉验证。本记录全部结论建立在**同一份**文本层之上。
   （第 4 讲那一份 `text-clean/…lecture3…txt` 也是逐字节相同 —— 这是**两讲共有的**方法学缺口。）
6. **配图的视觉细节**：我读完了**全部 11 张**的 `<title>`/`<desc>`/全部 `<text>`；**逐张通读完整 SVG 源码与几何**的是 `-1`、`-2`
   （抓 P0-1、P0-3 所需）；其余 9 张我**没有**逐张读源码几何（只核了文案与正文的一致性）。
   **去重说明**：`-4`（「还得等 B 回来」时序矛盾）与 `-7`（「理想 100%」满条与注释度量矛盾）的**图内自相矛盾**已由**已有的视觉复核**判为「有错误」，
   属**视觉复核的职责**、不是源侧事实问题 ⇒ 我**不重复立案**，只在此指出这两张图在修图时要一并处理。
7. **L36「3600 万 / 14 亿 / 3 亿 / 300 亿」的单位**：源逐字是 `36 Million` / `1.4 Billion` / `300 Million` / `30 Billion`，页面写成「3600 万 / 14 亿 / 3 亿 / 300 亿」——
   中文数量级换算我逐条算过：36M=3600 万 ✅、1.4B=14 亿 ✅、300M=3 亿 ✅、30B=300 亿 ✅。**四条都对**（记在这里是因为它属于「换算类」，容易错）。
8. **faithful 层的边界**：本讲我**没有**跑 faithful 探针（只在第 4 讲讲义上跑过一次，结论见第 4 讲记录）。⇒ 本讲**没有**第二份独立抽取可交叉验证，这一点与第 5 条是同一个缺口。

---

## 覆盖面

- **配图（两类必须分开写）**：
  - **逐张通读（读完整 SVG 源码、核几何 + 文案，2 张）**：`-1`（上下堆叠顺序 → 抓到 P0-3）、`-2`（`相对 DDR3 顺序核` 逐字 → 抓到 P0-1）。
  - **全文文本抽取（程序抽取全部 11 张的 `<title>`/`<desc>`/所有 `<text>`，逐张比对与正文的一致性）：11 张全部**（`-1` 至 `-11`）。
  - **只取哈希入档、未读**：**0 张**。另：11 份**已有**视觉复核报告的 `复核对象 SHA256` 我**逐份比对过，11/11 与当前 SVG 一致**（即复核未过期）。
  - 结论：**11/11 张的文案都核过；2/11 张的几何逐张读过**；其余 9 张的几何未读（见「我核不到的」第 6 条）。
- **已验（源侧，逐字 + PAGE）**：236 页；p18 3D 堆叠 + `Other "True 3D" technologies under development`；p20 两条问题与两个子情形；
  p21 四个图规模数（36M/1.4B/300M/30B）；p22 代码行 + 两条瓶颈；p23/24/28 Tesseract 结构、非缓存物理寻址接口、非阻塞远程函数调用；
  p30 被评测系统（含 `DDR3-OoO` / `128 In-Order` / `8 OoO`）；p31 `>13X` + `five graph algorithms` + `+56% +25%`；p34 `> 8X Energy Reduction`；
  p35 五条缺点；p41/p44 四个负载；p42/p60 `62.7%`；p43 两条观察 + `lightweight logic` + `fixed-function` + `55.4% and 54.2%`；
  p45 `57.3%` + `54.4%`；p46 打包 + p47 量化；p48/p49 拆解数（49.1/44.6）；
  p51–59 Chrome 渲染与标签页压缩（含 ZRAM、`implemented as PIM logic`）；p61–69 GPU/链表/依赖缺失/预取/气候/近似匹配/时序；
  p71/72 PEI 两条 Key Idea（四条「不变/极小改/不需映射」逐字）、p75 `Always Executing in Memory? Not A Good Idea`、p76 `pfence` 与原子性、p78 三个结果、p79 十个负载名、p86 两条缺点；
  p98 五条采纳障碍；p104/109/110/111/112–114/115/116–127 四个挑战与 CoNDA（含 `10.4% and 4.4%`）；p128–143 SynCron（`first end-to-end` + `9.5% and 6.2%`）；
  p145/146/147–164 IMPICA（`1.2X–1.9X` / `+16%` / `6%-41%` / `5.6%` / `0.45mm2` / `L2 Cache 5 mm2 per MB`）、p165–167 VBI；
  p168–179 五件基础设施（DAMOV / Ramulator-PIM / PrIM 16 个基准名 / NAPEL / SoftMC / MQSim）；p180–187 四件应用（`3.7x` / GenASM / NERO / NATSA）；
  p213–232 结论（p228 全栈 + `step by step`、p229 六条原则逐字、p230 `doubtful` + `at least two decades`、p231/232 Flash 时间线）。
- **未验**：上面「我核不到的」8 条。

---

**核对人声明**：本记录只覆盖开头那个哈希的版本（`CE3CAD5F8302169D`，15875 B）。按附录五，对其它版本的结论不成立。
本记录只读正文与配图、只在 `docs/audit/` 下写我自己这两份文件；**未修改任何 `content/` 文件**；`status` 由 Lead 处理。
