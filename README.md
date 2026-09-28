# pm-antdesign

基于 Ant 视觉语言的企业级设计系统 Skill，面向**报表 / 看板 / 中后台**数据密集界面，适配 WorkBuddy / Claude Code 等 Agent 的 skill 体系。产出的是**可落地实施的规范文档**（设计令牌、组件规则、QA 清单），而非运行时代码。

## 来源与许可

本仓库为 **MIT 衍生作品**，请保留以下署名与许可声明后再分发：

- **原始骨架**：由 `typeui.sh` 生成（`SKILL.md` 中 `metadata.author: typeui.sh`）。
- **v2.0 报表扩展**：由 `lukesir` 贡献，包含报表类设计规范、设计令牌补全、组件规则、QA 清单。
- **许可证**：MIT（见 `LICENSE`）。

## 目录结构

```
pm-antdesign/
├── SKILL.md                  入口：触发词、令牌契约、报表强制规则
├── DESIGN.md                 权威令牌表（颜色 / 字阶 / 间距 / 圆角 / 断点）
├── LICENSE                   MIT 许可文本（含双署名）
├── assets/
│   └── design-tokens.css     可直接粘贴的令牌样式表 + 基础工具类
└── references/
    ├── report-ui-spec.md     报表类核心细则（页面布局 / 字段展示 / 交互逻辑 / 样式风格）
    ├── component-rules.md    组件 anatomy / states / a11y 验收标准
    └── qa-checklist.md       可逐项打勾执行的评审清单（视觉 / 数据 / 交互 / 无障碍 / 健壮性）
```

## 安装

将本目录整体放入 Agent 的 skills 目录即可，例如：

- WorkBuddy：`~/.workbuddy/skills/pm-antdesign/`
- Claude Code：`~/.claude/skills/pm-antdesign/`

## 重要声明

本 skill 的 **primary 主色与 Ant Design v5 默认一致**，但 **status 状态色 / 字体栈 / 字重刻度为本系统自有选择，并非 Ant Design 默认值**。请勿将其当作 Ant Design 官方规范引用。
