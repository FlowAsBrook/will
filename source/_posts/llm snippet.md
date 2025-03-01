---
title: llm snippet
date: 2024-01-10 15:03:53
tags: sinppet
categories: llm
---

# concept

## GPU

- 大模型往往含有大量的参数和复杂的神经网络结构，需要高度的并行处理能力和大的内存带宽来有效地训练和运行。GPU在这些方面的优势让它成为运行大型AI模型的首选硬件。但这不意味着CPU完全无法运行这些模型，只是相对来说CPU运行起来效率低，可能会非常慢，因此在实际应用中通常选择GPU来进行深度学习任务。

## Fine-tuning vs Embedding

- If you are trying to “teach” the model new information, embeddings is the way to go. If you want to change the structure or way it response, then use fine-tuning.

---

- **Fine-tuning:** Teach the model *how* to answer a question (e.g. structure/format, personality, etc)
- **Embedding:** Provide the model with new/specific information with which to answer questions.

---

# model

## DeepSeek-R1-Distill-Qwen-14B

The relationship between **DeepSeek** and **Qwen** is likely a collaborative or integrated one. **DeepSeek** might be the technology or framework for efficient search and information retrieval, while **Qwen-14B** is the core language model providing the understanding and generation capabilities. If these two are combined (as in **DeepSeek-R1-Distill-Qwen-14B**), it might indicate that DeepSeek is using the Qwen model (likely distilling it to improve efficiency) for specific tasks like searching, information retrieval, or knowledge extraction.

- **DeepSeek** enhances the **Qwen-14B** model (through distillation and integration) to make it more effective in specialized tasks like information retrieval or search.

- The **DeepSeek-R1-Distill-Qwen-14B** model would then represent a **customized, efficient version of Qwen-14B**, fine-tuned for certain tasks with the DeepSeek framework added in.
