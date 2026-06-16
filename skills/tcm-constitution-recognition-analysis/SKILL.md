---
name: "tcm-constitution-recognition-analysis"
description: "Determines nine TCM constitution types including Yin deficiency, Yang deficiency, Qi deficiency, phlegm-dampness, and blood stasis through facial features and physical signs, and provides personalized health preservation and conditioning suggestions. | 中医体质识别分析技能，通过面部特征与体征判别阴虚、阳虚、气虚、痰湿、血瘀等九种中医体质类型，给出个性化养生调理建议"
version: "1.0.5"
---

# TCM Constitution Identification & Analysis Tool | 中医体质识别分析工具

Based on TCM Constitution Theory and AI image recognition technology, this feature utilizes high-precision cameras to
capture facial characteristics, combining them with signs from the tongue, skin color, and luster to intelligently
identify nine TCM constitution types—including Yin Deficiency, Yang Deficiency, Qi Deficiency, Phlegm-Dampness, and
Blood Stasis. Adhering to the national standard Classification and Determination of TCM Constitutions, the system
integrates subtle facial features with identification algorithms to generate assessment reports detailing constitution
types, tendency analysis, and health risks. Guided by the TCM philosophy of "Preventive Treatment of Disease" (treating
potential diseases), it provides personalized regimens covering diet, daily routine, acupoint massage, and exercise,
empowering users to achieve precise health preservation and constitution conditioning.

本功能基于中医体质学说与人工智能图像识别技术，通过高精度摄像头采集用户面部特征，结合舌象、肤色、光泽等体征信息，智能判别阴虚、阳虚、气虚、痰湿、血瘀等九种中医体质类型。系统依据《中医体质分类与判定》国家标准，融合面部微细特征与体质辨识算法，生成包含体质类型、倾向分析及健康风险的评估报告，并基于中医“治未病”理念，提供个性化的饮食调养、起居建议、穴位按摩及运动方案，助力用户实现精准养生与体质调理

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：通过面部照片/视频，基于中医面诊原理识别九种中医体质类型
- 能力包含：面部特征提取、体质类型判别、体质评分、个性化养生调理建议
- 支持判别九种体质：**阴虚体质、阳虚体质、气虚体质、痰湿体质、湿热体质、血瘀体质、气郁体质、特禀体质、平和体质**
- 根据中医理论，"舌脉合参"面诊优先，通过面色特征辅助判别体质倾向
- 触发条件:
    1. **默认触发**：当用户提供面部照片需要进行中医体质识别时，默认触发本技能
    2. 当用户明确需要中医体质识别、面诊分析时，提及中医体质、面诊、体质辨识、养生调理等关键词，并且上传了面部照片/视频
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史体质报告、中医体质报告清单、体质识别报告列表、查询历史体质报告、显示所有体质报告、中医体质分析报告，查询中医体质识别分析报告
- 自动行为：
    1. 如果用户上传了附件或者照片/视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有体质报告"、"显示所有面诊结果"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.tcm_constitution_recognition_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 采集要求（获得准确结果的前提）

为了获得较准确的体质识别，请确保：

1. **面部正对摄像头**，光线充足均匀，避免强光和阴影
2. **素颜最佳**，避免浓妆影响面色特征提取
3. **露出完整面部**，不要口罩、帽子、墨镜遮挡

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行中医体质识别分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、tcm123、constitution456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询体质识别报告记录），并询问是否继续

---

- 标准流程:
    1. **准备面部照片输入**
        - 提供本地图片/视频文件路径或网络 URL
        - 确保满足上述采集要求，获得更准确结果
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行中医体质识别分析**
        - 调用 `-m scripts.tcm_constitution_recognition_analysis` 处理输入（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地图片/视频文件路径
            - `--url`: 网络图片/视频 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史中医体质识别分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的中医体质识别分析报告
        - 包含：面部基本信息、主要体质类型、次要体质倾向、各体质评分、中医理论分析、个性化养生调理建议（饮食、运动、生活习惯）

## 资源索引

- 必要脚本：见 [scripts/tcm_constitution_recognition_analysis.py](scripts/tcm_constitution_recognition_analysis.py)(用途：调用
  API 进行中医体质识别分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：jpg/jpeg/png/mp4/avi/mov，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **重要提示**：本识别结果仅供中医养生参考，不能替代专业中医师诊断，身体不适请及时就医
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"主要体质"、"分析时间"、"平和评分"、"点击查看"五列，其中"报告名称"列使用`中医体质识别报告-{记录id}`形式拼接, "
  点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 主要体质 | 分析时间 | 平和评分 | 点击查看 |
  |----------|----------|----------|----------|----------|
  | 中医体质识别报告 -20260328221000001 | 气虚质 | 2026-03-28 22:10:00 |
  85/100 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地面部照片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.tcm_constitution_recognition_analysis --input /path/to/face.jpg --open-id openclaw-control-ui

# 分析网络图片（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.tcm_constitution_recognition_analysis --url https://example.com/face.jpg --open-id openclaw-control-ui

# 显示历史分析报告/显示分析报告清单列表/显示历史体质报告（自动触发关键词：查看历史体质报告、历史报告、体质报告清单等）
python -m scripts.tcm_constitution_recognition_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.tcm_constitution_recognition_analysis --input face.jpg --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.tcm_constitution_recognition_analysis --input face.jpg --open-id your-open-id --output result.json
```
