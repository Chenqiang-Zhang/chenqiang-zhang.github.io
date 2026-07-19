---
title: "MMTT 周报 06：Prompt-Tuning 如何学习 Persona"
pubDatetime: 2026-07-09T21:00:00+09:00
description: "阅读 Kasahara 等人的日英双语 persona dialogue 研究：冻结语言模型，只训练 persona soft prompt，并混合人格对话与普通对话。"
tags:
  - MMTT
  - Prompt Tuning
  - Persona Dialogue
  - Paper Reading
---

> 本文整理自 2026 年 7 月 9 日周报，主要介绍 Kasahara 等人在 NAACL 2022 Student Research Workshop 发表的 *Building a Personalized Dialogue System with Prompt-Tuning*。

## 论文想解决什么问题

通用对话模型容易给出流畅但缺乏个性的回答，也可能在多轮交流中产生矛盾。直接 fine-tuning 整个模型成本较高，因此论文尝试用 prompt-tuning 让模型获得稳定 persona。

它与普通文本 prompt 不同：这里训练的不是可读文字，而是一组连续的 soft prompt embedding。预训练模型本体保持冻结，只更新 persona 对应的小型向量参数。

![Prompt-tuning 的模型结构](/img/mmtt-weekly/2026-07-09/architecture.png)

论文使用长度为 200 的 persona token，并用 persona sentences 初始化。训练数据同时包含两类对话：

- 与 persona 直接相关的对话，用于学习身份和偏好；
- 与 persona 无关的普通对话，用于保留自然闲聊能力。

这个混合策略很重要。如果所有回答都强行提到 persona，角色会显得刻意；如果只有普通闲聊，人格又不会稳定出现。

## 日语实验与评价

论文分别进行了英语和日语实验。日语部分使用 JPersonaChat 与 JEmpatheticDialogues，模型包括 GPT2-XL 和 HyperCLOVA。每个 persona 只需要数百组训练对话。

评价分为两部分：

- 自动评价使用 Distinct-1/2 衡量输出多样性；
- 人工评价通过众包，让多名标注者评价流畅性、参与感、相关性和 persona 一致性。

![众包评价界面](/img/mmtt-weekly/2026-07-09/crowdsourcing.png)

较大的 prompt-tuned 模型在人工评价中产生了更自然、更符合 persona 的回答。不过 Distinct-N 只说明局部表达是否重复，不能单独证明角色一致，也不能直接证明“像真人”。

## 对 MMTT 的启发

这篇论文带来三个直接启发：

1. Persona 与普通闲聊应该共存，而不是每句话都展示人设；
2. 除了最终身份判断，还应评价流畅性、相关性和重复程度；
3. 更稳定的人格不一定需要全量训练，PEFT 可以降低成本。

但它不能直接替代当前 MMTT 的 closed API Witness。Prompt-tuning 需要访问模型 embedding 和训练过程，而商业 API 通常只允许文本提示。短期仍应优先做好可审计的文本 prompt；中期如果自托管开源模型，可以比较 prompt-tuning、LoRA、QLoRA 和 adapter。

[论文页面](https://aclanthology.org/2022.naacl-srw.13/) · [下载本期原始 PDF](/reports/mmtt/2026-07-09.pdf)

