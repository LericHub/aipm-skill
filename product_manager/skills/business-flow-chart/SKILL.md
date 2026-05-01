---
name: "business-flow-chart"
description: "Provides standardized business process flowchart drawing guidelines, focusing on decision node norms, branch logic, and exception path handling. Invoke when drawing business process flowcharts, system judgment logic diagrams, or UML activity diagrams."
---

# Business Process Flowchart Drawing Guide

## Overview

The core of business process flowcharts lies in using standardized symbols to clearly display system judgment logic and business flow paths. The focus is on accurately expressing conditional branches through diamond decision nodes and ensuring all branches have clear exits.

## Key Elements and Usage Standards

### 1. Basic Symbol Mandatory Standards

| Symbol | Shape | Usage Rules |
|--------|-------|-------------|
| Start/End | Oval | Only 1 start and 1 end point per diagram, avoid logic loop missing |
| Operation Step | Rectangle | Specific business actions, max 15 characters per node |
| Decision Node (Core) | Diamond | **Must** have at least 2 branches, conditions must be clearly labeled |
| Flow Arrow | Arrow Line | Strictly top-to-bottom, left-to-right, avoid crossings |

### 2. Decision Logic Special Handling

**Multi-condition Branches:**
- Diamond should state unified judgment basis (e.g., "Order Type?")
- Branches should label mutually exclusive conditions (e.g., "Type A", "Type B")

**Nested Decisions:**
- Avoid stacking multiple diamonds directly
- Split sub-judgments into independent sub-processes
- Main process only retains key decision points

**Exception Paths:**
- All judgment branches must be closed-loop
- "Payment Failed" must point to "Retry" or "Terminate"
- **No dangling branches allowed**

## Drawing System Judgment Logic Practical Approach

### Step 1: Sort Logic Before Drawing

1. **Identify judgment trigger points:** List all decision points in the system
2. **Enumerate branch scenarios:** Force write all possible results for each judgment node
3. **Verify logic closed-loop:** Simulate execution to ensure every branch reaches end node

### Step 2: Layered Design to Avoid Confusion

- **Main process focuses on key decisions:** Only retain core judgments affecting business direction
- **Complex logic uses swimlane diagrams:** Divide responsibilities by roles
- **Annotate rule parameters:** State thresholds or basis next to judgment nodes

## Common Errors and Avoidance Guide

### Judgment Logic Errors

| Error Type | Example | Correct Approach |
|-----------|---------|------------------|
| Fuzzy Conditions | "Passed?" | "Credit Score ≥ 600?" |
| Missing Branches | Only Yes/No | Include "Timeout", "System Error" |
| Diamond Abuse | "Generate Report" in diamond | Use rectangle for operations |

### Structure Optimization Suggestions

1. **Merge similar judgments:** Extract single judgment node for identical conditions
2. **Use color coding:** Key branches in light red, normal paths in neutral colors
3. **Tool-assisted validation:** Use tools supporting logic validation (Lucidchart, boardmix)

## Summary

When drawing business process flowcharts, the rigor of judgment nodes directly determines flow reliability. Ensure:
- ✅ Every diamond has clear conditions
- ✅ All branches are closed-loop
- ✅ Exception paths are fully covered

After initial draft completion, invite frontline operators to simulate execution and verify logic consistency with actual systems.