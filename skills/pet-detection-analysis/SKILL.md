---
name: "pet-detection-analysis"
description: "Detects cats, dogs, and birds appearing in the target area; supports video stream and image detection, suitable for home pet monitoring scenarios. | 宠物检测技能，检测出目标区域内出现的猫、狗、鸟，支持视频流和图片检测，适用于家庭宠物监控场景"
version: "1.0.0"
---

# Pet Detection Skill | 宠物检测技能

Tailored specifically for home pet monitoring scenarios, this feature is equipped with a high-sensitivity multi-species
recognition algorithm capable of precisely locking onto and distinguishing cats, dogs, and birds within the target area.
The system boasts robust adaptability across all scenarios, perfectly supporting both real-time video stream analysis
and static image detection. Whether monitoring dynamic daily activities or capturing static moments, it delivers
millisecond-level response times and high-precision identification. This technology breaks through the limitations of
single-species recognition, providing a comprehensive and flexible intelligent monitoring solution for multi-pet
households, ensuring that every movement of your pets within the home environment is accurately recorded and perceived.

本功能专为家庭宠物监控场景量身打造，搭载了高灵敏度的多物种识别算法，能够精准锁定并区分目标区域内的猫、狗及鸟类。系统具备强大的全场景适配能力，完美兼容实时视频流分析与静态图片检测，无论是动态的日常活动看护还是静态的画面捕捉，均能实现毫秒级响应与高精度判定。这一技术打破了单一物种识别的局限，为多宠家庭提供了全面、灵活的智能监测方案，确保宠物在家庭环境中的每一次活动都能被精准记录与感知。

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史检测报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过视频/图片对目标区域进行宠物检测，识别猫、狗、鸟等常见宠物，输出结构化的宠物检测报告
- 能力包含：宠物分类定位、宠物数量统计、存在性检测
- 支持检测目标：猫、狗、鸟
- 触发条件:
    1. **默认触发**：当用户提供监控视频/图片 URL 或文件需要进行宠物检测时，默认触发本技能
    2. 当用户明确需要进行宠物检测，提及宠物检测、猫咪检测、狗狗检测等关键词，并且上传了视频或图片
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史检测报告、宠物检测报告清单、检测报告列表、查询历史报告、显示所有检测报告、宠物检测历史记录，查询宠物检测分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存到技能目录下 attachments
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有检测报告"、"
       显示所有宠物检测报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.pet_detection_analysis --list --open-id` 参数调用 API
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

**在执行宠物检测前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、petdetect123 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询宠物检测报告记录），并询问是否继续

---

- 标准流程:
    1. **准备媒体输入**
        - 提供监控视频文件路径、网络视频 URL 或现场图片
        - 确保监控画面完整覆盖监测区域，画面稳定
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行宠物检测**
        - 调用 `-m scripts.pet_detection_analysis` 处理素材（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径（使用 multipart/form-data 方式上传）
            - `--url`: 网络视频/图片 URL 地址（API 服务自动下载）
            - `--media-type`: 媒体类型，可选值：video/image，默认 video
            - `--confidence-threshold`: 置信度阈值，低于该分值不输出，默认 0.5
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示宠物检测历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的宠物检测报告
        - 包含：检测基本信息、各类宠物数量统计

## 资源索引

- 必要脚本：见 [scripts/pet_detection_analysis.py](scripts/pet_detection_analysis.py)(用途：调用 API 进行宠物检测，本地文件使用
  multipart/form-data 方式上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和媒体格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：视频支持 mp4/avi/mov 格式，图片支持 jpg/png/jpeg 格式，最大 100MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供家庭宠物监控参考，具体处置请结合实际情况
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史检测报告清单的时候，从数据 json 中提取字段 reportImageUrl 作为超链接地址，使用 Markdown 表格格式输出，包含"
  报告名称"、"检测时间"、"宠物总数"、"点击查看"四列，其中"报告名称"列使用`宠物检测分析报告-{记录id}`形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 检测时间 | 宠物总数 | 点击查看 |
  |----------|----------|----------|----------|
  | 宠物检测分析报告-20260312172200001 | 2026-03-12 17:22:00 | 2 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 检测本地监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.pet_detection_analysis --input /path/to/monitor.mp4 --media-type video --open-id openclaw-control-ui

# 检测现场图片，调整置信度阈值（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.pet_detection_analysis --input /path/to/room.jpg --media-type image --confidence-threshold 0.6 --open-id openclaw-control-ui

# 检测网络监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.pet_detection_analysis --url https://example.com/monitor.mp4 --media-type video --open-id openclaw-control-ui

# 显示历史检测报告/显示检测报告清单列表/显示历史宠物检测报告（自动触发关键词：查看历史检测报告、历史报告、检测报告清单等）
python -m scripts.pet_detection_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.pet_detection_analysis --input video.mp4 --media-type video --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.pet_detection_analysis --input video.mp4 --media-type video --open-id your-open-id --output result.json
```
