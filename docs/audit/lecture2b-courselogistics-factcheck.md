# 事实核对 · ETH Zürich Computer Architecture（Fall 2022 · Onur Mutlu）第 3 讲（2b）这门课怎么上：目标、评估与学法

- 核对人：**非作者**（复用 mit-6.5840 七讲记录的核对者身份 `mit65840-reviewer`）
- 核对日期：2026-09-29
- 被核对版本：`content/03-lecture2b-courselogistics/index.md`
  归一化 SHA256 前16 = **75A2606CEDADCDAB**
  （全 64 位 `75A2606CEDADCDABF67B6ABC1424E766761077A494A3E8EAA4D670983AA24F90`，7013 B）
  （**与 Lead 派单时给的哈希一致** ✅）
  - 4 张配图（前 16 位）：`-1 A033E4B953445454` · `-2 6F832438D6ECDF68` · `-3 0A12268C398335A2` · `-4 9C0DF41E0FC1419B`
- 源材料：

| # | 文件 | 用途 |
| --- | --- | --- |
| 1 | `_sources/eth-ddca-ca/evidence/eth-ca2022-lecture2b-logistics.pdf.embedded.txt`（219 行，7879 B，**20 页**） | **主要依据**（本讲篇幅小、抽取层完整） |
| 2 | `_sources/eth-ddca-ca/text-clean/eth-ca2022-lecture2b-logistics.txt` | 交叉核对（同源的 clean 版） |

> 提取层：沿用第 1、2 讲的结论 —— **faithful（`pdftext3.py`）在 eth-ca 的 PDF 上不可用**（输出乱码），
> 本讲用 `evidence/*.pdf.embedded.txt` 并逐条附 PAGE 与逐字原文。

---

## 结论

**P0（事实错误）：0 条 ｜ P1（易误解/依据不足）：0 条 ｜ P2（措辞）：2 条**

本讲的实质内容（两条课程目标、评估权重、先修与期望、必读）**全部逐字核到**；页面在**唯一一处越界**（整门课的地图）**主动标注了「这一句是我们整理的，不是源讲义上的原话」** ✅ —— 这是本课三讲里处理得最干净的一处。

---

## 已核对通过（逐条附 PAGE + 逐字引用）

| 正文 | 源（PAGE · 逐字引用） | 结果 |
| --- | --- | --- |
| L13 讲义 20 页 | 最后一页标记 `===== PAGE 20 =====` | ✅ |
| L22 「讲师把『怎么学』单独讲了一节」 | 该讲义即 `Lecture 2b: Course Info & Logistics`（20 页，含 `What Do I Expect From You?`、`How Will You Be Evaluated?`、`Heads Up`、`Required Reading`） | ✅ 内容与该页概述一致 |
| L28 两条课程目标（第一条：熟悉处理器/内存/平台架构的基本工作**原理**与**设计取舍**，重点在基础、取舍、当前与未来关键问题；第二条：提供设计/实现/评估现代处理器所需的背景与经验，方式是**亲手实现模拟器**，重点在功能、动手实现与效率） | **PAGE 12**：`Goal 1: To familiarize those interested in computer system design with both fundamental operation principles and design tradeoffs of processor, memory, and platform architectures in today's systems.` + `Strong emphasis on fundamentals, design tradeoffs, key current/future issues`；`Goal 2: To provide the necessary background and experience to design, implement, and evaluate a modern processor by performing hands-on simulator implementation.` + `Strong emphasis on functionality, hands-on design & implementation, and efficiency` | ✅ **两条目标逐字对应**（含三处「重点」） |
| L32 整门课的地图 | **页面自己标注**：`（这一句是我们按后续讲次的安排整理的，不是源讲义上的原话）` | ✅ **越界已声明**（这正是第 1 讲记录里 P1 的那一类问题，此处处理正确） |
| L40/L42 软硬件都要学：懂硬件的软件人能写出更好的软件；懂软件的硬件人能设计更好的硬件；两边都懂才能设计更好的计算系统 | **PAGE 7**：`This course might seem like it is only "Computer Hardware"`；`However, you will be much more capable if you master both hardware and software (and the interface between them)`；`Can develop better software if you understand the hardware`；`Can design better hardware if you understand the software`；`Can design a better computing system if you understand both` | ✅ **三句逐字对应** |
| L44 两边靠 ISA 这层接口接起来；转换层次图 | **PAGE 8** `The Transformation Hierarchy`（含 `SW/HW Interface`） | ✅ |
| L52 评估权重：Lab 50%、期末笔试（**180 分钟**）30%、平时作业 20%；另有额外加分机会 | **PAGE 11**：`Lab assignments: 50%`；`Final exam (180 minutes): 30%`；`Homeworks: 20%`；`Many extra credit possibilities in HWs, Labs, Exam` | ✅ **三个百分比 + 180 分钟 + 加分项全部逐字对应** |
| L66 先修背景：数字电路、会编程、以及一颗愿意接受新概念的心 | **PAGE 9**：`Required background: Digital circuits course, programming, an open mind willing to take in many exciting concepts` | ✅ 逐字对应 |
| L68 把材料学透＝听课、读材料、做练习、做 Lab，四件事都得做；并且早开始 | **PAGE 9**：`Learn the material thoroughly / attend lectures, do the readings, do the exercises, do the labs`；`Start early`；**PAGE 10**：`How you prepare and manage your time is very important`；`There will be many lab and homework assignments / They will take time / Start early, work hard` | ✅ **四件事逐字对应**；「都会吃时间」也是原文 |
| L70 提问、记笔记、参与线上（lecture、Moodle）与答疑时间；引巴斯德「机遇偏爱有准备的头脑」 | **PAGE 9**：`Ask questions, take notes, participate`；`Participate online (lecture, Moodle)`；`If you want feedback, come to office hours`；`Remember "Chance favors the prepared mind." (Pasteur)` | ✅ **逐字对应**（引文与出处都对） |
| L72 讲义指定必读 `youandyourresearch.pdf` | **PAGE 16/17**：`Required Reading` / `Required Reading on Mindset & More` + `https://safari.ethz.ch/architecture/fall2021/lib/exe/fetch.php?media=youandyourresearch.pdf` | ✅ 文件名与 URL 逐字对应（**作者归属见 P2-1**） |
| L84 「作业与 Lab 都会吃时间」「早开始」 | **PAGE 15**：`Lab 1 is already out / Due in ~2 weeks after release`；`HW1 will be out soon / Due in ~2 weeks after release`；`My goal is to enable your learning and growth, so labs can be done any time until the end of the semester / But, please know yourself and plan accordingly` | ✅ 主句都对（**「按周释放」见 P2-2**） |

**配图**：4 张逐张核了 `title`/`desc`/图内文字：
`-1` 三件事（懂原理 / 会实现 / 能取舍）与 L28/L36 一致 ✅；`-2` 软件人↔硬件人 + 「接住两边的是 ISA 这层接口」与 L42/L44 一致 ✅；
`-3` 三条形（50% / 期末 180 分钟 30% / 20%）+ 「三段合计 100%，另有额外加分机会」与源 PAGE 11 一致 ✅；
`-4` 四条期望 + 「源讲义引巴斯德：机遇偏爱有准备的头脑」与源 PAGE 9 一致 ✅。**四张都没读出文字没下的断言。**

---

## P2 · 措辞

### P2-1 「Hamming 的《You and Your Research》」这个作者归属，讲义文本层里没有

正文 L72：「讲义另指定了一篇必读：**Hamming 的《You and Your Research》**。」
- 源（PAGE 16/17）只给了标题行 `Required Reading on Mindset & More` 与 URL/文件名 `youandyourresearch.pdf`，
  **没有出现 Hamming 这个名字**。
- 这份文件就是 Hamming 那篇著名演讲（文件名与内容对得上），所以**判断大概率正确**；但它属于**页面替原文补的归属**。
- **修在**：加一句「（讲义给的是文件名 `youandyourresearch.pdf`）」或改成「讲义指定的必读是一篇讲研究心态的材料（Hamming 的 *You and Your Research*）」。

### P2-2 L84 「作业与 Lab 都**按周释放、按周堆积**」—— 源里没有这个节奏

正文 L84（小结第三条）：「作业与 Lab 都按周释放、按周堆积，堆到期末就只剩赶工。」
- 源（PAGE 15）说的是：`Lab 1 is already out`、`Due in ~2 weeks after release`、`HW1 will be out soon`、`Due in ~2 weeks after release`，
  并且 `labs can be done any time until the end of the semester`。
- ⇒ **「两周内到期」是原文，「按周释放」不是**；而且原文恰好**放宽**了 Lab 的截止（可以做到学期末）。
- **修在**：改成「作业与 Lab 会一批批释放、每次都吃时间（讲义写的是释放后约两周到期，Lab 也可以做到学期末）」。

---

## 我核不到的（诚实记录）

1. **L22 「讲师把『怎么学』单独讲了一节」**：讲义标题是 `Course Info & Logistics`，其中确实有 `What Do I Expect From You?`（两页）与 `How Will You Be Evaluated?`。
   ⇒ **「单独讲一节」在版面上是否被讲师明确框成『怎么学』这一节**，文本层看不出来（我核到的是内容分布，不是讲师的框法）。范围 = 本讲 embedded 全文。
2. **P2-1 里 Hamming 的归属**：我只能核到文件名；**作者身份是从该文件与本领域常识推的**，不是源页文字。
3. **`text-clean` 与 `embedded` 是同源**：本讲没有第二份独立抽取可交叉验证（faithful 在该 PDF 上不可用）。

---

## 覆盖面（附录十二）

- **已验**：20 页；两条课程目标（逐字）；软硬件三条（逐字）；ISA 与转换层次页；评估三项 + 180 分钟 + 加分项（逐字）；
  先修三项（逐字）；学习四件事（逐字）；提问/笔记/线上（lecture、Moodle）/答疑 + 巴斯德引文（逐字）；必读文件名与 URL；
  Lab/HW 约两周到期 + Lab 可做到学期末；以及**页面自己标注的那处越界**（整门课地图）。
  4 张配图逐张核 `title`/`desc`/图内文字。
- **未验**：上面「我核不到的」3 条。

---

**核对人声明**：本记录只覆盖开头那个哈希的版本（`75A2606CEDADCDAB`，与 Lead 派单一致）。按附录五，对其它版本的结论不成立。
本记录只读正文、不修改任何 `content/` 文件；`status` 由 Lead 处理。
