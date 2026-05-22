# DynamoDB 基础操作 POC — 学习总结

> 一个循序渐进的 DynamoDB + pynamodb 学习项目，从最小骨架到单表设计，共 12 个示例。

## 📚 项目概述

这个项目是对 DynamoDB 最佳实践的学习复现。通过 12 个递进式的示例，掌握：

- **基础**（00-05）：表结构、属性类型、CRUD、批量、查询优化
- **优化**（06-08）：条件表达式、事务、二级索引
- **高级**（09-11）：综合演示、单表 1:N、单表 M:N

**核心语言**：Python + pynamodb（DynamoDB 的 ORM）
**学习时间**：4-6 小时
**难度**：初级 → 中级

---

## 🎯 核心学习成果

### 掌握的关键概念
- ✅ **架构设计**：单表 vs 多表，PK/SK 设计
- ✅ **查询优化**：Query vs Scan，GSI 的威力（5 倍成本差）
- ✅ **扩展能力**：批量操作、事务、条件表达式
- ✅ **实战模式**：1:N、M:N 关系设计

### 最重要的三个洞察
1. **Query > Scan**：永远用 Query + 索引代替 Scan
2. **单表 > 多表**：用单表设计避免 JOIN（DynamoDB 无 JOIN）
3. **成本由 PK 决定**：好的分区键设计 = 好的性能

---

## 📖 12 个学习模块概览

| # | 模块 | 重点 | 学习时间 |
|---|---|---|---|
| **00** | 最小骨架 | Model、Attribute、Meta | 15 分钟 |
| **01** | 属性类型 | 8 种属性 + default / null | 20 分钟 |
| **02** | 表管理 | 创建、销毁、计费模式 | 15 分钟 |
| **03** | CRUD | save、get、update、delete、refresh | 25 分钟 |
| **04** | 批量操作 | batch_write、batch_get、自动分块 | 20 分钟 |
| **05** | Query vs Scan | ⭐ 性能课题，成本 5 倍差 | 30 分钟 |
| **06** | 条件表达式 | 防覆盖、乐优锁 | 20 分钟 |
| **07** | 事务 | 多行原子操作 | 20 分钟 |
| **08** | 二级索引 | ⭐ GSI vs LSI、投影 | 30 分钟 |
| **09** | 综合演示 | 真实场景组合 | 25 分钟 |
| **10** | 单表 1:N | ⭐⭐ 客户→卡→交易 | 40 分钟 |
| **11** | 单表 M:N | ⭐⭐ 三种设计方案对比 | 40 分钟 |

---

## 🔑 核心知识梗概

### 00-05：基础与查询优化
**一句话总结**：学会 CRUD 和查询，理解 Query 和 Scan 的成本差异。

**关键数据**：
```
Scan 全表 30 项  → 2.5 RCU
Query GSI 3 项  → 0.5 RCU
成本差：5 倍！
```

### 06-08：优化与索引
**一句话总结**：条件表达式处理并发，GSI 解决查询问题。

**关键模式**：
- 条件表达式 = DynamoDB 的"锁"
- GSI = "反向索引"，一个写、两个查询方向

### 09-11：单表设计（最重要）
**一句话总结**：一张表、多种实体、用 PK 前缀分组、SK 前缀区分类型。

**单表 vs 多表**：
```
多表（Customer / Card / Transaction）
查客户全部信息 → 需要 3 次查询 ❌

单表（所有数据）
query("CUSTOMER#C001") → 一次搞定 ✅
```

---

## 🚀 快速开始

### 1. 环境要求
- Python 3.10+
- AWS 账户（us-east-1 DynamoDB 权限）

### 2. 安装
```bash
# 克隆项目（本项目基于 learn-dynamodb-basic-operations）
git clone https://github.com/shm6886/Haoming-Sun-dynamodb-basic-operations-poc
cd Haoming-Sun-dynamodb-basic-operations-poc

# 安装依赖
pip install -r requirements.txt
# 或使用 uv
uv sync
```

### 3. 配置 AWS
编辑 `.env`，填入你的 AWS profile（需要 us-east-1 权限）：
```bash
AWS_PROFILE="your-profile-name"
```

### 4. 运行示例
```bash
# 运行单个脚本
python examples/00-minimal-poc/s01_minimal_poc.py

# 清理所有表
python examples/cleanup_all_tables.py
```

---

## 📚 核心知识速查

### Query vs Scan
| 操作 | 成本 | 速度 | 何时用 |
|---|---|---|---|
| **Query** | ∝ 结果数 | 快 | ✅ 生产代码 |
| **Scan** | ∝ 表大小 | 慢 | ❌ 运维脚本 only |

**法则**：想不到 Query 方案？→ 加 GSI → 再 Query

### 单表设计核心
```python
PK = "ENTITY_TYPE#ID"
SK = "RELATIONSHIP#DATA"

# 例：单表存储 Customer / Card / Transaction
CUSTOMER#C001          | PROFILE
CUSTOMER#C001          | CARD#CD001
CUSTOMER#C001          | TX#CD001#2026-04-27T10:00
```

### 成本优化顺序
1. **设计好 PK**（最重要，直接影响分布和查询）
2. **用 batch_write / batch_get**（减少往返）
3. **加 GSI**（避免 scan）
4. **条件表达式**（并发安全）

---

## 🛠️ 技术栈

| 工具 | 用途 |
|---|---|
| **pynamodb** | DynamoDB Python ORM |
| **boto3** | AWS 官方 SDK |
| **pytest** | 测试框架 |
| **uv** | 包管理器 |

---

## 📚 扩展阅读

- [AWS DynamoDB 官方指南](https://docs.aws.amazon.com/dynamodb/)
- [Single-table design patterns](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html)
- [pynamodb 文档](https://pynamodb.readthedocs.io/)
- [boto3 DynamoDB 参考](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html)

---

## 💡 最后的话

> DynamoDB 的关键不是记住 API，而是理解它的设计哲学：
> - 无连接（REST API）→ 高扩展性
> - 无 JOIN → 单表设计
> - 按容量计费 → 查询优化最重要

掌握这三点，你就真正理解了为什么要这样设计数据库。

