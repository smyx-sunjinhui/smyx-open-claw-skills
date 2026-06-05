---
name: "smyx-eye-anomaly-detection-analysis"
description: "AI-powered pet eye anomaly detection from close-up facial images/video. Detects conjunctival redness, abnormal tearing/tear stains, and pupil/cornea opacity (cataract / corneal edema), then outputs anomaly alerts to help owners catch eye disease risks early. Scenarios: daily home health self-check, boarding center routine inspection, animal hospital triage, senior pet cataract monitoring. | 通过宠物摄像头捕捉宠物面部近景视频，利用AI视觉分析技术检测眼部充血（结膜颜色发红）、异常流泪（泪痕严重或持续性溢泪）、瞳孔区域浑浊（可能为白内障或角膜水肿）等异常征象，输出异常提示，帮助主人及早发现眼部疾病风险。适用于日常健康监测、老年宠物护理及宠物医院预检。应用场景：宠物家庭日常健康自检、宠物寄养中心巡检、宠物医院门诊初筛、老年宠物白内障监测。"
version: "1.0.0"
---

# Pet Eye Anomaly Detection (Redness / Tearing / Cataract) | 宠物眼睛异常识别（红肿/流泪/白内障）

AI-powered pet eye anomaly detection from close-up facial images/video. Detects conjunctival redness, abnormal tearing/tear stains, and pupil/cornea opacity (cataract / corneal edema), then outputs anomaly alerts to help owners catch eye disease risks early. Scenarios: daily home health self-check, boarding center routine inspection, animal hospital triage, senior pet cataract monitoring.

通过宠物摄像头捕捉宠物面部近景视频，利用AI视觉分析技术检测眼部充血（结膜颜色发红）、异常流泪（泪痕严重或持续性溢泪）、瞳孔区域浑浊（可能为白内障或角膜水肿）等异常征象，输出异常提示，帮助主人及早发现眼部疾病风险。适用于日常健康监测、老年宠物护理及宠物医院预检。应用场景：宠物家庭日常健康自检、宠物寄养中心巡检、宠物医院门诊初筛、老年宠物白内障监测。

## 🎯 AI 角色

**假设你是一个专业的宠物眼科健康AI。你的任务是分析宠物面部近景视频或高清图像，检测双眼的结膜颜色、泪痕程度、瞳孔及角膜透明度，识别是否存在充血、异常流泪、白内障等眼部异常。不要提供医疗诊断，仅输出基于视觉的异常提示。**

## 任务目标

- 本 Skill 用于：通过宠物面部近景图像/视频进行双眼健康视觉评估，识别结膜充血、异常流泪、角膜/瞳孔浑浊等异常征象，输出分级异常提示
- 能力包含：双眼定位、结膜颜色分析、泪痕程度评估、瞳孔/角膜透明度检测、异常征象分级、左右眼对比、健康建议输出
- 触发条件:
    1. **默认触发**：当用户提供宠物面部近景图像或视频需要分析时，默认触发本技能进行眼部异常识别
    2. 当用户明确需要眼部健康检查时，提及眼睛、结膜、充血、流泪、泪痕、白内障、角膜、瞳孔等关键词，并且上传了图像或视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史眼部检查报告、历史眼睛异常报告、眼部报告清单、显示所有眼睛报告、查询眼部健康记录
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有眼部检查报告"、"显示眼睛异常报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_eye_anomaly_detection_analysis --list --open-id` 参数调用 API
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

**在执行眼部异常识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备图像/视频输入**
        - 提供本地宠物面部近景图像/视频或网络 URL
        - 拍摄建议：
          - 距离 20-40cm 的**面部正面或侧面近景**，确保双眼清晰可见
          - **光线充足**（自然光最佳），避免逆光、过曝、阴影遮挡眼部
          - 尽量让宠物**保持安静、睁眼**，避免动态模糊
          - 视频时长建议 5-15 秒即可
        - 支持图像（jpg/png）和视频（mp4/avi/mov）格式
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行眼部异常识别**
        - 调用 `-m scripts.smyx_eye_anomaly_detection_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地宠物面部近景图像/视频文件路径
            - `--url`: 网络宠物面部近景图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 宠物类型，可选值：cat/dog/other，默认 cat
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示眼睛异常识别历史报告列表清单
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看识别结果**
        - 接收结构化的眼部健康评估报告
        - 包含：**左/右眼结膜颜色**（正常粉色 / 轻度充血 / 明显充血）、**泪痕程度**（无 / 轻度 / 严重）、**异常流泪检测**（持续性溢泪标记）、**瞳孔与角膜透明度**（清澈 / 轻度浑浊 / 明显浑浊）、**白内障/角膜水肿征象**、**异常等级**、**健康建议**（如"左眼结膜充血明显，建议就医检查"）
        - **重要提示**：仅基于视觉特征输出异常提示，**不提供医疗诊断**，确诊需由专业兽医检查

## 👁️ 检测项目说明

| 检测项 | 关键指标 | 可能提示的疾病方向 |
|--------|----------|--------------------|
| 🔴 结膜充血 | 结膜颜色发红、血管明显 | 结膜炎、角膜炎、过敏 |
| 💧 异常流泪 | 严重泪痕、持续溢泪 | 泪管阻塞、结膜炎、眼睑内翻 |
| 🌫️ 角膜浑浊 | 角膜出现灰白雾状 | 角膜水肿、角膜溃疡 |
| ⚪ 瞳孔浑浊 | 瞳孔区域呈现蓝白色雾状 | 白内障、晶状体硬化 |
| 👀 左右不对称 | 双眼瞳孔大小、颜色明显差异 | 神经系统问题、外伤、青光眼 |

## 🚨 异常分级与建议

| 异常等级 | 表现 | 建议 |
|----------|------|------|
| 🟢 正常 | 双眼明亮、结膜粉色、角膜清澈、无泪痕 | 持续日常监测即可 |
| 🟡 轻度异常 | 轻微泪痕或单侧轻度充血 | 观察 24-48 小时，注意环境清洁与饮食 |
| 🟠 中度异常 | 明显充血、严重泪痕或局部浑浊 | 建议尽快预约兽医检查 |
| 🔴 重度异常 | 明显角膜/瞳孔浑浊、双眼明显不对称、视力疑似受损 | ⚠️ 尽快就医，避免视力进一步损伤 |

## 💡 高风险品种与人群

| 类别 | 重点关注原因 |
|------|--------------|
| 老年宠物（>7岁） | 白内障、晶状体硬化高发 |
| 短鼻犬猫（巴哥、英斗、波斯、加菲等） | 眼球突出，易角膜损伤、泪溢 |
| 长毛品种（比熊、博美、贵宾、波斯） | 毛发刺激易引发流泪、结膜炎 |
| 糖尿病患宠 | 易并发糖尿病性白内障 |

## 资源索引

- 必要脚本：见 [scripts/smyx_eye_anomaly_detection_analysis.py](scripts/smyx_eye_anomaly_detection_analysis.py)(用途：调用 API 进行宠物眼部异常识别，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持 jpg/png/mp4/avi/mov 格式，最大 10MB
- **拍摄要求**：面部近景、光线充足、双眼清晰；模糊/逆光/眼睛闭合的图像无法得出可靠结果
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **识别结果仅供视觉参考，绝不替代专业兽医诊断**；任何疑似异常都建议就医确诊
- 部分品种眼部生理特征本身偏红或泪痕较重，需结合个体基线判断
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 当显示历史识别报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"报告名称"、"宠物类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`眼睛异常识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 宠物类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 眼睛异常识别报告-20260312172200001 | 猫 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地宠物面部近景图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_eye_anomaly_detection_analysis --input /path/to/pet_face.jpg --pet-type cat --open-id your-open-id

# 分析网络宠物面部近景图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_eye_anomaly_detection_analysis --url https://example.com/pet_face.mp4 --pet-type dog --open-id your-open-id

# 显示历史识别报告/显示报告清单列表（自动触发关键词：查看历史眼部检查报告、眼部报告清单等）
python -m scripts.smyx_eye_anomaly_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_eye_anomaly_detection_analysis --input eye.jpg --pet-type cat --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_eye_anomaly_detection_analysis --input eye.jpg --pet-type cat --open-id your-open-id --output result.json
```
