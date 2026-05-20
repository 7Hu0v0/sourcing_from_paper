# CVPR Industry Scout Skill

一个用于 CVPR / ICCV / NeurIPS / ICML 等顶会论文产业机构识别与作者 sourcing 的 Codex skill。

它会对论文逐篇整理：

- 论文方向
- 是否属于工业界/大厂参与论文
- 论文原文中的工业机构名称
- 按工业机构归类作者
- 对学校+公司双 affiliation 作者标记 `实习生`
- 为工业作者查找 LinkedIn，并给出匹配证据

## Skill 内容

```text
cvpr-industry-scout/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    └── industry-institutions.md
```

## 安装

把 `cvpr-industry-scout` 文件夹复制到 Codex skills 目录：

```bash
mkdir -p ~/.codex/skills
cp -R cvpr-industry-scout ~/.codex/skills/
```

然后开一个新对话即可触发。

## 典型用法

```text
帮我分析这批 CVPR 论文，标出论文方向、是否大厂/工业界、工业机构、作者归类和 LinkedIn。
```

```text
这个 Excel 里有论文 title 和 authors，按 CVPR Industry Scout 的规则补齐工业机构和 LinkedIn。
```

## 关键原则

- 工业机构必须来自论文 affiliation 原文，不能凭记忆猜。
- 同名公司和相近组织严格区分，例如 Alibaba 不等于 Ant Group，ByteDance 不等于 Kuaishou。
- 学校+公司双 affiliation 作者只归到公司，并标记为实习生。
- LinkedIn 只列有足够证据的匹配；不确定时标记 `存疑` 或 `未找到`。
