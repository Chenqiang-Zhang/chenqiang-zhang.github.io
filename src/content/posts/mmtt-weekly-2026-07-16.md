---
title: "MMTT 周报 07：复现 Prompt-Tuning，以及它没有证明什么"
pubDatetime: 2026-07-16T21:00:00+09:00
description: "用 Qwen2.5 和 RealPersonaChat 复现 persona soft prompt：训练损失与 Distinct-2 改善，但这些指标仍不能证明对话更像人类。"
tags:
  - MMTT
  - Reproduction
  - Prompt Tuning
  - Prompt Engineering
---

> 本文整理自 2026 年 7 月 16 日周报。复现代码位于 [Prompt-Tuning](https://github.com/Chenqiang-Zhang/Prompt-Tuning) 仓库。它复现了论文方法，但不是对原论文数字的严格重复。

## 复现了什么

实验使用 Qwen2.5-0.5B 和 Qwen2.5-3B，冻结基础模型和 token embedding，只训练 200 个 soft persona token。数据使用公开的日语 RealPersonaChat 替代原论文数据，并将 persona-related dialogue 与 general dialogue 按 1:1 混合。

每个选定 persona 有 946 组训练样本和 52 组保留的人格评价样本，另有 150 组通用评价样本。损失只作用于回答 token，因此训练目标是：在给定上下文和 soft prompt 时预测下一条回复。

## 训练结果

![CP persona 的训练损失](/img/mmtt-weekly/2026-07-16/loss-curve.png)

0.5B 模型的 token-level cross-entropy 从 3.358 降到 2.303，3B 模型从 2.892 降到 1.652。两条曲线都说明模型学会了训练目标，3B 拟合得更好。

但这张图**不能说明对话更像人类**。它只说明 soft prompt 帮助模型预测训练数据中的下一个回答。模型可能通过复制常见表达、学习局部共现或记住数据模式降低损失，却仍然在真实多轮对话中表现僵硬。

## 生成指标

![保留集生成指标](/img/mmtt-weekly/2026-07-16/generation-metrics.png)

3B 在 persona 与 general 两个保留条件中的 character-level Distinct-2 都更高，说明相邻字符二元组的重复更少，局部表达更丰富。平均回复长度约 22 个日文字符，与参考回答接近；短回复对 MMTT 很重要，因为过长、过于完整的回答本身就是明显线索。

然而 Distinct-1/2 不测量事实正确性、长期人格一致性、对话意图，也不测量人类感。一个随机但不自然的模型也可能得到较高多样性。

## 定性观察

部分 3B 输出确实比 0.5B 更像短对话。例如面对「食べます。かき氷機も自前で持っています。」：

> 0.5B：かき氷機も自前で持っています。そちらは初めてです。  
> 3B：かき氷！ 夏はおいしいですよね。

0.5B 明显复制用户用词，3B 更像自然回应。但 3B 的回答仍未体现 persona。这再次说明“局部自然”与“人格稳定”不是同一个指标。

## 复现的主要限制

- 基础模型和数据集与论文不同，所以这是方法复现，不是结果复现；
- 目前只保存了一个 persona 的完整结果，无法估计跨 persona 的稳定性；
- 缺少原论文的众包人类评价；
- greedy decoding 与 MMTT 生产环境的采样式 API 不同；
- 短对话对上的训练损失不能代表五分钟多轮聊天。

## MMTT 的 Prompt 原则

MMTT 的目标不是不断调 prompt，把 AI 通过率刷到最高。更合理的做法是：从已有图灵测试和 persona research 中选择有出处的候选方案，在 Phase 2 前冻结文本与版本，再用 channel × prompt 实验观察它产生的人类感是否穿过 MT。

短期采用文本 prompt 的优势是便宜、适用于 closed API、可逐行审计，也能把 prompt 明确作为实验因素。代价是语言间迁移不稳定、容易形成新的固定口癖，也可能被注入攻击破坏。长规则清单和固定兜底回复尤其危险，因为它们会在不同会话中产生同一种异常模式。

因此下一步更适合采用：小而稳定的 persona、普通闲聊能力、按语言本地化的表层风格，以及少量不可改变的研究安全边界。训练方法可以作为未来比较，但不应让当前研究从“人类感位于语言哪一层”偏移成模型刷榜。

[查看复现仓库](https://github.com/Chenqiang-Zhang/Prompt-Tuning) · [下载本期原始 PDF](/reports/mmtt/2026-07-16.pdf)

