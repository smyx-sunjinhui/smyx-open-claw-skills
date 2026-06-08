---
name: "smyx-living-alone-rhythm-anomaly-analysis"
description: "Using a fixed camera in the living room or bedroom of a person living alone, the system continuously analyzes night video (typically 22:00-06:00) to detect lights-off time (when light sources turn off) and early-morning activity (human movement or body motion between 0-6 AM). It builds a personal historical baseline (e.g., average lights-off time and early-morning activity frequency over the past 7-14 days). | 通过家庭客厅或卧室固定摄像头，夜间（通常指22:00-6:00）连续分析视频，检测熄灯时间（光源关闭的时刻）、凌晨活动（0-6点期间的人体移动或肢体动作）。建立个人历史基线（如过去7-14天的平均熄灯时间和凌晨活动频率），当当前熄灯时间比基线延迟超过2小时，或凌晨活动频次显著增加（如超出基线2个标准差）时，输出'作息规律异常'提醒。"
version: "1.0.1"
---

# Living-Alone Sleep Rhythm Anomaly Analysis | 独居者作息规律异常分析

Using a fixed camera in the living room or bedroom of a person living alone, the system continuously analyzes night video (typically 22:00-06:00) to detect lights-off time (when light sources turn off) and early-morning activity (human movement or body motion between 0-6 AM). It builds a personal historical baseline (e.g., average lights-off time and early-morning activity frequency over the past 7-14 days). When the current lights-off time is delayed more than 2 hours beyond the baseline, or early-morning activity frequency rises significantly (e.g., > baseline mean + 2 standard deviations), it outputs a 'rhythm anomaly' reminder. This helps family members or community workers monitor the sleep health of the person living alone and detect potential physiological or psychological issues (insomnia, anxiety, nocturnal delirium, etc.). Application scenarios: homes of elderly people living alone, single apartments, remote-care services. The system generates daily rhythm reports; when significant anomalies appear, it pushes reminders via the app, recommending family members or community-grid workers to call and check in. Skill features: disrupted sleep rhythm may be an early signal of physical illness (pain, increased nocturia) or mental issues (depression, anxiety). AI-based automatic analysis helps families spot anomalies early and proactively reach out, preventing condition deterioration. Can be integrated into home-care cameras or community grid-management platforms to enhance the safety and health support for people living alone.

通过家庭客厅或卧室固定摄像头，夜间（通常指22:00-6:00）连续分析视频，检测熄灯时间（光源关闭的时刻）、凌晨活动（0-6点期间的人体移动或肢体动作）。建立个人历史基线（如过去7-14天的平均熄灯时间和凌晨活动频率），当当前熄灯时间比基线延迟超过2小时，或凌晨活动频次显著增加（如超出基线2个标准差）时，输出'作息规律异常'提醒。该技能可辅助家属或社区人员关注独居者的睡眠健康，及时发现潜在的生理或心理问题（如失眠、焦虑、夜间谵妄等）。应用场景：独居老人家庭、单身公寓、远程照护服务。系统每日生成作息报告，当出现显著异常时通过APP推送提醒，建议家属或社区网格员电话关心。技能特点：作息规律紊乱可能是身体疾病（如疼痛、夜尿增多）或心理问题（抑郁、焦虑）的早期信号。通过AI自动分析，可帮助家人及早发现异常，主动关怀，避免病情恶化。该技能可集成到居家养老摄像头或社区网格化管理平台中，提升独居者的生活安全和健康保障水平。

## 🎯 AI 角色

**假设你是一个专业的独居者健康生活规律监测 AI。你的任务是分析卧室或客厅固定摄像头的夜间视频，检测熄灯时间（灯光关闭的时刻）以及凌晨时段（0-6 点）的人体活动（任何移动或肢体动作）。通过对比个人历史基线，判断作息是否出现显著异常。不要提供医疗诊断，仅输出基于视觉的作息参数和偏离提示。**

## 任务目标

- 本 Skill 用于：基于独居者卧室/客厅夜间监控视频，提取熄灯时间 + 凌晨活动 → 对比 7-14 天历史基线 → 输出作息规律异常提醒（供家属/社区主动关心）
- 能力包含：夜间画面亮度变化检测（熄灯时刻识别）、低光/红外人体活动检测、凌晨活动事件计数与累计时长、个人历史基线统计（均值/标准差）、当晚 vs 基线偏差计算（小时差 / Z-score）、异常类型分类（late_lights_off / frequent_early_morning_motion / prolonged_dark_motion / combined_rhythm_disruption）、连续异常天数累计、家属/社区提醒文本生成
- 触发条件:
    1. **默认触发**：当用户提供独居者卧室/客厅夜间视频 URL 或文件需要分析时，默认触发本技能进行作息规律异常分析
    2. 当用户明确提及独居老人作息、熄灯时间、凌晨活动、失眠预警、夜尿、夜间谵妄、抑郁焦虑预警、远程照护、社区网格化关怀等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看独居者作息历史报告、作息异常报告清单、熄灯/凌晨活动报告清单、查询历史作息记录、显示所有独居者作息报告、显示远程照护诊断报告，查询作息异常预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有独居者作息报告"、"
       显示所有作息异常报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --list --open-id` 参数调用 API
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

**在执行独居者作息规律异常分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备独居者卧室/客厅夜间视频输入**
        - 提供本地视频路径或网络 URL，建议覆盖 22:00 - 次日 06:00 完整夜间时段
        - 摄像头建议：客厅或卧室固定摄像头，覆盖独居者主要活动区域；**必须支持低光/红外/微光夜视模式**，能在熄灯后继续采集人体活动
        - 帧率 ≥ 5 FPS、隐私敏感场景应做马赛克处理（仅识别人体轮廓即可）
        - 可选附带：独居者姓名、年龄、近期身体状况、家属/网格员联系方式、基线时间窗（默认 7-14 天）、阈值覆盖
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行独居者作息规律异常分析**
        - 调用 `-m scripts.smyx_living_alone_rhythm_anomaly_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地独居者卧室/客厅夜间视频文件路径（建议覆盖 22:00-06:00）
            - `--url`: 网络独居者卧室/客厅夜间视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，独居者作息监测场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示独居者作息规律异常历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的独居者作息规律异常分析报告
        - 包含：是否检测到独居者（subject_detected）、当晚作息指标（current_metrics：lights_off_time / early_morning_motion_count / early_morning_motion_duration_sec）、历史基线指标（baseline_metrics：baseline_lights_off_time_avg / baseline_early_morning_motion_avg / baseline_early_morning_motion_std）、偏差（deviation：delta_lights_off_hours / motion_z_score）、异常类型（anomaly_type：late_lights_off / frequent_early_morning_motion / prolonged_dark_motion / combined_rhythm_disruption）、连续异常天数（anomaly_continuous_days）、提醒级别（alert_level：notice / warning / urgent）、推送给家属的文本（如"老人近 3 晚平均凌晨 2 点才熄灯，且凌晨活动次数从 1.2 上升至 5 次，建议电话关心是否睡眠/身体不适"）
        - **重要提示**：仅输出基于视觉的作息参数与偏离提示，不提供失眠症、抑郁症、夜间谵妄等具体医学诊断；连续异常建议由家属/社区/医生进一步评估

## 资源索引

- 必要脚本：见 [scripts/smyx_living_alone_rhythm_anomaly_analysis.py](scripts/smyx_living_alone_rhythm_anomaly_analysis.py)(
  用途：调用 API 进行独居者作息规律异常分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、基线/Z-score/异常阈值定义和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；**关键**：必须包含夜间时段，且摄像头支持低光/红外夜视
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 异常判定建议结合**连续天数**与个人基线，避免单次偶发触发误报；首次部署应先采集 7-14 天稳定基线
- 短期作息变化也可能由出差/家有客人/服药等正常事件造成，提醒侧建议由家属电话核实而非直接报警
- 隐私合规：独居者夜间监控视频涉及个人高度隐私，使用前需取得本人/监护人明确知情同意；建议优先采用人体轮廓识别模式，妥善加密保管
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"异常类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`独居者作息规律异常报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 异常类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 独居者作息规律异常报告-20260312172200001 | combined_rhythm_disruption（熄灯延迟 2.5h + 凌晨活动 z=2.8） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地独居者夜间视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --input /path/to/night_22_to_06.mp4 --open-id your-open-id

# 分析网络独居者夜间视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --url https://example.com/night_22_to_06.mp4 --open-id your-open-id

# 显示历史独居者作息规律异常报告（自动触发关键词：查看独居者作息历史报告、作息异常报告清单等）
python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --input night.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_living_alone_rhythm_anomaly_analysis --input night.mp4 --open-id your-open-id --output result.json
```
