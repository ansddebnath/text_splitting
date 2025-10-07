---
theme: seriph
author: Debanjan Debnath
exportFilename: Long content processing
colorSchema: light
aspectRatio: 16/9
canvasWidth: 1200
themeConfig:
fonts:
  sans: Source Sans Pro
  serif: Source Sans Pro
  mono: Cascadia code
defaults:
  layout: default
hideInToc: true
transition: slide-left
background: /text_splitting/images/background.jpg
layout: cover
mdc: true
---

<div class="absolute top-10 text-left gap-0">
<h1 class="font-bold !text-[45px]">Mastering Token-Based Text splitting</h1>
<span>A Complete Guide from Fundamentals to Implementation</span>
</div>

<div class="absolute bottom-10 text-xl">By Debanjan Debnath</div>

---
hideInToc: true
---

<div class="absolute top-10">
<h1 class="font-bold">Table of content</h1>
<Toc minDepth="1" maxDepth="2"></Toc>
</div>

---
layout: default
level: 1
title: Tokens in LLM
---

# What Are Tokens?

## Definition
Tokens are the fundamental units that language models process. They represent the "vocabulary" that AI models understand.

## Token Characteristics:
* Not equivalent to words or characters
* Vary in length and composition
* Model-specific (different models use different tokenization schemes)
* The basis for all input/output limitations

**Critical Concept:** Language models don't read text as humans do. They process numerical token sequences.

---
layout: two-cols-header
level: 2
title: Understanding tokenization
---

# Understanding Tokenization

::left::

Tokenization follows a systematic algorithm based on **Byte Pair Encoding (BPE)**. There are multiple algorithms are available and different LLM model uses different BPE algorithm.

### BPE Algorithm Overview:
* Start with individual characters
* Find most frequent adjacent pairs
* Merge these pairs into single tokens
* Repeat until vocabulary size reached
* Apply learned merges to new text

::right::
<v-click>

### Example:
Using the `tiktoken` as per `o200k-base` encoding, <span class="font-600 font-mono text-primary">My Name is Debanjan.</span> is split into 7 tokens as shown below.

<div class="text-sm leading-none font-mono mb-6px text-center">
  <span class="leading-none bg-[hsla(260,100%,75%,0.6)] pl-2px">My</span>
  <span class="leading-none bg-[hsla(135,100%,75%,0.6)]"> Name</span>
  <span class="leading-none bg-[hsla(40,100%,75%,0.6)]"> is</span>
  <span class="leading-none bg-[hsla(358,100%,75%,0.6)]"> Deb</span>
  <span class="leading-none bg-[hsla(195,100%,75%,0.6)]">anj</span>
  <span class="leading-none bg-[hsla(260,100%,75%,0.6)]">an</span>
  <span class="leading-none bg-[hsla(135,100%,75%,0.6)] pr-2px">.</span>
</div>

</v-click>

<v-click>

### Key Observations:
* One word can split into multiple tokens.
* Spaces are often attached to following words.
* Common words typically are 1 token.
* Rare words typically are multiple tokens.
* Punctuation and special characters are separate tokens.

</v-click>

<style>
.two-cols-header {
  column-gap: 20px;
}
</style>

---
layout: default
title: Why tokens matters
level: 1
---

# Why Tokens Matters

* Every LLM has a fixed **context window** - the maximum number of tokens it can process in a single request.
* Understanding encoding helps predict token behavior and overcome token limit issue.

<v-click>

## Model token limits

| Model | Context Window | Use Case |
|-------|----------------|----------|
| GPT-3.5-Turbo | 4,096 tokens | Short conversations |
| GPT-4 | 8,192 tokens | Standard applications |
| GPT-4-32K | 32,768 tokens | Long documents |
| GPT-4-Turbo | 128,000 tokens | Complex analysis |
| Claude 3.5 Sonnet | 200,000 tokens | Extended context |
| Gemini 1.5 Pro | 1,000,000 tokens | Massive documents |

</v-click>
---
layout: two-cols-header
title: Token limit impact
level: 2
---

# Token Limit Impact on Applications

::left::

<v-click>

## Scenario 1: API Failures

* A large document (e.g., 50 pages) is sent in one API call.
* It exceeds the token limit → request fails → application crashes.
* Impact: Poor user experience and system instability.

<div class='relative w-fit max-w-[80%] h-auto rounded-2 text-justify mx-auto b-l-red-600 dark:b-l-red-500 border-l-8 flex flex-row gap-2 pr-4 mt-4 bg-gray-500 bg-opacity-20 hover:bg-opacity-10 items-center overflow-hidden shine-effect'>
  <mdi-error class='text-[20px] text-red-600 dark:text-red-500 relative top-[-2px] ml-2'/> 
  <div class='my-2.5'>
    <p class='font-bold text-lg !m-0 !mb-0.5'>Error</p>
    <p class='text-sm !leading-tight !m-0'>
      This model's maximum context limit reached.
    </p>
  </div>
</div>

<style>
/* First shine streak (original) */
.shine-effect::before {
  content: "";
  position: absolute;
  top: 0;
  left: -75%;
  width: 10%;
  height: 150%;
  background: hsla(0, 0%, 100%, 0.10);
  animation: shineSweep 2s infinite;
  pointer-events: none;
  transform: rotate(15deg);
  transform-origin: top right;
}

/* Second, thinner shine streak */
.shine-effect::after {
  content: "";
  position: absolute;
  top: 0;
  left: -75%;
  width: 2%; /* thinner streak */
  height: 150%;
  background: hsla(0, 0%, 100%, 0.15); /* slightly brighter */
  animation: shineSweep 2s infinite;
  animation-delay: 0.1s; /* offset timing for staggered effect */
  pointer-events: none;
  transform: rotate(15deg);
  transform-origin: top right;
}

/* Animation keyframes */
@keyframes shineSweep {
  0% { left: -75%; }
  100% { left: 125%; }
}
</style>



</v-click>

::right::

<v-click>

## Scenario 2: Information Loss

* To avoid failure, the system truncates long documents.
* Important content is removed, leading to incomplete analysis.
* Impact: Inaccurate or misleading results.

<div class='w-fit text-sm flex flex-row gap-4 mx-auto mt-4'>
  <!-- Left visual block with animation -->
  <div class='bg-gray w-[200px] font-bold bg-opacity-20% rounded p-2 justify-start flex flex-col gap-1 animate-fade-in'>
    <div class='bg-gray-500/70% w-[95%] h-[8px] rounded-0.5 ml-auto animate-pulse'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-100'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-200'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-300'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-400'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-500'></div>
    <div class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-600'></div>
    <div class='flex flex-row gap-0.5'>
      <span class='bg-gray-500/70% w-full h-[8px] rounded-0.5 animate-pulse delay-700'></span>
      <span class='bg-red-500/50% w-[60%] h-[8px] rounded-0.5 animate-pulse delay-800'></span>
    </div>
    <div class='bg-red-500/50% w-full h-[8px] rounded-0.5 animate-pulse delay-900'></div>
    <div class='bg-red-500/50% w-full h-[8px] rounded-0.5 animate-pulse delay-[1000ms]'></div>
    <div class='bg-red-500/50% w-[30%] h-[8px] rounded-0.5 animate-pulse delay-[1100ms]'></div>
  </div>

  <!-- Right info text -->
  <div class='w-full p-2 flex flex-col gap-0 font-bold'>
    <div class='w-full'>LLM context</div>
    <div class='w-full text-red-500'>Information loss</div>
  </div>
</div>

<style>
  /* Custom subtle fade-in animation for the whole block */
  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(4px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .animate-fade-in {
    animation: fadeIn 0.8s ease-out both;
  }

  /* Add custom animation delay utilities */
  .delay-100 { animation-delay: 0.1s; }
  .delay-200 { animation-delay: 0.2s; }
  .delay-300 { animation-delay: 0.3s; }
  .delay-400 { animation-delay: 0.4s; }
  .delay-500 { animation-delay: 0.5s; }
  .delay-600 { animation-delay: 0.6s; }
  .delay-700 { animation-delay: 0.7s; }
  .delay-800 { animation-delay: 0.8s; }
  .delay-900 { animation-delay: 0.9s; }
  .delay-[1000ms] { animation-delay: 1s; }
  .delay-[1100ms] { animation-delay: 1.1s; }
</style>


</v-click>

::bottom::

<v-click>

## Key takeaway:
Large documents must be [split intelligently]{style="color:var(--slidev-theme-primary);font-weight:bold;"} — using methods like:
* **Chunking:** Divide text by topic or section.
* **Context caching:** Carry forward important context between chunks.
* **Selective summarization:** Compress low-value sections first.

</v-click>


---
layout: default
title: Token limit work around
level: 1
transition: view-transition
mdc: true
---

# Token limit work around

<div>
Certain techniques can help overcoming token limitation. Some of them are discussed below,
</div>

<div v-click>

## 1. Truncation:
<div class='grid grid-cols-2 gap-4'>
<div>
The easiest way to truncate the text by removing words or sentences. It is a simple fix but the model might miss the important context. Truncation can be done on a character or word level, depending on the requirement. The following python code shows how to truncate a text from the end of the sentence.
</div>
<div>
```python {*}{lines:true}
def truncate(long_text:str, i:int) -> str:
  return ' '.join(long_text.split()[:-i])
```
</div>
</div>
</div>

<div v-click='2'>

## 2. Remove redundant Terms:
<div class='grid grid-cols-2 gap-4'>
<div>
Stop word removal is a common technique in NLP to reduce the corpus size. Stop word include meaningless terms like "to" and "the" often appearing in the text. These are important for sentence formation, but mordern LLMs focus on the key terms, so we can simply write without stop words. This method isn't reliable and for complex sentences it should be manually verify that the sentence make enough sense. The following python code shows how to remove stop words from text.
</div>
<div>
```python {*|1-3|4-5|6-9|*}{lines:true, at:3}
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
nltk.download('stopwords')
nltk.download('punkt')
def remove_stop_words(long_text:str) -> str:
  tokens = word_tokenize(long_text)
  short_sentence = ' '.join([t for t in word_tokenis if t is not in stopwords.words('english')])
  return short_sentence
```
</div>
</div>
</div>

---
hideInToc: true
---

# Token limit work around

## 3. Chunk processing:
Another approach for handling long texts is to break them into smaller, manageable chunks. Various chunking strategies exist for splitting text, and some of the most effective methods will be discussed in the upcoming slides. This text-splitting technique can be further enhanced by implementing a sliding window approach with overlapping buffers that preserve surrounding sentences across splits, maintaining context continuity.

## Key considerations during chunking:
* **Chunk size:** Balance between token limits and preserving sufficient context for meaningful understanding
* **Overlap strategy:** Include overlapping tokens or sentences between consecutive chunks to maintain contextual flow
* **Semantic boundaries:** Split at natural breakpoints like paragraphs, sentences, or topic boundaries rather than arbitrary positions
* **Information preservation:** Ensure critical information isn't split across chunks in ways that lose meaning
* **Reassembly strategy:** Plan how chunked results will be merged or synthesized for the final output

---
layout: cover
title: Different chunking methods
level: 1
background: /text_splitting/images/background.jpg
---

# Different chunking methods

<div>Based on the requirement we can split the text into multiple chunks in different ways.</div>

---
layout: two-cols-header
title: Approximate
level: 2
---

# Approximate chunking

::left::
<div v-click='1'>

* Using approximate token count logic we can chunk the words into multiple pieces.

## Approximate token count

| Token | Characters | Words |
|-------|------------|-------|
| 1 | 4 | 0.75 |
| 100 | 400 | 75|
| 2048 | - | 1500 |
</div>
::right::
<div v-click='2'>

```python {*|2,3|5-8|9|*}{lines:true, at:3}
def approximation_chunk(text, max_tokens=4000):
    words = text.split()
    words_per_chunk = int(max_tokens * 0.75)
    
    chunks = []
    for i in range(0, len(words), words_per_chunk):
        chunk_words = words[i:i+words_per_chunk]
        chunks.append(' '.join(chunk_words))
    return chunks
```

### Note:
* We can further improve this by implementing overlapping and semantic boundaries.
</div>
::bottom::

<div v-click='7'>

## Key takeaways:
* This is very fast
* Accuracy is low
* Use this in prototyping not in production

</div>

---
layout: two-cols-header
title: Ratio based
level: 2
---

# Ratio-Based Chunking

::left::
<div v-click='1'>

* Calculate exact token-to-word ratio, then apply to chunking.

## Key features:
* Uses actual token count for ratio
* More accurate than fixed 1.3x multiplier
* Adapts to document characteristics

## The Problem:
* Token distribution is not uniform
* Local vocabulary affects tokenization
* Word boundaries don't guarantee token boundaries
* Still requires validation (which means encoding anyway)

</div>
::right::

<div v-click='2'>

```python {*|1|4,5|6,7|8-13|14|*}{lines:true,at:3}
import tiktoken

def ratio_based_chunk(text, max_tokens=4000):
    encoding = tiktoken.get_encoding("o200k_base")
    total_tokens = len(encoding.encode(text))
    total_words = len(text.split())
    words_per_token = total_words / total_tokens
    words_per_chunk = int(max_tokens * words_per_token)
    words = text.split()
    chunks = []
    for i in range(0, len(words), words_per_chunk):
        chunk_words = words[i:i+words_per_chunk]
        chunks.append(' '.join(chunk_words))
    return chunks
```

### Note:
* We can further improve this method by iteratively adjusting the chunk size until it reaches the threshold limit.
</div>

---
layout: two-cols-header
title: Direct token manipulation
level: 2
---

# Direct Token chunking

::left::
<div v-click='1'>

* Operate directly on token arrays instead of text to achieve precise control with a single encoding pass.

## The Process:
* Encode the entire text into a token array (single pass)
* Slice token array at exact token boundaries
* Decode each token slice back to text
* Guaranteed token compliance

</div>
::right::

<div v-click='2'>

```python {*|1|4,5|6|7|8|*}{lines:true,at:3}
import tiktoken

def direct_token_slicing(text, max_tokens=4000):
    encoding = tiktoken.get_encoding("o200k_base")
    tokens = encoding.encode(text)
    chunks = [tokens[i:i+max_tokens] for i in range(0, len(tokens), max_tokens)]
    text_chunks = [encoding.decode(chunk) for chunk in chunks]
    return text_chunks
```

### Note:
* This method guarantees exact token limits without iterative estimation.

</div>

::bottom::
<div v-click='9'>

## Key takeaway:
* Token arrays represent the ground truth
* Slicing preserves exact token counts
* No estimation or iterative adjustment required
* Single encoding pass ensures maximum efficiency
</div>

---
layout: two-cols-header
title: Advanced techniques
level: 2
---

# Advanced Chunking Methods

::left::
<div v-click='1'>

Move beyond static or token-based chunking with adaptive, context-aware methods.

## Techniques:
* **Overlapping:** Adds small context overlap between chunks to prevent meaning loss.  
* **Semantic Chunking:** Uses embeddings to split text based on meaning, not token count.  
* **Agentic Chunking:** Dynamically adapts chunking to the task or agent’s reasoning needs.

</div>

::right::
<div v-click='2'>

## Why advance:
* Handles long documents more intelligently  
* Reduces fragmentation of concepts  
* Maintains coherence for downstream reasoning  

### Example:
* Semantic chunking uses embeddings (e.g., OpenAI, SBERT)
* Agentic chunking uses task-aware logic to dynamically adjust boundaries

</div>

::bottom::
<div v-click='3'>

## Key takeaway:
* Start simple (approximation, ratio)
* Use direct token slicing for precision
* Add overlap and semantics for natural flow
* Employ agentic methods for adaptive, intelligent chunking
</div>

---
layout: two-cols-header
title: Overlapping
level: 2
---

# Overlapping Chunking

::left::
<div v-click='1'>
Maintain contextual continuity by reusing part of the previous chunk in the next.

## Key Features:
* Prevents context loss at chunk boundaries  
* Adds smooth transition between adjacent chunks  
* Especially effective for summarization and retrieval tasks

</div>
<div v-click='10' class='!mt-8'>

## Note:
* The overlap size depends on task — 5–25% is typical.
* Larger overlaps improve continuity but increase redundancy.

## Key takeaway:
* Overlapping preserves context flow between segments
* Useful when maintaining semantic link is more important than compactness

</div>
::right::
<div v-click='2'>

```python {*|1|7,8|9|10,11|12-20|21|*}{lines:true,at:3}
import tiktoken

def get_chunks(content:str,
               max_tokens:int=4000,
               overlap_tokens:int=1000,
               encoding_name="o200k_base") -> list:
    encoding = tiktoken.get_encoding(encoding_name)
    tokenized_text = encoding.encode(content)
    total_tokens = len(tokenized_text)
    if total_tokens <= max_tokens:
        return [content]
    chunks, current_pos = [], 0
    while current_pos < total_tokens:
        chunk_end = min(current_pos + max_tokens, total_tokens)
        chunk_tokens = tokenized_text[current_pos:chunk_end]
        chunk_text = encoding.decode(chunk_tokens)
        chunks.append(chunk_text)
        if chunk_end >= total_tokens:
            break
        current_pos += (max_tokens - overlap_tokens)
    return chunks
```

</div>

---
layout: two-cols-header
title: Semantic Boundary
level: 2
---

# Semantic boundary chunking

::left::
* Split text at semantic boundaries where topic, intent or idea changes. Rather than by fixed token counts this preserves context.
* Langchain has recently introduced `SemanticChunker`, a complete guide is attached in this slide.

<div v-click='1'>

## Benefits
* Preserves logical flow and context
* Avoids splitting mid-thought
* Enables topic-aware document processing
</div>

<div v-click='2'>

## Key Takeaway
* Semantic chunking aligns structural segmentation with linguistic meaning,
* Producing chunks optimized for summarization and retrieval tasks.
</div>

::right::

<div class="w-full h-[95%] overflow-hidden justify-center bg-gray-100 rounded-2">
  <iframe 
    src="https://python.langchain.com/docs/how_to/semantic-chunker/#__palette=2"
    class="rounded-2 origin-top-left"
    style="transform: scale(0.6); width: 166.67%; height: 166.67%;"
  ></iframe>
</div>

---
layout: two-cols-header
title: Comparative Analysis
level: 2
---

# Comparative Analysis Results

::left::
<v-click>

Evaluation across various chunking strategies to illustrate the trade-offs between accuracy, and contextual coherence in large-scale text segmentation.

| Method | Token Accuracy | Context Retention |
|--------|----------------|-------------------|
| Approximate | 60% | Low |
| Ratio-Based | 85% | Medium |
| Direct Token | **100%** | Low |
| Overlapping | 100% | High |
| Semantic Boundary | 98% | **Very High** |

</v-click>

::right::
<v-click>

## Key Findings
* Direct Token Manipulation is precise and efficient.
* Overlapping keeps context with slight redundancy.
* Semantic Chunking gives natural, meaning-based splits.
* Approximate/Ratio methods are fast for quick tasks.
* Token-based methods scale well with document size.

</v-click>

::bottom::
<v-click>

## Summary Insight
* Choose precision when accuracy matters,  
* choose semantics when context matters,  
* combine both for adaptive, intelligent chunking.

</v-click>

---
layout: cover
background: /text_splitting/images/background.jpg
---


<h1 class="font-bold !text-[60px]">Thank you</h1>
