# storyboard-artist-skill

将用户的一句话故事创意转化为专业分镜脚本。纯提示词驱动，输出涵盖分镜总纲、场次分镜表、转场设计、运镜节奏、光影色调、构图母题、声音设计线索及制作提示，可直接用于动态分镜绘制和拍摄筹备。

## 推荐模型与 API

本技能为纯提示词驱动，不绑定特定模型。以下为经过实测的推荐方案，按"易落地性"排序：

### 🥇 首选：DeepSeek-V3（性价比最高）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.deepseek.com/v1` |
| 模型名 | `deepseek-chat` |
| 价格 | 输入 ¥1/百万token，输出 ¥2/百万token |
| 优势 | 国内直连无需代理，镜头语言描述精准，中文表达自然 |
| 注册 | [https://platform.deepseek.com](https://platform.deepseek.com) |

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-deepseek-api-key",
    base_url="https://api.deepseek.com/v1"
)

response = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": "你的故事创意"}
    ],
    temperature=0.8,
    max_tokens=8000
)
```

### 🥈 备选：硅基流动 SiliconFlow（国内最便捷）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.siliconflow.cn/v1` |
| 模型名 | `deepseek-ai/DeepSeek-V3` |
| 价格 | 新用户赠送额度，约 ¥0.5/百万token |
| 优势 | 一个 API Key 可切换多模型，国内直连 |
| 注册 | [https://siliconflow.cn](https://siliconflow.cn) |

### 🥉 品质首选：GPT-4o（结构化输出最可靠）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.openai.com/v1` |
| 模型名 | `gpt-4o` |
| 价格 | 输入 $2.5/百万token，输出 $10/百万token |
| 优势 | 镜头编号和格式最稳定，适合预算充足的生产环境 |
| 注意 | 国内需代理访问 |

### 其他可用模型

- **智谱 GLM-4-Plus**：`https://open.bigmodel.cn/api/paas/v4`，中文理解力强
- **Qwen2.5-72B**：通过硅基流动或阿里云 DashScope 调用
- **Claude 3.5 Sonnet**：创意质量极高，但国内访问不便

## 快速开始

### 方式一：直接复制提示词（最快）

打开 [skill.yaml](./skill.yaml)，找到 `system_prompt` 字段，将完整内容复制到任意 LLM 平台的 System Prompt 设置中，将一句话创意作为用户消息发送即可。

### 方式二：在 Dify 中导入

1. 创建 Chatflow 应用，添加 LLM 节点
2. System Prompt 粘贴 `system_prompt` 完整内容
3. 模型参数：Temperature `0.8`，Max Tokens `8000`
4. 添加开始节点，定义输入变量 `user_idea`
5. LLM 节点用户消息引用 `{{user_idea}}`
6. 发布应用

### 方式三：在 Coze 中导入

1. 创建新 Bot，在"人设与回复逻辑"中粘贴 `system_prompt`
2. 设置 Temperature 为 0.8
3. 将一句话创意直接发送给 Bot

### 方式四：Python 脚本调用

```python
import yaml
from openai import OpenAI

with open("skill.yaml", "r", encoding="utf-8") as f:
    skill = yaml.safe_load(f)

client = OpenAI(
    api_key="your-api-key",
    base_url="https://api.deepseek.com/v1"
)

result = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": skill["system_prompt"]},
        {"role": "user", "content": "一个落魄钢琴手在地下停车场听见一段神秘旋律，追踪后发现弹奏者是30年前的自己。"}
    ],
    temperature=skill["runtime"]["temperature"],
    max_tokens=skill["runtime"]["max_tokens"]
)

print(result.choices[0].message.content)
```

## 输入示例

```
一个落魄钢琴手在地下停车场听见一段神秘旋律，追踪后发现弹奏者是30年前的自己。
```

## 输出示例

完整输出示例请查看 [examples/output_01.md](./examples/output_01.md)，包含以下结构化段落：

- **【分镜总纲】** — 镜头风格定义、宽高比、总镜头数、核心策略、ASL
- **【关键场次分镜】** — 3个场次×6-10镜，每镜含景别/角度/焦段/运镜/画面/时长/声音
- **【转场设计】** — 场次间转场类型、衔接逻辑、叙事功能
- **【运镜与节奏说明】** — 运镜情绪逻辑、剪辑节奏、节奏图
- **【光影与色调提示】** — 主光源/补光/光比/特殊效果/色调
- **【构图母题】** — 主母题+演变+打破时机+辅助逻辑
- **【声音设计线索】** — 环境声/音效节点/音乐进入/静音使用
- **【制作提示】** — 设备/技术难点/预算/后期/绘制建议

## 与其他 Skill 配合使用

本技能包与以下 Skill 天然互补，形成完整的影视前期设计链：

1. **[screenwriter-skill](https://github.com/small-bluesky/screenwriter-skill)**：一句话→完整剧本
2. **[art-director-skill](https://github.com/small-bluesky/art-director-skill)**：一句话→美术设计视觉方案
3. **storyboard-artist-skill**（本技能）：一句话→分镜脚本

推荐工作流：
- 先用 **screenwriter-skill** 生成剧本，获得明确的场次和人物
- 用 **art-director-skill** 生成视觉方案，确定色彩、材质和空间
- 将同一句话创意输入 **storyboard-artist-skill**，生成分镜脚本
- 三者输出的场景、人物和视觉风格可交叉对照，形成完整的"剧本+美术+分镜"前期包

## 高级用法

### 调整运行时参数

- **temperature**：`0.7-0.9` 适合创意发散。分镜需要创意但更需要精确，如需更稳定的镜头编号和格式，可降至 `0.6`
- **max_tokens**：本技能输出量最大（3个场次×6-10镜），建议 ≥ `8000`。使用 DeepSeek-V3 时可设为 `10000`
- **stream**：默认关闭。流式输出适合实时展示生成过程

### 与 RAG 结合

在 LLM 调用前增加知识库检索节点，注入以下参考资料：
- 特定导演的分镜风格分析（如希区柯克的构图逻辑、朴赞郁的运镜方式）
- 经典电影的具体场次分镜解析
- 运镜技术和设备操作手册

### 接入图像生成

`extensions.image_generation` 预留了图像生成接口。未来工作流可扩展为：
1. LLM 生成分镜脚本文本
2. 提取每个镜头的画面描述
3. 自动调用 Midjourney / DALL-E / Stable Diffusion 生成分镜画面草图

### 接入动态分镜工具

`extensions.animation_support` 预留了动态分镜接口。未来可：
1. 将分镜脚本解析为结构化数据（JSON）
2. 自动导入 Storyboarder / ShotPro 等工具
3. 生成带运镜动画的动态分镜预览

### 拆分为多节点工作流

在 Dify 等平台中可将工作流拆分：
1. 节点一：分镜总纲 + 关键场次分镜（核心创作）
2. 节点二：转场设计 + 运镜节奏 + 光影色调（技术细化）
3. 节点三：构图母题 + 声音设计 + 制作提示（执行层）

## 常见问题

### 为什么新增了「转场设计」和「声音设计线索」？

初版缺少这两个维度。转场设计是分镜师的核心工作之一——好的转场不仅是技术衔接，更是叙事工具。声音设计线索则为分镜提供了"听觉维度"，让导演和声音设计在前期就能对齐创意意图，避免后期补课时发现画面和声音不匹配。

### 为什么每个镜头要给出焦段建议？

焦段直接影响画面透视关系和空间感。18mm广角和85mm中长焦拍同一场景，画面感受完全不同。给出焦段建议可以确保分镜的视觉意图在拍摄时被准确还原，避免"分镜看起来很棒但实拍完全不是那个感觉"的问题。

### 为什么要求镜头时长之和等于场次总时长？

这是专业分镜的基本要求。时长约束迫使分镜师考虑节奏和呼吸感，避免"每个镜头都想给足时间"导致的整体拖沓。实际拍摄中导演当然可以调整，但分镜阶段就应该有明确的节奏规划。

### 推荐用 DeepSeek 还是 GPT-4o？

- **日常使用、个人项目、预算有限**：DeepSeek-V3，性价比极高
- **商业项目、需要最稳定的镜头编号和格式**：GPT-4o，结构化输出最可靠
- **国内快速上手**：硅基流动，注册即用

### 输出太长被截断怎么办？

本技能输出量较大，建议：
1. 将 max_tokens 设为 8000–10000
2. 使用支持长输出的模型（DeepSeek-V3、GPT-4o 均支持）
3. 如果仍被截断，可回复"继续"让模型补充
4. 或使用"拆分为多节点工作流"方案

## 项目结构

```
storyboard-artist-skill/
├── README.md
├── skill.yaml
├── examples/
│   ├── input_01.txt
│   └── output_01.md
├── assets/
│   └── workflow-diagram.png
├── LICENSE
└── .gitignore
```

## 贡献指南

欢迎通过 Issue 或 PR 参与贡献：

- 提示词优化，提升特定类型（动作片、爱情片、悬疑片等）的分镜设计质量
- 提供更多示例输入输出
- 适配更多 Agent 平台的导入方案
- 补充更多模型的实测效果对比
- 开发结构化输出解析器（将文本分镜转为 JSON/CSV）

请确保所有提交使用 **UTF-8 without BOM** 编码，换行符为 **LF**。

## 许可证

本项目基于 MIT 许可证开源，详见 [LICENSE](./LICENSE) 文件。