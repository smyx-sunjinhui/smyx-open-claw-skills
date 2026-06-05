---
name: "smyx-elderly-night-bed-exit-wandering-analysis"
description: "Using fixed cameras (infrared night vision) in nursing-home or home bedrooms, the system continuously monitors elderly bed-exit status and activity trajectory at night. It identifies bed-exit start time, total bed-exit duration, whether the person repeatedly walks back and forth in the hallway or room (wandering), and judges whether preset safety thresholds (e.g., bed-exit > 30 min or wandering > 10 min) are exceeded. Abnormal alerts are pushed to caregivers' phones or the nurse-station big screen to prevent wandering away, falls, or accidents. Application scenarios: nursing homes, home-based elderly care, community day-care centers. The system runs automatically at night; when bed-exit lasts too long or repeated wandering occurs, it pushes alerts via app or care system (e.g., 'Grandpa Zhang in bed 3 has been out of bed for 45 minutes, please check in time'). Skill features: prolonged night bed-exit (e.g., fall after getting up) and wandering (e.g., night roaming by elders with cognitive impairment) are high-risk events in elderly care. AI real-time monitoring enables timely warnings, reduces accidents, and improves caregiving efficiency. Can be integrated into nursing-home management systems or smart-home security platforms. | 通过养老院或居家卧室的固定摄像头（红外夜视），夜间连续监测老年人的离床状态和活动轨迹。识别离床开始时间、离床总时长、是否在走廊或房间内反复来回走动（徘徊），并判断是否超过预设的安全阈值（如离床>30分钟或徘徊>10分钟）。输出异常预警，可联动护理人员手机或护士站大屏，防止老人走失、跌倒或发生意外。应用场景：养老院、居家养老、社区日间照料中心。系统在夜间自动运行，当老人离床时间过长或出现反复徘徊行为时，通过APP或护理系统推送提醒（如'3号床张爷爷离床已45分钟，请及时查看'）。技能特点：夜间离床时间过长（如老人下床后跌倒无法起身）和徘徊（如认知障碍老人夜间游荡）是养老院和居家养老中的高风险事件。通过AI实时监测，可及时预警，减少意外发生，提升护理效率。该技能可集成到养老院管理系统或智能家居安防平台。"
version: "1.0.0"
---

# Elderly Night Bed-Exit & Wandering Detection | 老年人夜间离床时长与徘徊识别

Using fixed cameras (infrared night vision) in nursing-home or home bedrooms, the system continuously monitors elderly bed-exit status and activity trajectory at night. It identifies bed-exit start time, total bed-exit duration, whether the person repeatedly walks back and forth in the hallway or room (wandering), and judges whether preset safety thresholds (e.g., bed-exit > 30 min or wandering > 10 min) are exceeded. Abnormal alerts are pushed to caregivers' phones or the nurse-station big screen to prevent wandering away, falls, or accidents. Application scenarios: nursing homes, home-based elderly care, community day-care centers. The system runs automatically at night; when bed-exit lasts too long or repeated wandering occurs, it pushes alerts via app or care system (e.g., 'Grandpa Zhang in bed 3 has been out of bed for 45 minutes, please check in time'). Skill features: prolonged night bed-exit (e.g., fall after getting up) and wandering (e.g., night roaming by elders with cognitive impairment) are high-risk events in elderly care. AI real-time monitoring enables timely warnings, reduces accidents, and improves caregiving efficiency. Can be integrated into nursing-home management systems or smart-home security platforms.

通过养老院或居家卧室的固定摄像头（红外夜视），夜间连续监测老年人的离床状态和活动轨迹。识别离床开始时间、离床总时长、是否在走廊或房间内反复来回走动（徘徊），并判断是否超过预设的安全阈值（如离床>30分钟或徘徊>10分钟）。输出异常预警，可联动护理人员手机或护士站大屏，防止老人走失、跌倒或发生意外。应用场景：养老院、居家养老、社区日间照料中心。系统在夜间自动运行，当老人离床时间过长或出现反复徘徊行为时，通过APP或护理系统推送提醒（如'3号床张爷爷离床已45分钟，请及时查看'）。技能特点：夜间离床时间过长（如老人下床后跌倒无法起身）和徘徊（如认知障碍老人夜间游荡）是养老院和居家养老中的高风险事件。通过AI实时监测，可及时预警，减少意外发生，提升护理效率。该技能可集成到养老院管理系统或智能家居安防平台。

## 🎯 AI 角色

**假设你是一个专业的老年人夜间行为安全 AI。你的任务是分析卧室或走廊摄像头的夜间视频（红外模式），检测老年人是否离开床铺，记录离床的总时长，并识别是否存在反复来回走动的徘徊行为。当离床时长超过设定阈值（如 30 分钟）或徘徊持续时间超过阈值（如 10 分钟）时，输出异常预警。不要提供医疗诊断或具体护理操作方案，仅输出行为统计与报警信息。**

## 任务目标

- 本 Skill 用于：基于夜间红外卧室/走廊监控视频，检测老年人离床事件、累计离床时长与徘徊行为，并按安全阈值输出预警
- 能力包含：床铺区域分割、人体检测与跟踪、离床/上床事件识别、离床时长统计、活动轨迹分析、徘徊识别（反复来回走动）、安全阈值判定（默认离床>30 分钟 / 徘徊>10 分钟，可覆盖）、分级预警（none/info/warning/critical）、预警文本生成
- 触发条件:
    1. **默认触发**：当用户提供卧室/走廊夜间监控视频 URL 或文件需要分析时，默认触发本技能进行老年人夜间离床/徘徊识别
    2. 当用户明确提及离床、起夜、夜间起床、徘徊、夜游、卧室监控、夜间监护、养老院夜班、护士站、走失预警、跌倒风险等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看夜间离床历史报告、徘徊预警报告清单、夜间监护报告清单、查询历史离床记录、显示所有夜间离床报告、显示老人夜间监护诊断报告，查询夜间预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有夜间离床报告"、"
       显示所有徘徊预警报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --list --open-id` 参数调用 API
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

**在执行老年人夜间离床/徘徊识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备夜间监控视频输入**
        - 提供本地卧室/走廊夜间监控视频文件路径或网络 URL
        - 视频建议红外/低照度夜视模式，覆盖床铺与走廊出入口，时间范围建议覆盖整夜（如 22:00 - 次日 07:00）
        - 可选附带：床位编号、被监护人姓名、阈值覆盖（exit_duration_threshold_min / wandering_duration_threshold_min）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行夜间离床/徘徊识别**
        - 调用 `-m scripts.smyx_elderly_night_bed_exit_wandering_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地卧室/走廊夜间监控视频文件路径
            - `--url`: 网络夜间监控视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，老年人夜间监护场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示老年人夜间离床/徘徊历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的夜间离床/徘徊预警报告
        - 包含：离床事件列表（bed_exit_events，起止时间 + 持续秒数）、累计离床时长（total_exit_duration_min）、是否检测到徘徊（wandering_detected）、徘徊持续时长（wandering_duration_min）、预警等级（none/info/warning/critical）、预警文本（如"3 号床张爷爷离床已 45 分钟，请及时查看"）
        - **重要提示**：仅输出行为统计与预警信息，不提供医疗诊断或具体护理操作方案

## 资源索引

- 必要脚本：见 [scripts/smyx_elderly_night_bed_exit_wandering_analysis.py](scripts/smyx_elderly_night_bed_exit_wandering_analysis.py)(
  用途：调用 API 进行老年人夜间离床/徘徊识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、阈值定义和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议夜视模式、覆盖整夜时段
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 预警结果仅作为护理参考，疑似跌倒/失踪请立即人工核实并采取紧急行动
- 隐私合规：夜间卧室视频涉及个人隐私，使用前需取得被监护人或家属知情同意
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"床位/被监护人"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`夜间离床徘徊分析报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 床位/被监护人 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 夜间离床徘徊分析报告-20260312172200001 | 3号床-张爷爷 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地夜间监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --input /path/to/night_room.mp4 --open-id your-open-id

# 分析网络夜间监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --url https://example.com/night_room.mp4 --open-id your-open-id

# 显示历史夜间监护报告/徘徊预警报告清单（自动触发关键词：查看夜间离床历史报告、徘徊预警报告清单等）
python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --input night.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_elderly_night_bed_exit_wandering_analysis --input night.mp4 --open-id your-open-id --output result.json
```
