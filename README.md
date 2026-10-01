# PromptChinesePoetry

![MiYago LOGO.png](image/MiYago%20LOGO.png)

## 介绍

快速发展的大语言模型（LLM）在诗歌生成领域存在广泛应用，但是利用已有模型为诗歌生成任务合成训练数据可能会导致多样性退化或引入已有模型的偏见。

鉴于此，本数据集收集了上万首真实的中文古典诗词，并为每首诗/词合成了自然语言生成 prompt，以更好地训练和评估模型"根据指定指令生成中文诗词"的能力。

每条样本都是一次完整的多轮对话：系统提示要求以繁体中文作答，用户用自然语言描述想要的诗词（体裁、题材、韵律、情绪、场景等），助手回复对应的古诗原文。

## 数据组成和结构

### 组成

本数据集包含两个文件：

| 文件 | 条目数 | 说明 |
| --- | --- | --- |
| `data/poetry_user_inputs.jsonl` | 8,187 |
| `data/poetry_100_1000_user_inputs.jsonl` | 27,441 |

合计约 35,000 条样本。

### 字段结构

每行是一个独立的 JSON 对象（JSON Lines 格式）：

```json
{
  "conversations": [
    {
      "role": "system",
      "content": "你是一个作家，请以繁体中文输出。"
    },
    {
      "role": "user",
      "content": "想让你模仿温庭筠的风格写一首《杨柳枝》，三四句就行，写春景里黄鹂在柳枝上叫的意象，带点闺怨或者思远的情绪"
    },
    {
      "role": "assistant",
      "content": "杨柳枝·两两黄鹂色似金\n\n两两黄鹂色似金，袅枝啼露动芳音。\n春来幸自长如线，可惜牵缠荡子心。"
    }
  ],
  "language": "ru"
}
```

- **`conversations`**：三轮对话，分别为 system 设定、user 指令、assistant 生成的诗/词原文。
- **`language`**：prompt 的语言标签，取值为 ISO 639-1 语言代码（如 `en`、`ja`、`ru`）。

### 语言分布

两个文件的用户 prompt 的语言构成比例：

| 语言 | 代码 | `poetry_user_inputs` | `poetry_100_1000_user_inputs` |
| --- | --- | --- | --- |
| 中文 | （无字段） | 5,905 | 19,773 |
| 日语 | `ja` | 165 | 553 |
| 波兰语 | `pl` | 164 | 549 |
| 西班牙语 | `es` | 164 | 549 |
| 阿拉伯语 | `ar` | 164 | 549 |
| 俄语 | `ru` | 164 | 549 |
| 意大利语 | `it` | 164 | 549 |
| 德国语 | `de` | 163 | 546 |
| 印地语 | `hi` | 163 | 545 |
| 法语 | `fr` | 163 | 549 |
| 越南语 | `vi` | 163 | 549 |
| 印尼语 | `id` | 163 | 547 |
| 葡萄牙语 | `pt` | 162 | 549 |
| 泰语 | `th` | 159 | 541 |
| 韩语 | `ko` | 159 | 542 |
| 英语 | `en` | 163 | 549 |


### 数据样例

#### 中文 prompt

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家，请以繁体中文输出。"},
    {"role": "user", "content": "帮我写一首五言绝句，主题是隐居山林、淡泊自在的感觉，最后两句提到以后去南岳会记得这座山的名字。"},
    {"role": "assistant", "content": "俞公巖\n\n萬事若在手，百年聊稱情。\n他時南嶽去，記得此巖名。"}
  ]
}
```

#### 非中文 prompt

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家，请以繁体中文输出。"},
    {"role": "user", "content": "Напиши, пожалуйста, китайскую词 (цы) на мелодию 酒泉子 в стиле Вэнь Тина Юня. Тема — весеннее одиночество тоскующей женщины в покоях: цветущие ивы у пруда с зелёной ряской, дождь, тоска по возлюбленному, пустая оппочинальня ночью. Длина — стандартная для этого ципая, два пиньцзэ примерно по 40 с лишним знаков."},
    {"role": "assistant", "content": "酒泉子·花映柳条\n\n花映柳条，闲向绿萍池上。\n凭栏干，窥细浪，雨萧萧。\n近来音信两疏索，洞房空寂寞。\n掩银屏，垂翠箔，度春宵。"}
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
            # conversations[2]["content"] 为繁体中文诗词
```

## MiYago Datasets

[MiYago Datasets](https://huggingface.co/collections/Mikoris/miyago-datasets)是一个专为提升模型创意写作和角色扮演能力而生的数据集 Collection。

## 许可协议

本数据集采用 [Apache License 2.0](LICENSE) 许可。