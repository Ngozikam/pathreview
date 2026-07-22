## Week 7 - Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/153

**Issue title:** Faithfulness checker crashes when a context chunk has `text: None`

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The faithfulness checker crashes when a retrieved context chunk contains a `text` key whose value is `None`. The current code uses `chunk.get("text", "")`, but the default empty string only applies when the key is missing, so an existing `text: None` value is passed into `" ".join()` and causes a `TypeError`. A successful fix should allow the RAG faithfulness checker to handle `None` text values gracefully without crashing while preserving the expected faithfulness scoring behavior.

**Issue fit and selection reasoning:**
I selected this Tier 1 issue because it is a localized bug in the RAG evaluation module and the expected change is limited in scope. I located and reviewed the `FaithfulnessChecker.check()` method in `rag/evaluator/faithfulness_checker.py`, read the relevant unit tests in `tests/unit/test_faithfulness_checker.py`, and traced where `FaithfulnessChecker` is used in `rag/evaluator/eval_suite.py`. I understand that the failure occurs while building the context string when a chunk contains `"text": None`. This issue is a good fit for my current experience because I can reproduce the bug, understand the affected code and test, and implement and verify a focused fix without making broad architectural changes.

**Branch name:** `fix/153-none-context-chunk-text`

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction summary:**
I reproduced Issue #153 locally by running `pytest tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text -v`. The test failed in `rag/evaluator/faithfulness_checker.py` with `TypeError: sequence item 0: expected str instance, NoneType found`, confirming that `FaithfulnessChecker.check()` crashes when a context chunk contains `"text": None`.