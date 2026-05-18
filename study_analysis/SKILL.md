---
name: "study-analysis"
description: "Conducts video analysis of learning behavior for children/students, identifies poor learning habits, provides structured analysis reports and family education improvement suggestions, focusing on learning habit cultivation and behavior correction. | A comprehensive tool designed to analyze video footage of children's and students' learning behaviors. It identifies poor study habits and provides structured analysis reports along with actionable suggestions for family education improvements. The tool is dedicated to fostering positive study habits and facilitating behavioral correction. | 分析孩子学习行为 孩子学习行为分析工具，针对孩子/学生的学习行为进行视频分析，识别不良学习习惯，提供结构化分析报告和家庭教育改善建议，专注学习习惯培养和行为矫正"
version: "1.0.2"
---

# Child Learning Behavior Analysis Tool | 孩子学习行为分析工具

Based on advanced computer vision and behavior recognition algorithms, this feature is specifically designed for
analyzing the learning behaviors of children and students. The system utilizes cameras to capture key behaviors during
study sessions, such as posture, concentration levels, and fidgeting, accurately identifying poor learning habits like
slumping, frequent distraction, and incorrect pen-holding. Combined with time-series analysis, the system generates
structured analysis reports containing concentration curves, behavior frequency statistics, and risk levels. Grounded in
educational psychology principles, it provides parents with personalized suggestions for improving home education—such
as environment optimization and time management techniques—helping children cultivate good study habits and achieve
behavioral correction alongside improved learning efficiency.

本功能基于先进的计算机视觉与行为识别算法，专为孩子及学生的学习行为分析设计。系统通过摄像头捕捉学习过程中的坐姿、专注度、小动作等关键行为，精准识别不良学习习惯（如趴桌、频繁分心、握笔姿势错误等）。结合时间序列分析，系统可生成包含专注度曲线、行为频次统计及风险等级的结构化分析报告，并基于教育心理学原理，为家长提供个性化的家庭教育改善建议（如环境优化、时间管理技巧），助力孩子养成良好学习习惯，实现行为矫正与学习效率提升

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过视频分析对孩子/学生的学习行为进行评估，识别不良学习习惯，发现潜在学习问题，提供结构化分析报告和家庭教育改善建议
- 能力包含：视频行为分析、专注度评估、坐姿姿势评估、学习习惯识别、不良行为检测、家庭教育建议生成
- 触发条件:
    1. **默认触发**：当用户提供需要分析的孩子学习视频 URL 或文件需要进行学习行为分析时，默认触发本技能
    2. 当用户明确需要进行孩子学习行为分析、学习习惯评估、作业行为检查时，提及学习行为、学习习惯、孩子作业、坐姿矫正、分心走神等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史学习报告、学习分析报告清单、学习行为分析列表、显示所有学习分析报告，查询学习行为分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存到技能目录下 attachments
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有学习报告"、"
       显示所有学习行为分析报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.study_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 学习行为分析维度

本技能重点识别评估以下学习行为维度，帮助家长发现不良学习习惯：

1. **专注度评估**
    - 高度专注：持续关注学习内容，很少分心
    - 中度分心：偶尔走神、看手机、东张西望
    - 严重分心：频繁走神，难以保持注意力在学习上

2 **坐姿姿势评估**

- 坐姿端正：腰背挺直，距离书本屏幕合适
- 弯腰驼背：坐姿不正，弯腰趴在桌上
- 歪头斜肩：长期歪头写字，可能影响脊柱发育
- 距离不当：眼睛离书本/屏幕太近

3. **学习行为习惯**
    - 磨蹭拖延：开始作业耗时过长，频繁停顿
    - 边玩边学：同时玩手机/看电视/吃东西
    - 主动思考：主动阅读思考，尝试解题
    - 依赖帮助：一遇到问题就问，不独立思考

4. **不良学习行为识别**
    - 分心走神：频繁被外界干扰吸引注意力
    - 小动作过多：转笔、玩橡皮、抖腿等频繁小动作
    - 抄作业舞弊：偷看参考答案、抄袭他人作业
    - 超时学习：连续学习过长时间不休息

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行学习行为分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、studyC113、study123 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询学习分析报告记录），并询问是否继续

---

- 标准流程:
    1. **准备视频输入**
        - 提供本地视频文件路径或网络视频 URL
        - 确保视频清晰展示孩子学习/做作业过程，光线充足，能够看清动作姿势
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行学习行为分析**
        - 调用 `-m scripts.study_analysis` 处理视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频文件路径（使用 multipart/form-data 方式上传）
            - `--url`: 网络视频 URL 地址（API 服务自动下载）
            - `--analysis-type`: 分析类型，可选值：comprehensive/focus/posture/habit/risk，默认 comprehensive（综合分析）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示学习行为分析历史报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的学习行为分析报告
        - 包含：整体学习评分、各维度评分、不良习惯识别、风险警示、家庭教育改善建议

## 资源索引

- 必要脚本：见 [scripts/study_analysis.py](scripts/study_analysis.py)(用途：调用 API 进行学习行为分析，本地文件使用
  multipart/form-data 方式上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和视频格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- **重要声明**：本分析仅供家庭教育参考，不能替代专业老师或心理咨询师诊断。发现严重学习困难建议及时寻求专业帮助
- 仅在需要时读取参考文档，保持上下文简洁
- 视频要求：支持 mp4/avi/mov 格式，最大 100MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从数据 json 中提取字段 reportImageUrl 作为超链接地址，使用 Markdown 表格格式输出，包含"
  报告名称"、"分析类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`学习分析报告-{记录id}`形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 分析类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 学习分析报告-20260312172200001 | 综合分析 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 综合学习行为分析（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.study_analysis --input /path/to/homework_video.mp4 --analysis-type comprehensive --open-id openclaw-control-ui

# 专注度专项分析（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.study_analysis --url https://example.com/study_video.mp4 --analysis-type focus --open-id openclaw-control-ui

# 坐姿姿势专项分析（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.study_analysis --input /path/to/writing_video.mp4 --analysis-type posture --open-id openclaw-control-ui

# 显示历史分析报告/显示分析报告清单列表/显示历史学习报告（自动触发关键词：查看历史学习报告、历史报告、学习报告清单等）
python -m scripts.study_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.study_analysis --input video.mp4 --analysis-type comprehensive --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.study_analysis --input video.mp4 --analysis-type comprehensive --open-id your-open-id --output result.json
```
