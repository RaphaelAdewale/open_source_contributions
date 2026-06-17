# Contribution [1]: [FR] highlight variant stock in parts views

**Contribution Number:** [1]  
**Student:** Raphael Adewale 
**Issue:** https://github.com/apache/seatunnel/issues/10753
**Status:** [Phase II]

---

## Why I Chose This Issue
I chose issue "Helm Chart v3.Release not trigger hook" in pulumi-kubernetes (Pulumi) because it aligns with my interest in cloud infrastructure and my goal to build hands-on experience with Kubernetes and Infrastructure as Code tooling. The issue is labeled "good-first-issue" and has a clearly scoped bug with a specific trigger condition.
I'm interested in this because:

1.  I've worked with Kubernetes concepts before, so I understand the basics of Helm charts and release lifecycles, this issue lets me go deeper into how hooks are supposed to fire
2.  The bug is contained to the Helm Chart v3 integration module, not the entire provider codebase, making it realistic to navigate as a first contributor
3.  Pulumi is actively maintained and presented major Kubernetes updates as recently as KubeCon 2024, meaning PRs in this area are likely to be reviewed and merged
4.  I want to understand how Pulumi bridges real Kubernetes behavior (Helm hook lifecycle) with its own IaC resource model — a pattern that's transferable to any cloud infrastructure role

From reading the issue, the current problem is that lifecycle hooks defined in a Helm v3 chart are not being triggered on Release, meaning post-install or pre-upgrade hooks silently don't run — breaking deployments that depend on them. My contribution would fix a real reliability gap that affects production Kubernetes users.
---

## Understanding the Issue

### Problem Description

[In your own words, what's broken or missing?]

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
