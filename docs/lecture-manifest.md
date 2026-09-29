# 讲次清单 · ETH Zurich Computer Architecture（CA Fall 2022 为主）

> 本文件定义这门课的「**全量**」= 下表全部讲授单元。逐讲产出一页。
> 授权：CC BY-NC-SA 4.0（wiki 站点级页脚，逐页出现）。第二人复核见
> 平台仓库 `docs/audit/licence-eth-ddca-ca-second-review.md`。
> **版本前缀固定为 `/architecture/fall2022/…`** —— 不带版本的路径现指向 Spring 2026。

## 授权边界（写作时不变）

**可用**：wiki 文本 + 课程自制 `onur-*` 讲义（`fetch.php?media=onur-…`）。
**不可用**：`(Paper)` 项、`*_micro2021/2022*`、`hermes-*`、`pluto_*`、`segram_*`、`flash_cosmos`、
`pidram`、`genpip-*`、`morpheus_*`、`pythia_*`、`polynesia_*`、`mensa_*`、`dr-strange-*`、
`alser-*`、`giray-*`、`quac-trng-*`（以上为第三方/客座/论文材料）；
助教材料 `ataberk-*` / `kanellok-*` **须逐件确认**无 `otherwise noted` 才可考虑。
**永久排除**：作业与考试题面/解答。

## 讲次表（Fall 2022，按 `onur-comparch-fall2022-*` 讲义）

| # | slug（拟定） | 讲义文件（`afterlecture`/`beforelecture` 以实际取到的为准） |
| --- | --- | --- |
| 1 | `lecture1-intro` | `lecture1-intro-afterlecture.pdf` |
| 2 | `lecture2a-memory-trends` | `lecture2a-memory-trends-challenges-opportunities-afterlecture.pdf` |
| 3 | `lecture2b-courselogistics` | `lecture2b-courselogistics-afterlecture.pdf` |
| 4 | `lecture3-processing-using-memory` | `lecture3-processing-using-memory-afterlecture.pdf` |
| 5 | `lecture4-processing-near-memory` | `lecture4-processing-near-memory-afterlecture.pdf` |
| 6 | `lecture6-rowhammer` | `lecture6-rowhammer-and-secureandreliablememory-afterlecture.pdf` |
| 7 | `lecture7a-rowhammer-ii` | `lecture7a-rowhammer-ii-afterlecture.pdf` |
| 8 | `lecture7b-retention-refresh` | `lecture7b-memory-dataretention-and-refresh-afterlecture.pdf` |
| 9 | `lecture8a-retention-refresh-ii` | `lecture8a-memory-dataretention-and-refresh-ii-afterlecture.pdf` |
| 10 | `lecture8b-memory-latency` | `lecture8b-memorylatency-afterlecture.pdf` |
| 11 | `lecture9-memory-latency-ii` | `lecture9-memorylatency-ii-afterlecture.pdf` |
| 12 | `lecture10-upmem` | `lecture10-upmem-afterlecture.pdf` |
| 13 | `lecture11a-memory-controllers` | `lecture11a-memorycontrollers-afterlecture.pdf` |
| 14 | `lecture11b-simulation` | `lecture11b-simulation-afterlecture.pdf` |
| 15 | `lecture12a-simulation-ii` | `lecture12a-simulation-ii-afterlecture.pdf` |
| 16 | `lecture12b-emerging-memory` | `lecture12b-emergingmemorytechnologies-afterlecture.pdf` |
| 17 | `lecture13a-emerging-memory-ii` | `lecture13a-emergingmemorytechnologies-ii-afterlecture.pdf` |
| 18 | `lecture13b-memory-controllers-ii` | `lecture13b-memorycontrollers-ii-performance-interference-qos-afterlecture.pdf` |
| 19 | `lecture15-memory-contention` | `lecture15-memorycontention-performance-qos-complexity-afterlecture.pdf` |
| 20 | `lecture16-prefetching` | `lecture16-prefetching-beforelecture.pdf` |
| 21 | `lecture17a-multiprocessors` | `lecture17a-multiprocessors-afterlecture.pdf` |
| 22 | `lecture17b-memory-ordering` | `lecture17b-memoryordering-afterlecture.pdf` |
| 23 | `lecture19-cache-coherence` | `lecture19-cachecoherence-afterlecture.pdf` |
| 24 | `lecture20-interconnects` | `lecture20-interconnects-afterlecture.pdf` |
| 25 | `lecture21-on-chip-networks` | `lecture21-onchipnetworks-afterlecture.pdf` |
| 26 | `lecture22-parallelism-heterogeneity` | `lecture22-parallelism-heterogeneity-bottleneck-acceleration-beforelecture.pdf` |
| 27 | `lecture25-simd-gpu` | `lecture25-simd-processors-and-gpu-beforelecture.pdf` |
| 28 | `lecture26-gpu-programming` | `lecture26-gpuprogramming-beforelecture.pdf` |
| 29 | `lecture27-flash-memory` | `lecture27-flashmemory-beforelecture.pdf` |
| 30 | `lecture28-vliw-systolic` | `lecture28-vliw-systolic-array-architectures-beforelecture.pdf` |
| 31 | `lecture29-virtual-memory` | `lecture29-virtual-memory-beforelecture.pdf` |

**⇒ 31 个讲授单元。**（讨论课 `discussionsession1/2/3` 与习题课 `problem-solving-iv` 不计入正课；
若第 2 轮之后要收，另开一档。）

## 备注

- 上表按 `_sources/eth-ddca-ca/evidence/eth-ca-fall2022-schedule.html` 里实际出现的
  `onur-comparch-fall2022-*` 链接整理（36 个 PDF/PPTX 对 ⇒ 去重 18，加上其它命名共 31 项）。
  **开工前应逐条核对文件确实可取**（`000 ≠ 404`，失败要重试）。
- DDCA **Spring 2023** 是另一套编号（`onur-ddca-2023-lecture*`），
  **本课程以 Fall 2022 的 CA 为主线**；若要合并两套，需先确认不重复计数。
