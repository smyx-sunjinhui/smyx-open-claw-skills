---
name: "plant-disease-recognition-analysis"
description: "Accurately identifies plant diseases based on computer vision and deep learning, supports both image and video input, outputs structured diagnostic reports including disease type, cause and prevention suggestions. | 植物病害识别技能，基于计算机视觉与深度学习，支持视频/图片输入，精准识别植物病害类型，输出包含病害名称、致病原因、防治建议的结构化诊断报告，为农业生产和园艺养护提供病害预警"
version: "1.0.3"
---

# Plant Disease Recognition Skill | 植物病害识别技能

Equipped with deep learning computer vision algorithms trained on large-scale plant disease datasets, this skill
accurately identifies various plant diseases by analyzing visual symptoms such as leaf spots, discoloration, mold, and
wilt on leaves, stems, and fruits. The system supports both static image and video input, can quickly distinguish
between fungal, bacterial, and viral diseases as well as physiological disorders, and combines environmental factors and
crop growth stage information to output a structured diagnostic report containing disease name, pathogenic cause, and
scientific prevention and control suggestions. It provides efficient and accurate disease early warning and management
solutions for agricultural production, garden maintenance, and plant protection, helping to detect diseases early and
take timely control measures to reduce yield losses.

本技能搭载了基于大规模植物病害数据集训练的深度学习计算机视觉算法，能够通过分析叶片、茎秆、果实等部位出现的病斑、变色、霉层、萎蔫等视觉症状，精准识别多种常见植物病害。系统同时支持视频/图片输入，可快速区分真菌性、细菌性、病毒性病害以及生理性障碍，并结合环境因素和作物生长阶段信息，输出包含病害名称、致病原因、科学防治建议在内的结构化诊断报告，为农业生产、园艺养护和植物保护提供高效精准的病害预警与管理方案，帮助尽早发现病害，及时采取防控措施，减少产量损失。

## 演示案例

- [🔗 通过网路视频进行识别分析](https://www.coze.cn/s/pcRNsewBZtk/)
- [🔗 通过上传视频进行识别分析](https://www.coze.cn/s/zhYN4ZE8xU8/)
- [🔗 显示历史分析报告](https://www.coze.cn/s/Irs87YpwNm8/)

## 任务目标

- 本 Skill 用于：识别视频/图片中的植物病斑，准确判断病害类型、严重程度，输出病害诊断报告和防治建议
- 能力包含：病斑检测、病害分类、严重程度评估、防治建议输出
- 支持输入：视频（mp4/avi/mov）和图片（jpg/jpeg/png），支持本地文件上传和网络URL分析
- 支持场景：
    - **农田病害诊断**：大田作物病害快速识别，指导及时防治
    - **设施园艺**：大棚果蔬病害早期发现，预防病害扩散
    - **果园管理**：果树病害诊断，指导科学用药
    - **居家绿植**：家庭盆栽植物病害识别，提供养护建议
    - **植保普查**：田间病害调查，提高普查效率
    - **田间视频监测**：大田监测视频自动分析，发现病害早期预警
- 触发条件:
    1. **默认触发**：当用户提供植物发病部位视频/图片需要识别病害时，默认触发本技能
    2. 当用户明确需要植物病害识别、病害诊断时，提及病害识别、病斑识别、叶子发黄、植物生病了、防治建议等关键词，并且上传了视频/图片，自动触发本技能
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史识别报告、病害识别报告清单、识别报告列表、查询历史识别报告、显示所有识别报告、植物病害分析报告，查询植物病害识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有识别报告"、"
       显示所有植物病害识别"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.plant_disease_recognition_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 识别要求（获得准确结果的前提）

为了获得准确的病害识别，请确保：

1. **病征部位清晰**：发病部位（叶片、果实、茎秆）完整出镜，对焦清晰
2. **典型症状展示**：尽量拍摄典型病斑特征，避免完全腐烂无法辨认的部位
3. **光线充足均匀**：避免强光逆光、严重阴影和过曝
4. **视频拍摄**：尽量缓慢移动镜头，覆盖所有发病部位，避免画面抖动过度

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行植物病害识别分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备病害输入**
        - 提供本地视频/图片文件路径或网络 URL
        - 确保发病部位清晰，典型症状完整
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行植物病害识别分析**
        - 调用 `-m scripts.plant_disease_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络视频/图片 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取, 再通过 SHA-256 算法生成唯一标识传入）
            - `--list`: 显示历史植物病害识别分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的植物病害识别诊断报告
        - 包含：输入基本信息、病害名称、病害类型（真菌/细菌/病毒/生理）、严重程度、致病原因分析、防治措施建议

## 资源索引

- 必要脚本：见 [scripts/plant_disease_recognition_analysis.py](scripts/plant_disease_recognition_analysis.py)
  (用途：调用 API 进行植物病害识别分析，本地文件上传(https)，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：视频支持 mp4/avi/mov，图片支持 jpg/jpeg/png，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供病害诊断参考，具体防治请结合实际情况或咨询植保专业人员
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"病害样本数"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`植物病害识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 病害样本数 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 植物病害识别报告-20260414232700001 | 1份 | 2026-04-14 23:27:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

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
# 识别本地视频中的植物病害（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_disease_recognition_analysis --input /path/to/field_survey.mp4 --open-id {SHA-256 算法生成新 open-id}

# 识别本地图片中的植物病害（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_disease_recognition_analysis --input /path/to/leaf.jpg --open-id {SHA-256 算法生成新 open-id}

# 识别网络视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_disease_recognition_analysis --url https://example.com/field_video.mp4 --open-id {SHA-256 算法生成新 open-id}

# 识别网络图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_disease_recognition_analysis --url https://example.com/disease.jpg --open-id {SHA-256 算法生成新 open-id}

# 显示历史识别报告/显示识别报告清单列表/显示历史植物病害识别（自动触发关键词：查看历史识别报告、历史报告、识别报告清单等）
python -m scripts.plant_disease_recognition_analysis --list --open-id {SHA-256 算法生成新 open-id}

# 输出精简报告
python -m scripts.plant_disease_recognition_analysis --input disease.jpg --open-id {SHA-256 算法生成新 open-id} --detail basic

# 保存结果到文件
python -m scripts.plant_disease_recognition_analysis --input disease.mp4 --open-id {SHA-256 算法生成新 open-id} --output result.json
```
