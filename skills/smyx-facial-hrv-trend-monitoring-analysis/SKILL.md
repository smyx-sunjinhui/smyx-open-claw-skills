---
name: "smyx-facial-hrv-trend-monitoring-analysis"
description: "Using everyday cameras (laptop camera, smartphone front camera, smart mirror), the system records 30-60 seconds of facial video and uses remote photoplethysmography (rPPG) to extract subtle color variations from facial skin micro-circulation, from which it computes heart-rate-variability (HRV) metrics including SDNN (standard deviation of all normal sinus RR intervals) and RMSSD (root mean square of successive RR-interval differences). It supports long-term trend analysis and generates daily/weekly HRV curves. Applicable to stress assessment, cardiovascular health monitoring and fatigue management. Application scenarios: home health monitoring, enterprise employee health management, physical-examination centers, smart mirrors. Users sit still in front of the camera for 1 minute daily; the system computes HRV and records the trend, pushing 'high stress' or 'fatigue accumulation' reminders when HRV drops significantly. Skill features: HRV is a key indicator of autonomic-nervous-system health, related to stress, fatigue and cardiovascular risk. Contact-free measurement via everyday cameras lowers the usage threshold and lets more people keep track of their recovery state and stress level. Can serve as a value-add feature in smart-office or smart elderly-care scenarios. | 通过日常摄像头（如电脑摄像头、手机前置摄像头）拍摄面部视频（30-60秒），利用光电容积描记技术（远程光电容积描记术，rPPG）提取面部皮肤微循环的微弱色度变化，从中计算心率变异性（HRV）指标，包括SDNN（全部正常窦性心搏间期的标准差）、RMSSD（相邻心搏间期差值的均方根）等。支持长期趋势分析，生成每日/每周HRV变化曲线。该技能可用于压力评估、心血管健康监测及疲劳管理。应用场景：居家健康监测、企业员工健康管理、体检中心、智能镜子。用户每日对着摄像头静坐1分钟，系统自动计算HRV并记录趋势，当HRV显著下降时推送'压力过大'或'疲劳累积'提醒。技能特点：HRV是衡量自主神经系统健康的重要指标，与压力、疲劳、心血管风险相关。通过日常摄像头无接触测量，可降低使用门槛，让更多人持续关注自身恢复状态和压力水平，助力健康管理。该技能可作为增值功能集成到智慧办公、健康养老等场景。"
version: "1.0.0"
---

# Adult Facial HRV Trend Monitoring (rPPG) | 成人心率变异性（HRV）趋势监测（面部）

Using everyday cameras (laptop camera, smartphone front camera, smart mirror), the system records 30-60 seconds of facial video and uses remote photoplethysmography (rPPG) to extract subtle color variations from facial skin micro-circulation, from which it computes heart-rate-variability (HRV) metrics including SDNN (standard deviation of all normal sinus RR intervals) and RMSSD (root mean square of successive RR-interval differences). It supports long-term trend analysis and generates daily/weekly HRV curves. Applicable to stress assessment, cardiovascular health monitoring and fatigue management. Application scenarios: home health monitoring, enterprise employee health management, physical-examination centers, smart mirrors. Users sit still in front of the camera for 1 minute daily; the system computes HRV and records the trend, pushing 'high stress' or 'fatigue accumulation' reminders when HRV drops significantly. Skill features: HRV is a key indicator of autonomic-nervous-system health, related to stress, fatigue and cardiovascular risk. Contact-free measurement via everyday cameras lowers the usage threshold and lets more people keep track of their recovery state and stress level. Can serve as a value-add feature in smart-office or smart elderly-care scenarios.

通过日常摄像头（如电脑摄像头、手机前置摄像头）拍摄面部视频（30-60秒），利用光电容积描记技术（远程光电容积描记术，rPPG）提取面部皮肤微循环的微弱色度变化，从中计算心率变异性（HRV）指标，包括SDNN（全部正常窦性心搏间期的标准差）、RMSSD（相邻心搏间期差值的均方根）等。支持长期趋势分析，生成每日/每周HRV变化曲线。该技能可用于压力评估、心血管健康监测及疲劳管理。应用场景：居家健康监测、企业员工健康管理、体检中心、智能镜子。用户每日对着摄像头静坐1分钟，系统自动计算HRV并记录趋势，当HRV显著下降时推送'压力过大'或'疲劳累积'提醒。技能特点：HRV是衡量自主神经系统健康的重要指标，与压力、疲劳、心血管风险相关。通过日常摄像头无接触测量，可降低使用门槛，让更多人持续关注自身恢复状态和压力水平，助力健康管理。该技能可作为增值功能集成到智慧办公、健康养老等场景。

## 🎯 AI 角色

**假设你是一个专业的生理信号分析 AI。你的任务是分析人脸面部视频，使用远程光电容积描记技术（rPPG）提取皮肤微循环波动信号，计算心率变异性（HRV）指标。输出 SDNN、RMSSD 以及长期趋势（需结合历史数据）。不要提供医疗诊断或临床心血管评估，仅输出基于信号处理的定量指标与趋势提示。**

## 任务目标

- 本 Skill 用于：基于 30-60 秒静坐面部视频，通过 rPPG 提取脉搏波 → 计算 HRV 指标 → 结合历史数据生成长期趋势
- 能力包含：面部检测 + ROI（前额/双颊）选择、RGB 时序提取、带通滤波 + POS/CHROM 算法提取 BVP、RR 间期序列、平均心率（HR）、SDNN、RMSSD、pNN50、LF/HF 比、信号质量评级（high / medium / low）、HRV 综合得分（0-100）、近 7 天趋势（rising / stable / declining）+ 变化百分比、压力/疲劳累积提示
- 触发条件:
    1. **默认触发**：当用户提供成人静坐 30-60 秒面部视频 URL 或文件需要分析时，默认触发本技能进行 HRV 趋势监测
    2. 当用户明确提及 HRV、心率变异性、SDNN、RMSSD、rPPG、远程光电容积描记、压力评估、自主神经、疲劳累积、智能镜子心率等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看 HRV 历史报告、心率变异性报告清单、面部 HRV 趋势报告清单、查询历史 HRV 记录、显示所有 HRV 报告、显示自主神经监测诊断报告，查询压力疲劳趋势预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有 HRV 报告"、"
       显示所有面部心率变异性报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --list --open-id` 参数调用 API
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

**在执行成人面部 HRV 趋势监测前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备成人静坐面部视频输入**
        - 提供本地 30-60 秒静坐面部视频文件路径或网络 URL
        - 摄像头建议：电脑/手机前置/智能镜子摄像头；正面平视、距离 30-60 cm、光照稳定
        - 视频帧率 **必须 ≥ 25 FPS（推荐 30 FPS）**、分辨率 ≥ 480p；测量期间保持静坐、避免说话/大幅运动
        - 可选附带：被检测人姓名、近期作息/运动/咖啡因摄入、本次测量场景（晨起 / 工作中 / 睡前）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行成人面部 HRV 趋势监测**
        - 调用 `-m scripts.smyx_facial_hrv_trend_monitoring_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地成人静坐面部视频文件路径（30-60秒）
            - `--url`: 网络成人静坐面部视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，rPPG 生理信号分析场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示成人面部 HRV 历史监测报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的 HRV 趋势监测报告
        - 包含：平均心率（heart_rate_bpm）、SDNN（sdnn_ms）、RMSSD（rmssd_ms）、pNN50（pnn50_pct）、LF/HF 比（lf_hf_ratio）、信号质量（signal_quality：high / medium / low）、HRV 综合得分（current_score，0-100）、近 7 天趋势（trend_7day：rising / stable / declining）+ 变化百分比（trend_change_pct）、压力/疲劳趋势提示（alert_message：如"近一周 HRV 持续下降 18%，可能压力/疲劳累积，建议休整"）
        - **重要提示**：仅输出基于 rPPG 信号处理的 HRV 定量指标与趋势提示，不提供心律失常、心血管疾病的医学诊断；如出现胸闷、心悸等症状请就医

## 资源索引

- 必要脚本：见 [scripts/smyx_facial_hrv_trend_monitoring_analysis.py](scripts/smyx_facial_hrv_trend_monitoring_analysis.py)(
  用途：调用 API 进行成人心率变异性（HRV）趋势监测（面部）分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 rPPG 流程、HRV 指标定义、信号质量和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；**关键**：帧率必须 ≥ 25 FPS，否则 HRV 指标可信度大幅下降
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- HRV 受运动、咖啡因、情绪、体位等多因素影响，建议每日同时段、同条件测量便于趋势纵向对比
- 检测结果仅作为个人健康趋势参考，本工具不替代心电图等医疗级心律评估，更不替代医生诊断
- 隐私合规：面部视频涉及生物特征隐私，使用前需取得本人同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"HRV(SDNN/RMSSD)"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`面部HRV趋势监测报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | HRV(SDNN/RMSSD) | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 面部HRV趋势监测报告-20260312172200001 | SDNN 38ms / RMSSD 26ms（近7天 ↓18%） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地 30-60 秒静坐面部视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --input /path/to/sit_30s.mp4 --open-id your-open-id

# 分析网络 30-60 秒静坐面部视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --url https://example.com/sit_30s.mp4 --open-id your-open-id

# 显示历史 HRV 趋势监测报告（自动触发关键词：查看 HRV 历史报告、心率变异性报告清单等）
python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --input sit.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_facial_hrv_trend_monitoring_analysis --input sit.mp4 --open-id your-open-id --output result.json
```
