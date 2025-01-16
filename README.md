# Hate Speech Detection Model Workflow

## **STEP 1: DATA COLLECTION**
- Collect datasets from various sources for multiple languages.
- Ensure diversity and representation of both hate speech and non-hate speech examples.

---

## **STEP 2: TEXT PROCESSING**

### **1. Noise Removal**
- Remove unwanted characters and artifacts from text that do not contribute to its meaning.
  - Examples: **URLs**, **emojis**, **hashtags**, **mentions**, **special characters**, **punctuation**, and **non-textual elements**.

### **2. Lowercasing**
- Convert all text to lowercase to ensure consistency and reduce vocabulary size.

### **3. Tokenization**
- Break sentences into smaller parts (**tokens**) that the model can understand.
  - **Tokens** can be:
    - Words
    - Subwords
    - Characters
- **Performed using:** `mBERT` and `XLM-RoBERTa`.

#### **Subword Tokenization**
- If a word is in the vocabulary, it remains as-is.
- If a word is not in the vocabulary, it is broken into smaller subwords.

#### **mBERT vs. XLM-RoBERTa**
- **mBERT**: Uses **WordPiece tokenization**.
  - Adds `##` before subwords that follow the first part.
  - Example: `"Multilingual hate speech"` → `['Multi', '##lingual', 'hate', 'speech']`.
- **XLM-RoBERTa**: Uses **SentencePiece tokenization**.
  - Adds `_` (underscore) before new words.
  - Example: `"Multilingual hate speech"` → `['_Multi', 'lingual', '_hate', '_speech']`.

### **4. Stopword Removal**
- Remove common words like `"is"`, `"and"`, or `"the"` that do not carry significant meaning.

### **5. Stemming**
- Cut off the ends of words to reduce them to their **root form**.
  - Examples:
    - `"running"`, `"runner"`, `"ran"` → `"run"`.
    - `"happiness"`, `"happy"` → `"happi"`.

### **6. Lemmatization**
- Reduce words to their **dictionary form (lemma)** based on their grammatical context.
  - Examples:
    - `"running"` → `"run"` (verb).
    - `"better"` → `"good"` (comparative adjective).
    - `"studies"` → `"study"`.

---

## **STEP 3: FEATURE EXTRACTION**

### **1. Word Embeddings**
- Represent words as **vectors** where similar words have similar numerical representations.
  - Example: `"hate"` and `"anger"` have similar vectors, while `"love"` and `"peace"` form their own group.

### **2. Embedding Models Used**
#### **mBERT (Multilingual BERT)**
- **Supports**: 104 languages.
- **Structure**: 12 layers.
- **Vector Dimensions**: 768.
- **Model Size**: 110 million parameters.

#### **XLM-RoBERTa (Cross-Lingual RoBERTa)**
- **Supports**: 100 languages.
- **Optimized for**: Cross-language tasks.
- **Versions**:
  - **Base**: 125 million parameters.
  - **Large**: 355 million parameters.

#### **IndicBERT**
- Lightweight model for **Indian languages** like Hindi, Tamil, and Bengali.
- **Structure**: 12 million parameters.
- **Based on**: ALBERT (an efficient version of BERT).

#### **MuRIL (Multilingual Representations for Indian Languages)**
- Focuses on **Indian languages** and **code-mixed text** (e.g., Hinglish).
- **Structure**: 12 layers.
- **Vector Dimensions**: 768.
- **Model Size**: 110 million parameters.

---

## **STEP 4: BiLSTM LAYER**

### **Role of BiLSTM**
- Helps the model understand the **context** of each word by considering both preceding and succeeding words.

### **How It Works**
- **Reads Sentence in Two Directions**:
  - **Forward**: From start to end of the sentence.
  - **Backward**: From end to start of the sentence.
- **Captures Full Context**: Combines information from both directions for better understanding.

### **Input and Output**
- **Input**: Embeddings from models like `mBERT` or `XLM-RoBERTa`.
- **Output**: Hidden states for each word that capture both meaning and context.

---

## **STEP 5: ATTENTION LAYER**

### **Purpose**
- Focus on the most important words in a sentence, assigning them more weight.

### **Why Use Attention?**
- Not all words are equally important in a sentence.
  - Example: In `"I don’t like this awful behavior"`, words like **"awful"** and **"behavior"** are more important for detecting hate speech than `"I"` or `"don’t"`.

### **How Attention Works**
1. **Assign Importance**:
   - Calculates attention scores for each word.
   - Higher scores for important words (e.g., `"awful"`, `"hate"`).
2. **Softmax Normalization**:
   - Converts scores into **probabilities** (attention weights) that sum to 1.
3. **Weighted Sum**:
   - Combines the hidden states from the BiLSTM layer, giving more weight to important words.

### **Output**
- Produces a **context vector** summarizing the important parts of the sentence.
- Passes this vector to the **classification layer** to determine if the text contains hate speech.

---
