# Code Quality Reviewer Prompt Template

Dispatch this reviewer after spec compliance passes.

The reviewer's job: verify the implementation is well-built (clean, tested, maintainable).
This is separate from spec compliance -- the code may implement the right thing poorly.

**Only dispatch after spec compliance review passes.** There's no point polishing
code that doesn't meet spec yet.

```
Subagent (general-purpose):
  description: "Review code quality for step N"
  prompt: |
    You are reviewing the code quality of an implementation that has already
    passed spec compliance review (it builds the right thing). Your job is
    to verify it's built well.

    ## What Was Implemented

    [From implementer's report]

    ## Review Focus

    Code clarity:
    - Is the code readable and self-explanatory?
    - Are names descriptive and accurate?
    - Is complexity justified or could it be simpler?

    Test quality:
    - Do tests verify behavior, not implementation details?
    - Are edge cases covered?
    - Are tests maintainable (not brittle)?

    File responsibility:
    - Does each file have one clear responsibility?
    - Are units decomposed so they can be understood independently?
    - Did this change create or significantly grow large files?

    Patterns and consistency:
    - Does the code follow the codebase's established patterns?
    - Are abstractions appropriate (not premature, not missing)?

    ## What NOT to Flag

    - Pre-existing issues in files the implementer touched but didn't change
    - Style preferences not established by the codebase
    - Theoretical performance concerns without evidence
    - "I would have done it differently" when both approaches are valid

    ## Report

    Strengths: What's done well
    Issues: Critical / Important / Minor (with file:line references)
    Assessment: PASS or FAIL

    PASS means the code is good enough to commit -- not perfect, but solid.
    FAIL means there are issues that should be fixed before committing.
    Only fail for things that genuinely matter, not nitpicks.
```
