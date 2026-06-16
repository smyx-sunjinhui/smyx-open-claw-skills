---
name: "smyx-child-social-interaction-analysis-analysis"
description: "Using fixed cameras in kindergartens or early-education centers, the system analyzes multi-person video to detect social-interaction behaviors among children, including approach (distance < 1 m), conversation (face-to-face with mouth movement), and cooperative play (collaborative play, chasing, etc.). | 通过幼儿园或早教中心的固定摄像头，分析多人视频，检测儿童之间的社交互动行为，包括接近（距离<1米）、对话（面对面且嘴部运动）、共同游戏（合作玩耍、追逐等）。系统定期生成社交互动热力图，为教师提供参考。"
version: "1.0.2"
---

# Child Social Interaction Frequency & Duration Analysis | 儿童社交互动频次与时长分析

Using fixed cameras in kindergartens or early-education centers, the system analyzes multi-person video to detect social-interaction behaviors among children, including approach (distance < 1 m), conversation (face-to-face with mouth movement), and cooperative play (collaborative play, chasing, etc.). It counts the number of interactions, total duration, and initiator (the child who actively approaches or initiates the activity) for each pair of children and generates a social-interaction report. The skill helps teachers understand the development of children's social abilities and identify isolated or excluded children. Application scenarios: kindergartens, early-education centers, playgrounds. The system periodically generates social-interaction heatmaps as a reference for teachers. Skill features: a reference for early screening of autism spectrum tendencies.

通过幼儿园或早教中心的固定摄像头，分析多人视频，检测儿童之间的社交互动行为，包括接近（距离<1米）、对话（面对面且嘴部运动）、共同游戏（合作玩耍、追逐等）。统计每对儿童之间的互动次数、总时长、发起方（主动接近或发起游戏的一方），生成社交互动报告。该技能有助于教师了解儿童的社交能力发展，识别孤僻或被排斥的儿童。应用场景：幼儿园、早教中心、游乐场。系统定期生成社交互动热力图，为教师提供参考。技能特点：孤独症早筛参考。

## 🎯 AI 角色

**假设你是一个专业的儿童社交行为分析 AI。你的任务是分析幼儿园或游乐场固定摄像头的视频，检测儿童之间的社交互动（接近、对话、共同游戏），统计互动次数、时长以及发起方。不要提供心理诊断或孤独症诊断，仅输出基于视觉的社交行为统计数据与方向性关注提示。**

## 任务目标

- 本 Skill 用于：基于多人场景视频，统计每对儿童的社交互动行为（接近 / 对话 / 共同游戏 / 身体接触）频次、总时长与发起方，生成社交互动热力图，识别互动量显著偏低的儿童
- 能力包含：多儿童检测与跨帧 ID 跟踪、面对面朝向估计、嘴部运动识别（对话）、群体行为识别（合作 / 追逐）、儿童间距离估算（≤ 1 m 触发接近）、互动事件分类（approach / conversation / cooperative_play / physical_contact）、发起方判定（initiator）、孤僻/被排斥候选识别（loner_candidates）、社交互动热力图生成、班级整体活跃度概览
- 触发条件:
    1. **默认触发**：当用户提供幼儿园/早教中心/游乐场多人场景视频 URL 或文件需要分析时，默认触发本技能进行儿童社交互动分析
    2. 当用户明确提及儿童社交、互动、对话、合作游戏、孤僻儿童、被排斥、社交能力发展、孤独症早筛、班级互动热力图等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看儿童社交互动历史报告、社交互动报告清单、班级社交热力图清单、查询历史社交互动记录、显示所有儿童社交报告、显示班级社交诊断报告，查询孤僻儿童预警清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有儿童社交报告"、"
       显示所有社交互动报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_child_social_interaction_analysis_analysis --list --open-id` 参数调用 API
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

**在执行儿童社交互动频次与时长分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备幼儿园/游乐场多人场景视频输入**
        - 提供本地多人场景视频路径或网络 URL
        - 摄像头建议固定俯视/广角，覆盖完整活动区域；视频时长建议 ≥ 1 分钟，帧率 ≥ 10 FPS
        - 可选附带：班级名单（稳定 ID 关联）、场地标定（用于像素 → 米的距离换算）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行儿童社交互动频次与时长分析**
        - 调用 `-m scripts.smyx_child_social_interaction_analysis_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地幼儿园/游乐场多人场景视频文件路径
            - `--url`: 网络幼儿园/游乐场多人场景视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，儿童社交行为分析场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示儿童社交互动历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的社交互动分析报告
        - 包含：检测到的儿童数量（children_detected_count）、儿童 ID 列表（child_ids）、每对儿童的互动统计（interaction_pairs：pair_id / interaction_count / total_duration_sec / 各事件类型分项）、主动发起方统计（initiator_stats）、孤僻/被排斥候选名单（loner_candidates）、社交互动热力图 URL（social_heatmap_url）、班级整体活跃度概览（summary）、异常提示（如"3 号儿童 30 分钟内互动 0 次，建议教师关注"）
        - **重要提示**：仅输出基于视觉的社交行为统计数据与方向性关注提示，不提供心理诊断或孤独症诊断

## 资源索引

- 必要脚本：见 [scripts/smyx_child_social_interaction_analysis_analysis.py](scripts/smyx_child_social_interaction_analysis_analysis.py)(
  用途：调用 API 进行儿童社交互动频次与时长分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、社交事件枚举和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；建议固定俯视/广角，覆盖完整活动区域
- 距离 1 m 阈值的精度依赖标定信息，若未提供场地标定将采用经验估算，建议同条件下纵向对比趋势
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 统计结果仅作为教师/家长教育辅助参考，本工具不替代儿童心理/发育评估；如怀疑孤独症等发育异常请前往专业医疗机构
- 隐私合规：幼儿园多人视频涉及未成年人隐私，使用前需取得监护人/园方知情同意，并妥善保管/加密相关录像
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"班级活跃度"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`儿童社交互动分析报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 班级活跃度 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 儿童社交互动分析报告-20260312172200001 | 中等（关注 1 名孤僻候选） | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地幼儿园多人视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_social_interaction_analysis_analysis --input /path/to/classroom.mp4 --open-id your-open-id

# 分析网络幼儿园多人视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_social_interaction_analysis_analysis --url https://example.com/classroom.mp4 --open-id your-open-id

# 显示历史社交互动分析报告（自动触发关键词：查看儿童社交互动历史报告、班级社交热力图清单等）
python -m scripts.smyx_child_social_interaction_analysis_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_child_social_interaction_analysis_analysis --input classroom.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_child_social_interaction_analysis_analysis --input classroom.mp4 --open-id your-open-id --output result.json
```
