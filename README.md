Hate Speech Detection Model Workflow

STEP 1: DATA COLLECTION

Collect datasets from various sources for multiple languages, ensuring diversity and representation of hate speech and non-hate speech examples.

STEP 2: TEXT PROCESSING

Noise Removal

Remove unwanted characters and artifacts from text that do not contribute to its meaning:

Examples: URLs, emojis, hashtags, mentions, special characters, punctuation, and non-textual elements.

Lowercasing

Convert all text to lowercase to ensure consistency and reduce vocabulary size.

Tokenization

Break sentences into smaller parts (tokens) that the model can understand. Tokens can be words, subwords, or characters.

Performed using: mBERT and XLM-RoBERTa.

Subword Tokenization:

If a word is in the vocabulary, it remains as-is.

If a word is not in the vocabulary, it is broken into smaller subwords.

mBERT vs. XLM-RoBERTa:

mBERT: Uses WordPiece tokenization.

Adds ## before subwords that follow the first part.

Example: "Multilingual hate speech" becomes ['Multi', '##lingual', 'hate', 'speech'].

XLM-RoBERTa: Uses SentencePiece tokenization.

Adds ▁ (underscore) before new words.

Example: "Multilingual hate speech" becomes ['▁Multi', 'lingual', '▁hate', '▁speech'].

Stopword Removal

Remove common words like "is," "and," or "the" that do not carry significant meaning.

Stemming

Cut off the ends of words to reduce them to their root form.

Examples:

"running," "runner," "ran" → "run."

"happiness," "happy" → "happi."

Lemmatization

Reduce words to their dictionary form (lemma) based on their grammatical context.

Examples:

"running" → "run" (verb).

"better" → "good" (comparative adjective).

"studies" → "study."

STEP 3: FEATURE EXTRACTION

Feature extraction involves turning words into numbers (vectors) that capture their meaning and relationships.

Word Embeddings

Represent words as vectors where similar words have similar numbers.

Example: "hate" and "anger" have similar vectors, while "love" and "peace" have their own group of similar vectors.

Embedding Models Used

mBERT (Multilingual BERT):

Supports 104 languages.

Uses 12 layers to process text.

Each word is represented as a vector with 768 dimensions.

Model size: 110 million parameters.

XLM-RoBERTa (Cross-Lingual RoBERTa):

Works with 100 languages and is optimized for cross-language tasks.

Two versions:

Base: 125 million parameters.

Large: 355 million parameters.

IndicBERT:

Lightweight model specifically designed for Indian languages like Hindi, Tamil, and Bengali.

Uses 12 million parameters.

Based on ALBERT (an efficient version of BERT).

MuRIL (Multilingual Representations for Indian Languages):

Focuses on Indian languages and code-mixed text (e.g., Hinglish).

Uses 12 layers and represents words with 768 dimensions.

Model size: 110 million parameters.

After extracting embeddings, the relationship between words in a sentence is understood to build context.

STEP 4: BiLSTM LAYER

The BiLSTM (Bidirectional Long Short-Term Memory) layer helps the model understand the context of each word by considering both the words that come before and after it in a sentence.

How It Works:

Reads Sentence in Two Directions:

Forward: From the start to the end of the sentence.

Backward: From the end back to the start.

Captures Full Context: Combines information from both directions to better understand each word’s meaning in the sentence.

Input and Output:

Input: Embeddings from models like mBERT or XLM-RoBERTa.

Output: Hidden states for each word that capture both its meaning and context.

These hidden states are passed to the next layer (e.g., attention or classification layer).

STEP 5: ATTENTION LAYER

The attention layer helps the model focus on the most important words in a sentence, assigning them more weight for better processing.

Why Use Attention?

Not all words are equally important in a sentence. For example:

In "I don’t like this awful behavior," words like "awful" and "behavior" are more important for detecting hate speech than "I" or "don’t."

How Attention Works:

Assign Importance: Calculates attention scores for each word.

Higher scores for important words (e.g., "awful," "hate").

Softmax Normalization: Converts scores into probabilities (attention weights) that sum to 1.

Weighted Sum: Combines the hidden states from the BiLSTM layer, giving more weight to important words.

Output:

Produces a context vector summarizing the important parts of the sentence.

Passes this vector to the classification layer to determine if the text contains hate speech.
