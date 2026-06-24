---
name: "psychological-stress-assessment-analysis"
description: "Combines facial blood flow and emotional characteristics to analyze stress index, anxiety tendency, and depression tendency, suitable for mental health monitoring scenarios. | 心理压力评估技能，结合面部血流与情绪特征，分析压力指数、焦虑倾向、抑郁倾向，适用于心理健康监测场景"
version: "1.0.7"
---

# Psychological Stress Assessment Skill | 心理压力评估技能

Based on advanced non-contact physiological signal detection and affective computing technologies, this feature captures
subtle facial blood flow changes (rPPG) and micro-expression characteristics (FACS) via high-precision cameras to deeply
analyze user stress levels, anxiety tendencies, and depression tendencies. By leveraging remote photoplethysmography to
restore physiological indicators like Heart Rate Variability (HRV) and combining this with AI emotion recognition
algorithms to capture emotional fluctuations in micro-expressions, the system accurately quantifies mental health
status. Ideal for corporate employee care, campus psychological screening, and home health monitoring, this feature
provides users with imperceptible and objective mental health assessment reports, facilitating the early detection and
intervention of psychological issues.

本功能基于先进的非接触式生理信号检测与情感计算技术，通过高精度摄像头捕捉面部微细血流变化（rPPG）及细微表情特征（FACS），深度融合分析用户的压力指数、焦虑倾向及抑郁倾向。系统利用远程光电容积脉搏波技术还原心率变异性等生理指标，结合AI情绪识别算法捕捉微表情中的情绪波动，能够精准量化心理健康状态。该功能适用于企业员工关怀、校园心理筛查及家庭健康监测场景，为用户提供无感、客观的心理健康评估报告，助力心理问题的早期发现与干预

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史评估报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过人脸视频结合视觉分析进行心理压力评估，获取结构化的心理压力评估报告
- 能力包含：压力指数分析、焦虑倾向识别、抑郁倾向识别
- 触发条件:
    1. **默认触发**：当用户提供人脸视频/图片 URL 或文件需要进行心理压力评估时，默认触发本技能
    2. 当用户明确需要进行心理压力评估，提及压力评估、焦虑倾向、抑郁倾向、心理压力监测等关键词，并且上传了视频或图片
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史评估报告、心理压力评估报告清单、评估报告列表、查询历史报告、显示所有评估报告、心理压力评估历史记录，查询心理压力评估分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频/图片文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有评估报告"、"
       显示所有压力评估报告"、"查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.psychological_stress_assessment_analysis --list` 调用 API
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
    1. **准备视频输入**
        - 提供人脸视频文件路径或网络视频 URL
        - 确保人脸完整露出，光线充足
    2. **系统自动完成身份关联**
        - 无需用户输入任何身份参数
        - 不在回复中展示内部身份值
    3. **执行心理压力评估**
        - 调用 `-m scripts.psychological_stress_assessment_analysis` 处理视频文件（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频文件路径
            - `--url`: 网络视频 URL 地址（API 服务自动下载）
            - `--media-type`: 媒体类型，可选值：video/image，默认 video
            - `--list`: 显示心理压力评估历史分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的心理压力评估报告
        - 包含：基本信息、压力指数、焦虑倾向、抑郁倾向、提示建议

## 资源索引

-

必要脚本：见 [scripts/psychological_stress_assessment_analysis.py](scripts/psychological_stress_assessment_analysis.py)(
用途：调用 API 进行心理压力评估，本地文件上传，网络 URL 由 API 服务自动下载)

- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和视频格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 视频要求：支持 mp4/avi/mov 格式，最大 10MB
- 建议视频时长不少于 2 分钟以反映真实压力状态
- 本技能仅作心理健康评估参考，不能替代专业心理咨询和临床诊断，发现持续异常请及时寻求专业帮助
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网络地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史评估报告清单的时候，从数据 json 中提取字段 reportImageUrl 作为超链接地址，使用 Markdown 表格格式输出，包含"
  报告名称"、"评估时间"、"压力指数"、"点击查看"四列，其中"报告名称"列使用`心理压力评估报告-{记录id}`形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 评估时间 | 压力指数 | 点击查看 |
  |----------|----------|----------|----------|
  | 心理压力评估报告-20260312172200001 | 2026-03-12 17:22:00 |
  68/100 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地人脸视频
python -m scripts.psychological_stress_assessment_analysis --input /path/to/face_video.mp4 --media-type video 分析人脸照片
python -m scripts.psychological_stress_assessment_analysis --input /path/to/face.jpg --media-type image 显示历史评估报告/显示评估报告清单列表/显示历史心理压力评估报告（自动触发关键词：查看历史评估报告、历史报告、评估报告清单等）
python -m scripts.psychological_stress_assessment_analysis --list

# 输出精简报告
python -m scripts.psychological_stress_assessment_analysis --input video.mp4 --media-type video --detail basic

# 保存结果到文件
python -m scripts.psychological_stress_assessment_analysis --input video.mp4 --media-type video --output result.json
```
