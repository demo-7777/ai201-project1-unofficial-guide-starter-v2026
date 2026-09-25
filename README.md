# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none, because the grader can't
> read it.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Week 1

## What This Does

This project uses the campus_life corpus to answer questions about student life using info from the provided documents. It can answer questions about topics like dining, housing, courses, parking, and campus facilities. The system retrieves the most relevant document chunks, checks whether they are relevant enough to use, and then generates an answer based only on those chunks. Each answer also includes the source document so the information can be checked.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy

**Chunk size: 500 characters**
**Overlap: No fixed overlap, chunks are grouped by paragraph boundaries instead.**

I chose this strategy because the campus_life documents are short posts, with most documents already fitting within a few hundred characters. In Milestone 1, the starter produced 88 documents and 88 chunks, which showed that the default 800-character splitter rarely split anything. I changed the strategy to group complete paragraphs together up to about 500 characters so each chunk stays focused while preserving complete ideas.

This produced 90 chunks instead of 88, and the sampled chunks were still understandable without needing neighboring chunks.

## Sample Chunks

======================================================================
Chunk 1  |  source: admin_add_drop_deadline.txt#0  |  produced by: chunker.py::split_documents
======================================================================
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.

======================================================================
Chunk 2  |  source: course_biol_160_exams.txt#0  |  produced by: chunker.py::split_documents
======================================================================
BIOL 160 Cell Biology — assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.

======================================================================
Chunk 3  |  source: course_math_220_exams.txt#0  |  produced by: chunker.py::split_documents
======================================================================
MATH 220 Linear Algebra — assessment

Two midterms and a cumulative final. Curved to a b- median.

The problem sets are the course; the lectures make sense afterwards rather than during.

======================================================================
Chunk 4  |  source: dining_the_ridgeway_cafe.txt#0  |  produced by: chunker.py::split_documents
======================================================================
The Ridgeway Café

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.

======================================================================
Chunk 5  |  source: housing_morrow_house.txt#0  |  produced by: chunker.py::split_documents
======================================================================
Morrow House — what it's actually like

Just finished a year in this building. Built 1954, partially renovated 2008. Rooms are singles and doubles, hall bathrooms.

The good: cheapest housing tier by about $900 a year, and the singles are real singles.

The bad: known damp problem on the ground floor; two rooms were taken offline in 2024.

Laundry costs $1.50 wash, $1.25 dry, coin or card. On noise: loud until about 1am on weekends, no enforced quiet hours.

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:**

"What are the wait times at The Ridgeway Café around 12:30?"

**Answer:**

"The wait times at The Ridgeway Café are 10 to 15 minutes at 12:30."

**Source:**

dining_the_ridgeway_cafe.txt

```
```

**My relevance cutoff:**

0.7

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| What lunches do students mention ordering most often? | Yes | 0.6203 |
| What do students say about how difficult it is to find parking? | Yes | 0.5795 |
| What do students say about professors' office hour availability? | Yes | 0.5168 |
| What steps do students mention for changing roommates? | Yes | 0.5822 |
| What times do students say the gym is least crowded? | Yes | 0.5086 |
| What is the capital of Mongolia? | No | 0.8246 |
| How do I change the oil in a diesel engine? | No | 0.9340 |
| Who won the 1994 World Cup? | No | 0.8859 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| How do I write a for loop in Rust? | No | 0.8960 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.**

I used AI to help review my five test questions and make them more specific and easier to check. The AI suggested clearer wording and more specific expected phrases. I then edited the questions myself so they matched the topics I wanted to test in the campus_life corpus.

**2.**

I used AI to help revise the chunking logic in chunker.py. It suggested grouping text by paragraph boundaries with a target size of about 500 characters instead of using the starter’s fixed 800-character windows. I added that approach, ran the index again, and checked five sample chunks to make sure they were still understandable on their own.

**3.**

I used AI to help review the Week 2 test results and identify where the failures were happening in the pipeline. It suggested that Criteria 1 and 5 were mainly retrieval problems because the returned chunks often did not contain the specific information needed, while Criterion 2 was a generation problem because refusal responses did not name sources. I used that diagnosis to choose hybrid search with BM25 as my single improvement, then compared the before and after evaluation results to see if it helped.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Week 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     week 1 — the point is that someone can see what you said before you knew
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
| 1. Retrieved chunk contains the answer | 4 of 5 | 1 of 5  | 1 of 5 | 1 of 5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 1 of 5 | 1 of 5 | 1 of 5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 4. Sampled chunks have enough context | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 5. Relevant source appears in top 3 | 5 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | MISSED |

Run evidence produced by: `run_eval.py::main`
Retrieval produced by: `store.py::search`
Chunks produced by: `chunker.py::split_documents`

Criterion 1: 

Question: What do students say about professors' office hour availability?

Best distance: 0.5168

I do not have enough information to answer this question.

Criterion 2:

I do not have enough information in the provided documents to answer what lunches students mention ordering most often.

Criterion 3: 

Question: What is the capital of Mongolia?
Best distance: 0.825
Gate: refused

Criterion 4:

Evidence from python app.py chunks -n 5

Chunk: dining_the_ridgeway_cafe.txt
Produced by: chunker.py::split_documents

The Ridgeway Café

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.

Criterion 5:

Question: What do students say about how difficult it is to find parking?

1. admin_parking_permits.txt — distance 0.5795



<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     week — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer  | MISSED | Only 1 of 5 test questions had a retrieved chunk that clearly contained the answer, below the 4 of 5 target |
| 2 | Every answer names a source | MISSED | Several generated answers were refusals that did not name a source document, so the 5 of 5 target was not met. |
| 3 | Gate stops out-of-corpus questions | MET | The relevance gate refused all 5 out of scope questions, which exceeded the 4 of 5 target. |
| 4 | Sampled chunks have enough context | MET | I reviewed the five sampled chunks and all 5 were understandable without neighboring chunks, exceeding the 4 of 5 target. |
| 5 | Relevant source appears in top 3 | MISSED | A relevant source appeared in the top 3 for only 2 of 5 test questions, below the 5 of 5 target. |

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

### Criterion 1 — Retrieved chunks contain the answer

**Stage:** Retrieval

For four of the five test questions, the retrieved chunks did not contain enough information to answer the question. For example, the office-hours, roommate-change, and gym questions returned chunks that were related to campus life but did not contain the specific information being asked for. Because the answer was missing before generation began, the failure happened at retrieval.

### Criterion 2 — Every answer names a source

**Stage:** Generation

Several responses correctly refused to answer because the retrieved documents did not contain enough information, but those refusal responses did not name any source document. Since the retrieved source list existed but the final generated response omitted source names, this failure happened during generation.

### Criterion 5 — Relevant source appears in top 3

**Stage:** Retrieval

Relevant sources were not consistently ranked near the top. The parking question retrieved `admin_parking_permits.txt` as the top result, but several other questions returned unrelated or only loosely related documents in the top results. This suggests the semantic retrieval method was matching general topic similarity without consistently finding documents containing the exact information requested.


## The Improvement

**What I changed:**

I changed retrieval from semantic vector search alone to a hybrid approach that combines semantic similarity with BM25 keyword matching. The hybrid score gives more weight to semantic similarity while also considering exact keyword matches.

**Why I picked it:**

My diagnoses showed that Criteria 1 and 5 were mainly failing during retrieval. Several questions returned documents that were generally related to campus life but did not contain the specific information being requested, so I tested whether adding keyword matching would rank more useful documents higher.

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

**Did it help?**

The hybrid search did not meaningfully improve the measured results. Criteria 1 and 5 still missed their original targets, and the same four questions still did not retrieve enough information to produce supported answers. The change altered which documents were returned and their ranking, but it did not increase the number of test questions the system could answer correctly.

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

Criteria 1 and 5 are still missed because retrieval does not consistently return chunks containing the specific information needed by the question. Adding BM25 changed the ranking of some documents, but it did not improve the overall test results. A next step would be to try a different retrieval weighting, increase top-k, or test a different chunking strategy.

Criterion 2 is still missed because refusal responses do not name a source document. A next step would be to change the generation prompt so that even refusal responses identify the retrieved sources. I stopped after the single required improvement so I could measure its effect without mixing multiple changes together.

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

I would change Criterion 5 so that it measures whether the top three retrieved chunks actually contain the answer instead of only requiring a relevant source. During testing, some sources were related to the topic but still did not contain enough information to answer the question. Measuring whether the answer is present would make the criterion more precise.

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
