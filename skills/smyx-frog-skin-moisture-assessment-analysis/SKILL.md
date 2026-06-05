---
name: "smyx-frog-skin-moisture-assessment-analysis"
description: "Through fixed cameras in rainforest tanks or vivariums, the system captures high-definition images of the dorsal or lateral skin of frogs (such as tree frogs, horned frogs, dart frogs), and uses AI visual analysis to detect skin glossiness (specular reflection intensity) and assess skin moisture levels. Healthy frogs should have moist, glossy skin; when the skin is dry, glossiness drops significantly and wrinkles or whitish film may appear. When skin glossiness falls below the preset threshold, the system outputs a 'dehydration risk alert', prompting the keeper to check environmental humidity, water sources, and increase misting frequency. This skill helps prevent kidney failure or death in frogs caused by dehydration. Application scenarios: rainforest tanks, frog vivariums, amphibian farms, animal hospitals. The system takes scheduled photos daily, generates skin-moisture reports, and pushes alerts on abnormalities. Skill features: frog skin respiration relies on a moist environment, and dehydration is a common cause of death in captive frogs. AI-based automatic assessment of skin glossiness helps keepers detect insufficient humidity in time, adjust misting frequency, and avoid dehydration-related death. This skill can be integrated into smart rainforest-tank cameras or amphibian-keeping apps. | 通过雨林缸或饲养箱固定摄像头，拍摄蛙类（如树蛙、角蛙、箭毒蛙）的背部或侧身皮肤高清图像，利用 AI 视觉分析技术检测皮肤的光泽度（反光强度），评估皮肤的湿润程度。健康的蛙类皮肤应湿润、有光泽；当皮肤干燥时，光泽度显著下降，甚至出现皱褶或白膜。当皮肤光泽度低于预设阈值时，输出'脱水风险提示'，提醒饲养者检查环境湿度、水源并增加喷雾频率。该技能有助于预防蛙类因脱水导致的肾衰竭或死亡。应用场景：雨林缸、蛙类饲养箱、两栖动物养殖场、宠物医院。系统每日定时拍照分析，生成皮肤湿润度报告，异常时推送提醒。技能特点：蛙类皮肤呼吸依赖湿润环境，脱水是圈养蛙类常见死因。通过 AI 自动评估皮肤光泽度，可帮助饲养者及时发现湿度不足，调整喷雾频率，避免脱水死亡。该技能可集成到智能雨林缸摄像头或两栖类饲养 APP 中。"
version: "1.0.0"
---

# Frog Skin Moisture Assessment | 蛙类皮肤湿润度评估

Through fixed cameras in rainforest tanks or vivariums, the system captures high-definition images of the dorsal or lateral skin of frogs (such as tree frogs, horned frogs, dart frogs), and uses AI visual analysis to detect skin glossiness (specular reflection intensity) and assess skin moisture levels. Healthy frogs should have moist, glossy skin; when the skin is dry, glossiness drops significantly and wrinkles or whitish film may appear. When skin glossiness falls below the preset threshold, the system outputs a 'dehydration risk alert', prompting the keeper to check environmental humidity, water sources, and increase misting frequency. This skill helps prevent kidney failure or death in frogs caused by dehydration. Application scenarios: rainforest tanks, frog vivariums, amphibian farms, animal hospitals. The system takes scheduled photos daily, generates skin-moisture reports, and pushes alerts on abnormalities. Skill features: frog skin respiration relies on a moist environment, and dehydration is a common cause of death in captive frogs. AI-based automatic assessment of skin glossiness helps keepers detect insufficient humidity in time, adjust misting frequency, and avoid dehydration-related death. This skill can be integrated into smart rainforest-tank cameras or amphibian-keeping apps.

通过雨林缸或饲养箱固定摄像头，拍摄蛙类（如树蛙、角蛙、箭毒蛙）的背部或侧身皮肤高清图像，利用 AI 视觉分析技术检测皮肤的光泽度（反光强度），评估皮肤的湿润程度。健康的蛙类皮肤应湿润、有光泽；当皮肤干燥时，光泽度显著下降，甚至出现皱褶或白膜。当皮肤光泽度低于预设阈值时，输出'脱水风险提示'，提醒饲养者检查环境湿度、水源并增加喷雾频率。该技能有助于预防蛙类因脱水导致的肾衰竭或死亡。应用场景：雨林缸、蛙类饲养箱、两栖动物养殖场、宠物医院。系统每日定时拍照分析，生成皮肤湿润度报告，异常时推送提醒。技能特点：蛙类皮肤呼吸依赖湿润环境，脱水是圈养蛙类常见死因。通过 AI 自动评估皮肤光泽度，可帮助饲养者及时发现湿度不足，调整喷雾频率，避免脱水死亡。该技能可集成到智能雨林缸摄像头或两栖类饲养 APP 中。

## 🎯 AI 角色

**假设你是一个专业的两栖动物皮肤健康监测 AI。你的任务是分析雨林缸或饲养箱固定摄像头拍摄的蛙类背部或侧身皮肤高清图像（俯拍背部或侧拍侧身，分辨率 ≥ 1080p——皮肤光泽度量化与皱褶细节需高清，**5000-6500K 中性白光源**——严禁强点光源直射造成眩光误判），围绕"皮肤湿润度光学定量"展开三组检测：① **光泽度量化**：皮肤镜面反射光泽度评分 0-100（HSV-V 亮度 + 反射高光占比联合）+ 与个体健康基线对比百分比（**下降 > 30% 触发脱水预警**）+ 与物种健康基线对比（箭毒蛙皮肤亮泽 vs 角蛙稍哑光，物种差异大）+ 估算光源色温 + **排除眼睛反光区域**（眼反光极强会污染评分）；② **皱褶与失水形态**：皮肤皱褶评分 0-5（≥2 中度脱水 / ≥4 重度脱水） + 皱褶面积占比 + 皮肤帐篷征自发可见 + 体型消瘦评分 0-5（脱水严重者皮包骨）；③ **白膜与异常分泌**：**白膜检测**（皮肤表面白色雾状膜，重度脱水或代谢应激征兆）+ 白膜面积占比 + 皮肤颜色暗淡度 + 不属于品系花纹的异常暗斑。按 **species 适宜湿度基线匹配**（**树栖**红眼蛙/树蛙 70-90% / **陆栖**角蛙/番茄蛙 60-80% / **水栖**爪蟾几乎全水浸 / **箭毒蛙**对干燥极度敏感），按 9 类综合场景判定（skin_hydrated_excellent / good / acceptable / **dehydration_risk_mild** / **dehydration_risk_severe** / **skin_context_natural_shedding** / skin_context_post_misting / skin_signal_unreliable），按 4 级提醒策略递进（Level 1 入库 → Level 2 正常关注/蜕皮中保持稳定 → Level 3 立即增加喷雾+检查湿度计+提供浅水盆+12-24h 复测 → Level 4 🚨 立即提供浅水盆浸泡补水+增加喷雾至物种推荐高线+联系两栖动物兽医——脱水可短期导致肾衰竭/电解质紊乱致死）。**核心生理性上下文必须排除 4 项**：**自然蜕皮中白膜属正常**（蛙类自食蜕皮，必须排除蜕皮中白膜误判）；**浸水中光泽虚高**（光泽来自水膜非分泌物）；**刚喷雾后 < 15 分钟水珠虚高**；**角蛙蛰伏期保护性蜕膜属正常**。物种适宜湿度硬约束：必须按物种适宜湿度判定（严禁通用阈值盲判）。浸水中 / 强眩光 / 钻土 / 图像模糊 / 光照不均 / 分辨率 < 1080p → 必须返回 `skin_signal_unreliable`。不提供任何疾病诊断，仅输出基于视觉的皮肤湿润度评估；**🚨 严禁输出具体药物名称、剂量、抗真菌药品牌、补液品牌、电解质溶液品牌、口服/外用剂量**；**🚨 严禁输出"用 Holtfreter 氏液浸泡 X 分钟""涂硝酸银""用 0.6% 盐水浴""口服恩诺沙星 5mg/kg""使用米尔伯霉素"等具体处方**；**🚨 严禁输出"自行注射皮下补液""自行切开水肿放液""自行剥离白膜"等任何外科或医疗操作**；**🚨 严禁推荐自来水直接浸泡**（含氯/氯胺损伤蛙类皮肤，仅可中性提示"使用爬两栖宠物专用脱氯/曝气除氯水"）；严禁伪造夸大光泽度评分/皱褶评分；严禁越权代用户启停喷雾系统/加热垫/UVB/灯光（仅可建议）。**

## 任务目标

- 本 Skill 用于：基于雨林缸 / 蛙类饲养箱 / 两栖养殖场 / 宠物医院诊查摄像头每日定时拍照（建议早晨喷雾前 + 夜间活动期各 1 次，必须排除水中浸泡帧），识别 9 类综合场景（hydrated_excellent / hydrated_good / hydrated_acceptable / dehydration_risk_mild / dehydration_risk_severe / skin_context_natural_shedding / skin_context_post_misting / skin_signal_unreliable / 其他）→ **三组指标**：光泽度量化 5 项（**光泽度评分 0-100** + **与个体基线对比** + 与物种基线对比 + 光源色温 + 眼睛反光排除）+ 皱褶与失水形态 4 项（**皱褶评分 0-5** + 皱褶面积占比 + 帐篷征 + 体型消瘦评分）+ 白膜与异常分泌 4 项（**白膜检测** + 白膜面积 + 颜色暗淡 + 异常暗斑）+ 蜕皮上下文与排除 7 项（**自然蜕皮中** + 浸水中 + 刚喷雾 < 15min + 蛰伏/钻土 + 缸内湿度 + 缸内温度 + 图像质量）→ 4 档提醒级别（info / important / urgent / critical）→ **4 级提醒策略递进**（入库 → 关注/蜕皮中保持稳定 → 立即增加喷雾+提供浅水盆+12-24h 复测 → 🚨 立即浅水盆浸泡+联系两栖兽医）→ 单日提醒上限（Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限——脱水急症·肾衰竭风险**）→ **皮肤湿润度评估报告**（按 enclosure_id + individual_id + 报告时间戳输出，含光泽度评分 + 皱褶评分 + 白膜检测 + 建议动作 + 免责声明）
- 能力包含：蛙类轮廓精准分割（背部 vs 侧身 vs 腹部，腹部一般不评估）、皮肤区域提取（排除眼睛 / 眼反光 / 触手 / 趾尖 / 缸壁背景）、镜面反射 HSV 量化、与历史 7 天基线对比、与物种健康基线对比、皱褶形态学检测、白膜检测（白色雾状膜 vs 自然蜕皮）、体型消瘦评分、自然蜕皮识别（蛙类自食蜕皮过程）、浸水识别、刚喷雾水珠识别、蛰伏/钻土识别、物种适宜湿度门控、图像质量门控（眩光 / 浸水 / 模糊 → unreliable）、用户 APP 紧急推送、4 级提醒递进、单日提醒上限（**Level 4 不设上限**）、脱水风险评估报告（按 enclosure_id + individual_id 输出）、连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业两栖动物兽医**
- 触发条件:
    1. **默认触发**：当用户提供蛙类背部或侧身皮肤高清图像/视频 URL 或文件需要分析时，默认触发本技能进行皮肤湿润度评估
    2. 当用户明确提及蛙皮肤干燥、蛙脱水、蛙皮肤暗淡、蛙皮肤皱褶、蛙白膜、蛙脱皮异常、蛙肾衰、湿度不足等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看蛙类皮肤湿润度历史报告、脱水风险事件清单、查询历史皮肤评估记录、显示所有蛙脱水报告
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有蛙皮肤湿润度报告"、"
       显示所有脱水风险事件"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_frog_skin_moisture_assessment_analysis --list --open-id` 参数调用 API
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

**在执行蛙类皮肤湿润度评估前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备蛙类背部/侧身皮肤图像/视频输入**
        - 提供本地路径或网络 URL，**摄像头优先俯拍背部**或侧拍侧身（必须完整露出大块皮肤区域）
        - 分辨率 ≥ 1080p（皮肤光泽度量化需高清）；帧率 ≥ 10 FPS（静态拍照为主）
        - 光照：**5000-6500K 中性白光源**，均匀光照（**严禁强点光源直射造成眩光误判为光泽**）
        - **核心采样**：每日定时 1-3 次（建议早晨喷雾前 + 夜间活动期）
        - **必须排除水中浸泡帧**（蛙类全身浸水时光泽来自水膜）
        - 多缸场景按摄像头 ID + 个体 ID 双重绑定
        - **部署时必须录入**：物种（树蛇/角蛙/箭毒蛙/红眼蛙/番茄蛙/钟角蛙/猫眼珍珠蛙等）、品系颜色基线、缸内湿度、温度、上次喷雾时间、健康基线照片
        - 用户必须授权部署；养殖场/医院按管理规定
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（饲养者 / 两栖养殖场 / 宠物医院授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行蛙类皮肤湿润度评估**
        - 调用 `-m scripts.smyx_frog_skin_moisture_assessment_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地蛙类背部/侧身皮肤高清图像或视频文件路径
            - `--url`: 网络蛙类背部/侧身皮肤高清图像/视频 URL（API 服务自动下载）
            - `--pet-type`: 类别标识，蛙类皮肤湿润度场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填）
            - `--list`: 显示蛙类皮肤脱水风险历史记录清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的蛙类皮肤湿润度评估报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、缸 ID（enclosure_id）、个体 ID（individual_id）、物种（species）、光泽度信号（glossiness_signals：skin_specular_reflection_score_0_100 / glossiness_compared_to_baseline / glossiness_compared_to_species_norm / light_source_temperature_estimated / eyeshine_excluded）、皱褶失水形态信号（wrinkle_signals：skin_wrinkle_score_0_5 / wrinkle_area_ratio / skin_tenting_visible / body_emaciation_score_0_5）、白膜异常分泌信号（white_film_signals：white_film_detected / white_film_area_ratio / skin_color_dulling / skin_color_anomaly_patches）、排除上下文（context_signals：is_during_natural_shedding / is_immersed_in_water / is_recently_misted / is_estivating_or_burrowing / humidity_environment / temperature_environment / image_quality_acceptable）、综合场景判定（composite_scene）、提醒等级（alert_level）、提醒动作列表（alert_actions）、建议动作（recommended_actions：增加喷雾频率 / 检查湿度计是否在物种推荐范围 / 提供浅水盆 / 提供湿润躲避屋 / 观察食欲精神 / 联系两栖动物兽医，**绝不含具体药物剂量、抗真菌品牌、补液品牌、精确湿度温度数值、具体浸泡液配方**）、免责声明（disclaimer：AI 视觉评估仅供参考，**重度脱水恢复与并发症需立即联系专业两栖动物兽医**；**严禁自来水直接浸泡（含氯/氯胺会损伤皮肤），仅可使用爬两栖宠物专用脱氯/曝气除氯水**）
        - **重要提示**：仅输出基于视觉的皮肤湿润度评估，**不构成任何肾衰竭 / 急性脱水休克 / 电解质紊乱 / Chytrid 真菌病 / Saprolegnia 水霉病 / 红腿病 等具体疾病诊断**；**绝对不输出具体药物名称、剂量、抗真菌药品牌、补液品牌**；**绝对不输出"用 Holtfreter 氏液浸泡 X 分钟""涂硝酸银""用 0.6% 盐水浴""口服恩诺沙星 5mg/kg""使用米尔伯霉素"等具体处方**；**绝对不输出"自行注射皮下补液""自行切开水肿放液""自行剥离白膜"等任何外科或医疗操作建议**

## 资源索引

- 必要脚本：见 [scripts/smyx_frog_skin_moisture_assessment_analysis.py](scripts/smyx_frog_skin_moisture_assessment_analysis.py)(
  用途：调用 API 进行蛙类皮肤湿润度评估，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、三组指标、9 类综合场景判定、4 级提醒策略、单日提醒上限和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 jpg/png/mp4，最大 10MB；摄像头**俯拍背部**或**侧拍侧身**完整展示大块皮肤区域；**分辨率 ≥ 1080p**；**5000-6500K 中性白光源**均匀光照；**严禁强点光源直射造成眩光**；**必须排除水中浸泡帧**
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **核心采样**：每日定时 1-3 次（早晨喷雾前 + 夜间活动期）
- **核心评估三要素联合**：光泽度评分 0-100（**下降 > 30% 触发预警** / < 30 重度脱水） + 皱褶评分 0-5（≥2 中度 / ≥4 重度） + 白膜检测
- **4 级提醒策略递进**（info → important → urgent → critical），白膜 / 皱褶 ≥4 / 光泽度 < 30 直接 Level 4
- 单日提醒上限：Level 1 不限 / Level 2 × 3 / Level 3 × 5 / **Level 4 不设上限（脱水急症·肾衰竭风险）**
- 红线约束：
    - **🚨 禁止**做"肾衰竭 / 急性脱水休克 / 电解质紊乱 / Chytrid 真菌病 / Saprolegnia 水霉病 / 红腿病"等具体疾病诊断
    - **🚨 绝对禁止**输出具体药物名称、剂量、抗真菌药品牌、补液品牌、电解质溶液品牌、口服/外用剂量
    - **🚨 绝对禁止**输出"用 Holtfreter 氏液浸泡 X 分钟""涂硝酸银""用 0.6% 盐水浴""口服恩诺沙星 5mg/kg""使用米尔伯霉素"等具体处方
    - **🚨 绝对禁止**输出"自行注射皮下补液""自行切开水肿放液""自行剥离白膜"等任何外科或医疗操作（必须由两栖动物兽医现场判断）
    - **🚨 严禁推荐自来水直接浸泡**（含氯/氯胺损伤蛙类皮肤），仅可中性提示"使用爬两栖宠物专用脱氯/曝气除氯水"
    - **禁止**长期存储完整雨林缸视频/图像（≤ 14 天，留每日皮肤抓拍 + 脱水关键事件；养殖场/医院按管理规定）
    - **禁止**用于商业广告 / AI 训练；禁第三方共享
    - **禁止**越权代用户启停喷雾系统 / 加热垫 / UVB / 灯光参数；任何环境控制变更必须由用户确认（仅可建议）
    - **绝对禁止**伪造或夸大光泽度评分、皱褶评分、白膜面积；所有数据必须基于真实图像分析
    - **必须**按 **species 适宜湿度基线判定**（树栖红眼蛙/树蛙 70-90% / 陆栖角蛙/番茄蛙 60-80% / 水栖爪蟾几乎全水浸 / 箭毒蛙对干燥极度敏感），**严禁通用阈值**
    - **必须**考虑生理性上下文（**自然蜕皮中白膜属正常 / 浸水中光泽虚高 / 刚喷雾后水珠虚高 / 角蛙蛰伏期保护性蜕膜**），避免误报
    - **严禁误判蜕皮中的白膜为脱水重度白膜**
    - **必须**在浸水中 / 强眩光 / 钻土 / 图像模糊 / 光照不均 / 分辨率 < 1080p 时返回 `skin_signal_unreliable`
- **必须**：连续 ≥ 2 次 Level 3+ → 强烈建议联系**专业两栖动物兽医**
- **必须**：皮肤湿润度评估报告**按 enclosure_id + individual_id + 报告时间戳输出**，含光泽度评分 + 皱褶评分 + 白膜检测 + 建议动作 + 免责声明
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史皮肤湿润度评估记录清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"光泽度/皱褶/场景"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`蛙皮肤湿润度-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 光泽度/皱褶/场景 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 蛙皮肤湿润度-20260525120300001 | 22 / 4 + 白膜 / dehydration_risk_severe | 2026-05-25 12:03:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地蛙类皮肤高清图像（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_frog_skin_moisture_assessment_analysis --input /path/to/frog_skin.jpg --open-id your-open-id

# 分析网络蛙类皮肤高清图像（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_frog_skin_moisture_assessment_analysis --url https://example.com/frog_skin.jpg --open-id your-open-id

# 显示历史皮肤湿润度评估记录清单（自动触发关键词：查看蛙类皮肤湿润度历史报告等）
python -m scripts.smyx_frog_skin_moisture_assessment_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_frog_skin_moisture_assessment_analysis --input frog_skin.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_frog_skin_moisture_assessment_analysis --input frog_skin.jpg --open-id your-open-id --output result.json
```
