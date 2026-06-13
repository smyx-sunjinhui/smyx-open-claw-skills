---
name: "smyx-transpiration-rate-estimation-analysis"
description: "AI-powered transpiration rate estimation for indoor plants. From smart planters or fixed cameras, uses thermal infrared images of leaves (preferred) — or regular RGB images combined with ambient temperature/humidity — to estimate the leaf-to-air temperature difference, combines radiation/humidity parameters (sensor or model-inferred), and computes a relative transpiration rate index (0-100%). Transpiration rate correlates with root water-uptake activity, indirectly reflecting root health and water transport capacity. Helps determine whether the plant is water-stressed, has damaged roots, or is under environmental stress. Scenarios: smart planters, indoor green plant care, plant factories, research greenhouses. | 通过智能花盆或固定摄像头采集植物叶片的红外热成像图像（或普通RGB图像结合环境温湿度数据），利用AI模型估算叶片温度与空气温度的差值，结合辐射、湿度等参数（可由传感器提供或模型内估），计算植物蒸腾速率的相对值（0-100%）。蒸腾速率与根系吸水活力正相关，可间接反映根系健康及水分输送能力。该技能有助于判断植物是否缺水、根系受损或环境胁迫。应用场景：智能花盆、室内绿植养护、植物工厂、科研温室。"
version: "1.0.1"
---

# Transpiration Rate Estimation | 室内绿植蒸腾速率估算

AI-powered transpiration rate estimation for indoor plants. From smart planters or fixed cameras, uses thermal infrared images of leaves (preferred) — or regular RGB images combined with ambient temperature/humidity — to estimate the leaf-to-air temperature difference, combines radiation/humidity parameters (sensor or model-inferred), and computes a relative transpiration rate index (0-100%). Transpiration rate correlates with root water-uptake activity, indirectly reflecting root health and water transport capacity. Helps determine whether the plant is water-stressed, has damaged roots, or is under environmental stress. Scenarios: smart planters, indoor green plant care, plant factories, research greenhouses.

通过智能花盆或固定摄像头采集植物叶片的红外热成像图像（或普通RGB图像结合环境温湿度数据），利用AI模型估算叶片温度与空气温度的差值，结合辐射、湿度等参数（可由传感器提供或模型内估），计算植物蒸腾速率的相对值（0-100%）。蒸腾速率与根系吸水活力正相关，可间接反映根系健康及水分输送能力。该技能有助于判断植物是否缺水、根系受损或环境胁迫。应用场景：智能花盆、室内绿植养护、植物工厂、科研温室。

## 🎯 AI 角色

**假设你是一个专业的植物生理 AI。你的任务是分析植物叶片的图像（热成像优先，或普通 RGB 结合环境温湿度），估算叶片-空气温差，并基于能量平衡原理估算蒸腾速率的相对值（0-100%），进而推断根系吸水活力。不要提供土壤水分具体数值，仅输出蒸腾速率指数和活力评估。**

## 任务目标

- 本 Skill 用于：通过室内植物叶片的红外热成像（优先）或 RGB 图像 + 可选环境温湿度数据，估算蒸腾速率相对值（0-100%），并推断根系吸水活力
- 能力包含：叶片温度估算（热成像直接读取 / RGB 模型推断）、叶片-空气温差计算、能量平衡蒸腾速率建模、蒸腾速率指数（0-100%）、根系吸水活力等级（强 / 正常 / 偏弱 / 受阻）、可能的胁迫类型提示（缺水 / 根系受损 / 高温高湿降低蒸腾 / 通风不足）、养护方向建议
- 触发条件:
    1. **默认触发**：当用户提供室内植物叶片的热成像图或普通 RGB 图像（可选附带环境温湿度数据）需要蒸腾分析时，默认触发本技能
    2. 当用户明确需要蒸腾 / 水分状态评估时，提及蒸腾速率、叶温、热成像、根系吸水、植物缺水预警、水分胁迫、根系活力、智能花盆水分等关键词，并且上传了图像或视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**：查看历史蒸腾报告、历史蒸腾速率报告、蒸腾趋势清单、显示所有蒸腾分析报告、查询植物水分诊断记录
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有蒸腾报告"、"显示蒸腾速率报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_transpiration_rate_estimation_analysis --list --open-id` 参数调用 API 查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行蒸腾速率估算前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备植物叶片图像/视频输入**
        - 提供本地图像/视频文件路径或网络 URL
        - 优先使用红外热成像图（更准确）；若仅有 RGB 图像，建议同时提供环境温湿度数据
        - 同一植株建议固定机位、固定时间段采集（如每天上午光照较稳定时段），便于跨期趋势对比
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行蒸腾速率估算**
        - 调用 `-m scripts.smyx_transpiration_rate_estimation_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地图像/视频文件路径（热成像或 RGB）
            - `--url`: 网络图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 类别标识，植物场景使用 other，默认 other
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示蒸腾速率历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的蒸腾速率估算报告
        - 包含：估算叶片温度、环境温度、叶-气温差、蒸腾速率指数（0-100%）、根系吸水活力等级（强 / 正常 / 偏弱 / 受阻）、可能的胁迫类型提示、养护方向建议（如"蒸腾速率偏低 28%，根系吸水可能受阻，建议检查土壤湿度、是否积水烂根，并改善通风"）
        - **重要提示**：仅输出基于视觉（含可选环境数据）的相对值与定性评估，不提供土壤含水量等具体数值

## 资源索引

- 必要脚本：见 [scripts/smyx_transpiration_rate_estimation_analysis.py](scripts/smyx_transpiration_rate_estimation_analysis.py)(用途：调用 API 进行蒸腾速率估算，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和图像/视频格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 文件要求：支持 jpg/png/mp4/avi/mov 格式，最大 10MB；热成像建议使用伪彩或可解码的辐射图
- 拍摄要求：固定机位、稳定光照时段采集；尽量避免热源干扰（暖气、灯具直射、玻璃反射）
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 分析结果仅供养护参考，不提供土壤水分具体数值；持续异常建议结合土壤水分计或根系检查
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown 表格格式输出，包含"报告名称"、"植物类别"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`蒸腾速率估算报告-{记录id}`形式拼接, "点击查看"列使用`[🔗 查看报告](reportImageUrl)`格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 植物类别 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 蒸腾速率估算报告-20260523000000001 | 室内绿植 | 2026-05-23 00:00:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地热成像/RGB 叶片图像（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_transpiration_rate_estimation_analysis --input /path/to/leaf_thermal.jpg --open-id your-open-id

# 分析网络图像/视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.smyx_transpiration_rate_estimation_analysis --url https://example.com/leaf.jpg --open-id your-open-id

# 显示历史分析报告/显示分析报告清单列表/显示历史蒸腾报告（自动触发关键词：查看历史蒸腾报告、历史报告、蒸腾速率清单等）
python -m scripts.smyx_transpiration_rate_estimation_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_transpiration_rate_estimation_analysis --input leaf.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_transpiration_rate_estimation_analysis --input leaf.jpg --open-id your-open-id --output result.json
```
