# Portfolio Projects

Welcome to my Data Science and NLP portfolio! Below is an overview of each project, along with links to more detailed documentation and code.

## Table of Contents
1. [Twitter Sentiment Analysis with NLP](#twitter-sentiment-analysis-with-nlp)
2. [COVID-19 Dashboard & Analysis](#covid-19-dashboard--analysis)
3. [Scraping & Data Wrangling](#scraping--data-wrangling)

## Twitter Sentiment Analysis with NLP
**Overview**:

This project explores sentiment classification on SemEval 2017 tweets, comparing traditional machine learning (SVM, Naive Bayes) with deep learning (LSTM) using GloVe, BERT, and BERTweet embeddings. Key experiments include handling Twitter-specific text, assessing stopword removal, and measuring performance via accuracy and F1 scores.

- **Path to code**: [`/Twitter_Sentiment_Analysis`](./Twitter_Sentiment_Analysis/)
- **Insights**:
  - Traditional models like MultinomialNB and SVM performed reasonably well with significantly shorter training times.
  - Stopword retention slightly improved SVM’s performance, suggesting syntactic cues contribute to sentiment signals.
  - Contextual embeddings (BERT, BERTweet) offered clear improvements in class-wise F1 scores, particularly on nuanced or informal text.

![Model Performance Comparison](./Twitter_Sentiment_Analysis/model_comparison_summary.png)

Read more in the [detailed documentation](./Twitter_Sentiment_Analysis/README.md).

