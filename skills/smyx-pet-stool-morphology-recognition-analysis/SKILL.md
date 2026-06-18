---
name: "smyx-pet-stool-morphology-recognition-analysis"
description: "Triggers when a user provides an image/video URL or file of dog toilet area or outdoor dog-walking path for analysis; supports local uploads or network URLs to call server-side APIs for pet stool morphology recognition, analyzing stool color (brown, black, red, white), shape (formed, loose/soft, watery, granular hard), and the presence of blood or mucus, outputting standardized abnormal observation features to help early discovery of gastrointestinal diseases (without diagnosing diseases). Application scenarios: dog toilets, outdoor dog-walking path cameras, pet health monitoring, multi-pet households. | 当用户提供狗厕所或户外遛狗路径区域的粪便图像/视频时，触发本技能进行排便形态识别分析；支持通过上传本地文件或网络URL，调用服务端API识别粪便颜色（棕、黑、红、白）、形状（条状、稀糊、颗粒）、是否带血或粘液，输出异常特征观察结果，帮助早期发现肠胃疾病（不诊断疾病）。应用场景：狗厕所、遛狗路径摄像头、宠物健康监测、多宠家庭。"
version: "1.0.3"
---

# Pet Stool Morphology Recognition Analysis | 宠物排便形态识别（狗厕所/户外）

Triggers when a user provides an image/video URL or file of dog toilet area or outdoor dog-walking path for analysis;
supports local uploads or network URLs to call server-side APIs for pet stool morphology recognition, analyzing stool
color (brown, black, red, white), shape (formed, loose/soft, watery, granular hard), and the presence of blood or mucus,
outputting standardized abnormal observation features to help early discovery of gastrointestinal diseases (without
diagnosing diseases). Application scenarios: dog toilets, outdoor dog-walking path cameras, pet health monitoring,
multi-pet households.

当用户提供狗厕所或户外遛狗路径区域的粪便图像/视频时，触发本技能进行排便形态识别分析；支持通过上传本地文件或网络URL，调用服务端API识别粪便颜色（棕、黑、红、白）、形状（条状、稀糊、颗粒）、是否带血或粘液，输出异常特征观察结果，帮助早期发现肠胃疾病（不诊断疾病）。应用场景：狗厕所、遛狗路径摄像头、宠物健康监测、多宠家庭。

## 🎯 AI 角色

**你是一个专业的宠物健康监测AI。你的任务是基于宠物排便区域的图像或视频帧，分析粪便的形态特征（颜色、形状、有无带血或粘液），输出标准化观察结果。不要提供疾病诊断或治疗方案，仅客观描述粪便外观。
**

## 任务目标

- 本 Skill 用于：通过狗厕所或户外遛狗路径区域的图像/视频进行宠物排便形态识别分析，获取标准化的观察结果和异常特征提示，帮助早期发现肠胃疾病
- 能力包含：图像/视频分析、粪便颜色识别（棕、黑、红、白）、粪便形状识别（条状、稀糊、颗粒）、带血/粘液检测、异常特征输出、肠胃健康风险提示
- 触发条件:
    1. **默认触发**：当用户提供狗厕所或户外遛狗路径区域的图像/视频 URL 或文件需要分析时，默认触发本技能进行排便形态识别
    2. 当用户明确需要进行宠物排便监测时，提及狗厕所、遛狗、户外排便、粪便分析、粪便颜色、粪便形状、便血、粘液便、稀便、肠胃异常等关键词，并且上传了图片/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史排便报告、历史排便形态报告、排便分析报告清单、查询排便记录、显示所有狗厕所报告、显示排便形态识别报告，查询肠胃健康风险提示报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有排便报告"、"
       显示所有排便形态报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_pet_stool_morphology_recognition_analysis --list --open-id` 参数调用 API
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

**在执行排便形态识别分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备图像/视频输入**
        - 提供本地图像/视频文件路径或网络 URL
        - 确保画面清晰展示粪便目标区域（狗厕所托盘或户外路径），光线充足、无明显遮挡
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行排便形态识别分析**
        - 调用 `-m scripts.smyx_pet_stool_morphology_recognition_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本
          **）
        - 参数说明:
            - `--input`: 本地图像/视频文件路径
            - `--url`: 网络图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 宠物类型，可选值：cat/dog/other，默认 dog
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示宠物排便形态历史分析报告列表清单（可输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的排便形态观察报告
        -
       包含：粪便颜色分析（棕色/正常、黑色/疑似上消化道出血、红色/便血、白色/灰白色/疑似胆道异常）、粪便形状分析（正常条状、稀软不成形、水样状、颗粒状干硬便）、带血检测（表面血迹、血丝、黑便）、粘液检测（透明粘液、果冻状粘液）、异常特征汇总、肠胃健康风险提示
        - **重要提示**：仅客观描述观察到的现象，不提供疾病诊断或治疗建议

## 资源索引

-

必要脚本：见 [scripts/smyx_pet_stool_morphology_recognition_analysis.py](scripts/smyx_pet_stool_morphology_recognition_analysis.py)(
用途：调用 API 进行排便形态识别分析，本地文件上传，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 文件要求：支持 jpg/jpeg/png/bmp/webp 图像 与 mp4/avi/mov 视频，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供健康参考，不提供疾病诊断或治疗建议
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 `reportImageUrl` 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"宠物类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`排便形态识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 宠物类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 排便形态识别报告-20260521235900001 | 狗 | 2026-05-21 23:59:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地狗厕所/户外排便图像或视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pet_stool_morphology_recognition_analysis --input /path/to/dog_stool.jpg --pet-type dog --open-id your-open-id

# 分析网络狗厕所/户外排便视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pet_stool_morphology_recognition_analysis --url https://example.com/dog_stool.mp4 --pet-type dog --open-id your-open-id

# 显示历史分析报告清单（自动触发关键词：查看历史排便报告、排便形态报告清单等）
python -m scripts.smyx_pet_stool_morphology_recognition_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_pet_stool_morphology_recognition_analysis --input dog_stool.jpg --pet-type dog --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_pet_stool_morphology_recognition_analysis --input dog_stool.jpg --pet-type dog --open-id your-open-id --output result.json
```
