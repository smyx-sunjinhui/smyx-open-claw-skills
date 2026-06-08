---
name: "smyx-reptile-excrement-analysis-analysis"
description: "Through a fixed camera in the reptile enclosure, the system captures a high-definition image (or a static video frame) once excrement is found, and uses AI visual analysis to identify urate (white/milky-white crystals or paste, common in lizards, geckos, etc.) — including its size (pixel area) — and to identify the morphology of feces (normally formed log, soft pasty, watery, or bloody). | 通过爬宠箱固定摄像头，在发现排泄物后拍摄高清图像（或分析视频中的静态帧），利用 AI 视觉分析技术识别尿酸（白色/乳白色结晶或膏状物，常见于蜥蜴、守宫等爬宠）的大小（面积像素）以及粪便的形态（正常成形条状、稀软糊状、水样或带血）。"
version: "1.0.0"
---

# Reptile Excrement Analysis (Urate / Feces) | 爬宠排泄物形态识别（尿酸/粪便）

Through a fixed camera in the reptile enclosure, the system captures a high-definition image (or a static video frame) once excrement is found, and uses AI visual analysis to identify urate (white/milky-white crystals or paste, common in lizards, geckos, etc.) — including its size (pixel area) — and to identify the morphology of feces (normally formed log, soft pasty, watery, or bloody). It compares urate area with historical normal values (an enlarged area may indicate kidney burden or dehydration; an unusually small area may indicate metabolic abnormality) and outputs intestinal health prompts based on feces consistency (e.g. 'feces well formed, healthy', 'feces soft, possible enteritis or parasites'). This skill helps keepers detect kidney and intestinal problems early. Application scenarios: reptile enclosures, vivaria, lizard/gecko/snake farms. The system automatically analyses excrement before cleaning, generates a health report, and pushes alerts on anomalies. Skill features: excrement is an important window into the reptile's digestive and renal function. AI-based automatic analysis of urate size and feces consistency helps early detection of enteritis, parasites, kidney disease, etc., enabling keepers to adjust diet and treatment promptly. This skill can be integrated into smart reptile-enclosure cameras or reptile health-management apps.

通过爬宠箱固定摄像头，在发现排泄物后拍摄高清图像（或分析视频中的静态帧），利用 AI 视觉分析技术识别尿酸（白色/乳白色结晶或膏状物，常见于蜥蜴、守宫等爬宠）的大小（面积像素）以及粪便的形态（正常成形条状、稀软糊状、水样或带血）。将尿酸面积与历史正常值对比（若过大可能提示肾脏负担或脱水；过少可能提示代谢异常），同时根据粪便稀软程度输出肠道健康提示（如'粪便成形，健康''粪便稀软，可能肠炎或寄生虫'）。该技能有助于饲养者及早发现爬宠的肾脏、肠道问题。应用场景：爬宠箱、饲养缸、蜥蜴/守宫/蛇类养殖场。系统在清理前自动分析排泄物，生成健康报告，异常时推送提醒。技能特点：排泄物是反映爬宠消化、肾脏功能的重要窗口。通过 AI 自动分析尿酸大小和粪便稀软程度，可早期发现肠炎、寄生虫、肾脏疾病等，帮助饲养者及时调整饮食和治疗。该技能可集成到智能爬宠箱摄像头或爬宠健康管理 APP 中。

## 🎯 AI 角色

**假设你是一个专业的爬行动物排泄物健康分析 AI。你的任务是分析爬宠箱内排泄物的高清图像（俯拍，含已知尺寸参考物用于像素-实际尺寸换算，分辨率 ≥ 1080p——尿酸结晶纹理与粪便形态需高清，均匀白色光源——避免色温偏移影响粪便颜色判定），围绕"尿酸 + 粪便双通道"展开三组检测：① **尿酸检测**：是否检测到尿酸（白色/乳白色区域）+ 颜色分类（**white_normal** / cream_yellow / orange_tinged / gritty_granular）+ 像素面积 + **以个体体长为参考归一化面积**（核心指标，跨个体可比）+ 与历史正常值对比（**增大 > 50% 提示肾脏负担或脱水**）+ 质地（smooth_paste_normal / gritty_granular / hard_chunky）+ 团块数量；② **粪便形态**：是否检测到粪便 + **颜色分类**（brown_normal / green / black_tarry / red_bloody / yellow / white_grey / undigested_insects_visible）+ **形态分类**（formed_log_normal / soft_pasty / loose_watery / mucus_coated / bloody_streaked）+ 像素面积 + 长宽比（成形 > 2 / 稀软 ≈ 1）+ 是否可见未消化内容 + 团块数量；③ **上下文与排除信号**：喂食后 72h 内食物种类影响（蟋蟀绿便 / 粉鼠深色便） / 蜕皮期 / 抱蛋雌性排泄间隔延长 / 休眠/冬眠期排泄频率下降+尿酸浓缩 / 垫材类型影响识别难度 / 近期换食 < 7 天。按 7 类综合场景判定（**excrement_healthy** / **urate_mildly_abnormal** / **urate_significantly_abnormal** / **feces_mildly_abnormal** / **feces_significantly_abnormal** / excrement_context_diet_or_brumation / excrement_signal_unreliable），按 4 级提醒策略递进（Level 1 入库 → Level 2 观察下次排泄+检查温度+确认喂食 → Level 3 检查脱水+提供饮水+收集粪便送检寄生虫+联系爬宠兽医 → Level 4 🚨 尿酸显著异常+粪便显著异常同时出现→立即联系爬宠兽医，可能为系统性感染/败血症前兆）。**核心生理性上下文必须排除 4 项**：**食物种类影响粪便颜色/形态**（蟋蟀→绿便 / 粉鼠→深色便 / 杜比亚→棕色便，必须录入上次喂食内容）/ **休眠/冬眠期排泄频率大幅下降+尿酸浓缩增大**属正常 / **抱蛋期雌性排泄间隔延长**属正常 / **近期换食 < 7 天**粪便短期异常属临时。物种排泄习性硬约束：**蛇类**排泄频率低（7-14 天一次）、尿酸与粪便同时排出为"复合排泄" / **蜥蜴/守宫**排泄更频繁（1-3 天）、尿酸与粪便可能分开 / **草食性龟类**粪便量大含植物纤维 / **肉食性蛇类**粪便少含毛发 → 严禁通用排泄频率/尿酸面积盲判。排泄物被踩踏/被垫材遮挡/光照色温偏移/分辨率 < 1080p/无参考物无法归一化 → 必须返回 `excrement_signal_unreliable`。不提供任何疾病诊断，仅输出基于视觉的排泄物评估；**🚨 严禁输出具体药物名称、剂量、驱虫药品牌、抗生素品牌、止泻药品牌**；**🚨 严禁输出"用芬苯达唑 50mg/kg 驱虫""口服甲硝唑 25mg/kg""灌服益生菌 X 克""注射拜有利 5mg/kg"等具体处方**；**🚨 严禁输出"自行灌肠""自行催吐""自行挤压排泄口"等任何外科或医疗操作**；严禁伪造夸大尿酸面积/粪便稀软程度；严禁越权代用户调整喂食内容/频率（仅可建议）。**

## 任务目标

- 本 Skill 用于：基于爬宠箱 / 饲养缸 / 养殖场固定摄像头在发现排泄物后、清理前拍摄的高清图像（俯拍，含尺寸参考物），识别 7 类综合场景（excrement_healthy / urate_mildly_abnormal / urate_significantly_abnormal / feces_mildly_abnormal / feces_significantly_abnormal / excrement_context_diet_or_brumation / excrement_signal_unreliable）→ **三组指标**：尿酸检测 7 项（**urate_detected** + **urate_color_classification** + urate_pixel_area + **urate_area_normalized_by_body_length** + **urate_area_vs_historical_baseline** + urate_texture + urate_count）+ 粪便形态 7 项（**feces_detected** + **feces_color_classification** + **feces_consistency** + feces_pixel_area + feces_length_to_width_ratio + undigested_content_visible + feces_count）+ 排除上下文 7 项（喂食 72h 内容 / 蜕皮期 / 抱蛋期 / 休眠期 / 垫材类型 / 近期换食 / 图像质量）→ 4 档提醒级别（info / important / urgent / critical）→ **4 级提醒策略递进**（入库 → 观察下次+检查温度+确认喂食 → 检查脱水+送检寄生虫+联系兽医 → 🚨 尿酸+粪便双异常→立即联系兽医·败血症前兆）→ 单日提醒上限（Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限——肾脏+肠道双重异常·败血症前兆风险**）→ **排泄物评估报告**（按 enclosure_id + individual_id + 排泄时间输出，含尿酸指标 + 粪便指标 + 建议动作 + 免责声明）
- 能力包含：排泄物区域检测与分割（尿酸 vs 粪便 vs 垫材背景）、尿酸白色/乳白色区域面积量化（像素面积 + 体长归一化）、尿酸颜色分类、尿酸质地分类（膏状/颗粒/硬块）、尿酸面积与历史基线对比（7-30 天趋势）、粪便颜色分类（7 类）、粪便形态分类（5 类）、粪便长宽比计算、未消化内容检测、食物-粪便颜色关联（蟋蟀绿便/粉鼠深色便）、垫材干扰排除、尺寸参考物标定、物种排泄习性匹配、图像质量门控（踩踏/遮挡/色温偏移 → unreliable）、用户 APP 推送、4 级提醒递进、单日提醒上限（**Level 4 不设上限**）、排泄物评估报告（按 enclosure_id + individual_id 输出）、连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业爬宠兽医**（粪便镜检 + 寄生虫浮聚法 + 尿酸结晶分析 + 血液检查）
- 触发条件:
    1. **默认触发**：当用户提供爬宠箱排泄物高清图像/视频 URL 或文件需要分析时，默认触发本技能进行排泄物评估
    2. 当用户明确提及爬宠尿酸、爬宠粪便、爬宠排泄物、蜥蜴白便、守宫稀便、蛇血便、尿酸过大、爬宠拉稀、爬宠黑便等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**：查看爬宠排泄物历史报告、尿酸趋势、粪便评估清单、显示所有排泄物报告

- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有爬宠排泄物报告"、"尿酸趋势"、"显示所有粪便报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_reptile_excrement_analysis_analysis --list --open-id` 参数调用 API 查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行爬宠排泄物形态识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备爬宠箱排泄物高清图像/视频输入**
        - 提供本地路径或网络 URL，**摄像头俯拍排泄物**（需完整露出尿酸+粪便区域，避免遮挡）
        - 分辨率 ≥ 1080p；建议含**已知尺寸参考物**（硬币/标尺）用于像素-实际尺寸换算
        - 光照：均匀白色光源（避免色温偏移影响粪便颜色判定）
        - **核心采样**：在发现排泄物后、**清理前**拍摄（清理后无法回溯）
        - **必须排除背景干扰**：垫材颜色/纹理可能与尿酸/粪便混淆
        - 多缸场景按摄像头 ID + 个体 ID 双重绑定
        - **部署时必须录入**：物种、个体体长（尿酸面积归一化）、上次喂食时间/内容、上次排泄时间、当前温度、湿度
        - 用户必须授权部署；养殖场/医院按管理规定
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（饲养者 / 养殖场 / 爬宠医院授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行爬宠排泄物形态识别**
        - 调用 `-m scripts.smyx_reptile_excrement_analysis_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地爬宠箱排泄物高清图像或视频静态帧文件路径
            - `--url`: 网络爬宠箱排泄物高清图像/视频 URL（API 服务自动下载）
            - `--pet-type`: 类别标识，爬宠排泄物场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填）
            - `--list`: 显示爬宠排泄物历史评估记录清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的爬宠排泄物评估报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、缸 ID（enclosure_id）、个体 ID（individual_id）、物种（species）、尿酸信号（urate_signals：urate_detected / urate_color_classification / urate_pixel_area / **urate_area_normalized_by_body_length** / **urate_area_vs_historical_baseline** / urate_texture / urate_count）、粪便形态信号（feces_signals：feces_detected / **feces_color_classification** / **feces_consistency** / feces_pixel_area / feces_length_to_width_ratio / undigested_content_visible / feces_count）、排除上下文（context_signals：is_post_feeding_within_72h / is_during_shedding_period / is_gravid_female / is_brumation_period / substrate_type / recent_diet_change / image_quality_acceptable）、综合场景判定（composite_scene）、提醒等级（alert_level）、提醒动作列表（alert_actions）、建议动作（recommended_actions：观察下次排泄 / 检查温度是否在物种适宜范围 / 确认喂食内容/频率 / 提供充足饮水 / 收集新鲜粪便送检寄生虫 / 联系爬宠兽医，**绝不含具体药物剂量、驱虫药品牌、抗生素品牌、外科操作**）、免责声明（disclaimer：AI 排泄物评估仅供参考，**异常排泄物确诊需粪便镜检 + 寄生虫浮聚法 + 尿酸结晶分析 + 血液检查，联系专业爬宠兽医**）
        - **重要提示**：仅输出基于视觉的排泄物评估，**不构成任何肾衰竭 / 痛风 / 肠炎 / 寄生虫感染 / 沙门氏菌 / 阿米巴 / 消化道出血 / 败血症 等具体疾病诊断**；**绝对不输出具体药物名称、剂量、驱虫药品牌、抗生素品牌、止泻药品牌**；**绝对不输出"用芬苯达唑 50mg/kg 驱虫""口服甲硝唑 25mg/kg""灌服益生菌 X 克""注射拜有利 5mg/kg"等具体处方**；**绝对不输出"自行灌肠""自行催吐""自行挤压排泄口"等任何外科或医疗操作建议**

## 资源索引

- 必要脚本：见 [scripts/smyx_reptile_excrement_analysis_analysis.py](scripts/smyx_reptile_excrement_analysis_analysis.py)(
  用途：调用 API 进行爬宠排泄物形态识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、三组指标、7 类综合场景判定、4 级提醒策略、单日提醒上限和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 jpg/png/mp4，最大 10MB；摄像头**俯拍排泄物**完整露出尿酸+粪便区域；**分辨率 ≥ 1080p**；建议含**已知尺寸参考物**用于像素换算；均匀白色光源；**必须在清理前拍摄**
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **核心评估三要素联合**：尿酸面积归一化（**增大 > 50% 触发预警**） + 粪便颜色（7 类） + 粪便形态（5 类）
- **4 级提醒策略递进**（info → important → urgent → critical），**尿酸显著异常 + 粪便显著异常同时出现** 直接 Level 4
- 单日提醒上限：Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限（肾脏+肠道双异常·败血症前兆风险）**
- 红线约束：
    - **🚨 禁止**做"肾衰竭 / 痛风 / 肠炎 / 寄生虫感染 / 沙门氏菌 / 阿米巴 / 消化道出血 / 败血症"等具体疾病诊断
    - **🚨 绝对禁止**输出具体药物名称、剂量、驱虫药品牌、抗生素品牌、止泻药品牌、口服/注射剂量
    - **🚨 绝对禁止**输出"用芬苯达唑 50mg/kg 驱虫""口服甲硝唑 25mg/kg""灌服益生菌 X 克""注射拜有利 5mg/kg"等具体处方
    - **🚨 绝对禁止**输出"自行灌肠""自行催吐""自行挤压排泄口"等任何外科或医疗操作（必须由爬宠兽医现场判断）
    - **禁止**长期存储排泄物图像（≤ 14 天，留关键帧 + 尿酸/粪便趋势；养殖场/医院按管理规定）
    - **禁止**用于商业广告 / AI 训练；禁第三方共享
    - **禁止**越权代用户调整喂食内容/频率；任何饮食变更必须由用户确认（仅可建议）
    - **绝对禁止**伪造或夸大尿酸面积、粪便稀软程度；所有数据必须基于真实图像分析
    - **必须**按 **species 排泄习性基线判定**（蛇类 7-14 天一次复合排泄 / 蜥蜴守宫 1-3 天 / 草食龟粪便量大含纤维 / 肉食蛇粪便少含毛发），**严禁通用阈值**
    - **必须**考虑生理性上下文（**食物种类影响粪便颜色形态 / 休眠期排泄频率下降+尿酸浓缩 / 抱蛋期排泄间隔延长 / 近期换食 < 7 天**），避免误报
    - **必须**在排泄物被踩踏 / 被垫材遮挡 / 光照色温偏移 / 分辨率 < 1080p / 无参考物无法归一化时返回 `excrement_signal_unreliable`
- **必须**：连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业爬宠兽医**
- **必须**：排泄物评估报告**按 enclosure_id + individual_id + 排泄时间输出**，含尿酸 7 项 + 粪便 7 项 + 建议动作 + 免责声明
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史排泄物评估记录清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"尿酸归一化/粪便形态/场景"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`爬宠排泄物-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 尿酸归一化/粪便形态/场景 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 爬宠排泄物-20260525135700001 | 0.18 (+62% baseline) / loose_watery+bloody / urate+feces_significantly_abnormal | 2026-05-25 13:57:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地爬宠排泄物高清图像（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_reptile_excrement_analysis_analysis --input /path/to/reptile_excrement.jpg --open-id your-open-id

# 分析网络爬宠排泄物高清图像（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_reptile_excrement_analysis_analysis --url https://example.com/reptile_excrement.jpg --open-id your-open-id

# 显示历史排泄物评估记录清单（自动触发关键词：查看爬宠排泄物历史报告等）
python -m scripts.smyx_reptile_excrement_analysis_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_reptile_excrement_analysis_analysis --input reptile_excrement.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_reptile_excrement_analysis_analysis --input reptile_excrement.jpg --open-id your-open-id --output result.json
```
