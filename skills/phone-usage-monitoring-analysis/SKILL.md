---
name: "phone_usage_monitoring_analysis"
description: "Based on computer vision, automatically detects employees playing with phones during work hours, supports real-time video stream and image detection, counts the duration and frequency of phone usage, helps enterprises standardize office order, and improves work efficiency. | 职场玩手机智能监测技能，基于计算机视觉自动检测工作时间员工玩手机行为，支持视频流和图片实时检测，统计玩手机时长与频次，帮助企业规范办公秩序，提升工作效率"
version: "1.0.6"
---

# Workplace Phone Usage Smart Monitoring Skill | 职场玩手机智能监测技能

Based on advanced computer vision and human pose estimation algorithms, this feature automatically detects and
identifies employees' phone usage during working hours. The system supports dual detection for both real-time video
streams and static images, effectively distinguishing between normal work operations and unauthorized phone usage by
precisely analyzing hand movements, device characteristics, and behavioral patterns. Additionally, the system
automatically tracks the duration and frequency of phone usage for each employee and generates visualized data reports,
enabling enterprises to monitor office discipline in real-time. This provides data support for standardizing employee
behavior and optimizing management strategies, thereby effectively improving overall work efficiency.

本功能基于先进的计算机视觉与人体姿态估计算法，能够在工作时间内自动检测并识别员工的玩手机行为。系统支持对实时视频流和静态图片进行双重检测，通过精准分析手部动作、设备特征及行为模式，有效区分正常工作操作与违规玩手机行为。同时，系统会自动统计每位员工的玩手机时长与频次，生成可视化数据报表，帮助企业实时掌握办公秩序状况，为规范员工行为、优化管理策略提供数据支撑，从而有效提升整体工作效率

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过办公区域监控视频/图片进行职场玩手机行为智能分析，自动识别工作时间员工玩手机行为，生成办公效率监测报告
- 能力包含：视频/图片分析、手机物体检测、玩手机行为识别、时长统计、频次分析、违规行为预警、办公效率建议生成
- 触发条件:
    1. **默认触发**：当用户提供监控视频/图片 URL 或文件需要检测玩手机行为时，默认触发本技能进行办公行为监测分析
    2. 当用户明确需要进行办公监测、玩手机检测、员工行为管理，提及玩手机监测、办公效率、员工行为监控、在岗状态检测等关键词，并且上传了视频文件或者图片文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史监测报告、历史效率报告、玩手机监测报告清单、查询历史报告、查看监测报告列表、显示所有监测报告、显示玩手机分析报告，查询办公行为监测报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有监测报告"、"显示所有效率报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.phone_usage_monitoring_analysis --list` 调用 API
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
    1. **准备视频/图片输入**
        - 提供本地视频/图片文件路径或网络媒体 URL
        - 确保监控画面清晰覆盖办公工位区域，光线充足
    2. **系统自动完成身份关联**
        - 无需用户输入任何身份参数
        - 不在回复中展示内部身份值
    3. **执行玩手机行为监测分析**
        - 调用 `-m scripts.phone_usage_monitoring_analysis` 处理文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络媒体 URL 地址（API 服务自动下载）
            - `--detection-type`: 检测类型，可选值：video(视频流检测)/image(图片检测)，默认 video
            - `--work-area`: 工作区域类型，可选值：open-office(开放办公)/cubicle(独立工位)/meeting-room(会议室)/other，默认
              other
            - `--list`: 显示历史玩手机监测分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的办公效率监测报告
        - 包含：监测区域信息、检测统计结果、玩手机行为识别数据、时长频次统计、违规行为预警、效率提升建议

## 资源索引

- 必要脚本：见 [scripts/phone_usage_monitoring_analysis.py](scripts/phone_usage_monitoring_analysis.py)(用途：调用 API
  进行玩手机行为分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和媒体格式限制，场景码已设置为
  PHONE_USAGE_MONITORING_ANALYSIS)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 格式支持：视频支持 mp4/avi/mov 格式，图片支持 jpg/png/jpeg 格式，最大 10MB
- 分析结果仅供企业内部管理参考，请注意保护员工个人隐私，遵守相关法律法规
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"检测类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`玩手机行为监测报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 检测类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 玩手机行为监测报告 -20260312172200001 | 视频检测 | 2026-03-12 17:22:
  00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析开放办公区视频
python -m scripts.phone_usage_monitoring_analysis --input /path/to/office_video.mp4 --detection-type video --work-area open-office 分析工位监控图片
python -m scripts.phone_usage_monitoring_analysis --input /path/to/office_image.jpg --detection-type image --work-area cubicle 分析网络视频流
python -m scripts.phone_usage_monitoring_analysis --url https://example.com/office_monitor.mp4 --detection-type video --work-area meeting-room 显示历史分析报告/显示分析报告清单列表/显示历史监测报告（自动触发关键词：查看历史监测报告、历史报告、监测报告清单等）
python -m scripts.phone_usage_monitoring_analysis --list

# 输出精简报告
python -m scripts.phone_usage_monitoring_analysis --input monitor.mp4 --detection-type video --detail basic

# 保存结果到文件
python -m scripts.phone_usage_monitoring_analysis --input image.jpg --detection-type image --output result.json
```
