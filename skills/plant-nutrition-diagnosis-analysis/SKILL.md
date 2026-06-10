---
name: "plant-nutrition-diagnosis-analysis"
description: "Diagnoses plant nutrient deficiency or excess based on computer vision and plant physiology, outputs targeted fertilization suggestions for precision nutrient management. | 植物营养诊断技能，基于计算机视觉与植物生理学，通过叶片特征诊断氮磷钾及微量元素缺乏或过剩，输出精准施肥建议"
version: "1.0.4"
---

# Plant Nutrition Diagnosis Skill | 植物营养诊断技能

Based on computer vision technology combined with professional plant physiology knowledge, this skill accurately
diagnoses nutrient imbalance disorders such as nitrogen, phosphorus, potassium deficiency or excess, as well as trace
element abnormalities by analyzing the color, texture and morphological characteristics of plant leaves. The system
quickly locates the type of nutrient imbalance based on "symptom location" (such as the difference between old leaves
and new leaves) and "visual pattern" (such as interveinal chlorosis, leaf margin scorch), and outputs a structured
diagnostic report including nutrient deficiency judgment, physiological cause analysis, and targeted fertilization
suggestions. It provides scientific basis for precision fertilization and crop health management, helping growers
quickly correct nutrient disorders and maintain healthy plant growth.

本技能结合计算机视觉技术与专业植物生理学知识，通过分析植物叶片的颜色、纹理和形态特征，能够精准诊断氮、磷、钾大量元素缺乏或过剩以及微量元素异常等营养失衡病症。系统依据"
症状发生部位"（如老叶与新叶的差异）及"视觉表现模式"
（如叶脉间失绿、叶缘焦枯），快速定位营养失衡类型，输出包含缺乏元素判断、生理成因分析及针对性施肥建议的结构化诊断报告，为精准施肥与作物健康管理提供科学依据，帮助种植者快速矫正营养失调，维持植物健康生长。

## 演示案例

- [🔗 通过网路视频进行识别分析](https://www.coze.cn/s/2ZxZUcLbebI/)
- [🔗 通过上传视频进行识别分析](https://www.coze.cn/s/sNRmBMdQgew/)
- [🔗 显示历史分析报告](https://www.coze.cn/s/e-SBiWG0rlc/)

## 任务目标

- 本 Skill 用于：通过叶片视频/图片诊断植物营养状况，判断缺乏或过剩的营养元素，输出科学施肥建议
- 能力包含：叶片特征提取、营养元素缺乏/过剩诊断、成因分析、施肥建议输出
- 支持场景：
    - **大田作物营养诊断**：指导大田作物精准施肥，提高肥料利用率
    - **果园蔬菜施肥管理**：果树蔬菜营养失衡快速诊断
    - **设施园艺**：温室作物营养状况监测与矫正
    - **居家绿植养护**：家庭盆栽黄叶、长势差等问题营养诊断
    - **农业科研试验**：作物营养试验症状自动化记录与诊断
- 触发条件:
    1. **默认触发**：当用户提供植物叶片视频/图片需要诊断营养状况时，默认触发本技能
    2. 当用户明确需要植物营养诊断、缺肥判断时，提及叶子发黄、叶片失绿、缺氮缺磷、怎么施肥、营养诊断等关键词，并且上传了视频/图片，自动触发本技能
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史诊断报告、营养诊断报告清单、诊断报告列表、查询历史诊断报告、显示所有诊断报告、植物营养分析报告，查询植物营养诊断分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有诊断报告"、"
       显示所有植物营养诊断"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.plant_nutrition_diagnosis_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 诊断要求（获得准确结果的前提）

为了获得准确的营养诊断，请确保：

1. **典型症状叶片**：拍摄具有典型症状的叶片，避免完全正常或完全坏死无法辨认的叶片
2. **特征完整展示**：整张叶片完整出镜，包括叶片基部、尖部和叶缘
3. **自然光下拍摄**：光线均匀，避免严重色差和反光，真实反映叶片颜色

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行植物营养诊断分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备叶片视频/图片输入**
        - 提供本地视频/图片文件路径或网络 URL
        - 确保具有典型症状的叶片完整清晰
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行植物营养诊断分析**
        - 调用 `-m scripts.plant_nutrition_diagnosis_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络视频/图片 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史植物营养诊断分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的植物营养诊断报告
        - 包含：输入基本信息、诊断营养元素、失衡类型（缺乏/过剩）、症状匹配度、成因分析、矫正施肥建议

## 资源索引

- 必要脚本：见 [scripts/plant_nutrition_diagnosis_analysis.py](scripts/plant_nutrition_diagnosis_analysis.py)
  (用途：调用 API 进行植物营养诊断分析，本地文件上传，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png，最大 20MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 诊断结果仅供施肥参考，具体施肥方案请结合土壤检测结果和当地农业技术推广部门建议
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"诊断植株数"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`植物营养诊断报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 诊断植株数 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 植物营养诊断报告-20260414233200001 | 1株 | 2026-04-14 23:32:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 📝 隐私与数据安全声明

本技能在处理用户上传的视频时，严格遵守数据安全规范：

- **数据保密处理**：
    - 系统基于 用户名/手机号 生成的标识仅作为用户关联信息，**不保存任何可直接识别个人身份的明文信息**。
- **安全传输**：
    - 所有数据（包括视频文件及关联标识）均通过 **HTTPS/TLS 加密通道** 发送至云端 API 进行分析，防止数据在传输过程中被窃取或篡改。
- **数据留存策略**：
    - 云端服务器遵循“最小必要原则”，**分析任务完成后即刻删除原始视频数据，不进行持久化存储**，确保用户隐私数据不被留存或滥用。

## 使用示例

```bash
# 诊断本地视频/图片中植物的营养状况（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_nutrition_diagnosis_analysis --input /path/to/leaf.mp4 --open-id your-open-id

# 诊断网络视频/图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.plant_nutrition_diagnosis_analysis --url https://example.com/yellow-leaf.mp4 --open-id your-open-id

# 显示历史诊断报告/显示诊断报告清单列表/显示历史植物营养诊断（自动触发关键词：查看历史诊断报告、历史报告、诊断报告清单等）
python -m scripts.plant_nutrition_diagnosis_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.plant_nutrition_diagnosis_analysis --input leaf.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.plant_nutrition_diagnosis_analysis --input leaf.jpg --open-id your-open-id --output result.json
```
