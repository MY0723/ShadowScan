# ShadowScan 安全扫描器

> **作者：ROOT4044** | 公众号：**不秃头的安全**

***

## 📌 项目简介

ShadowScan 是一款面向安全从业人员的自动化信息泄露扫描与资产测绘工具。深度整合多平台搜索引擎，通过自动化 Dork 语法扫描发现暴露在公网的敏感文件，并自动提取身份证号、手机号、学号、邮箱、银行卡号等个人敏感信息（PII）。

支持 **Bing / FoFa / Hunter / Quake / Shodan / Censys / Zoomeye / 0.zone / DayDayMap / ThreatBook** 等主流搜索引擎与资产测绘平台。

***

## 📖 目录

- [模块一：Dork 扫描](#模块一dork扫描)
- [模块二：资产测绘](#模块二资产测绘)
- [环境要求](#环境要求)
- [快速开始](#快速开始)
- [技术栈](#技术栈)
- [免责声明](#免责声明)

***

## 模块一：Dork 扫描

自动化搜索引擎 Dork 语法扫描，发现暴露在公网的敏感文件并进行 PII 信息提取。

<img width="2535" height="1434" alt="bcec12916ccb68c3e121207cb854677a" src="https://github.com/user-attachments/assets/447ab8f0-aa6b-4e1b-88a0-907d862457a0" />
<img width="2103" height="1397" alt="image" src="https://github.com/user-attachments/assets/e377c144-0912-42f2-afa3-b974bd62eddb" />
<img width="2103" height="1397" alt="image" src="https://github.com/user-attachments/assets/1900b5f3-2c03-49fa-ab91-02822b35ec73" />
<img width="2468" height="1137" alt="image" src="https://github.com/user-attachments/assets/2a95e302-e22f-4696-9cc4-4ead433e76a9" />

显示浏览器(扫描时需手动认证下)
<img width="2523" height="852" alt="image" src="https://github.com/user-attachments/assets/ee3fb987-dcb5-4bf7-8361-9d9c9628562f" />
<img width="2051" height="789" alt="image" src="https://github.com/user-attachments/assets/f1caea8f-945b-47f9-913b-c32d0e9694f2" />
Bing自动访问下载
<img width="2454" height="1494" alt="image" src="https://github.com/user-attachments/assets/b69f7732-be94-4983-b74e-c2645c05331e" />


### 1.1 多引擎 Dork 搜索

支持 Bing、FoFa、Hunter 三大搜索引擎，内置 8 大扫描策略类别，按优先级分三批执行：

**P1 高优先级（首批执行）：**

| 策略类别    | 涵盖子类别                                             | 目标文件                   |
| ------- | ------------------------------------------------- | ---------------------- |
| 敏感文件    | 通讯录名单、客户信息、员工信息、学籍成绩、合同协议、招标采购等                   | xlsx / doc / pdf       |
| 系统入口    | 通用后台、OA系统、邮箱系统、VPN入口、财务/人事/CRM/ERP系统等             | —                      |
| 配置文件    | 环境配置、数据库配置、API文档、Spring Boot Actuator、运维配置、Web配置等 | —                      |
| 凭证Token | API密钥、密码文件、证书密钥、SSH密钥、数据库连接信息等                    | json / txt / env       |
| 敏感数据类型  | 身份证、手机号、学号、邮箱、银行卡、工号、IP地址、社保号等                    | xlsx / doc / pdf / txt |

**P2 中优先级（第二批执行）：**

| 策略类别 | 涵盖子类别                               | 目标文件       |
| ---- | ----------------------------------- | ---------- |
| 运维设施 | 代码仓库、监控平台、容器平台、云存储、数据库管理、日志系统、运维工具等 | —          |
| 行业数据 | 教育行业、政府机构、医疗机构、金融行业等                | pdf / xlsx |

**P3 低优先级（第三批执行）：**

| 策略类别 | 涵盖子类别                              | 目标文件            |
| ---- | ---------------------------------- | --------------- |
| 漏洞特征 | SQL注入、文件上传、Webshell、XSS、敏感路径、备份文件等 | bak / zip / rar |

**智能搜索控制：**

- 每批自动将 2 个关键词与 1 种文件类型组合，严格限制 URL 长度在 800 字符以内，杜绝 Header Field Too Long (HTTP 400) 错误
- 每次搜索前自动清理浏览器 Cookie，防止请求头累积过长
- 支持单域名或多域名批量输入，一行一个目标
- 扫描线程数可调，代理可配

### 1.2 自定义关键词管理

- 内置 50+ 子类别，数百个预设搜索关键词，覆盖通讯录、学籍成绩、教务系统、研究生、内部文件等多种场景
- 可视化关键词管理面板，支持新增、编辑、删除、启用/禁用
- 支持为每个子类别单独配置文件类型和 URL 路径模式
- 修改即时生效，无需重启

### 1.3 敏感文件下载与内容解析

- 搜索结果中的潜在敏感文档自动下载（支持 xlsx、doc、pdf、txt 等格式）
- 内置 PDF 文本解析引擎（Apache PDFBox），提取 PDF 文档全文内容
- 内置 Office 文档解析能力，读取 Excel、Word 文件内容
- 根据文件大小自动过滤，避免超大文件阻塞扫描流程

### 1.4 PII 敏感信息检测

自动检测并提取 6 类个人敏感信息：

| 类型    | 检测能力                                    |
| ----- | --------------------------------------- |
| 身份证号  | 15 位和 18 位公民身份号码，含校验位验证                 |
| 学号    | 三层检测：标签前缀匹配 + 年份数字模式 + 教育上下文激活，覆盖多种学号格式 |
| 手机号   | 中国大陆 11 位手机号码                           |
| 邮箱地址  | 标准邮箱格式识别                                |
| 银行卡号  | 16 / 19 位银行卡号                           |
| IP 地址 | 内网 IP 与公网 IPv4 地址                       |

### 1.5 扫描结果展示与管理

扫描结果以表格形式实时呈现，包含以下列：

- **目标链接**：蓝色链接样式，左键单击浏览器打开，右键菜单支持复制链接、打开文件目录
- **标题**：资源标题或文件名
- **分类**：归属的策略类别
- **风险**：S/H/M/L/I 五级风险标注，S 级红色、H 级橙色高亮
- **样本数据**：提取到的 PII 样本内容
- **状态**：文件下载和分析处理状态

交互能力：

- 表格所有列支持右键复制内容
- 选中行后右侧"详情预览"面板展示完整文本内容
- 实时日志面板记录全流程操作，支持关键词搜索过滤日志
- 分类统计面板实时展示各类别发现数量

### 1.6 可视化 Excel 报告

自动生成包含 4 个工作表的多维度 Excel 报告：

| 工作表    | 内容说明                                                     |
| ------ | -------------------------------------------------------- |
| 扫描结果   | 全部发现明细：目标链接、标题、分类、子分类、风险等级、来源引擎、匹配关键词、样本数据、PII 统计数量、发现时间 |
| 统计概览   | 风险等级分布、PII 各类型统计总数、高危域名 Top 20 排行                        |
| 汇总详情   | 按 PII 类型分类汇总：风险评分、风险类型、域名、涉及数量、关键详情样本、目标链接、搜索语法、扫描时间     |
| PII 统计 | 身份证 / 学号 / 手机号 / 邮箱 / 银行卡 / IP 地址 各类型泄露数量汇总              |

***

## 模块二：资产测绘

集成 8+ 主流资产测绘平台，提供语法速查、一键查询和暴露面梳理能力。

### 2.1 多平台资产查询

支持以下平台的独立查询，每个平台独立 Tab 页：

| 平台         | 简介          |
| ---------- | ----------- |
| FOFA       | 网络空间资产搜索引擎  |
| Hunter     | 奇安信网络空间测绘平台 |
| Quake      | 360 网络空间测绘  |
| Shodan     | 全球联网设备搜索引擎  |
| Censys     | 互联网资产情报平台   |
| Zoomeye    | 知道创宇网络空间雷达  |
| 0.zone     | 零零信安资产测绘平台  |
| DayDayMap  | 零零信安资产测绘平台  |
| ThreatBook | 微步在线威胁情报平台  |

每个平台查询页提供：

- 自定义语法查询输入框，灵活搜索目标资产
- 查询结果以表格呈现，列依据平台自动适配
- 查询条数可自定义
- 查询结果支持导出 Excel
- 一键导入按钮，将查询结果直接导入"暴露面梳理"模块进行聚合分析

### 2.2 语法速查参考

内置 FOFA、Shodan、Hunter、Quake、Zoomeye、0.zone、DayDayMap、Censys、ThreatBook 八大平台核心语法速查，收录 200+ 条常用搜索语法：

- 域名搜索语法
- IP 搜索语法
- 端口过滤语法
- 协议过滤语法
- 证书搜索语法
- Web 指纹识别语法
- 中间件/框架识别语法

按平台分类 Tab 页切换查阅，持续更新。
<img width="2103" height="1397" alt="image" src="https://github.com/user-attachments/assets/b40413a1-4e21-4fb0-a491-caf6ec4e4c44" />

### 2.3 暴露面梳理

将 FOFA、Hunter、Shodan 等多平台查询结果汇总到统一视图，实现跨平台资产、漏洞、情报的关联分析：

- **资产维度**：IP、开放端口、Web 服务 URL、服务指纹信息
- **漏洞维度**：关联 CVE 漏洞编号与漏洞情报
- **邮箱维度**：邮箱账号泄露关联
- **代码泄露维度**：GitHub 源码泄露、配置文件泄露信息
- **证书维度**：SSL/TLS 证书信息关联
- **DNS 维度**：DNS 记录、ICP 备案、子域名信息
- **GitHub 维度**：GitHub 仓库泄露监控数据
- **聚合维度**：子域名、ICP 备案、Whois 等信息综合展示

核心价值：将分散在各平台的数据聚合到一个视图，一键梳理目标全量暴露面，减少跨平台切换成本。
<img width="2103" height="1397" alt="image" src="https://github.com/user-attachments/assets/af3758e7-d0c4-4d51-9beb-5f9ec39b2068" />
<img width="2292" height="1269" alt="2d6c32405303898aa112beb06117972f" src="https://github.com/user-attachments/assets/8bd9c768-d4d6-4dad-abda-826c2bf6ec44" />
<img width="1098" height="294" alt="image" src="https://github.com/user-attachments/assets/f7be9915-defd-4453-bd84-e974d83e505b" />
<img width="870" height="453" alt="image" src="https://github.com/user-attachments/assets/27692ba9-4419-4265-9b70-78f3de87ebea" />

#### 2.3.1 fofa
<img width="2103" height="1397" alt="121e39599ddc4220a0b8f77c73c5cb8c" src="https://github.com/user-attachments/assets/5f99db9b-ce5f-4a18-8c5c-1a2aeb816657" />

#### 2.3.2 鹰图/Hunter
<img width="2103" height="1397" alt="7bd30f270cf08b334549a2f7c34f2ac8" src="https://github.com/user-attachments/assets/9128556a-985a-4c75-80a8-417cd6f9af9a" />

#### 2.3.3 360Quake
<img width="2103" height="1397" alt="image" src="https://github.com/user-attachments/assets/6ad92044-2d9d-4d7c-937c-d8918a5eaef7" />

#### 2.3.4 Shodan
<img width="2103" height="1397" alt="40b289bed8c7257e68fc5235d002f3d2" src="https://github.com/user-attachments/assets/8bf57965-a23a-4dcb-a021-5221bbd790a5" />

#### 2.3.5 Censys
<img width="2103" height="1397" alt="d8cac1deda6e4229fe1961a65baf1842" src="https://github.com/user-attachments/assets/1b7ca0e7-4999-4c41-a189-32d1e418cfea" />

#### 2.3.6 ZoomEye
<img width="2103" height="1397" alt="a7d0131ed0d581ca0c20e4636c06f9c4" src="https://github.com/user-attachments/assets/8b990c2e-b413-4389-8525-cee797e4cefc" />

#### 2.3.7 0.zone多功能
<img width="2103" height="1397" alt="00fdc68583069688821bbfec5afa830c" src="https://github.com/user-attachments/assets/30704ee0-610a-4ccc-b677-526bc40b7fb7" />
<img width="2103" height="1397" alt="df50815854f97e810c81fd00d155b354" src="https://github.com/user-attachments/assets/770d3cb9-1819-4726-9d2a-920a59f433e5" />
<img width="2103" height="1397" alt="cbc0587fa8fcec208b7962e6e2d8a998" src="https://github.com/user-attachments/assets/2a94db9e-b487-4d34-83f4-996a262f03ef" />
<img width="2103" height="1397" alt="e800e3a8ef9fb12530798dd7aa0c103c" src="https://github.com/user-attachments/assets/6b9205f3-63bd-4fdc-8b0f-f1cae0076b8a" />
<img width="2103" height="1397" alt="dc267e993dee23a648342acf3d5ccde2" src="https://github.com/user-attachments/assets/8bf75705-b1f7-4b5e-9faa-3a2996aee70b" />

#### 2.3.8 DayDayMap
<img width="2103" height="1397" alt="6874d60131c81aef6c3bfee3baebc0be" src="https://github.com/user-attachments/assets/fc791d4b-719d-4130-8af6-2cb3db5c6ef6" />


### 2.4 API 配置管理

集中管理所有平台的 API 密钥：

| 平台         | 配置项             |
| ---------- | --------------- |
| FOFA       | Email + API Key |
| Hunter     | API Key         |
| Quake      | API Key         |
| Shodan     | API Key         |
| Censys     | Org ID + Secret |
| Zoomeye    | API Key         |
| 0.zone     | API Key         |
| DayDayMap  | API Key         |
| ThreatBook | API Key         |

支持代理设置（HTTP / SOCKS5）。

<img width="2559" height="1527" alt="86d53eabcd9d7323d6abed9ea6710876" src="https://github.com/user-attachments/assets/09c2e60c-9893-46e0-b160-68bf6a49c3fb" />

***

## 📁 项目文件结构

```
ShadowScan/
├── ShadowScan.jar              # 可执行 JAR 主程序
├── config.json                 # API Key 配置文件
├── shadowscan.db               # 本地 SQLite 数据库
├── start.bat                   # Windows 一键启动脚本（智能 JDK 检测）
├── custom-keywords.json        # 自定义 Dork 关键词（可选）
└── README.md                   # 本说明文档
```

***

## 📋 环境要求

| 依赖   | 版本要求                          |
| ---- | ----------------------------- |
| JDK  | 17+（推荐 JDK 17 LTS）            |
| 操作系统 | Windows 10 / 11（已测试）          |
| 内存   | 推荐 4GB+（最低 2GB）               |
| 网络   | 需能访问 Bing / FOFA / Hunter 等平台 |

> Linux / macOS 需自行配置 JavaFX 运行时依赖。

***

## 🚀 快速开始

### 方式一：双击启动

1. 确保已安装 JDK 17+（运行 `java -version` 检查版本）
2. 双击运行 `start.bat`（脚本自动检测并匹配 JDK 版本）

### 方式二：命令行启动

```bash
java -jar ShadowScan.jar
```

### 使用步骤

1. **导入目标**：菜单栏 `文件 → 导入目标`，或在"目标输入"框中填写域名（一行一个）
2. **选择策略**：勾选需要启用的扫描类别（首次建议全选）
3. **开始扫描**：点击 `▶ 开始扫描`，等待 Dork 搜索和文件分析
4. **查看结果**：点击表格中的行，右侧"详情预览"展示完整内容
5. **导出报告**：点击 `📊 导出报告` 生成 Excel 报告

### 使用资产测绘

1. 切换到 `🗺️ 资产测绘` Tab
2. 选择目标平台（FOFA / Hunter 等）
3. 输入查询语法（可参考"语法"Tab 速查）
4. 点击查询，查看资产结果

***

## 🛠️ 技术栈

| 技术               | 用途              |
| ---------------- | --------------- |
| Java 17 + JavaFX | 桌面 GUI 框架       |
| Playwright       | 浏览器自动化驱动搜索引擎    |
| Apache POI       | Excel 文件读写与报告导出 |
| Apache PDFBox    | PDF 文件文本内容解析    |
| SQLite           | 扫描结果持久化存储       |
| Maven            | 项目构建与依赖管理       |

***

## 👤 作者

- **ROOT4044**
- 公众号：**不秃头的安全**

***

## ⚠️ 免责声明

1. 本工具仅供授权的安全测试、渗透测试和教育研究使用
2. 使用者须遵守所在国家/地区的法律法规，确保在合法授权前提下操作
3. 严禁利用本工具进行任何非法入侵、未授权数据获取等违法行为
4. 作者对因使用者滥用本工具所导致的一切后果不承担任何法律责任

***

## 📝 更新日志

### v1.0.0

- 模块一：多引擎 Dork 扫描（Bing / FoFa / Hunter），8 大策略类别、50+ 子类别、数百关键词
- 敏感文件自动下载与 PDF / Office 文本内容解析
- PII 六类检测（身份证 / 学号 / 手机号 / 邮箱 / 银行卡 / IP），学号三层检测策略
- Excel 四工作表报告（扫描结果 / 统计概览 / 汇总详情 / PII 统计）
- 智能 URL 长度控制 + Cookie 自动清理，杜绝 400 错误
- 结果表格全列右键复制，目标链接左键浏览器打开
- 模块二：8+ 资产测绘平台集成，语法速查 200+ 条
- 暴露面梳理：多源数据聚合分析（资产 / 漏洞 / 邮箱 / 代码泄露 / 证书 / DNS / GitHub / 聚合）
- 代理设置、自定义关键词管理

## 📝致谢

感谢LeakDetector作者提供思路，项目地址：https://github.com/cbbzx12/LeakDetector，大家同样可以支持下。

## Star History

<a href="https://www.star-history.com/?repos=MY0723%2FShadowScan&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=MY0723/ShadowScan&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=MY0723/ShadowScan&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=MY0723/ShadowScan&type=date&legend=top-left" />
 </picture>
</a>
