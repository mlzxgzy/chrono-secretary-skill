# FORK 说明（ChronoPM 二开）

本仓库是 [qiusuo0226/chrono-pm-skill](https://github.com/qiusuo0226/chrono-pm-skill) 的 fork，由 mlzxgzy 持续二开。

- fork 起点：上游 v4.1.1（commit `0584b64`）
- 本 fork 地址：https://github.com/mlzxgzy/chrono-secretary-skill
- 版本线：自 v4.1.2 起独立演进，与上游版本号不再对齐；许可证沿用 MIT（见 `LICENSE.txt`，上游版权归属仇索）

## 本 fork 与上游的核心差异（二开口径）

写规则、改模板、调向导时必须遵循以下口径，**不要回退上游口径**：

1. **合同信息一律按需**：用户没提合同时，向导/模板/巡检/自查都不得询问、提醒合同字段；无合同项目的 `requirements/contract-register.md` 空或缺失合法，巡检合同层整层豁免。上游把合同层设为初始化必填（To G 政企场景），本 fork 的持有者也会拿它记纯个人项目，故放宽。
2. **人工计量单位默认「人天」**：未约定时直接登记「人天」，不追问、不阻塞向导；用户指定人时/人月/其他则按其值登记。上游的「单位中立、缺失不放行」在本 fork 废止。
3. **直录模式（本用户常设）**：用户口述建档信息时**不跑六步逐题提问**——把原话能推出的字段（名称/起止日期/阶段/范围/约束/模块数）一次写进 brief、context、需求册与里程碑；用户分多条消息陆续补充属常态，累积后**同轮一次落盘**。必填缺口最多一轮 ≤3 题，也可不问：收尾回复末尾留**一行**提醒。收尾格式=改动文件清单 + 登记摘要 + 一行待补充，禁止「请确认以上信息是否正确」式问卷收尾。对外简报/拍板输出走**极简三段式**（今日优先≤3 条 / 待你拍板 / 一句提醒），不铺长表长段。规则落点见 `references/18-init-wizard-rules.md` §0、`references/06-file-rules.md` §5.4（无 Change Log 节的模板不新增节）。

总方向：凡是上游为 To G / To B 政企场景设的**必填闸**（合同额、单位中立、缺失阻塞等），二开方向是**放宽而非加强**。

## 与上游的同步策略

- 上游有新版本时，**不自动 merge**。先看上游 CHANGELOG 摘要有无需要的功能；需要时以 cherry-pick / 手工搬运方式单点引入，合入后必须重新过本仓库审计（`governance-shared/scripts/audit_release.py`）并核对二开口径未被上游文案覆盖。
- 上游改进动过的地方若与本 fork 差异冲突，以本 fork 口径为准，冲突解法记入 CHANGELOG。

## 版本记录

本 fork 的每次发版见 [ChronoPM-Project/CHANGELOG.md](ChronoPM-Project/CHANGELOG.md) 顶部段落（标注「二开」的即本 fork 变更）。提交与发布流程见 [CONTRIBUTING.md](CONTRIBUTING.md)。
