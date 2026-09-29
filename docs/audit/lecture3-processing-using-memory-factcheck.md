# 事实核对 · ETH Zürich Computer Architecture（Fall 2022 · Onur Mutlu）第 4 讲 用内存来算：把计算搬到数据所在的地方

- 核对人：**非作者**（本轮由 Lead 派单的**独立核对 subagent** 执行；与作者 `eth-author` 不共享上下文 —— 作者已到上下文上限，本记录全程未询问作者、未改动 `content/`）
- 核对日期：2026-09-29
- 被核对版本：`content/04-lecture3-processing-using-memory/index.md`
  归一化 SHA256 前16 = **B8EEFB1BD97A71DB**
  （全 64 位 `B8EEFB1BD97A71DBD73EF4B42F131D5C4FB779C6CA742676699C1DEA41C255A8`，CRLF→LF 后 17599 B）
  - 10 张配图（前 16 位）：`-1 D0A2DD222E8FF6F3` · `-2 9EF541E2795175B5` · `-3 63D15ACD11F7E4A3` ·
    `-4 831A56DDD80B1F5C` · `-5 82D4096CC476F14C` · `-6 6D22DF62B18B43DB` · `-7 C48E67A8E2551BCB` ·
    `-8 0A4772EC82F1976A` · `-9 4DBA365295C041AA` · `-10 8292F9606A28FC56`
- 源材料：

| # | 文件 | 用途 |
| --- | --- | --- |
| 1 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture3-processing-using-memory.pdf.embedded.txt`（3166 行，73394 B，**255 页**） | **主要依据**：本讲义（Fall 2022 afterlecture）的原始内嵌抽取，带 `===== PAGE n =====` |
| 2 | `_sources/eth-ddca-ca/text-clean/eth-ca2022-lecture3-processing-using-memory.txt` | 交叉核对 —— **这是我必须如实说明的一条：它与 #1 逐字节相同**（`sha256(CRLF→LF)` 都是 `db2683f8607a1594…`，`a.read_bytes()==b.read_bytes()` 为 `True`）⇒ **它不构成独立交叉验证**（见「我核不到的」第 6 条） |
| 3 | 本页 10 张自绘 SVG（`content/04-lecture3-processing-using-memory/figures/`） | 核「正文对配图的位置/内容描述」是否与图一致 |
| 4 | `docs/audit/visual-review/eth-ca__lecture3-processing-using-memory-*.md`（已有 10 份） | 只用于**独立佐证图的版面几何**（第 1/3 张），不作为源材料依据 |

> **提取层说明（按派单要求写明）**：本记录**未用 faithful 输出下任何结论**。
> 派单给的实测前提是「`_sources/_audit/pdftext3.py`（faithful）在 eth-ca 的 PDF 上输出乱码，
> 检索 `Computer Architecture` / `Mutlu` / `Cerebras` / `FLOPS` 全 0 命中」。
> **我照做了这个探针，结论与派单不一致，且必须记录在案**（方法见下，全部可复现）：
>
> - 我跑的是 `python _sources/_audit/pdftext3.py <该 PDF>`，产出 `…pdf.faithful.txt`（99,445 B / 82,056 字符）。
> - 关键字命中：`Computer Architecture` **11** · `Mutlu` **33** · `Cerebras` **0** · `FLOPS` **2** · `RowClone` **51** · `Ambit` **25**；
>   抽样 `Negligible` 1 · `11.6` 3 · `62.7` 1 · `119x` 1 · `89x` 1。**前 900 字符逐字可读**（`Prof. Onur Mutlu` / `Fall 2022` 等）。
> - ⇒ **它不是「乱码」，也不是「0 命中」**。它确实有缺陷：含 **2100 个 NUL 字节**，且**每行只切一个 token**（`Take`/`advantage`/`of` 分行），
>   页标记是**按文本流**而不是按 PDF 页（266 个 `===== PAGE =====` 对 **255 页**）⇒ **不能用于页码定位**。
> - 我的两个检索器都**能**在这份 faithful 文件里命中：工具 `grep` 报 `Mutlu|Cerebras|FLOPS` **35 条命中**；`rg -c Mutlu` 报 **33**。
> - **边界声明**：我只探了这一份 PDF（第 4 讲的讲义），**没有**重跑第 1/2 讲或其它 eth-ca PDF；派单者的实测可能来自别的文件或别的脚本版本。
>   我**没有**把 faithful 用于任何结论 —— 本记录每一条都引 `embedded.txt` 的 `===== PAGE n =====`。
> - **给 Lead 的提醒**：如果别的讲次以「faithful 全 0 命中」为理由绕过交叉验证，**那个理由在本 PDF 上不成立**，
>   建议用可复现的判据（含 NUL / 页标记不可靠）替代。

---

## 结论

**P0（事实错误）：2 条 ｜ P1（易误解/依据不足）：3 条 ｜ P2（措辞）：3 条**

- **2 条 P0 都是同一类：正文对「本页自绘配图」的位置描述与图本身不符**（图是竖向三行 / 上中下，正文说成左右）。
  两条都由**图的 SVG 几何**与**独立视觉复核报告的版面描述**双向佐证，读者按图即可判定为假。
  **面向源讲义的事实错误：0 条** —— 本讲数字密度很高（41% / 115 / 62.7% / 11.6× / 74× / 1.9× / 3.2× / 6.0× / 41.5× / 0.01% /
  32× / 35× / 88× / 5.8× / 257× / 31× / 21× / 2.1× / 119× / 89×），**我逐个回源，全部命中**（见「已核对通过」）。
- 3 条 P1：①「归成**三股**推力」（源讲义是**两股**：器件推力 + 应用拉力；第三方方向是**结果**）；
  ②「RowClone 的投稿**相对顺利**」（源讲义逐字写着 `Rejected from ISCA 2013 conference`，且给了 5 页 RowClone 评审/多次投稿）；
  ③ 清零的容量代价同页并列写了 **0.5%** 与 **0.2%**（源讲义自己在两页给两个数，页面原样并列、未说明）。

---

## P0 · 事实错误

| # | 位置 | 正文说 | 源材料说 | 依据（逐字引用 + 页码） |
| --- | --- | --- | --- | --- |
| P0-1 | L30（配图 `…-1.svg` 的正下方那句） | 「**左边**是唯一被认真优化的部件，**右边**是占掉大部分系统的存与搬。这一讲要动的就是**右边**。」 | 该图是**竖向三行**：**最上**一格＝「被认真优化的部分：计算」；**中间**一排**三个并列框**＝「存储／通信与互连／数据搬运」；**最下**一格＝「结果：能量效率低、性能低、系统复杂」。⇒ 没有左右关系，且「存与搬」是**三格**不是一格 | 图源码（逐字）：`<rect x="23" y="64" width="596" …>` + `被认真优化的部分：计算`；`<rect x="23" y="134" width="180">存储/基本是笨的`、`<rect x="231" y="134" …>通信与互连/又长又费电`、`<rect x="439" y="134" …>数据搬运/量大且必须出芯片`；`<rect x="23" y="216" …>结果：能量效率低、性能低、系统复杂`。三点 y 值 64 / 134 / 216 全为竖向堆叠。<br>独立佐证：`docs/audit/visual-review/eth-ca__lecture3-processing-using-memory-1.md` 版面段逐字：「第一行一个通栏黄色圆角框；第二行三个并列蓝色圆角框（左、中、右）；第三行一个通栏粉色圆角框」「整体走向**自上而下**」 |
| P0-2 | L62（配图 `…-3.svg` 的正下方那句） | 「图上**下面两格是原因**，**上面那格是结果**：设计者被夹在中间，必须同时回答器件与应用的诉求。」 | 该图的**结果在中间**、两个**原因分处上下**：最上格＝「应用与系统：数据密集、访存与能量都是瓶颈」（**原因**，箭头向下指中间）；中间格＝「设计被挤在中间：接口、编程模型、算法都要跟着改」（**结果**，被上下两个箭头夹击）；最下格＝「器件与电路：缩放变难，需要更多智能来管理」（**原因**，箭头向上指中间）。⇒ 原因**只有两格且分处上下**，「结果」在**中间**，不是「上面那格」 | 图源码（逐字）：`<rect x="23" y="64" …>应用与系统：…`；`<rect x="23" y="134" …>设计被挤在中间：…`；`<rect x="23" y="204" …>器件与电路：…`；`<path d="M283,111 L283,123" … marker-end="url(#arrow)"/>`（上→中）与 `<path d="M283,199 L283,187" … marker-end="url(#arrow)"/>`（下→中）。<br>独立佐证：`…-3.md` 版面段逐字：「**上、中、下**三个等宽圆角矩形框纵向等距排列」「两个箭头都指向中间框，构成上下夹击的走向」 |

> **分类说明（请 Lead 决定是否维持 P0）**：这两条不是「引错源讲义」，而是「正文对本页自绘配图的描述与图不符」。
> 我仍按 P0 记，理由是：它们**可被读者按图直接判定为假**，且**读起来完全顺**（正是「讲错了但讲得很顺」那一类），
> 放在 P1 容易被当成措辞问题放过去。若本项目的 P0 只收「面向源讲义」的错误，这两条请降为 P1 —— 但**必须修**（改正文或改图，二者取一）。

---

## P1 · 易误解或依据不足

### P1-1 L50 「源讲义把原因归成**三股推力**」—— 源讲义是**两股**，第三项是**结果**

正文 L50：「那为什么过去做不起来、现在又能做？源讲义把原因归成**三股推力**，并把它们画成「设计被挤在中间」：**上面是应用与系统的拉力，下面是器件与电路的推力**。」
- **源讲义只给两股**（逐字）：
  - `===== PAGE 40 =====` / `===== PAGE 63 =====`：**`The Push from Circuits and Devices`**（器件/电路往上推）；
  - `===== PAGE 62 =====`：`◼ Push from Technology` + `◼ Pull from Systems and Applications`（只为**两股**：推力、拉力）；
  - `===== PAGE 2 =====`（本讲的 sub-agenda）：`Bottom Up: Push from Circuits and Devices` / `Top Down: Pull from Systems and Applications` —— **两项**。
- 第三项在源里是**结果**不是力：`===== PAGE 37 =====` 与 `===== PAGE 39 =====` 逐字 `◼ Designs are squeezed in the middle`（在 `Huge problems with Memory Technology` 与 `Huge demand from Applications & Systems` 之后）。
- **正文自己在同一句里就只列了两股**（「应用与系统的拉力」「器件与电路的推力」）⇒ 自相矛盾。
- 独立佐证：`docs/audit/visual-review/eth-ca__lecture3-processing-using-memory-3.md` 已独立判为缺陷并逐字写：
  「标题标签「三股推力把设计挤在中间」中的「三股」与图示不符…**只有两个**（上层「应用与系统」、下层「器件与电路」）…**第三股推力在图中没有任何对应的方框、标签或箭头**」，判定「有错误」。
- **修在**：把「三股推力」改成「两股力」/「一推一拉」，或明确写「源讲义额外点出第三个现象：设计被挤在中间（这是结果，不是第三股力）」。图 `…-3.svg` 的 `<title>` 同步改。

### P1-2 L154 「RowClone 的投稿**相对顺利**」—— 源讲义逐字写着它也被拒过，且给了 5 页评审/多次投稿

正文 L154：「这一讲的最后一段偏题，讲的是上面这些工作是怎么被发表的，源讲义花了三十多页。**RowClone 的投稿相对顺利。**但 Ambit 这条线不顺利……」
- **源里有的**（逐字）：`===== PAGE 199 =====`：`Initially, it was dismissed by many reviewers ❑ Rejected from 4 conferences!`（这句是 **Ambit**）。`===== PAGE 200–203 =====`：`ISCA 2016: Rejected` / `MICRO 2016: Rejected` / `HPCA 2017: Rejected` / `ISCA 2017: Rejected` ✅。`===== PAGE 215 =====`：`MICRO 2017: Accepted` ✅。
- **源里同时有的、与「顺利」相抵的**（逐字）：`===== PAGE 192 =====`（标题 `RowClone: Historical Perspective`）：
  `◼ Initially, it was dismissed by some reviewers` / `❑ **Rejected from ISCA 2013 conference**`。
- 更有：`PAGE 193` `One Review (ISCA 2013 Submission)` · `PAGE 194` `Another Review and Rebuttal` · `PAGE 195` `ISCA 2013 Submission` ·
  `PAGE 196` `Yet Later… in ISCA 2015…` · `PAGE 197` `MICRO 2013 Submission` ⇒ RowClone 这条线**本身就有多次投稿与至少一次明确被拒**（7 页）。
- ⇒ 「相对顺利」**只在「被拒次数」的对比上成立**（RowClone 至少 1 次 vs Ambit 4 次），但读者会读成「没被拒过」。
- **修在**：改成「RowClone 被拒过（ISCA 2013），但只绕了一圈就在 MICRO 2013 落地；Ambit 这条线走了四次会议才在 MICRO 2017 被接收」。
  （我**没有**核到 `PAGE 196` 那条 `ISCA 2015` 是「又被拒」还是别的 —— 该页文字层只有标题，见「我核不到的」。）

### P1-3 L92 清零的容量代价在相邻两句里给了 **0.5%** 与 **0.2%**，且未说明是两个不同的数

正文 L92：「做法是在每个子阵列里**预留一行，让它恒为零**（**代价是 0.5% 的容量**），清零时把这一行复制到目标行即可。源讲义给的数字是延迟降低 6.0 倍、DRAM 能量降低 41.5 倍，**容量损失 0.2%**。」
- 两个数**都能回源**，但**在源里是两页**：
  - `===== PAGE 80 =====` 逐字：`RowClone: Fast Row Initialization` / `0 0 0 0 0 0 0 0 0 0 0 0` / `Fix a row at Zero` / `(**0.5% loss in capacity**)`
  - `===== PAGE 81 =====` 逐字：`◼ Zero initialization (most common)` / `❑ Reserve a row in each subarray (always zero)` / `❑ Copy data from reserved row (FPM mode)` / `❑ 6.0X lower latency, 41.5X lower DRAM energy` / `❑ **0.2% loss in capacity**`
- ⇒ **矛盾源自源讲义本身**（同一个「每个子阵列预留一行」的方案，两页写 0.5% 与 0.2%）。页面**忠实地把两个数并列**，但读者会以为「同一个机制有两个互相打架的容量代价」。
- **修在**：写明「源讲义两页给的容量代价不一致（p80 写 0.5%、p81 写 0.2%），此处照录」，或只用一个并加脚注。**不要**替源讲义把两个数调和成同一个。

---

## P2 · 措辞

### P2-1 L68 「它不需要在内存里额外放计算单元」—— 源讲义没有这句，属推论

正文 L68：「第一条是 processing using memory：……**它不需要在内存里额外放计算单元**，而是利用内存内部已有的连接……和它的模拟特性……」
- **源里最近的逐字**（`===== PAGE 68 =====`）：`Approach 1: Processing Using Memory` / `◼ Take advantage of operational principles of memory to perform bulk data movement and computation in memory` / `❑ Can exploit internal connectivity to move data` / `❑ Can exploit analog computation capability`；
  另一页 `===== PAGE 116 =====`：`◼ DRAM has great capability to perform bulk data movement and computation internally **with small changes**`。
- **检索词与范围**：`without adding|no additional|extra hardware|compute units|logic units|not need`（工具 `grep`，范围 = 本讲 `embedded.txt` 全文 3166 行）⇒ **0 命中**；换写法 `universal|Boolean function|NAND|NOR gate` ⇒ **0 命中**。
- ⇒ 「不需要额外放计算单元」是**合理推论**，但源讲义只说「**改动很小**」。「改动很小」与「不放任何东西」不是一回事。**建议**改成「它对内存的改动很小（源讲义的说法是 with small changes）」或注明这条是概括。

### P2-2 L132 图注（alt 文本）说「上面是微操作生成与执行」，该图是**横向一行四格**，没有上下关系

正文 L132：`![SIMDRAM 的底座是垂直数据摆放与多数函数，**上面**是微操作生成与执行](figures/lecture3-processing-using-memory-8.svg)`
- 图源码：四格 y 值**全为 64**（同一行），x 依次 23 / 175 / 327 / 479：`垂直摆数据 一列放一个比特` → `多数函数 配上取反` → `生成并优化 微操作序列` → `硬件执行 按自定义时序`，箭头三支全为横向（`M152,91 L164,91` 等）。
- ⇒ 「底座 / 上面」是**隐喻**，图上并无上下分层。**建议** alt 改成「从左到右：先垂直摆数据与多数函数，再生成/优化微操作，最后硬件执行」。

### P2-3 L52 「**演讲者在这里的措辞**是「内存缩放问题是真的」」—— 这是**幻灯片标题**，不是口头措辞

正文 L52：「……也就是说 [[term:memory-controller]] 要变得更聪明，而不是继续靠缩小尺寸。**演讲者在这里的措辞是「内存缩放问题是真的」**。」
- 源逐字（`===== PAGE 41 =====` 的**标题行**）：`Memory Scaling Issues Are Real`（该页正文只有那篇 IMW 2013 论文的引用信息）。
- ⇒ 这是**挂在幻灯片上的标题**，不是演讲者的口头说法。按方法④（归属类要专查）：把「演讲者的措辞」改成「源讲义这一节的标题就是「内存缩放问题是真的」」更准确。

---

## 已核对通过（逐条附 PAGE /逐字引用）

| 正文 | 源（PAGE · 逐字引用） | 结果 |
| --- | --- | --- |
| L13 讲义 **255 页** | 最后一个页标记 = `===== PAGE 255 =====`（内容为 `Computer Architecture / Lecture 3: Processing using Memory`） | ✅ 页数对 |
| L24「引用 **1946 年**那份关于电子计算机逻辑设计的报告，说一个计算系统只有**三个部件：计算、通信、存储**」 | `PAGE 12`（与 13 同页内容）：`A Computing System` / `◼ Three key components` / `◼ Computation` / `◼ Communication` / `◼ Storage/memory` / `Burks, Goldstein, von Neumann, "Preliminary discussion of the logical design of an electronic computing instrument," **1946**.` | ✅ **逐字对应**（年份、三部件、报告名） |
| L24「**只有计算被认真优化过**，存储单元基本是「笨」的，除了处理器芯片里那点缓存」 | `PAGE 14`：`Today's Computing Systems` / `◼ Are overwhelmingly processor centric` / `◼ Processor is heavily optimized and is considered the master` / `◼ Data storage units are **dumb** and are largely unoptimized (except for some that are on the processor die)` | ✅ 逐字对应 |
| L26「处理只在一个地方发生，其它部分都在存数据和搬数据……三条后果都是负面词：**能量效率低、性能低、系统复杂**」 | `PAGE 22`：`Perils of Processor-Centric Design` / `◼ Grossly-imbalanced systems` / `❑ Processing done only in one place` / `❑ Everything else just stores and moves data: data moves a lot` / `→ **Energy inefficient** / → **Low performance** / → **Complex**` | ✅ **三条后果逐字对应** |
| L36「一次内存访问消耗的能量，约等于一次复杂加法的 **100 到 1000 倍**」 | `PAGE 25`：`Dally, HiPEAC 2015` / `A memory access consumes ~100-1000X the energy of a complex addition` | ✅ 逐字对应（含引用） |
| L38「移动端网页浏览场景里 data-movement 占掉系统能量的 **41%**；搬运一次数据的能量约等于 **115** 次加法操作」 | `PAGE 26`：`◼ Data movement is a major system energy bottleneck` / `❑ Comprises **41% of mobile system energy during web browsing** [2]` / `❑ Costs **~115 times** as much energy as an **ADD** operation [1, 2]`；两条引用逐字为 `[1]: Reducing data Movement Energy via Online Data Clustering and Encoding (MICRO'16)`、`[2]: Quantifying the energy cost of data movement for emerging smart phone workloads on mobile platforms (IISWC'14)` | ✅ **两个数都对，方向也对**（是「搬运 vs 一次加法」，不是反过来） |
| L38「对 Google 消费级设备负载的实测：系统总能量的 **62.7%** 花在数据搬运上」 | `PAGE 27`：`Energy Waste in Mobile Devices` / `Boroumand, Ghose, Kim, Ausavarungnirun, Shiu, Thakur, Kim, Kuusela, Knies, Ranganathan, **and Onur Mutlu**` / `"**Google Workloads for Consumer Devices: Mitigating Data Movement Bottlenecks**" ASPLOS … March 2018` / `**62.7% of the total system energy is spent on data movement**` | ✅ 逐字对应 |
| L40「源讲义在**第 4 页**写得很直接：高延迟与高能耗来自那些又长又费电的互连、费电的电气接口，以及被搬运的大量数据本身」 | `PAGE 4`：`Observation and Opportunity` / `◼ High latency and high energy caused by data movement` / `❑ **Long, energy-hungry interconnects**` / `❑ **Energy-hungry electrical interfaces**` / `❑ **Movement of large amounts of data**` | ✅ **页码 + 三条原因全对** |
| L44 / L166「源讲义给这一讲定的目标是「**用最小的数据搬运完成计算**」」 | `PAGE 29`：`We Need A Paradigm Shift To …` / `◼ **Enable computation with minimal data movement**` | ✅ 逐字对应 |
| L48「Kautz 在 **1969** 年发表过《**单元式逻辑内存阵列**》，Stone 在 **1970** 年发表过《**逻辑内存计算机**》」 | `PAGE 35`：`◼ Kautz, "**Cellular Logic-in-Memory Arrays**", **IEEE TC 1969**.`；`PAGE 36`：`◼ Stone, "**A Logic-in-Memory Computer**," **IEEE TC 1970**.` | ✅ **两位作者、两个标题、两个年份全对** |
| L50「把它们画成「**设计被挤在中间**」」 | `PAGE 37` / `PAGE 39`：`◼ **Designs are squeezed in the middle**` | ✅ 逐字对应 |
| L52「[[term:RowHammer]] 这类问题说明器件本身需要更多「智能」来管理」 | `PAGE 37` / `PAGE 39`：`❑ Memory technology scaling is not going well (**e.g., RowHammer**)` / `❑ **Many scaling issues demand intelligence in memory**` | ✅ 逐字对应 |
| L54「数据密集型应用越来越多，访存是瓶颈，能量与功耗也是瓶颈，而且这几件事必须同时解决」 | `PAGE 39`：`◼ Huge demand from Applications & Systems` / `❑ Data access bottleneck` / `❑ Energy & power bottlenecks` / `❑ **Need all at the same time: performance, energy, sustainability**` | ✅ 逐字对应 |
| L58「**UPMEM 2019** 把处理器放进 DRAM 的模块：**DDR4** 内存条形态，每条 **8 GB** 配 **128 个 DPU**、共 **16 颗 PIM 芯片**，用的是标准 **2x 纳米** DRAM 工艺」 | `PAGE 43`：`UPMEM Processing-in-DRAM Engine (2019)` / `◼ Includes standard DIMM modules, with a large number of DPU processors combined with DRAM chips` / `❑ **DDR4 R-DIMM modules**` / `◼ **8GB+128 DPUs (16 PIM chips)**` / `◼ **Standard 2x-nm DRAM process**` | ✅ **五个要素全对** |
| L58「一台机器插 **20 条**就是 **2560 个 DPU**」 | `PAGE 45` 标题逐字：`**2,560-DPU** Processing-in-Memory System`；每条的 DPU 数 = `128`（`PAGE 43` 逐字 `128 DPUs`）⇒ 2560 ÷ 128 = **20 条**。同页图内的分块标注也是 `x10` 与 `x2`（10×2 = 20 个 PIM-enabled memory 模块） | ✅ **2560 与 128 都逐字核到；「20 条」是可复核的除法（2560/128=20）**，且被图里的 `x10 × x2` 独立支持 |
| L58「**三星 2021** 给出把功能放进内存的 DRAM **与 HBM-PIM**」 | `PAGE 48–52`：`**Samsung Function-in-Memory DRAM (2021)**`；`PAGE 53`：`**Lecture on FIMDRAM/HBM-PIM**`；`PAGE 48` 附图出处逐字含 `high-bandwidth memory with AI processing power` | ✅ 年份、FIMDRAM 与 HBM-PIM 都对 |
| L58「同年还有内存条形态的 **AxDIMM**」 | `PAGE 54`：`**Samsung AxDIMM (2021)**` / `◼ **DIMM-based PIM**` | ✅ 逐字对应 |
| L58「**SK 海力士 2022** 的 **AiM** 基于 **GDDR6**」 | `PAGE 56`：`◼ 4 Gb **AiM** die with 16 processing units (PUs)`；`PAGE 57`：`**SK Hynix AiM: System Organization (2022)**` / `◼ **GDDR6-based** AiM architecture` | ✅ 逐字对应 |
| L58「**阿里巴巴 2022** 的 **HB-PNM** 把逻辑裸片与 DRAM 裸片**三维堆叠**」 | `PAGE 59`：`**Alibaba HB-PNM: Overall Architecture (2022)**` / `◼ **3D-stacked logic die and DRAM die** vertically bonded by hybrid bonding (HB)` | ✅ 逐字对应 |
| L58「源讲义也提到还有大量实验芯片与创业公司在做」 | `PAGE 38`：`Processing-in-Memory Landscape Today` / `[UPMEM 2019] [Samsung 2021] [SK Hynix 2022] [Samsung 2021] [Alibaba 2022]` / `**And, many other experimental chips and startups**` | ✅ 逐字对应（**五个年份标签与正文一一对上**） |
| L68 第一条路的定义（利用内存自身的运行原理做批量搬运与计算；利用内部连接搬数据 + 模拟特性） | `PAGE 68`：`◼ Take advantage of operational principles of memory to perform bulk data movement and computation in memory` / `❑ Can exploit internal connectivity to move data` / `❑ Can exploit **analog computation capability**` | ✅ 逐字对应（唯一缺口见 **P2-1**） |
| L76「源讲义列出的代表工作是 **RowClone**、内存内的**按位与/或**、以及 **SIMDRAM** 框架」 | `PAGE 68`：`Examples: **RowClone**, **In-DRAM AND/OR**, Gather/Scatter DRAM`；`PAGE 106`/`PAGE 134`：`**SIMDRAM Framework for in-DRAM Computing**` | ✅ 三个名字都对（源另列 Gather/Scatter DRAM，正文未提 —— 不算错，正文说的是「代表工作」） |
| L82「现在的系统怎么复制一大块数据……数据要出内存、进 cache、再回到内存」 | `PAGE 72`：`Today's Systems: Bulk Data Copy` / `1) High latency 2) High bandwidth utilization 3) **Cache pollution** 4) Unwanted data movement` | ✅ 对应（cache 污染这条对上） |
| L84「RowClone 的做法就是利用这一点：**连续激活两行**」 | `PAGE 74`：`RowClone: In-DRAM Row Copy` / `**Idea: Two consecutive ACTivates**` | ✅ 逐字对应 |
| L84 读＝激活一行把整行送进行缓冲、写＝把行缓冲写回被激活的行 | `PAGE 76`：`1. Activate src row (copy data from src to row buffer)` / `2. Activate dst row (disconnect src from row buffer, connect dst – copy data from row buffer to dst)` | ✅ 逐字对应 |
| L86「源讲义给出的代价是「**可忽略的硬件成本**」」 | `PAGE 74`：`**Negligible HW cost**` | ✅ 逐字对应 |
| L86「让**感应放大器**充当一次「中转站」」 | `PAGE 75`：`**Sense Amplifier** (Row Buffer)` / `Amplify the difference` | ✅ 逐字对应 |
| L90「同一子阵列内的复制收益最大：latency 降低 **11.6 倍**、能量降低 **74 倍**」 | `PAGE 74`：`**11.6X latency reduction, 74X energy reduction**`；`PAGE 79`/`PAGE 82`：`Intra-Subarray` 一组 = `11.6x` 与 `74.4x` | ✅ 逐字对应 |
| L90「跨内存组的复制要经过**共享内部总线**，收益降到 **1.9 倍**与 **3.2 倍**」 | `PAGE 77`：`RowClone: **Inter-Bank**` / `Memory Channel` / `Chip I/O` / `Bank` / `**Shared internal bus**` / `**1.9X latency reduction, 3.2X energy reduction**`；`PAGE 82` 的 `Inter-Bank` 一组同为 `1.9x` / `3.2x` | ✅ 逐字对应 |
| L92 清零：**预留一行恒为零**、延迟降 **6.0 倍**、DRAM 能量降 **41.5 倍** | `PAGE 80`：`Fix a row at Zero`；`PAGE 81`：`◼ Zero initialization (**most common**)` / `❑ **Reserve a row in each subarray (always zero)**` / `❑ Copy data from reserved row (FPM mode)` / `❑ **6.0X lower latency, 41.5X lower DRAM energy**` | ✅ **逐字对应**（容量那一项见 **P1-3**） |
| L92「整套机制对芯片面积的影响只有 **0.01%**」 | `PAGE 78`：`Generalized RowClone **0.01% area cost**`；`PAGE 82`：`Very low cost: **0.01% increase in die area**` | ✅ 两处互证 |
| L98「最大的限制是映射：只有数据落在**同一个子阵列**里收益才是 11.6 倍与 74 倍；**同内存组的不同子阵列**里收益几乎没有；**跨内存通道**则完全帮不上」 | `PAGE 92`：`◼ **Requires data to be mapped in the same subarray** to deliver the largest benefits` / `❑ Helps less if data movement is not within a subarray` / `❑ **Does not help if data movement is across DRAM channels**` / `◼ **Inter-subarray copy is very inefficient**`；数量级佐证 `PAGE 82`：`Inter-Subarray` 的延迟降幅 = **`1.0x`**（能量 `1.5x`） | ✅ **方向/范围逐条对上**（「几乎没有」有 1.0× 这个硬数支撑） |
| L100「从应用到电路都要改，缓存一致性、数据复用都会带来真实开销」 | `PAGE 92`：`◼ Causes many changes in the system stack` / `❑ **End-to-end design spans applications to circuits**` / `◼ **Cache coherence and data reuse cause real overheads**` | ✅ 逐字对应 |
| L100「这篇工作的评估**只有模拟**，而且**没有考虑多芯片系统**」 | `PAGE 92`：`◼ **Evaluation is done solely in simulation**` / `◼ **Evaluation does not consider multi-chip systems**` | ✅ 逐字对应 |
| L102「LISA 用**隔离晶体管**把子阵列连起来，代价是 **0.8%** 的芯片面积」 | `PAGE 98`：`• Low-cost Inter-linked subarrays (LISA)` / `– Wide datapath via **isolation transistors: 0.8% DRAM chip area**` | ✅ 逐字对应 |
| L102「跨子阵列复制的延迟从 **1.363 毫秒**降到 **0.148 毫秒**（**9.2 倍**），应用加速 **66%**、DRAM 能量降 **55%**」 | `PAGE 98`：`• Fast bulk data copy: **Copy latency 1.363ms→0.148ms (9.2x)**` / `→ **66% speedup, -55% DRAM energy**` | ✅ **五个数全对**（1.363→0.148 与 9.2× 自洽：1.363/0.148 ≈ 9.2） |
| L102「同一套连接还能做「**内存内缓存**」与**快速预充电**」 | `PAGE 98`：`• **In-DRAM caching**: Hot data access latency 48.7ns→21.5ns (2.2x) → 5% speedup` / `• **Fast precharge**: Precharge latency 13.1ns→5.0ns (2.6x) → 8% speedup` | ✅ 逐字对应 |
| L102「**FIGARO** 把数据搬动的粒度做得更细，**NoM** 则专门优化**跨内存组**的搬运」 | `PAGE 95`：`◼ Can we enable data movement at **smaller granularities** within a bank? ❑ Yes, see **FIGARO** [Wang et al., MICRO 2020]` / `◼ Can we do better **inter-bank copy**? ❑ Yes, see **Network-on-Memory** [CAL 2020]` | ✅ **两件工作的分工方向都对**（FIGARO=粒度更细，NoM=跨组/跨 bank） |
| L112「同时激活三行，位线上的电压就等于这三行的「**多数函数**」，公式是 **AB 加 BC 加 AC**，也就是**三取二为真**」 | `PAGE 119`：`In-DRAM AND/OR: **Triple Row Activation**` / `A B C` / `Final State` / `**AB + BC + AC**`；同页数值行含 `½VDD`、`VDD` | ✅ 逐字对应（AB+BC+AC 正是三取二多数函数） |
| L114 BULKAND 的**五步**：A 复制到指定行、B 复制到第二条、全零行复制到第三条、同时激活三行、结果复制到 C 行 | `PAGE 120`：`◼ **BULKAND A, B → C**` / `1. RowClone A into D1` / `2. RowClone B into D2` / `3. RowClone **R0** into D3` / `4. ACTIVATE D1,D2,D3` / `5. RowClone Result into C`（且 `R0 – **reserved zero row**`） | ✅ **五步逐字对应**，含「用全零行使多数退化成与」这一点的源依据（R0 = reserved zero row） |
| L116「按位非不能用多数函数直接得到，办法是改单元结构：用**「双接触」单元**把**取反后的值喂进感应放大器**，再写进一条专用行」 | `PAGE 122`：`In-DRAM NOT: **Dual Contact Cell**` / `Idea: Feed the **negated value** in the **sense amplifier** into a **special row**` | ✅ 逐字对应 |
| L120「相对 DDR3 基准，性能提升 **32 倍**、能耗降低 **35 倍**」 | `PAGE 126`：`**Ambit vs. DDR3: Performance and Energy**` / `**32X 35X**` | ✅ 逐字对应 |
| L120「另一页的概括是 **30 到 60 倍**的性能与能效改善」 | `PAGE 118`：`We can support in-DRAM COPY, ZERO, AND, OR, NOT, MAJ` / `At low cost` / `Using inherent analog computation capability of DRAM` / `**30-60X performance and energy improvement**` | ✅ 逐字对应 |
| L120「应用是**位图索引**与 **BitWeaving** 这类按位扫描为主的数据库操作」 | `PAGE 128`：`Example Data Structure: **Bitmap Index**`；`PAGE 127`：`[1] Li and Patel, **BitWeaving**, SIGMOD 2013`；`PAGE 130`：`Performance: **BitWeaving** on Ambit` | ✅ 两个名字都对（这两个工作按位扫描，与「批量按位的天然用户」一致） |
| L126「SIMDRAM 是**端到端**框架，含**编程接口、ISA 与硬件支持**三部分；目标是**任意运算**都能做、且**对 DRAM 结构改动尽可能小**」 | `PAGE 135`：`• SIMDRAM: An **end-to-end** processing-using-DRAM framework that provides the **programming interface, the ISA, and the hardware support** for:` / `- Efficiently computing complex operations in DRAM` / `- Providing the ability to implement **arbitrary operations** as required` / `- Using an in-DRAM massively-parallel SIMD substrate that **requires minimal changes to DRAM architecture**` | ✅ **逐字对应（三部分 + 两个目标全对上）** |
| L128 第一是**垂直摆放**：一个多位数据的各比特放不同行，一次激活＝同时处理很多个数，**移位变得免费** | `PAGE 136`：`(1) **Vertical data layout**` / `Pros compared to the conventional horizontal layout:` / `• **Implicit shift operation**` / `• **Massive parallelism**` | ✅ 逐字对应 |
| L128 第二是**多数函数 + 取反**作积木，可以搭出**任何布尔函数** | `PAGE 136`：`(2) **Majority-based computation**` / `Cout= AB + ACin + BCin`；`PAGE 140`：`Step 1 generates an optimized **MAJ/NOT-implementation of the desired operation**`（其引文为 `L. Amarù et al, "**Majority-Inverter Graph**…" DAC 2014` —— MAJ+INV 是函数完备表示） | ✅ 逐字对应（「任何布尔函数」有 MAJ/NOT 实现 + Majority-Inverter Graph 支撑） |
| L130「把一条复杂指令翻译成一串微操作（**µProgram**）：先决定操作数放在**哪些行**，再**生成并优化**，最后**交给硬件执行**」 | `PAGE 142`：`• **µProgram**: A series of microarchitectural operations (e.g., ACT/PRE)…` / `**Task 1: Allocate DRAM rows to the operands**` / `**Task 2: Generate µProgram**`；`PAGE 144`/`145`：`Task 1: Allocating DRAM Rows to Operands`；`PAGE 149–152`：`**Task 2: Optimize the µProgram**`（`Coalesce row copies` / `Merge MAJ + row copy`）；`PAGE 156`：`**Step 3: µProgram Execution**` | ✅ **三步逐字对应** |
| L130「框架里还有一个**转置单元**，放在**末级缓存**里」 | `PAGE 160`：`**Transposition Unit**` / `**Last–Level Cache**` / `Vertical → Horizontal Transpose` / `Horizontal → Vertical Transpose`；`PAGE 161`：`Low impact on the throughput of SIMDRAM operations` / `Low area cost (0.06 mm2 in 22nm tech. node)` | ✅ 逐字对应（末级缓存 + 双向转置） |
| L134「**16 种**复杂运算上，吞吐是 **CPU 的 88 倍**、**高端 GPU 的 5.8 倍**；能效是 **CPU 的 257 倍、GPU 的 31 倍**；**7 个**真实应用上性能是 **CPU 的 21 倍、GPU 的 2.1 倍**」 | `PAGE 168`：`• **88× and 5.8× the throughput of a CPU and a high-end GPU**, respectively, over **16 operations**` / `• **257× and 31× the energy efficiency of a CPU and a high-end GPU**, respectively, over 16 operations` / `• **21× and 2.1× the performance of a CPU and a high-end GPU, over seven real-world applications**`；图页互证 `PAGE 165`（`88.0` `5.8`）、`PAGE 166`（`257` `31`）、`PAGE 167`（`21.0` `2.1`） | ✅ **六个倍数 + 16 + 7 全部逐字核到，并与三张图的柱值互证** |
| L134「**16 种**复杂运算」「**7 个**真实应用」的定义 | `PAGE 164`：`• **16 complex in-DRAM operations**: Absolute / Addition-Subtraction / BitCount / Equality… / **7 real-world applications**: BitWeaving, TPH-H, kNN, LeNET, VGG-13/VGG-16, Brightness`；`PAGE 163` 基线逐字为 `A multi-core CPU (**Intel Skylake**)` / `A high-end GPU (**NVidia Titan V**)` / `Ambit` | ✅ 数值与「CPU/GPU 基线」的定义都对 |
| L140 ComputeDRAM 思路是「**违反时序参数**」；位线电压会停在 **VDD/2** 附近；源讲义的说法是「**用违反时序参数的办法去模仿 RowClone**」 | `PAGE 108`/`PAGE 177`/`PAGE 225`：`RowClone in Off-the-Shelf DRAM Chips` / `◼ **Idea: Violate DRAM timing parameters to mimic RowClone**`；`PAGE 227`：`Row Copy in ComputeDRAM` / `Bitline is above **VDD/2** when R2 is activated.`；`PAGE 228`：`ACT(R2) will activate R3 and R2` | ✅ 逐字对应（「近似做出来」有 `ACT(R2) will activate R3 and R2` 支撑） |
| L142 PiDRAM 工作流五步：**系统调用** → **操作库** → 写**内存映射寄存器** → **PIM 操作控制器**执行 → **调度器**仲裁并按**自定义时序**发 DRAM 命令 | `PAGE 236`：`1- User application interfaces with the OS via **system calls**` / `2- OS uses **PuM Operations Library (pumolib)** to convey operation related information to the hardware using` / `3- **STORE** instructions that target the **memory mapped registers of the PuM Operations Controller (POC)**` / `4- **POC** oversees the execution of a PuM operation (e.g., RowClone, bulk bitwise operations)` / `5- **Scheduler** arbitrates between regular (load, store) and PuM operations and issues DRAM commands with **custom timings**` | ✅ **五步逐字对应** |
| L142「目标是把这件事做成一个**可复现的端到端平台**，而且**开源**」 | `PAGE 234`：`PiDRAM` / `Goal: Develop a flexible platform to explore **end-to-end implementations** of PuM techniques`；`PAGE 239`：`**PiDRAM is Open Source**` / `https://github.com/CMU-SAFARI/PiDRAM` | ✅ 逐字对应 |
| L142「内存内的**复制与初始化**把吞吐分别提高了 **119 倍**与 **89 倍**」 | `PAGE 238`：`Microbenchmark Copy/Initialization Throughput` / `**In-DRAM Copy and Initialization improve throughput by 119x and 89x**` | ✅ **两个数 + 它们各自配的对象全对**（copy→119、initialization→89） |
| L144「另一条路：在**相变存储器（PCM）**上做类似的事，相关工作叫 **Pinatubo**」 | `PAGE 112` / `PAGE 181` / `PAGE 242`：`**Pinatubo: RowClone and Bitwise Ops in PCM**`；`PAGE 107`：`◼ Can similar ideas be used in other types of memories? **Phase Change Memory**? … ❑ Yes, see the **Pinatubo** paper [DAC 2016]` | ✅ 逐字对应 |
| L152「这一讲的最后一段偏题……源讲义**花了三十多页**」 | 该段起于 `===== PAGE 189 =====`：`**Historical Perspective & A Detour on the Review Process**`，止于 `===== PAGE 224 =====`（其后 p225 已回到 ComputeDRAM）⇒ 189→224 = **36 页** | ✅ 与「三十多页」相符 |
| L154「Ambit 先在 **2016 年的 ISCA、2016 年的 MICRO、2017 年的 HPCA、2017 年的 ISCA** 上连续被拒」 | `PAGE 200` `**ISCA 2016: Rejected**` / `PAGE 201` `**MICRO 2016: Rejected**` / `PAGE 202` `**HPCA 2017: Rejected**` / `PAGE 203` `**ISCA 2017: Rejected**`；`PAGE 199`：`Initially, it was dismissed by many reviewers` / `❑ **Rejected from 4 conferences!**` | ✅ **四次会议与顺序全对** |
| L154「其中一份评审的意见是「**这永远不会被实现**」」 | `PAGE 206` 与 `PAGE 208` 的标题逐字：`**… This Will Never Get Implemented**`（两页各一次，上下文分别为 `Review from ISCA 2016` / `Another Review from ISCA 2016`） | ✅ 逐字对应 |
| L154「它最终在 **2017 年的 MICRO** 上被接收」 | `PAGE 215`：`**MICRO 2017: Accepted**` | ✅ 逐字对应 |
| L156「引用 **Raj Jain** 的《**The Art of Computer Systems Performance Analysis**》」 | `PAGE 216` / `217` / `218`：`Aside: A Recommended Book` / `**Raj Jain, "The Art of Computer Systems Performance Analysis," Wiley, 1991.**` | ✅ 逐字对应（作者 + 书名） |
| L156「给**评审者**与**社区**各提了几条建议，核心是两点：**评审要公平，而且要知道自己并非什么都懂**」 | `PAGE 219`：`**Suggestions to Reviewers**` / `◼ **Be fair; you do not know it all**` / `◼ Be open-minded; you do not know it all` / `◼ Be constructive, not destructive`；`PAGE 220`：`**Suggestion to Community**` / `We Need to Fix the **Reviewer Accountability Problem**`；`PAGE 222`：`Research Community Needs **Accountable Reviewers**` | ✅ **两类建议 + 「公平」+「你并非什么都懂」逐字对应** |

**算术自检（方法⑤，只用源里同时给出的两组约束）**：
- `2560 ÷ 128 = 20`（PAGE 45 的 `2,560-DPU` ÷ PAGE 43 的 `128 DPUs`）与正文「插 20 条」一致 ✅
- `1.363ms ÷ 0.148ms ≈ 9.2`（PAGE 98 自己写了 `(9.2x)`）✅
- `16 种运算` 的 88×/5.8×/257×/31× 与 `7 个应用` 的 21×/2.1× 在 PAGE 165/166/167 的柱值里各出现一次，与 PAGE 168 的总结句一一对应 ✅

---

## 我核不到的（诚实记录）

> 「我没搜到」≠「源材料没有」。下面几条我**没有**找到源材料依据 ⇒ 我**不能**说它对或错。

1. **页码级定位的固有边界**：`PAGE 193–197`、`PAGE 204–214`（历史段里的**评审意见正文**）在文本层里**只有标题**（如 `One Review (ISCA 2013 Submission)` / `A Review from HPCA 2017: REJECT`），**评审正文是图片**，抽不出来。
   ⇒ 我对这段只能核到「哪次会议被拒/接收」与「`This Will Never Get Implemented` 这个标题」，**任何对评审内容的描述我都无法逐字核**。
   页面 L156 的两点概括我只在 `PAGE 219` 的**条目**里找到依据（`Be fair; you do not know it all`），**没有**核到「一项工作在当时看不清能不能实现，不等于它没有价值」这句在源里的对应句 —— 它更像页面自己的总结（我认为总结得当，但它是**推论**）。
2. **`PAGE 196` `Yet Later… in ISCA 2015…` 到底是「又被拒」还是别的**：该页只有标题。检索词 `ISCA 2015`（本讲 embedded 全文）⇒ 只命中该标题与论文清单里的 `MICRO 2015` 条目。
   ⇒ 我在 **P1-2** 里只说了「RowClone 至少有一次明确被拒（ISCA 2013）」，**没有**断言 ISCA 2015 那次也是拒。
3. **L92 的 0.5% / 0.2% 哪个对**：源讲义自己两页不一致（p80 vs p81），我**不能**替它裁定。反向检索了 `0.5%`、`0.2%`、`capacity`、`loss in capacity`（本讲 embedded 全文），除这两页外无第三处旁证（比如 area/容量总量）可以解方程。
   **边界**（方法⑤）：那一页**没有**第二组互相约束的证据（没有给子阵列的行数或总容量），所以方程解不出来 —— 我不硬解。
4. **L134 的 `16 种运算` 里有没有「取反」这一项**：`PAGE 164` 列了 16 项的**名字**，但排版把 `AND-/OR-/XOR-Reduction` 挤成一行，我**无法确定**那一行算 1 项还是 3 项。⇒ 我核到了「16」这个总数，**没有**逐项点清。检索词 `16 complex` 只命中 p164/p168 两处。
5. **L44「三条数字指向同一个结论」中的「三条」**：源里 `PAGE 26` 只给了**两条**测量（41% 与 ~115×），第三条（62.7%）在**另一页** `PAGE 27`。正文把它当一组「三条」讲是**跨页合并**，源里**没有**「三条」这个说法。
   ⇒ 我不认为这算错（三个数都在源里），但**源讲义没有把它们并作一组**这一点我没法核到「源里有」。记在这里备查。
6. **交叉核对文件不独立**：`text-clean/eth-ca2022-lecture3-processing-using-memory.txt` 与 `evidence/…pdf.embedded.txt` **逐字节相同**（`==` 为 `True`）。
   ⇒ 我**没有**第二份独立抽取可交叉验证，本记录全部结论都建立在**同一份**文本层之上。这是本记录最大的方法学缺口。
7. **配图的视觉细节**：我只读了**全部 10 张**的 `<title>`/`<desc>`/全部 `<text>`，并**逐张通读**了其中 3 张（`-1`、`-3`、`-8`）的完整 SVG 几何。
   **其余 7 张的几何**（箭头方向、是否有压线/溢出）我**没有**逐张读源码，只按它们与正文的**文字一致性**判断。

---

## 覆盖面

- **配图（两类必须分开写）**：
  - **逐张通读（读完整 SVG 源码、核几何 + 文案，共 3 张）**：`-1`（竖向三行 → 抓到 P0-1）、`-3`（上中下夹击 → 抓到 P0-2）、`-8`（横向四格 → 抓到 P2-2）。
  - **全文文本抽取（程序抽取全部 10 张的 `<title>`/`<desc>`/所有 `<text>`，逐张比对与正文的一致性）：10 张全部**（`-1` 至 `-10`）。
  - **只取哈希入档、未读**：**0 张**。
  - 结论：**10/10 张的文案都核过；3/10 张的几何逐张读过**；其余 7 张的几何未读（见「我核不到的」第 7 条）。
- **已验（源侧，逐字 + PAGE）**：1946 报告与三部件；p14「只有处理器被优化、存储单元是笨的」；p22 三条后果；p25 `100-1000X`；
  p26 `41%` + `~115×` + 两条引用；p27 `62.7%` + ASPLOS 2018；p4 三条原因且**页码正确**；p29 `minimal data movement`；
  p35/p36 Kautz 1969 / Stone 1970；p37/p39 `Designs are squeezed in the middle` + RowHammer + 三件事同时;
  UPMEM p43 **8GB/128 DPU/16 chips/2x-nm/DDR4** 五项；p45 `2,560-DPU`（且 2560÷128=20 与原图 `x10×x2` 互证）；三星 FIMDRAM+HBM-PIM(p48–53)、AxDIMM(p54)、AiM/GDDR6(p56–57)、HB-PNM 3D 堆叠(p59)、`many other experimental chips and startups`(p38)；
  p68 第一条路定义 + 代表工作；p72 cache 污染；p74 `Two consecutive ACTivates` / `Negligible HW cost` / `11.6X` / `74X`；p75 感应放大器；p76 两步；
  p77 `Shared internal bus` + `1.9X` / `3.2X`；p78/p82 `0.01%`；p80/p81 预留零行 + `6.0X` + `41.5X`；
  p92 四条弱点（同子阵列/跨子阵列/跨 channel/只模拟/未考虑多芯片）且 `Inter-Subarray=1.0x` 支撑「几乎没有」；
  p95 FIGARO 与 NoM 的分工；p98 LISA `isolation transistors 0.8%` + `1.363ms→0.148ms (9.2x)` + `66%` + `-55%` + 内存内缓存 + 快速预充电；
  p118 `30-60X`；p119 `AB + BC + AC`；p120 BULKAND 五步；p122 双接触单元；p126 `32X 35X`；p127/p128/p130 BitWeaving 与 Bitmap Index；
  p135 `end-to-end / programming interface / ISA / hardware support / arbitrary operations / minimal changes`；
  p136 垂直摆放 + 多数函数 + `Implicit shift`/`Massive parallelism`；p140 MAJ/NOT + Majority-Inverter Graph；
  p142/144/145/149–152/156 µProgram 三步；p160/161 末级缓存里的转置单元；p163/164/165/166/167/168 `16`/`7`/`88×`/`5.8×`/`257×`/`31×`/`21×`/`2.1×`；
  p108/177/225/227/228 ComputeDRAM 违反时序 + VDD/2 + `ACT(R2) will activate R3 and R2`；
  p234/236/238/239 PiDRAM 五步 + `119x` / `89x` + 开源；p107/112/181/242 Pinatubo/PCM；
  p189–224 历史段 36 页；p200–203 四次会议被拒 + p215 `MICRO 2017: Accepted` + p206/p208 `This Will Never Get Implemented`；
  p216–218 Raj Jain 书；p219/p220/p222 两类建议。
- **未验**：上面「我核不到的」7 条。

---

**核对人声明**：本记录只覆盖开头那个哈希的版本（`B8EEFB1BD97A71DB`，17599 B）。按附录五，对其它版本的结论不成立。
本记录只读正文与配图、只在 `docs/audit/` 下写这一份文件；**未修改任何 `content/` 文件**；`status` 由 Lead 处理。
`_sources/` 下我生成的探针产物 `…pdf.faithful.txt`（见提取层说明）**已删除**，未留在工作区。
