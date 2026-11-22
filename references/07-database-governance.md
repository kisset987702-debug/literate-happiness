# MODULE 07 — DATABASE GOVERNANCE

来源：`创作智能系统/7.txt`（原文逐字保留在本文件末尾）

## 五个逻辑数据域

| 域 | 装什么 | 写入时机 |
|---|---|---|
| 世界库 | 规则卡、地理、势力、时间线、经济 | 设定确认时 |
| 人物库 | 人物卡、关系、认知边界、状态 | 建卡与状态变化时 |
| 剧情事件库 | 章纲、事件因果、伏笔台账 | 每章写完 |
| 读者质量库 | 量表得分、问题登记、复核结果 | 每章审查后 |
| 工程治理库 | 版本、备份、DEF、BLK、证据 | 每次变更 |

五个是**逻辑域**，不代表五个物理 SQLite 文件。现有工程实现为单库多表。

## 五状态

`PROPOSED` 候选 → `CONFIRMED` 已确认 → `FROZEN` 已冻结 → `DEPRECATED` 已废弃 / `REJECTED` 已否决

规则：

- 对话里说定的，默认只是 PROPOSED，**不等于已写入**
- 正文只能使用 CONFIRMED 及以上
- FROZEN 不得修改，只能新版覆盖并保留旧版
- 降级（→DEPRECATED/REJECTED）必须写理由

## 写入流程（缺一步不得报告成功）

1. **核验基线**：确认当前版本与文件哈希
2. **备份**：复制到 `.backup/`，记录文件大小与 SHA-256
3. **迁移**：结构变更走 migration 脚本，不手改
4. **权限**：确认写入身份与路径正确（TEST / 生产分离）
5. **事务**：单次变更原子提交，失败回滚
6. **回读**：写入后立即 SELECT 回来比对
7. **完整性**：`PRAGMA integrity_check` + `foreign_key_check`
8. **证据**：命令、输出、时间、哈希落档

## 本机实测现状（2026-11-22 核验）

工程：`E:\AI写小说\AI写小说\创作智能系统\top_writer_pro_v6_implementation`

```
库文件：data/TEST-PROJECT-001.sqlite3
integrity_check : ok
foreign_key_check: 无违规
表（7）：audit_log, changesets, engine_registry, evidence, project_meta, records, snapshots
行数：engine_registry 7 / project_meta 1 / 其余 0
```

判读：**结构已建、内容为空**。即：五个逻辑域的容器存在，但没有装任何设定、人物、章节记录。
- 当前不得声称「五大数据库已在治理中」
- 当前设定一律视为 **PROPOSED**，除非另行写入并回读

## 诚实边界

- 对话记录 ≠ SQLite 写入
- 没跑过回读验证，不许说「已写入成功」
- TEST-PROJECT-001 是测试库，不是生产库，不得混用

---

## 原文

DATABASE GOVERNANCE：管理世界、人物、剧情事件、读者质量、工程治理五个逻辑数据域。记录区分PROPOSED、CONFIRMED、FROZEN、DEPRECATED、REJECTED。对话记录不等于SQLite写入。任何数据库修改必须核验基线、备份、迁移、权限、事务、回读、完整性和证据。
