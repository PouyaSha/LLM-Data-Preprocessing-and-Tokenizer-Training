
# LLM Data Preprocessing and Tokenizer Training 

This repository documents the process of cleaning and preparing a raw text dataset for training Large Language Models (LLMs) and demonstrates the training of three different subword tokenizers. The project uses a sample of the Persian subset of the Common Crawl (C4) dataset.

## 1. Data Exploration and Statistics 

The first step was to load the raw data and analyze its basic statistical properties. The initial dataset consisted of **52,663 documents**.

```text
--- Dataset Statistics ---
Number of documents: 52,663
Average document length: 504.17 words
Largest document length: 32,478 words
Smallest document length: 2 words
-------------------------
Total number of words: 26,550,903
Most frequent word: 'و' (appeared 1,026,606 times)


The wide range in document length (from 2 to over 32,000 words) immediately suggested the presence of low-quality or noisy data that needed to be filtered.


**2\. Data Filtering and Preprocessing Pipeline** 
--------------------------------------------------

To improve data quality, a multi-stage filtering pipeline was created. Each stage is a function designed to remove documents based on a specific heuristic.

### **Filtering Functions**

The pipeline consisted of the following six filters:

*   **Language Filter**: Removes any documents not identified as Persian (fa) using a pre-trained FastText model.
    
*   **Length Filter**: Removes documents that are excessively short or long, which are often not useful for training.
    
*   **Mean Word Length Filter**: Filters out documents where the average word length is outside a reasonable range, targeting spam or non-linguistic content.
    
*   **Symbol Ratio Filter**: Removes documents with a high ratio of symbols (e.g., #, @) to words.
    
*   **Alphabetic Filter**: Ensures that a high percentage of the words in a document contain at least one alphabetic character, filtering out data that is mostly numbers or punctuation.
    
*   **Stop Word Filter**: Removes documents that do not contain a minimum number of common stop words, which can be an indicator of unnatural text.
    

### **Hyperparameter Tuning** 

The goal was to tune the filter hyperparameters to retain approximately **60%** of the original data. This was an iterative process.

#### **Attempt 1: Initial Baseline**

With an initial set of weaker constraints, the pipeline retained over 80% of the data, which was too high.

**Hyperparameters:**
