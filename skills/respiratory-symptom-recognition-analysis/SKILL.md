---
name: "respiratory_symptom_recognition_analysis"
description: "Based on computer vision, automatically detects coughing, phlegm, and wheezing frequency, counts the frequency of episodes, used for early health anomaly alerts, helping to detect respiratory diseases in a timely manner. | 呼吸道症状智能识别技能，基于计算机视觉自动检测咳嗽、咳痰、喘息频率，统计发作频次，用于健康异常早期提醒，帮助及时发现呼吸道疾病"
version: "1.0.5"
---

# Respiratory Symptom Smart Recognition Tool | 呼吸道症状智能识别工具

Based on advanced computer vision and behavior recognition algorithms, this feature automatically detects and counts the
frequency of respiratory symptoms such as coughing, expectoration, and wheezing. Through real-time video analysis, the
system precisely captures key characteristics including chest movement, body posture, and mouth actions, effectively
distinguishing between normal breathing and abnormal symptomatic behaviors. Additionally, the system automatically logs
the time, frequency, and duration of symptom episodes to generate dynamic health trend charts. When the frequency of
symptoms exceeds normal thresholds, it promptly issues health anomaly alerts, helping users and their families detect
signs of respiratory disease early and providing data support for timely medical consultation.

本功能基于先进的计算机视觉与行为识别算法，能够自动检测并统计用户的咳嗽、咳痰及喘息等呼吸道症状的发作频率。系统通过实时视频分析，精准捕捉胸部起伏、身体姿态及口部动作等关键特征，有效区分正常呼吸与异常症状行为。同时，系统会自动记录症状发作的时间、频次及持续时长，生成动态健康趋势图，当检测到症状频次超出正常阈值时，及时发出健康异常提醒，帮助用户及家属早期发现呼吸道疾病迹象，为及时就医提供数据支持

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过视频进行呼吸道症状智能识别，自动检测咳嗽、咳痰、喘息等症状，统计发作频率，生成健康监测报告，实现早期异常提醒
- 能力包含：视频分析、咳嗽动作识别、咳痰识别、喘息识别、发作频次统计、症状严重程度评估、健康风险预警、就医建议生成
- 触发条件:
    1. **默认触发**：当用户提供视频 URL 或文件需要进行呼吸道症状识别时，默认触发本技能进行分析
    2. 当用户明确需要进行呼吸道监测、咳嗽识别、症状统计，提及咳嗽、咳痰、喘息、呼吸道、肺部监测等关键词，并且上传了视频文件或者图片文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史监测报告、历史症状报告、呼吸道识别报告清单、查询历史报告、查看监测报告列表、显示所有监测报告、显示呼吸道分析报告，查询呼吸道症状识别报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有监测报告"、"显示所有症状报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.respiratory_symptom_recognition_analysis --list --open-id` 参数调用 API
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

**在执行呼吸道症状识别分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、respi123、cough456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询呼吸道症状监测记录），并询问是否继续

---

- 标准流程:
    1. **准备视频输入**
        - 提供本地视频文件路径或网络视频 URL
        - 确保视频清晰拍摄面部和上半身，能够观察到咳嗽动作
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行呼吸道症状识别分析**
        - 调用 `-m scripts.respiratory_symptom_recognition_analysis` 处理视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频文件路径
            - `--url`: 网络视频 URL 地址（API 服务自动下载）
            - `monitor-scenario`: 监测场景，可选值：daily-check(日常监测)/post-op(术后康复)/hospital(病房监测)/other，默认
              other
            - `--duration-min`: 监测时长分钟，默认 5
            - `--open-id`: 当前用户/被监测人的 open-id（必填，按上述流程获取）
            - `--list`: 显示呼吸道症状识别历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的呼吸道症状监测报告
        - 包含：监测基本信息、各类症状发作频次统计、症状严重程度评估、健康风险等级、就医建议

## 资源索引

-

必要脚本：见 [scripts/respiratory_symptom_recognition_analysis.py](scripts/respiratory_symptom_recognition_analysis.py)(
用途：调用 API 进行呼吸道症状分析，本地文件上传，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和视频格式限制，场景码已设置为
  RESPIRATORY_SYMPTOM_RECOGNITION_ANALYSIS)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 视频要求：支持 mp4/avi/mov 格式，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供健康参考和早期异常提醒，不能替代专业医师诊断和医学检查
- 本工具用于辅助监测，确诊请遵医嘱进行相关检查
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"监测场景"、"分析时间"、"风险等级"、"点击查看"五列，其中"报告名称"列使用`呼吸道症状监测报告-{记录id}`
  形式拼接，点击查看列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 监测场景 | 分析时间 | 风险等级 | 点击查看 |
  |----------|----------|----------|----------|----------|
  | 呼吸道症状监测报告 -20260312172200001 | 日常监测 | 2026-03-12 17:22:00 | 🟡
  轻度 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析日常监测视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.respiratory_symptom_recognition_analysis --input /path/to/monitor_video.mp4 --monitor-scenario daily-check --open-id openclaw-control-ui

# 分析术后康复监测视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.respiratory_symptom_recognition_analysis --input /path/to/recovery_video.mp4 --monitor-scenario post-op --open-id openclaw-control-ui

# 分析网络视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.respiratory_symptom_recognition_analysis --url https://example.com/respiratory_video.mp4 --monitor-scenario hospital --open-id openclaw-control-ui

# 显示历史分析报告/显示分析报告清单列表/显示历史监测报告（自动触发关键词：查看历史监测报告、历史报告、监测报告清单等）
python -m scripts.respiratory_symptom_recognition_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.respiratory_symptom_recognition_analysis --input video.mp4 --monitor-scenario daily-check --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.respiratory_symptom_recognition_analysis --input video.mp4 --monitor-scenario daily-check --open-id your-open-id --output result.json
```
