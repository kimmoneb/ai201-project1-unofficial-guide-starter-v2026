# Unit 2 Run Log

## Before Improvement

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 3. Relevance gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain a complete thought | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 5. Answers are factually accurate | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |

## Diagnosis

**Criterion 1 — MISSED:**  
The relevance gate rejected all five in-scope questions before they could proceed through the full pipeline. The best retrieval distances were approximately 0.66–0.83, while the gate cutoff was 0.6. This suggests the gate threshold is too strict for the current corpus and retrieval results.

**Criterion 2 — MISSED:**  
Because the relevance gate rejected every in-scope question, the model did not generate any answers. As a result, there were no generated answers that could name a source.

**Criterion 3 — MET:**  
The relevance gate successfully refused all 5 out-of-scope questions, exceeding the target of 4 out of 5. This shows that the gate is effective at blocking unrelated questions, although it is currently also blocking relevant questions.

**Criterion 4 — MISSED:**  
Only 3 of the 5 sampled chunks could be understood as complete thoughts without additional context. For example, the chunk "On the add/drop deadline" does not contain enough information to stand on its own. This indicates that the current chunking strategy sometimes creates chunks that are too small or incomplete.

**Criterion 5 — MISSED:**  
No answers were generated because all five in-scope questions were rejected by the relevance gate. Therefore, none of the five answers could be fact-checked for accuracy.

## Diagnosis

**Criterion 1 – MISSED**
- Stage: Retrieval
- Mechanism: The relevance gate rejected all five in-scope questions before they could continue through the pipeline. The best retrieval distances were approximately 0.66–0.83, while the cutoff was 0.6. Relevant results were therefore treated as out-of-scope.

**Criterion 2 – MISSED**
- Stage: Retrieval / Generation
- Mechanism: Because the relevance gate rejected every in-scope question, none reached the generation stage. Therefore, the system could not generate answers containing source names.

**Criterion 4 – MISSED**
- Stage: Chunking
- Mechanism: Only 3 of the 5 sampled chunks were understandable as complete thoughts. Some chunks were too short or incomplete, such as "On the add/drop deadline," which does not contain enough information to stand on its own.

**Criterion 5 – MISSED**
- Stage: Retrieval / Generation
- Mechanism: All five in-scope questions were rejected by the relevance gate, so no answers were generated and factual accuracy could not be demonstrated.

### Pattern Across Misses

The main pattern is that the relevance gate is too strict for the current retrieval distances. This single retrieval-stage problem caused Criteria 1, 2, and 5 to fail because relevant questions were prevented from reaching generation. A separate chunking issue caused Criterion 4 to fail because some chunks were incomplete.

## Milestone 4 - Improvement

**Change made:** Increased the relevance gate threshold from 0.60 to 0.85.

**Reason:** The original 0.60 threshold rejected all five in-scope questions even though their best retrieval distances were approximately 0.66-0.83. Increasing the threshold allows relevant questions to continue through the pipeline.

**After-test status:** The evaluation was attempted with the new 0.85 threshold, but the generation stage could not complete because the Gemini API repeatedly returned a 503 UNAVAILABLE error due to temporary high demand. The new threshold did allow the first in-scope question to reach the generation stage, showing that the relevance gate was no longer blocking it.


## What's Still Broken

After increasing the relevance threshold from 0.60 to 0.85, the full evaluation could not be completed because the Gemini API repeatedly returned a 503 UNAVAILABLE error due to temporary high demand.

The original evaluation also showed that the chunking strategy sometimes produced chunks that were too small or incomplete to stand on their own. A future improvement would be to test a larger chunk size or add overlap between chunks and compare the results against the original strategy.

The relevance gate also still needs additional testing. Although increasing the threshold allowed an in-scope question to pass the gate, a higher threshold could also allow unrelated questions through. I would test several threshold values to find a better balance between accepting relevant questions and refusing out-of-scope questions.

## What I'd Do Differently

I would make the retrieval criterion more specific by measuring whether the answer appears within the top retrieved chunks instead of relying mainly on whether a question passes the relevance gate. This would make it easier to separate retrieval quality from the behavior of the relevance threshold.

## How I Used AI

I used AI to help interpret the evaluation results and identify patterns across the failed criteria. AI helped me reason about the relevance threshold and distinguish retrieval and chunking problems from generation failures. I also used AI to troubleshoot the Gemini 503 error during the after-improvement evaluation.