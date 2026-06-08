---
name: "smyx-pregnant-posture-fatigue-detection-analysis"
description: "通过家庭固定摄像头识别孕妇久站、弯腰等姿态，统计连续站立时长和弯腰频次，评估孕期劳累风险。发现久站、频繁弯腰或疑似重体力活动时，生成休息提醒，并可通过智能音箱或手机App推送。适用于家庭、孕妇学校、社区健康中心，可接入智能家居或孕期管理应用；结果仅供健康参考，不替代医生诊断。 | Uses a fixed home camera to detect prolonged standing, bending, and related posture of a pregnant woman, track standing duration and bending frequency, and assess fatigue risk. It sends rest reminders via speaker or app when tiring behavior is found. Suitable for homes, prenatal schools, community health centers, and smart-home or pregnancy apps. For health reference only, not medical diagnosis."
version: "1.0.1"
---

# Pregnant Prolonged-Standing / Over-Fatigue Detection | 孕妇久站/过度劳累识别

Using a fixed camera at home (e.g., living room, kitchen), the system analyzes a pregnant woman's posture in real time, detecting prolonged standing (continuous standing beyond a threshold, default 30 minutes) and frequent bending (bending frequency exceeding normal levels, e.g., > 10 times per hour), and assesses physical-exertion and fatigue risk. When prolonged standing or frequent bending is detected, it outputs a 'fatigue risk reminder' suggesting the pregnant woman sit down to rest or avoid heavy physical activity, preventing complications from over-fatigue during pregnancy. Application scenarios: pregnant women's homes, prenatal schools, community health centers. The system monitors in real time; when fatiguing behavior is detected, it pushes reminders via smart speaker or mobile app (e.g., 'You've been standing for 30 minutes, please sit down and rest'). Skill features: over-fatigue during pregnancy (prolonged standing, frequent bending) may increase the risk of preterm labor, placental abruption, lumbar/back pain, etc. AI real-time monitoring and reminders help pregnant women adjust activity intensity and protect maternal-infant health. Can be integrated into smart-home cameras or pregnancy-management apps as a caring assistant for expecting parents.

通过家庭固定摄像头（如客厅、厨房）实时分析孕妇的姿态，检测长时间站立（连续站立超过阈值，默认30分钟）以及频繁弯腰（弯腰频次超过正常值，如每小时>10次），评估孕妇的体力消耗和劳累风险。当出现久站或频繁弯腰时，输出'劳累风险提醒'，建议孕妇坐下休息或避免重体力活动，预防孕期过度疲劳引发的并发症。应用场景：孕妇家庭、孕妇学校、社区健康中心。系统实时监测，当检测到劳累行为时通过智能音箱或手机APP推送提醒（如'您已站立30分钟，建议坐下休息'）。技能特点：孕妇过度劳累（久站、频繁弯腰）可能增加早产、胎盘早剥、腰背疼痛等风险。通过AI实时监测并提醒，可帮助孕妇主动调整活动强度，保护母婴健康。该技能可集成到智能家居摄像头或孕期管理APP中，成为准父母的贴心助手。

## 🎯 AI 角色

**假设你是一个专业的孕期健康管理 AI。你的任务是分析固定摄像头（对准孕妇活动区域）的实时视频，检测孕妇的姿态，识别长时间站立（连续站立时长）和频繁弯腰的动作，评估劳累风险。不要提供医疗诊断或具体处方，仅输出基于视觉的姿态和动作统计与友好提醒。**

## 任务目标

- 本 Skill 用于：基于家庭固定摄像头视频，实时识别孕妇姿态 + 统计连续站立时长 + 弯腰频次 → 评估劳累风险并输出友好提醒
- 能力包含：人体检测与姿态分类（standing / sitting / squatting / bending / lying / walking）、连续站立计时与重置规则（短暂转身/换姿势不打断；有效坐下 ≥ 3 min 重置）、弯腰事件识别（基于躯干前倾角 + 髋部弯曲角度）、每小时/每日弯腰频次统计、疑似搬重物动作识别（参考）、提醒类型分类（prolonged_standing / frequent_bending / heavy_lifting_suspected / combined_fatigue_risk / normal）、智能音箱/APP 友好提醒文本生成
- 触发条件:
    1. **默认触发**：当用户提供家庭客厅/厨房等孕妇活动区域固定摄像头视频 URL 或文件需要分析时，默认触发本技能进行久站/过度劳累识别
    2. 当用户明确提及孕妇久站、频繁弯腰、孕期劳累、孕期保健、早产预防、胎盘早剥风险预防、腰背疼痛、孕期家务等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看孕妇久站历史报告、孕妇劳累提醒清单、孕期姿态报告清单、查询历史孕期劳累事件、显示所有孕妇久站报告、显示孕期健康诊断报告，查询孕期劳累预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有孕妇久站报告"、"
       显示所有孕妇劳累提醒报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --list --open-id` 参数调用 API
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

**在执行孕妇久站/过度劳累识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备孕妇活动区域固定摄像头视频输入**
        - 提供本地视频路径或网络 URL
        - 摄像头建议：家庭客厅/厨房/卧室固定摄像头，覆盖孕妇主要活动区域，**能看到全身轮廓**
        - 帧率 ≥ 5 FPS、分辨率 ≥ 480p、光照稳定
        - 多人场景下建议结合体型/外观特征锁定主目标（如孕妇穿着特定颜色），隐私敏感场景可启用人体轮廓模式
        - 可选附带：孕妇姓名、孕周、近期产检情况、阈值覆盖（continuous_standing_threshold_min / bending_per_hour_threshold）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行孕妇久站/过度劳累识别**
        - 调用 `-m scripts.smyx_pregnant_posture_fatigue_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地孕妇活动区域固定摄像头视频文件路径
            - `--url`: 网络孕妇活动区域固定摄像头视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，孕期健康管理场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示孕妇久站/过度劳累识别历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的孕妇久站/过度劳累识别报告
        - 包含：是否检测到孕妇（subject_detected）、当前姿态（posture：standing / sitting / squatting / bending / lying / walking）、当次会话指标（current_session：continuous_standing_duration_min / bending_event_count_hourly）、当日指标（daily_metrics：total_standing_duration_today_min / bending_event_count_daily / lifting_object_event_count_daily / sit_break_count_today）、提醒类型（alert_type：prolonged_standing / frequent_bending / heavy_lifting_suspected / combined_fatigue_risk / normal）、提醒级别（alert_level：info / notice / warning）、推送/语音播报文本（如"您已站立 30 分钟，建议坐下休息一会儿~"）、建议动作（recommend_action：voice_play_reminder / push_app_notice / suggest_rest / suggest_avoid_heavy_lifting / observe_only）
        - **重要提示**：仅输出基于视觉的姿态与动作统计与友好提醒，不提供早产 / 胎盘早剥 / 腰背疼痛 等具体医学诊断；如孕妇有腹痛、阴道出血、明显不适等情况请立即就医并由产科医生评估

## 资源索引

- 必要脚本：见 [scripts/smyx_pregnant_posture_fatigue_detection_analysis.py](scripts/smyx_pregnant_posture_fatigue_detection_analysis.py)(
  用途：调用 API 进行孕妇久站/过度劳累识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、久站/弯腰阈值/提醒类型定义和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；**关键**：画面必须能看到孕妇全身轮廓，否则姿态分类不可靠
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 弯腰事件可能因穿宽松孕妇装、镜头角度偏差等出现误识别；建议结合每日趋势综合判断
- 久站提醒的语气应温柔友好，避免造成孕妇心理负担；不应将本工具变成"监视"型工具
- 隐私合规：家庭孕妇视频涉及高度敏感个人隐私，使用前需取得孕妇本人明确知情同意，妥善加密保管；建议优先采用人体轮廓模式
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"提醒类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`孕妇久站过度劳累报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 提醒类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 孕妇久站过度劳累报告-20260312172200001 | combined_fatigue_risk（连续站立 35min + 1h 弯腰 12 次） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地孕妇活动区域视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --input /path/to/livingroom.mp4 --open-id your-open-id

# 分析网络孕妇活动区域视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --url https://example.com/livingroom.mp4 --open-id your-open-id

# 显示历史孕妇久站/过度劳累识别报告（自动触发关键词：查看孕妇久站历史报告、孕妇劳累提醒清单等）
python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --input lr.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_pregnant_posture_fatigue_detection_analysis --input lr.mp4 --open-id your-open-id --output result.json
```
