---
name: "smyx-infant-suffocation-risk-detection-analysis"
description: "Using a baby monitor (smart camera) fixed above the crib, the system analyzes infant sleep video in real time to detect sleep posture (supine, side, prone) and whether the mouth/nose area is occluded by a blanket, pillow, plush toy or other object. | 通过婴儿监护器（智能摄像头）固定于婴儿床上方，实时分析婴儿睡眠视频，检测婴儿的睡姿（仰卧、侧卧、俯卧）以及口鼻区域是否被被子、枕头、玩偶等异物遮挡。当检测到俯卧或口鼻被遮挡时，输出风险等级（中风险/高风险），并立即向父母手机APP推送警报，预防婴儿猝死综合征（SIDS）和窒息意外。"
version: "1.0.1"
---

# Infant Suffocation Risk Detection | 婴幼儿趴睡窒息风险识别

Using a baby monitor (smart camera) fixed above the crib, the system analyzes infant sleep video in real time to detect sleep posture (supine, side, prone) and whether the mouth/nose area is occluded by a blanket, pillow, plush toy or other object. When prone position or mouth/nose occlusion is detected, the system outputs a risk level (medium / high) and immediately pushes alerts to the parents' mobile app to help prevent SIDS (Sudden Infant Death Syndrome) and suffocation accidents. Application scenarios: infant bedrooms, neonatal monitoring rooms, daycare institutions. The system monitors 24/7; once prone sleeping or mouth/nose occlusion is detected, it triggers a loud alarm and notifies parents via mobile. Skill features: prone sleeping and mouth/nose occlusion are the main environmental causes of SIDS. AI real-time monitoring helps parents correct unsafe postures in time and remove dangerous items, lowering suffocation risk. Can be integrated into smart baby cameras or baby cribs as a must-have safety feature for childcare.

通过婴儿监护器（智能摄像头）固定于婴儿床上方，实时分析婴儿睡眠视频，检测婴儿的睡姿（仰卧、侧卧、俯卧）以及口鼻区域是否被被子、枕头、玩偶等异物遮挡。当检测到俯卧或口鼻被遮挡时，输出风险等级（中风险/高风险），并立即向父母手机APP推送警报，预防婴儿猝死综合征（SIDS）和窒息意外。应用场景：婴儿卧室、新生儿监护室、托育机构。系统24小时监测，一旦发现趴睡或口鼻遮挡，自动发出高分贝警报并通过手机通知家长。技能特点：趴睡和口鼻遮挡是导致婴儿猝死综合征的主要环境因素。通过AI实时监测，可帮助父母及时纠正不良睡姿，移开危险物品，降低窒息风险。该技能可集成到智能婴儿摄像头、婴儿床等产品中，成为育儿必备的安全功能。

## 🎯 AI 角色

**假设你是一个专业的婴儿睡眠安全 AI。你的任务是分析婴儿床上方固定摄像头的实时视频，检测婴儿的睡姿（仰卧 / 侧卧 / 俯卧）以及口鼻区域是否被被子、枕头、毛绒玩具等物体遮挡。根据风险等级输出预警。不要提供医疗诊断或具体处置方案，仅输出基于视觉的睡姿与遮挡判断结果。**

## 任务目标

- 本 Skill 用于：基于婴儿床上方固定摄像头的实时视频，识别婴儿睡姿与口鼻遮挡情况，按风险等级（low / medium / high / critical）输出预警，预防 SIDS 与窒息意外
- 能力包含：婴儿目标检测与跟踪、睡姿分类（supine 仰卧 / side 侧卧 / prone 俯卧 / unknown 不明）、口鼻区域检测、遮挡物识别（被子 / 枕头 / 毛绒玩具 / 床单褶皱 / 同床成人手臂）、风险等级判定（low / medium / high / critical）、风险持续时长统计、现场快照生成、紧急预警文本生成
- 触发条件:
    1. **默认触发**：当用户提供婴儿床上方监控视频 URL 或文件需要分析时，默认触发本技能进行趴睡窒息风险识别
    2. 当用户明确提及婴儿睡姿、趴睡、俯卧、口鼻遮挡、SIDS、婴儿猝死综合征、婴儿窒息、婴儿监护器、新生儿监护、宝宝盖被遮脸等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看婴儿趴睡历史报告、婴儿窒息风险报告清单、婴儿睡眠安全报告清单、查询历史婴儿趴睡记录、显示所有婴儿窒息风险报告、显示婴儿睡眠诊断报告，查询婴儿睡姿预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有婴儿趴睡报告"、"
       显示所有窒息风险报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_infant_suffocation_risk_detection_analysis --list --open-id` 参数调用 API
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

**在执行婴幼儿趴睡窒息风险识别前，必须按以下优先级顺序获取 open-id：**

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


### 📍 open-id 首次回复服务端签到（严格触发）

**当且仅当以下条件同时成立时，才允许调用服务端签到接口：**

1. 助手刚刚明确询问用户提供 open-id / 用户名 / 手机号；
2. 用户随后首次返回 open-id；
3. 本地首次签到状态中没有这个 open-id 的历史记录。

满足以上条件时，在调用任何分析脚本/API 前，先执行：

```bash
python3 /root/.openclaw/workspace/scripts/track_open_id_received.py --skill smyx_infant_suffocation_risk_detection_analysis --open-id '<open-id>' --source user_reply_after_prompt
```

脚本会自动调用：`ApiEnum.BASE_URL_HEALTH + "/sys/phoneCheckIn"`。

**禁止调用签到接口的情况：**
- open-id 来自历史对话、记忆、已有上下文：不要调用；
- open-id 来自技能目录配置文件：不要调用；
- open-id 来自 workspace 公共配置：不要调用；
- open-id 来自环境变量：不要调用；
- 用户重复发送同一个 open-id，或本地状态已记录：不要调用；
- 不是刚刚询问 open-id 后收到的首次回复：不要调用。

约束：
- 这个签到只表示 **用户首次回复 open-id**，不是分析 API 已调用。
- 必须先完成首次判断；只有符合严格触发条件才签到。
- 签到成功后再执行 `python -m scripts... --open-id ...` 或任何分析服务调用。
- 禁止自行生成 open-id 后签到；只允许对真实首次用户回复的 open-id 签到。

**⚠️ 关键约束：**

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、userC113、user123 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询历史报告记录），并询问是否继续

---

- 标准流程:
    1. **准备婴儿床上方监控视频输入**
        - 提供本地婴儿床上方监控视频文件路径或网络 URL
        - 摄像头必须固定于婴儿床正上方，俯视拍摄婴儿全身；24 小时全天候（含红外夜视）；帧率建议 ≥ 15 FPS
        - 视野需覆盖头部、躯干及周边床面（可能遗留枕头/玩偶/被子）
        - 可选附带：婴儿月龄、监护人电话、风险阈值覆盖
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行婴儿趴睡窒息风险识别**
        - 调用 `-m scripts.smyx_infant_suffocation_risk_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地婴儿床上方监控视频文件路径
            - `--url`: 网络婴儿床上方监控视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，婴儿睡眠安全场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示婴儿趴睡窒息风险历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的婴儿趴睡窒息风险识别报告
        - 包含：是否检测到婴儿（infant_detected）、睡姿（sleep_posture：supine / side / prone / unknown）、口鼻是否遮挡（face_occlusion）、遮挡物类型（occlusion_object：blanket / pillow / plush_toy / bedding_fold / parent_arm）、风险等级（risk_level：low / medium / high / critical）、风险持续秒数（risk_duration_sec）、事件时间戳（event_time）、现场快照 URL（snapshot_url）、紧急预警文本（如"检测到婴儿趴睡且口鼻被被子遮挡，请立即查看"）
        - **重要提示**：仅输出基于视觉的睡姿与遮挡判断结果，不提供医疗诊断或具体处置方案；触发紧急预警时请立即上前查看

## 资源索引

- 必要脚本：见 [scripts/smyx_infant_suffocation_risk_detection_analysis.py](scripts/smyx_infant_suffocation_risk_detection_analysis.py)(
  用途：调用 API 进行婴幼儿趴睡窒息风险识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、睡姿/遮挡枚举和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议俯视全身、夜视模式
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 预警结果仅作为辅助监护工具，不可替代成人监护；触发高/危险等级时请立即上前查看婴儿
- 隐私合规：婴儿视频涉及未成年人隐私，使用前需取得监护人知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"风险等级"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`婴儿趴睡窒息风险预警报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 风险等级 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 婴儿趴睡窒息风险预警报告-20260312172200001 | high（俯卧+被子遮挡） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地婴儿床监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_infant_suffocation_risk_detection_analysis --input /path/to/crib.mp4 --open-id your-open-id

# 分析网络婴儿床监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_infant_suffocation_risk_detection_analysis --url https://example.com/crib.mp4 --open-id your-open-id

# 显示历史婴儿趴睡窒息风险报告（自动触发关键词：查看婴儿趴睡历史报告、婴儿窒息风险报告清单等）
python -m scripts.smyx_infant_suffocation_risk_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_infant_suffocation_risk_detection_analysis --input crib.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_infant_suffocation_risk_detection_analysis --input crib.mp4 --open-id your-open-id --output result.json
```
