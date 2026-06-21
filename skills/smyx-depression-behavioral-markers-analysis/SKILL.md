---
name: "smyx-depression-behavioral-markers-analysis"
description: "Using fixed home cameras (bedroom and dining area), the system analyzes the multi-day behavior pattern of elderly people or solo-living individuals, detecting daily lying-in-bed duration (continuous lying > 20 hours per day) and a sharp drop in eating frequency / duration (e.g., daily eating-action count below 50% of personal baseline). | 通过家庭固定摄像头（卧室和餐厅区域），分析老年人或独居者连续多日的行为模式，检测卧床时长（连续卧床超过20小时/天）以及进食频次/时长骤减（如每日进食动作次数低于历史基线的50%）。当这些行为变化持续超过设定天数（如3天）时，输出行为变化报告，提醒家属或社区医生关注可能存在的抑郁倾向或其他健康问题。"
version: "1.0.3"
---

# Depression Behavioral Markers (Long Immobility & Appetite Change) | 抑郁症辅助行为标记（长时间不动、食欲改变）

Using fixed home cameras (bedroom and dining area), the system analyzes the multi-day behavior pattern of elderly people or solo-living individuals, detecting daily lying-in-bed duration (continuous lying > 20 hours per day) and a sharp drop in eating frequency / duration (e.g., daily eating-action count below 50% of personal baseline). When these behavioral changes persist beyond a configured threshold (e.g., 3 days), the system outputs a behavioral-change report to remind family members or community doctors about possible depressive tendency or other health issues. This skill is ONLY a behavioral-observation aid and is NOT a medical diagnostic tool. Application scenarios: solo-living elderly homes, remote mental-health monitoring, community elderly care. The system generates a daily behavior summary and pushes alerts when an abnormal pattern is detected. Skill features: depression in the elderly often presents as decreased activity, reduced appetite, and increased bed time. AI auto-monitoring of these behavior changes can issue early signals before family or doctors notice, supporting timely intervention, reducing suicide risk, and improving quality of life. Can be integrated into home-care cameras or health-management platforms as a practical mental-health monitoring tool.

通过家庭固定摄像头（卧室和餐厅区域），分析老年人或独居者连续多日的行为模式，检测卧床时长（连续卧床超过20小时/天）以及进食频次/时长骤减（如每日进食动作次数低于历史基线的50%）。当这些行为变化持续超过设定天数（如3天）时，输出行为变化报告，提醒家属或社区医生关注可能存在的抑郁倾向或其他健康问题。该技能仅为行为观察辅助工具，不作为医学诊断依据。应用场景：独居老人家庭、精神健康远程监测、社区养老。系统每日生成行为摘要，当检测到异常行为模式时推送提醒。技能特点：老年人抑郁症常表现为活动减少、食欲下降、卧床时间增多。通过AI自动监测这些行为变化，可在家属或医生尚未察觉时发出早期信号，有助于及时干预，降低自杀风险，改善生活质量。该技能可集成到居家养老摄像头或健康管理平台中，成为精神健康监测的实用工具。

## 🎯 AI 角色

**假设你是一个专业的老年人行为健康监测 AI。你的任务是分析家庭固定摄像头（卧室和餐厅区域）的连续视频（至少 24 小时），检测卧床时长（统计一天内卧床总时长）以及进食行为（识别手部抓握餐具送入口中的动作次数和时长）。对比历史基线（过去 7-14 天的个人平均数据），当卧床时长超过 20 小时/天或进食动作次数/时长低于基线的 50% 时，输出行为变化报告。不要提供医疗诊断，仅输出基于视觉的行为统计和变化提示。**

## 任务目标

- 本 Skill 用于：基于家庭卧室 + 餐厅双区域固定摄像头连续 ≥ 24 小时视频，统计每日卧床总时长 + 离床事件次数 + 进食动作次数 + 进食总时长 + 完整餐次数 → 与个人 7-14 天基线对比 → 连续异常 ≥ 3 天 → 输出行为变化报告 + 家属/社区医生友好提醒
- 能力包含：人体检测 + 床位 ROI 卧位识别（lying_in_bed_duration_daily_min）、离床事件计数、手部抓握餐具送入口动作识别（eating_action_count_daily）、餐次数与进食总时长统计、餐后剩余食物比例估计（参考）、个人 7-14 天基线均值/标准差计算、连续异常天数累计、行为异常模式分类（hypersomnia_immobility / appetite_loss / both / none）、风险信号等级判定（none → strong_signal）、家属/社区医生友好提醒文本生成、心理援助热线参考
- 触发条件:
    1. **默认触发**：当用户提供家庭卧室 + 餐厅区域固定摄像头连续 ≥ 24 小时视频 URL 或文件需要分析时，默认触发本技能进行抑郁症辅助行为标记分析
    2. 当用户明确提及老年人抑郁、长时间卧床、食欲下降、不爱吃饭、整天躺着、活动减少、精神健康远程监测、社区养老心理等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看抑郁行为标记历史报告、卧床/食欲变化报告清单、行为变化报告清单、查询历史行为变化记录、显示所有抑郁辅助行为报告、显示精神健康行为诊断报告，查询抑郁辅助预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有抑郁辅助行为报告"、"
       显示所有卧床/食欲变化报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_depression_behavioral_markers_analysis --list --open-id` 参数调用 API
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

**在执行抑郁症辅助行为标记分析前，必须按以下优先级顺序获取 open-id：**

```
第 1 步：【最高优先级】检查技能所在目录的配置文件（优先）
        路径：scripts/config.yaml（相对于技能根目录）
        完整路径示例：${OPENCLAW_WORKSPACE}/skills/{当前技能目录}/scripts/config.yaml
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
    1. **准备家庭卧室 + 餐厅区域固定摄像头连续视频输入**
        - 提供本地视频路径或网络 URL，**单次分析必须 ≥ 24 小时连续记录**
        - 摄像头建议：**必须覆盖卧室区域（含床位）和餐厅区域（含餐桌）双视角**，可由两个摄像头分别覆盖
        - 帧率 ≥ 1 FPS、分辨率 ≥ 480p、夜间需配合红外或低照度增强
        - ROI 标定：床位 ROI（bed_region）+ 餐桌 ROI（dining_region）
        - 必须有 **7-14 天历史基线数据**，否则首次仅输出"基线累积中"状态
        - 隐私敏感场景必须启用人体轮廓 + 面部马赛克模式
        - 可选附带：老人姓名、年龄、近期重大事件（如丧偶/搬家）、阈值覆盖（卧床异常阈值 / 进食异常比例 / 连续异常天数）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行抑郁症辅助行为标记分析**
        - 调用 `-m scripts.smyx_depression_behavioral_markers_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地家庭固定摄像头（卧室+餐厅区域，≥24h）视频文件路径
            - `--url`: 网络家庭固定摄像头（卧室+餐厅区域，≥24h）视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，老年人行为健康监测场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示抑郁症辅助行为标记历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的抑郁症辅助行为标记报告
        - 包含：是否检测到老人（subject_detected）、基线是否就绪（baseline_ready）、当日卧床（daily_lying：lying_in_bed_duration_daily_min / out_of_bed_event_count_daily / room_movement_minutes_daily）、当日进食（daily_eating：eating_action_count_daily / eating_total_duration_min_daily / meal_event_count_daily / food_remained_ratio_estimate）、基线对比（baseline_comparison：baseline_window_days / lying_delta_vs_baseline_min / eating_action_ratio_vs_baseline）、连续异常天数（consecutive_abnormal_days）、行为异常模式（abnormal_pattern：hypersomnia_immobility / appetite_loss / both / none）、风险信号等级（risk_signal_level：none / mild_signal / notable_signal / strong_signal）、提醒类型（alert_type：behavioral_change_3day / behavioral_change_7day / improving / normal）、提醒级别（alert_level：info / notice / warning）、推送给家属/社区医生的友好文本（alert_message，如"妈妈最近 3 天每天卧床 ≥ 21 小时、进食动作较平时少了 60%，建议尽快电话关心或安排居家探视"）、建议动作（recommend_action：push_family_notice / suggest_visit / suggest_consult_doctor / observe_only）、心理援助热线参考（helpline_reference，strong_signal 时附）
        - **重要提示**：仅输出基于视觉的客观行为统计与友好提醒，**不构成抑郁症诊断、GDS-15 / PHQ-9 等量表评分或治疗方案**；任何抑郁症确诊与治疗必须由精神科医生评估制定；若老人出现明显自伤/自杀言语或行为，请立即联系心理援助热线（**北京心理危机研究与干预中心 010-82951332** / **全国心理援助热线 400-161-9995**）或当地急救机构

## 资源索引

- 必要脚本：见 [scripts/smyx_depression_behavioral_markers_analysis.py](scripts/smyx_depression_behavioral_markers_analysis.py)(
  用途：调用 API 进行抑郁症辅助行为标记分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、卧床/进食阈值/基线对比/异常模式与红线约束和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频；**关键约束**：单次分析必须 ≥ 24 小时连续记录，且必须同时覆盖卧室与餐厅
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 单日感冒、发烧、近期手术康复期、外出旅行等情形会显著影响卧床与进食指标，建议在配置中标记"非常态期"以暂停告警
- 老人在外用餐（如子女家、社区食堂）会导致 eating_action_count_daily 显著低估，需结合家庭日程综合判定
- 红线约束：**禁止**输出抑郁症诊断、量表评分（GDS-15 / PHQ-9）、用药建议或处方；**禁止**长期存储原始视频；**禁止**将"行为变化"等同于"确诊抑郁症"
- 当出现 `strong_signal` 或老人有任何自伤/自杀言语或行为时，**必须**在提醒中附**心理援助热线 010-82951332 / 400-161-9995**并强烈建议家属立即介入
- 隐私合规：卧室视频涉及高度敏感个人隐私，使用前需取得老人本人明确知情同意，妥善加密保管；建议优先采用人体轮廓 + 面部马赛克模式 + 仅保存指标统计
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"风险信号/异常模式"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`抑郁辅助行为标记报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 风险信号/异常模式 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 抑郁辅助行为标记报告-20260312172200001 | notable_signal / both（卧床 21h + 进食 -60%，连续 3 天） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地连续 24h+ 卧室+餐厅视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_depression_behavioral_markers_analysis --input /path/to/24h_home.mp4 --open-id your-open-id

# 分析网络连续 24h+ 卧室+餐厅视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_depression_behavioral_markers_analysis --url https://example.com/24h_home.mp4 --open-id your-open-id

# 显示历史抑郁症辅助行为标记报告（自动触发关键词：查看抑郁行为标记历史报告、行为变化报告清单等）
python -m scripts.smyx_depression_behavioral_markers_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_depression_behavioral_markers_analysis --input 24h.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_depression_behavioral_markers_analysis --input 24h.mp4 --open-id your-open-id --output result.json
```
