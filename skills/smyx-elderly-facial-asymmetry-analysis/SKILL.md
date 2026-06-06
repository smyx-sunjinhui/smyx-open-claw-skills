---
name: "smyx-elderly-facial-asymmetry-analysis"
description: "Using a fixed home camera to capture frontal facial images or short videos of the elderly, the system uses AI facial-landmark detection to analyze features such as the height difference between left/right mouth corners, the symmetry of nasolabial folds (smile lines), and the asymmetry of eyebrow lifts, and computes a facial asymmetry index (0-100%). The skill works as an auxiliary screening tool for stroke prodromal signs, reminding family members or caregivers to pay attention to facial paralysis or mouth-corner deviation and seek timely medical care. Application scenarios: home-based elderly care, nursing homes, community health centers. The system can be scheduled (e.g., daily) or auto-triggered when abnormal behavior is observed; it outputs an asymmetry index and pushes a 'facial asymmetry risk' alert when a threshold is exceeded. Skill features: facial asymmetry is an important early sign of stroke. AI periodic screening helps families detect anomalies in time and preserve the thrombolytic treatment window. Can be integrated into smart cameras or health-management platforms as a practical feature for at-home senior health monitoring. | 通过家庭固定摄像头拍摄老年人正面面部图像或视频，利用AI面部关键点检测技术分析左右嘴角的高度差、鼻唇沟（法令纹）的对称性、眉毛抬高的差异等特征，计算面部不对称指数（0-100%）。该技能可作为脑卒中（中风）前兆的辅助筛查工具，提示家属或护理人员关注老年人是否存在面瘫、口角歪斜等神经系统异常，及时就医。应用场景：居家养老、养老院、社区健康中心。系统可定期（如每天）或在老年人出现异常行为时自动触发检测，输出不对称指数，当超过设定阈值时推送'面部不对称风险'提醒。技能特点：面部不对称是脑卒中的重要早期体征之一。通过AI定期检测，可帮助家属及时发现异常，争取溶栓治疗时间窗。该技能可集成到智能摄像头或健康管理平台中，成为老年人居家健康监测的实用功能。"
version: "1.0.1"
---

# Elderly Facial Asymmetry / Mouth-Corner Deviation Detection | 老年人面部不对称/口角歪斜识别

Using a fixed home camera to capture frontal facial images or short videos of the elderly, the system uses AI facial-landmark detection to analyze features such as the height difference between left/right mouth corners, the symmetry of nasolabial folds (smile lines), and the asymmetry of eyebrow lifts, and computes a facial asymmetry index (0-100%). The skill works as an auxiliary screening tool for stroke prodromal signs, reminding family members or caregivers to pay attention to facial paralysis or mouth-corner deviation and seek timely medical care. Application scenarios: home-based elderly care, nursing homes, community health centers. The system can be scheduled (e.g., daily) or auto-triggered when abnormal behavior is observed; it outputs an asymmetry index and pushes a 'facial asymmetry risk' alert when a threshold is exceeded. Skill features: facial asymmetry is an important early sign of stroke. AI periodic screening helps families detect anomalies in time and preserve the thrombolytic treatment window. Can be integrated into smart cameras or health-management platforms as a practical feature for at-home senior health monitoring.

通过家庭固定摄像头拍摄老年人正面面部图像或视频，利用AI面部关键点检测技术分析左右嘴角的高度差、鼻唇沟（法令纹）的对称性、眉毛抬高的差异等特征，计算面部不对称指数（0-100%）。该技能可作为脑卒中（中风）前兆的辅助筛查工具，提示家属或护理人员关注老年人是否存在面瘫、口角歪斜等神经系统异常，及时就医。应用场景：居家养老、养老院、社区健康中心。系统可定期（如每天）或在老年人出现异常行为时自动触发检测，输出不对称指数，当超过设定阈值时推送'面部不对称风险'提醒。技能特点：面部不对称是脑卒中的重要早期体征之一。通过AI定期检测，可帮助家属及时发现异常，争取溶栓治疗时间窗。该技能可集成到智能摄像头或健康管理平台中，成为老年人居家健康监测的实用功能。

## 🎯 AI 角色

**假设你是一个专业的老年人健康监测 AI。你的任务是分析老年人正面面部图像，检测左右两侧面部关键点的对称性，计算不对称指数。不要提供医疗诊断或临床建议，仅输出基于面部几何特征的客观指标与风险等级提示。**

## 任务目标

- 本 Skill 用于：基于老年人正面面部图像或短视频，定量评估面部对称性，给出 0-100% 不对称指数与风险等级提示，辅助卒中/面瘫早期筛查
- 能力包含：正面面部检测与对齐、面部关键点提取（嘴角 / 法令纹 / 眉毛 / 眼裂 / 中线）、左右几何对比、不对称指数计算（0-100%）、口角下垂侧识别（left / right / none）、风险等级判定（normal / mild / moderate / severe）、医疗复核提示
- 触发条件:
    1. **默认触发**：当用户提供老年人正面面部图像或视频 URL/文件需要分析时，默认触发本技能进行面部不对称识别
    2. 当用户明确提及面部不对称、口角歪斜、嘴歪、面瘫、卒中前兆、中风筛查、法令纹不对称、面部下垂等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看面部不对称历史报告、口角歪斜报告清单、面部对称性报告清单、查询历史不对称指数、显示所有面部不对称报告、显示老人面部健康诊断报告，查询面部风险预警清单
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有面部不对称报告"、"
       显示所有口角歪斜报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_elderly_facial_asymmetry_analysis --list --open-id` 参数调用 API
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

**在执行老年人面部不对称/口角歪斜识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备老年人正面面部图像/视频输入**
        - 提供本地正面面部图像/短视频路径或网络 URL
        - 推荐正面、平视、清晰、光照均匀；单图或 3-10 秒静止表情视频均可
        - 拍摄距离建议 30-80 cm，五官区域清晰可见
        - 可选附带：被检测人姓名、近期是否有突发症状（肢体无力 / 言语含糊 / 视物模糊）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行老年人面部不对称识别**
        - 调用 `-m scripts.smyx_elderly_facial_asymmetry_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地老年人正面面部图像/视频文件路径
            - `--url`: 网络老年人正面面部图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，老年人健康监测场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示老年人面部不对称历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的面部不对称识别报告
        - 包含：是否检测到面部（face_detected）、面部不对称指数（asymmetry_index，0-100%）、口角下垂侧（mouth_corner_drop_side：left / right / none）、关键观测指标数值（嘴角差 / 法令纹对称性 / 眉毛抬高差 / 眼裂宽度对称性 / 中线偏移）、风险等级（normal / mild / moderate / severe）、风险提示文本（如"检测到面部不对称指数偏高，建议关注是否存在面瘫或卒中前兆，必要时就医"）、医疗复核提示
        - **重要提示**：仅输出基于面部几何特征的客观指标与风险等级提示，不提供医学诊断；如出现疑似面瘫/口角歪斜伴随其他症状（如肢体无力、言语含糊），请按"中风急救"流程立即就医

## 资源索引

- 必要脚本：见 [scripts/smyx_elderly_facial_asymmetry_analysis.py](scripts/smyx_elderly_facial_asymmetry_analysis.py)(
  用途：调用 API 进行老年人面部不对称/口角歪斜识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、不对称指数分级和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 jpg/png 图像或 mp4/avi/mov 视频，最大 10MB；建议正面、光照均匀、五官清晰
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 检测结果仅作为辅助筛查参考，本工具不替代专业神经科诊断；若疑似中风请按 FAST 原则立即就医
- 隐私合规：面部数据涉及生物特征隐私，使用前需取得被监护人或家属知情同意，并妥善保管/加密相关图像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"不对称指数"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`面部不对称识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 不对称指数 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 面部不对称识别报告-20260312172200001 | 35%（moderate） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地正面面部图像（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_facial_asymmetry_analysis --input /path/to/face.jpg --open-id your-open-id

# 分析网络面部图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_facial_asymmetry_analysis --url https://example.com/face.jpg --open-id your-open-id

# 显示历史面部不对称识别报告（自动触发关键词：查看面部不对称历史报告、口角歪斜报告清单等）
python -m scripts.smyx_elderly_facial_asymmetry_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_elderly_facial_asymmetry_analysis --input face.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_elderly_facial_asymmetry_analysis --input face.jpg --open-id your-open-id --output result.json
```
