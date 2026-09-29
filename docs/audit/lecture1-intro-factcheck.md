# 事实核对 · ETH Zürich Computer Architecture（Fall 2022 · Onur Mutlu）第 1 讲 导论与基础：体系结构在解决什么问题

- 核对人：**非作者**（复用 mit-6.5840 七讲记录的核对者身份 `mit65840-reviewer`）
- 核对日期：2026-09-29
- 被核对版本：`content/01-lecture1-intro/index.md`
  归一化 SHA256 前16 = **BE5E6549B44AA9AD**
  （全 64 位 `BE5E6549B44AA9AD0521F521B5F697D3D3FEA00ADAEEFF35A5FF3C4007571DE1`，21846 B，最后写入 2026-09-29 9:47:19）
- 源材料（逐个列出，并说明我怎么用它）：

| # | 文件 | 用途 |
| --- | --- | --- |
| 1 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture1-intro.pdf.embedded.txt`（3864 行，106837 B） | **主要依据**：本讲义 PDF（Fall 2022 afterlecture，335 页）的内嵌文本抽取，带 `===== PAGE n =====` 页码标记 |
| 2 | `_sources/eth-ddca-ca/text-clean/eth-ca2022-lecture1-intro.txt` | 交叉核对（clean 版：只做了去空格，与 #1 同源） |
| 3 | `_sources/eth-ddca-ca/evidence/eth-ca-fall2022-schedule.html` | 核「第 3、4 讲展开这两条路」这条**课程结构**声明 |
| 4 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture2a-memory-trends.pdf.embedded.txt` + `text-clean/eth-ca2022-lecture2a-memory-trends.txt` | RAIDR 在这门课里的**第二处出现**（也用来确认 15%/47% 没有被别处解释） |
| 5 | 我自己跑 `_sources/_audit/pdftext3.py`（faithful 抽取）在 `evidence/eth-ca2022-lecture1-intro.pdf` 上，输出放在临时目录 | **结论：这份 PDF 上 faithful 是坏的**，见下面的「提取层说明」 |

> **提取层说明（影响本记录的可信边界，先写清楚）**
> 按 Lead 的规则「课件文本层一律用 `.faithful.txt`」，我在本 PDF 上跑了 `pdftext3.py`：产出 113993 字符，但**内容是乱码**——
> 在 faithful 输出里检索 `Computer Architecture` / `Mutlu` / `Stadelhofen` / `Cerebras` / `FLOPS` / `transformation` / `Memory Refresh`
> **全部 0 命中**（唯一命中 `15%` 的那一行是 `>?,.7,5@%0,7,@/7,15%0"*+/5,.0.%/$"%!AB%"=="*7,2"` 这样的乱码）。
> ⇒ **这和我之前核的 mlsys 情形不同**：Lead 说 faithful 对 mlsys 的 9 份缓存 PDF 是 9/9 可用的；
> **但它对这份 ETH PDF 不可用**。所以我改以 **#1（原始内嵌抽取）**为主要依据，#2 为交叉核对，并在下面每条结论都写出**页码 + 逐字引用**。
> ⇒ 我**没有**把任何结论建立在 faithful 的输出上。**如果哪天 faithful 能正确解这份 PDF，P1-1（关于 15%/47%）应当用它复查一遍。**

---

## 结论

**P0（事实错误）：0 条 ｜ P1（易误解/依据不足）：3 条 ｜ P2（措辞）：3 条**

本讲的事实密度很高（矩阵乘法的 7 个数字、7 处平台规格、6 段逐字引用、4 处文献归属），**我逐个回源核过，全部命中**；
没有发现与源冲突的陈述。P1 都属「讲义文本层里没有这段依据」或「漏一层」，不是判错。

---

## ★ 作者交出的不确定点：RAIDR 的 15% / 47%（本讲第一优先，也是 P1-1）

**正文 L214 说**：
> 源讲义在这里引了 Liu 等人在 ISCA 2012 的 RAIDR 工作，那一页上给出两个数字：15% 与 47%。**按这篇工作的说法，这是刷新占 DRAM 能量的比例，现在大约在 15% 这一档，而容量继续增长之后会逼近 47%。**

**讲义文本层里那一页（PAGE 334，全 335 页里的倒数第二页）全文只有**：
```
Another Example: Memory Refresh
334
15%
47%
Liu et al., “RAIDR: Retention-Aware Intelligent DRAM Refresh,”ISCA 2012.
```
⇒ **两个数字在源里（✅ 数字本身有出处）；但它们是什么的比例、以及「现在 15% → 容量增长后 47%」这条映射，讲义文本层没有写。**
（该页标题只说这是「Memory Refresh」的另一个例子，并给出 RAIDR 出处。）

**我为此做过的检索（范围与检索词，逐条给出）**：
1. 范围 `_sources/eth-ddca-ca/` 全树，正则 `RAIDR` ⇒ 5 命中：`text-clean/…lecture1…` L3857、`evidence/…lecture1….embedded.txt` L3857、
   `text-clean/…lecture2a…` L976、`evidence/…lecture2a….embedded.txt` L976、`_t2.txt` L91。**没有一处解释这两个数字。**
2. 同一范围，正则 `[Rr]efresh.{0,80}(15|47)\s?%` 与 `(15|47)\s?%.{0,80}[Rr]efresh` ⇒ **0 命中**。
3. lecture2a（`Memory Trends`，同一门课另一讲）里 RAIDR 只出现在一份论文清单里（`Solution 1: New Memory Architectures` 的第 1 条）⇒ 那里也没有解释。
4. 我跑 faithful 抽取后再查 ⇒ 输出乱码，不可用（见上）。

⇒ **裁决：P1（依据不足）。** 我不能说这条是错的 —— 15%/47% 就在那一页上、页题也是 refresh，**该页很可能有一张图**
（PDF 里那两行数字是文本对象，图的坐标轴/单位可能是图像或矢量图，文本层里没有）—— 但**本讲义的文本层不支持「这是刷新占 DRAM 能量的比例」以及「现在 vs 将来」这条映射**。
**建议（三选一）**：① 在句末注明「比例的坐标轴含义来自该页的图，讲义文本层未写出」；② 补引 RAIDR 原文（ISCA 2012）给出处；③ 把它降级成「那一页给出 15% 与 47% 两个数字」并说明我们对其含义的解读。

**另外：这一页的图与正文口径不一致（同一处，第二半）**
- 正文 L214：**现在**大约 15%，**容量继续增长之后**逼近 47% ⇒ 时间轴上的两端。
- `figures/lecture1-intro-11.svg` 的图内可见文字（`T07`/`T08`）：`刷新占 DRAM 能量` / `15% 到 47%` ⇒ **读起来是一个区间**；
  它的 `desc` 同样写「刷新占了 DRAM 能量的 **15% 到 47%**」。
⇒ 同一件事，正文说「现在→将来」，图说「15%–47%」。这对**只读图**的读者是不同的事实。**建议把图与 `desc` 改成与正文同口径**（如「现在 ≈15%，容量增长后趋近 47%」）。

---

## 已核对通过（逐条附页码/行号，供下一个复核者直接关闭）

| 正文 | 源（页码 · 逐字引用） | 结果 |
| --- | --- | --- |
| L22 矩阵乘法 5 个耗时 + L24/L68/L70/L72/L74 的 5 个倍数 | **PAGE 7** 表格：`Python 25,552.48 1x` / `Java 2,372.68 11x` / `C 542.67 47x` / `Parallel loops 69.80 366x` / `Parallel divide and conquer 3.80 6,727x` / `plus vectorization 1.10 23,224x` / `plus AVX intrinsics 0.41 62,806x`；页题 `Multiplying Two 4096-by-4096 Matrices` | ✅ **7 个数字 + 5 个倍数全对**（正文按四舍五入写成 25552.48，源里是 25,552.48） |
| L34 四条理由（更快/更便宜/更小/更可靠；新应用：3D 可视化、VR、自动驾驶、个人基因组；更好的解法；理解计算机怎么工作） | **PAGE 97**：`smaller, more reliable, …` / `Enable new applications` / `Life-like 3D visualization 20 years ago? Virtual reality?` / `Self-driving cars?` / `Personalized genomics? Personalized medicine?` / `Enable better solutions to problems` / `Software innovation is built on trends and changes in computer architecture` / `Understand why computers work the way they do` | ✅ 逐条对应 |
| L38 「335 页」「第 190 至 285 页整段标注 Further Slides for Your Own Study」 | **PAGE 190**：`Further Slides for Your Own Study (May Be Covered in Future Lectures)`；**PAGE 285** = `Takeaways`；**PAGE 286** = `Let's Start with Some Fundamentals`（主线在此恢复）；最后一个页码标记 = `===== PAGE 335 =====` | ✅ 起点、终点、总页数都对 |
| L42 层次名单 | **PAGE 94** `The Transformation Hierarchy` 的框：`Problem / Algorithm / Program/Language / System Software / SW/HW Interface / Micro-architecture / Logic / Devices / Electrons`；**PAGE 95** 亦含 `Runtime System` | ⚠️ 见 **P1-2**（漏了 System Software / Runtime System 这一层） |
| L44 算法三要求（有限性、确定性、可有效计算）+「同一个问题可以有很多个算法」 | **PAGE 327**：`can be carried out by a computer -Finiteness -Definiteness -Effective computability` / `Many algorithms for the same problem` | ✅ 逐字对应 |
| L50 ISA 引文 | **PAGE 327**：`ISA (Instruction Set Architecture) / Interface/contract between SW and HW. / What the programmer assumes hardware will satisfy.` | ✅ 逐字对应 |
| L54 「微架构是 ISA 的实现」 | **PAGE 327**：`Microarchitecture / An implementation of the ISA`；`Digital logic circuits / Building blocks of micro-arch (e.g., gates)` | ✅ 定义对（举例部分见 P2-1） |
| L86 公理引文 | **PAGE 101**：`Axiom / To achieve the highest energy efficiency and performance: we must take the expanded view of computer architecture` | ✅ 逐字对应 |
| L90 扩展视野的两句话 | **PAGE 101**：`Co-design across the hierarchy: Algorithms to devices / Specialize as much as possible within the design goals` | ✅ 逐字对应 |
| L94 费曼 1959 / Leiserson 2020；「上面和下面都有很大空间…」引文 | **PAGE 6/7**：`Richard Feynman, "There's Plenty of Room at the Bottom: An Invitation to Enter a New Field of Physics", a lecture given at Caltech, 1959.` / `Leiserson+, "There's plenty of room at the Top: What will drive computer performance after Moore's law?", Science, 2020`；**PAGE 105** `Axiom, Revisited`：`There is plenty of room both at the top and at the bottom but much more so when you communicate well between and optimize across the top and the bottom` | ✅ 两条观察的年份、作者、指向（底＝更小尺度；顶＝摩尔定律之后）都对 |
| L106 自动驾驶平台：260 mm²、60 亿晶体管、600 GFLOPS GPU、12 个 2.2 GHz ARM、两颗冗余芯片 | **PAGE 84**（并与 **PAGE 192** `TESLA Full Self-Driving Computer (2019)` 同一组数字）：`ML accelerator: 260 mm2, 6 billion transistors, 600 GFLOPS GPU, 12 ARM 2.2 GHz CPUs.` / `Two redundant chips for better safety.` | ✅ 五个数全对；归属（自动驾驶）也对 |
| L108 TPU：2021 年每颗 250 TFLOPS、TPU3 90 TFLOPS、一块板 1 ExaFLOPS | **PAGE 83**：`250 TFLOPS per chip in 2021` / `vs 90 TFLOPS in TPU3` / `1 ExaFLOPS per board` | ✅ 三个数全对，方向也对（250 是新的一代、90 是上一代） |
| L116 体系结构定义引文 | **PAGE 93**：`The science and art of designing, selecting, and interconnecting hardware components and designing the hardware/software interface to create a computing system that meets functional, performance, energy consumption, cost, and other specific goals.` | ✅ 逐字对应 |
| L124 当下困难清单（9 项）+「没有清楚确定的答案」 | **PAGE 98**：`Huge hunger for data and new data-intensive applications` / `Power/energy/thermal constraints` / `Complexity of design` / `Difficulties in technology scaling` / `Memory bottleneck` / `Reliability problems` / `Programmability problems` / `Security and privacy issues` / `No clear, definitive answers to these problems` | ✅ 九项 + 结论句逐条对应 |
| L126 「第 285 页 Takeaways 总结成五堵墙：能量、可靠性、复杂度、安全、可扩展性」+ 该页在自学段里 | **PAGE 285**：`Five walls: Energy, reliability, complexity, security, scalability`（且 190≤285≤285，确在自学段内） | ✅ 逐字对应 |
| L132 RowHammer / Meltdown / Spectre 的机理描述 | **PAGE 234/235**：`Security: Meltdown and Spectre (2018)` / `Speculative execution leaves traces of secret data in the processor's cache (internal storage)` / `It brings data that is not supposed to be brought/accessed if there was no speculative execution`；RowHammer 在自学段有整块（**PAGE 200+**：`The Story of RowHammer`、`RowHammer bit flips 1) in more rows and 2) farther …` 等共数十页） | ✅ 两类问题的机理与归属都对 |
| L138 Cerebras WSE-2：2.6 万亿晶体管、46225 mm²；当时最大 GPU 542 亿晶体管、826 mm² | **PAGE 90**：`Cerebras WSE-2 / 2.6 Trillion transistors / 46,225 mm2` / `Largest GPU / 54.2 Billion transistors / 826 mm2`（并注 `NVIDIA Ampere GA100`） | ✅ 四个数全对 |
| L142 「第 267 页写了：计算被数据卡住了」+「第 267 至 274 页在自学段里」 | **PAGE 267** 全文：`The Problem / Computing is Bottlenecked by Data`；**PAGE 274** 是这组的最后一页，**PAGE 275** 换题 ⇒ 区间 267–274 ✅；190 ≤ 267 ✅ | ✅ 页码与引文都对 |
| L144 Dally（HiPEAC 2015）：一次内存访问 ≈ 一次复杂加法的 100–1000 倍 | **PAGE 274**：`Data Movement vs. Computation Energy / Dally, HiPEAC 2015 / A memory access consumes ~100-1000X the energy of a complex addition` | ✅ 逐字对应（**PAGE 279** 另有 `~1000X` 的版本） |
| L148 移动端实测 62.7% + 浏览器/ML 框架/视频编解码并列出现 | **PAGE 273**：`62.7% of the total system energy is spent on data movement`；**PAGE 271/272**：`Chrome / Google's web browser`、`TensorFlow Mobile / Google's machine learning framework`、`Video Playback / Video Capture / Google's video codec` | ✅ 62.7% 与四个负载都对（「同一页」的措辞见 P2-3） |
| L160/L162/L164 内存内计算两条路的名字与区分 | **PAGE 155**：`Processing in Memory: Two Approaches / 1. Processing near Memory / 2. Processing using Memory`；`Processing near Memory` 另在 PAGE 143/148/155/160 反复出现 | ✅ **两个名字逐字对**，连「反复讲」这个说法也对 |
| L168 「课程后面有整整两讲（第 3、4 讲）展开这两条路」 | `eth-ca-fall2022-schedule.html`：`L3: Processing using Memory`、`L4: Processing near Memory` | ✅ **第 3、4 讲确实是这两条路** |
| L172 Bahnhof Stadelhofen（卡拉特拉瓦早期作品、直线与直角很少见）+ 另一座车站做对比 | **PAGE 288**：`Bahnhof Stadelhofen: "The train station has several of the features that became signatures of his work; straight lines and right angles are rare." / ETH Alumnus, PhD in Civil Engineering`；**PAGE 289** `Compare To This` | ✅ 逐字对应 |
| L174 作业：去看那座车站；截止时间学期内任意时刻、讲义建议晚点做 | **PAGE 299**：`Your First Comp Arch Assignment / Go and visit Bahnhof Stadelhofen` / `Due date: Any time during this course` / `Later during the course is better` / `Strengths, weaknesses, goals of design` | ✅ 逐字对应 |
| L176 评估清单 8 项 +「怎么判定好不好永远是关键问题」 | **PAGE 305**：`Aside: Evaluation Criteria for the Designs / Functionality (Does it meet the specification?) / Reliability / Space requirement / Cost / Expandability / Comfort level of users / Happiness level of users / Aesthetics / …` / `How to evaluate goodness of design is always a critical question.` | ✅ 八项逐条对应 |
| L182 卡拉特拉瓦引文 | **PAGE 307**：`"To me, there are two overriding principles to be found in nature which are most appropriate for building: one is the optimal use of material, the other the capacity of organisms to change shape, to grow, and to move." Santiago Calatrava` | ✅ 逐字对应 |
| L188 弗兰克·劳埃德·赖特引文 | **PAGE 317**：`A Quote from The Other Famous Architect / "architecture […] based upon principle, and not upon precedent" (Frank Lloyd Wright)` | ✅ 逐字对应 |
| L196 库恩《科学革命的结构》三种科学阶段 | **PAGE 283/284**：`Recommended book: Thomas Kuhn, "The Structure of Scientific Revolutions" (1962) / Pre-paradigm science: no clear consensus in the field / Normal science: dominant theory used to explain/improve things (business as usual); exceptions considered anomalies / Revolutionary science: underlying assumptions re-examined` | ✅ 逐字对应（正文只用了后两种，未失真） |
| L200 抽象的例子：写 Java 的人不需要知道 ISA | **PAGE 330**：`E.g., high-level language programmer does not really need to know what the ISA is and how a computer executes instructions` / `programming in Java vs. C vs. assembly vs. binary vs. by specifying control signals of each transistor every cycle` | ✅ 逐字对应 |
| L204 两组「如果」（软件侧 5 条、硬件侧 2 条） | **PAGE 331**：`The program you wrote is running slow?` / `does not run correctly?` / `consumes too much energy?` / `Your system just shut down and you have no idea why?` / `Someone just compromised your system and you have no idea how?` / `The hardware you designed is too hard to program?` / `The hardware you designed is too slow because it does not provide the right primitives to the software?` | ✅ 两组逐条对应 |
| L208 课程的两个目标 | **PAGE 332**：`Two key goals of this course are / to understand how a processor works underneath the software layer and how decisions made in hardware affect the software/programmer / to enable you to be comfortable in making design and optimization decisions that cross the boundaries of different layers and system components` | ✅ 逐字对应 |
| L212 多核例子（每核带二级缓存、共享三级缓存、内存控制器、内存条） | **PAGE 333/334**：`CORE 0..3` / `L2 CACHE 0..3` / `SHARED L3 CACHE` / `DRAM INTERFACE` / `DRAM MEMORY CONTROLLER` / `DRAM BANKS`（AMD Barcelona 版图） | ✅ 层次与顺序都对 |
| L214 RAIDR 的两个数字本身 | **PAGE 334**：`Another Example: Memory Refresh / 15% / 47% / Liu et al., "RAIDR: ..." ISCA 2012` | ✅ 数字有出处；**含义见 P1-1** |

---

## P1 · 易误解或依据不足

### P1-1（本讲第一优先）★ RAIDR 的 15% / 47%：数字有源，含义无文本层依据；且图与正文口径不一致

- **正文 L214**：「…那一页上给出两个数字：15% 与 47%。按这篇工作的说法，这是刷新占 DRAM 能量的比例，现在大约在 15% 这一档，而容量继续增长之后会逼近 47%。」
- **源（PAGE 334）全文**：`Another Example: Memory Refresh` / `334` / `15%` / `47%` / `Liu et al., “RAIDR: Retention-Aware Intelligent DRAM Refresh,”ISCA 2012.`
- **差在哪**：源页只有两个数字 + 一句出处，**没有说这两个数字是什么的比例，也没有说「现在 15%／将来 47%」**。
- **我找过的范围与检索词**：`_sources/eth-ddca-ca/` 全树；`RAIDR`（5 命中，无一解释）；`[Rr]efresh.{0,80}(15|47)\s?%`、`(15|47)\s?%.{0,80}[Rr]efresh`（0 命中）；
  同门课 lecture2a 的 RAIDR 出现处（只是论文清单）；faithful 抽取（乱码，不可用）。
- **裁决**：**P1（依据不足），不是判错**——那页很可能有一张图，坐标轴/单位是图像或矢量对象，文本层拿不到。**⇒ 见「我核不到的」。**
- **附带（图/正文不一致）**：`lecture1-intro-11.svg` 的可见文字与 `desc` 写「刷新占 DRAM 能量 `15% 到 47%`」（区间），而正文写「现在 15% → 将来逼近 47%」（两端）。
- **修在**：① L214 句末注明「比例含义来自该页的图，文本层未写出」或补引 RAIDR 原文；② 把图的 `15% 到 47%` 与 `desc` 改成与正文同口径。

### P1-2 变换层次漏了一层（System Software / Runtime System）

- **正文 L42**：「源讲义给这张地图起的名字是 transformation-hierarchy。**从最上面往下数：问题、算法、程序与语言、ISA、微架构、逻辑电路、器件与电子。**」（7 项）＋图 `lecture1-intro-1.svg` 也只画 7 格。
- **源 PAGE 94**（`The Transformation Hierarchy`）的框里有：`Problem / Algorithm / Program/Language / **System Software** / SW/HW Interface / Micro-architecture / Logic / Devices / Electrons`；
  **PAGE 95** 的同一张图也用 `**Runtime System**` 标出这一层。
- **差在哪**：正文把这一层整段省略了，而这是一份「从最上面往下数」的**完整枚举**；读者会以为层次只有 7 层。
- **修在**：在列表与图里补上「系统软件／运行时（VM、OS、MM）」一格（源就把它画在「程序与语言」与「ISA」之间），
  或明确写「为便于讲解，本页只画主线七层，源图里还有系统软件/运行时这一层」。

### P1-3 两处「墙」的机理没有文本层依据，也没有标为我们补的背景

- **L130（能量墙）**：「过去几十年，工艺每进步一代，电压就能跟着降一档，频率还能往上提，功耗密度大体不变…这条规律停下来之后，电压降不动了，频率也不敢再往上拉，芯片上就出现了『有些区域必须在某个时刻关掉』的局面。」
- **L134（复杂度墙）**：「…核数、缓存层次、一致性协议、加速器都在增加，需要验证的状态空间随之爆炸。」
- **源里有什么**：`Five walls: Energy, reliability, complexity, security, scalability`（PAGE 285）与 `Complexity of design`（PAGE 98）——**只有墙的名字**。
- **我找过的检索词**（范围 = 本讲义文本层）：`Dark|Dennard|Voltage|voltage|Power density|power density|Frequency|frequency` ⇒ **1 命中**（是 RowHammer 论文标题里
  `Reduced Wordline Voltage`，与本段无关）；`verification|state space` ⇒ **0 命中**。
- **裁决**：**P1（依据不足）**。这两段是**标准教科书内容、很可能没错**，但它们在本讲的文本层里**找不到依据**——
  读者会把它们当成讲义给的机理（这一段紧跟「源讲义把当下的困难列成一串」）。
  **注意**：这两段也可能来自**纯图页**（电压/频率随年份的曲线、验证复杂度示意图），那样文本层本来就抓不到 ⇒ 所以我说的是「依据不足」而不是「错」。
- **修在**：加一句「以下机理是我们为解释这两堵墙补的背景（讲义只列了墙的名字）」，或补引对应页的图号。
- **补充事实（来自 git 历史，不是我的核对结论）**：本讲有 commit `bea337f`「第 1 讲 Dennard 缩放表述纠错（电压降一档、频率继续提升，而非电压频率一起降）」——
  说明 L130 这句话作者已经**专门改过一轮**。这**不改变**「讲义文本层里没有这段依据」这个判断（我核的是当前版本 `BE5E6549` 的文本层），
  但说明作者是把它当成需要严谨对待的陈述在维护的，所以我把它留在 P1 而不是 P2。

---

## P2 · 措辞

### P2-1 L54 对微架构的举例未标来源

正文 L54：「几级 pipeline、要不要乱序执行、cache 做多大、分支怎么预测，都写在这一层。」
源（PAGE 327）对微架构只有一句 `An implementation of the ISA`。**检索**：`out-of-order|branch predict` ⇒ **0 命中**。
⇒ 内容是常识、不算错，但属**我们补的举例**。建议标一句，或删掉例子只留「它是 ISA 的一种实现」。

### P2-2 图的 alt / 图内文字把第一种内存内计算窄化了

正文 L160 说第一种（`Processing near memory`）**可以放在内存芯片内部，也可以放在与内存同一封装里的逻辑裸片上**（两种放法）；
而 `figures/lecture1-intro-8.svg` 的 `desc` 写「把计算单元放进内存芯片**这一侧**」、图内可见文字写「路一：把计算单元放进内存芯片」「计算单元也在片内」，
正文 L166 的 alt 也写「把计算单元放进内存芯片」⇒ **图比正文窄了一层**（同封装逻辑裸片那条路没画出来）。
⇒ 建议：要么把图/alt 改成与正文同口径（「放进内存芯片，或放进同一封装里的逻辑裸片」），要么在正文里说明图只画了其中一种。

### P2-3 「并排放在同一页上」→ 实际是相邻两页

正文 L148：「源讲义把它们并排放在**同一页**上」。源里这四个负载出现在 **PAGE 271 与 PAGE 272 两页**（标题分别为
`Data is Key for Future Workloads` 与 `Data Overwhelms Modern Machines`，内容相同、只差后半句）。建议改成「并排放在相邻两页上」。

---

## 我核不到的（诚实记录）

> 按 `附录十四`：**「我没搜到」不等于「源材料没有」。** 下面是**没有找到依据**的主张 ⇒ **我不能说它对，也不能说它错**。

1. **15% / 47% 的语义**（=P1-1 的核心）：讲义文本层里那一页只有两个数字。**它是什么的比例、是「现在 vs 将来」还是「两个容量档」，
   我核不到。** 范围与检索词已在 P1-1 逐条列出。**⇒ 这条要判定，只能看那一页的图（PDF 里的图像/矢量图），我这里的文本层拿不到。**
   **如果 Lead 能提供该页的渲染图（或 faithful 修好后的输出），这条可以立刻定案。**
2. **L110 的两处**：「同一份讲义还展示了把内存芯片直接变成计算场所的系统，**一块板子上并列着几十颗内存芯片**」与
   「测序仪要的是把一个特定算法做到极低的能耗，**甚至做成握在手里的小设备**」。
   我在文本层里能找到的是相邻的同组页（`DRAM Chip / Main Memory / PIM-enabled Memory`，PAGE 92 附近；`1 ExaFLOPS per board`，PAGE 83），
   **但「几十颗」这个量和「握在手里」这个形态我没有在文本层里定位到**（很可能是纯图页/照片页）。
   检索词：`sequencing|genome|handheld|hand-held`（命中很多，但都不是这两句的形态）、`chips|board`（未逐一定位）。⇒ **记为「我核不到」，不判 P1/P2。**
3. **faithful 提取层**：如开头「提取层说明」所述，`pdftext3.py` 在这份 PDF 上产出乱码。**我**没有**用它的输出下任何结论**；
   但反过来说，**我也没有用它交叉验证过正文的每一条** —— 我用的是原始内嵌抽取 + text-clean。

---

## 覆盖面（附录十二）

- **已验**：矩阵乘法 7 个数字 + 5 个倍数；平台规格 7 处（自动驾驶 5 个数、TPU 3 个数、Cerebras 4 个数、1 ExaFLOPS）；
  6 段逐字引用（ISA、公理、扩展视野两句话、体系结构定义、卡拉特拉瓦、赖特）；文献归属 4 处（费曼 1959、Leiserson 2020、Dally HiPEAC 2015、RAIDR ISCA 2012）；
  页码指认 4 处（190–285 自学段、267–274、285 Takeaways、335 页）；课程结构 1 处（第 3、4 讲）；
  11 张图的 `title`/`desc`/图内文字与正文 `alt` 逐张比对（重点核了 `-1`「六层转换」与图的 7 格一致、`-11` 的 15%/47%、`-8` 的两条路）。
- **未验**：`audit_content.py` / `check_figures.py` 等机检指标（不在本次范围）；渲染量墨迹（本讲无「看起来偏了」类主张）；**faithful 提取层**（本 PDF 上不可用）；
  以及上面「我核不到的」3 条。

---

**核对人声明**：本记录只覆盖开头那个哈希的版本（`BE5E6549B44AA9AD`）。按附录五，对其它版本的结论不成立。
本记录只读正文、不修改任何 `content/` 文件；`status` 由 Lead 处理。
