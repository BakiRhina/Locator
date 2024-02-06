# Consistency in Query-based Meeting Summarization

 Our project is inspired by an article authored by **Microsoft**, *"QMSum: A New Benchmark for Query-based Multi-domain Meeting Summarization"*. This paper introduces a comprehensive dataset designed to train models to perform **query-based multi-domain meeting summarization**. The authors suggest several potential avenues for future work, particularly focusing on improving the **consistency** between the generated summaries and the corresponding queries, as determined by the reference summaries. Therefore, our primary objective in this project is to **enhance this consistency**.


## Microsoft Locator

Microsoft designed a locator which architecture is shown in the figure below. It has several parts, involving an embedding step with a Large Language Model (**BERT** in their case), a **CNN** to extract important information and features about the embeddings, and a layer of the Transformer architecture, among others.

![image](https://github.com/BakiRhina/Locator/assets/108484177/3959f32d-b56c-453e-bc60-052efae6528f)


## Dataset (QMSum)

Microsoft built a complex and precise dataset to train its locator. This data can be found in https://github.com/Yale-LILY/QMSum/tree/main and consists of 1808 query-summary pairs over 232 meetings in multiple domains.

Of these 232 meetings, 162 are dedicated to the training set that will be used to train our Locator. The other meetings are reserved to the test and validation dataset, that will be used to evaluate the performance of our model, Locator and Summarizer.

To fully comprehend our experiment, we will delve into the details of how this dataset was constructed in this section. In the realm of query-based meeting summarization, very few benchmark datasets exist, even fewer are multi-domain. This unique aspect makes this dataset a particularly intriguing new benchmark for such applications.
  
After transcribing numerous meetings, annotators manually created general questions for each meeting, followed by their associated summaries. They repeated this process for more specific questions as well. Thus, for every meeting, we have both general and specific questions along with their associated summaries, which facilitated the training of several models subsequently.
  
The following diagram, extracted directly from the cited article, presents the process of creating this dataset:

![image](https://github.com/BakiRhina/Locator/assets/108484177/65a9f47d-5d09-42b5-86ff-bab692af1f5d)

To get a little bit more familiar with the dataset we have in front of us, a sample’s being shown below, where we can see the meeting 

```
{
    "topic_list": [
        {
            "topic": "Introduction of petitions and prioritization of governmental matters",
            "relevant_text_span": [["0","19"]]
        },

       [...]
    ],

    "general_query_list": [
        {
            "query": "Summarize the whole meeting.",
            "answer": "The meeting of the standing committee took place to discuss matters pertinent to the Coronavirus pandemic. The main issue at stake was to ..."
        },

        [...]

    ],
    "specific_query_list": [
        {
            "query": "Summarize the discussion about introduction of petitions and prioritization of government matters.",
            "answer": "The Chair brought the meeting to order, announcing that the purpose of the meeting was to discuss COVID-19 's impact on Canada. Five petitions were presented ...",
            "relevant_text_span": [["0","19"]]
        },

        [...]
    ],
    "meeting_transcripts": [
        {
            "speaker": "The Chair (Hon. Anthony Rota (NipissingTimiskaming, Lib.))",
            "content": "I call the meeting to order.  Welcome to the third meeting of the House of Commons Special Committee on the COVID-19 Pandemic ..."
        },

       [...]
```

## Our Locator

Our objective is to enhance the consistency of the Locator by using different embedding models on the queries and utterances of the meetings. Among these models we try **RoBERTa**, **ELECTRA**, **ELMO** and **Longformer**.


|Size|BERT|ELECTRA|RoBERTa|Longformer|
|----|----|-------|-------|----------|
|Input|512|512|512|2048/4096|
|Embedding|768|256/768|768|768|

These language models will be used to embed the queries and the utterances in the QMSum dataset to train the locator, firstly, and to predict the relevant spans when deploying it. Below there is the architecture that we designed taking Microsoft's paper as a reference.

![image](https://github.com/BakiRhina/Locator/assets/108484177/ef68d4f4-8a6e-4e4f-8659-58996fb8b535)

## Summarization

The second part of the model's architecture pertains to the summarizer. In the original paper, they utilized several models capable of summarizing texts, namely BART, PGNet, and HMNet. For our experiment, we decided to utilize only BART and a version of BART fine-tuned on meeting summaries found on Hugging Face (which we will refer to as BART_MEETING). Here is the link to this model: https://huggingface.co/knkarthick/MEETING_SUMMARY .

Below we show the experimental flow of the whole system, containing the embeddings, Locator and Summarizer.

![image](https://github.com/BakiRhina/Locator/assets/108484177/b1dcef46-99a8-4ce8-9d0e-b44684dc4241)

## Evaluation

We opted to use the same metric as in the Microsoft paper, specifically, the **ROUGE** measure. These metrics provide a quantitative assessment of summary quality and help compare different summarization approaches. For our experiment, we will focus exclusively on R-1, R-2, and R-L measures.

The following table presents the ROUGE scores for all the locators with summarizers. Also, we provide the performance from (Zhong et al., 2021) at the top of the table. However, the prior performance cannot be strictly compared because it did not provide the original locator model, which means we have to build the locator model by ourselves and some differences in detail will be inevitable. According to the results obtained, the best score is for the MEETING_SUMMARIZE models and the gold spans, with an R-L of 33.1 for this model. This surpasses the best model from Microsoft (using gold spans), which scored 31.27. However, the scores with our locators are relatively modest.
An asterisk (*) indicates that the model has been fine-tuned on the QMSum dataset. On the right side of the table, we can observe the impact that fine-tuning has had on our models' performance. For example, using fine-tuning to generate summaries from the gold spans has, on average, improved the ROUGE scores by 80%.

As for ROBERTA, BERT, and ELECTRA, fine-tuning has respectively improved the scores by 50%, 71%, and 62%. Furthermore, we notice that the performance of the summaries on the relevant spans extracted by our three locators is far less consistent than on the gold spans (based on ROUGE scores). Indeed, even with the fine-tuned models, our summaries achieve an average ROUGE-L of 16.2, whereas with the gold spans, they achieve an average of 26.5, which is roughly 39% less.

Despite the overall low performance of the three locators, the one using ROBERTA yielded slightly superior scores.

![image](https://github.com/BakiRhina/Locator/assets/108484177/c5bbf4fd-e533-40ea-a47a-9b3e09b788d9)

## Conclusion

There is no doubt that this dataset is comprehensive and highly effective for such a task. Our initial goal was to improve the consistency of our summaries, and based on the ROUGE scores, we have seen a slight enhancement in our summarizer's performance. Indeed, for all our summarizers (used with gold spans), we obtain an average ROUGE-L score of 26.46, with a clear difference between the non-fine-tuned models and those fine-tuned on the QMSum datasets. Specifically, the fine-tuned models show an average improvement (all ROUGE scores combined, all locators combined) of 65.7%.

As for the performance of our locators, we have an average ROUGE-L score for locators using ROBERTA, BERT, and ELECTRA, which are respectively 16.65, 15.83, and 16.1. Given that the original paper stated the average ROUGE-L when using a random locator is 11.76, this demonstrates the comparatively low performance of our locators.
