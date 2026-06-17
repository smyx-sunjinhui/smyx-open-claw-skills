---
name: "smyx-aquarium-plant-health-monitor-analysis"
description: "AI-powered aquatic plant health monitoring from aquarium camera images. Detects leaf color changes (yellowing, bleaching, blackening), morphological anomalies (melting, curling, holes), and iron deficiency symptoms in submerged plants. Outputs health assessment and care suggestions (e.g., supplement iron fertilizer, adjust lighting, increase CO₂). Helps early detection of aquatic plant issues and maintains aquarium ecological balance. Scenarios: smart fish tanks, aquascaping tanks, aquarium shops. | 通过智能鱼缸或水下摄像头拍摄水草的图像，利用AI视觉分析技术识别水草叶片的颜色变化（黄化、白化、发黑）、形态异常（溶叶、卷曲、穿孔）以及缺铁等典型症状，输出健康评估及养护建议（如补充铁肥、调整光照、增加CO₂）。有助于及早发现水草生长问题，维持水族箱生态平衡。应用场景：智能鱼缸、水族箱、水草造景缸、水族店。"
version: "1.0.1"
---

# Aquarium Plant Health Monitor | 水族箱水草健康监测

AI-powered aquatic plant health monitoring from aquarium camera images. Detects leaf color changes (yellowing, bleaching, blackening), morphological anomalies (melting, curling, holes), and iron deficiency symptoms in submerged plants. Outputs health assessment and care suggestions (e.g., supplement iron fertilizer, adjust lighting, increase CO₂). Helps early detection of aquatic plant issues and maintains aquarium ecological balance. Scenarios: smart fish tanks, aquascaping tanks, aquarium shops.

通过智能鱼缸或水下摄像头拍摄水草的图像，利用AI视觉分析技术识别水草叶片的颜色变化（黄化、白化、发黑）、形态异常（溶叶、卷曲、穿孔）以及缺铁等典型症状，输出健康评估及养护建议（如补充铁肥、调整光照、增加CO₂）。有助于及早发现水草生长问题，维持水族箱生态平衡。应用场景：智能鱼缸、水族箱、水草造景缸、水族店。

## 🎯 AI 角色

**假设你是一个专业的水族植物健康AI。你的任务是分析水族箱中水草（沉水植物）的高清图像，识别叶片颜色和形态异常，与常见水草健康问题特征库比对，输出健康状态及可能的病因。不要提供具体水质参数调整方案，仅输出基于视觉的评估。**

## 任务目标

- 本 Skill 用于：通过水族箱水草图像进行健康监测，输出水草健康评估、可能病因及养护方向建议
- 能力包含：水草叶片颜色异常检测（黄化/白化/发黑/褐变）、形态异常识别（溶叶/卷曲/穿孔/徒长）、藻类附着检测、常见缺素症状比对（缺铁/缺氮/缺钾/缺镁等）、健康等级评估、养护方向建议（施肥/光照/CO₂/修剪）
- 触发条件:
    1. **默认触发**：当用户提供水族箱水草图像或视频需要健康分析时，默认触发本技能
    2. 当用户明确需要水草健康诊断时，提及水草黄叶、水草溶叶、水草发黑、水草白化、水草徒长、水草状态差、草缸问题、水草缺肥、鱼缸水草不健康等关键词，并且上传了图像或视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史水草报告、历史水族箱报告、水草健康报告清单、显示所有水草监测报告、查询水草诊断记录
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有水草报告"、"显示水族箱监测报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_aquarium_plant_health_monitor_analysis --list --open-id` 参数调用 API
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

**在执行水族箱水草健康监测前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备图像/视频输入**
        - 提供本地水族箱水草图像/视频文件路径或网络 URL
        - 拍摄建议：
          - **透过缸壁拍摄**：正面拍摄水草区域，尽量减少玻璃反光
          - **聚焦水草**：单片叶片或一簇水草占画面主要区域
          - **水下拍摄更佳**：如有水下摄像头，水下成像更清晰
          - **光线均匀**：避免单侧强光导致阴影，建议使用水族灯全开状态拍摄
          - **正反面均拍**：叶面可见颜色变化，叶背可辅助判断藻类附着
          - **多角度拍摄**：俯拍+侧拍有助于判断徒长状态（节间拉长）
        - 支持图像（jpg/png）和视频（mp4/avi/mov）格式
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行水草健康监测**
        - 调用 `-m scripts.smyx_aquarium_plant_health_monitor_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地水族箱水草图像/视频文件路径
            - `--url`: 网络水族箱水草图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 对象类型，水族场景默认 other
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示水族箱水草健康监测历史报告列表清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看监测结果**
        - 接收结构化的水族箱水草健康监测报告
        - 包含：**叶片特征描述**（颜色异常、形态变化、藻类附着情况）、**最可能病因**（如缺铁/光照不足/CO₂不足/藻害/溶叶症）、**健康等级**（健康/亚健康/轻度异常/重度异常）、**养护方向建议**（如"补充铁肥"、"增加光照时长"、"提高CO₂浓度"，不涉及具体参数值）
        - **重要提示**：仅输出基于视觉的健康评估，**不提供具体水质参数调整方案**；专业水质调控请咨询水族专家

## 🌿 常见水草健康问题对照表

| 问题类型 | 典型症状 | 可能病因 | 易发水草 |
|----------|----------|----------|----------|
| 🟡 黄化 | 叶片整体或叶脉间变黄 | 缺氮/缺铁/光照不足 | 皇冠草、椒草、榕草 |
| ⚪ 白化 | 新叶发白或半透明 | 严重缺铁/缺微量元素 | 皇冠草、水榕、铁皇冠 |
| ⚫ 发黑 | 叶片变黑腐烂 | 缺钾/有机物过多/水质恶化 | 莫斯、水榕、椒草 |
| 🟤 褐变 | 叶片出现褐色斑点或整体褐化 | 硅藻覆盖/缺钾/光照光谱不佳 | 各类水草均可能 |
| 💧 溶叶 | 叶片透明软化溶解 | 水质剧烈变化/新草转水/缺肥 | 皇冠草、椒草、谷精草 |
| 🌀 卷曲 | 叶片向内卷曲或扭曲 | 缺钙/缺钾/CO₂不足 | 皇冠草、椒草、牛顿草 |
| 🔴 穿孔 | 叶片出现孔洞 | 缺钾/蜗牛啃食/细菌感染 | 皇冠草、水榕、椒草 |
| 📏 徒长 | 节间拉长、叶片稀疏 | 光照不足/光谱偏红 | 红蝴蝶、宫廷草、珍珠草 |
| 🦠 藻害 | 叶面覆盖绿斑/丝藻/黑毛藻 | 光照过强/肥力过剩/CO₂不足 | 各类水草均可能 |
| 🥀 老叶脱落 | 底部老叶逐步枯黄脱落 | 缺氮/缺镁/自然代谢 | 皇冠草、椒草、水榕 |
| 🔴 红草褪色 | 红色水草变绿 | 光照不足/缺铁/缺磷 | 红蝴蝶、红宫廷、血红宫廷 |

## 🔍 水草健康识别维度

| 维度 | 观察重点 | 判断意义 |
|------|----------|----------|
| 颜色变化 | 黄化/白化/发黑/褐变/褪色 | 不同颜色指向不同病因 |
| 症状部位 | 老叶 vs 新叶 vs 嫩芽 | 老叶→移动性元素缺乏(N/K/Mg)，新叶→非移动性元素(Fe/Ca) |
| 形态异常 | 溶叶/卷曲/穿孔/徒长 | 溶叶→水质突变，卷曲→缺Ca/K，徒长→光照不足 |
| 藻类附着 | 绿斑藻/丝藻/黑毛藻/硅藻 | 藻类类型指示不同水质问题 |
| 叶片质地 | 透明软化/厚硬/脆裂 | 软化→溶叶/缺肥，脆裂→缺钾 |
| 生长状态 | 节间长度/叶片密度/顶端优势 | 徒长→光照/CO₂不足，矮化→缺肥 |

## 📊 水草健康等级评估

| 等级 | 症状表现 | 养护建议 |
|------|----------|----------|
| 🟢 健康 | 叶色翠绿/形态正常、无藻类附着 | 保持当前养护，定期监测 |
| 🟡 亚健康 | 叶色轻微偏黄或少量褐斑 | 适当补充液肥，观察变化 |
| 🟠 轻度异常 | 明确黄化/少量溶叶/轻度藻害 | 针对性施肥，调整光照/CO₂ |
| 🔴 重度异常 | 大面积溶叶/发黑/严重藻害/徒长 | 紧急干预，修剪病叶，咨询水族专家 |

## 💡 养护方向参考

| 问题类型 | 养护方向 |
|----------|----------|
| 🟡 缺氮（整体黄化） | 适当增加含氮液肥；注意不要过量引发藻类 |
| 🟢 缺铁（新叶白化/黄化） | 补充铁肥（EDTA-Fe/DTPA-Fe）；配合适当光照促进吸收 |
| 🟤 缺钾（叶缘褐变/穿孔） | 追施钾肥；注意K与Ca/Mg的平衡 |
| 🟡 缺镁（老叶叶脉间黄化） | 补充镁肥；与钾肥交替施用避免拮抗 |
| ☀️ 光照不足（徒长/褪色） | 延长光照时长或增加光照强度；检查灯管是否老化 |
| 💨 CO₂不足（生长缓慢/藻害） | 提高CO₂浓度；检查CO₂设备是否正常工作 |
| 🦠 藻害 | 减少光照时长；适量减少施肥；引入除藻生物（黑壳虾/小精灵鱼） |
| 💧 溶叶/水质突变 | 换水时注意温差和水质匹配；新草转水需逐步适应 |

> ⚠️ 本技能仅提供**养护方向建议**，**不提供具体水质参数调整方案**（如pH/KH/GH具体数值）；专业水质调控需根据缸内实际情况咨询水族专家。

## 🔄 与陆生植物问题的区分要点

| 特征 | 水草（沉水植物） | 陆生植物 |
|------|------------------|----------|
| 环境 | 水下生长，受水质/CO₂/光照综合影响 | 空气中生长，主要受土壤/光照影响 |
| 缺铁表现 | 新叶白化或严重黄化（水中铁易沉淀） | 新叶叶脉间失绿（网纹状） |
| 溶叶现象 | ✅ 常见（水质突变/转水） | ❌ 极少发生 |
| 藻类竞争 | ✅ 常见（与水草争夺营养/光照） | ❌ 不存在 |
| CO₂影响 | ⚡ 关键因素（水中CO₂浓度远低于空气） | 影响较小（空气中CO₂充足） |

> 💡 水草问题的诊断需综合考量水质、光照、CO₂、肥料四要素，单纯视觉判断可能存在不确定性。建议结合水质测试数据综合分析。

## 🐟 常见水草品种与典型问题

| 水草品种 | 易发问题 | 注意事项 |
|----------|----------|----------|
| 皇冠草 | 新叶白化(缺铁)、老叶穿孔(缺钾) | 重根肥需求大，需定期补充 |
| 椒草 | 溶叶(水质突变)、褐斑 | 对水质变化敏感，换水需温和 |
| 水榕 | 发黑、藻害 | 生长缓慢，对CO₂需求低 |
| 铁皇冠 | 白化(缺铁)、黑毛藻 | 名字带"铁"，对铁肥需求高 |
| 莫斯 | 发黑、丝藻缠绕 | 需良好水流，避免有机物沉积 |
| 红蝴蝶 | 褪色变绿(光照/缺铁)、徒长 | 需强光+足量铁肥维持红色 |
| 宫廷草 | 徒长(光照不足)、底部落叶 | 需高光高CO₂，定期修剪 |
| 珍珠草 | 徒长、稀疏 | 需强光，否则底部叶片脱落 |

## 资源索引

- 必要脚本：见 [scripts/smyx_aquarium_plant_health_monitor_analysis.py](scripts/smyx_aquarium_plant_health_monitor_analysis.py)(用途：调用 API 进行水族箱水草健康监测，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持 jpg/png/mp4/avi/mov 格式，最大 10MB
- **拍摄要求**：透过缸壁或水下拍摄，聚焦水草，避免反光；模糊/逆光/反光严重的图像无法得出可靠结果
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **监测结果仅供水草健康参考，不提供具体水质参数调整方案**；专业水质调控请咨询水族专家
- 部分水草问题症状相似（如缺铁与缺氮均可致黄化），AI 识别可能存在不确定性，建议结合水质测试数据综合判断
- 玻璃缸壁反光、水面波纹、水中悬浮颗粒可能干扰识别，建议拍摄时尽量减少干扰因素
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 当显示历史监测报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"报告名称"、"对象类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`水族箱水草健康监测报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 对象类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 水族箱水草健康监测报告-20260312172200001 | 水草 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地水族箱水草图像（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_aquarium_plant_health_monitor_analysis --input /path/to/aquarium_plant.jpg --open-id your-open-id

# 分析网络水族箱水草图像（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_aquarium_plant_health_monitor_analysis --url https://example.com/aquarium.jpg --open-id your-open-id

# 显示历史监测报告/显示报告清单列表
python -m scripts.smyx_aquarium_plant_health_monitor_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_aquarium_plant_health_monitor_analysis --input aquarium.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_aquarium_plant_health_monitor_analysis --input aquarium.jpg --open-id your-open-id --output result.json
```
