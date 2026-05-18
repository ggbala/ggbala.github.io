# 雅思单词练习器计划书

## 目标

构建一个可以部署到静态服务器的 HTML 单词练习器。页面启动后从相对目录 `question-bank/manifest.json` 获取远程词表清单，再按清单读取 `question-bank/` 下的 `.txt` 题库文件。用户可以选择一个 topic 练习，也可以从全部词表中随机抽取 50 题练习。进入练习后，显示中文意思和英文句子，按题库第三列指定的核心词挖空。

## 关键可行性结论

- 可以用单个 HTML 文件实现，无需 npm、React、Vue 或数据库。
- GitHub Pages 等静态服务器不提供目录文件列表，前端不能直接扫描 `question-bank/`。
- 可行方案是：在 `question-bank/` 下维护 `manifest.json`，页面先 fetch 这个清单，再逐个 fetch 清单里的 `.txt` 文件。
- 页面使用相对路径 `./question-bank/manifest.json`，适合部署在 `words-pratice/` 这类子目录下。

## 文件结构

建议目录结构：

```text
words-pratice/
  index.html
  word-practice.html
  question-bank/
    manifest.json
    Education.txt
    Technology.txt
    Environment.txt
    Health.txt
    Work.txt
    Cities.txt
    Culture.txt
    Globalisation.txt
```

约定：

- `question-bank/` 是题库文件夹。

- 每个 `.txt` 文件代表一个 topic。

- topic 名称来自文件名，例如 `Environment.txt` 的 topic 是 `Environment`。

  

## 题库清单 manifest.json

`question-bank/manifest.json` 负责公开静态题库文件列表，推荐格式：

```json
{
  "files": [
    { "name": "Education", "file": "Education.txt" },
    { "name": "Technology", "file": "Technology.txt" },
    { "name": "Environment", "file": "Environment.txt" }
  ]
}
```

维护规则：

- 每新增或生成一个词表 `.txt` 后，必须同步把该文件加入 `question-bank/manifest.json`。
- `file` 使用相对于 `question-bank/` 的文件名，例如 `Globalisation.txt`。
- `name` 是页面显示的 topic 名，可以包含空格，例如 `Mental Health`。
- 删除或重命名词表时，也必须同步更新 `manifest.json`，避免页面 fetch 到 404。
- 发布前检查 `manifest.json` 是合法 JSON，且每个配置的 `.txt` 文件都存在。

## 题库 TXT 格式

推荐使用一行一题的 TSV 格式，便于人工编辑：

```text
# 中文意思<TAB>英文完整句子<TAB>填空词，最多3个
政府应投资公共交通，以减少城市地区的交通拥堵。	Governments should invest in public transport to reduce traffic congestion in urban areas.	reduce, congestion, urban
高质量教育在促进社会流动性方面起着关键作用。	High-quality education plays a crucial role in promoting social mobility.	crucial, promoting, mobility
```

解析规则：

- 空行忽略。
- 以 `#` 开头的行作为注释忽略。
- 每行用 Tab 分隔中文、英文和填空词三列。
- 中文列显示给用户。
- 英文列用于生成练习题。
- 第三列用英文逗号配置 1-3 个填空词，填空词必须出现在英文句子中。
- 如果格式错误，页面在加载结果中提示具体文件和行号。

## 挖空规则

默认规则：

- 英文句子按单词 token 拆分。
- 第三列配置的 1-3 个核心词变成输入框。
- 未配置为填空词的英文 token 显示原文。
- 标点保留在相邻位置，不要求用户输入标点。
- 大小写不敏感。
- 忽略输入前后空格。
- 可选支持英式/美式拼写别名，但第一版不做复杂同义词判断。

示例：

```text
中文：政府应投资公共交通，以减少城市地区的交通拥堵。
显示：Governments should invest in public transport to [____] traffic [____] in [____] areas.
答案：reduce / congestion / urban
```

## 页面流程

1. 启动页
   - 显示应用标题。
   - 自动读取 `question-bank/manifest.json`。
   - 提供“重新获取词表”按钮。

2. 题库加载页
   - 读取 manifest 中配置的 `.txt` 文件。
   - 按文件名生成 topic 列表。
   - 显示每个 topic 的题目数量。
   - 显示格式错误、空文件、重复题等警告。

3. Topic 选择页
   - 用户可以选择一个 topic。
   - 用户可以选择“随机 50 题”，从全部已加载词表中混合抽题。
   - 可选“顺序练习 / 随机练习”。

4. 练习页
   - 顶部显示 topic、进度、当前题号。
   - 主区域显示中文意思。
   - 下方显示英文句子，挖空词为输入框。
   - 输入错误显示红色。
   - 输入正确显示绿色。
   - 当前题全部绿色后，才允许进入下一题。

## 交互规则

- 用户输入后实时校验当前空。
- 当前空正确后，自动聚焦到下一个空。
- 当前空错误时保留焦点，边框或背景显示红色。
- 全部空正确后，下一题按钮变为可用。
- 全部正确后，按 `Enter` 或 `ArrowRight` 进入下一题。
- 未全部正确时，`Enter` 只用于尝试跳转到下一个未完成空。
- 支持“重新开始当前 topic”。
- 支持“返回 topic 选择”。

## 视觉规则

- 正确输入框：绿色边框、浅绿色背景。
- 错误输入框：红色边框、浅红色背景。
- 未输入输入框：中性边框。
- 全部通过后显示明确的完成状态，例如“本题完成，可以进入下一题”。
- 移动端可用，但优先优化桌面浏览器体验。

## 题库内容策略

- 不复制 Cambridge IELTS 真题、培训机构资料或版权材料。
- 使用原创雅思风格句子。
- topic 覆盖 IELTS 常见主题：
  - Education
  - Technology
  - Environment
  - Health
  - Work
  - Cities
  - Culture
  - Globalisation
- 句子难度控制在雅思 6.0–7.5 左右。
- 每句 12–22 个词，包含自然、地道、学术化的表达。

## 第一版交付范围

- `index.html`
  - 单文件远程静态应用。
  - 支持从 `question-bank/manifest.json` 获取词表列表。
  - 支持按 topic 练习。
  - 支持从全部词表随机抽取 50 题练习。
  - 支持按第三列自动挖空核心词。
  - 支持正确/错误即时反馈。
  - 支持全部正确后快捷键下一题。

- `question-bank/`
  - 每个 topic 一个 txt 文件。
  - 维护 `manifest.json` 作为发布后的题库列表。

## 暂不做的功能

- 不记录历史进度。
- 不做登录。
- 不做云同步。
- 不引入数据库。
- 不做复杂同义词判断。
- 不自动读取固定本地路径。

## 风险和处理

- 静态服务器无法自动列目录：用 `manifest.json` 解决。
- manifest 配置遗漏会导致新词表不显示：生成词表后必须同步更新清单。
- manifest 指向不存在文件会导致 404：发布前检查文件存在。
- 自动 token 拆分可能对连字符、缩写、所有格不完美：第一版先覆盖常见英文句子，后续可优化 tokenizer。
- 多答案同义词判断复杂：第一版要求和题库英文句子一致，避免误判。

## 实施步骤

1. 创建 `index.html` 的基础布局和样式。
2. 实现 `manifest.json` 获取和 `.txt` 解析。
3. 实现 topic 列表、题数统计和格式错误提示。
4. 实现英文句子 token 拆分和后半句挖空。
5. 实现输入框实时校验、颜色反馈和自动跳焦。
6. 实现下一题逻辑、快捷键和完成状态。
7. 创建并维护 `question-bank/manifest.json`。
