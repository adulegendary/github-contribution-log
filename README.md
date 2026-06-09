# github-contribution-log
# Contribution [#]: [Bug]: Schedule Tab Shows 2 "Security Exception" on new account without admin

**Contribution Number:** [1 / 2 / 3]  
**Student:** Adonai Weldemicael  
**Issue:**(https://github.com/carlos-emr/carlos/issues/2689)
**Status:** Phase I completed 

---

## Why I Chose This Issue
Fits my skills — Java + a bit of SQL, which is what I know.
Right difficulty — "good first issue," 
Diagnosis already done — the reporter traced the cause and named the files, so I can focus on the fix.
It's available — no one assigned, no PR in progress.
Teaches the full workflow — reproduce → fix → test → PR


---

## Understanding the Issue

A new staff account in CARLOS gets a login but no permissions. So when that person logs in, the system lets them through the front door — but the first page it sends them to (the schedule) checks "are you allowed to see appointments?", the answer is "this person has no permissions at all," and instead of saying "access denied" nicely, the page crashes and pops up 2 error alerts.

### Problem Description
When an admin creates a new user account, the account gets a login but no role (no permissions). The user can sign in, but the first page they land on the schedule checks whether they're allowed to view appointments. Since the account has no role, that check fails, the page errors out, and the user sees two "Security Exception" alerts instead of their schedule


### Expected Behavior

[What should happen?]

### Current Behavior

[What actually happens?]

### Affected Components

[Which parts of the codebase are involved?]

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
