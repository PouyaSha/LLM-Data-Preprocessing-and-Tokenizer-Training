LLM Data Preprocessing and Tokenizer Training 🚀
================================================

This repository documents the process of cleaning a raw text dataset for training Large Language Models (LLMs) and demonstrates the training of three different subword tokenizers. The project uses a sample of the Persian subset of the Common Crawl (C4) dataset to showcase the importance of a high-quality data pipeline.

1\. Data Exploration and Statistics 📊
--------------------------------------

The first step was to load the raw data and analyze its basic properties. The initial dataset consisted of **52,663 documents**.

Plaintext

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   --- Dataset Statistics ---  Number of documents: 52,663  Average document length: 504.17 words  Largest document length: 32,478 words  Smallest document length: 2 words  -------------------------  Total number of words: 26,550,903  Most frequent word: 'و' (appeared 1,026,606 times)   `

The wide range in document length—from just 2 words to over 32,000—immediately suggested the presence of low-quality or noisy data that needed to be filtered.

2\. Data Filtering and Preprocessing Pipeline 🧹
------------------------------------------------

To improve data quality, a multi-stage filtering pipeline was created. Each stage is a function designed to remove documents based on a specific heuristic.

### Filtering Functions

The pipeline consisted of the following six filters:

*   **Language Filter**: Removes any documents not identified as Persian (fa) using a pre-trained FastText model.
    
*   **Length Filter**: Removes documents that are excessively short or long.
    
*   **Mean Word Length Filter**: Filters out documents where the average word length is outside a reasonable range, targeting spam or non-linguistic content.
    
*   **Symbol Ratio Filter**: Removes documents with a high ratio of symbols (e.g., #, @) to words.
    
*   **Alphabetic Filter**: Ensures a high percentage of words in a document contain at least one alphabetic character.
    
*   **Stop Word Filter**: Removes documents that do not contain a minimum number of common stop words, which can be an indicator of unnatural text.
    

### Hyperparameter Tuning 🎯

The goal was to tune the filter hyperparameters to retain approximately **60%** of the original data. This was an iterative process.

#### Attempt 1: Initial Baseline

With an initial set of reasonable constraints, the pipeline retained over 80% of the data, which was too high.

**Result:**

Plaintext

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Total number of raw data: 52663  98.73% of raw data remained after language filtering  85.32% of raw data remained after length filtering  85.23% of raw data remained after mean word length filtering  84.85% of raw data remained after symbol ratio filtering  81.94% of raw data remained after alphabet filtering  81.66% of raw data remained after stop word filtering   `

#### Attempt 2: Final Tuned Parameters

After analyzing the baseline, the hyperparameters were made significantly stricter, especially the length and alphabetic filters.

**Final Hyperparameters:**

Python

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   length_filter_min = 75  length_filter_max = 10000  word_length_filter_min = 3  word_length_filter_max = 8  symbol_filter_max_ratio = 0.07  alphabetic_filter_min_ratio = 0.9  stop_words_filter_min_count = 5   `

**Final Result:** This successfully brought the data retention down to the target of **~58%**.

Plaintext

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Total number of raw data: 52663  ...  58.03% of raw data remained after alphabet filtering  57.93% of raw data remained after stop word filtering   `

The final filtered and normalized dataset was saved to train.txt.

3\. Tokenizer Training
----------------------

Three different subword tokenization algorithms were trained. For each algorithm, two tokenizers were created:

1.  One trained on the **clean, filtered data** (train.txt).
    
2.  One trained on the **original, raw data** (raw\_text.txt) for comparison.
    

All tokenizers were trained with a target vocabulary size of **25,000**.

### WordPiece

The algorithm used by models like BERT. It greedily builds a vocabulary by merging tokens that maximize the likelihood of the training data. Subwords are marked with a ## prefix.

### BPE (Byte-Pair Encoding)

The algorithm used by models like GPT. It greedily merges the most _frequent_ pair of tokens. This project used a ByteLevel implementation, which works on raw bytes, allowing it to tokenize any text without "unknown" tokens.

### Unigram

A probabilistic model used by T5. It starts with a very large vocabulary and prunes away the least useful tokens. This allows it to produce multiple valid tokenizations for the same text, which can improve model robustness.

4\. Analysis and Comparison
---------------------------

The final step was to analyze the behavior of the different tokenizers.

### The Impact of Data Quality

Across all three algorithms, the tokenizers trained on the **clean data** produced more logical and linguistically coherent subwords. The tokenizers trained on **raw data** had vocabularies "polluted" with junk tokens and often broke down common words in strange ways. This highlights the critical importance of a high-quality preprocessing pipeline.

### Vocabulary Intersection Analysis

The vocabularies of the **WordPiece** and **Unigram** tokenizers (both trained on clean data) were compared.

Plaintext

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   Number of unique tokens generated by unigram: 24,989  Number of unique tokens generated by wordpiece: 24,380  Number of intersection tokens: 3,270   `

The analysis revealed that the intersection was **extremely small (only ~13%)**. This is due to their fundamentally different learning algorithms (greedy merging vs. probabilistic pruning) and pre-tokenization methods (WhitespaceSplit vs. Metaspace). This result powerfully demonstrates that there is no single "correct" vocabulary for a language; it is a direct artifact of the chosen tokenization algorithm.

Acknowledgements
----------------

This project was completed as part of the "Large Language Models" course homework offered by Dr. Rohban et. al. at Sharif University of Technology.
