# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does
This project creates a question-answering system using the campus_life corpus. The system retrieves information from documents about campus life, including dining, housing, courses, transportation, and other student-related topics. It uses the retrieved information to answer questions and identify the source where the information came from. The system also uses a relevance cutoff to avoid answering questions that are not covered by the corpus.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy

**Chunk size:** Paragraph-based
**Overlap:** None
I decided to split the documents by paragraph breaks instead of using the original fixed 800-character chunks. The documents in the campis_life corpus are mostly short posts, so paragraph-based splitting keeps related sentences together without cutting through them based on character count. After changing the chunker, it produced 271 chunks with an average length of 101 characters. 
<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documets`

```
On the add/drop deadline
```

**Chunk 2** — source: `course_cs_210_workload.txt#2` — produced by: `chunker.py::split_documets`

```
It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 3** — source: `course_phys_130.txt#3` — produced by: `chunker.py::split_documets`

```
The one piece of advice: the lab practical is worth 20% and almost nobody prepares for it.
```

**Chunk 4** — source: `dining_verrill_street_grill.txt#1` — produced by: `chunker.py::split_documets`

```
I'm a junior and I've done this twice now. Wait times: up to 30 minutes on Friday evenings, otherwise under 10. The thing worth going for is the burger, which is the only late-night hot food on campus. The thing to know is that one register, so the queue is a single line no matter how busy.
```

**Chunk 5** — source: `housing_morrow_house.txt#2` — produced by: `chunker.py::split_documets`

```
The good: cheapest housing tier by about $900 a year, and the singles are real singles.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** How frequently does the campus shuttle run?

**Answer:** I don't have enough information to answer how frequently the campus shuttle runs (transit_shuttle.txt).
The system retrieved 'transit_shuttle.txt' with a best distance of 0.299, which was below my 0.6 cutoff and indicated a close match. However, the model still said it did not have enough information to answer. This shows retrieval finds a relevant source, the generated response may still fail to use the information from that source.


**My relevance cutoff:** 0.6
I kept the cutoff at 0.6 because results beloe this value are treated as relevant. My test questions were outside the scope of the campus_life corpus, while the provided out-of-scope questions had best distances between 0.78 and 0.86. A 0.6 cutoff prevents these unrelated questions from passing.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| What two Caribbean islands got independence from Great Britain in August 1962? | No | 0.826 |
| What city opened the first American Subway System? | No | 0.660 |
| Which team won FIFA World Cup in 2026? | No | 0.788 |
| Who is the fastest woman in history? | No | 0.709 |
| What is the name of the island with 365 beaches? | No | 0.702 |

The best distances for my five test questions ranged from 0.660 to 0.826.
The five provided out-of-scope questions ranged from 0.780 to 0.850.
There was some overlap because my test questions were also not covered by the
campus_life corpus. I kept the relevance cutoff at 0.6 because the retrieved
chunks did not contain the answers to my questions, and increasing the cutoff
could allow unrelated information to pass the relevance gate.

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I used AI when I ran into an ONNX embedding error that prevented me from building the search index. It helped me narrow the problem down to store.py and understand what needed to be changed. I tested the suggested fix before keeping it to make sure the indexing and retrieval actually worked.

**2.** I used AI to better understand the distance scores I got while testing my questions. It helped me understand why my questions were returning high distances and how those results compared with the out-of-scope questions. Instead of changing my questions to get better results, I kept them and documented that the campus_life corpus did not contain the information needed to answer them.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain a complete thought | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 5. Answers are factually accurate | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MISSED | All three runs scored 0/5, below the target of 4/5. |
| 2 | Every answer names a source | MISSED | All three runs scored 0/5 because the gate prevented answers from being generated. |
| 3 | Gate stops out-of-corpus questions | MET | All three runs scored 5/5, exceeding the 4/5 target. |
| 4 | Sampled chunks contain a complete thought | MISSED | Only 3/5 sampled chunks were complete enough to stand on their own. |
| 5 | Answers are factually accurate | MISSED | All three runs scored 0/5 because no answers were generated to evaluate. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->
     **Criterion 1 — MISSED**
- Stage: Retrieval / relevance gate
- Mechanism: The five in-scope questions had best retrieval distances of approximately 0.66–0.83, but the relevance threshold was 0.60. The gate therefore rejected relevant questions before they could continue.

**Criterion 2 — MISSED**
- Stage: Generation
- Mechanism: Because the relevance gate stopped the in-scope questions, generation never produced answers, so the answers could not contain source names.

**Criterion 4 — MISSED**
- Stage: Chunking
- Mechanism: Some chunks were too short or incomplete to stand alone. For example, a sampled chunk contained only "On the add/drop deadline," which requires additional context.

**Criterion 5 — MISSED**
- Stage: Generation
- Mechanism: The relevance gate prevented the in-scope questions from reaching generation, so there were no generated answers to evaluate for factual accuracy.

**Pattern:** The main pattern across Criteria 1, 2, and 5 was the relevance gate blocking in-scope questions. Criterion 4 revealed a separate chunking problem.

## The Improvement

**What I changed:**
I increased the relevance gate threshold from 0.60 to 0.85.

**Why I picked it:**
The five in-scope questions had best retrieval distances of approximately 0.66–0.83, so the original 0.60 threshold was rejecting relevant questions. Increasing the threshold directly addresses the relevance-gate problem identified in my diagnosis.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**After-run status:** The full after-improvement evaluation could not be completed. With the threshold increased to 0.85, the first in-scope question passed the relevance gate and reached generation, but the Gemini API repeatedly returned a 503 UNAVAILABLE error due to temporary high demand. Because the evaluation did not complete, I did not assign unsupported after-run scores or verdicts.

**Did it help?**
The change affected the intended stage because an in-scope question that had previously been rejected was able to pass the relevance gate and reach generation. However, I cannot determine whether the system improved across all five criteria because the full after-test was interrupted by the repeated Gemini 503 errors.
<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->


## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
