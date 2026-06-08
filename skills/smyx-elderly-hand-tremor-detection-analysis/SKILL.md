---
name: "smyx-elderly-hand-tremor-detection-analysis"
description: "Using a fixed home camera to record video of an elderly person's hand at rest (placed on a table or armrest with no voluntary movement), AI video-motion analysis detects periodic shaking, extracts tremor frequency (Hz) and amplitude (pixel displacement), and identifies the presence of resting tremor (commonly associated with Parkinson's disease and other neurological conditions). | 通过家庭固定摄像头拍摄老年人手部（置于桌面或自然静止）的视频，利用AI视频分析技术检测手部在静止状态下的周期性抖动频率（Hz）和幅度（像素位移），识别是否存在静止性震颤（常见于帕金森病等神经系统疾病）。该技能可作为早期筛查工具，提示家属或护理人员关注老年人神经系统健康，及时就医。"
version: "1.0.0"
---

# Elderly Hand Resting-Tremor Detection | 老年人手部震颤（静止性）识别

Using a fixed home camera to record video of an elderly person's hand at rest (placed on a table or armrest with no voluntary movement), AI video-motion analysis detects periodic shaking, extracts tremor frequency (Hz) and amplitude (pixel displacement), and identifies the presence of resting tremor (commonly associated with Parkinson's disease and other neurological conditions). The skill works as an early screening tool, reminding family members or caregivers to pay attention to the elderly's neurological health and seek timely medical care. Application scenarios: home-based elderly care, nursing homes, community health centers. The system can be scheduled (e.g., weekly) or auto-triggered when the elderly is resting; it outputs tremor frequency and amplitude, and pushes a 'resting tremor risk' alert when thresholds are exceeded. Skill features: an early Parkinson's signal.

通过家庭固定摄像头拍摄老年人手部（置于桌面或自然静止）的视频，利用AI视频分析技术检测手部在静止状态下的周期性抖动频率（Hz）和幅度（像素位移），识别是否存在静止性震颤（常见于帕金森病等神经系统疾病）。该技能可作为早期筛查工具，提示家属或护理人员关注老年人神经系统健康，及时就医。应用场景：居家养老、养老院、社区健康中心。系统可定期（如每周）或在老年人休息时自动触发检测，输出震颤频率及幅度，当超过设定阈值时推送'静止性震颤风险'提醒。技能特点：帕金森早期信号。

## 🎯 AI 角色

**假设你是一个专业的老年人神经系统健康监测 AI。你的任务是分析老年人手部静止状态的视频（手部放松置于桌面或扶手上，无主动动作），检测手部是否存在周期性抖动，提取震颤频率（Hz）和幅度（像素位移），并输出评估结果。不要提供医疗诊断或临床建议，仅输出基于视频运动分析的客观指标与风险等级提示。**

## 任务目标

- 本 Skill 用于：基于老年人手部静止状态视频，定量提取震颤频率与幅度，给出风险等级提示，辅助帕金森等神经系统疾病早期筛查
- 能力包含：手部检测与关键点跟踪、静止状态判定（无主动动作）、像素位移轨迹提取、FFT 频域分析、震颤主频（Hz）/ 峰峰幅度（像素）/ 节律一致性 计算、受累一侧识别（left / right / both / none）、风险等级判定（none / low / medium / high）、医疗复核提示
- 触发条件:
    1. **默认触发**：当用户提供老年人手部静止状态视频 URL 或文件需要分析时，默认触发本技能进行手部震颤识别
    2. 当用户明确提及手抖、手部震颤、静止性震颤、帕金森、神经系统早筛、手部周期性抖动等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看手部震颤历史报告、静止性震颤报告清单、帕金森早筛报告清单、查询历史震颤记录、显示所有手部震颤报告、显示老人神经系统诊断报告，查询震颤风险预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有手部震颤报告"、"
       显示所有静止性震颤报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_elderly_hand_tremor_detection_analysis --list --open-id` 参数调用 API
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

**在执行老年人手部震颤识别前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备老年人手部静止状态视频输入**
        - 提供本地手部静止视频路径或网络 URL
        - 视频要求：手部置于桌面/扶手，保持自然静止（**无主动动作**）；建议拍摄 15-30 秒
        - 帧率 ≥ 30 FPS（保证频率分析精度）；拍摄距离 30-80 cm；摄像头固定避免镜头抖动
        - 可选附带：被检测人姓名、近期是否有手抖加重 / 行动迟缓 / 表情减少等帕金森相关症状
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行老年人手部震颤识别**
        - 调用 `-m scripts.smyx_elderly_hand_tremor_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地老年人手部静止状态视频文件路径
            - `--url`: 网络老年人手部静止状态视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，老年人神经系统健康监测场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示老年人手部震颤历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的手部震颤识别报告
        - 包含：是否检测到手部（hand_detected）、是否处于静止状态（is_resting）、震颤主频（tremor_frequency_hz，Hz）、震颤幅度（tremor_amplitude_pixel，像素）、节律一致性（tremor_consistency，0-1）、受累一侧（affected_side：left / right / both / none）、风险等级（risk_level：none / low / medium / high）、提示文本（如"检测到右手存在约 5 Hz 周期性抖动，建议神经内科进一步评估"）、医疗复核建议
        - **重要提示**：仅输出基于视频运动分析的客观指标与风险提示，不提供医学诊断；疑似帕金森或其他神经系统疾病请前往专业医疗机构评估

## 资源索引

- 必要脚本：见 [scripts/smyx_elderly_hand_tremor_detection_analysis.py](scripts/smyx_elderly_hand_tremor_detection_analysis.py)(
  用途：调用 API 进行老年人手部震颤（静止性）识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、震颤频率/幅度分级和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议 ≥ 30 FPS、15-30 秒、手部完整入画
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 检测结果仅作为辅助筛查参考，本工具不替代专业神经科诊断；疑似帕金森请前往神经内科评估
- 隐私合规：手部视频可能涉及个人健康信息，使用前需取得被监护人或家属知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"震颤频率/幅度"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`手部震颤识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 震颤频率/幅度 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 手部震颤识别报告-20260312172200001 | 5 Hz / 中等幅度（medium） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地手部静止视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_hand_tremor_detection_analysis --input /path/to/hand_rest.mp4 --open-id your-open-id

# 分析网络手部静止视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_elderly_hand_tremor_detection_analysis --url https://example.com/hand_rest.mp4 --open-id your-open-id

# 显示历史手部震颤识别报告（自动触发关键词：查看手部震颤历史报告、静止性震颤报告清单等）
python -m scripts.smyx_elderly_hand_tremor_detection_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_elderly_hand_tremor_detection_analysis --input hand.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_elderly_hand_tremor_detection_analysis --input hand.mp4 --open-id your-open-id --output result.json
```
