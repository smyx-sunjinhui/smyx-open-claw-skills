---
name: "incontinence_alert_analysis"
description: "Automatically identifies wet clothing and abnormal excretion via visual AI. Instantly notifies caregivers to improve care for incontinent elderly, bedridden patients, and infants, reducing skin issues and complications. | 智能失禁状态提醒技能，基于视觉AI自动识别衣物潮湿、排泄异常等状况，第一时间推送通知给看护人员，提升失能老人、卧床病人、婴幼儿的护理质量，减少皮肤问题和并发症"
version: "1.0.5"
---

# Smart Incontinence Status Alert Skill | 智能失禁状态提醒技能

Based on visual AI technology, this capability automatically identifies conditions such as damp clothing or abnormal
excretion in disabled seniors, bedridden patients, or infants. By analyzing image texture, reflection characteristics,
and regional changes, the system monitors body surface and contact area status in real-time. Upon detecting anomalies,
it immediately pushes notifications to caregivers to facilitate timely cleaning and changing. It is suitable for
scenarios in nursing homes, home care, and maternal-infant care, effectively reducing the risks of skin eczema, diaper
rash, and infection, while enhancing nursing quality and response efficiency.

本技能基于视觉AI技术，自动识别失能老人、卧床病人或婴幼儿的衣物潮湿、排泄异常等状况。系统通过分析图像纹理、反射特征及区域变化，实时监测体表与接触面状态，发现异常后第一时间向看护人员推送通知，帮助及时清洁更换。适用于养老机构、居家护理及母婴照护场景，有效降低皮肤湿疹、尿布疹及感染风险，提升护理质量与响应效率。

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过监控摄像头视频/图片进行智能失禁状态识别，自动检测衣物潮湿、排泄异常状况，及时触发预警通知看护人员，提升护理效率
- 能力包含：视频/图片分析、衣物潮湿识别、排泄物检测、异常状态预警、护理记录生成、护理提醒建议
- 触发条件:
    1. **默认触发**：当用户提供监控视频/图片 URL 或文件需要进行失禁状态检测时，默认触发本技能进行异常识别分析
    2. 当用户明确需要进行护理监测、失禁提醒、潮湿检测，提及护理提醒、失禁预警、衣物潮湿、卧床护理、老人护理、婴儿护理等关键词，并且上传了视频文件或者图片文件
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史护理报告、历史预警记录、失禁提醒报告清单、查询历史报告、查看护理报告列表、显示所有预警记录、显示失禁分析报告，查询失禁状态提醒报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有护理报告"、"显示所有预警记录"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.incontinence_alert_analysis --list` 调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 操作步骤

### 🔐 用户身份处理（内部自动完成）

用户身份参数由系统内部自动处理，**不得向用户展示、询问或要求输入任何身份标识**。

执行本技能分析或历史报告查询时，脚本会自动完成身份初始化：

- 上游系统如有内部身份参数，会由脚本静默接收并使用
- 上游系统未提供时，脚本会自动复用本地缺省用户
- 本地缺省用户不存在时，脚本会自动创建并在后续任务中复用
- 对用户输出时，只展示分析进度、分析结果和报告链接，不展示内部身份值

**关键约束：**

- 不得提示用户输入用户名、手机号或任何内部身份参数
- 不得在回复、报告、示例、错误提示中暴露内部身份值
- 不得把内部身份参数列为用户需要理解或传入的参数
- 历史报告查询同样由系统内部身份自动关联，用户只需表达“查看历史报告/报告清单”等意图

---

- 标准流程:
    1. **准备视频/图片输入**
        - 提供本地视频/图片文件路径或网络媒体 URL
        - 确保摄像头对准护理对象下半身/臀部区域，画面清晰，光线充足
    2. **系统自动完成身份关联**
        - 无需用户输入任何身份参数
        - 不在回复中展示内部身份值
    3. **执行失禁状态检测分析**
        - 调用 `-m scripts.incontinence_alert_analysis` 处理文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频/图片文件路径
            - `--url`: 网络媒体 URL 地址（API 服务自动下载）
            - `--user-type`: 护理对象类型，可选值：elderly(失能老人)/bedridden(卧床病人)/infant(婴幼儿)/other，默认 other
            - `--detection-mode`: 检测模式，可选值：real-time(实时监控)/regular-check(定时巡查)，默认 real-time
            - `--list`: 显示历史失禁提醒检测报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的失禁状态护理预警报告
        - 包含：护理对象信息、检测结果、异常状态分级、预警级别、清洁护理建议

## 资源索引

- 必要脚本：见 [scripts/incontinence_alert_analysis.py](scripts/incontinence_alert_analysis.py)(用途：调用 API
  进行失禁状态分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和媒体格式限制，场景码已设置为
  INCONTINENCE_ALERT_ANALYSIS)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 格式支持：视频支持 mp4/avi/mov 格式，图片支持 jpg/png/jpeg 格式，最大 10MB
- 分析结果仅供护理参考，不能替代专业医护人员判断和人工检查
- 本工具涉及个人隐私，请严格保密检测记录，仅授权看护人员访问
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"护理对象"、"分析时间"、"预警等级"、"点击查看"五列，其中"报告名称"列使用`失禁状态提醒报告-{记录id}`形式拼接，点击查看列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 护理对象 | 分析时间 | 预警等级 | 点击查看 |
  |----------|----------|----------|----------|----------|
  | 失禁状态提醒报告 -20260312172200001 | 失能老人 | 2026-03-12 17:22:00 | 🔴
  需要立即处理 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析失能老人实时监控视频
python -m scripts.incontinence_alert_analysis --input /path/to/care_video.mp4 --user-type elderly --detection-mode real-time 分析卧床病人巡查图片
python -m scripts.incontinence_alert_analysis --input /path/to/care_image.jpg --user-type bedridden --detection-mode regular-check 分析婴幼儿监控视频
python -m scripts.incontinence_alert_analysis --url https://example.com/baby_care.mp4 --user-type infant --detection-mode real-time 显示历史分析报告/显示分析报告清单列表/显示历史护理报告（自动触发关键词：查看历史护理报告、历史报告、护理报告清单等）
python -m scripts.incontinence_alert_analysis --list

# 输出精简报告
python -m scripts.incontinence_alert_analysis --input video.mp4 --user-type elderly --detail basic

# 保存结果到文件
python -m scripts.incontinence_alert_analysis --input image.jpg --user-type bedridden --output result.json
```
