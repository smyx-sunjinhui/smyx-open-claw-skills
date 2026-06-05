---
name: "smyx-turtle-pneumonia-symptom-detection-analysis"
description: "Through fixed enclosure cameras, the system analyzes mouth and nasal videos of turtles to detect abnormally frequent open-mouth breathing in non-feeding states (mouth opening frequency unusually elevated), as well as the presence of mucus (reflective spots or strands) or nasal discharge around the mouth and nose. When any of these symptoms appear alone or together, the system outputs a 'pneumonia risk warning', prompting the keeper to check environmental temperature and water quality and isolate/treat promptly. This skill helps early detection of respiratory infections in turtles and reduces mortality. Application scenarios: home turtle tanks, breeding ponds, animal hospitals. The system monitors in real time and pushes alerts when abnormal breathing behavior is detected. Skill features: turtle pneumonia has high mortality, and early symptoms (open-mouth breathing, mucus) are often overlooked. AI-based automatic monitoring helps keepers detect and intervene early, improving cure rate. This skill can be integrated into smart turtle-tank cameras or reptile health management apps. | 通过龟缸固定摄像头，分析龟类的口鼻部视频，检测龟在非进食状态下（未摄食时）口部频繁开合（张嘴呼吸，频率异常增高），以及口鼻区域是否有黏液（反光点或丝状物）或鼻腔分泌物。当同时或单独出现上述症状时，输出'肺炎风险提示'，提醒饲养者检查环境温度、水质，并及时隔离治疗。该技能有助于早期发现龟类的呼吸道感染，降低死亡率。应用场景：家庭龟缸、养殖池、宠物医院。系统实时监测，当检测到异常呼吸行为时推送提醒。技能特点：龟类肺炎死亡率高，早期症状（张嘴呼吸、黏液）常被忽视。通过 AI 自动监测，可帮助饲养者及早发现并干预，提高治愈率。该技能可集成到智能龟缸摄像头或爬宠健康管理 APP 中。"
version: "1.0.0"
---

# Turtle Pneumonia Symptom (Open-Mouth Breathing) Detection | 龟类张嘴呼吸（肺炎征兆）识别

Through fixed enclosure cameras, the system analyzes mouth and nasal videos of turtles to detect abnormally frequent open-mouth breathing in non-feeding states (mouth opening frequency unusually elevated), as well as the presence of mucus (reflective spots or strands) or nasal discharge around the mouth and nose. When any of these symptoms appear alone or together, the system outputs a 'pneumonia risk warning', prompting the keeper to check environmental temperature and water quality and isolate/treat promptly. This skill helps early detection of respiratory infections in turtles and reduces mortality. Application scenarios: home turtle tanks, breeding ponds, animal hospitals. The system monitors in real time and pushes alerts when abnormal breathing behavior is detected. Skill features: turtle pneumonia has high mortality, and early symptoms (open-mouth breathing, mucus) are often overlooked. AI-based automatic monitoring helps keepers detect and intervene early, improving cure rate. This skill can be integrated into smart turtle-tank cameras or reptile health management apps.

通过龟缸固定摄像头，分析龟类的口鼻部视频，检测龟在非进食状态下（未摄食时）口部频繁开合（张嘴呼吸，频率异常增高），以及口鼻区域是否有黏液（反光点或丝状物）或鼻腔分泌物。当同时或单独出现上述症状时，输出'肺炎风险提示'，提醒饲养者检查环境温度、水质，并及时隔离治疗。该技能有助于早期发现龟类的呼吸道感染，降低死亡率。应用场景：家庭龟缸、养殖池、宠物医院。系统实时监测，当检测到异常呼吸行为时推送提醒。技能特点：龟类肺炎死亡率高，早期症状（张嘴呼吸、黏液）常被忽视。通过 AI 自动监测，可帮助饲养者及早发现并干预，提高治愈率。该技能可集成到智能龟缸摄像头或爬宠健康管理 APP 中。

## 🎯 AI 角色

**假设你是一个专业的爬行动物呼吸道健康监测 AI。你的任务是分析龟缸固定摄像头的视频（正对头颈部或侧前 30°，分辨率 ≥ 1080p——口鼻黏液为丝状/反光点细节需高清，帧率 ≥ 25 FPS——开合动作快需高帧率），围绕"非进食状态下的呼吸征兆"展开三组核心检测：① **口部开合频率**：每分钟开合次数 + 平均时长 + 开合幅度，**> 10 次/分钟触发风险门槛**；② **口鼻黏液与分泌物**：口腔内反光点 + 丝状物 + 鼻孔气泡 + 鼻分泌物（透明清涕 / 黄脓 / 血染）；③ **呼吸节律与姿态**：头颈持续伸展不缩 + **张嘴+头颈伸展典型肺炎姿态** + 水栖龟漂浮倾斜（肺部积液浮力不平衡，肺炎晚期强信号） + 嗜睡评分。按 **species 适宜温度基线**（热带物种苏卡达/缅陆适温高、温带物种草龟/黄缘适温中等、深水龟 vs 浅水龟节律不同）匹配，按 7 类综合场景判定（respiration_normal / respiration_mild_anomaly / **pneumonia_risk_mild** / **pneumonia_risk_moderate** / **pneumonia_risk_severe** / respiration_within_basking_context / respiration_signal_unreliable），按 4 级提醒策略递进（Level 1 入库 → Level 2 复测水温气温+加强观察 24-48h → Level 3 立即升温至物种推荐高线+隔离温暖干燥箱+干养+联系兽医 → Level 4 **🚨 立即干养+升温保暖+联系兽医**——肺炎急症可短期致死）。**核心生理性上下文必须排除**：**进食时口部开合属正常**（必须排除进食窗口，由用户标注或自动识别）；**水栖龟水下口部开合为换气吐泡属正常**（必须等浮出水面或晒台时分析）；**晒背蒸发期可能开口属正常**；**消化期呼吸短促**。物种特异性硬约束：必须按物种适宜温度基线判定（**严禁通用阈值盲判**）。头部缩入壳内 / 水栖全程水下 / 图像模糊 / 光照不足 / 进食时段未排除 / 分辨率 < 1080p → 必须返回 `respiration_signal_unreliable`。不提供任何疾病诊断，仅输出基于视觉的异常体征识别；**🚨 严禁输出具体药物名称、剂量、抗生素品牌、抗真菌药品牌、肌注剂量、口服剂量**；**🚨 严禁输出"打恩诺沙星 X mg/kg""口服阿莫西林""注射头孢拉定""灌服板蓝根"等具体处方**；**🚨 严禁输出"具体升温到 N℃ 持续 N 天"等精确温度疗法**（仅可提示"水温/气温调至物种推荐高线，由用户根据物种手册设定"）；严禁伪造夸大开合频率与黏液检测；严禁越权代用户启停加热棒/UVB/干养水养切换（仅建议）。**

## 任务目标

- 本 Skill 用于：基于龟缸固定摄像头 / 智能龟缸内置摄像头 / 养殖池水面摄像头 / 宠物医院诊查摄像头**实时视频**（默认 ≥ 3 分钟连续观察，必须排除进食窗口），识别 7 类综合场景（respiration_normal / respiration_mild_anomaly / pneumonia_risk_mild / pneumonia_risk_moderate / pneumonia_risk_severe / respiration_within_basking_context / respiration_signal_unreliable）→ **四组指标**：口部开合 6 项（**每分钟开合次数** + 单次时长 + 开合幅度 + 置信度 + 进食窗口 + 水下状态）+ 口鼻黏液与分泌物 6 项（**口腔黏液** + 丝状物数量 + **鼻分泌物** + 颜色 + 鼻孔气泡 + 综合置信度）+ 呼吸节律与姿态 5 项（呼吸频率 + 头颈持续伸展 + **张嘴+头颈伸展典型肺炎姿态** + **水栖漂浮倾斜** + 嗜睡评分）+ 排除上下文 6 项（环境温度 + 水温适宜 + 进食 30min 内 + 晒背中 + 蜕皮期 + 繁殖期）→ 4 档提醒级别（info / important / urgent / critical）→ **4 级提醒策略递进**（入库 → 复测水温气温+观察 → 升温隔离干养+联系兽医 → 🚨 立即干养升温+紧急联系兽医）→ 单日提醒上限（Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限**）→ **肺炎风险预警报告**（按 enclosure_id + individual_id + 事件时间戳输出，含开合频率 + 黏液鼻分泌物 + 姿态评分 + 建议动作 + 免责声明）
- 能力包含：龟头部精准定位与跟踪、口部张合事件检测（嘴角张开 + 喙缘距离 + 时间序列）、黏液丝状物检测（颗粒形态学 + 反光点过滤）、鼻分泌物检测（鼻孔局部色差 + 气泡检测）、头颈伸展度量、水栖龟漂浮姿态识别（壳体倾斜角 + 肺部积液浮力不平衡推断）、嗜睡评分、生理性上下文识别（进食 / 水下 / 晒背 / 消化 / 蜕皮 / 繁殖）、物种适宜温度门控、图像质量门控（头部缩壳 / 全程水下 / 模糊 / 光照 → unreliable）、用户 APP 紧急推送、4 级提醒递进、单日提醒上限（**Level 4 不设上限**）、肺炎风险预警报告（按 enclosure_id + individual_id 输出）、连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业爬宠兽医**（X 光 + 肺部听诊 + 鼻分泌物镜检/培养）
- 触发条件:
    1. **默认触发**：当用户提供龟缸固定摄像头视频 URL 或文件需要分析时，默认触发本技能进行龟类肺炎征兆识别
    2. 当用户明确提及龟张嘴呼吸、龟伸脖子、龟漂浮、龟鼻涕、龟黏液、龟呼吸困难、龟肺炎、URI 等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看龟类肺炎预警历史报告、肺炎风险事件清单、查询历史呼吸异常记录、显示所有龟肺炎报告
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有龟肺炎预警"、"
       显示所有呼吸异常事件"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --list --open-id` 参数调用 API
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

**在执行龟类肺炎征兆识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备龟缸固定摄像头视频输入**
        - 提供本地路径或网络 URL，**摄像头必须正对头颈部**或侧前 30°
        - 分辨率 ≥ 1080p（口鼻黏液丝状/反光点需高清）；帧率 ≥ 25 FPS（开合快需高帧率）
        - 光照：充足且均匀（避免反光误判为黏液 / 避免阴影遮挡口鼻）
        - **核心采样窗口**：默认 ≥ 3 分钟连续观察
        - **必须排除进食时段**（进食时口部开合属正常，由用户标注或自动识别投喂窗口排除）
        - **水栖龟必须浮出水面或晒台上**时分析（水下开合为换气吐泡属正常）
        - 多缸场景按摄像头 ID + 个体 ID 双重绑定
        - **部署时必须录入**：物种（巴西龟 / 草龟 / 黄缘 / 黄喉 / 鳄龟 / 苏卡达 / 缅陆 / 印星等）、龟类型（水栖/半水栖/陆栖）、水温（水栖必填）、气温、UVB 状态、上次投喂时间戳
        - 用户必须授权部署；养殖场/医院按管理规定
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（饲养者 / 养殖场 / 宠物医院授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行龟类肺炎征兆识别**
        - 调用 `-m scripts.smyx_turtle_pneumonia_symptom_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地龟缸固定摄像头视频文件路径
            - `--url`: 网络龟缸固定摄像头视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，龟类肺炎征兆场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填）
            - `--list`: 显示龟类肺炎风险预警历史记录清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的龟类肺炎风险预警报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、龟缸 ID（enclosure_id）、个体 ID（individual_id）、物种（species）、龟类型（turtle_type：aquatic / semi_aquatic / terrestrial）、水温（water_temperature_c）、气温（ambient_temperature_c）、口部开合信号（mouth_opening_signals：mouth_opening_events_per_minute / mouth_opening_duration_ms_avg / mouth_opening_amplitude_normalized / mouth_opening_confidence / is_during_feeding_window / is_underwater_aquatic）、口鼻黏液与分泌物信号（mucus_nasal_signals：mucus_detected_in_mouth / mucus_strand_count / nasal_discharge_detected / nasal_discharge_color / bubble_at_nostril / mucus_nasal_confidence）、呼吸节律与姿态信号（posture_signals：breathing_rate_per_minute / neck_extension_persistent / gaping_with_neck_extension / floating_tilted_aquatic / lethargy_score_0_5）、排除上下文（context_signals：ambient_temperature_c / water_temperature_appropriate / is_post_feeding_within_30min / is_during_basking / is_during_shedding / is_breeding_season）、综合场景判定（composite_scene）、提醒等级（alert_level）、提醒动作列表（alert_actions）、建议动作（recommended_actions：复测水温气温 / 隔离温暖干燥箱 / 干养 / 观察食欲精神 / 联系爬宠兽医，**绝不含具体药物剂量、抗生素品牌、精确升温温度数值**）、免责声明（disclaimer：AI 视觉识别仅供参考，**肺炎确诊需 X 光 + 肺部听诊 + 鼻分泌物镜检/培养，由专业爬宠兽医执行**）
        - **重要提示**：仅输出基于视觉的客观异常体征识别，**不构成任何细菌性肺炎 / 病毒性肺炎 / 真菌性肺炎 / 呼吸道支原体感染 / RNTV / 上呼吸道感染 URI 等具体疾病诊断**；**绝对不输出具体药物名称、剂量、抗生素品牌**；**绝对不输出"打恩诺沙星 X mg/kg""口服阿莫西林""注射头孢拉定""灌服板蓝根"等具体处方**；**绝对不输出"具体升温到 N℃ 持续 N 天"等精确温度疗法**

## 资源索引

- 必要脚本：见 [scripts/smyx_turtle_pneumonia_symptom_detection_analysis.py](scripts/smyx_turtle_pneumonia_symptom_detection_analysis.py)(
  用途：调用 API 进行龟类肺炎征兆识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、四组指标、7 类综合场景判定、4 级提醒策略、单日提醒上限和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov，最大 10MB；摄像头需**正对头颈部清晰展示口鼻区域**；**分辨率 ≥ 1080p**（口鼻黏液细节）；帧率 ≥ 25 FPS；**默认 ≥ 3 分钟连续观察**；**必须排除进食时段**；**水栖龟必须浮出水面或晒台上**
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **核心采样窗口**：≥ 3 分钟连续观察
- **核心预警门槛**：口部开合 **> 10 次/分钟**（非进食状态下）OR 黏液 OR 鼻分泌物
- **4 级提醒策略递进**（info → important → urgent → critical），严重姿态（张嘴+头颈伸展 / 漂浮倾斜 / 黄脓血染鼻涕 / 鼻孔气泡）直接 Level 4
- 单日提醒上限：Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限（肺炎急症可短期致死）**
- 红线约束：
    - **🚨 禁止**做"细菌性肺炎 / 病毒性肺炎 / 真菌性肺炎 / 呼吸道支原体感染 / RNTV / 上呼吸道感染 URI"等具体疾病诊断
    - **🚨 绝对禁止**输出具体药物名称、剂量、抗生素品牌、抗真菌药品牌、肌注剂量、口服剂量
    - **🚨 绝对禁止**输出"打恩诺沙星 X mg/kg""口服阿莫西林""注射头孢拉定""灌服板蓝根"等具体处方
    - **🚨 绝对禁止**输出"具体升温到 N℃ 持续 N 天"等精确温度疗法（仅可"水温/气温调至物种推荐高线"由用户根据物种手册）
    - **禁止**长期存储完整龟缸视频（≤ 14 天，留口部开合时间序列 + 肺炎关键征兆片段；养殖场/医院按管理规定）
    - **禁止**用于商业广告 / AI 训练；禁第三方共享
    - **禁止**越权代用户启停加热棒 / UVB / 灯光 / 干养/水养切换；任何环境控制变更必须由用户确认（仅可建议）
    - **绝对禁止**伪造或夸大开合频率、黏液检测、鼻分泌物等指标；所有数据必须基于真实视频帧分析
    - **必须**按 **species 适宜温度基线**判定（热带苏卡达/缅陆适温高 / 温带草龟/黄缘适温中等 / 深水龟 vs 浅水龟节律不同）；**严禁通用阈值**
    - **必须**考虑生理性上下文（**进食时口部开合属正常 / 水栖龟水下开合为换气吐泡 / 晒背蒸发期可能开口 / 消化期呼吸短促**），必须排除
    - **必须**在头部缩入壳内 / 水栖全程水下 / 图像模糊 / 光照不足 / 进食时段未排除 / 分辨率 < 1080p 时返回 `respiration_signal_unreliable`
- **必须**：连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业爬宠兽医**（X 光 + 肺部听诊 + 鼻分泌物镜检/培养）
- **必须**：肺炎风险预警报告**按 enclosure_id + individual_id + 事件时间戳输出**，含开合频率 + 黏液鼻分泌物 + 姿态评分 + 建议动作 + 免责声明
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史肺炎风险预警记录清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"开合频率/姿态/场景"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`龟肺炎预警-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 开合频率/姿态/场景 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 龟肺炎预警-20260525110200001 | 16 次/分 + 张嘴+头颈伸展 + 黄脓鼻涕 / pneumonia_risk_severe | 2026-05-25 11:02:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地龟缸固定摄像头视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --input /path/to/turtle.mp4 --open-id your-open-id

# 分析网络龟缸固定摄像头视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --url https://example.com/turtle.mp4 --open-id your-open-id

# 显示历史肺炎风险预警记录清单（自动触发关键词：查看龟类肺炎预警历史报告等）
python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --input turtle.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_turtle_pneumonia_symptom_detection_analysis --input turtle.mp4 --open-id your-open-id --output result.json
```
