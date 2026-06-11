---
name: "smyx-child-nightmare-rollover-detection-analysis"
description: "Using a fixed camera in the child's bedroom (infrared night vision), the system continuously captures video and audio at night to analyze the child's sleep behavior. It detects rollover frequency (rollovers per minute), cries (recognizing specific cry-sound features), and sleep talk (speech during sleep), and generates a sleep-quality report. When rollovers occur too often (e.g., > 3 per hour), strong crying is detected, or sleep talk is observed, the system pushes 'possible nightmare' or 'restless sleep' alerts to the parents. Application scenarios: child bedrooms, infant rooms. The system relays night-time monitoring to help parents understand the child's sleep quality and provide timely comfort. Skill features: improve sleep. | 通过儿童床或卧室的固定摄像头（红外夜视），在夜间连续采集视频及音频，分析儿童的睡眠行为。检测翻身次数（每分钟翻身频率）、哭声（识别特定的哭声音频特征）以及梦话（检测睡眠中的语音），生成睡眠质量报告。当翻身过于频繁（如>3次/小时）、出现强烈哭声或梦话时，推送给父母'可能做噩梦'或'睡眠不安'的预警。应用场景：儿童卧室、婴儿房。系统夜间接力监测，帮助家长了解儿童睡眠质量，及时安抚。技能特点：改善睡眠。"
version: "1.0.2"
---

# Child Restless Sleep / Nightmare Detection | 儿童睡眠中频繁翻身/噩梦识别

Using a fixed camera in the child's bedroom (infrared night vision), the system continuously captures video and audio at night to analyze the child's sleep behavior. It detects rollover frequency (rollovers per minute), cries (recognizing specific cry-sound features), and sleep talk (speech during sleep), and generates a sleep-quality report. When rollovers occur too often (e.g., > 3 per hour), strong crying is detected, or sleep talk is observed, the system pushes 'possible nightmare' or 'restless sleep' alerts to the parents. Application scenarios: child bedrooms, infant rooms. The system relays night-time monitoring to help parents understand the child's sleep quality and provide timely comfort. Skill features: improve sleep.

通过儿童床或卧室的固定摄像头（红外夜视），在夜间连续采集视频及音频，分析儿童的睡眠行为。检测翻身次数（每分钟翻身频率）、哭声（识别特定的哭声音频特征）以及梦话（检测睡眠中的语音），生成睡眠质量报告。当翻身过于频繁（如>3次/小时）、出现强烈哭声或梦话时，推送给父母'可能做噩梦'或'睡眠不安'的预警。应用场景：儿童卧室、婴儿房。系统夜间接力监测，帮助家长了解儿童睡眠质量，及时安抚。技能特点：改善睡眠。

## 🎯 AI 角色

**假设你是一个专业的儿童睡眠健康 AI。你的任务是分析儿童夜间睡眠视频及音频，检测翻身动作、哭声以及梦话，评估睡眠质量。不要提供医疗诊断或睡眠障碍诊断，仅输出基于视觉和听觉的睡眠行为统计与方向性安抚提醒。**

## 任务目标

- 本 Skill 用于：基于儿童夜间睡眠音视频，统计翻身次数、哭声/梦话事件，评估睡眠质量并对噩梦/睡眠不安推送预警
- 能力包含：儿童夜视检测、姿态/朝向变化分析（翻身事件）、哭声音频特征识别、梦话/呓语识别、突发肢体动作识别（噩梦惊跳）、翻身频率（次/小时）计算、睡眠质量综合得分（0-100）+ 等级（excellent / good / fair / poor）、噩梦/睡眠不安预警生成
- 触发条件:
    1. **默认触发**：当用户提供儿童夜间睡眠音视频 URL 或文件需要分析时，默认触发本技能进行翻身/噩梦识别
    2. 当用户明确提及儿童睡眠、翻身频繁、噩梦、梦话、夜哭、睡眠不安、夜啼、夜间安抚、睡眠质量等关键词，并且上传了音视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看儿童夜间睡眠历史报告、噩梦预警报告清单、睡眠质量报告清单、查询历史翻身记录、显示所有儿童睡眠报告、显示儿童睡眠健康诊断报告，查询睡眠不安预警清单
- 自动行为：
    1. 如果用户上传了附件或者音视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有儿童睡眠报告"、"
       显示所有噩梦预警报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_child_nightmare_rollover_detection_analysis --list --open-id` 参数调用 API
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

**在执行儿童睡眠中频繁翻身/噩梦识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备儿童夜间睡眠音视频输入**
        - 提供本地儿童夜间睡眠音视频路径或网络 URL
        - 摄像头建议固定于儿童床上方/侧方，覆盖全身；夜间启用红外/微光模式
        - **必须含音频通道**（哭声/梦话识别），视频帧率建议 ≥ 10 FPS；时段建议覆盖整夜（如 20:00 - 次日 07:00）
        - 可选附带：儿童年龄、近期是否有发热/作息变化/情绪波动、阈值覆盖（rollover_rate_threshold / cry_strength_threshold）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行儿童夜间翻身/噩梦识别**
        - 调用 `-m scripts.smyx_child_nightmare_rollover_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地儿童夜间睡眠音视频文件路径
            - `--url`: 网络儿童夜间睡眠音视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，儿童睡眠健康场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示儿童夜间翻身/噩梦历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的夜间翻身/噩梦识别报告
        - 包含：是否检测到儿童（child_detected）、累计睡眠时长（sleep_duration_min）、翻身次数（rollover_count）+ 频率（rollover_rate_per_hour）、哭声事件（cry_events）、梦话事件（sleep_talk_events）、突发肢体动作（body_jerk_events）、睡眠质量综合得分（sleep_quality_score，0-100）+ 等级（sleep_quality_grade：excellent / good / fair / poor）、噩梦预警标志（nightmare_alert）、预警文本（如"宝宝近 1 小时翻身 5 次并伴有哭声，可能做噩梦，请前往安抚"）
        - **重要提示**：仅输出基于视觉与听觉的睡眠行为统计，不提供医学诊断或睡眠障碍诊断

## 资源索引

- 必要脚本：见 [scripts/smyx_child_nightmare_rollover_detection_analysis.py](scripts/smyx_child_nightmare_rollover_detection_analysis.py)(
  用途：调用 API 进行儿童睡眠中频繁翻身/噩梦识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、阈值定义和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频（**必须包含音频通道**），最大 10MB；建议覆盖整夜、夜视模式
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴书；建议先核实采集端权限
- 分析结果仅作为养育辅助参考，本工具不替代专业儿科/睡眠医学诊断；长期睡眠质量差请咨询专业医生
- 隐私合规：儿童夜间音视频涉及未成年人隐私，使用前需取得监护人知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"睡眠质量"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`儿童夜间翻身噩梦识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 睡眠质量 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 儿童夜间翻身噩梦识别报告-20260312172200001 | 62 / fair（翻身偏多） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地夜间睡眠音视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_nightmare_rollover_detection_analysis --input /path/to/night_sleep.mp4 --open-id your-open-id

# 分析网络夜间睡眠音视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_nightmare_rollover_detection_analysis --url https://example.com/night_sleep.mp4 --open-id your-open-id

# 显示历史儿童夜间翻身/噩梦识别报告（自动触发关键词：查看儿童夜间睡眠历史报告、噩梦预警报告清单等）
python -m scripts.smyx_child_nightmare_rollover_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_child_nightmare_rollover_detection_analysis --input sleep.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_child_nightmare_rollover_detection_analysis --input sleep.mp4 --open-id your-open-id --output result.json
```
