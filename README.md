# AskData Studio 运营场景版

面向短视频运营的自然语言问数项目，包含 Vue 前端、FastAPI + LangGraph 后端、41 张模拟业务表的数据生成脚本、字段级 Schema 定义、测试脚本和历史评测报告。

## 代码基线（2026-11-10）

本次提交保存运营场景版的现有实现，供后续迭代对比。仓库保留源码、配置示例、前端依赖锁文件、Schema/表清单、评测用例和历史报告；不包含完整 CSV 实例数据、生成的 Embedding 缓存、本地密钥、会话和保存的用户记录。

模拟数据由固定种子生成，原实例包含 41 张表、276 个字段、53 条表关系及 722,732 行数据。Schema 中保留字段样例和统计摘要，评测用例保留必要的参考结果；这些不代表真实生产业务数据。

当前支持单数据库查询，多数据库 Handoff 尚未启用。历史评测使用预设检索词，未覆盖完整入口路由与最终分析；结果比较器尚未落实排序校验。报告数字仅代表当时实现和判分规则下的离线结果，不能作为严格端到端准确率。本基线记录这些限制，未修改业务与评测逻辑，也未重新运行付费模型评测。

## 环境要求

- Python 3.11
- Node.js 20.19 或更高版本
- 兼容 OpenAI 接口的大模型、Embedding 和 Rerank 服务

## 安装

以下命令均从解压后的项目根目录执行。

后端（Windows PowerShell）：

```powershell
cd backend
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

后端（macOS / Linux）：

```bash
cd backend
python3.11 -m venv .venv
./.venv/bin/python -m pip install -r requirements.txt
cp .env.example .env
```

编辑 `backend/.env`，填写 `LLM_API_KEY`。默认模型配置为 `qwen3.7-plus`、`text-embedding-v4` 和 `qwen3-rerank`。

前端：

```powershell
cd frontend
npm install
```

## 生成本地模拟数据

首次从 GitHub 获取项目后，先生成未随仓库上传的 CSV 数据。在 `backend` 目录、已安装依赖的 Python 环境中执行：

```bash
python scripts/generate_short_video_ops_data.py
python scripts/validate_short_video_ops.py
```

脚本会生成或覆盖 `backend/data/databases/short_video_ops/` 下的模拟 CSV 和表清单。已有本地数据时，仅在需要重建实例时执行。仓库已附带对应的 `_schema.json`；修改数据或结构后，可运行 `python scripts/generate_short_video_ops_schema.py` 更新字段样例和统计信息。

填写 `backend/.env` 后，可执行 `python -m scripts.prepare_schema_index` 生成本地字段向量索引。此步骤会调用 Embedding 服务；首次查询也会按需构建索引，生成的 `backend/data/schema_store.json` 不纳入版本管理。

## 启动

分别打开两个终端。

后端（Windows）：

```powershell
cd backend
.\.venv\Scripts\python.exe run.py
```

后端（macOS / Linux）：

```bash
cd backend
./.venv/bin/python run.py
```

前端：

```powershell
cd frontend
npm run dev
```

访问 `http://127.0.0.1:5173`，API 文档位于 `http://127.0.0.1:8000/docs`。

## 测试

在 `backend` 目录执行：

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests -p "test_*.py"
.\.venv\Scripts\python.exe scripts\validate_short_video_ops.py
```

评测命令与指标说明见 `backend/evaluation/README.md`。现有评测结果位于 `backend/evaluation/results`。

## 测试账号

- 管理员：`admin` / `admin123`
- 用户增长运营：`growth` / `growth123`
- 渠道投放运营：`channel` / `channel123`
- 内容运营：`content` / `content123`
