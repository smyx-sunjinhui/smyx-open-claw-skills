---
name: "smyx-uv-safety-monitor-analysis"
description: "AI-powered UV disinfection safety monitor for pets. Real-time camera analysis detects whether a pet enters an active UV-C disinfection zone and whether the UV lamp is on (via blue-purple glow recognition or smart-home API linkage). When both conditions are met, it auto-triggers a high-risk alert, recommends shutting off the UV lamp, and logs the event to prevent corneal burns or skin damage. Scenarios: smart homes, pet households, pet boarding facilities. | 通过智能家居摄像头实时识别宠物是否进入正在进行紫外线消毒的区域，自动关闭UV灯并推送提醒，防止宠物因误入消毒区而受到紫外线伤害。结合目标检测（宠物识别）与UV灯状态感知（可通过画面蓝紫色光晕/光谱特征或智能家居API联动），实现主动式安全防护。应用场景：智能家居、宠物家庭、宠物寄养场所。"
version: "1.0.0"
---

# Pet UV Safety Monitor | 宠物紫外线消毒安全监测

AI-powered UV disinfection safety monitor for pets. Real-time camera analysis detects whether a pet enters an active UV-C disinfection zone and whether the UV lamp is on (via blue-purple glow recognition or smart-home API linkage). When both conditions are met, it auto-triggers a high-risk alert, recommends shutting off the UV lamp, and logs the event to prevent corneal burns or skin damage. Scenarios: smart homes, pet households, pet boarding facilities.

通过智能家居摄像头实时识别宠物是否进入正在进行紫外线消毒的区域，自动关闭UV灯并推送提醒，防止宠物因误入消毒区而受到紫外线伤害。结合目标检测（宠物识别）与UV灯状态感知（可通过画面蓝紫色光晕/光谱特征或智能家居API联动），实现主动式安全防护。应用场景：智能家居、宠物家庭、宠物寄养场所。

## 🎯 AI 角色

**假设你是一个专业的家庭宠物安全防护AI。你的任务是分析室内消毒区域的实时视频流，检测是否有宠物（猫、狗等）进入该区域，并判断紫外线（UV）灯是否处于工作状态（可通过画面中的蓝紫色光晕、特定光源闪烁或智能家居信号判断）。一旦同时满足"宠物进入"与"UV灯开启"两个条件，则输出高危预警并建议立即关闭UV灯。不要提供医疗建议，仅输出基于视觉和逻辑的判断结果。**

## 任务目标

- 本 Skill 用于：通过室内摄像头视频进行紫外线消毒区域的宠物安全监测，检测宠物闯入 + UV灯开启的叠加风险，输出高危预警和设备联动建议
- 能力包含：宠物目标检测（猫/狗/其他）、UV灯工作状态识别（蓝紫色光晕/光谱特征/智能家居信号）、消毒区域入侵判定、双条件叠加预警、设备联动建议（关闭UV灯）、事件快照与日志记录
- 触发条件:
    1. **默认触发**：当用户提供紫外线消毒区域视频需要安全分析时，默认触发本技能
    2. 当用户明确需要UV消毒安全监测时，提及紫外线、UV灯、消毒、消毒区、宠物安全、UV伤害等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史UV安全报告、历史消毒监测报告、UV安全报告清单、显示所有消毒报告、查询紫外线事件记录
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有UV安全报告"、"显示消毒监测报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_uv_safety_monitor_analysis --list --open-id` 参数调用 API
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

**在执行紫外线消毒安全监测前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备视频输入**
        - 提供本地消毒区域视频文件路径或网络视频 URL
        - 拍摄建议：固定摄像头视角覆盖UV消毒区域，画面需能识别蓝紫色UV光晕（若需判断UV灯状态）
        - 支持视频格式：mp4/avi/mov
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行紫外线消毒安全监测**
        - 调用 `-m scripts.smyx_uv_safety_monitor_analysis` 处理视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地消毒区域视频文件路径
            - `--url`: 网络消毒区域视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 宠物类型，可选值：cat/dog/other，默认 cat
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示紫外线消毒安全监测历史报告列表清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看监测结果**
        - 接收结构化的紫外线消毒安全监测报告
        - 包含：**宠物检测**（是否进入消毒区、宠物类型）、**UV灯状态**（开启/关闭，检测依据）、**风险等级**、**预警建议**（如"宠物进入消毒区，UV灯已关闭"）、**事件快照时间戳**
        - **重要提示**：仅输出基于视觉和逻辑的判断结果，**不提供医疗建议**；若宠物已暴露于UV环境，建议观察并及时就医

## ☢️ UV灯状态识别方法

| 识别方式 | 原理 | 可靠性 |
|----------|------|--------|
| 🟣 蓝紫色光晕识别 | UV-C灯工作时画面呈现明显蓝紫色光谱 | 中（受环境光干扰） |
| 💡 智能家居API联动 | 通过智能插座/灯开关状态判断 | 高（需设备支持） |
| ⏰ 定时消毒模式 | 用户预设消毒时段自动判定 | 中（需配置） |
| 📡 蓝牙/Zigbee信标 | UV灯内置信标广播工作状态 | 高（需硬件支持） |

## 🚨 风险分级与联动策略

| 等级 | 条件 | 策略 | APP 通知 |
|------|------|------|----------|
| 🟢 安全 | UV灯关闭 / 无宠物进入 | 持续监测 | 不推送 |
| 🟡 注意 | UV灯开启但无宠物 | 加强监测频率 | "UV消毒进行中，请确保宠物远离" |
| 🔴 高危 | UV灯开启 + 宠物进入消毒区 | ① 立即关闭UV灯<br>② 推送紧急警报<br>③ 记录事件快照 | 🚨 "宠物进入消毒区，UV灯已关闭！请检查宠物状态" |

## ⚠️ 紫外线对宠物的危害

| 暴露部位 | 症状 | 严重程度 |
|----------|------|----------|
| 👁️ 眼睛 | 角膜灼伤、畏光、流泪 | 严重，可能永久损伤 |
| 🐾 皮肤（鼻部/耳尖/腹部） | 红肿、脱皮、灼伤 | 中-严重 |
| 🫁 呼吸道 | 臭氧刺激呼吸道黏膜 | 轻度不适 |
| 全身 | 长期暴露增加皮肤癌风险 | 长期隐患 |

## 🔧 智能设备联动参考

| 联动设备 | 安全作用 | 适用等级 |
|----------|----------|----------|
| 🔌 智能插座 | 远程/自动切断UV灯电源 | 高危触发 |
| 🔒 智能门/围栏 | 阻止宠物进入消毒区 | 注意起 |
| 🔊 智能音箱 | 播放警告声驱离宠物 | 注意起 |
| 💡 警示灯 | 门外红灯提示消毒中 | 注意起 |
| 📱 APP推送 | 通知主人远程确认 | 注意起 |

## 资源索引

- 必要脚本：见 [scripts/smyx_uv_safety_monitor_analysis.py](scripts/smyx_uv_safety_monitor_analysis.py)(用途：调用 API 进行紫外线消毒安全监测，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 视频要求：支持 mp4/avi/mov 格式，最大 10MB
- UV灯状态识别受环境光线影响，暗环境检测更准确；建议配合智能家居API提升可靠性
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **监测结果仅供安全防护参考，不提供医疗建议**；若宠物已暴露于UV环境，请观察眼部和皮肤并及时就医
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 当显示历史监测报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"报告名称"、"宠物类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`UV消毒安全监测报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 宠物类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | UV消毒安全监测报告-20260312172200001 | 猫 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地消毒区域视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_uv_safety_monitor_analysis --input /path/to/uv_room.mp4 --pet-type cat --open-id your-open-id

# 分析网络消毒区域视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_uv_safety_monitor_analysis --url https://example.com/uv_room.mp4 --pet-type dog --open-id your-open-id

# 显示历史监测报告/显示报告清单列表
python -m scripts.smyx_uv_safety_monitor_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_uv_safety_monitor_analysis --input video.mp4 --pet-type cat --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_uv_safety_monitor_analysis --input video.mp4 --pet-type cat --open-id your-open-id --output result.json
```
