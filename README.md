# MinminFlow

MinminFlow 是一个用于「电商详情页」规划、生成、审核和交付的 Codex Skill。它由 [RockFlow](https://github.com/rockmoons/rock-flow) 改造而来：沿用其 8 屏长图详情页工作流，生图 API 已切换为 openlux（https://api.openlux.ai/v1）。

## 核心能力

- 分析产品图与风格参考图，区分「已确认信息 / 图片可见事实 / AI 推断」
- 先输出 8 屏内容与视觉规划，经确认后才开始生成
- 使用首屏卖点种子维持整套页面的叙事关系
- 建立文字视觉母版和可选的 1:3 图像母版（仅作连续性锚点，不裁切）
- 默认生成独立的 9:21 详情页分屏
- 前 2 至 3 屏阶段拼接预览、提前审核
- 检查产品漂移、文字混乱、未经确认的参数
- 按问题定位最小返修范围，局部重画
- 自动整理单屏、母版、预览图和完整长图并交付

## 使用方式

最简单的请求：

```text
图1是产品图，图2是风格参考。
请为这个产品生成一套8屏电商详情页。
```

也可以补充产品信息：

```text
产品名称：SonicAir H7 Pro
核心卖点：长续航、舒适包耳、蓝牙5.3、折叠收纳
目标人群：通勤和日常娱乐用户
禁止出现：主动降噪、防水、未经确认的认证
```

## 环境变量

生图需要 openlux API 密钥，使用前设置：

```powershell
$env:OPENLUX_API_KEY = "你的密钥"
```

密钥来自你的 openlux API 账户。请勿把密钥提交到仓库。

## 安装

对 Codex 说：

```text
请从 https://github.com/Morri-bot/minminflow 安装 minminflow 技能
```

或手动安装：

```powershell
python "C:\Users\Administrator\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py" --repo Morri-bot/minminflow --path .agents/skills/minminflow
```

## 文件结构

```text
minminflow/
└── .agents/skills/minminflow/
    ├── SKILL.md                          # 核心工作流程和执行规则
    ├── agents/openai.yaml                # 显示名称、简介、默认提示
    └── references/detail-page-patterns.md  # 详情页结构与文案模式库
```

## 与 RockFlow 的差异

- 生图 API：rockmoons → openlux（https://api.openlux.ai/v1，鉴权头 `X-API-KEY`）
- 生成模式：固定使用 `z-image-turbo`
- 环境变量：`ROCKMOONS_API_KEY` → `OPENLUX_API_KEY`
- 技能名：rock-flow → minminflow
- 保留：产品图锚定（`image_urls`）与宽高比映射（9:21 / 1:3 / 9:16 / 1:1 / auto）

## 致谢

原版 RockFlow 由 rockmoons 创建（WeChat: rockmoons | 抖音: 阿南 rockmoons）。
