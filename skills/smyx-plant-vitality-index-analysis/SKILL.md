---
name: "smyx-plant-vitality-index-analysis"
description: "Using a plant-monitoring platform that periodically (e.g., daily) collects plant images, environmental data, and growth metrics (new bud count, leaf-area change, leaf color), an AI evaluation model fuses leaf color (chlorophyll index), morphology (spread, leaf size), and growth dynamics (new buds, leaf-area growth rate) to output an overall vitality score from 0-100 along with a trend (rising / stable / declining). It helps users intuitively grasp plant health and guides care decisions. Application scenarios: smart planters, plant factories, home gardening, plant-monitoring platforms. The system generates a daily vitality report and pushes alerts when scores keep dropping (e.g., 'vitality index dropped 15% in the past week, please check light or roots'). Skill features: an intuitive way to understand plant health. | 通过植物监测平台定期（如每天）采集的植物图像、环境数据以及生长指标（如新芽数、叶片面积变化、叶色），利用AI综合评估模型融合叶片颜色（叶绿素指数）、形态（舒展度、叶片大小）、生长动态（新芽萌发数、叶面积增长率），输出0-100的整体活力评分，并给出活力趋势（上升/稳定/下降）。该技能帮助用户直观了解植物健康状况，指导养护决策。应用场景：智能花盆、植物工厂、家庭园艺、植物监测平台。系统每日生成活力指数报告，当评分持续下降时推送提醒（如'活力指数近一周下降15%，请检查光照或根系'）。技能特点：直观了解植物健康。"
version: "1.0.0"
---

# Plant Vitality Index | 植物整体活力指数（综合评分）

Using a plant-monitoring platform that periodically (e.g., daily) collects plant images, environmental data, and growth metrics (new bud count, leaf-area change, leaf color), an AI evaluation model fuses leaf color (chlorophyll index), morphology (spread, leaf size), and growth dynamics (new buds, leaf-area growth rate) to output an overall vitality score from 0-100 along with a trend (rising / stable / declining). It helps users intuitively grasp plant health and guides care decisions. Application scenarios: smart planters, plant factories, home gardening, plant-monitoring platforms. The system generates a daily vitality report and pushes alerts when scores keep dropping (e.g., 'vitality index dropped 15% in the past week, please check light or roots'). Skill features: an intuitive way to understand plant health.

通过植物监测平台定期（如每天）采集的植物图像、环境数据以及生长指标（如新芽数、叶片面积变化、叶色），利用AI综合评估模型融合叶片颜色（叶绿素指数）、形态（舒展度、叶片大小）、生长动态（新芽萌发数、叶面积增长率），输出0-100的整体活力评分，并给出活力趋势（上升/稳定/下降）。该技能帮助用户直观了解植物健康状况，指导养护决策。应用场景：智能花盆、植物工厂、家庭园艺、植物监测平台。系统每日生成活力指数报告，当评分持续下降时推送提醒（如'活力指数近一周下降15%，请检查光照或根系'）。技能特点：直观了解植物健康。

## 🎯 AI 角色

**假设你是一个专业的植物健康评估 AI。你的任务是综合植物的多项视觉和环境指标（叶片颜色、新芽数量、叶片面积变化、生长速度等），计算整体活力指数（0-100 分），并分析近期变化趋势。不要提供具体养护操作（如施肥配方、修剪方案），仅输出评分及趋势。**

## 任务目标

- 本 Skill 用于：基于植物图像（单图或连续日间序列）+ 可选环境数据/生长指标，输出 0-100 整体活力评分与变化趋势
- 能力包含：叶片颜色（叶绿素指数）评估、叶片形态（舒展度、叶片大小）评估、新芽萌发数计数、叶面积增长率估算（基于序列）、整体株型紧凑度、活力总分（0-100）、活力等级（excellent / good / fair / poor）、近期趋势（rising / stable / declining）、变化百分比、活力下降阈值告警
- 触发条件:
    1. **默认触发**：当用户提供植物图像/连续日间图像序列/视频 URL 或文件需要分析时，默认触发本技能进行活力指数综合评分
    2. 当用户明确提及活力指数、植物活力、综合评分、健康打分、生长状态打分、植物体检、活力趋势、叶绿素指数等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看植物活力历史报告、活力指数报告清单、活力评分报告清单、查询历史活力指数、显示所有植物活力报告、显示植物体检诊断报告，查询活力趋势清单
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有植物活力报告"、"
       显示所有活力指数报告"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_plant_vitality_index_analysis --list --open-id` 参数调用 API
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

**在执行植物活力指数评估前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备植物图像输入**
        - 提供本地植物图像/连续日间图像序列/视频路径或网络 URL
        - 建议每日同一角度、同一时段拍摄，覆盖近 3-7 天以便给出趋势；单图也可输出当次评分
        - 可选附带：植物名称/品种、光照 lux、气温 ℃、湿度 %、土壤湿度 %、新芽数、叶面积、上次浇水/施肥时间
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行植物活力指数综合评分**
        - 调用 `-m scripts.smyx_plant_vitality_index_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地植物图像/连续日间图像序列/视频文件路径
            - `--url`: 网络植物图像/连续日间图像序列/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，植物活力评估场景默认 `other`
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示植物活力指数历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的植物活力评分报告
        - 包含：活力总分（vitality_score，0-100）、活力等级（excellent / good / fair / poor）、近期趋势（rising / stable / declining）、近 7 天变化百分比（trend_change_pct）、子维度分数（叶绿素 / 形态 / 新芽 / 增长率）、活力下降阈值告警提示（如"活力指数近一周下降 15%，请检查光照或根系"）
        - **重要提示**：仅输出综合评分与趋势，不提供具体养护操作建议（如施肥配方、修剪方案）

## 资源索引

- 必要脚本：见 [scripts/smyx_plant_vitality_index_analysis.py](scripts/smyx_plant_vitality_index_analysis.py)(
  用途：调用 API 进行植物整体活力指数综合评分，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范、评分维度和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 输入要求：支持 jpg/png 图像或 mp4/avi/mov 视频，最大 10MB；建议连续日间序列以获得稳定趋势
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 评分结果仅供养护参考，单次评分受拍摄角度/光照影响较大，建议结合趋势查看
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"植物种类"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`植物活力指数报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 植物种类 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 植物活力指数报告-20260312172200001 | 龟背竹 | 2026-03-12 17:22:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地植物图像/序列（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_plant_vitality_index_analysis --input /path/to/plant.jpg --open-id your-open-id

# 分析网络植物图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_plant_vitality_index_analysis --url https://example.com/plant.jpg --open-id your-open-id

# 显示历史活力指数报告/活力指数报告清单（自动触发关键词：查看植物活力历史报告、活力指数报告清单等）
python -m scripts.smyx_plant_vitality_index_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_plant_vitality_index_analysis --input plant.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_plant_vitality_index_analysis --input plant.jpg --open-id your-open-id --output result.json
```
