# PortfolioProjects
## Twitter Sentiment Analysis with NLP
This project addresses SemEval 2017 Task 4A to classify tweets into positive, neutral, or negative sentiment. It demonstrates how different machine learning and deep learning models perform when faced with real-world, informal Twitter data.

### Overview
Goal: Explore the impact of feature representations (TF-IDF vs. embeddings) and model choices (traditional vs. deep learning) on sentiment classification performance.

Dataset: SemEval 2017 Task 4A, comprising annotated tweets labeled by sentiment.

Key Techniques: Twitter-specific text cleaning (mentions, hashtags, URLs), dimensionality reduction, handling noisy labels, and embedding-based features.

Models Implemented
Support Vector Machine (SVM) with GloVe

Gaussian Naive Bayes with GloVe

Multinomial Naive Bayes with TF-IDF

Bidirectional LSTM with GloVe

Bidirectional LSTM with BERT

Bidirectional LSTM with BERTweet

Note: We also evaluate the impact of stopword removal on classifier performance.

Evaluation
Metrics: Accuracy, macro-F1, class-wise F1, and training time.

Results: Advanced models (LSTM + BERT/BERTweet) achieve the highest accuracy and F1 scores. Classical methods (Naive Bayes, SVM) train faster and may suffice for lightweight scenarios.

Key Finding: Retaining stopwords slightly improves certain classifier performances, showing that function words can carry subtle cues for sentiment.
