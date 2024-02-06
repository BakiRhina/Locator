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

## Our approach

Our objective is to enhance the consistency of the Locator by using different embedding models on the queries and utterances of the meetings. Among these models we try **RoBERTa**, **ELECTRA**, **ELMO** and **Longformer**.


|Size|BERT|ELECTRA|RoBERTa|Longformer|
|----|----|-------|-------|----------|
|Input|512|512|512|2048/4096|
|Embedding|768|256/768|768|768|
