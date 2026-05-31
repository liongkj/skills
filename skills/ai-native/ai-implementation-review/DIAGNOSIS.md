# Runtime Diagnosis Protocols

Each protocol follows: symptom → hypotheses → measurements → fix.

---

## Hallucination / Incorrect Output

### Symptom
Model returns facts not supported by the input, contradicts known context, or makes up sources.

### Hypotheses
1. **Context quality** — the relevant information was not included in the prompt (truncation, missing retrieval, wrong window)
2. **Prompt specificity** — the instruction is vague or allows fabrication
3. **Temperature too high** — sampling noise introduces ungrounded content
4. **RAG grounding weak** — retrieved chunks are irrelevant, outdated, or too sparse
5. **Source attribution missing** — the model was not instructed to cite sources

### Measurements
- Log the exact prompt and model output for a hallucination instance
- Count tokens in the context window — did truncation cut relevant content?
- Check retrieval results — were relevant documents returned and within the window?
- Test with temperature=0 — does the hallucination disappear?
- Test with explicit "only answer from the provided context" instruction

### Fix
1. If truncation: increase context budget, trim irrelevant preamble, prioritize recent/important context
2. If retrieval: improve chunking strategy, add reranking, increase top-K
3. If temperature: set to 0 for fact-oriented tasks
4. If instruction: add "cite your sources" and "if unsure, say you don't know"
5. If persistent: add output validation against known facts

---

## Latency

### Symptom
Model response takes too long — above user tolerance (typically >2s for chat, >5s for analysis).

### Hypotheses
1. **Model too large** for the task
2. **Prompt too long** — large context increases time-to-first-token
3. **Tool call chain too deep** — sequential round trips multiply latency
4. **Retry storms** — transient failures trigger multiple attempts
5. **Streaming disabled** — full output generated before any display

### Measurements
- Measure time-to-first-token vs total generation time
- Count tool call rounds per request
- Tokenize the prompt — how many tokens on input vs output?
- Check model API response times in logs
- Check retry counters

### Fix
1. If model too large: swap to smaller model for simple tasks, keep large model for complex reasoning
2. If prompt long: shorten system prompt, trim conversation history, use sliding window
3. If tool chain deep: parallelize independent tool calls, reduce tool call rounds
4. If retry storms: implement exponential backoff, reduce timeout
5. If streaming off: enable streaming for chat-like interactions

---

## Cost Blowup

### Symptom
API costs are higher than expected or growing without proportional user value.

### Hypotheses
1. **Prompt too large per call** — paying for tokens that don't contribute to output quality
2. **Redundant retries** — every transient failure doubles cost
3. **Model too large** — using GPT-4 where GPT-4o-mini would suffice
4. **No caching** — identical context encoded repeatedly
5. **No fallback** — expensive model used even for trivial tasks
6. **Agent loops uncontrolled** — agent keeps calling tools without termination

### Measurements
- Calculate average token cost per user interaction
- Log prompt sizes per call — identify largest prompts
- Count retries per successful call
- Identify calls where output quality would not suffer with a cheaper model

### Fix
1. If prompt large: prune system prompt, use shorter conversation history, cache frequent prefixes
2. If retries: reduce timeouts, improve error handling, limit retries to 2
3. If model: route simple tasks to cheaper model, reserve expensive model for complex reasoning
4. If no caching: implement context caching (OpenAI Prompt Caching, etc.)
5. If agent loops: set max iterations, add cost budget per session

---

## Brittle Output

### Symptom
Model output fails to parse, has wrong schema, or produces unusable tool arguments.

### Hypotheses
1. **Output format mismatch** — model ignores structured output constraints
2. **Schema too complex** — nested JSON with ambiguous fields
3. **Tool call arg errors** — wrong types, missing required fields, extra fields
4. **Enum drift** — model produces values outside the allowed set

### Measurements
- Log parsing failure rate over time
- Collect examples of misformatted output — identify patterns
- Check if the model supports structured output mode (JSON mode, tool calling)
- Validate schema complexity — can a human fill it out without errors?

### Fix
1. If format mismatch: use structured output mode (JSON mode, constrained decoding)
2. If schema too complex: flatten the schema, reduce nesting, use simpler types
3. If tool args: add explicit validation before execution, implement arg retry with error feedback
4. If enum drift: provide examples in the prompt, use constrained decoding for enums

---

## Context Overflow

### Symptom
Model forgets important information from earlier in the conversation or truncation warnings appear.

### Hypotheses
1. **Context window exceeded** — prompt + output exceeds model limit
2. **No truncation strategy** — oldest messages dropped without consideration of importance
3. **Long conversation without summarization** — raw history accumulates past useful context

### Measurements
- Log token count per request
- Check for truncation warnings or silent truncation
- Count messages in conversation history
- Identify where the model starts forgetting — measure the "forget point" in tokens

### Fix
1. If overflow: implement sliding window that keeps recent messages + important older context
2. If no strategy: prioritize messages by relevance (recency + importance), not just recency
3. If long conversation: summarize old segments, keep summary in context, drop raw messages
4. If critical: increase model context window (e.g. 128K vs 8K)

---

## Quality Regression

### Symptom
Output quality degrades over time — eval scores drop, user complaints increase, or outputs become less relevant.

### Hypotheses
1. **Prompt drift** — system prompt was changed and quality declined
2. **Model update** — the underlying model was updated or swapped
3. **Context shift** — user behavior or input distribution changed
4. **Eval suite stale** — test cases no longer represent real usage
5. **Rubric degradation** — scoring criteria relaxed or no longer enforced

### Measurements
- Compare eval scores over time — when did the drop occur?
- Check prompt change history around the drop date
- Compare outputs from before and after the drop on the same test cases
- Measure distribution shift in user inputs
- Review rubric adherence scores

### Fix
1. If prompt drift: revert prompt change or tune the new prompt
2. If model update: pin model version, test new version against golden set
3. If context shift: add new test cases representing current input distribution
4. If eval stale: refresh golden dataset, add recent failure cases as tests
5. If rubric degraded: re-calibrate rubric with current quality standards
