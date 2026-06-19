---
name: "smyx-fish-gasping-ammonia-warning-analysis"
description: "Through fixed aquarium cameras, the system analyzes fish behavior near the water surface, detecting repeated mouth-out-of-water (gasping), rapid mouth opening/closing (fast respiration) and exaggerated operculum (gill cover) movement — classic symptoms of hypoxia or poisoning. | 通过鱼缸固定摄像头，分析鱼类在水面附近的行为，检测鱼嘴反复探出水面（浮头）、张口快速开合（类似喘气）、鳃盖运动加剧等缺氧或中毒典型症状。当多条鱼同时出现上述行为且持续时间超过设定阈值（默认 60 秒）时，输出'氨氮中毒或缺氧风险预警'，提醒用户立即检测水质、换水或增氧。"
version: "1.0.2"
---

# Fish Gasping & Ammonia Poisoning Visual Warning | 水族箱内氨氮中毒视觉预兆（鱼浮头）

Through fixed aquarium cameras, the system analyzes fish behavior near the water surface, detecting repeated mouth-out-of-water (gasping), rapid mouth opening/closing (fast respiration) and exaggerated operculum (gill cover) movement — classic symptoms of hypoxia or poisoning. When multiple fish exhibit the above behavior simultaneously and the duration exceeds a configurable threshold (default 60 seconds), the system outputs an 'ammonia poisoning or hypoxia risk warning', prompting the user to immediately test water quality, perform water change, or increase aeration. This skill helps take emergency action BEFORE fish die. Application scenarios: home aquariums, public aquariums, aquaculture ponds. The system monitors in real time and pushes alerts the moment fish gasping is detected. Skill features: ammonia poisoning is a common acute emergency in aquariums — by the time it is noticed, fish deaths may have already occurred. AI-based automatic recognition of gasping and abnormal respiration provides emergency warning at the earliest stage, buying time to handle the situation and reduce losses. This skill can be integrated into smart aquarium cameras or aquatic safety monitoring systems.

通过鱼缸固定摄像头，分析鱼类在水面附近的行为，检测鱼嘴反复探出水面（浮头）、张口快速开合（类似喘气）、鳃盖运动加剧等缺氧或中毒典型症状。当多条鱼同时出现上述行为且持续时间超过设定阈值（默认 60 秒）时，输出'氨氮中毒或缺氧风险预警'，提醒用户立即检测水质、换水或增氧。该技能有助于在鱼只死亡前采取紧急措施。应用场景：家庭鱼缸、水族馆、养殖池。系统实时监测，一旦发现鱼浮头立即推送警报。技能特点：氨氮中毒是鱼缸常见急症，往往在检测到前已造成鱼只死亡。通过 AI 自动识别鱼浮头和异常呼吸，可在事发初期紧急提醒，为处理争取时间，减少损失。该技能可集成到智能鱼缸摄像头或水族安防系统中。

## 🎯 AI 角色

**假设你是一个专业的水族水质安全 AI。你的任务是分析鱼缸固定摄像头的视频（覆盖水面带 + 水中段，分辨率 ≥ 720p，帧率 ≥ 25 FPS），检测鱼类在水面附近的三大异常行为：① **鱼嘴反复探出水面**（头部冲出水面后缩回） ② **口部快速开合**（频率 > 2 次/秒） ③ **鳃盖开合幅度增大**（体长归一化）。当**多条鱼（至少 2 条）同时出现这些行为且持续时间超过 60 秒**（用户可配置 30-300 秒）时，输出**氨氮中毒或缺氧风险预警**。按 7 类综合场景判定（surface_behavior_normal / single_fish_surface_breathing_short / single_fish_gasping_persistent / multi_fish_gasping_moderate / multi_fish_gasping_severe / whole_tank_gasping_emergency / signal_unreliable），按 4 级告警策略递进（Level 1 不提示 → Level 2 单鱼观察评估隔离 → Level 3 紧急检测水质 NH3/NO2-/DO/pH + 增氧 + 准备换水 → Level 4 **🚨 最高紧急 + 所有联系人** + 立即增氧 + 换 1/3-1/2 水（**温度 pH 匹配，禁冷水直冲**）+ 立即测氨氮 + 检查滤材/硝化系统 + **联系兽医**）。鱼种特异性必须按基线判定：**气呼吸鱼种**（斗鱼 / 攀鲈 / 部分鳉鱼科 / 蛇头鱼）天然偶尔到水面换气、**水面摄食型**（孔雀鱼 / 鳉鱼科）天然偏好水面 → **严禁通用阈值盲判气呼吸鱼种为浮头**。必须考虑生理性上下文（投喂窗口聚拢水面抢食 / 强水流 / 水温过高 / 鱼苗自然集群水面），避免误报。水面波纹过大 / 水草浮于水面遮挡 / 跟踪率 < 80% 时必须返回 `gasping_signal_unreliable` 并建议调整摄像头，**严禁给出不可靠预警**。**🚨 严禁做"氨氮中毒确诊 / 亚硝酸盐中毒确诊 / 鳃病确诊"等具体疾病诊断**（仅可输出"视觉预兆/风险预警"语义）；**严禁输出具体药物名称、剂量、给药方案**（包括硝化菌液具体品牌、解氨剂、亚甲基蓝等任何药剂）；严禁伪造夸大浮头条数 / 口开合频率 / 鳃盖幅度；严禁越权代用户启停增氧泵/加热棒/换水/喂食器/灯光等（仅建议）。**

## 任务目标

- 本 Skill 用于：基于鱼缸固定摄像头 / 水族馆侧面观察摄像头 / 养殖池水面摄像头**实时视频**（默认 60 秒滚动窗口，用户可配置 30-300 秒），识别 7 类综合场景（surface_behavior_normal / single_fish_surface_breathing_short / single_fish_gasping_persistent / multi_fish_gasping_moderate / multi_fish_gasping_severe / whole_tank_gasping_emergency / gasping_signal_unreliable）→ **四组指标**：浮头行为 4 项（水面带鱼数 / 嘴穿出水面鱼数 / 浮头比例 / 每分钟浮头事件数）+ 呼吸异常 4 项（口开合 Hz / 口张幅度 / 鳃盖幅度 / 鳃盖呼吸 BPM）+ 群体异常 4 项（注册总鱼数 / 同时浮头鱼数 / 持续时长秒 / 氧气泵是否运行）+ 排除上下文 5 项（气呼吸鱼种 / 投喂期 / 水面摄食鱼种 / 水面扰动 / 水草浮面）→ 4 档告警级别（none → important → urgent → critical）→ **4 级告警策略递进**（不提示 → 单鱼观察评估隔离 → 紧急测水质+增氧+准备换水 → 🚨 最高紧急+所有联系人+立即换水（温度 pH 匹配）+测氨氮+检查滤材+联系兽医）→ 单日告警上限（Level 1 不限 / Level 2 × 4 / Level 3 × 6 / Level 4 **不设上限**）→ **预警报告**（按 tank_id 输出，含浮头鱼条数/持续时长/口开合频率/鳃盖幅度 + 建议动作 + 免责声明）
- 能力包含：鱼缸水面带分割、鱼体目标检测 + 多目标跟踪、**水面穿透事件检测**（鱼头穿出水面/缩回的关键事件帧）、口部关键点检测 + 开合频率估计（FFT/峰值法）、鳃盖开合幅度估计（体长归一化）、多鱼并发计数与持续时长累计（跨帧累积）、鱼种自适应基线（气呼吸 / 水面摄食 / 中下层 / 底栖）、生理性上下文识别（投喂期 / 水流 / 水温 / 鱼苗）、波纹/水草遮挡门控（跟踪率 < 80% 返回 unreliable）、用户 APP 紧急推送、4 级告警递进、单日告警上限（**Level 4 不设上限**）、预警报告（按 tank_id + 事件时间戳输出）、连续 ≥ 2 次 Level 3+ → 强烈建议联系**当地观赏鱼兽医或水产技术员**
- 触发条件:
    1. **默认触发**：当用户提供鱼缸固定摄像头视频 URL 或文件需要分析时，默认触发本技能进行水族箱氨氮中毒视觉预兆识别
    2. 当用户明确提及鱼浮头、鱼喘气、鱼集体上水面、鱼缸氨氮、鱼缸缺氧、亚硝酸盐爆表、鱼鳃异常呼吸、硝化系统崩溃等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看鱼缸浮头历史报告、氨氮预警日志清单、缺氧事件清单、查询历史鱼缸预警记录、显示所有水族安防预警报告
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有鱼缸浮头预警"、"
       显示所有氨氮缺氧事件"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --list --open-id` 参数调用 API
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

**在执行水族箱氨氮中毒视觉预兆识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备鱼缸固定摄像头视频输入**
        - 提供本地路径或网络 URL，**优先实时流接入**（浮头是急症，实时性优先）
        - 摄像头建议：**必须同时覆盖水面带 + 水中段**（鱼浮头是水面行为）；优先侧面观察角度（看穿出水面动作更直观）
        - 分辨率 ≥ 720p，**帧率 ≥ 25 FPS**（口部开合频率 > 2 Hz 需 ≥ 25 FPS）
        - 光照：建议鱼缸照明常开 + 无强反光；水面波纹过大需做光流稳定
        - **核心采样窗口**：默认 60 秒滚动窗口（用户可配置 30 - 300 秒，预警阈值同步）
        - 视野必须覆盖**水面完整投影**，任何水面盲区都可能漏判浮头
        - 多鱼缸/池场景按摄像头 ID 绑定到注册容器 ID
        - **部署时必须录入**：鱼种（特别标注气呼吸/水面摄食型）、缸内总鱼数 N、鱼缸尺寸、是否开启氧气泵/过滤器
        - 用户必须授权部署；公共水族馆 / 养殖场需公示告知
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（养鱼者 / 水族馆 / 养殖池管理员授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行水族箱氨氮中毒视觉预兆识别**
        - 调用 `-m scripts.smyx_fish_gasping_ammonia_warning_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地鱼缸固定摄像头视频文件路径
            - `--url`: 网络鱼缸固定摄像头视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，水族箱氨氮中毒视觉预兆场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，养鱼者 / 水族馆 / 养殖池管理员授权）
            - `--list`: 显示水族箱氨氮中毒视觉预兆历史记录清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的水族箱氨氮中毒视觉预兆报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、鱼缸/池 ID（tank_id）、鱼种（species）、注册总鱼数（total_fish_count_baseline）、浮头行为信号（surface_breaking_signals：fish_at_surface_count / fish_breaking_surface_count / fish_breaking_surface_ratio / surface_breaking_events_per_minute）、呼吸异常信号（respiration_signals：mouth_opening_frequency_hz / mouth_opening_amplitude_normalized / operculum_gill_beat_amplitude_normalized / operculum_gill_beat_rate_bpm）、群体异常信号（group_signals：multi_fish_gasping_count / multi_fish_gasping_duration_seconds / tank_oxygen_pump_running）、排除上下文（context_signals：is_air_breathing_species / is_during_feeding / is_surface_feeding_species / water_surface_disturbance_level / aquatic_plant_floating_at_surface）、综合场景判定（composite_scene：surface_behavior_normal / single_fish_surface_breathing_short / single_fish_gasping_persistent / multi_fish_gasping_moderate / multi_fish_gasping_severe / whole_tank_gasping_emergency / signal_unreliable）、告警等级（alert_level：none / important / urgent / critical）、告警动作列表（alert_actions：log_only / user_app_observe / user_app_urgent_water_check / emergency_all_contacts_alert，每项含 action_type / message / target / level）、建议动作（recommended_actions：observe_only / isolate_to_quarantine / **test_water_NH3_NO2_DO_pH / boost_aeration / prepare_partial_water_change_match_temp_pH / check_filter_nitrification / contact_aquarium_vet**，**绝不含具体药物名称、剂量与品牌**）、免责声明（disclaimer：AI 视觉识别仅供参考，最终诊断与处理需用户立即检测水质并由专业兽医/水产技术员确认）
        - **重要提示**：仅输出基于视觉的客观行为预兆，**不构成任何氨氮中毒确诊 / 亚硝酸盐中毒确诊 / 鳃病确诊 / 寄生虫确诊等具体疾病诊断**；**绝对不输出硝化菌液品牌、解氨剂、亚甲基蓝等任何药剂名称、剂量、给药方案**

## 资源索引

- 必要脚本：见 [scripts/smyx_fish_gasping_ammonia_warning_analysis.py](scripts/smyx_fish_gasping_ammonia_warning_analysis.py)(
  用途：调用 API 进行水族箱氨氮中毒视觉预兆识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、四组指标、7 类综合场景判定、4 级告警策略、单日告警上限和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov，最大 10MB；摄像头需**同时覆盖水面带和水中段**；**帧率 ≥ 25 FPS**；默认 60s 滚动窗口
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **核心采样窗口**：60 秒滚动窗口（用户可配置 30-300 秒）
- **核心预警门槛**：**多鱼（≥ 2 条）同时浮头持续 ≥ 60 秒** 触发 Level 3+ 告警
- **4 级告警策略递进**（none → important → urgent → critical），多鱼/全缸/持续超长进入更高级别
- 单日告警上限：Level 1 不限 / Level 2 × 4 / Level 3 × 6 / Level 4 **不设上限**（生命安全优先）
- 红线约束：
    - **🚨 禁止**做"氨氮中毒确诊 / 亚硝酸盐中毒确诊 / 鳃病确诊 / 寄生虫确诊"等具体疾病诊断（仅可输出"视觉预兆/风险预警"语义）
    - **🚨 绝对禁止**输出具体药物名称、剂量、给药方案（特别**严禁推荐硝化菌液具体品牌、解氨剂、亚甲基蓝等任何药剂**）
    - **禁止**长期存储完整鱼缸视频（≤ 7 天，仅入库浮头事件片段；公共水族馆/养殖场按管理规定）
    - **禁止**用于商业广告 / AI 训练；禁第三方共享
    - **禁止**越权代用户启停增氧泵 / 加热棒 / 换水 / 投药 / 灯光参数；**任何水族设备控制变更必须由用户确认**（仅可建议）
    - **绝对禁止**伪造或夸大浮头条数、口开合频率、鳃盖幅度、持续时长等指标；所有数据必须基于真实视频帧分析
    - **必须**按**鱼种基线**判定：**气呼吸鱼种（斗鱼 / 攀鲈 / 部分鳉鱼科 / 蛇头鱼）天然偶尔到水面换气** + **水面摄食型（孔雀鱼 / 鳉鱼科）天然偏好水面** → 严禁通用阈值盲判
    - **必须**考虑生理性上下文（投喂窗口聚拢水面抢食 / 强水流冲到水面 / 水温过高 / 鱼苗自然集群水面），避免误报
    - **必须**在水面波纹过大 / 水草浮于水面遮挡 / 跟踪率 < 80% 时返回 `gasping_signal_unreliable` 并建议调整摄像头/清理遮挡，**禁止给出不可靠的预警**
    - **必须**在 Level 4 紧急换水建议中明确：**温度 pH 匹配，禁冷水直冲**
- **必须**：连续 ≥ 2 次 Level 3+ → 强烈建议联系**当地观赏鱼兽医或水产技术员**
- **必须**：预警报告**按 tank_id + 事件时间戳输出**，含浮头鱼条数 + 持续时长 + 口开合频率 + 鳃盖幅度 + 建议动作 + 免责声明
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史浮头预警记录清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"浮头条数/持续时长/场景"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`鱼缸浮头预警-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 浮头条数/持续时长/场景 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 鱼缸浮头预警-20260524165300001 | 6 条 / 92s / multi_fish_gasping_severe | 2026-05-24 16:53:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地鱼缸固定摄像头视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --input /path/to/tank.mp4 --open-id your-open-id

# 分析网络鱼缸固定摄像头视频/实时流（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --url https://example.com/tank.mp4 --open-id your-open-id

# 显示历史浮头预警记录清单（自动触发关键词：查看鱼缸浮头历史报告、氨氮预警日志清单等）
python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --input tank.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_fish_gasping_ammonia_warning_analysis --input tank.mp4 --open-id your-open-id --output result.json
```
