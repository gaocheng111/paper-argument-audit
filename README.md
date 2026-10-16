# paper-argument-audit

面向中英文学术论文的个人修改 Skill：**目标期刊学习 → 论证检查 → 学术表达 → 基础校对 → Word 批注交付**。

本项目保存可追溯的写作规则、案例和文件工具，供 Codex 按需读取；它不是模型微调，也不会自动执行30篇论文的阅读或自动判断论文质量。

**当前版本：v0.4。** 期刊学习流程已就绪；尚未建立任何指定目标期刊的30篇画像。Word 批注示例通过结构、原生 Word 及页面检查；真实论文的诊断准确率尚未测定。

## 功能

| 模块 | 内容 | 关键边界 |
|---|---|---|
| 1 目标期刊学习 | 至少30篇去重后的同类型完整论文；选题、贡献和各节叙述观察；带来源、例外与分母的期刊画像 | 已发表特征只能支持样本中的惯例，不能直接证明编辑偏好或接受概率 |
| 2 论证检查 | 问题、主张、理由、证据、根据、承认与回应；教材16章、2附录、54案例、34规则、16校准情境 | 角色按实际功能判断，不要求每段凑齐；不把关联改成因果 |
| 3 学术表达 | 64篇文章研读及设计再分析、8组深入推理设计、80项语境词语、16种推进做法、24校准情境 | 改善引言、综述与讨论的清楚程度；方法和结果服从学术规范 |
| 批注与校对 | 真实 DOCX 批注；角色定位及错误类型、原因、建议句；中文和英文基础校对 | 保留原文；科学含义、分母、范围、情态和证据强度不被润色改变 |

## 安装

下载或克隆本仓库，在仓库根目录运行：

```powershell
python -X utf8 tools/install_local.py
```

安装器会复用已有的同名安装位置；新安装默认使用 `~/.agents/skills`。此项目的既有本机安装位于 `~/.codex/skills/paper-argument-audit`，安装器检测到后会沿用它，避免重复发现。

已有版本与仓库不同，需要更新时运行：

```powershell
python -X utf8 tools/install_local.py --update
```

更新前自动备份已有同名 Skill；不会删除额外的用户文件。也可用 `--dest` 指定 Skills 父目录。安装后可在下一轮调用；若选择器未刷新，可重新启动 Codex。位置说明依据 [OpenAI 官方 Skills 文档](https://learn.chatgpt.com/docs/build-skills)。

Skill 的核心规则无需额外 Python 包。仅运行 Word 批注脚本时需要 Python 3.10+ 与 lxml：

```powershell
python -m pip install -r requirements.txt
```

DOCX 渲染仍需宿主提供的文档工具或可用的 Word/受支持渲染器；本项目不捆绑办公软件。原生 Word 验证不是跨平台 CI 的一部分。

## 调用

```text
$paper-argument-audit
目标期刊：填写全名
文章类型：填写对应类型
稿件：提供Word路径
样本：提供对应期刊全文目录，或要求查找合法可访问的全文
先确认已有期刊画像；没有则精读至少30篇同类型完整论文。
随后检查论证、优化叙述与语境用词并校对。
保留原文，在Word批注中标明论证角色、错误类型、修改原因和具体建议。
```

暂不做期刊适配时，可明确要求：

```text
$paper-argument-audit 请对提供的Word稿做通用论证审查与语言校对。
本次暂不做期刊适配，保留原文，交付真实Word批注稿。
```

没有指定期刊、全文不足30篇或尚无可用稿件时，Skill应说明实际进度，不以摘要、虚构文献或待补结果冒充完成。

## Word 批注示例

下载 [批注示例](examples/annotated-manuscript-demo.docx)，用 Word 的“审阅”显示全部批注。示例为自拟材料，含15条新增批注和1条原有批注；普通网页或PDF预览可能不显示批注。

角色批注定位“主张、理由、证据、根据、承认、回应”，并关联支持ID。修改批注给出错误类型、原因、证据边界、可实施修法及必要的待补信息。

示例演示关联与因果、隐含根据、承认和回应、前后矛盾、循环论证、中文句式及英文语法等问题。它不是实际研究结果。

## 文件工具

```powershell
# 生成段落清单和源文件哈希
python -X utf8 skills/paper-argument-audit/scripts/word_comments.py inventory input.docx inventory.json
# 按 plan.json 追加真实批注，原文保留
python -X utf8 skills/paper-argument-audit/scripts/word_comments.py apply input.docx plan.json annotated.docx
# 检查批注部件及连接
python -X utf8 skills/paper-argument-audit/scripts/word_comments.py verify annotated.docx
# 核对期刊精读记录，不等于认证理解质量
python -X utf8 skills/paper-argument-audit/scripts/check_journal_corpus.py journal-profiles/manifest.json
```

批注工具只负责写入与校验，语义诊断由 Skill 执行。它支持普通文字run、表格段落、重复定位与重叠批注；遇到字段、超链接、修订、复杂对象等会拒绝该次写入，需另行原生处理。脚注、页眉页脚和文本框等也不自动宣称覆盖。

详见 [批注规范](skills/paper-argument-audit/references/Word批注与校对规范.md)、[期刊学习流程](skills/paper-argument-audit/references/module-1/期刊学习与适配.md)及[语料记录规范](skills/paper-argument-audit/references/module-1/语料记录规范.md)。

## 仓库结构

```text
skills/paper-argument-audit/   可直接安装的Skill与参考资料
examples/                    自拟Word批注示例
docs/                        来源、验证记录与发布说明
tools/                       安装、仓库检查和打包工具
tests/                       文件操作与门槛的回归测试
.github/workflows/           自动检查
```

## 开发与验证

```powershell
python -m pip install -r requirements-dev.txt
python tools/validate_repository.py
python -m unittest discover -s tests -v
python tools/build_release.py
```

打包生成独立 Skill ZIP、完整仓库分发 ZIP及SHA-256清单。输出在 `dist/`，不重复提交到Git历史；可上传到 GitHub Release。[验证说明](docs/VALIDATION.md)明确区分结构检查、原生Word检查和真实论文效果。

## 来源与许可状态

论证核心依据用户提供的《论证的艺术》原书第3版中文译本；表达模块依据用户提供、标为2026-10-10期的《The Economist》文本。来源身份、页码、覆盖和未核实事项见[来源说明](docs/SOURCES.md)。未独立核验该期刊文件真伪及其中新闻、科研和历史断言。

仓库包含归纳分析及有限短语示例，不包含原书、期刊原PDF、整期提取文本或私人论文。来源中的观点不是用户论文的已核实事实来源。

尚未选择开源许可证，见 [LICENSE](LICENSE) 与 [NOTICE.md](NOTICE.md)。第三方文字与品牌的权利不由本仓库授予。
