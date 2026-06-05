---
name: "smyx-indoor-plant-light-stress-detect-analysis"
description: "AI-powered indoor plant light stress detection from smart planter or fixed camera images (optionally combined with light sensor lux data). Detects morphological anomalies caused by low light (elongated internodes / etiolation, thin leaves, pale green color) or strong-light damage (leaf burn spots, scorched edges, curling, bleaching). Combined with optional lux sensor readings, it determines the current light stress type (insufficient / excessive / normal) and outputs adjustment suggestions (e.g. move to window, add shading, adjust grow-light duration). Scenarios: smart planters, indoor green plant care, home gardening, office plants. | 通过智能花盆或固定摄像头拍摄植物整体图像（也可选配光照传感器数据），利用AI视觉分析技术检测植物因光照不足引起的形态异常（如茎节间距拉长—徒长、叶片变薄、颜色浅绿）或因光照过强引起的损伤（叶片灼伤斑、焦边、卷曲、褪绿）。结合可选的光照传感器实时数据（勒克斯值），综合判断植物当前所受的光照胁迫类型（不足/过强/正常），并输出光照调整建议（如"增加光照，可移至窗边""遮阴，避免直射光"）。应用场景：智能花盆、室内绿植养护、家庭园艺、办公室植物。"
version: "1.0.0"
---

# Indoor Plant Light Stress Detection | 室内植物光照不足/过强识别

AI-powered indoor plant light stress detection from smart planter or fixed camera images (optionally combined with light
sensor lux data). Detects morphological anomalies caused by low light (elongated internodes / etiolation, thin leaves,
pale green color) or strong-light damage (leaf burn spots, scorched edges, curling, bleaching). Combined with optional
lux sensor readings, it determines the current light stress type (insufficient / excessive / normal) and outputs
adjustment suggestions (e.g. move to window, add shading, adjust grow-light duration). Scenarios: smart planters, indoor
green plant care, home gardening, office plants.

通过智能花盆或固定摄像头拍摄植物整体图像（也可选配光照传感器数据），利用AI视觉分析技术检测植物因光照不足引起的形态异常（如茎节间距拉长—徒长、叶片变薄、颜色浅绿）或因光照过强引起的损伤（叶片灼伤斑、焦边、卷曲、褪绿）。结合可选的光照传感器实时数据（勒克斯值），综合判断植物当前所受的光照胁迫类型（不足/过强/正常），并输出光照调整建议（如"
增加光照，可移至窗边""遮阴，避免直射光"）。应用场景：智能花盆、室内绿植养护、家庭园艺、办公室植物。

## 🎯 AI 角色

*
*假设你是一个专业的植物光环境健康AI。你的任务是分析室内植物的图像（整体形态及叶片细节），检测因光照不足导致的徒长特征（茎节间距拉长、叶片稀疏、叶色浅绿）或因光照过强导致的灼伤特征（叶片黄褐色枯斑、焦边、卷曲、叶面白化），并可结合可选的光照传感器数据（勒克斯
lux 值），综合评估光照胁迫状态（不足 / 正常 / 过强），输出调整建议。不要提供具体设备参数，仅输出基于视觉（及可选传感器）的判断。**

## 任务目标

- 本 Skill 用于：通过室内植物的整体图像或视频进行光照胁迫识别，结合可选的环境光 lux 数据，输出光照胁迫类型评估及调整建议
- 能力包含：徒长特征识别（节间拉长 / 叶片稀疏 / 颜色浅绿 / 弯向光源）、强光灼伤识别（黄褐色枯斑 / 焦边 / 卷曲 /
  白化褪绿）、整体姿态评估（垂头 / 倒伏 / 健壮）、光照胁迫状态判定（不足 / 正常 / 过强）、可选 lux
  数据融合判断、调整建议输出（移位 / 遮阴 / 补光灯时长调整）
- 触发条件:
    1. **默认触发**：当用户提供室内植物的整体图像或视频需要光照诊断时，默认触发本技能
    2. 当用户明确需要光照胁迫诊断时，提及植物徒长、节间拉长、叶子变薄、叶色变浅、叶片灼伤、焦边、晒伤、白化、阳光太强、光照不足、补光灯、智能花盆光照等关键词，并且上传了图像或视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**：查看历史光照报告、历史植物光照报告、光照胁迫报告清单、显示所有光照诊断报告、查询植物光环境记录
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有光照报告"、"
       显示植物光照诊断报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --list --open-id` 参数调用 API
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

**在执行光照胁迫分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备植物图像/视频输入**
        - 提供本地图像/视频文件路径或网络 URL
        - 确保画面清晰展示植物整体形态（茎、叶、姿态）以及叶片细节（颜色、斑点、卷曲度）
        - 如有光照传感器 lux 数据可一并提供（可选，用于辅助判断）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行光照胁迫分析**
        - 调用 `-m scripts.smyx_indoor_plant_light_stress_detect_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本
          **）
        - 参数说明:
            - `--input`: 本地图像/视频文件路径
            - `--url`: 网络图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，植物场景使用 other，默认 other
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示光照胁迫历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的光照胁迫分析报告
        - 包含：徒长特征评估（节间拉长 / 叶片稀疏 / 颜色浅绿 / 弯向光源）、强光灼伤评估（黄褐色枯斑 / 焦边 / 卷曲 /
          白化褪绿）、整体姿态描述、光照胁迫状态判定（不足 / 正常 / 过强）、可选 lux 数据融合判断、调整建议（如"
          建议移至明亮散射光窗边""遮阴避免正午直射""可适当延长补光灯每日时长"）
        - **重要提示**：仅输出基于视觉（及可选传感器）的客观评估，不提供具体补光灯瓦数、设备型号等参数化方案

## 资源索引

-
必要脚本：见 [scripts/smyx_indoor_plant_light_stress_detect_analysis.py](scripts/smyx_indoor_plant_light_stress_detect_analysis.py)(
用途：调用 API 进行光照胁迫分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和图像/视频格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 文件要求：支持 jpg/png/mp4/avi/mov 格式，最大 10MB
- 拍摄要求：建议拍摄植物整体及叶片特写两类画面，自然光或常规室内灯光下拍摄，避免过曝/过暗影响判断
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供养护参考，不提供具体设备/补光产品型号建议
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"报告名称"、"植物类别"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`室内植物光照胁迫报告-{记录id}`
  形式拼接, "点击查看"列使用`[🔗 查看报告](reportImageUrl)`格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 植物类别 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 室内植物光照胁迫报告-20260522225800001 | 室内绿植 | 2026-05-22 22:58:
  00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地室内植物图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --input /path/to/plant.jpg --open-id your-open-id

# 分析网络植物图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --url https://example.com/plant.jpg --open-id your-open-id

# 显示历史分析报告/显示分析报告清单列表/显示历史光照报告（自动触发关键词：查看历史光照报告、历史报告、光照胁迫报告清单等）
python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --input plant.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_indoor_plant_light_stress_detect_analysis --input plant.jpg --open-id your-open-id --output result.json
```
