---
name: "plant-species-recognition-analysis"
description: "Accurately identifies plant species from images based on deep learning and computer vision, outputs structured information including species name, family, growth habits and maintenance tips. | 植物物种识别技能，基于深度学习与计算机视觉技术，通过图像快速识别植物物种，输出物种名称、科属分类、生长习性及养护要点等结构化信息，为园艺、生态调研、自然教育提供专业识别服务"
version: "1.0.3"
---

# Plant Species Recognition Skill | 植物物种识别技能

Equipped with advanced deep learning computer vision algorithms trained on massive plant datasets, this skill delivers
fast and accurate identification of tens of thousands of plant species worldwide. The system not only provides basic
species classification information but also outputs detailed structured knowledge including scientific name, family and
genus classification, native distribution, growth habits, and professional cultivation and maintenance tips. It supports
differentiation and labeling of similar-looking species, helping users accurately distinguish between easily confused
plants. Whether you're a gardening enthusiast exploring plant knowledge, a researcher conducting ecological field
surveys, or a teacher carrying out natural education activities, this skill provides efficient and reliable plant
recognition and knowledge services.

本技能搭载了基于海量植物数据集训练的先进深度学习计算机视觉算法，能够对全球数万种常见植物进行快速精准识别。系统不仅提供基础物种分类信息，还输出包含学名、科属分类、原生分布、生长习性、专业栽培养护要点在内的详细结构化知识，同时支持对外观相似物种进行区分标注，帮助用户准确区分易混淆植物。无论是园艺爱好者探索植物知识、科研人员开展生态野外调查，还是教师进行自然教育活动，本技能都能提供高效准确的植物识别与知识服务。

## 演示案例

- [🔗 通过网路视频进行识别分析](https://www.coze.cn/s/8jiZueXudVM/)
- [🔗 通过上传视频进行识别分析](https://www.coze.cn/s/b4ZBcLDhsls/)
- [🔗 显示历史分析报告](https://www.coze.cn/s/aYnCkcHdQx4/)

## 任务目标

- 本 Skill 用于：识别视频/图片中的植物，准确判断植物物种名称，提供完整的植物知识信息
- 能力包含：植物检测、物种分类、相似物种区分、结构化知识输出
- 支持场景：
    - **园艺指导**：居家绿植、户外花卉识别，获取专业养护建议
    - **生态调研**：野外植物调查，快速识别记录物种信息
    - **自然教育**：亲子科普、户外研学，帮助认识身边植物
    - **旅行探索**：出游遇到未知植物，一键识别了解特性
- 触发条件:
    1. **默认触发**：当用户提供植物视频/图片需要识别物种时，默认触发本技能
    2. 当用户明确需要植物识别、物种鉴定时，提及植物识别、植物种类、花草识别、树木识别、物种分类等关键词，并且上传了视频/图片，自动触发本技能
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史识别报告、植物识别报告清单、识别报告列表、查询历史识别报告、显示所有识别报告、植物物种分析报告，查询植物物种识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有识别报告"、"显示所有植物识别"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.plant_species_recognition_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 识别要求（获得准确结果的前提）

为了获得准确的物种识别，请确保：

1. **植物主体完整**，叶片、花朵等识别特征完整出镜，避免过度遮挡
2. **光线充足清晰**，避免过度模糊、过暗过曝和严重色差
3. 多株植物同框时尽量保持间距适中，便于分别识别

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行植物物种识别分析前，必须按以下优先级顺序获取 open-id：**

```
第 1 步：检查用户是否在消息中明确提供了 open-id
        ↓ (未提供)
第 2 步：❗ 必须暂停执行，明确提示用户提供用户名或手机号作为 open-id
```

**⚠️ 关键约束：**

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、plant123、id456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析

---

- 标准流程:
    1. **准备植物视频/图片输入**
        - 提供本地视频/图片文件路径或网络 URL
        - 确保植物主体完整，特征清晰，光线充足
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行植物物种识别分析**
        - 调用 `-m scripts.plant_species_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络视频/图片 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取, 再通过 SHA-256 算法生成唯一标识传入）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的植物物种识别分析报告
        - 包含：输入基本信息、检测到的植物数量、物种名称、科属分类、生长习性、养护要点、识别置信度

## 资源索引

- 必要脚本：见 [scripts/plant_species_recognition_analysis.py](scripts/plant_species_recognition_analysis.py)
  (用途：调用 API 进行植物物种识别分析，本地文件上传(https)，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png，最大 20MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供知识参考，药用食用等用途请务必咨询专业人士确认安全性
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"植物数量"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`植物物种识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 植物数量 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 植物物种识别报告-20260414225700001 | 1株 | 2026-04-14 22:57:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 📝 隐私与数据安全声明

本技能在处理用户上传的视频时，严格遵守数据安全规范：

- **数据脱敏处理**：
    - 系统基于用户名/手机号生成的 SHA-256 标识仅作为匿名化脱敏处理后的用户关联信息，**不包含任何可直接识别个人身份的明文信息
      **。
- **安全传输**：
    - 所有数据（包括视频文件及关联标识）均通过 **HTTPS/TLS 加密通道** 发送至云端 API 进行分析，防止数据在传输过程中被窃取或篡改。
- **数据留存策略**：
    - 云端服务器遵循“最小必要原则”，**分析任务完成后即刻删除原始视频数据，不进行持久化存储**，确保用户隐私数据不被留存或滥用。

## 使用示例

```bash
# 识别本地视频/图片中的植物（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_species_recognition_analysis --input /path/to/plant.mp4 --open-id {SHA-256 算法生成新 open-id}

# 识别网络视频/图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_species_recognition_analysis --url https://example.com/flower.mp4 --open-id {SHA-256 算法生成新 open-id}

# 显示历史识别报告/显示识别报告清单列表/显示历史植物识别（自动触发关键词：查看历史识别报告、历史报告、识别报告清单等）
python -m scripts.plant_species_recognition_analysis --list --open-id {SHA-256 算法生成新 open-id}

# 输出精简报告
python -m scripts.plant_species_recognition_analysis --input plant.jpg --open-id {SHA-256 算法生成新 open-id} --detail basic

# 保存结果到文件
python -m scripts.plant_species_recognition_analysis --input plant.jpg --open-id {SHA-256 算法生成新 open-id} --output result.json
```
