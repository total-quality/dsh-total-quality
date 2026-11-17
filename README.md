# 全质开源质量平台 · DeepSeek Harness 插件

插件包名：`dsh-total-quality`。源代码仓库：[total-quality/dsh-total-quality](https://github.com/total-quality/dsh-total-quality)。

本地运行的中小企业质量管理与改进平台。基础版本 2.0.0，本次整理版包含独立安装隔离、蓝色服务二维码及配套说明。
源代码按 MIT 许可提供，见 LICENSE.md。

## 主要功能

| 模块 | 内容 |
| --- | --- |
| 质量驾驶舱 | 运行中枢、确认与纠正、执行跟进、接收与追溯、经验与标准 |
| AI 工作台 | AI 改进工作台、质量问题库、质量改进项目、QRQC、报表中心 |
| 统计工具 | SPC 控制图、八大判异准则、Cp/Cpk/Pp/Ppk、统计检验、MSA、DOE、FMEA 等 |
| 体系文件 | 质量手册、程序文件、三层次文件 |
| 知识与团队 | 知识经验与员工成长 |
| 系统 | 插件架构、移动端、审计日志、系统设置 |
| 文件导出 | Word 报告、Excel 或 CSV 数据 |

首次启动由程序创建合成样例数据：48 条质量问题、5 个课题、2 个关键特性、600 条测量值、16 条知识经验、12 名成员和 8 场 QRQC。
这些是试用与培训数据，实际生产分析需要导入自己的业务数据。

## 安装与打开

完整步骤见 [安装说明](安装说明.md)。

在 Harness 的“添加插件”中输入 `dsh-total-quality` 安装。使用 CLI web profile 时，在已安装 DSH 与 pnpm 的终端中执行：

```sh
dsh plugin --profile web add dsh-total-quality
```

安装完成后，在当前 Harness 的左侧栏打开“全质开源质量平台”。若提示重新加载，按提示重新加载宿主。
包名用于安装；页面地址仍是 `/qip-real/`，数据目录和 `qip_real_*` 工具前缀保持不变。

将 qip-real 文件夹解压到固定位置，在目标 Harness 的会话里让智能体调用 plugin_manager / install_bundle，安装该文件夹的绝对路径。
安装后侧栏入口为“全质开源质量平台”，整页应用地址为 http://127.0.0.1:<DSH端口>/qip-real/。
包内不是独立监听端口的网页服务器，需要由 DSH 加载。

本次在 Windows、Node 24.19.0、DSH 0.2.2-rc.1 的 web profile 中安装并做基础检查。
其他环境需要核对其 DSH bundle、web 客户端和 node:sqlite 支持情况。

## 独立安装与数据

本版使用独立页面 /qip-real/、宿主工具 qip_real_*、品牌缓存 qip-real.brand。
默认数据位于 DSH_HOME/qip-real/data/；未设置 DSH_HOME 时使用用户目录下的 .dsh/qip-real/data/。

    ~/.dsh/qip-real/data/
      qip-sme.db          SQLite 业务数据库
      qip-db-sme.json     JSON 快照
      reports/           生成的报告与导出文件

可用 QIP_REAL_DATA_DIR 指定其他数据目录。与原插件并存时应使用不同目录。
插件代码与数据分开存放；升级前请备份数据，关闭 DSH 后复制整个 data 文件夹，以包含可能存在的 SQLite WAL 文件。

## 模型

通过 Harness 的 LLM 服务调用模型，复用其配置与登录态。
默认选择 deepseek-official / deepseek-flash；可用 QIP_LLM_PROVIDER、QIP_LLM_MODEL 覆盖。
无法解析默认模型时，桥接层会尝试回落 Harness 当前模型。
在“系统设置 → 模型接入”查看状态；“测试连通性”会发送实际模型请求，可能产生账号用量。
本地存储不会自动同步到 GitHub；使用 AI 时，模型请求仍会将相关输入交给 Harness 配置的模型服务。

## 16 个宿主工具

    qip_real_overview
    qip_real_spc_list_chars
    qip_real_spc_rule_check
    qip_real_spc_capability
    qip_real_spc_control_chart
    qip_real_measurements
    qip_real_issue_search
    qip_real_issue_create
    qip_real_project_list
    qip_real_project_advance
    qip_real_knowledge_search
    qip_real_risk_scan
    qip_real_benefit_calc
    qip_real_report_generate
    qip_real_data_export
    qip_real_db_status

对话示例：“使用全质独立插件列出关键特性，然后计算 CT-A01 近 30 天的 Cpk。”
先列出实际特性，再选择数据库中存在的编号。

## 服务二维码

“运行中枢”右侧采用蓝色微信二维码，颜色 #3a6ddf。
下方文字：免费服务微信：周一到周六，早8:00至晚8:00
资源位于 public/assets/wechat-support-blue.svg，由原始二维码内嵌并作显示颜色映射。

## 开发与自检

没有前端构建步骤，采用原生 JavaScript 与 ESM/CommonJS。

    node --check index.js
    node --check host.cjs
    node --check public/app.js
    node 自检.cjs

自检在临时目录建库并计算 Cpk，不使用正式数据目录，不需要联网或登录模型。
修改 public/ 页面资源后刷新；修改宿主代码后重新加载或重装 dsh-total-quality bundle。
更详细的接口与目录说明见源文件，以及 [贡献指南](CONTRIBUTING.md)。
