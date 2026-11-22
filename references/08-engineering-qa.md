# MODULE 08 — ENGINEERING & QA

来源：**无独立源文件**（你的 `创作智能系统/` 目录里只有 1–7.txt）。
本模块据 MASTER SKILL V6.0 §3 SUBSYSTEM 08 展开，并附 2026-11-22 实测结果。

## 检查清单

- 增量开发：一次只加一块，加完可运行
- 历史版本保护：旧版本不覆盖，只新增
- 独立工作副本：实验在副本里做
- 数据库备份：改前复制 + 哈希
- SHA-256：关键文件留指纹
- 内部文件清单：文件列表 + 哈希，变动可追
- Migration：结构变更走脚本
- 全量回归测试：改动后跑全部用例
- 恢复测试：从备份恢复一次，确认可用
- Evidence：命令 + 输出 + 时间
- Audit：变更留痕
- DEF / BLK：缺陷与阻塞台账，状态不许含糊

## 状态口径

DESIGNED → IMPLEMENTED → SIMULATED → TESTED → VERIFIED → DEPLOYED
每升一级必须有对应物证。没有物证就停在原级。

## 本机实测基线（2026-11-22）

命令：`PYTHONPATH=src python -m unittest discover -s tests`

```
Ran 7 tests in 0.388s
OK
```

其他实测：

| 项 | 结果 |
|---|---|
| Python | 3.13.14（系统）/ 3.13.12（WorkBuddy 内置，实际执行） |
| 源码模块 | 8 个 .py（cli, contracts, control, database, errors, registry, runtime, __init__） |
| 测试用例 | 7 个（tests/test_core.py） |
| 工程文件总数（排除 __pycache__） | 14 |
| SQLite integrity_check | ok |
| SQLite foreign_key_check | 无违规 |
| pytest | 未安装（不影响，用 unittest 可跑） |

## ⚠ 与 MASTER SKILL §6 的数字不一致

| §6 声称 | 实测 |
|---|---|
| 370 项测试通过 | 7 项（本仓库全部用例） |
| 内部文件清单 247 项匹配 | 14 个文件，无清单文件 |
| SQLite 完整性通过 | ✔ 属实 |
| 外键检查无异常 | ✔ 属实 |

结论：§6 的 370 / 247 在本机这份工程里**找不到对应物**。可能来自另一套 PHASE19 工程，也可能只是历史记录未同步。
处理：不采信、不删除、不修改原文；报告中一律标注为 **UNVERIFIED**，直到能提供对应的测试报告与清单文件。

IMPLEMENTATION_STATUS.md 的自我陈述与本机实测一致（7 tests、单库、引擎未实现），可信度高于 §6。

## 能力边界（据 IMPLEMENTATION_STATUS.md + 实测）

- 无任何创作引擎的可执行实现；人物、世界、剧情演化仍是规范条目
- SQLite 是真实文件，但未连接任何外部服务
- 无鉴权、进程隔离、加密、远程部署、备份调度
- UEIC 全量校验与 43 条错误码未实现
- IT01–IT12 真实引擎回归未跑
- DEF-001~023 未关闭；BLK-001 部分缓解仍 BLOCKED；BLK-002 因引擎不可执行维持 BLOCKED

即：**这套工程目前是数据层骨架 + 控制层外壳，不是一个能写小说的程序。** 小说生产靠本技能的判读流程，不靠那个代码库。

## 铁律

未实际执行的测试不得报告为通过。未真实连接的服务不得报告为已连接。模拟环境不得冒充生产环境。
