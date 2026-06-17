---
name: "contactless-health-risk-detection-analysis"
description: "Combines frontal facial image capture with multimodal physiological feature analysis to provide early risk screening and alerts for chronic and acute conditions such as heart attack, stroke, hypertension, and hyperlipidemia. | 非接触式健康风险识别技能，通过正面人像采集结合多模态生理特征分析，提供心梗、脑梗、高血压、高血脂等慢病急症早期风险筛查预警"
version: "1.0.5"
---

# Contactless Health Risk Screening Tool | 非接触式健康风险检测分析工具

By capturing frontal facial images and analyzing multimodal physiological signals—including micro-expressions, skin
color variations, and eye movement patterns—this capability comprehensively assesses early risks of chronic and acute
conditions such as heart attacks, strokes, hypertension, and hyperlipidemia. Leveraging non-contact imaging technology,
the system extracts vital metrics like blood oxygen levels and heart rate variability (HRV), utilizing AI models to
facilitate rapid daily screening. Upon identifying high-risk characteristics, it triggers immediate alerts, making it
ideal for homes, communities, and elderly care facilities to support chronic disease management and acute condition
prevention.

本技能通过正面人像采集，结合面部微表情、肤色变化、眼动特征等多模态生理信号分析，综合评估心梗、脑梗、高血压、高血脂等慢病及急症的早期风险。系统利用非接触式成像技术，提取血氧、心率变异性等生理指标，结合AI模型实现日常快速筛查。一旦识别高风险特征，即发出预警提示，适用于家庭、社区及养老机构等场景，助力慢病管理与急症预防。

## 演示案例

- [🔗 通过网路视频进行识别分析](https://www.coze.cn/s/RrbFGxWFu5c/)
- [🔗 通过上传视频进行识别分析](https://www.coze.cn/s/ZVfuVAmFK1A/)
- [🔗 显示历史分析报告](https://www.coze.cn/s/wZpc5KC83LY/)

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过正面人像视频/图片，非接触式进行早期健康风险筛查
- 能力包含：多模态生理特征提取、慢病风险评估、急症预警提示
- 支持筛查风险：
    - **急性高危**：心梗、脑梗早期风险预警
    - **慢性慢病**：高血压、高血脂、高血糖
    - **神经退行性**：阿尔茨海默症（老年痴呆）早期风险
- **技术原理**：基于面部影像特征提取生理相关信息，进行风险概率评估
- **重要定位**：仅作早期风险筛查提示，**不替代专业医疗诊断**
- 触发条件:
    1. **默认触发**：当用户提供正面人像需要进行非接触健康风险筛查时，默认触发本技能
    2. 当用户明确需要健康风险筛查、非接触检测时，提及健康筛查、心梗脑梗预警、高血压筛查、慢病风险等关键词，并且上传了正面人像照片/视频
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史筛查报告、健康风险报告清单、筛查报告列表、查询历史筛查报告、显示所有筛查报告、健康风险分析报告，查询非接触健康风险识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者照片/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有筛查报告"、"显示所有风险报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.contactless_health_risk_detection_analysis --list --open-id`
          参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 采集要求（获得准确结果的前提）

为了获得较准确的风险评估，请确保：

1. **正面完整面部**，正对摄像头，距离 30-50 厘米
2. **光线充足均匀**，避免强光直射和大面积阴影
3. **素颜最佳**，避免浓妆、口罩、帽子、墨镜遮挡
4. **推荐采集**：10-30 秒短视频，静态图片也支持分析

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行非接触健康风险识别分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、risk123、health456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询筛查报告记录），并询问是否继续

---

- 标准流程:
    1. **准备正面人像输入**
        - 提供本地图片/短视频文件路径或网络 URL
        - 确保满足上述采集要求，获得更准确结果
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行非接触健康风险识别分析**
        - 调用 `-m scripts.contactless_health_risk_detection_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地图片/视频文件路径
            - `--url`: 网络图片/视频 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史非接触健康风险识别分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的非接触健康风险识别分析报告
        - 包含：人像采集信息、各项疾病风险等级（低/中/高）、风险提示、建议就医指导

## 资源索引

-

必要脚本：见 [scripts/contactless_health_risk_detection_analysis.py](scripts/contactless_health_risk_detection_analysis.py)(
用途：调用 API 进行非接触健康风险识别分析，本地文件上传，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png/mp4/avi/mov，视频推荐时长 10-30 秒，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **⚠️ 重要声明**：本分析结果仅供早期风险筛查参考，**不替代专业医疗诊断和检查**，发现高风险请及时到医院就诊
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"分析时间"、"高风险项数"、"风险等级"、"点击查看"五列，其中"报告名称"列使用`健康风险筛查报告-{记录id}`形式拼接, "
  点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 分析时间 | 高风险项数 | 整体风险 | 点击查看 |
  |----------|----------|------------|----------|----------|
  | 健康风险筛查报告 -20260328221000001 | 2026-03-28 22:10:00 | 1项(高血压) |
  中风险 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地照片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.contactless_health_risk_detection_analysis --input /path/to/face.jpg --open-id openclaw-control-ui

# 分析网络视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.contactless_health_risk_detection_analysis --url https://example.com/face.mp4 --open-id openclaw-control-ui

# 显示历史筛查报告/显示筛查报告清单列表/显示历史风险报告（自动触发关键词：查看历史筛查报告、历史报告、筛查报告清单等）
python -m scripts.contactless_health_risk_detection_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.contactless_health_risk_detection_analysis --input face.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.contactless_health_risk_detection_analysis --input face.jpg --open-id your-open-id --output result.json
```
