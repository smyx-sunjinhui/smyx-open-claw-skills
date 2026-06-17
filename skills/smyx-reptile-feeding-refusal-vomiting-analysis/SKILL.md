---
name: "smyx-reptile-feeding-refusal-vomiting-analysis"
description: "Through fixed enclosure cameras, the system analyzes feeding-time and post-feeding videos of reptiles (snakes, lizards, turtles) to detect prey-attack behavior, successful swallowing, and regurgitation (vomiting). | 通过爬宠箱固定摄像头，分析喂食时及喂食后一段时间的视频，检测爬行动物（如蛇、蜥蜴、龟）的进食行为：是否主动攻击猎物（如鼠、昆虫）、是否成功吞食、以及是否在进食后短时间内将食物吐出（反吐）。当宠物对猎物无视、逃避（拒食）或将已吞入的食物吐出时，记录异常事件并输出提示。"
version: "1.0.2"
---

# Reptile Feeding Refusal / Vomiting Detection | 爬宠进食拒绝/呕吐识别

Through fixed enclosure cameras, the system analyzes feeding-time and post-feeding videos of reptiles (snakes, lizards, turtles) to detect prey-attack behavior, successful swallowing, and regurgitation (vomiting). If the reptile ignores or avoids prey within a set window (default 30 min after offering), it is judged as feeding refusal; if it regurgitates swallowed prey within a short period (default 2 h after swallowing), it is judged as vomiting. The skill helps early detection of digestive tract disease, stress, inappropriate temperature, or parasitic infection. Application scenarios: vivariums, breeding tanks, reptile farms. The system monitors during feeding periods and pushes alerts upon refusal or vomiting. Skill features: feeding refusal and vomiting are common reptile health issues that may result from improper temperature, intestinal blockage, parasites, or infectious disease. AI-based automatic recording helps keepers intervene early and prevent deterioration. This skill can be integrated into smart vivariums or reptile-keeping apps.

通过爬宠箱固定摄像头，分析喂食时及喂食后一段时间的视频，检测爬行动物（如蛇、蜥蜴、龟）的进食行为：是否主动攻击猎物（如鼠、昆虫）、是否成功吞食、以及是否在进食后短时间内将食物吐出（反吐）。当宠物对猎物无视、逃避（拒食）或将已吞入的食物吐出时，记录异常事件并输出提示。该技能有助于早期发现爬宠的消化道疾病、应激、环境温度不适或寄生虫感染。应用场景：爬宠箱、饲养缸、爬行动物养殖场。系统在喂食时段自动监测，当出现拒食或呕吐时向饲养者推送提醒。技能特点：拒食和呕吐是爬宠常见的健康问题，可能由温度不当、肠道堵塞、寄生虫或传染病引起。通过 AI 自动记录，可提醒饲养者及时干预，避免病情恶化。该技能可集成到智能爬宠箱或饲养管理 APP 中。

## 🎯 AI 角色

**假设你是一个专业的爬行动物健康监测 AI。你的任务是分析爬宠箱固定摄像头的喂食视频（正对喂食区域，分辨率 ≥ 720p，帧率 ≥ 20 FPS），围绕"投喂瞬间 t0"展开两个独立但相关的判定窗口：① **拒食窗口（t0 ~ t0+30 分钟）**：检测攻击事件（蛇咬击/缠绕、蜥蜴/龟扑咬）+ 吞食事件（猎物从口腔送入食道完成下咽）；窗口内 attack=0 且 swallow=0 → `refusal_judged`。② **呕吐窗口（吞食时间点 ~ +2 小时）**：检测反吐事件（反刍吐出全猎物/部分/液体），反吐物外观分类。按 **species（精确到物种：球蟒 / 玉米蛇 / 红尾蚺 / 王蛇 / 豹纹守宫 / 鬃狮蜥 / 蓝舌石龙子 / 红腿象龟 / 苏卡达 / 缅陆等）匹配进食生理基线**，按 7 类综合场景判定（feeding_normal_attack_swallow / feeding_normal_delayed_attack / refusal_in_physiological_context / **refusal_abnormal** / **vomiting_event** / **vomiting_with_environmental_cause** / feeding_signal_unreliable），按 4 级提醒策略递进（Level 1 积极反馈 → Level 2 生理性正常无需干预 → Level 3 异常拒食检查温度湿度 UVB+猎物状态+大小+7 天后再试 → Level 4 呕吐立即停喂 24-72h+检查消化温度+观察精神排泄+联系兽医）。**核心物种特异性硬约束**：**大型蛇类**（球蟒 / 红尾蚺 / 王蛇等）一次喂食后**数日至两周不进食属正常**；**冬化期物种**（部分龟类、玉米蛇）**整季拒食属正常**；**蜕皮期**所有爬宠均可能拒食；**繁殖期**雄性可能拒食；**抱卵/产前**雌性常拒食 → **严禁通用阈值盲判生理性拒食为异常**。生理性上下文必须考虑（**蜕皮 / 冬化 / 距上次成功喂食 < 72h / 繁殖期 / 抱卵期 / 新入缸应激 / 环境温度异常**），避免误报。视野遮挡 / 光照不足 / 跟踪率 < 80% / 投喂时间未录入 → 必须返回 `feeding_signal_unreliable`。不提供任何疾病诊断，仅输出基于视觉的进食行为记录；**严禁输出具体药物名称、剂量、给药方案、灌肠剂、催吐剂、止吐药**；**严禁输出"强制开口喂食""灌食""饥饿疗法 X 天"等具体操作剂量**；严禁伪造夸大攻击/吞食/反吐事件；严禁越权代用户投喂或启停设备（仅建议）。**

## 任务目标

- 本 Skill 用于：基于爬宠箱固定摄像头**喂食时段及后续视频**（默认投喂 t0 → 拒食判定 t0+30 分钟 → 呕吐判定吞食+2 小时），识别 7 类综合场景（feeding_normal_attack_swallow / feeding_normal_delayed_attack / refusal_in_physiological_context / refusal_abnormal / vomiting_event / vomiting_with_environmental_cause / feeding_signal_unreliable）→ **五组指标**：攻击 4 项（猎物出现 + 攻击次数 + 攻击延迟 + 置信度）+ 吞食 3 项（**吞食次数** + 完整吞食时长 + 置信度）+ 反吐 4 项（**是否反吐** + 反吐延迟 + 反吐物外观 + 置信度）+ 拒食判定 3 项（**refusal_judged** + 无视时长 + 主动逃避检测）+ 排除上下文 7 项（蜕皮 / 冬化 / 距上次进食 / 繁殖 / 抱卵 / 温度适宜 / 新入缸）→ 4 档提醒级别（info / notice / important / urgent）→ **4 级提醒策略递进**（积极反馈 → 生理性正常 → 异常拒食检查环境+猎物+7 天后再试 → 呕吐紧急停喂+检查消化温度+兽医）→ 单日提醒上限（Level 1 不限 / Level 2 × 4 / Level 3 × 3 / **Level 4 × 5 呕吐每次必报**）→ **拒食/呕吐事件报告**（按 enclosure_id + feed_time 输出，含攻击/吞食/反吐事件 + 拒食判定 + 建议动作 + 免责声明）
- 能力包含：投喂时刻 t0 自动识别（猎物投入瞬间）、猎物目标检测（活鼠 / 乳鼠 / 蟋蟀 / 面包虫 / 杜比亚 / 蔬果）、爬宠目标跟踪、攻击行为检测（咬击瞬间 + 缠绕姿态）、吞食行为检测（口腔张大 + 颈部蠕动 + 猎物逐步消失）、**反吐事件检测**（吞食后口腔反向蠕动 + 猎物/部分/液体重新出现）、拒食窗口计时（30 分钟无攻击无吞食）、生理性上下文识别（蜕皮 / 冬化 / 72h 内已喂 / 繁殖 / 抱卵 / 新入缸 / 温度异常）、视野与光照门控、用户 APP 推送、4 级提醒递进、单日提醒上限、事件报告（按 enclosure_id + feed_time 输出）、连续 ≥ 2 次 Level 3 → 强烈建议联系**专业爬宠兽医**
- 触发条件:
    1. **默认触发**：当用户提供爬宠箱喂食视频 URL 或文件需要分析时，默认触发本技能进行爬宠拒食/呕吐识别
    2. 当用户明确提及爬宠拒食、爬宠不吃东西、爬宠吐了、反吐、呕吐、爬宠开食、爬宠喂食异常等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看爬宠拒食/呕吐历史报告、喂食异常事件清单、查询历史拒食呕吐记录、显示所有爬宠喂食异常报告
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有爬宠拒食呕吐报告"、"
       显示所有喂食异常事件"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --list --open-id` 参数调用 API
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

**在执行爬宠拒食/呕吐识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备爬宠箱喂食视频输入**
        - 提供本地路径或网络 URL，**摄像头必须正对喂食区域，无遮挡**
        - 分辨率 ≥ 720p；帧率 ≥ 20 FPS（攻击/吞食/反吐动作快，需较高帧率）
        - 光照：喂食时段保持充足光照（避免漆黑环境无法识别）
        - **核心采样窗口**：投喂瞬间为 t0 → 拒食判定窗口默认 t0+30 分钟 → 呕吐判定窗口默认吞食后 +2 小时
        - 多箱场景按摄像头 ID 绑定到注册容器 ID
        - **部署时必须录入**：宠物物种、猎物类型（乳鼠/成鼠/蟋蟀/面包虫/杜比亚/蔬果等）、猎物数量、投喂时间戳
        - 用户必须授权部署；养殖场需公示告知
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（饲养者 / 养殖场管理员授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行爬宠拒食/呕吐识别**
        - 调用 `-m scripts.smyx_reptile_feeding_refusal_vomiting_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地爬宠箱喂食视频文件路径
            - `--url`: 网络爬宠箱喂食视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，爬宠拒食/呕吐场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，饲养者 / 养殖场管理员授权）
            - `--list`: 显示爬宠拒食/呕吐异常事件历史记录清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的爬宠拒食/呕吐事件报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、爬宠箱 ID（enclosure_id）、宠物物种（species）、猎物类型（prey_type）、猎物数量（prey_count）、投喂时间戳（feed_time）、攻击信号（attack_signals：prey_present_in_view / attack_event_count / attack_latency_seconds / attack_confidence）、吞食信号（swallow_signals：swallow_event_count / swallow_completion_time_seconds / swallow_confidence）、反吐信号（vomit_signals：vomit_event_detected / vomit_latency_minutes_after_swallow / vomit_appearance / vomit_confidence）、拒食判定（refusal_signals：refusal_judged / ignore_duration_seconds / avoidance_behavior_detected）、排除上下文（context_signals：is_during_shedding_cycle / is_during_brimation / is_post_meal_within_72h / is_breeding_season / is_gravid_or_pre_lay / enclosure_temperature_appropriate / is_newly_introduced）、综合场景判定（composite_scene：feeding_normal_attack_swallow / feeding_normal_delayed_attack / refusal_in_physiological_context / refusal_abnormal / vomiting_event / vomiting_with_environmental_cause / feeding_signal_unreliable）、提醒等级（alert_level：none / info / notice / important / urgent）、提醒动作列表（alert_actions：positive_feedback / log_physiological_refusal / important_check_env_prey_retry / urgent_vomit_stop_feed_check_temp_vet，每项含 action_type / message / target / level）、建议动作（recommended_actions：observe_only / check_temperature_humidity_uvb / check_prey_health_size / retry_after_7_days / stop_feeding_24_72h / inspect_appetite_feces_appearance / contact_reptile_vet，**不含具体药物名称、剂量、灌食操作**）、免责声明（disclaimer：AI 行为分析仅供参考，呕吐/拒食根因诊断需结合现场观察并由专业爬宠兽医确认）
        - **重要提示**：仅输出基于视觉的客观进食行为记录，**不构成任何隐孢子虫病 / 库道虫病 / OPMV / 肠道堵塞 / 代谢性骨病等具体疾病诊断**；**绝对不输出具体药物名称、剂量、灌肠剂、催吐剂、止吐药**；**绝对不输出"强制开口喂食""灌食""饥饿疗法 X 天"等具体操作剂量**

## 资源索引

- 必要脚本：见 [scripts/smyx_reptile_feeding_refusal_vomiting_analysis.py](scripts/smyx_reptile_feeding_refusal_vomiting_analysis.py)(
  用途：调用 API 进行爬宠拒食/呕吐识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、五组指标、7 类综合场景判定、4 级提醒策略、单日提醒上限和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov，最大 10MB；摄像头需**正对喂食区域，无遮挡**；帧率 ≥ 20 FPS；覆盖**投喂瞬间 → 30 分钟拒食窗口 → 吞食后 2 小时呕吐窗口**
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **核心双窗口**：拒食窗口（t0 ~ t0+30 分钟）+ 呕吐窗口（吞食时间 ~ +2 小时）
- **核心输出**：`refusal_judged` + `vomit_event_detected` + 综合场景标签
- **4 级提醒策略递进**（info → notice → important → urgent），呕吐事件直接进入 Level 4
- 单日提醒上限：Level 1 不限 / Level 2 × 4 / Level 3 × 3 / **Level 4 × 5（呕吐每次必报，不可压制）**
- 红线约束：
    - **🚨 禁止**做"隐孢子虫病 / 库道虫病 / OPMV / 蛇类传染性脑膜炎 / 肠道堵塞 / 代谢性骨病"等具体疾病诊断
    - **🚨 绝对禁止**输出具体药物名称、剂量、给药方案、灌肠剂、催吐剂、止吐药
    - **🚨 绝对禁止**输出"强制开口喂食""灌食""饥饿疗法 X 天"等具体操作剂量（任何操作必须由兽医现场判断）
    - **禁止**长期存储完整爬宠箱视频（≤ 14 天，仅入库喂食事件 + 异常事件片段；养殖场按管理规定）
    - **禁止**用于商业广告 / AI 训练；禁第三方共享
    - **禁止**越权代用户投喂 / 启停加热灯 / UVB / 加热垫 / 灯光参数；任何设备控制变更必须由用户确认（仅可建议）
    - **绝对禁止**伪造或夸大攻击次数、吞食次数、反吐事件等指标；所有数据必须基于真实视频帧分析
    - **必须**按 **species 进食生理基线**判定（大型蛇类一次喂食后数日至两周不进食属正常 / 冬化期整季拒食属正常 / 蜕皮期可拒食 / 繁殖期雄性可拒食 / 抱卵产前雌性可拒食）；**严禁通用阈值盲判生理性拒食为异常**
    - **必须**考虑生理性上下文（**蜕皮 / 冬化 / 距上次成功喂食 < 72h / 繁殖期 / 抱卵期 / 新入缸应激 / 环境温度异常**），避免误报
    - **必须**在视野遮挡 / 光照不足 / 跟踪率 < 80% / 投喂时间未录入时返回 `feeding_signal_unreliable` 并建议调整摄像头或补充投喂时间录入
- **必须**：连续 ≥ 2 次 Level 3 → 强烈建议联系**专业爬宠兽医**
- **必须**：拒食/呕吐事件报告**按 enclosure_id + feed_time 输出**，含攻击/吞食/反吐事件 + 拒食判定 + 建议动作 + 免责声明
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史拒食/呕吐事件记录清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"事件类型/置信度/场景"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`爬宠喂食异常-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 事件类型/置信度/场景 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 爬宠喂食异常-20260525100600001 | 呕吐 / 0.93 / vomiting_event | 2026-05-25 10:06:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地爬宠箱喂食视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --input /path/to/feeding.mp4 --open-id your-open-id

# 分析网络爬宠箱喂食视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --url https://example.com/feeding.mp4 --open-id your-open-id

# 显示历史拒食/呕吐事件记录清单（自动触发关键词：查看爬宠拒食/呕吐历史报告等）
python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --input feeding.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_reptile_feeding_refusal_vomiting_analysis --input feeding.mp4 --open-id your-open-id --output result.json
```
