<div align="center">

# LLM Engineering Tests — August 2026

### Testing models under non-standard engineering conditions

**10 tests · 10 models · first independent answer · no hints or retries**

[Open the results](https://dimadr.github.io/tests/llm_test_august_2026.html)

</div>

---

## About the project

This is not an attempt to create another universal benchmark or a ranking of the “best LLM overall”.

The goal is simpler and more practical: test several models on engineering tasks where recalling the right command, syntax, or standard pattern is not enough.

What matters more in these tasks is the ability to:

- localize a problem before changing the system;
- distinguish observations from assumptions;
- preserve invariants across multiple reasoning steps;
- understand concurrency, partial failure, and crash windows;
- avoid promising atomicity or exactly-once guarantees when the architecture cannot provide them;
- notice contradictions in the task itself;
- avoid inventing a defect merely because the prompt asks to find one;
- re-check the original requirements after producing a solution.

The complete scores and the matrix for all ten tests are published separately:

**https://dimadr.github.io/tests/llm_test_august_2026.html**

---

## Results

By average answer quality across all ten tests, **Qwen 3.8 Max ranked first with 8.65/10**.

Full ranking:

1. **Qwen 3.8 Max — 8.65**
2. **GLM 5.3 DeepThink Max — 8.50**
3. **Big Pickle — 8.40**
4. **DeepSeek 4 Pro — 8.25**
5. **Claude Code Sonnet 5 Medium — 8.15**
6. **Kimi K3 — 8.05**
7. **Grok — 7.40**
8. **Gemini 3.7 Flash — 7.10**
9. **GLM 5.2 — 6.75**
10. **MiMo 2.5 Pro — 5.45**

But the average score turned out to be only part of the picture.

The models differed substantially in their **engineering reasoning profiles**: one was better at localizing real failures, another at preserving complex invariants, while another handled SQL well but was weaker at checking the assumptions embedded in the task itself.

---

## The cost of quality

The formal ranking measures answer quality.  
**Speed and cost were intentionally excluded from the overall score.**

This matters when interpreting the results.

### Qwen 3.8 Max

Qwen achieved the best overall result and was especially strong in the second test series.

In practical use, however, it was one of the slowest models in the comparison.

> **With Qwen, higher quality often came at the cost of waiting time.**

Latency was not measured with a stopwatch, so this remained a qualitative observation and did not affect the score.

### Big Pickle

Big Pickle ranked third overall and won the first test series.

It was tested using **free access**.

No discount was given for price, model visibility, or service status: the prompts and grading criteria were identical to those used for the other models.

That makes its result particularly interesting from a practical perspective:

> **8.40/10 at zero cost for the test run.**

### GLM 5.3 DeepThink Max

GLM 5.3 DeepThink Max scored **8.50/10** and ranked second, nearly matching Qwen.

The within-family improvement is especially notable:

> **GLM 5.2 — 6.75 → GLM 5.3 DeepThink Max — 8.50 (+1.75).**

Speed was not measured systematically. GLM 5.3 subjectively spent a long time reasoning during the first series, but completed the second series noticeably faster than usual, so these tests do not support a stable conclusion about its latency.

### GLM 5.2

GLM 5.2 remains in the comparison as a separately tested version and as a control point for measuring progress within the model family.

---

## Methodology

All models received the same original tasks.

Only the **first independent answer** counted:

- no hints;
- no corrective clarification;
- no retry after critique;
- the highest available reasoning mode offered by the respective service.

A corrected answer after feedback did not replace the original score.

### What was evaluated

**Correctness**  
Technical and logical correctness of the solution.

**Discipline**  
Compliance with the task constraints, avoidance of unstated assumptions, and avoidance of unnecessary system changes.

**Epistemics**  
The ability to distinguish between:

- proven;
- probable;
- possible;
- unknown;
- impossible.

---

# Test areas

The exact prompts are intentionally not published yet.

This allows the same test series to remain usable for evaluating new models without exposing the specific traps and control points in advance.

Only the purpose of each test is described below.

---

## 01 · Diagnostic discipline

Diagnosis of a network problem while the local service is already confirmed to be working.

The test checked whether the model begins with observation and traffic localization, or immediately starts changing firewall rules, configuration, or other components.

Key principle:

> **First determine where the system is failing; only then change it.**

---

## 02 · Atomic state publication

File-based state with requirements for:

- atomic publication;
- safe re-runs;
- preservation of the previous successful state after an error;
- correct retention.

The test did not merely look for the presence of `mv` or a temporary directory. It checked whether dangerous intermediate states remained between individually reasonable operations.

---

## 03 · Intermittent failure diagnosis

A rare reverse-proxy failure affecting only a small fraction of requests.

The model had to:

- construct several causal hypotheses;
- prioritize them;
- propose a diagnostic test for each rather than a possible fix;
- choose one first test with the highest diagnostic value.

---

## 04 · Concurrency & crash consistency

Multiple writers and readers operating on shared state.

The test covered:

- mutual exclusion;
- lost updates;
- atomic publication;
- `kill -9`;
- crash consistency;
- the boundary between atomic visibility and durability.

---

## 05 · Limits of atomicity

The requirements contained a guarantee that could not be achieved by modifying the writer alone.

The task tested whether the model could **prove impossibility**, rather than assemble a convincing-looking combination of `rename`, symlinks, and locks that still failed to provide the required snapshot semantics.

---

## 06 · Epistemic discipline

The model was given code and asked to find a logical defect.

But the code already satisfied all stated requirements.

The test targeted a characteristic LLM failure mode:

> **Will the model invent a defect simply because the user expects one?**

A strong answer had to verify the invariant and conclude:

**“There is no defect.”**

This became one of the most discriminating tests in the set.

---

## 07 · Distributed systems

A payment operation between two services over an unreliable network.

The test covered:

- ambiguous timeout;
- retry;
- idempotency;
- persistent intent;
- crash windows;
- database uniqueness;
- the boundary between exactly-once delivery and exactly-once effect;
- the difference between a local database transaction and an external side effect.

---

## 08 · Transactional concurrency

Concurrent state modification in PostgreSQL.

The task tested not only whether the model could write correct SQL, but whether it understood why the SQL was correct:

- `READ COMMITTED`;
- row locking;
- re-evaluation;
- conditional `UPDATE`;
- `RETURNING`;
- consistency between state changes and the operation log.

---

## 09 · Security reasoning

A small public endpoint containing a real vulnerability.

The test checked whether the model could follow the chain:

**vulnerability → exploitability → actual impact → remediation**

while avoiding unsupported escalation from file disclosure to RCE, root access, or full system compromise.

---

## 10 · Requirements consistency

A set of mandatory requirements contained several independent logical contradictions.

The model had to:

1. prove that the original requirement set was impossible;
2. identify independent conflicts;
3. propose the smallest change to the requirements;
4. re-check the entire set afterward.

The final step turned out to be especially revealing.

Many models found the contradiction more easily than they avoided creating a new contradiction with their own proposed fix.

---

## Two test series

### Series 1

Primary focus:

- diagnostic discipline;
- causal localization;
- file-system atomicity;
- concurrency;
- crash consistency;
- proving the limits of what can be implemented.

### Series 2

Primary focus:

- invariant preservation;
- epistemic discipline;
- distributed systems;
- transactional concurrency;
- security reasoning;
- consistency of complex requirements.

The second series deliberately reduced the importance of knowing a specific command or technology and placed more weight on the quality of the reasoning itself.

---

## The most revealing result

The main difference between models was not simply how much they knew about Linux, PostgreSQL, Python, or HTTP.

Nearly all modern models can produce a technically plausible answer.

The harder part is to:

- avoid treating an assumption as a fact;
- avoid inventing an unstated requirement;
- trace an interleaving all the way to the end;
- find the failure window between two locally correct operations;
- recognize impossibility;
- recognize the absence of a defect;
- verify one's own solution as strictly as the original task.

> **It is more useful to test not how well a model can look like an engineer, but whether it can preserve the invariant when the system behaves in the most inconvenient possible way for its solution.**

---

## Limitations

This is not a universal hierarchy of LLMs.

The results apply only to:

- these ten tasks;
- the tested model versions;
- the selected reasoning modes;
- the state of the services at the time of testing.

A model with a lower overall score may be substantially better on another class of tasks.

The purpose of the project is to expose differences in engineering reasoning and provide a practical basis for choosing a model for real work.

---

## Links

**Results**  
https://dimadr.github.io/tests/llm_test_august_2026.html

**Repository**  
https://github.com/dimadr/tests

---

<div align="center">

August 2026

</div>
