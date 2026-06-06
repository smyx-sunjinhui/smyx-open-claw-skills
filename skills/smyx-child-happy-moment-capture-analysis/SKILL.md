---
name: "smyx-child-happy-moment-capture-analysis"
description: "Using fixed cameras at home, kindergartens, or playgrounds, the system analyzes children's behavior and expressions in real time to identify happy moments: big laughter (mouth corners sharply raised, eyes squinted into crescents, teeth showing), jumping (both feet off the ground), clapping (rhythmic hand clapping), and joyful reactions to praise or rewards. When a happy event is detected, the system automatically captures a high-definition photo or a short video clip (2 seconds before and after), generates a 'Happy Diary' pushed to the parent's mobile APP, and plays an encouragement sound (such as 'You're amazing!' or cheerful music). This helps record positive emotions during the child's growth, strengthens parent-child interaction, and nurtures confidence. Application scenarios: family living rooms, kindergarten classrooms, playgrounds, parent-child activity centers. The system monitors in real time, automatically captures happy moments, and generates daily/weekly happiness collections. Skill features: a child's happy moments are short and precious; busy parents often miss them. AI auto-capture helps preserve beautiful memories, while instant encouragement reinforces positive behavior and supports mental well-being. Can be integrated into smart cameras, kids' watches or parenting APPs as a heartwarming parent-child interaction feature. | 通过家庭、幼儿园或游乐场的固定摄像头，实时分析儿童的行为和表情，识别开心瞬间：大笑（面部表情：嘴角大幅度上翘、眼睛眯成月牙、露出牙齿）、蹦跳（双脚离地跳跃）、拍手（双手有节奏地拍击）、以及接收到表扬或奖励时的愉悦反应。当检测到开心事件时，自动抓拍高清图片或短视频（前后2秒），生成'开心日记'推送至家长手机APP，并播放鼓励音效（如'你真棒！'或欢快音乐）。该技能有助于记录孩子成长中的积极情绪，增强亲子互动，培养自信心。应用场景：家庭客厅、幼儿园教室、游乐场、亲子活动中心。系统实时监测，自动捕捉孩子的开心时刻，生成每日/每周快乐合集。技能特点：孩子的快乐时刻短暂且珍贵，家长常因忙碌而错过。通过AI自动抓拍，可帮助家长留存美好回忆，同时通过即时鼓励强化孩子的积极行为，促进心理健康。该技能可集成到智能摄像头、儿童手表或育儿APP中，成为亲子互动的暖心功能。"
version: "1.0.1"
---

# Child Happy Moment Capture & Positive Reinforcement | 儿童开心时刻识别与正向激励

Using fixed cameras at home, kindergartens, or playgrounds, the system analyzes children's behavior and expressions in real time to identify happy moments: big laughter (mouth corners sharply raised, eyes squinted into crescents, teeth showing), jumping (both feet off the ground), clapping (rhythmic hand clapping), and joyful reactions to praise or rewards. When a happy event is detected, the system automatically captures a high-definition photo or a short video clip (2 seconds before and after), generates a 'Happy Diary' pushed to the parent's mobile APP, and plays an encouragement sound (such as 'You're amazing!' or cheerful music). This helps record positive emotions during the child's growth, strengthens parent-child interaction, and nurtures confidence. Application scenarios: family living rooms, kindergarten classrooms, playgrounds, parent-child activity centers. The system monitors in real time, automatically captures happy moments, and generates daily/weekly happiness collections. Skill features: a child's happy moments are short and precious; busy parents often miss them. AI auto-capture helps preserve beautiful memories, while instant encouragement reinforces positive behavior and supports mental well-being. Can be integrated into smart cameras, kids' watches or parenting APPs as a heartwarming parent-child interaction feature.

通过家庭、幼儿园或游乐场的固定摄像头，实时分析儿童的行为和表情，识别开心瞬间：大笑（面部表情：嘴角大幅度上翘、眼睛眯成月牙、露出牙齿）、蹦跳（双脚离地跳跃）、拍手（双手有节奏地拍击）、以及接收到表扬或奖励时的愉悦反应。当检测到开心事件时，自动抓拍高清图片或短视频（前后2秒），生成'开心日记'推送至家长手机APP，并播放鼓励音效（如'你真棒！'或欢快音乐）。该技能有助于记录孩子成长中的积极情绪，增强亲子互动，培养自信心。应用场景：家庭客厅、幼儿园教室、游乐场、亲子活动中心。系统实时监测，自动捕捉孩子的开心时刻，生成每日/每周快乐合集。技能特点：孩子的快乐时刻短暂且珍贵，家长常因忙碌而错过。通过AI自动抓拍，可帮助家长留存美好回忆，同时通过即时鼓励强化孩子的积极行为，促进心理健康。该技能可集成到智能摄像头、儿童手表或育儿APP中，成为亲子互动的暖心功能。

## 🎯 AI 角色

**假设你是一个专业的儿童积极情绪记录 AI。你的任务是分析固定摄像头的实时视频（可选叠加音频），检测儿童的面部表情（大笑：嘴 + 眼周肌肉同时收缩的杜兴式真笑）、肢体动作（蹦跳：双脚同时离地；拍手：有节奏拍击 ≥ 2 次；双手高举庆祝；拥抱）以及笑声强度，多信号融合判断是否为显著开心事件。当确认开心事件时，抓拍前后 2 秒短视频和高清照片，输出鼓励语动作（智能音箱语音 / 欢快音效 / 家长 APP 推送），生成每日/每周快乐合集。不提供任何心理分析或性格评估，仅输出基于视觉的行为识别结果。保留温和、克制的激励节奏（每次播放间隔 ≥ 5 分钟），避免强化形成"表演式快乐"。**

## 任务目标

- 本 Skill 用于：基于家庭客厅/幼儿园教室/游乐场/亲子活动中心固定摄像头（可选音频）实时视频，识别儿童开心瞬间（大笑强度 / 杜兴式真笑 / 笑容时长 / 蹦跳 / 拍手 / 跳舞转圈 / 拥抱 / 双手高举庆祝 + 笑声音频强度 + 欢呼）→ 多信号融合避免误抓 → 抓拍前后 2 秒短视频 + 高清照片（≥ 1080p）→ 触发 3 类鼓励动作（智能音箱语音 / 欢快音效 / 家长 APP 推送）→ 每日 22:00 自动汇总当日 ≥ notable 事件 → 每周日晚 21:00 生成 3-5 段精选快乐合集
- 能力包含：杜兴式真笑识别（嘴+眼周肌肉同时收缩）、大笑强度评分（0-100）、双脚同时离地蹦跳检测、有节奏拍手识别（≥ 2 次）、跳舞/转圈识别、拥抱事件检测、双手高举庆祝姿态识别、笑声音频强度与频谱欢快度评估、欢呼/兴奋声识别、社交上下文判别（with_parent / with_peer / with_teacher / alone_play）、触发上下文识别（praise_from_adult / new_toy / game_win / pet_interaction，仅用于推送文案不做长期记录）、4 路多信号融合触发规则、3 档强度（mild / notable / peak）、自动抓拍片段安全审核（仅露面+正向情绪+衣着整齐才入库）、每日/每周快乐合集生成、温和克制的鼓励节奏控制
- 触发条件:
    1. **默认触发**：当用户提供家庭客厅/幼儿园教室/游乐场/亲子活动中心固定摄像头视频 URL 或文件需要分析时，默认触发本技能进行儿童开心时刻识别与正向激励
    2. 当用户明确提及孩子开心、宝宝大笑、儿童欢乐瞬间、开心日记、亲子互动抓拍、儿童正向激励、快乐合集等关键词，并且上传了视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看孩子开心日记历史、快乐合集清单、每日/每周快乐合集、查询历史开心瞬间记录、显示所有儿童快乐抓拍报告、显示亲子互动暖心瞬间，查询开心瞬间清单
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有孩子开心日记"、"
       显示所有快乐合集"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_child_happy_moment_capture_analysis --list --open-id` 参数调用 API
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

**在执行儿童开心时刻识别与正向激励前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备家庭客厅/幼儿园教室/游乐场/亲子活动中心固定摄像头视频输入**
        - 提供本地视频路径或网络 URL，**优先实时流接入**，离线分析单段建议 ≥ 5 分钟
        - 摄像头建议：能拍到儿童面部和全身
        - 帧率 ≥ 10 FPS（推荐 15-25 FPS，便于抓拍清晰快照）、分辨率 ≥ 720p（推荐 1080p）
        - 音频可选（强烈推荐）：用于识别笑声强度；采样率 ≥ 16kHz
        - 抓拍前后**各 2 秒**短视频，照片 ≥ 1080p
        - 多人场景：家庭场景按家庭注册儿童 ID 跟踪；公共场景（幼儿园/游乐场）使用匿名儿童编号
        - **公共场景必须事先获得所有出场儿童的家长书面同意**，否则启用人脸马赛克
        - 可选附带：儿童姓名、年龄、阈值覆盖（big_smile_intensity / laughter_audio_intensity）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id（家长/教师授权）
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行儿童开心时刻识别与正向激励**
        - 调用 `-m scripts.smyx_child_happy_moment_capture_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地家庭客厅/幼儿园教室/游乐场/亲子活动中心固定摄像头视频文件路径
            - `--url`: 网络家庭客厅/幼儿园教室/游乐场/亲子活动中心固定摄像头视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，儿童积极情绪记录场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，家长/教师授权）
            - `--list`: 显示儿童开心时刻识别与正向激励历史快乐合集清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的儿童开心时刻识别与正向激励报告
        - 包含：事件 ID（event_id）、事件时间戳（event_timestamp）、儿童 ID（child_id：家庭注册 / 公共场景匿名）、社交上下文（social_context：with_parent / with_peer / with_teacher / alone_play）、触发上下文（triggered_by：praise_from_adult / new_toy / game_win / pet_interaction / unknown，**仅用于推送文案**）、信号详情（signal_breakdown：面部 big_smile_intensity / genuine_smile_detected / smile_duration_sec + 肢体 jumping / clapping / dancing / hug / arms_raised + 音频 laughter_intensity / cheer）、开心强度（happy_event_intensity_level：mild / notable / peak）、抓拍照片 URL（snapshot_photo_url，关键瞬间帧，家长可一键删除）、短视频片段 URL（clip_video_url，前后 2 秒）、鼓励动作列表（encouragement_action：smart_speaker_voice / play_celebration_sound / parent_app_push，每项含 action_type / message / target）、每日快乐合集（daily_happiness_collection，每日 22:00 自动汇总）、每周快乐合集（weekly_happiness_collection，每周日晚 21:00 生成 3-5 段精选）、建议动作（recommend_action：push_snapshot_to_parent / generate_daily_collection / generate_weekly_collection / play_encouragement_audio）
        - **重要提示**：仅输出基于视觉与（可选）音频的客观开心瞬间识别和正向激励抓拍，**不构成任何心理评估或性格分析**；正向激励应作为亲子互动的暖心补充，**不能替代真实陪伴**

## 资源索引

- 必要脚本：见 [scripts/smyx_child_happy_moment_capture_analysis.py](scripts/smyx_child_happy_moment_capture_analysis.py)(
  用途：调用 API 进行儿童开心时刻识别与正向激励，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口规范、面部/肢体/音频信号、多信号融合触发规则、3 档强度和红线约束时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 mp4/avi/mov 视频，最大 10MB；**关键**：分辨率 ≥ 720p 推荐 1080p；帧率 ≥ 10 FPS 推荐 15-25 FPS
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **多信号融合**避免误抓：单一信号（仅微笑、仅跳）不应触发，必须满足 4 路融合规则任一
- 抓拍片段保存前**必须经过安全审核**：仅露面 + 正向情绪 + 衣着整齐才入库；负面或尴尬瞬间（哭泣、摔倒、衣物不整）**禁止**保存
- 红线约束：
    - **禁止**对儿童做"性格内向/外向 / 高情商 / 抑郁倾向"等任何心理评估或贴标签
    - **禁止**将儿童影像用于商业广告、人脸识别训练数据集、AIGC 训练
    - **禁止**向家长以外的第三方共享儿童影像（亲戚需家长授权才能查看）
    - **禁止**长期存储未被家长保存的原始视频（≤ 7 天自动清理）
    - **禁止**鼓励音效音量过响或频率过高，避免打断专注力或形成依赖
- **必须**为家长提供：一键删除单个抓拍 / 暂停今日抓拍 / 永久退出该功能 的简单入口
- 鼓励音效**建议每次播放间隔 ≥ 5 分钟**，**避免过度强化**形成"表演式快乐"
- 公共场景（幼儿园/游乐场）必须**事先获得所有出场儿童的家长书面同意**，否则对未授权儿童**自动人脸马赛克**
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史快乐合集清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"事件数/最高强度/社交上下文"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`儿童开心日记-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 事件数/最高强度/社交上下文 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 儿童开心日记-20260312172200001 | 12 次 / peak / with_parent | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地儿童活动视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_happy_moment_capture_analysis --input /path/to/livingroom.mp4 --open-id your-open-id

# 分析网络儿童活动视频/实时流（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_child_happy_moment_capture_analysis --url https://example.com/livingroom.mp4 --open-id your-open-id

# 显示历史开心日记清单（自动触发关键词：查看孩子开心日记历史、快乐合集清单等）
python -m scripts.smyx_child_happy_moment_capture_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_child_happy_moment_capture_analysis --input lr.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_child_happy_moment_capture_analysis --input lr.mp4 --open-id your-open-id --output result.json
```
