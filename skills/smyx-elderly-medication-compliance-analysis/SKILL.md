---
name: "smyx-elderly-medication-compliance-analysis"
description: "Using a fixed camera installed above or beside the home medication area, the system monitors the elderly person's full medication process in real time. With pose estimation and object detection, it recognizes three key steps: (1) picking up — hand takes a tablet/capsule out of the pill box; (2) to-mouth — hand brings the medication to the lips; (3) swallowing — throat/jaw movement indicating a swallow. When a step is missing (e.g., picked up but not brought to mouth, or brought to mouth but no swallow), the case is recorded as 'medication not completed' and an alert is pushed to family members or caregivers. This skill helps ensure chronic-disease elders take medication on time and in the right dose, preventing missed or wrong doses. Application scenarios: chronic-disease elder households, nursing homes, community rehab centers. The system auto-runs at scheduled medication times and generates compliance reports after each session. Skill features: missed/wrong doses are a major cause of poor chronic-disease control and ER visits among elders. AI verification of pick-up / to-mouth / swallow greatly improves medication safety and reduces medical cost. Can be integrated into smart-home cameras or elderly-care management systems as a key tool for chronic-disease management. | 通过家庭药箱区域上方或侧方的固定摄像头，实时监测老年人取药、服药的全过程，利用姿态估计和目标检测技术识别以下三个关键步骤：①取药（手从药盒中取出药片/胶囊）、②送入口中（手部将药物送至嘴边）、③吞咽（喉部运动或颈部吞咽动作）。当系统检测到缺步骤（例如取药后未送入口中，或送入口中后无吞咽）时，记录为'未完成服药'，并向家属或护理人员推送提醒。该技能有助于确保慢性病老人按时按量服药，防止漏服或错服。应用场景：老年慢性病家庭、养老院、社区康复中心。系统在设定的服药时间点自动开启监测，完成服药后生成依从性报告。技能特点：老年人漏服或错服药物是导致慢性病控制不佳和急诊入院的重要原因。通过AI自动确认取药、入口、吞咽三个步骤，可大幅提高用药安全性，降低医疗成本。该技能可集成到智能家居摄像头或养老管理系统中，成为慢性病管理的关键工具。"
version: "1.0.0"
---

# Elderly Medication Compliance (Pick-up / To-mouth / Swallow) | 老年人服药动作确认（取药/入口/吞咽）

Using a fixed camera installed above or beside the home medication area, the system monitors the elderly person's full medication process in real time. With pose estimation and object detection, it recognizes three key steps: (1) picking up — hand takes a tablet/capsule out of the pill box; (2) to-mouth — hand brings the medication to the lips; (3) swallowing — throat/jaw movement indicating a swallow. When a step is missing (e.g., picked up but not brought to mouth, or brought to mouth but no swallow), the case is recorded as 'medication not completed' and an alert is pushed to family members or caregivers. This skill helps ensure chronic-disease elders take medication on time and in the right dose, preventing missed or wrong doses. Application scenarios: chronic-disease elder households, nursing homes, community rehab centers. The system auto-runs at scheduled medication times and generates compliance reports after each session. Skill features: missed/wrong doses are a major cause of poor chronic-disease control and ER visits among elders. AI verification of pick-up / to-mouth / swallow greatly improves medication safety and reduces medical cost. Can be integrated into smart-home cameras or elderly-care management systems as a key tool for chronic-disease management.

通过家庭药箱区域上方或侧方的固定摄像头，实时监测老年人取药、服药的全过程，利用姿态估计和目标检测技术识别以下三个关键步骤：①取药（手从药盒中取出药片/胶囊）、②送入口中（手部将药物送至嘴边）、③吞咽（喉部运动或颈部吞咽动作）。当系统检测到缺步骤（例如取药后未送入口中，或送入口中后无吞咽）时，记录为'未完成服药'，并向家属或护理人员推送提醒。该技能有助于确保慢性病老人按时按量服药，防止漏服或错服。应用场景：老年慢性病家庭、养老院、社区康复中心。系统在设定的服药时间点自动开启监测，完成服药后生成依从性报告。技能特点：老年人漏服或错服药物是导致慢性病控制不佳和急诊入院的重要原因。通过AI自动确认取药、入口、吞咽三个步骤，可大幅提高用药安全性，降低医疗成本。该技能可集成到智能家居摄像头或养老管理系统中，成为慢性病管理的关键工具。

## 🎯 AI 角色

**假设你是一个专业的老年人用药安全 AI。你的任务是分析药箱区域固定摄像头的实时视频，检测老年人服药的完整动作流程。需识别三个关键步骤：取药（手从药盒中取出药物）、送入口中（药物接触口唇区域）、吞咽（颈部喉结运动或下颌运动）。若任一动作缺失，则判定为"未完成"。不要提供医疗建议或具体用药方案，仅输出步骤检测结果与依从性判断。**

## 任务目标

- 本 Skill 用于：基于药箱区域固定摄像头视频，识别老年人服药全过程中的取药/入口/吞咽三个关键步骤，自动判定服药依从性
- 能力包含：老人目标检测、药盒/药片识别、手部姿态估计、取药动作识别、送入口中动作识别（手到口轨迹）、吞咽动作识别（颈部喉结 / 下颌运动）、缺步骤检测、依从性判定（completed / partial_pickup_only / partial_no_swallow / not_observed / unknown）、提醒文本生成
- 触发条件:
    1. **默认触发**：当用户提供药箱区域服药全过程监控视频 URL 或文件需要分析时，默认触发本技能进行服药动作依从性确认
    2. 当用户明确提及服药、吃药、取药、漏服、吞咽、用药依从性、慢性病管理、老人吃药提醒、药盒、药片等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看服药依从性历史报告、用药依从性报告清单、老人服药动作报告清单、查询历史服药记录、显示所有服药动作报告、显示老人用药诊断报告，查询服药提醒清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有服药依从性报告"、"
       显示所有用药依从性报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_elderly_medication_compliance_analysis --list --open-id` 参数调用 API
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

**在执行老年人服药动作确认前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备药箱区域服药全过程监控视频输入**
        - 提供本地药箱区域监控视频文件路径或网络 URL
        - 摄像头建议安装于药箱上方或侧方，能同时拍摄到药盒、双手及口部/颈部
        - 视频帧率建议 ≥ 15 FPS，确保手部 + 吞咽动作捕捉
        - 可选附带：被监护人姓名、服药时间窗口、药品名称、家属/护理人员联系方式
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行老年人服药动作确认**
        - 调用 `-m scripts.smyx_elderly_medication_compliance_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地药箱区域服药全过程监控视频文件路径
            - `--url`: 网络药箱区域服药全过程监控视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，老人用药安全场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示老年人服药动作依从性历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的服药动作依从性报告
        - 包含：是否检测到老人（person_detected）、三步动作检测（step_1_pick_up_detected / step_2_to_mouth_detected / step_3_swallow_detected）及时间戳、依从性状态（compliance_status：completed / partial_pickup_only / partial_no_swallow / not_observed / unknown）、缺失步骤（missed_step）、整体置信度（confidence）、事件时间戳（event_time）、现场快照 URL（snapshot_url）、提醒文本（如"宝奶奶在 08:00 取药后未观察到吞咽，请家人确认"）
        - **重要提示**：仅输出基于视觉的服药动作步骤检测与依从性判断，不提供医疗建议或具体用药方案

## 资源索引

- 必要脚本：见 [scripts/smyx_elderly_medication_compliance_analysis.py](scripts/smyx_elderly_medication_compliance_analysis.py)(
  用途：调用 API 进行老年人服药动作确认（取药/入口/吞咽）分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、依从性枚举和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议拍摄完整服药过程（取药 → 入口 → 吞咽）
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 依从性结果仅作为用药辅助确认参考，本工具不替代医生用药指导；判定为"未完成"时请通过电话/上门方式人工核实
- 隐私合规：药箱区域视频涉及个人健康信息，使用前需取得被监护人或家属知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"依从性状态"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`服药依从性确认报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 依从性状态 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 服药依从性确认报告-20260312172200001 | partial_no_swallow（缺吞咽） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地服药全过程视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_medication_compliance_analysis --input /path/to/medication.mp4 --open-id your-open-id

# 分析网络服药全过程视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_medication_compliance_analysis --url https://example.com/medication.mp4 --open-id your-open-id

# 显示历史服药依从性报告（自动触发关键词：查看服药依从性历史报告、用药依从性报告清单等）
python -m scripts.smyx_elderly_medication_compliance_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_elderly_medication_compliance_analysis --input med.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_elderly_medication_compliance_analysis --input med.mp4 --open-id your-open-id --output result.json
```
