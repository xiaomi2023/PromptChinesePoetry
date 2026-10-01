![MiYago LOGO.png](image/MiYago%20LOGO.png)

# PromptChinesePoetry

<p align="center">
<a href="README.md">English</a> |
中文
</p>

## 介绍

快速发展的大语言模型（LLMs）在诗歌生成领域存在广泛应用，但是利用已有模型为诗歌生成任务合成训练数据可能会导致多样性退化或引入已有模型的偏见。

鉴于此，本数据集收集了上万首真实的中文古典诗词，并为每首诗/词合成了自然语言生成 prompt，以更好地训练和评估模型"根据指定指令生成中文诗词"的能力。

每条样本都是一次完整的多轮对话：系统提示要求以中文作答，用户用自然语言描述想要的诗/词（体裁、题材、韵律、情绪、场景等），助手回复对应的原文。

## 数据组成和结构

### 文件

本数据集包含两个文件：

| 文件 | 条目数 | 说明 |
| --- | --- | --- |
| `data/poetry_user_inputs.jsonl` | 8,163 |
| `data/poetry_100_1000_user_inputs.jsonl` | 27,125 |

合计 35,288 条样本。

### 字段结构

每行是一个独立的 JSON 对象（JSON Lines 格式）：

```json
{
  "conversations": [
    {
      "role": "system",
      "content": "你是一个作家，请用中文输出。"
    },
    {
      "role": "user",
      "content": "想让你模仿温庭筠的风格写一首《杨柳枝》，三四句就行，写春景里黄鹂在柳枝上叫的意象，带点闺怨或者思远的情绪"
    },
    {
      "role": "assistant",
      "content": "杨柳枝·两两黄鹂色似金\n\n两两黄鹂色似金，袅枝啼露动芳音。\n春来幸自长如线，可惜牵缠荡子心。"
    }
  ]
}
```

- **`conversations`**：三轮对话，分别为 system 设定、user 指令、assistant 生成的诗/词原文。
- **`language`**：prompt 的语言标签，取值为 ISO 639-1 语言代码（如 `ja`、`ru`）。中文 prompt 无此字段。

### 语言分布

两个文件的用户 prompt 的语言构成比例：

| 语言 | 代码 | `poetry_user_inputs` | `poetry_100_1000_user_inputs` |
| --- | --- | --- | --- |
| 中文 | （无字段） | 5,893 | 19,542 |
| 日语 | `ja` | 164 | 545 |
| 波兰语 | `pl` | 162 | 541 |
| 西班牙语 | `es` | 162 | 541 |
| 阿拉伯语 | `ar` | 162 | 546 |
| 俄语 | `ru` | 163 | 544 |
| 意大利语 | `it` | 163 | 542 |
| 德国语 | `de` | 163 | 542 |
| 印地语 | `hi` | 162 | 541 |
| 法语 | `fr` | 163 | 545 |
| 越南语 | `vi` | 163 | 543 |
| 印尼语 | `id` | 164 | 543 |
| 葡萄牙语 | `pt` | 161 | 541 |
| 泰语 | `th` | 158 | 534 |
| 韩语 | `ko` | 160 | 535 |

共 15 种语言：中文加 14 种外语（无英语）。


### 数据样例

#### 中文 prompt

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家，请用中文输出。"},
    {"role": "user", "content": "帮我写一首五言绝句，主题是隐居山林、淡泊自在的感觉，最后两句提到以后去南岳会记得这座山的名字。"},
    {"role": "assistant", "content": "俞公巖\n\n萬事若在手，百年聊稱情。\n他時南嶽去，記得此巖名。"}
  ]
}
```

#### 非中文 prompt

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家，请用中文输出。"},
    {"role": "user", "content": "Напиши, пожалуйста, стихотворение на китайском языке в жанре 七律 (люйши из семи слов), на тему воспоминаний о друге-литераторе прошлых лет, с мотивами книг, свитков, облаков над горами Хэншань и лунного света над столицей. Настроение — спокойная ностальгия, ощущение отдалённости от мирской суеты. Желательно упомянуть чувство долга перед оказанной милостью и мечту о встрече с даосским отшельником в духе Чэнь Туаня."},
    {"role": "assistant", "content": "寄贈宣義大師\n\n篆寫千文邁古今，感陶承旨撰碑陰。\n兩朝雨露書中得，滿篋詩章物外尋。\n衡嶽水雲長挂夢，帝城烟月不關心。\n西遊去後無消息，想共陳摶一處吟。"}
  ],
  "language": "ru"
}
```

## 合成

首先从[chinese-poetry/chinese-poetry](https://github.com/chinese-poetry/chinese-poetry)中抓取涵盖多个来源和类型的诗词并进行清洗，
然后利用 LLM 根据诗词合成多样的用户 prompt，包含体裁、题材、韵律、情绪、场景等提示。  
本数据集的构建只使用了谷歌搜索结果数小于1000的诗词，以避免让模型在已存在于知识库的诗/词上进行无效训练。

## 使用方式

数据以 JSON Lines 格式发布，直接读取即可：

```python
import json

with open("data/poetry_user_inputs.jsonl", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            sample = json.loads(line)
            conversations = sample["conversations"]
            # conversations[1]["content"] 为多语言 prompt
            # conversations[2]["content"] 为中文诗词原文（简繁体混杂，源自原始数据）
```

## 问题和局限

助手回答可能会存在中文简体和繁体混杂的情况，建议在使用前进行细致的评估。

欢迎报告其他问题或提出建议。

## MiYago Datasets

[MiYago Datasets](https://huggingface.co/collections/Mikoris/miyago-datasets)是一个专为提升模型创意写作和角色扮演能力而生的数据集 Collection。

## 许可协议

本数据集采用 [Apache License 2.0](LICENSE) 许可。