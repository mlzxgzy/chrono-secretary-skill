# CONTRIBUTING — 提交与发布流程（AI 必读）

本文件是本仓库**唯一的提交/发布流程权威**。任何 AI 或人要改本仓库、发版、提交、push，操作前先读本文件并逐步照做。不要凭记忆或上游习惯另创流程。

二开口径（合同按需、计量单位默认人天等）见 [FORK.md](FORK.md)；口径问题一律以 FORK.md 为准。

## 0. 路径约定

| 对象 | 路径 |
|---|---|
| 仓库 | `/root/workspace/chrono-secretary-skill` |
| 包体（所有改动落点） | 仓库内 `ChronoPM-Project/` |
| 已安装技能（只读同步目标） | `/root/.hermes/skills/productivity/chrono-pm-project` |
| 发布审计 | `governance-shared/scripts/audit_release.py` |

规则文件 = `ChronoPM-Project/references/NN-*.md`；模板 = `assets/templates/`；init 脚本 = `scripts/chronopm_init/`；回归套件 = `tests/regression-suite.md`。

**铁律：一切改动先落仓库，再同步到已安装技能目录。禁止直接改已安装技能目录**——它没有版本控制，直接改会在下次同步时被覆盖。

## 1. 标准流程（顺序执行，一步不能跳）

1. **改代码/规则**：按需修改 `ChronoPM-Project/` 内文件。改行为时同步检查回归用例（见 §3）。
2. **版本 bump**：语义化版本。改 `ChronoPM-Project/scripts/_version.py` 的 `SKILL_VERSION`，然后：
   ```bash
   cd /root/workspace/chrono-secretary-skill
   python3 ChronoPM-Project/scripts/sync_version.py
   ```
   该脚本自动同步 VERSION / SKILL.md / skill.json / SKILL_BLUEPRINT §1 / README.md / README.en.md 共六触点。**不要手改这六处的版本号。**
3. **CHANGELOG**：`ChronoPM-Project/CHANGELOG.md` 顶部（标题行之后）插入新版本段。二开变更标注「二开」。插入时若替换锚点含上一版本标题行（`## x.y.z — 日期`），必须在替换文本里原样保留该标题，改完回读文件头确认。
4. **BLUEPRINT 演进表**：`ChronoPM-Project/SKILL_BLUEPRINT.md` §11.3 演进表追加一行 `| <版本> | 摘要 | 依据 |`。audit 第 3 项强校验该行存在。
5. **基线快照**（audit 第 9 项，缺了禁止发布）：
   ```bash
   mkdir -p governance-shared/baselines/<版本>
   cp -r ChronoPM-Project governance-shared/baselines/<版本>/
   find governance-shared/baselines/<版本> -name "__pycache__" -type d -exec rm -rf {} +
   ```
6. **审计闸**（必须 17/17 全 PASS）：
   ```bash
   python3 governance-shared/scripts/audit_release.py
   ```
   任何一项 FAIL 都禁止 commit 为新版本，先修再发。
7. **测试**（本机无 pytest，用技能自带 runner）：
   ```bash
   python3 /root/.hermes/skills/software-development/chrono-pm-fork-development/scripts/run_tests.py
   ```
   只报**新失败**才算过；已知既有失败见 §3。
8. **提交**：
   ```bash
   cd /root/workspace/chrono-secretary-skill
   git add -A
   git commit -m "<版本>（二开）：<一句话摘要>"
   ```
9. **同步已安装技能目录**（本机无 rsync，逐项 cp）：
   ```bash
   SKILL=/root/.hermes/skills/productivity/chrono-pm-project
   cd /root/workspace/chrono-secretary-skill/ChronoPM-Project
   for item in *; do rm -rf "$SKILL/$item" && cp -a "$item" "$SKILL/"; done
   diff -rq "$SKILL" .   # 必须无差异才算同步完成
   ```
10. **push**：`git push origin master`。remote 已配置为 SSH（`git@github.com:mlzxgzy/chrono-secretary-skill.git`），用本机 `~/.ssh/id_ed25519` key 认证，直接推即可。若 push 因网络/认证失败，提交本身不算失败，修好凭据再推，不要把 push 失败当阻塞、更不要为此回滚提交。

## 2. 审计红线（改文档/规则前必知）

| 红线 | 说明 |
|---|---|
| 回归用例数是硬闸 | audit 第 4/5 项强校验 `tests/regression-suite.md` 统计表合计 == README.md + README.en.md 共 6 处用例数 == Case ID 去重数。**改行为时原地改既有用例的期望列，不要增删用例行**；确实要增删时，必须同步改统计表合计与两份 README 的 6 处数字 |
| 规则/模板计数 | audit 第 12 项校验 `references/*.md` 数（24）与 `assets/templates/` 模板数（40）== README×2 + BLUEPRINT 中的计数。增删规则文件或模板时三处同步 |
| 新增引用必须真实存在 | SKILL.md 与 `references/*.md` 里出现的 `references/NN-*.md`、`assets/templates/*.md` 文件名必须真实存在（audit 第 7/8 项幽灵引用检查）。仓库根新增自由文档（如 FORK.md / CONTRIBUTING.md）不受此限 |
| README 目录树 | README×2 顶部目录树中标注的顶层目录必须真实存在（audit 第 10 项） |
| 演进表行 | SKILL_BLUEPRINT §11.3 必须有当前版本行（audit 第 3 项） |

## 3. 测试与已知问题

- **既有失败 7 例**：`tests/test_query_locate_v400.py` 在未改动的上游 HEAD 上即失败，与本地改动无关。判定「是不是我改挂的」用 `git archive HEAD` 解到临时目录跑同样测试对比；不要在自己 diff 里找原因，也不要当新 bug 修。
- runner 输出 `passed=N failed=0 known_failing_skipped=7` 即为通过。

## 4. 提交信息约定

- 格式：`<版本>（二开）：<一句话摘要>`，例：`4.1.2（二开）：初始化合同信息按需；人工计量单位默认人天；无合同项目巡检豁免`
- 一次逻辑变更一个 commit；版本 bump + CHANGELOG + 基线 + 审计产物属于同一次发版，一起提交
- 与上游同步（cherry-pick 等）的提交在信息里注明上游版本号

## 5. 本技能/文档自身的维护

- 本流程文档（CONTRIBUTING.md）与 Hermes 技能 `chrono-pm-fork-development` 承载同一套流程。**流程变更时两处同步改**：先改本文件，再 `skill_manage(action='patch')` 更新技能，保持一致。
- 二开口径变更（FORK.md）时，同步更新技能内的「用户既定二开口径」段。
