# 收费管理系统 - 智能数据分析平台

## 🎯 项目概述

这是一个基于FastAPI和MCP（Model Context Protocol）的智能收费数据分析平台，提供实时数据查询、智能报告生成和AI对话分析功能。

## 🏗️ 系统架构

### 整体架构图

```
┌─────────────────────────────────────────────────────────────┐
│                    前端界面 (左右分栏布局)                    │
│  ┌─────────────────────────┬─────────────────────────────┐  │
│  │      报告生成界面        │       智能对话界面          │  │
│  │   • 报告参数设置        │    • 自然语言查询          │  │
│  │   • 图表展示           │    • AI智能回答            │  │
│  │   • 报告下载           │    • 对话历史管理          │  │
│  └─────────────────────────┴─────────────────────────────┘  │
└─────────────────────┬───────────────────────────────────────┘
                      │ HTTP请求
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                FastAPI服务 (后端核心)                        │
│  ┌─────────────────┬─────────────────┬─────────────────────┐ │
│  │   报告API       │   聊天API       │    健康检查API      │ │
│  │   (report_api)  │   (chat)        │    (health)         │ │
│  └─────────────────┴─────────────────┴─────────────────────┘ │
└─────┬─────────────────────────────────────────┬─────────────┘
      │                                         │
      ▼                                         ▼
┌─────────────────────────────────┐  ┌─────────────────────────────────┐
│        报告生成模块              │  │        智能对话模块              │
│  ┌─────────────────────────────┐ │  │  ┌─────────────────────────────┐ │
│  │   ReportGenerator           │ │  │  │      MCP客户端              │ │
│  │   • 数据获取               │ │  │  │   • 工具调用管理            │ │
│  │   • 图表生成               │ │  │  │   • 会话管理               │ │
│  │   • AI分析                 │ │  │  │   • OpenAI集成             │ │
│  │   • 报告合成               │ │  │  └─────────────────────────────┘ │
│  └─────────────────────────────┘ │  │              │                  │
└─────────────────┬───────────────┘  └──────────────┼──────────────────┘
                  │                                 │
                  ▼                                 ▼
┌─────────────────────────────────┐  ┌─────────────────────────────────┐
│        外部API调用              │  │        MCP服务器                │
│  ┌─────────────────────────────┐ │  │  ┌─────────────────────────────┐ │
│  │     HTTP客户端              │ │  │  │   platformbilling_mcp       │ │
│  │   • 重试机制               │ │  │  │   • 12个MCP工具             │ │
│  │   • 超时处理               │ │  │  │   • 数据格式化             │ │
│  │   • 错误处理               │ │  │  │   • API代理                │ │
│  └─────────────────────────────┘ │  │  └─────────────────────────────┘ │
└─────────────────┬───────────────┘  └─────────────────┬───────────────┘
                  │                                    │
                  ▼                                    │
┌─────────────────────────────────────────────────────┼─────────────────┐
│                    外部API服务                        │                 │
│            http://116.182.4.67:8088/gssfApi2        │                 │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │                    API接口列表                                   │ │
│  │  • trade/realTimeTurnover          - 实时交易数据              │ │
│  │  • trade/companyPayTypeTurnover     - 分公司支付交易数据        │ │
│  │  • trade/companyVehTypeTurnover     - 分公司车型交易数据        │ │
│  │  • trade/stationPayTypeTurnover     - 收费站支付交易数据        │ │
│  │  • trade/stationVehTypeTurnover     - 收费站车型交易数据        │ │
│  │  • traffic/realTimeTrafficFlow     - 实时车流量数据            │ │
│  │  • traffic/companyPayTypeTrafficFlow - 分公司支付车流量数据    │ │
│  │  • traffic/companyVehTypeTrafficFlow - 分公司车型车流量数据    │ │
│  │  • traffic/stationPayTypeTrafficFlow - 收费站支付车流量数据    │ │
│  │  • traffic/stationVehTypeTrafficFlow - 收费站车型车流量数据    │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

### 模块化架构设计

```
backend/
├── start_server.py          # 🚀 服务启动入口
├── apis/                    # 🌐 API接口层
│   ├── fastapi_server.py    #   ├─ FastAPI主服务
│   ├── report_api.py        #   ├─ 报告生成API
│   └── platformbilling_mcp.py # └─ MCP服务器
├── clients/                 # 🔗 客户端层
│   ├── mcp_client.py        #   ├─ MCP客户端
│   └── api_client.py        #   └─ 外部API客户端
├── reports/                 # 📊 报告模块
│   └── report_generator.py  #   └─ 报告生成器
├── config/                  # ⚙️ 配置模块
│   ├── settings.py          #   ├─ 配置管理
│   ├── .env                 #   └─ 环境变量
└── utils/                   # 🛠️ 工具模块
    └── test_api.py          #     └─ API测试工具

frontend/
├── start_frontend.py        # 🚀 前端启动脚本
├── index.html               # 🏠 主界面
├── report.js                # 📊 报告功能脚本
├── chat.js                  # 💬 对话功能脚本
└── styles.css               # 🎨 样式文件
```

### 数据流向图

```
┌─────────────┐    HTTP     ┌─────────────┐    MCP      ┌─────────────┐
│   用户请求   │ ────────► │  FastAPI   │ ──────────► │ MCP服务器   │
│   (前端)    │           │   服务器    │             │             │
└─────────────┘           └─────────────┘             └─────────────┘
                                 │                           │
                                 ▼                           ▼
                          ┌─────────────┐             ┌─────────────┐
                          │  报告生成器  │             │  外部API    │
                          │             │             │   调用      │
                          └─────────────┘             └─────────────┘
                                 │                           │
                                 ▼                           ▼
                          ┌─────────────┐             ┌─────────────┐
                          │ 图表+AI分析 │             │  收费系统   │
                          │   结果      │             │   数据源    │
                          └─────────────┘             └─────────────┘
                                 │                           │
                                 ▼                           │
                          ┌─────────────┐                    │
                          │  前端展示   │ ◄──────────────────┘
                          │   (JSON)    │
                          └─────────────┘
```

### 技术架构栈

```
┌─────────────────────────────────────────────────────────────┐
│                      技术栈架构                              │
├─────────────────────────────────────────────────────────────┤
│  前端层 (Frontend)                                           │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │   HTML5     │    CSS3     │ JavaScript  │  Chart.js   │  │
│  │   结构层     │   样式层     │   逻辑层     │   图表层     │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
├─────────────────────────────────────────────────────────────┤
│  后端层 (Backend)                                            │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │   FastAPI   │     MCP     │  Matplotlib │    HTTPX    │  │
│  │  Web框架    │   协议层     │   图表生成   │  HTTP客户端  │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
├─────────────────────────────────────────────────────────────┤
│  数据层 (Data)                                               │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │   Pandas    │   NumPy     │   JSON      │  外部API    │  │
│  │  数据处理    │   数值计算   │  数据格式    │   数据源     │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
├─────────────────────────────────────────────────────────────┤
│  AI层 (Intelligence)                                         │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐  │
│  │   OpenAI    │   Qwen模型  │    MCP      │  自然语言   │  │
│  │    API      │   AI推理    │   工具调用   │    处理     │  │
│  └─────────────┴─────────────┴─────────────┴─────────────┘  │
└─────────────────────────────────────────────────────────────┘
```


#### 启动方式
```bash
# 启动完整服务（后端+前端）
cd backend
python statrt_server.py 
cd frontend
python start_frontend.py

# 仅启动后端
python statrt_server.py 
# 仅启动前端
python start_frontend.py



## 📊 报告生成功能详解

### 🔄 执行流程

当用户点击"生成报告"按钮时，系统按以下步骤执行：

#### 1. **前端触发** (`frontend/report.js`)
```javascript
// 用户点击生成报告按钮
generateReport() {
    // 发送POST请求到后端API
    fetch('/api/report/generate', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ start_date, end_date })
    })
}
```

#### 2. **后端API接收** (`backend/apis/report_api.py`)
```python
@app.post("/api/report/generate")
async def generate_report(request: ReportRequest, background_tasks: BackgroundTasks):
    # 创建报告任务
    report_id = str(uuid.uuid4())
    background_tasks.add_task(generate_report_task, report_id, request.start_date, request.end_date)
    return ReportResponse(report_id=report_id, status="processing")
```

#### 3. **报告生成器执行** (`backend/reports/report_generator.py`)

**核心方法：`generate_report()`**

```python
async def generate_report(self, start_date: str, end_date: str) -> ReportData:
    # 步骤1: 计算对比日期范围
    prev_start, prev_end, year_ago_start, year_ago_end = self._calculate_date_ranges(start_date, end_date)
    
    # 步骤2: 顺序获取多期数据（避免并发限制）
    current_period_data = await self._make_request("trade/companyPayTypeTurnover", "POST", {"beginTime": start_date, "endTime": end_date})
    previous_period_data = await self._make_request("trade/companyPayTypeTurnover", "POST", {"beginTime": prev_start, "endTime": prev_end})
    year_ago_data = await self._make_request("trade/companyPayTypeTurnover", "POST", {"beginTime": year_ago_start, "endTime": year_ago_end})
    traffic_flow_data = await self._make_request("traffic/companyPayTypeTrafficFlow", "POST", {"beginTime": start_date, "endTime": end_date})
    year_ago_traffic_data = await self._make_request("traffic/companyPayTypeTrafficFlow", "POST", {"beginTime": year_ago_start, "endTime": year_ago_end})
    vehicle_data = await self._make_request("traffic/companyVehTypeTrafficFlow", "POST", {"beginTime": start_date, "endTime": end_date})
    
    # 步骤3: 合并数据
    current_period_data['trafficFlow'] = traffic_flow_data
    current_period_data['vehicleData'] = vehicle_data
    year_ago_data['trafficFlow'] = year_ago_traffic_data
    
    # 步骤4: 生成图表数据
    charts_data = self._generate_charts_data(current_period_data, previous_period_data, year_ago_data, traffic_flow_data)
    
    # 步骤5: 生成AI分析文字
    analysis_text = self._generate_analysis_text(current_period_data, previous_period_data, year_ago_data)
    
    return ReportData(...)
```

### 📡 数据来源详解

#### **外部API接口** (http://116.182.4.67:8088/gssfApi2/v1/api)

系统调用以下6个API接口获取数据：

1. **`trade/companyPayTypeTurnover`** (当前期间)
   - 获取：分公司支付类型交易额数据
   - 用途：主要分析数据，包含ETC、现金、移动支付等

2. **`trade/companyPayTypeTurnover`** (上期对比)
   - 获取：上期分公司支付类型交易额数据
   - 用途：环比分析，计算短期趋势

3. **`trade/companyPayTypeTurnover`** (去年同期)
   - 获取：去年同期分公司支付类型交易额数据
   - 用途：同比分析，计算长期趋势

4. **`traffic/companyPayTypeTrafficFlow`** (当前期间)
   - 获取：分公司支付类型车流量数据
   - 用途：车流量分析，生成车流量图表

5. **`traffic/companyPayTypeTrafficFlow`** (去年同期)
   - 获取：去年同期分公司支付类型车流量数据
   - 用途：车流量同比分析

6. **`traffic/companyVehTypeTrafficFlow`** (当前期间)
   - 获取：分公司车型车流量数据
   - 用途：车型分析，丰富报告内容

#### **数据流向**

```
外部API → 报告生成器 → 数据处理 → 图表生成 + AI分析 → 前端展示
```

### 📈 图表生成逻辑

系统生成6种不同类型的图表：

1. **分公司交易额TOP10** - 基于 `current_period_data['data']['payTypeTurnoverList']`
2. **分公司车流量TOP10** - 基于 `traffic_flow_data['data']['payTypeTrafficFlowList']`
3. **车流量同比增长分析** - 基于当前和去年同期车流量数据对比
4. **ETC车流量与通行费百分比折线图** - 基于交易额和车流量数据交叉分析
5. **各分公司实收通行费分支付类型占比** - 基于 `current_period_data['data']['payTypeTurnoverList']`
6. **实收通行费同比增长分析** - 基于当前和去年同期交易额数据对比

### 🤖 AI分析生成逻辑

**核心方法：`_generate_analysis_text()`**

```python
def _generate_analysis_text(self, current_data: Dict, previous_data: Dict, year_ago_data: Dict) -> str:
    # 调用详细分析生成器
    detailed_analysis = self._generate_detailed_analysis(current_data, previous_data, year_ago_data)
    
    # 转换为文本格式
    return self._convert_analysis_to_text(detailed_analysis)
```

**详细分析包含：**

1. **执行摘要** (`_generate_executive_summary`)
   - 业务洞察、支付生态系统分析、运营效率评估

2. **关键指标** (`_generate_key_metrics`)
   - 核心财务指标、运营效率指标、组织效率指标、技术渗透指标、市场表现指标

3. **绩效分析** (`_generate_performance_analysis`)
   - 基础绩效排名、绩效分层、详细环比同比分析、效率对标、区域竞争力分析

4. **趋势分析** (`_generate_trend_analysis`)
   - 整体趋势分析、结构性趋势分析、支付方式趋势分析、趋势预测、季节性分析

5. **支付分析** (`_generate_payment_analysis`)
   - 基础支付结构、支付生态系统健康度、用户行为洞察、技术渗透率、支付效率、市场竞争力、发展趋势预测

6. **洞察建议** (`_generate_insights`, `_generate_recommendations`, `_generate_risk_alerts`)
   - 业务洞察、改进建议、风险预警

### 🔄 数据复用策略

**报告生成器采用智能数据复用策略：**

1. **单次API调用，多用途使用**
   - 同一份 `current_period_data` 用于：图表生成 + AI分析 + 数据概览
   - 避免重复调用API，提高效率

2. **数据合并优化**
   ```python
   # 将车流量数据合并到主数据结构中
   current_period_data['trafficFlow'] = traffic_flow_data
   current_period_data['vehicleData'] = vehicle_data
   ```

3. **图表和分析共享数据源**
   - 图表生成和分析文字使用相同的数据源
   - 确保数据一致性

### ⚡ 性能优化

1. **顺序请求** - 避免并发限制
2. **重试机制** - 处理网络波动
3. **数据缓存** - 避免重复计算
4. **后台处理** - 不阻塞用户界面

## 💬 智能对话功能

### MCP工具集成

系统集成了12个MCP工具，支持自然语言查询：

1. `get_realtime_transaction` - 获取集团实时交易数据
2. `get_company_payment_transaction` - 获取分公司支付交易数据
3. `get_company_vehicle_transaction` - 获取分公司车型交易数据
4. `get_station_payment_transaction` - 获取收费站支付交易数据
5. `get_station_vehicle_transaction` - 获取收费站车型交易数据
6. `get_realtime_traffic_flow` - 获取实时交通流量数据
7. `get_company_payment_traffic_flow` - 获取分公司支付类型交通流量
8. `get_company_vehicle_traffic_flow` - 获取分公司车型交通流量
9. `get_station_payment_traffic_flow` - 获取收费站支付类型交通流量
10. `get_station_vehicle_traffic_flow` - 获取收费站车型交通流量
11. `get_all_company_ids` - 获取所有分公司ID
12. `generate_report` - 在对话中生成数据报告

### 对话输出优化

- **隐藏工具调用详情** - 只显示AI最终回答
- **智能数据格式化** - 自动美化JSON数据展示
- **实时状态反馈** - 显示处理进度

## 🚀 快速开始

### 环境要求
- Python 3.8+
- 现代浏览器

### 安装依赖
```bash
pip install -r requirements.txt
```

### 配置环境变量
复制环境变量模板：
```bash
cp backend/.env.example backend/.env
```

编辑 `backend/.env` 文件，配置必要参数：
```env
# OpenAI客户端配置(用于AI推理)
QWEN_MODEL=qwen2.5-32b-instruct
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
QWEN_API_KEY=your_api_key_here

# FastAPI服务配置
FASTAPI_HOST=0.0.0.0
FASTAPI_PORT=8000
FASTAPI_RELOAD=false

# MCP客户端配置
MAX_TOOL_CALLS=6
MAX_TOOL_RESULT_CHARS=6000

# 日志配置
LOG_LEVEL=INFO
```

### 启动服务

#### 智能启动（推荐）
```bash
# 从项目根目录启动（自动查找配置文件）
python backend/start_server.py

# 从backend目录启动
cd backend
python start_server.py
```

#### 高级启动选项
```bash
# 开发模式（热重载）
python backend/start_server.py --reload --log-level debug

# 生产模式（多进程）
python backend/start_server.py --workers 4 --log-level warning

# 自定义配置
python backend/start_server.py --host 127.0.0.1 --port 8080 --env-file custom.env

# 查看所有选项
python backend/start_server.py --help
```
```

### 访问系统
- 前端界面：http://localhost:8080
- 后端API：http://localhost:8000
- API文档：http://localhost:8000/docs

## 📋 API接口

### 报告生成接口
- `POST /api/report/generate` - 生成报告
- `GET /api/report/status/{report_id}` - 查询报告状态
- `GET /api/report/result/{report_id}` - 获取报告结果
- `GET /api/report/list` - 列出所有报告
- `DELETE /api/report/{report_id}` - 删除报告

### 聊天接口
- `POST /v1/chat/completions` - OpenAI兼容的聊天接口

## 🎨 前端功能

### 报告管理
- 📊 智能报告生成
- 📈 多维度图表展示
- 📥 报告下载（Word格式）
- 🔍 图表全屏查看
- 📋 报告历史管理

### 智能对话
- 💬 自然语言查询
- 🤖 AI智能回答
- 📊 数据可视化展示
- 📝 对话历史管理

## 🔧 技术栈

### 后端
- **FastAPI** - 高性能Web框架
- **MCP** - Model Context Protocol
- **Matplotlib** - 图表生成
- **Pandas/NumPy** - 数据处理
- **HTTPX** - 异步HTTP客户端

### 前端
- **HTML5/CSS3** - 现代化界面
- **JavaScript** - 交互逻辑
- **Chart.js** - 图表展示（已替换为静态PNG）

### 数据源
- **外部API** - 收费系统数据接口
- **异步处理** - 后台任务处理

## 📝 更新日志

### v2.1.0 (最新) - 架构重构版
- 🏗️ **重大架构重构**: 模块化设计，代码组织更清晰
- 📁 **新增模块结构**: apis/ clients/ reports/ utils/ config/
- 🚀 **智能启动脚本**: 自动查找配置文件，支持多种启动模式
- 🔧 **路径自适应**: 支持从项目根目录或backend目录启动
- 📋 **完善文档**: 更新架构说明和使用指南
- ✅ **向后兼容**: 保持所有原有功能不变

### v2.0.0
- ✅ 修复报告生成语法错误
- ✅ 优化智能对话输出格式
- ✅ 增强AI分析详细程度
- ✅ 完善图表生成功能
- ✅ 改进前端用户体验

### v1.0.0
- 🎉 初始版本发布
- 📊 基础报告生成功能
- 💬 智能对话功能
- 📈 图表可视化

## 🔧 故障排除

### 常见问题

#### 1. 环境变量文件找不到
```
错误: 环境变量文件不存在: .env
```
**解决方案:**
```bash
# 复制环境变量模板
cp backend/.env.example backend/.env
# 编辑配置文件
nano backend/.env
```

#### 2. MCP服务器脚本不存在
```
错误: MCP服务器脚本不存在: backend/apis/platformbilling_mcp.py
```
**解决方案:**
检查文件路径是否正确，确保重构后的文件结构完整。

#### 3. 缺少必要环境变量
```
错误: 缺少必要的环境变量: QWEN_API_KEY, QWEN_BASE_URL, QWEN_MODEL
```
**解决方案:**
在 `backend/.env` 文件中配置以下变量：
```env
QWEN_API_KEY=your_api_key_here
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
QWEN_MODEL=qwen2.5-32b-instruct
```

#### 4. 端口被占用
```
ERROR: [Errno 48] Address already in use
```
**解决方案:**
```bash
# 使用不同端口启动
python backend/start_server.py --port 8001

# 或者杀死占用端口的进程
lsof -ti:8000 | xargs kill -9
```

#### 5. 模块导入错误
```
ModuleNotFoundError: No module named 'apis'
```
**解决方案:**
确保从正确的目录启动服务，推荐使用：
```bash
# 从项目根目录启动
python backend/start_server.py
```

### 调试模式

启用详细日志进行调试：
```bash
python backend/start_server.py --reload --log-level debug
```

### 获取帮助

查看所有可用选项：
```bash
python backend/start_server.py --help
```

## 🤝 贡献指南

欢迎提交Issue和Pull Request来改进项目！

### 开发环境设置
```bash
# 克隆项目
git clone <repository-url>
cd project

# 安装依赖
pip install -r requirements.txt

# 复制配置文件
cp backend/.env.example backend/.env

# 启动开发服务器
python backend/start_server.py --reload --log-level debug
```

## 📄 许可证

MIT License
```
