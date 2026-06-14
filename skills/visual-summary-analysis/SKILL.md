---
name: "visual-summary-analysis"
description: "Performs AI analysis on input video clips/image content and generates a smooth, natural scene description. | 视觉摘要智述技能，对传入的视频片段/图片内容进行AI分析，生成一段通顺自然的场景描述内容"
version: "1.0.3"
---

# Visual Summarization Skill | 视觉摘要智述技能

Based on advanced multimodal large models and video understanding technologies, this feature performs deep semantic
analysis and logical reasoning on input video clips or images. Utilizing computer vision algorithms, the system
precisely identifies key visual elements—including subject objects, environmental backgrounds, action behaviors, and
lighting atmosphere. It then combines this with Natural Language Generation (NLG) technology to transform abstract
visual information into smooth, logically coherent scene descriptions. Whether dealing with dynamic video events or
static image moments, the system captures critical details and restores the on-site context with vivid language. This
provides intelligent text summarization services for scenarios such as video content understanding, accessibility
assistance, and media asset management.

本功能基于先进的多模态大模型与视频理解技术，能够对传入的视频片段或图片进行深度语义分析与逻辑推理。系统通过计算机视觉算法精准识别画面中的主体对象、环境背景、动作行为及光影氛围，并结合自然语言生成技术，将抽象的视觉信息转化为一段通顺自然、逻辑连贯的场景描述。无论是动态的视频事件还是静态的图像瞬间，系统都能捕捉关键细节，用生动的语言还原现场情境，为视频内容理解、无障碍辅助、媒体资产管理等场景提供智能化的文本摘要服务

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：对传入的视频片段或图片内容进行AI分析，自动生成通顺自然的场景描述摘要
- 能力包含：场景内容识别、物体识别、行为识别、文字提取，整合成一段流畅自然的中文描述
- **适用场景**：监控视频内容摘要、会议片段内容概括、生活视频内容描述、图文内容转文字
- 输出：生成一段连贯自然的描述性文字，包含场景中主要元素、人物行为、环境等信息
- 触发条件:
    1. **默认触发**：当用户提供视频/图片需要生成内容描述/视觉摘要时，默认触发本技能
    2. 当用户明确需要视频内容描述、图片内容摘要、视觉智述时，提及视频摘要、内容描述、视觉摘要智述、视频转文字等关键词，并且上传了视频/图片
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史摘要报告、摘要报告清单、报告列表、查询历史摘要报告、显示所有摘要报告、视觉智述分析报告，查询视觉摘要智述分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有摘要报告"、"显示历史智述"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.visual_summary_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 使用要求（获得准确描述的前提）

为了获得高质量的内容描述，请确保：

1. **视频/图片内容清晰**，主要物体和场景完整可见
2. 视频片段长度建议不超过 5 分钟，过长内容建议分段描述
3. 主要场景主体不被大面积遮挡

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行视觉摘要智述分析前，必须按以下优先级顺序获取 open-id：**

```
第 1 步：【最高优先级】检查技能所在目录的配置文件（优先）
        路径：skills/smyx_common/scripts/config.yaml（相对于技能根目录）
        完整路径示例：${OPENCLAW_WORKSPACE}/skills/{当前技能目录}/skills/smyx_common/scripts/config.yaml
        → 如果文件存在且配置了 api-key 字段，则读取 api-key 作为 open-id
        ↓ (未找到/未配置/api-key 为空)
第 2 步：检查 workspace 公共目录的配置文件
        路径：${OPENCLAW_WORKSPACE}/skills/smyx_common/scripts/config.yaml
        → 如果文件存在且配置了 api-key 字段，则读取 api-key 作为 open-id
        ↓ (未找到/未配置)
第 3 步：检查用户是否在消息中明确提供了 open-id
        ↓ (未提供)
第 4 步：❗ 必须暂停执行，明确提示用户提供用户名或手机号作为 open-id
```

**⚠️ 关键约束：**

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、summary123、visual456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询摘要报告记录），并询问是否继续

---

- 标准流程:
    1. **准备视频/图片输入**
        - 提供本地文件路径或网络 URL
        - 确保内容清晰可见
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行视觉摘要智述分析**
        - 调用 `-m scripts.visual_summary_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络视频/图片 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史视觉摘要智述分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的视觉摘要智述分析报告
        - 包含：输入基本信息、生成的描述内容、关键物体识别列表、整体内容摘要

## 资源索引

- 必要脚本：见 [scripts/visual_summary_analysis.py](scripts/visual_summary_analysis.py)(用途：调用 API
  进行视觉摘要智述分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png/mp4/avi/mov，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"视频时长"、"生成时间"、"点击查看"四列，其中"报告名称"列使用`视觉摘要智述报告-{记录id}`形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 视频时长 | 生成时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 视觉摘要智述报告 -20260329001000001 | 1分30秒 | 2026-03-29 00:10 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地视频片段（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.visual_summary_analysis --input /path/to/clip.mp4 --open-id openclaw-control-ui

# 分析本地图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.visual_summary_analysis --input /path/to/image.jpg --open-id openclaw-control-ui

# 分析网络视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.visual_summary_analysis --url https://example.com/clip.mp4 --open-id openclaw-control-ui

# 显示历史摘要报告/显示摘要报告清单列表/显示历史智述（自动触发关键词：查看历史摘要报告、历史报告、摘要报告清单等）
python -m scripts.visual_summary_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.visual_summary_analysis --input clip.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.visual_summary_analysis --input clip.mp4 --open-id your-open-id --output result.json
```
