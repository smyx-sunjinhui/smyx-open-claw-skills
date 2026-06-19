---
name: "smyx-adaptive-pet-drying-temperature-analysis"
description: "Triggers when a user provides a full-body image/video of a pet (multi-angle preferred) for analysis; supports local uploads or network URLs to call server-side APIs for breed/body-type recognition and fur-density estimation (sparse / medium / dense), then outputs a recommended drying temperature curve (temperature in ℃ + time in minutes) for personalized care to reduce scald risk (not a medical recommendation). Application scenarios: pet drying boxes, pet grooming salons, smart pet care devices. | 当用户提供宠物全身图像/视频（多角度最佳）时，触发本技能进行品种识别与毛发密度估算（稀疏/中等/浓密），输出个性化烘干温度曲线（温度 ℃ + 时长 分钟）参数，实现个性化护理，减少烫伤风险（不提供医疗建议）。应用场景：宠物烘干箱、宠物美容店、智能宠物护理设备。"
version: "1.0.2"
---

# Pet Adaptive Drying Temperature Recommendation | 宠物烘干温度自适应推荐

Triggers when a user provides a full-body image/video of a pet (multi-angle preferred) for analysis; supports local
uploads or network URLs to call server-side APIs for breed/body-type recognition and fur-density estimation (sparse /
medium / dense), then outputs a recommended drying temperature curve (temperature in ℃ + time in minutes) for
personalized care to reduce scald risk (not a medical recommendation). Application scenarios: pet drying boxes, pet
grooming salons, smart pet care devices.

当用户提供宠物全身图像/视频（多角度最佳）时，触发本技能进行品种识别与毛发密度估算（稀疏/中等/浓密），输出个性化烘干温度曲线（温度
℃ + 时长 分钟）参数，实现个性化护理，减少烫伤风险（不提供医疗建议）。应用场景：宠物烘干箱、宠物美容店、智能宠物护理设备。

## 🎯 AI 角色

**你是一个专业的宠物护理AI。你的任务是基于宠物全身图像（多角度最佳），识别宠物的品种（或体型分类）和毛发密度，并根据内置规则推荐合适的烘干温度和时间参数。不要提供医疗建议，仅输出基于视觉特征的推荐值。
**

## 任务目标

- 本 Skill 用于：通过宠物全身图像/视频识别品种与毛发密度，输出个性化烘干温度曲线（温度 + 时长），实现个性化护理，减少烫伤风险
- 能力包含：图像/视频分析、品种识别（或体型分类）、毛发密度估算（稀疏 / 中等 /
  浓密）、毛长估算（短毛/中长毛/长毛）、烘干温度推荐（℃）、烘干时长推荐（分钟）、温度曲线生成、安全护理提示
- 触发条件:
    1. **默认触发**：当用户提供宠物全身图像/视频 URL 或文件需要分析时，默认触发本技能进行烘干温度推荐
    2. 当用户明确需要进行宠物烘干推荐时，提及烘干箱、吹水机、宠物烘干、烘干温度、烘干曲线、宠物美容、毛发护理、防烫伤等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史烘干推荐报告、历史烘干曲线报告、烘干推荐报告清单、查询烘干记录、显示所有宠物烘干报告、显示烘干温度推荐报告，查询宠物毛发护理报告
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有烘干推荐报告"、"
       显示所有烘干曲线报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --list --open-id` 参数调用 API
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

**在执行烘干温度自适应推荐分析前，必须按以下优先级顺序获取 open-id：**

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
        - 确保画面清晰展示宠物**完整全身**（含头部、躯干、四肢、尾巴），多角度图像识别更准
        - 光线充足、无明显遮挡、宠物毛发干燥或湿润均可（但需可见毛发纹理）
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行烘干温度自适应推荐分析**
        - 调用 `-m scripts.smyx_adaptive_pet_drying_temperature_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本
          **）
        - 参数说明:
            - `--input`: 本地图像/视频文件路径
            - `--url`: 网络图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 宠物类型，可选值：cat/dog/other，默认 dog
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示烘干温度推荐历史分析报告列表清单（可输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的烘干温度推荐报告
        - 包含：识别品种 / 体型分类（小型/中型/大型）、毛长（短毛/中长毛/长毛）、毛发密度（稀疏/中等/浓密）、推荐烘干温度（℃）、推荐烘干时长（分钟）、温度曲线（分阶段温度+时长）、防烫伤护理提示
        - **重要提示**：仅基于视觉特征输出推荐值，不提供医疗建议

## 烘干温度推荐参考曲线

| 毛发密度 / 毛长 | 短毛                  | 中长毛                 | 长毛                  |
|-----------|---------------------|---------------------|---------------------|
| 稀疏        | 38~42 ℃ / 10~15 min | 40~45 ℃ / 15~20 min | 42~48 ℃ / 20~25 min |
| 中等        | 40~45 ℃ / 15~20 min | 42~48 ℃ / 20~30 min | 45~50 ℃ / 30~40 min |
| 浓密        | 42~48 ℃ / 20~25 min | 45~50 ℃ / 30~40 min | 48~55 ℃ / 40~60 min |

> 注：上表仅为参考默认值，最终推荐值以 API 输出曲线为准。
> ⚠️ 安全上限：常规烘干温度不应超过 **60 ℃**；幼宠、老龄宠、扁鼻品种（法斗、巴哥、加菲等）应在常规推荐基础上 **下调 3~5 ℃**
> 并延长时间。
> 推荐温度曲线可直接由智能烘干箱/吹水机消费，实现分阶段降温护理。

## 资源索引

-
必要脚本：见 [scripts/smyx_adaptive_pet_drying_temperature_analysis.py](scripts/smyx_adaptive_pet_drying_temperature_analysis.py)(
用途：调用 API 进行品种识别与毛发密度估算并输出烘干温度推荐曲线，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 文件要求：支持 jpg/jpeg/png/bmp/webp 图像 与 mp4/avi/mov 视频，最大 10MB，建议多角度全身画面
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 推荐结果仅供烘干设备护理参考，不提供医疗建议
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 推荐曲线由设备端（智能烘干箱 / 吹水机）消费，实现动态风温/风速调节，防止烫伤
- 幼宠、老龄宠、扁鼻品种（法斗、巴哥、加菲等）应在常规推荐基础上下调 3~5 ℃ 并延长烘干时间，AI 角色在输出时需主动提醒
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 `reportImageUrl` 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"宠物类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`烘干温度推荐报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 宠物类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 烘干温度推荐报告-20260522021800001 | 狗 | 2026-05-22 02:18:00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地宠物全身图像/视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --input /path/to/pet_fullbody.jpg --pet-type dog --open-id your-open-id

# 分析网络宠物全身图像/视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --url https://example.com/pet_fullbody.mp4 --pet-type dog --open-id your-open-id

# 显示历史分析报告清单（自动触发关键词：查看历史烘干推荐报告、烘干曲线报告清单等）
python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --input pet_fullbody.jpg --pet-type dog --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_adaptive_pet_drying_temperature_analysis --input pet_fullbody.jpg --pet-type dog --open-id your-open-id --output result.json
```
