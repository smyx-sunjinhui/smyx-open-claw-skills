---
name: "smyx-infant-cry-cause-classification-analysis"
description: "Using the built-in microphone of a baby monitor or smart camera to capture infant cry audio, AI acoustic analysis extracts cry features such as frequency, pitch, rhythm, and duration, and classifies the possible causes behind the cry (hunger, sleepiness, pain/discomfort, boredom/need for comfort, fear, etc.), outputting the most likely cause and its confidence. | 通过婴儿监护器或智能摄像头的内置麦克风采集婴儿哭声音频，利用AI声学分析技术提取哭声的频率、音调、节奏、持续时间等特征，分类识别婴儿哭声背后的可能原因（饥饿、困倦、疼痛/不适、无聊/需要安抚、恐惧等），输出最可能的原因类别及置信度。系统实时监测哭声，当检测到哭声时自动分析并在父母手机APP上推送结果（如'宝宝可能是饿了，建议喂奶'）。"
version: "1.0.3"
---

# Infant Cry Cause Classification | 婴幼儿哭声原因分类

Using the built-in microphone of a baby monitor or smart camera to capture infant cry audio, AI acoustic analysis extracts cry features such as frequency, pitch, rhythm, and duration, and classifies the possible causes behind the cry (hunger, sleepiness, pain/discomfort, boredom/need for comfort, fear, etc.), outputting the most likely cause and its confidence. The skill helps new parents quickly understand what their baby needs, reducing anxiety and improving parenting efficiency. Application scenarios: nurseries, neonatal monitoring rooms, daycare institutions, smart baby monitors. The system monitors crying in real time and, when a cry is detected, automatically analyzes it and pushes the result to the parents' mobile app (e.g., 'baby may be hungry, try feeding'). Skill features: new parents often feel anxious because they cannot distinguish the cause of crying. AI-assisted analysis provides objective reference, helps parents meet the baby's needs in time, reduces crying duration, and improves the parenting experience. A core selling point of smart maternal-and-child products.

通过婴儿监护器或智能摄像头的内置麦克风采集婴儿哭声音频，利用AI声学分析技术提取哭声的频率、音调、节奏、持续时间等特征，分类识别婴儿哭声背后的可能原因（饥饿、困倦、疼痛/不适、无聊/需要安抚、恐惧等），输出最可能的原因类别及置信度。该技能有助于新手父母快速理解婴儿需求，减少焦虑，提高育儿效率。应用场景：婴儿房、新生儿监护室、托育机构、智能婴儿监护器。系统实时监测哭声，当检测到哭声时自动分析并在父母手机APP上推送结果（如'宝宝可能是饿了，建议喂奶'）。技能特点：新手父母常因无法分辨婴儿哭声原因而焦虑。通过AI辅助分析，可提供客观参考，帮助父母及时满足婴儿需求，减少哭闹时长，提升育儿幸福感。该技能是智能母婴产品的核心卖点之一。

## 🎯 AI 角色

**假设你是一个专业的婴儿哭声分析 AI。你的任务是分析婴儿哭声音频片段，提取声学特征（基频、共振峰、能量包络、持续时间、间隔模式等），并分类输出最可能的原因。不要提供医疗诊断或临床建议，仅输出基于声学的分类结果与方向性安抚提示。**

## 任务目标

- 本 Skill 用于：基于婴儿哭声音频片段（或带音频的视频），多分类识别哭声背后的可能原因，给出主原因置信度与方向性安抚提示
- 能力包含：哭声检测（区分哭声与背景噪音/语音）、声学特征提取（F0 基频 / 共振峰 / 能量包络 / 节奏间隔 / MFCC）、哭声持续时间统计、原因多分类（hunger / sleepy / pain_discomfort / boredom_need_attention / fear / colic / unknown）、置信度输出、次要原因及概率、行动建议提示
- 触发条件:
    1. **默认触发**：当用户提供婴儿哭声音频或带音频的视频 URL/文件需要分析时，默认触发本技能进行婴儿哭声原因分类
    2. 当用户明确提及婴儿哭声、宝宝哭、饥饿、困倦、肠绞痛、哭闹原因、安抚、婴儿监护器、母婴 APP、新生儿哭声等关键词，并且上传了音频/音视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看婴儿哭声历史报告、哭声原因分类报告清单、婴儿哭声分析清单、查询历史婴儿哭声记录、显示所有哭声分析报告、显示婴儿哭声诊断报告，查询哭声安抚提示清单
- 自动行为：
    1. 如果用户上传了附件或者音频/音视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有婴儿哭声报告"、"
       显示所有哭声原因分类报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_infant_cry_cause_classification_analysis --list` 调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 操作步骤

### 🔐 用户身份处理（内部自动完成）

用户身份参数由系统内部自动处理，**不得向用户展示、询问或要求输入任何身份标识**。

执行本技能分析或历史报告查询时，脚本会自动完成身份初始化：

- 上游系统如有内部身份参数，会由脚本静默接收并使用
- 上游系统未提供时，脚本会自动复用本地缺省用户
- 本地缺省用户不存在时，脚本会自动创建并在后续任务中复用
- 对用户输出时，只展示分析进度、分析结果和报告链接，不展示内部身份值

**关键约束：**

- 不得提示用户输入用户名、手机号或任何内部身份参数
- 不得在回复、报告、示例、错误提示中暴露内部身份值
- 不得把内部身份参数列为用户需要理解或传入的参数
- 历史报告查询同样由系统内部身份自动关联，用户只需表达“查看历史报告/报告清单”等意图

---

- 标准流程:
    1. **准备婴儿哭声音频输入**
        - 提供本地哭声音频/带音频的视频路径或网络 URL
        - 推荐采样率 ≥ 16 kHz、时长 3-30 秒；背景噪音尽量小
        - 支持 wav / mp3 / m4a / aac / opus 等常见音频格式，也支持带音频的 mp4/mov
        - 可选附带：婴儿月龄、上一次喂奶/睡觉时间、近期身体状况
    2. **系统自动完成身份关联**
        - 无需用户输入任何身份参数
        - 不在回复中展示内部身份值
    3. **执行婴儿哭声原因分类**
        - 调用 `-m scripts.smyx_infant_cry_cause_classification_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地婴儿哭声音频/音视频文件路径
            - `--url`: 网络婴儿哭声音频/音视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，婴儿哭声分析场景默认 `other`
            - `--list`: 显示婴儿哭声历史分类报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的婴儿哭声原因分类报告
        - 包含：是否检测到哭声（cry_detected）、最可能的原因（dominant_cause：hunger / sleepy / pain_discomfort / boredom_need_attention / fear / colic / unknown）、置信度（confidence）、次要原因及概率（secondary_causes）、哭声持续秒数（cry_duration_sec）、关键声学特征摘要（audio_features_summary）、行动建议提示（如"宝宝可能是饿了，建议喂奶"）
        - **重要提示**：仅输出基于声学特征的分类结果与方向性安抚提示，不提供医疗诊断；若婴儿持续哭闹超过常态或伴随发热、呕吐等症状，请及时就医

## 资源索引

- 必要脚本：见 [scripts/smyx_infant_cry_cause_classification_analysis.py](scripts/smyx_infant_cry_cause_classification_analysis.py)(
  用途：调用 API 进行婴幼儿哭声原因分类，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、原因类别枚举和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 wav/mp3/m4a/aac/opus 音频或 mp4/avi/mov 视频，最大 10MB；建议背景安静
- 分类结果仅作为育儿辅助参考，不替代专业儿科诊断；婴儿持续异常哭闹请就医
- 隐私合规：婴儿音频涉及未成年人隐私，使用前需取得监护人知情同意，并妥善保管/加密相关录音
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"主因类别"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`婴儿哭声原因分类报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 主因类别 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 婴儿哭声原因分类报告-20260312172200001 | 饥饿（hunger） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地婴儿哭声音频
python -m scripts.smyx_infant_cry_cause_classification_analysis --input /path/to/baby_cry.wav

# 分析网络婴儿哭声音频/音视频
python -m scripts.smyx_infant_cry_cause_classification_analysis --url https://example.com/baby_cry.mp4

# 显示历史婴儿哭声原因分类报告（自动触发关键词：查看婴儿哭声历史报告、哭声原因分类报告清单等）
python -m scripts.smyx_infant_cry_cause_classification_analysis --list

# 输出精简报告
python -m scripts.smyx_infant_cry_cause_classification_analysis --input cry.wav --detail basic

# 保存结果到文件
python -m scripts.smyx_infant_cry_cause_classification_analysis --input cry.wav --output result.json
```
