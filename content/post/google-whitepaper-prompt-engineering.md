+++
title = 'Google Whitepaper: The Art and Science of Prompt Engineering'
date = 2025-01-09T10:00:00-08:00
draft = false
tags = ['AI', 'LLM', 'Prompt Engineering', 'Google', 'Machine Learning']
+++

Google released a comprehensive whitepaper on prompt engineering authored by Lee Boonstra (September 2024). This post distills the key insights from that 65-page document - a practical guide to getting better results from Large Language Models.

---

## 1. Introduction

When thinking about LLM input and output, a text prompt (sometimes accompanied by other modalities such as images) is the input the model uses to predict a specific output.

**You don't need to be a data scientist or a machine learning engineer - everyone can write a prompt.**

However, crafting the most effective prompt can be complicated. Many aspects affect its efficacy: the model you use, the model's training data, model configurations, word choice, style, tone, structure, and context.

Prompt engineering is therefore an **iterative process**. Inadequate prompts can lead to ambiguous, inaccurate responses and hinder the model's ability to provide meaningful output.

---

## 2. What is Prompt Engineering?

Remember how an LLM works: **it's a prediction engine**. The model takes sequential text as input and predicts what the following token should be, based on the data it was trained on. The LLM does this repeatedly, adding each predicted token to the sequence for predicting the next one.

When you write a prompt, you're attempting to set up the LLM to predict the right sequence of tokens.

**Prompt engineering** is the process of designing high-quality prompts that guide LLMs to produce accurate outputs. This involves tinkering to find the best prompt, optimizing prompt length, and evaluating writing style and structure in relation to the task.

Prompts can be used for various tasks: text summarization, information extraction, question and answering, text classification, language or code translation, code generation, and code documentation and reasoning.

---

## 3. LLM Output Configuration

Once you choose your model, you need to figure out the model configuration. Most LLMs come with various configuration options that control output. Effective prompt engineering requires setting these optimally for your task.

### 3.1 Output Length

An important setting is the **number of tokens to generate**. Generating more tokens requires more computation, leading to higher energy consumption, potentially slower response times, and higher costs.

**Important**: Reducing output length doesn't make the LLM more stylistically succinct - it just causes the LLM to stop predicting once the limit is reached. If you need short output, you'll also need to engineer your prompt to accommodate.

### 3.2 Sampling Controls

LLMs don't formally predict a single token. Rather, they predict **probabilities** for what the next token could be. These probabilities are then sampled to determine the output token.

**Temperature**, **top-K**, and **top-P** are the most common settings that determine how predicted token probabilities are processed.

#### 3.2.1 Temperature

Temperature controls the degree of **randomness** in token selection:

- **Temperature 0** (greedy decoding): Deterministic - highest probability token always selected
- **Low temperature** (0.1-0.3): More deterministic, factual responses
- **High temperature** (0.7-1.0): More diverse, creative, unexpected results
- **Very high** (>1.0): All tokens become equally likely

#### 3.2.2 Top-K and Top-P

These sampling settings restrict the predicted next token to come from tokens with top predicted probabilities.

**Top-K sampling** selects the top K most likely tokens. Higher top-K means more creative and varied output; lower top-K means more restrictive and factual output. Top-K of 1 is equivalent to greedy decoding.

**Top-P sampling** (nucleus sampling) selects top tokens whose cumulative probability doesn't exceed P. P = 0 means only the most probable token (greedy decoding); P = 1 means all tokens in vocabulary are considered.

#### 3.2.3 Putting It All Together

When temperature, top-K, and top-P are all available, tokens meeting both top-K and top-P criteria become candidates, then temperature is applied to sample from those candidates.

**Extreme settings cancel out other configurations**: Temperature = 0 makes top-K and top-P irrelevant; Top-K = 1 makes temperature and top-P irrelevant; Top-P = 0 makes temperature and top-K irrelevant.

**Recommended starting points:**
- Coherent, moderately creative: Temperature 0.2, Top-P 0.95, Top-K 30
- Especially creative: Temperature 0.9, Top-P 0.99, Top-K 40
- Less creative, factual: Temperature 0.1, Top-P 0.9, Top-K 20
- Single correct answer (math): Temperature 0

---

## 4. Prompting Techniques

LLMs are tuned to follow instructions and trained on large amounts of data. But they aren't perfect - the clearer your prompt, the better the LLM can predict the right text.

### 4.1 General Prompting / Zero-Shot

A **zero-shot** prompt is the simplest type. It only provides a description of the task and some text for the LLM to get started with. The input could be a question, start of a story, or instructions. "Zero-shot" means "no examples."

For example, you could ask the model to classify a movie review as POSITIVE, NEUTRAL, or NEGATIVE without showing it any examples first. The model uses its training to understand what you want.

**Pro tip**: Use low temperature (0.1) since no creativity is needed for classification tasks.

### 4.2 One-Shot & Few-Shot Prompting

When zero-shot doesn't work, provide examples in the prompt.

**One-shot prompt**: Provides a single example for the model to imitate.

**Few-shot prompt**: Provides multiple examples showing a pattern to follow.

**How many examples?** It depends on complexity of the task, quality of the examples, and capabilities of the model. General rule: use at least **3-5 examples** for few-shot prompting. More complex tasks may need more; input length limitations may require fewer.

**Guidelines for choosing examples:**
- Use examples **relevant** to your task
- Examples should be **diverse** and **high quality**
- One small mistake can confuse the model
- Include **edge cases** for robust output

### 4.3 System, Contextual, and Role Prompting

These three techniques guide how LLMs generate text, focusing on different aspects:

**System Prompting** sets overall context and purpose - the "big picture" of what the model should do. System prompts define the model's fundamental capabilities and can specify output requirements like format or safety guidelines.

**Role Prompting** assigns a specific character or identity to the model, affecting output style, voice, and personality. For example, asking the model to "act as a travel guide" helps it generate responses consistent with that role's knowledge and behavior. You can also specify styles: confrontational, descriptive, formal, humorous, inspirational, persuasive, etc.

**Contextual Prompting** provides specific details or background information relevant to the current task, helping the model understand nuances and tailor responses accordingly. This ensures the model quickly understands your request and generates more accurate, relevant responses.

There can be considerable overlap between these three types.

**Benefits of structured output (like JSON):**
- No manual format creation needed
- Data can be returned in sorted order
- **Forces structure and limits hallucinations**

### 4.4 Step-Back Prompting

Step-back prompting improves performance by prompting the LLM to first consider a **general question** related to the specific task, then feeding that answer into a subsequent prompt.

This "step back" activates relevant **background knowledge and reasoning** before attempting to solve the specific problem.

**Benefits:**
- More accurate and insightful responses
- Encourages critical thinking
- Applies knowledge in new, creative ways
- Can mitigate biases by focusing on general principles

For example, before asking "Write a storyline for a new video game level," first ask "What are 5 key settings that contribute to challenging and engaging game levels?" Then use that response as context for your specific request.

### 4.5 Chain of Thought (CoT)

Chain of Thought prompting improves reasoning by generating **intermediate reasoning steps**. This helps the LLM generate more accurate answers, especially for mathematical and logical problems.

LLMs often struggle with mathematical tasks because they're trained on text, and math requires a different approach. The simple addition of "Let's think step by step" to your prompt can dramatically improve accuracy.

**Advantages of CoT:**
- Low-effort but very effective
- Works with off-the-shelf LLMs (no fine-tuning needed)
- Provides **interpretability** - you can see the reasoning steps
- Improves **robustness** between different LLM versions

**Disadvantages:**
- More output tokens = higher cost and longer response times

**Few-shot CoT** is even more powerful - provide examples that include the reasoning steps, not just the final answer.

**Good candidates for CoT:**
- Code generation (breaking requests into steps)
- Synthetic data creation
- Any task that can be solved by "talking through it"

**Best practices for CoT:**
- Put the answer after the reasoning
- Extract the final answer separately from the reasoning
- Set temperature to 0 (CoT is based on greedy decoding)

### 4.6 Self-Consistency

While LLMs have shown impressive success, their reasoning ability is often limited. CoT helps but uses simple "greedy decoding." **Self-consistency** combines sampling and majority voting for better results.

**How it works:**
1. **Generate diverse reasoning paths**: Provide the same prompt multiple times with high temperature to encourage different approaches
2. **Extract the answer** from each generated response
3. **Choose the most common answer** (majority voting)

Self-consistency gives a **pseudo-probability likelihood** of an answer being correct, but obviously has higher costs due to multiple runs.

### 4.7 Tree of Thoughts (ToT)

Tree of Thoughts **generalizes** CoT by allowing LLMs to explore **multiple different reasoning paths simultaneously**, rather than following a single linear chain.

**How it works:**
- Maintains a "tree of thoughts"
- Each thought is a coherent language sequence serving as an intermediate step
- The model explores different reasoning paths by branching from different nodes

While CoT follows a single linear path from input to output, ToT branches out into multiple parallel paths before converging on the final output.

ToT is particularly well-suited for **complex tasks requiring exploration**.

### 4.8 ReAct (Reason + Act)

ReAct is a paradigm for enabling LLMs to solve complex tasks using **natural language reasoning combined with external tools** (search, code interpreter, APIs). This allows LLMs to perform actions like interacting with external APIs to retrieve information.

**ReAct is a first step towards agent modeling.**

ReAct mimics how humans operate: we reason verbally and take actions to gain information.

**How it works:**
1. LLM reasons about the problem and generates a plan
2. Performs actions in the plan
3. Observes the results
4. Updates reasoning and generates a new plan
5. Continues until reaching a solution

For example, when asked "How many kids do the band members of Metallica have?", a ReAct agent would search for who the band members are, then search for each member's children, then aggregate the results.

### 4.9 Automatic Prompt Engineering (APE)

Writing prompts can be complex. **Automatic Prompt Engineering** automates this by using a model to write prompts for you.

**Process:**
1. **Generate**: Prompt a model to generate output variants
2. **Evaluate**: Score candidates using metrics (BLEU, ROUGE)
3. **Select**: Choose the highest-scoring candidate
4. **Iterate**: Tweak and evaluate again

This is particularly useful for generating training data variations or finding optimal prompt formulations.

---

## 5. Code Prompting

Gemini and other LLMs can help with writing code in any programming language. This can significantly speed up development.

### 5.1 Prompts for Writing Code

You can ask LLMs to write code by describing what you need. Be specific about the programming language and the desired functionality.

**Important**: Since LLMs can't truly reason and may repeat training data patterns, always **read and test generated code** before using it.

### 5.2 Prompts for Explaining Code

When working in teams, you often need to read others' code. LLMs can help explain unfamiliar code by breaking down each section and describing what it does.

### 5.3 Prompts for Translating Code

Need to convert code between languages? LLMs can translate code from one programming language to another while maintaining the same functionality.

### 5.4 Prompts for Debugging and Reviewing Code

LLMs can help debug code by identifying bugs from error messages and suggesting fixes. They can also recommend improvements like better error handling, cleaner syntax, or edge case handling.

### 5.5 What About Multimodal Prompting?

Prompting for code uses regular language models. **Multimodal prompting** is a separate concern - it refers to using **multiple input formats** (text, images, audio, code) depending on the model's capabilities and the task.

---

## 6. Best Practices

Finding the right prompt requires tinkering. Use these best practices to become a pro.

### 6.1 Provide Examples

The **most important** best practice. Few-shot prompting is highly effective because examples act as a powerful teaching tool. They showcase desired outputs, allowing the model to learn and tailor its generation accordingly. It's like giving the model a reference point or target to aim for.

### 6.2 Design with Simplicity

Prompts should be **concise, clear, and easy to understand** for both you and the model.

**Rule of thumb**: If it's confusing for you, it will likely be confusing for the model.

**Use action verbs**: Act, Analyze, Categorize, Classify, Compare, Create, Describe, Define, Evaluate, Extract, Find, Generate, Identify, List, Organize, Parse, Predict, Recommend, Retrieve, Rewrite, Select, Summarize, Translate, Write

### 6.3 Be Specific About the Output

A concise instruction might not guide the LLM enough or could be too generic. Specific details help the model focus on what's relevant. Instead of "Generate a blog post about video game consoles," say "Generate a 3 paragraph blog post about the top 5 video game consoles. The blog post should be informative and engaging, written in a conversational style."

### 6.4 Use Instructions Over Constraints

**Instructions** tell the model what TO do. **Constraints** tell the model what NOT to do.

Research suggests **positive instructions are more effective** than constraints. This aligns with how humans prefer positive instructions over lists of what not to do.

**Why instructions work better:**
- Directly communicate desired outcome
- Give flexibility and encourage creativity
- Constraints might leave the model guessing
- Lists of constraints can clash with each other

**When to use constraints:** Preventing harmful or biased content, or when strict output format is required.

### 6.5 Control the Max Token Length

To control response length, set a max token limit in configuration, or explicitly request specific length in your prompt (e.g., "Explain quantum physics in a tweet-length message").

### 6.6 Use Variables in Prompts

Make prompts dynamic and reusable by using variables that can be changed for different inputs. This saves time, makes integration easier, and lets you use the same prompt template with different inputs.

### 6.7 Experiment with Input Formats and Writing Styles

Different models, configurations, formats, and word choices yield different results. Experiment with style, word choice, and prompt type (zero-shot, few-shot, system prompt).

The same goal can be formulated as a question, a statement, or an instruction - try different approaches.

### 6.8 For Few-Shot Classification, Mix Up the Classes

When doing classification tasks, **mix up the possible response classes** in your examples. Otherwise, you might overfit to the specific order.

By mixing classes, you ensure the model learns to identify **key features of each class**, rather than memorizing example order.

**Good rule of thumb**: Start with **6 few-shot examples** and test accuracy from there.

### 6.9 Adapt to Model Updates

Stay on top of model architecture changes, added data, and capabilities. Try newer model versions and adjust prompts to leverage new features.

### 6.10 Experiment with Output Formats

For non-creative tasks (extracting, selecting, parsing, ordering, ranking, categorizing), try **structured output formats** like JSON or XML. This forces structure and limits hallucinations.

### 6.11 Experiment Together with Other Prompt Engineers

If you need a good prompt, have **multiple people** make attempts. When everyone follows best practices, you'll see variance in performance between different approaches. Collaboration leads to better prompts.

### 6.12 Document the Various Prompt Attempts

**Document your prompt attempts in full detail** so you can learn what works and what doesn't.

Prompt outputs can differ across models, across sampling settings, across different versions of the same model, and even across identical prompts with small formatting or word choice differences.

**Track these fields for each attempt:**
- Name and version of prompt
- Goal (one sentence)
- Model name and version
- Configuration (Temperature, Token Limit, Top-K, Top-P)
- Full prompt text
- Output(s)
- Result (OK / NOT OK / SOMETIMES OK)
- Feedback notes

**Additional tips:**
- Track iteration versions
- Save prompts in separate files from code for easier maintenance
- Rely on automated tests and evaluation procedures

---

## 7. Summary

This whitepaper covers a comprehensive set of **prompting techniques**:
- Zero-shot and Few-shot prompting
- System, Role, and Contextual prompting
- Step-back prompting
- Chain of Thought (CoT)
- Self-consistency
- Tree of Thoughts (ToT)
- ReAct (Reason + Act)
- Automatic Prompt Engineering

**Key Principles to Remember:**

1. Prompt engineering is **iterative** - craft, test, analyze, document, refine, repeat
2. **Examples are powerful** - few-shot often beats clever zero-shot
3. **Chain of Thought unlocks reasoning** - "Let's think step by step"
4. **Configuration matters** - temperature, top-K, top-P affect output significantly
5. **Document everything** - you'll need it when revisiting prompts
6. **Test thoroughly** - especially for code, always verify outputs
7. When you change a model or configuration, **go back and re-experiment**

---

*This post summarizes key insights from Google's "Prompt Engineering" whitepaper by Lee Boonstra (September 2024).*
