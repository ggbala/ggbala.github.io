# 雅思词汇真经 词表生成指南

## 数据来源

词表基于《雅思词汇真经》(刘洪波编著) 的 22 个主题章节生成，每个章节生成一个 `.txt` 题库文件。

## 格式规范

每个 `.txt` 文件采用 TSV 格式（Tab 分隔），结构如下：

```
# 中文意思	英文完整句子	填空词，最多3个
中文句子	English sentence with target words.	word1, word2, word3
```

### 解析规则

- 空行忽略
- 以 `#` 开头的行作为注释忽略
- 每行用 Tab (`\t`) 分隔三列：中文、英文、填空词
- 第三列用英文逗号配置 1-3 个填空词
- 填空词必须出现在英文句子中，大小写不敏感

## 内容质量标准

- 每句 12-22 个英文单词
- 句子难度控制在雅思 6.0-7.5 水平
- 使用自然、地道、学术化的表达
- 覆盖该主题的核心词汇
- 每章节不少于 50 题（推荐 55 题）
- 所有句子为原创，不复制 Cambridge IELTS 真题

## 填空词选择原则

- 优先选择该章节的主题核心词
- 优先选择学术词汇 (AWL)
- 每句 1-3 个填空词
- 填空词在英文句子中必须存在且唯一

## 词汇覆盖要求

- 每个章节覆盖该主题相关的核心名词、动词、形容词、副词
- 词汇不可跨章节重复（确保每个填空词只在一个章节出现）
- 同一章节内句子不重复

## 文件命名

- 使用英文名称，单词间用连字符连接
- 示例：`Natural-Scenery.txt`, `Animal-Kingdom.txt`

## 22 章节清单

1. 自然风光 (Natural Scenery)
2. 动物王国 (Animal Kingdom)
3. 环境保护 (Environmental Protection)
4. 人类社会 (Human Society)
5. 教育 (Education)
6. 科技 (Science & Technology)
7. 健康 (Health & Medicine)
8. 经济 (Economy)
9. 工作与职业 (Work & Career)
10. 文化与艺术 (Culture & Arts)
11. 传媒 (Mass Media)
12. 城市与建筑 (Cities & Architecture)
13. 交通 (Transport)
14. 旅游 (Tourism)
15. 政府与政治 (Government & Politics)
16. 法律与犯罪 (Law & Crime)
17. 家庭与生活 (Family & Life)
18. 饮食 (Food & Drink)
19. 时尚 (Fashion)
20. 运动 (Sports)
21. 全球化 (Globalisation)
22. 语言与交流 (Language & Communication)
