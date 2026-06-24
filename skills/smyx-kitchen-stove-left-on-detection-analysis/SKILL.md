---
name: "smyx-kitchen-stove-left-on-detection-analysis"
description: "Using a fixed kitchen camera (must be able to capture the stove area), the system analyzes video in real time to detect whether there is human activity in the kitchen area, and at the same time identifies stove flames or heat sources (e.g., thermal/infrared features) to determine whether the gas stove is on. | 通过厨房固定摄像头（需能拍摄到灶台区域）实时分析视频，检测厨房区域内是否有人体活动，同时识别灶台火焰或热源（如红外特征）以判断燃气灶是否处于开启状态。当检测到厨房无人连续超过预设时间（默认10分钟）且灶火仍处于开启状态时，输出'忘关火'预警，可联动智能燃气阀自动关闭阀门，并推送提醒至家属或护理人员手机，预防火灾和燃气泄漏事故。"
version: "1.0.3"
---

# Kitchen Stove Left-On Detection | 老年人厨房忘关火识别

Using a fixed kitchen camera (must be able to capture the stove area), the system analyzes video in real time to detect whether there is human activity in the kitchen area, and at the same time identifies stove flames or heat sources (e.g., thermal/infrared features) to determine whether the gas stove is on. When the kitchen has been unattended for longer than a preset duration (default 10 minutes) while the stove flame is still on, the system outputs a 'stove left on' alert, can interoperate with a smart gas valve to close the valve automatically, and pushes alerts to family members or caregivers' mobile phones to prevent fires and gas leaks. Application scenarios: solo-living elder households, nursing home kitchens, community senior canteens. The system monitors continuously; once unattended flames are detected, it immediately raises an alarm and triggers valve shutdown. Skill features: elderly people may forget to turn off the stove due to memory decline, posing fire hazards. AI real-time monitoring + alerting effectively prevents accidents and protects life and property. Can be integrated into smart-home security systems or elderly-care monitoring platforms.

通过厨房固定摄像头（需能拍摄到灶台区域）实时分析视频，检测厨房区域内是否有人体活动，同时识别灶台火焰或热源（如红外特征）以判断燃气灶是否处于开启状态。当检测到厨房无人连续超过预设时间（默认10分钟）且灶火仍处于开启状态时，输出'忘关火'预警，可联动智能燃气阀自动关闭阀门，并推送提醒至家属或护理人员手机，预防火灾和燃气泄漏事故。应用场景：独居老人家庭、养老院厨房、社区老年食堂。系统持续监测，一旦发现灶火忘关且无人看管，立即发出警报并联动关阀。技能特点：老年人记忆力衰退，易忘记关火，造成火灾隐患。通过AI实时监测并报警，可有效预防事故，保障生命财产安全。该技能可集成到智能家居安防系统或养老监护平台中。

## 🎯 AI 角色

**假设你是一个专业的厨房安全监测 AI。你的任务是分析厨房固定摄像头的实时视频，检测厨房区域内是否有人（老年人），同时检测灶台火焰或热源（红外特征）以判断燃气灶是否开启。当厨房连续无人超过预设阈值（默认 10 分钟）且灶火仍开启时，输出忘关火预警。不要提供其他安全建议或具体处置方案，仅输出基于视觉的人员活动 + 灶火状态判断结果与预警信息。**

## 任务目标

- 本 Skill 用于：基于厨房固定摄像头视频，联合判定"厨房无人 + 灶火开启"持续超阈值的忘关火危险场景，并触发紧急预警与联动关阀建议
- 能力包含：厨房人体检测与活动统计、火焰特征识别（可见光）、热源识别（红外/热成像）、燃气灶开/关状态判定、无人看管时长统计、阈值判定（默认 10 分钟，可覆盖）、分级预警（none / info / warning / critical）、智能燃气阀联动关阀建议、紧急联系人通知建议
- 触发条件:
    1. **默认触发**：当用户提供厨房灶台区域监控视频 URL 或文件需要分析时，默认触发本技能进行忘关火识别
    2. 当用户明确提及忘关火、燃气灶、灶台、灶火、独居老人厨房安全、燃气泄漏预防、火灾预防、智能燃气阀、关阀等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看厨房忘关火历史报告、灶火监测报告清单、厨房安全报告清单、查询历史忘关火记录、显示所有厨房忘关火报告、显示厨房安全诊断报告，查询厨房忘关火预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有厨房忘关火报告"、"
       显示所有灶火监测报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --list` 调用 API
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
    1. **准备厨房灶台区域监控视频输入**
        - 提供本地厨房监控视频文件路径或网络 URL
        - 摄像头必须固定于厨房，能清晰拍摄到灶台区域；24 小时全天候采集（建议含红外/热成像通道）
        - 视频帧率建议 ≥ 10 FPS
        - 可选附带：被监护人姓名、阈值覆盖（unattended_flame_threshold_min）、智能燃气阀开关状态、紧急联系人列表
    2. **系统自动完成身份关联**
        - 无需用户输入任何身份参数
        - 不在回复中展示内部身份值
    3. **执行老年人厨房忘关火识别**
        - 调用 `-m scripts.smyx_kitchen_stove_left_on_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地厨房灶台区域监控视频文件路径
            - `--url`: 网络厨房灶台区域监控视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，厨房安全场景默认 `other`
            - `--list`: 显示厨房忘关火历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的厨房忘关火识别报告
        - 包含：厨房内是否有人（kitchen_person_present）、灶火状态（flame_status：on / off / unknown）、无人看管 + 灶火开启持续分钟（unattended_duration_min）、事件序列（event_history：person_in_kitchen / person_left_kitchen / flame_on / flame_off）、忘关火预警标志（unattended_flame_alert）、预警等级（none / info / warning / critical）、预警文本（如"厨房无人 12 分钟但灶火仍开启，请立即查看，建议关闭燃气阀"）、智能燃气阀联动建议（smart_valve_hint）
        - **重要提示**：仅输出基于视觉的人员活动 + 灶火状态判断与预警，不提供其他安全建议或具体处置方案

## 资源索引

- 必要脚本：见 [scripts/smyx_kitchen_stove_left_on_detection_analysis.py](scripts/smyx_kitchen_stove_left_on_detection_analysis.py)(
  用途：调用 API 进行老年人厨房忘关火识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、检测对象/阈值定义和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议覆盖灶台区域、含红外/热成像通道效果更佳
- 触发紧急预警时，请立即通过电话/上门方式人工核实，本工具仅作辅助监测，必要时联动智能燃气阀关阀
- 隐私合规：厨房视频涉及家庭隐私，使用前需取得被监护人或家属知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"灶火/无人时长"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`厨房忘关火预警报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 灶火/无人时长 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 厨房忘关火预警报告-20260312172200001 | 灶火 ON / 无人 12min | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地厨房灶台监控视频
python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --input /path/to/kitchen.mp4

# 分析网络厨房灶台监控视频
python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --url https://example.com/kitchen.mp4

# 显示历史厨房忘关火监测报告（自动触发关键词：查看厨房忘关火历史报告、灶火监测报告清单等）
python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --list

# 输出精简报告
python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --input kitchen.mp4 --detail basic

# 保存结果到文件
python -m scripts.smyx_kitchen_stove_left_on_detection_analysis --input kitchen.mp4 --output result.json
```
