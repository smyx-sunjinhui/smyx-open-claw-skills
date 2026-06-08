---
name: "smyx-child-emotion-recognition-analysis"
description: "Using fixed cameras (and optional microphones) at home or in kindergartens, AI multimodal analysis recognizes a child's facial expressions (eyebrow/eye shape, mouth-corner curvature), cry-sound features (pitch, frequency, duration), and body-motion amplitude (waving, stomping, curling up) in real time, and jointly identifies the child's typical emotional state: happy, sad, angry, fearful, etc. | 通过家庭或幼儿园内的固定摄像头（及可选麦克风），利用AI多模态分析技术实时分析儿童的面部表情（如眉眼形态、嘴角弧度）、哭声音频特征（音调、频率、持续时间）以及肢体动作幅度（挥手、跺脚、蜷缩等），综合识别出儿童当前的典型情绪状态：快乐、悲伤、愤怒、恐惧等。"
version: "1.0.0"
---

# Child Emotion Recognition (Crying/Tantrum/Low Mood) | 儿童情绪波动识别（哭闹/暴躁/低落）

Using fixed cameras (and optional microphones) at home or in kindergartens, AI multimodal analysis recognizes a child's facial expressions (eyebrow/eye shape, mouth-corner curvature), cry-sound features (pitch, frequency, duration), and body-motion amplitude (waving, stomping, curling up) in real time, and jointly identifies the child's typical emotional state: happy, sad, angry, fearful, etc. The skill helps parents or teachers learn the child's mental state in time and provide effective soothing or intervention. Application scenarios: families, kindergartens, early-education centers. The system monitors in real time; when negative emotions (anger, fear, sadness) are detected, it can push reminders via app and suggest soothing actions (e.g., 'baby looks scared, please give a hug'). Skill features: children express emotions directly but busy parents often miss them. AI multimodal analysis helps parents understand the child's inner state, promote parent-child communication, and prevent emotional pile-up. Can be integrated into smart parenting devices or kindergarten management systems to upgrade smart-care capabilities.

通过家庭或幼儿园内的固定摄像头（及可选麦克风），利用AI多模态分析技术实时分析儿童的面部表情（如眉眼形态、嘴角弧度）、哭声音频特征（音调、频率、持续时间）以及肢体动作幅度（挥手、跺脚、蜷缩等），综合识别出儿童当前的典型情绪状态：快乐、悲伤、愤怒、恐惧等。该技能有助于家长或教师及时了解儿童心理状态，进行有效安抚或干预。应用场景：家庭、幼儿园、早教中心。系统实时监测，当识别到负面情绪（如愤怒、恐惧、悲伤）时，可通过APP推送提醒，并建议安抚措施（如'宝宝看起来害怕，请抱抱他'）。技能特点：儿童情绪表达直接但不易被忙碌的家长及时捕捉。通过AI多模态分析，可帮助家长理解孩子内心状态，促进亲子沟通，预防情绪积压。该技能可集成到智能育儿设备或幼儿园管理系统中，提升智能化关怀水平。

## 🎯 AI 角色

**假设你是一个专业的儿童情绪分析 AI。你的任务是分析儿童面部表情、哭声（若有音频）以及肢体动作，综合判断儿童当前的情绪类别。不要提供心理诊断或临床建议，仅输出基于多模态特征的情绪分类结果与方向性安抚提示。**

## 任务目标

- 本 Skill 用于：基于儿童监控视频（含或不含音频），多模态识别儿童当前主导情绪与强度，给出方向性安抚提示
- 能力包含：儿童面部检测与表情识别（眉眼形态、嘴角弧度）、肢体动作幅度估算（挥手、跺脚、蜷缩等）、哭声音频特征分析（可选：音调 / 频率 / 持续时间）、情绪分类（happy / calm / sad / angry / fear / cry / surprise）、情绪强度判定（low / medium / high）、情绪持续秒数、负面情绪阈值告警、安抚提示文案生成
- 触发条件:
    1. **默认触发**：当用户提供儿童监控视频或带音频的音视频 URL/文件需要分析时，默认触发本技能进行儿童情绪识别
    2. 当用户明确提及儿童情绪、哭闹、暴躁、低落、悲伤、恐惧、害怕、发脾气、儿童心理状态、亲子沟通、情绪安抚等关键词，并且上传了视频/音视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看儿童情绪历史报告、儿童情绪报告清单、情绪识别报告清单、查询历史儿童情绪、显示所有儿童情绪报告、显示儿童情绪诊断报告，查询情绪安抚提示清单
- 自动行为：
    1. 如果用户上传了附件或者视频/音视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有儿童情绪报告"、"
       显示所有情绪识别报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_child_emotion_recognition_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行儿童情绪波动识别前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、userC113、user123 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询历史报告记录），并询问是否继续

---

- 标准流程:
    1. **准备儿童监控视频输入**
        - 提供本地儿童监控视频/音视频文件路径或网络 URL
        - 视频建议清晰展示儿童面部（正面或侧脸），帧率 ≥ 15 FPS
        - 如需哭声分析，请上传带音频的 mp4/mov
        - 可选附带：被监护儿童年龄、当前场景（吃饭/睡前/分离）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行儿童情绪波动识别**
        - 调用 `-m scripts.smyx_child_emotion_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地儿童监控视频/音视频文件路径
            - `--url`: 网络儿童监控视频/音视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，儿童情绪识别场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示儿童情绪历史识别报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的儿童情绪识别报告
        - 包含：是否检测到儿童（child_detected）、主导情绪类别（dominant_emotion：happy / calm / sad / angry / fear / cry / surprise）、置信度（emotion_confidence）、情绪强度（emotion_intensity：low/medium/high）、其他次要情绪及概率（secondary_emotions）、情绪持续秒数（duration_sec）、负面情绪阈值告警（negative_emotion_alert）、安抚提示文案（如"宝宝看起来害怕，请抱抱他"）
        - **重要提示**：仅输出基于多模态特征的情绪分类结果与方向性安抚提示，不提供心理诊断或临床建议

## 资源索引

- 必要脚本：见 [scripts/smyx_child_emotion_recognition_analysis.py](scripts/smyx_child_emotion_recognition_analysis.py)(
  用途：调用 API 进行儿童情绪波动识别（哭闹/暴躁/低落）分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、情绪类别枚举和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议清晰面部 + 可选音频通道
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 识别结果仅作为亲子沟通辅助参考，不替代专业儿童心理咨询；持续负面情绪请咨询专业医生
- 隐私合规：儿童视频/音频涉及未成年人隐私，使用前需取得监护人知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"主导情绪"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`儿童情绪识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 主导情绪 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 儿童情绪识别报告-20260312172200001 | 哭闹（高强度） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地儿童监控音视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_emotion_recognition_analysis --input /path/to/child_clip.mp4 --open-id your-open-id

# 分析网络儿童监控音视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_emotion_recognition_analysis --url https://example.com/child_clip.mp4 --open-id your-open-id

# 显示历史儿童情绪识别报告（自动触发关键词：查看儿童情绪历史报告、情绪识别报告清单等）
python -m scripts.smyx_child_emotion_recognition_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_child_emotion_recognition_analysis --input clip.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_child_emotion_recognition_analysis --input clip.mp4 --open-id your-open-id --output result.json
```
