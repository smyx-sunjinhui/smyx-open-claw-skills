---
name: "cat-face-recognition-analysis"
description: "Dentifies specific cats by comparing images or videos of their faces. It supports distinguishing between different individuals in multi-cat households, ensuring accurate recognition once the data is registered in the database. | 猫脸识别技能，通过猫脸图片/视频比对识别具体是哪只猫咪，支持多猫家庭区分不同个体，录入底库后可准确识别"
version: "1.0.2"
---

# Cat Face Recognition Skill | 猫脸识别技能

By analyzing key feline facial features—such as ear shape, eye contours, and nose prints—this capability compares
individual data across images or video streams to precisely identify specific cats. It supports stable differentiation
of individuals even in multi-cat households. Once a database of your cats is pre-registered, the system enables
real-time identity recognition in daily activities or surveillance footage, making it ideal for smart feeding, lost pet
alerts, and behavior logging.

本技能支持对图片或视频流中的鸟类进行自动识别，覆盖不低于500种常见鸟类，可区分相似种与亚种。系统基于深度学习视觉模型，可部署于生态观测站、自然保护区或家庭庭院等场景，实现鸟种实时监测与记录。同时支持定制化模型训练，根据特定区域或物种需求优化识别效果，为鸟类多样性调查、观鸟爱好及生态保护提供智能辅助。

## 演示案例

- [🔗 通过网路视频进行识别分析](https://www.coze.cn/s/_rBzaMK0rxw/)
- [🔗 通过上传视频进行识别分析](https://www.coze.cn/s/qO0KEADtYno/)
- [🔗 显示历史分析报告](https://www.coze.cn/s/JPG_cjvCekQ/)

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：对图片/视频中的猫咪进行人脸识别，比对底库识别出具体是哪只猫咪
- 能力包含：猫脸检测、特征提取、底库比对、个体识别
- **特色功能**：支持多猫家庭，每个猫咪录入底库后，能准确分辨出"这只是谁"
- **适用场景**：多猫家庭智能监控、宠物喂食器身份识别、猫咪活动轨迹统计
- 触发条件:
    1. **默认触发**：当用户提供猫脸图片/视频需要识别具体猫咪个体时，默认触发本技能
    2. 当用户明确需要猫脸识别、猫咪个体区分时，提及猫脸识别、分辨猫咪、哪只猫、猫咪识别等关键词，并且上传了图片/视频
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史识别报告、猫脸识别报告清单、识别报告列表、查询历史识别报告、显示所有识别报告、猫脸分析报告，查询猫脸识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者图片/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有识别报告"、"显示历史识别"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.cat_face_recognition_analysis --list --open-id` 参数调用
          API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 识别要求（获得准确结果的前提）

为了获得准确的猫脸识别，请确保：

1. **猫咪正脸面对摄像头**，猫脸完整清晰可见
2. **光线充足**，避免过度遮挡和运动模糊
3. 需要先完成猫咪底库录入，才能进行比对识别

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行猫脸识别分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、catface123、whiskers456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询识别报告记录），并询问是否继续

---

- 标准流程:
    1. **准备猫脸图片/视频输入**
        - 提供本地文件路径或网络 URL
        - 确保猫脸正脸完整出镜，光线充足
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行猫脸识别分析**
        - 调用 `-m scripts.cat_face_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地图片/视频文件路径
            - `--url`: 网络图片/视频 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史猫脸识别分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的猫脸识别分析报告
        - 包含：输入基本信息、检测到的猫咪数量、每只猫咪识别结果、猫咪姓名、匹配置信度

## 资源索引

- 必要脚本：见 [scripts/cat_face_recognition_analysis.py](scripts/cat_face_recognition_analysis.py)(用途：调用 API
  进行猫脸识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png/mp4/avi/mov，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 识别准确率受拍摄角度、光线遮挡等因素影响，仅供参考
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"识别猫咪数"、识别时间"、"点击查看"四列，其中"报告名称"列使用`猫脸识别报告-{记录id}`形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 识别猫咪数 | 识别时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 猫脸识别报告 -20260329002400001 | 3只 | 2026-03-29 00:24 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 识别本地猫脸图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.cat_face_recognition_analysis --input /path/to/cats.jpg --open-id openclaw-control-ui

# 识别本地监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.cat_face_recognition_analysis --input /path/to/living_room.mp4 --open-id openclaw-control-ui

# 识别网络图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.cat_face_recognition_analysis --url https://example.com/cats.jpg --open-id openclaw-control-ui

# 显示历史识别报告/显示识别报告清单列表/显示历史猫脸识别（自动触发关键词：查看历史识别报告、历史报告、识别报告清单等）
python -m scripts.cat_face_recognition_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.cat_face_recognition_analysis --input cats.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.cat_face_recognition_analysis --input cats.jpg --open-id your-open-id --output result.json
```
