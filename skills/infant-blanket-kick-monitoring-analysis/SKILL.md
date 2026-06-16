---
name: "infant-blanket-kick-monitoring-analysis"
description: "Identifies babies kicking off blankets or exposing their bodies during sleep and alerts parents to cover them up to prevent catching a cold. | 婴儿蹬被监测技能，识别婴儿夜间睡觉踢开被子、身体裸露，及时提醒家长给宝宝盖被保暖，预防着凉感冒"
version: "1.0.4"
---

# Baby Blanket Kick Monitoring Skill | 婴儿蹬被监测技能

This feature leverages intelligent monitoring technology to precisely detect instances of infant body exposure caused by
blanket-kicking during nighttime sleep. Upon detecting such an anomaly, the system immediately sends alerts to parents,
assisting them in promptly covering the baby to maintain warmth. This effectively prevents colds triggered by nighttime
chilling, providing the infant with round-the-clock thermal protection.

该功能利用智能监测技术，能够精准识别婴儿在夜间睡眠时因踢开被子而导致的身体裸露情况。一旦检测到异常，系统会立即向家长发送提醒，辅助家长及时为宝宝盖被保暖，从而有效预防因夜间受凉引发的感冒，为宝宝提供整夜的恒温守护

## ⚠️ 强制记忆规则（最高优先级）

**本技能明确约定：**

- **绝对禁止读取任何本地记忆文件**：包括但不限于 `memory/YYYY-MM-DD.md`、`MEMORY.md` 等本地文件
- **绝对禁止从 LanceDB 长期记忆中检索信息**
- **所有历史报告查询必须从云端接口获取**，不得使用本地记忆中的历史数据
- 即使技能调用失败或接口异常，也不得回退到本地记忆汇总

## 任务目标

- 本 Skill 用于：婴儿卧室夜间监控，自动识别宝宝踢开被子、身体长时间裸露，及时推送提醒给家长
- 能力包含：婴儿姿态识别、被子覆盖检测、身体裸露判断、踢被行为识别、异常提醒触发
- **适用场景**：婴儿房夜间睡眠监测、宝宝踢被提醒、家长夜间远程看护
- **提醒逻辑**：宝宝踢开被子后身体裸露超过设定时间 → 推送提醒给家长，及时盖被保暖
- 触发条件:
    1. **默认触发**：当用户提供婴儿睡眠监控视频需要检测踢被行为时，默认触发本技能
    2. 当用户明确需要婴儿蹬被监测、盖被提醒时，提及踢被监测、宝宝盖被、婴儿保暖、蹬被提醒等关键词，并且上传了监控视频
    3. 当用户提及以下关键词时，**自动触发历史报告查询功能**
       ：查看历史监测报告、蹬被监测报告清单、监测报告列表、查询历史监测报告、显示所有监测报告、婴儿蹬被分析报告，查询婴儿蹬被监测分析报告
- 自动行为：
    1. 如果用户上传了附件或者视频文件，则自动保存为本地文件
    2. **⚠️ 强制数据获取规则（次高优先级）**：如果用户触发任何历史报告查询关键词（如"查看所有监测报告"、"显示历史踢被记录"、"
       查看历史报告"等），**必须**：
        - 直接使用 `python -m scripts.infant_blanket_kick_monitoring_analysis --list --open-id` 参数调用 API
          查询云端的历史报告数据
        - **严格禁止**：从本地 memory 目录读取历史会话信息、严格禁止手动汇总本地记录中的报告、严格禁止从长期记忆中提取报告
        - **必须统一**从云端接口获取最新完整数据，然后以 Markdown 表格格式输出结果

## 前置准备

- 依赖说明:scripts 脚本所需的依赖包及版本
  ```
  requests>=2.28.0
  ```

## 监测要求（获得准确结果的前提）

为了获得准确的踢被监测，请确保：

1. **摄像头固定位置**，覆盖婴儿床整体区域
2. **夜视/红外功能正常**，夜间关灯后宝宝轮廓清晰可见
3. **婴儿床无过多杂物遮挡**，确保宝宝身体和被子区域完整可见

## 操作步骤

### 🔒 open-id 获取流程控制（强制执行，防止遗漏）

**在执行婴儿蹬被监测分析前，必须按以下优先级顺序获取 open-id：**

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

- **禁止**自行假设,自行推导,自行生成 open-id 值（如 openclaw-control-ui、default、baby123、blanket456 等）
- **禁止**跳过 open-id 验证直接调用 API
- **必须**在获取到有效 open-id 后才能继续执行分析
- 如果用户拒绝提供 open-id，说明用途（用于保存和查询监测报告记录），并询问是否继续

---

- 标准流程:
    1. **准备婴儿床监控视频输入**
        - 提供本地视频文件路径或网络视频 URL
        - 确保婴儿床完整出镜，夜间红外清晰
    2. **获取 open-id（强制执行）**
        - 按上述流程控制获取 open-id
        - 如无法获取，必须提示用户提供用户名或手机号
    3. **执行婴儿蹬被监测分析**
        - 调用 `-m scripts.infant_blanket_kick_monitoring_analysis` 处理视频（**必须在技能根目录下运行脚本**）
        - 参数说明:
            - `--input`: 本地视频文件路径
            - `--url`: 网络视频 URL 地址（API 服务自动下载）
            - `--open-id`: 当前用户的 open-id（必填，按上述流程获取）
            - `--list`: 显示历史婴儿蹬被监测分析报告列表清单（可以输入起始日期参数过滤数据范围）
            - `--api-key`: API 访问密钥（可选）
            - `--api-url`: API 服务地址（可选，使用默认值）
            - `--detail`: 输出详细程度（basic/standard/json，默认 json）
            - `--output`: 结果输出文件路径（可选）
    4. **查看分析结果**
        - 接收结构化的婴儿蹬被监测分析报告
        - 包含：视频基本信息、识别到的踢被次数、最长裸露时长、是否触发提醒、护理建议

## 资源索引

- 必要脚本：见 [scripts/infant_blanket_kick_monitoring_analysis.py](scripts/infant_blanket_kick_monitoring_analysis.py)(
  用途：调用 API 进行婴儿蹬被监测分析，本地文件上传，网络 URL 由 API 服务自动下载)
- 配置文件：见 [scripts/config.py](scripts/config.py)(用途：配置 API 地址、默认参数和格式限制)
- 领域参考：见 [references/api_doc.md](references/api_doc.md)(何时读取：需要了解 API 接口详细规范和错误码时)

## 注意事项

- 仅在需要时读取参考文档，保持上下文简洁
- 支持格式：mp4/avi/mov，最大 10MB
- API 密钥可选，如果通过参数传入则必须确保调用鉴权成功，否则忽略鉴权
- **⚠️ 重要提示**：本监测结果仅供辅助提醒参考，不能替代家长看护和婴儿安全监护，请确保宝宝睡眠环境安全
- 禁止临时生成脚本，只能用技能本身的脚本
- 传入的网路地址参数，不需要下载本地，默认地址都是公网地址，api 服务会自动下载
- 当显示历史分析报告清单的时候，从接口返回 json 数据中提取字段 reportImageUrl 作为超链接地址，且自动转化为如下 Markdown
  表格格式输出，包含"
  报告名称"、"监测时长"、"踢被次数"、"是否提醒"、"监测时间"、"点击查看"六列，其中"报告名称"列使用`婴儿蹬被监测报告-{记录id}`
  形式拼接, "点击查看"列使用
  `[🔗 查看报告](reportImageUrl)`
  格式的超链接，用户点击即可直接跳转到对应的完整报告页面。
- 表格输出示例：
  | 报告名称 | 监测时长 | 踢被次数 | 是否提醒 | 监测时间 | 点击查看 |
  |----------|----------|----------|----------|----------|----------|
  | 婴儿蹬被监测报告 -20260329001700001 | 4小时 | 2次 | 1次已提醒 | 2026-03-29 00:
  17 | [🔗 查看报告](https://example.com/report?id=xxx) |

## 使用示例

```bash
# 分析本地婴儿房监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.infant_blanket_kick_monitoring_analysis --input /path/to/nursery.mp4 --open-id openclaw-control-ui

# 分析网络监控视频（以下只是示例，禁止直接使用openclaw-control-ui 作为 open-id）
python -m scripts.infant_blanket_kick_monitoring_analysis --url https://example.com/nursery.mp4 --open-id openclaw-control-ui

# 显示历史监测报告/显示监测报告清单列表/显示历史踢被监测（自动触发关键词：查看历史监测报告、历史报告、监测报告清单等）
python -m scripts.infant_blanket_kick_monitoring_analysis --list --open-id openclaw-control-ui

# 输出精简报告
python -m scripts.infant_blanket_kick_monitoring_analysis --input nursery.mp4 --open-id your-open-id --detail basic

# 保存结果到文件
python -m scripts.infant_blanket_kick_monitoring_analysis --input nursery.mp4 --open-id your-open-id --output result.json
```
