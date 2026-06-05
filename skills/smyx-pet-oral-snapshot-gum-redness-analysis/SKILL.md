---
name: "smyx-pet-oral-snapshot-gum-redness-analysis"
description: "Triggers when a user provides an oral snapshot image/video of a pet (usually auto-captured during yawning, lip-licking or mouth-opening moments) for analysis; supports local uploads or network URLs to call server-side APIs for oral health recognition, evaluating gum color (pink / bright red / dark red) and tartar coverage area, outputting standardized oral health observations to help early discovery of periodontal disease (without diagnosing diseases). Application scenarios: pet cameras, smart pet products, pet health management platforms. | 当用户提供宠物口腔抓拍图像/视频（通常在宠物打哈欠、舔嘴、张嘴时自动触发抓拍）时，触发本技能进行口腔健康识别；支持通过上传本地文件或网络URL，调用服务端API分析牙龈颜色（粉红、鲜红、暗红）与牙结石覆盖面积，输出标准化口腔健康观察结果，帮助早期发现牙周病等问题（不诊断疾病）。应用场景：宠物摄像头、智能宠物用品、宠物健康管理平台。"
version: "1.0.0"
---

# Pet Oral Snapshot & Gum Redness Recognition | 宠物口腔抓拍与牙龈红肿识别

Triggers when a user provides an oral snapshot image/video of a pet (usually auto-captured during yawning, lip-licking
or mouth-opening moments) for analysis; supports local uploads or network URLs to call server-side APIs for oral health
recognition, evaluating gum color (pink / bright red / dark red) and tartar coverage area, outputting standardized oral
health observations to help early discovery of periodontal disease (without diagnosing diseases). Application scenarios:
pet cameras, smart pet products, pet health management platforms.

当用户提供宠物口腔抓拍图像/视频（通常在宠物打哈欠、舔嘴、张嘴时自动触发抓拍）时，触发本技能进行口腔健康识别；支持通过上传本地文件或网络URL，调用服务端API分析牙龈颜色（粉红、鲜红、暗红）与牙结石覆盖面积，输出标准化口腔健康观察结果，帮助早期发现牙周病等问题（不诊断疾病）。应用场景：宠物摄像头、智能宠物用品、宠物健康管理平台。

## 🎯 AI 角色

**你是一个专业的宠物口腔健康分析AI。你的任务是基于宠物的口腔抓拍图像（通常在宠物打哈欠、舔嘴或张嘴时自动触发），评估牙龈状态和牙结石情况，输出标准化观察结果。不要提供疾病诊断或治疗方案，仅客观描述口腔内可见的健康指标。
**

## 任务目标

- 本 Skill 用于：通过宠物口腔抓拍图像/视频进行牙龈红肿与牙结石识别分析，获取标准化口腔健康观察结果，帮助早期发现牙周病等问题
- 能力包含：图像/视频分析、口腔区域定位、牙龈颜色识别（粉红/鲜红/暗红）、牙龈红肿等级评估、牙结石检测、牙结石覆盖面积估算（%）、口臭/牙菌斑视觉特征提示、口腔健康风险提示
- 触发条件:
    1. **默认触发**：当用户提供宠物口腔抓拍图像/视频 URL 或文件需要分析时，默认触发本技能进行口腔健康识别
    2. 当用户明确需要进行口腔检查时，提及口腔、牙龈、牙结石、牙周病、口臭、打哈欠抓拍、舔嘴抓拍、张嘴抓拍、宠物牙齿、洗牙必要性等关键词，并且上传了图像/视频文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史口腔报告、历史牙龈识别报告、口腔健康报告清单、查询牙龈记录、显示所有口腔抓拍报告、显示牙结石分析报告，查询宠物口腔健康提示报告
- 自动行为：
    1. 如果用户上传了附件或者图像/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有口腔报告"、"
       显示所有牙龈识别报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --list --open-id` 参数调用 API
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

**在执行口腔牙龈红肿识别分析前，必须按以下优先级顺序获取 open-id：**

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
    1. **准备图像/视频输入**
        - 提供本地图像/视频文件路径或网络 URL
        - 建议在宠物打哈欠、舔嘴、张嘴瞬间抓拍，画面需清晰展示**牙龈+牙齿**区域
        - 光线充足、不逆光、不要使用强烈滤镜（避免颜色失真）；视频建议覆盖完整张嘴过程
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行口腔牙龈红肿识别分析**
        - 调用 `-m scripts.smyx_pet_oral_snapshot_gum_redness_analysis` 处理图像/视频文件（**必须在技能根目录下运行脚本
          **）
        - 参数说明:
            - `--input`: 本地图像/视频文件路径
            - `--url`: 网络图像/视频 URL 地址（API 服务自动下载）
            - `--pet-type`: 宠物类型，可选值：cat/dog/other，默认 dog
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示口腔抓拍历史分析报告列表清单（可输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的口腔健康观察报告
        -
        包含：牙龈颜色分析（粉红/正常、鲜红/轻度炎症、暗红/重度炎症）、牙龈红肿等级（0=正常、1=轻度、2=中度、3=重度）、牙结石覆盖面积（%）、牙菌斑视觉特征、口腔健康风险提示、建议护理动作（刷牙、洁齿玩具、咨询兽医洗牙等）
        - **重要提示**：仅客观描述口腔内可见健康指标，不提供疾病诊断或治疗方案

## 牙龈与牙结石健康参考标准

| 等级      | 牙龈颜色 | 牙结石覆盖面积   | 状态描述        | 建议护理          |
|---------|------|-----------|-------------|---------------|
| ✅ 健康    | 粉红   | < 10%     | 口腔状态良好      | 日常刷牙、洁齿零食     |
| ⚠️ 轻度异常 | 偏红   | 10% ~ 30% | 早期牙菌斑/轻度牙龈炎 | 增加刷牙频率，使用洁齿玩具 |
| 🚨 中度异常 | 鲜红   | 30% ~ 60% | 牙龈炎/牙结石明显   | 建议咨询兽医，考虑洁牙   |
| 🆘 重度异常 | 暗红   | > 60%     | 重度牙周问题风险    | 建议尽快兽医检查      |

> 注：以上标准仅供视觉参考，不作为诊断依据。短头颅犬种（法斗、巴哥）、老龄宠物天然更易出现牙结石堆积，需更频繁监测。

## 资源索引

-
必要脚本：见 [scripts/smyx_pet_oral_snapshot_gum_redness_analysis.py](scripts/smyx_pet_oral_snapshot_gum_redness_analysis.py)(
用途：调用 API 进行口腔抓拍图像/视频的牙龈红肿与牙结石分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和场景代码)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 文件要求：支持 jpg/jpeg/png/bmp/webp 图像 与 mp4/avi/mov 视频，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- 推荐结果仅供口腔护理参考，不提供疾病诊断或治疗方案
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，API 服务会自动下载
- 设备端建议：在宠物打哈欠、舔嘴、张嘴等口部张开动作时自动触发抓拍，提升识别效果
- 短头颅犬种（法斗、巴哥）、老龄宠物天然更易出现牙结石堆积，AI 角色在输出时需主动提醒
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 `reportImageUrl` 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"宠物类型"、"分析时间"、"点击查看"四列，其中"报告名称"列使用`口腔牙龈红肿识别报告-{记录id}`形式拼接, "点击查看"
  列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 宠物类型 | 分析时间 | 点击查看 |
  |----------|----------|----------|----------|
  | 口腔牙龈红肿识别报告-20260522023200001 | 狗 | 2026-05-22 02:32:
  00 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地口腔抓拍图像/视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --input /path/to/oral_snapshot.jpg --pet-type dog --open-id your-open-id

# 分析网络口腔抓拍图像/视频（以下只是示例，禁止直接使用 openclaw-control-ui 作为 open-id）
python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --url https://example.com/oral_snapshot.mp4 --pet-type dog --open-id your-open-id

# 显示历史分析报告清单（自动触发关键词：查看历史口腔报告、口腔抓拍报告清单等）
python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --list --open-id your-open-id

# 输出精简报告
python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --input oral_snapshot.jpg --pet-type dog --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.smyx_pet_oral_snapshot_gum_redness_analysis --input oral_snapshot.jpg --pet-type dog --open-id your-open-id --output result.json
```
