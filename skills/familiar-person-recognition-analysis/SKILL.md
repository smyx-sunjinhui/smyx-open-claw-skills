---
name: "familiar-person-recognition-analysis"
description: "Identifies acquaintances in videos or images through face photo comparison. Supports database enrollment, and the recognition results tell you who is at which location. Suitable for identity verification in homes and office areas. | 熟人识别分析技能，通过人脸图片比对识别视频/图片中的熟人，支持底库录入，识别结果告诉你哪个位置是谁，适用于家庭、办公区域身份核验"
version: "1.0.3"
---

# Acquaintance Recognition & Analysis Skill | 熟人识别分析技能

Leveraging advanced facial recognition algorithms, this feature allows users to pre-enroll faces into a database and
subsequently performs deep intelligent analysis on uploaded videos or images. The system precisely captures facial
features within the frame and conducts real-time comparisons against the database to achieve rapid identification and
verification of known individuals. It clearly annotates the specific location and identity information of each
recognized subject in the image. Ideally suited for home security monitoring and personnel management in office areas,
this technology provides an efficient and precise intelligent solution for identity verification.

该功能通过先进的人脸识别算法，支持用户预先录入人脸底库，进而对上传的视频或图片进行深度智能分析。系统能够精准捕捉画面中的人脸特征并与底库数据进行实时比对，实现对熟人的快速识别与身份确认，并清晰标注出每位识别对象在画面中的具体位置及其身份信息。这项技术完美适用于家庭安全监控与办公区域的人员管理，为身份核验提供了高效、精准的智能化解决方案

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：在图片/视频中识别已知熟人，通过人脸比对匹配人脸库中的目标人员
- 能力包含：人脸检测、人脸特征提取、底库比对、身份识别、结果标注
- **工作流程**：先录入熟人人脸到自定义底库，然后在监控画面中识别出哪些是熟人、分别是谁
- 支持：单张图片识别、视频流帧序列检测
- 触发条件:
    1. **默认触发**：当用户需要在监控画面中识别熟人时，默认触发本技能进行熟人识别分析
    2. 当用户明确需要熟人识别、人脸比对时，提及熟人识别、人脸识别、人脸比对、认出是谁等关键词，并且提供了图片/视频
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史识别报告、熟人识别报告清单、识别报告列表、查询历史识别报告、显示所有识别报告、熟人识别分析报告，查询熟人识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者图片/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有识别报告"、"显示所有识别结果"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.familiar_person_recognition_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 使用说明

- 需要先**录入熟人人脸**到个人人脸底库才能进行识别
- 支持识别：单张图片、短视频中多个面孔分别识别

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行熟人识别分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、face123、recognize456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询识别报告记录），并询问是否继续

---

- 标准流程:
    1. **准备输入**
        - 提供图片/视频文件，需要识别其中的人脸
        - 已提前录入对应熟人到个人人脸底库
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行熟人识别分析**
        - 调用 `-m scripts.familiar_person_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地图片/视频文件路径
            - `--url`: 网络图片/视频 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史熟人识别分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的熟人识别分析报告
        - 包含：输入基本信息、检测到的人脸数量、识别出的熟人名单、每个人脸位置/置信度、识别结果汇总

## 资源索引

- 必要脚本：见 [scripts/familiar_person_recognition_analysis.py](scripts/familiar_person_recognition_analysis.py)(用途：调用
  API 进行熟人识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png/mp4/avi/mov，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 识别结果仅供参考，不能用于法定身份核验
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"输入类型"、"分析时间"、"识别人数"、"点击查看"五列，其中"报告名称"列使用`熟人识别分析报告-{记录id}`形式拼接, "
  点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 输入类型 | 分析时间 | 识别人数 | 点击查看 |
  |----------|----------|----------|----------|----------|
  | 熟人识别分析报告 -20260328221000001 | 图片 | 2026-03-28 22:10:00 |
  2人 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 识别本地图片中的熟人（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.familiar_person_recognition_analysis --input /path/to/door.jpg --open-id openclaw-control-ui

# 识别网络视频中的熟人（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.familiar_person_recognition_analysis --url https://example.com/monitor.mp4 --open-id openclaw-control-ui

# 显示历史识别报告/显示识别报告清单列表/显示历史熟人识别（自动触发关键词：查看历史识别报告、历史报告、识别报告清单等）
python -m scripts.familiar_person_recognition_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.familiar_person_recognition_analysis --input capture.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.familiar_person_recognition_analysis --input capture.jpg --open-id your-open-id --output result.json
```
