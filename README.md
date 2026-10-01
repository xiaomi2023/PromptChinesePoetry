![MiYago LOGO.png](image/MiYago%20LOGO.png)

# PromptChinesePoetry

<p align="center">
English |
<a href="README_CN.md">中文</a>
</p>

## Introduction

Large language models (LLMs) are widely used for poetry generation, but synthesizing training data for that task from existing models can lead to reduced diversity or introduce the biases already present in those models.

To address this, this dataset collects tens of thousands of real classical Chinese poems and ci, and synthesizes a natural-language generation prompt for each one, so as to better train and evaluate models on the ability to "generate Chinese poetry from a given instruction."

Every sample is one complete multi-turn conversation: the system prompt asks for output in Chinese, the user describes the desired poem in natural language (form, subject, prosody, mood, scene, etc.), and the assistant replies with the original classical poem.

## Dataset Composition and Structure

### Files

The dataset ships as two files:

| File | Entries |
| --- | --- |
| `data/poetry_user_inputs.jsonl` | 8,163 |
| `data/poetry_100_1000_user_inputs.jsonl` | 27,125 |

That is 35,288 samples in total.

### Field Structure

Each line is a standalone JSON object (JSON Lines format):

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

- **`conversations`** — three turns: the system setting, the user instruction, and the original poem/ci returned by the assistant.
- **`language`** — the language tag of the prompt, given as an ISO 639-1 code (e.g. `ja`, `ru`). Chinese prompts have no such field.

### Language Distribution

The language composition of the user prompts in both files:

| Language | Code | `poetry_user_inputs` | `poetry_100_1000_user_inputs` |
| --- | --- | --- | --- |
| Chinese | (no field) | 5,893 | 19,542 |
| Japanese | `ja` | 164 | 545 |
| Polish | `pl` | 162 | 541 |
| Spanish | `es` | 162 | 541 |
| Arabic | `ar` | 162 | 546 |
| Russian | `ru` | 163 | 544 |
| Italian | `it` | 163 | 542 |
| German | `de` | 163 | 542 |
| Hindi | `hi` | 162 | 541 |
| French | `fr` | 163 | 545 |
| Vietnamese | `vi` | 163 | 543 |
| Indonesian | `id` | 164 | 543 |
| Portuguese | `pt` | 161 | 541 |
| Thai | `th` | 158 | 534 |
| Korean | `ko` | 160 | 535 |

15 languages in total: Chinese plus 14 others (no English).

### Data Samples

#### Chinese prompt

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家，请用中文输出。"},
    {"role": "user", "content": "帮我写一首五言绝句，主题是隐居山林、淡泊自在的感觉，最后两句提到以后去南岳会记得这座山的名字。"},
    {"role": "assistant", "content": "俞公巖\n\n萬事若在手，百年聊稱情。\n他時南嶽去，記得此巖名。"}
  ]
}
```

#### Non-Chinese prompt

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

## Synthesis

Poems and ci covering multiple sources and types were first scraped from [chinese-poetry/chinese-poetry](https://github.com/chinese-poetry/chinese-poetry) and cleaned,
then diverse user prompts were synthesized with an LLM based on each poem, covering form, subject, prosody, mood, scene, etc.  
Only poems with fewer than 1,000 Google search results were used, to avoid training a model on poems that already exist in its knowledge base.

## Usage

The data is released in JSON Lines format and can be read directly:

```python
import json

with open("data/poetry_user_inputs.jsonl", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            sample = json.loads(line)
            conversations = sample["conversations"]
            # conversations[1]["content"] is the multilingual prompt
            # conversations[2]["content"] is the Chinese poem (mixed simplified/traditional, as in the source data)
```

## Issues and Limitations

Assistant replies may mix simplified and traditional Chinese; careful evaluation is recommended before use.

Please report any other issues or suggestions.

## MiYago Datasets

[MiYago Datasets](https://huggingface.co/collections/Mikoris/miyago-datasets) is a dataset collection built specifically to improve the creative writing and roleplay capabilities of models.

## License

This dataset is licensed under the [Apache License 2.0](LICENSE).