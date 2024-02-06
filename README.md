# Consistency in Query-based Meeting Summarization

 Our project is inspired by an article authored by **Microsoft**, *"QMSum: A New Benchmark for Query-based Multi-domain Meeting Summarization"*. This paper introduces a comprehensive dataset designed to train models to perform **query-based multi-domain meeting summarization**. The authors suggest several potential avenues for future work, particularly focusing on improving the **consistency** between the generated summaries and the corresponding queries, as determined by the reference summaries. Therefore, our primary objective in this project is to **enhance this consistency**.


## Microsoft Locator

Microsoft designed a locator which architecture is shown in the figure below. It has several parts, involving an embedding step with a Large Language Model (**BERT** in their case), a **CNN** to extract important information and features about the embeddings, and a layer of the Transformer architecture, among others.

--> picture Locator

## Dataset (QMSum)



## Our approach

Our objective is to enhance the consistency of the Locator by using different embedding models on the queries and utterances of the meetings. Among these models we try **RoBERTa**, **ELECTRA**, **ELMO** and **Longformer**.


|Size|BERT|ELECTRA|RoBERTa|Longformer|
|----|----|-------|-------|----------|
|Input|512|512|512|2048/4096|
|Embedding|768|256/768|768|768|
