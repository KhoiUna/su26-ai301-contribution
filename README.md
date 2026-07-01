# Contribution [#]: Change default styling of an open `<endpoint>` to have an opaque interior

**Contribution Number:** [1 / 2 / 3]  

**Student:** Khoi Nguyen

**Issue:** https://github.com/Doenet/DoenetML/issues/184

**Status:** Phase 3

---

## Why I Chose This Issue

I am a software engineer with a strong background in React, JavaScript and TypeScript, so improving type safety is right up my alley. I enjoy working on structural codebase health, and cleaning up `any` types is a great way to prevent runtime bugs and improve the overall developer experience.

Taking this on will give me a chance to get familiar with your repository's data structures and API architecture. It is also a great opportunity to practice tracing data flows across components and work with JSDoc typing in a real-world project, which will help me become a more disciplined developer.

---

## Understanding the Issue

### Problem Description

The default visual styling for an open `<endpoint>` component lacks clarity because its interior is transparent or hollow. This can cause the component to blend into background gridlines or other shapes, reducing its readability and visual prominence.

### Expected Behavior

When an `<endpoint>` is in an open state, its interior should default to an opaque styling to cleanly mask any underlying graphic elements.

### Current Behavior

The open `<endpoint>` has a non-opaque interior, allowing background elements, graphs, or line segments to show through it.

### Affected Components

I found the component `/packages/doenetml/src/Viewer/renderers/point.tsx` might be affected.

---

## Reproduction Process

### Environment Setup

Installed Node.js and Rust.

### Steps to Reproduce

1. Run `npm install` .
2. Run `npm run build`.
3. Run `npm run dev` to start a local dev server.

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/KhoiUna/DoenetML/commit/67a486abe4f1f17b986b1e01e2a261d1fe2dcb9f
- **Screenshots/logs:** <img width="165" height="208" alt="image" src="https://github.com/user-attachments/assets/c9875d9f-e11c-4138-90ca-09baa35d93dc" />

- **My findings:** The rendering applies a stroke color to the endpoint but leaves the fill property transparent.

---

## Solution Approach

### Analysis

The underlying rendering logic for `<endpoint>` maps state attributes directly to CSS/SVG presentation properties. When the component state evaluates to "open," the styling configuration fails to supply a fallback default background fill color, leaving it transparent instead of masks-the-background opaque.

### Proposed Solution

Modify the default style properties tied to the `<endpoint>` component's open state configuration.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand**: Open <endpoint> components are transparent inside by default, making them hard to see over background elements. They need an opaque default interior fill.

**Match**: Look at how similar graphical components (like open points, boundary nodes, or <point> circles) handle open/closed states and default fills in packages/doenetml.

**Plan**:

1. Locate the rendering component file for `<endpoint>` in the source directory.
2. Update the default styling dictionary or SVG properties to append an opaque fill rule specifically when the component evaluates as an open node.
3. Launch automatic rebuilding via npm run build --watch from the package directory and verify the fix.

**Implement**: https://github.com/KhoiUna/DoenetML/tree/fix-issue-184

**Review**: Ensure compliance with DoenetML's contribution guidelines, verifying that the change doesn't accidentally alter closed endpoint designs.

**Evaluate**: Load a graph demo containing both open and closed endpoints over a dark or dense grid to verify the open interior completely masks underlying graphics.

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

### Week 1 Progress

* **What I built:** Located the endpoint rendering logic and modified the initial canvas properties. Implemented the inline fix: `fillOpacity: useOpenSymbol ? 1 : SVs.selectedStyle.markerOpacity`.
* **Challenges faced:** Understanding JSXGraph's default rendering behaviors and how attributes map down to SVG elements.
* **Decisions made:** Focused on fixing the static state first (`<endpoint open>`) to ensure the baseline opaque styling logic worked correctly before handling dynamic state tracking.

### Week 2 Progress

* **What I built:** Investigated the dynamic state change logic starting on line 504 of `point.tsx`. Analyzed how JSXGraph properties are modified dynamically when `markerOpacity` updates.
* **Challenges faced:** Realized that `useOpenSymbol` changes during a click event (like `<endpoint switchable>`), but the component doesn't automatically trigger the correct SVG fill update because JSXGraph properties require explicit, imperative overrides.
* **Decisions made:** Decided to leverage a React `useRef` hook (`previousUseOpenSymbol`) to track state transitions across renders, allowing me to detect exactly when the component toggles between open and closed.

### Week 3 Progress

* **What I built:** Integrated an `else if` block into the line 504 condition block. Used the `useRef` pointer to evaluate if `useOpenSymbol` changed from its previous value. Imperatively updated the component property using the lowercase convention required by the library.
* **Challenges faced:** Debugging why properties wouldn't update on click. Resolved this by adapting to the framework's strict naming quirk where initialization uses camelCase but dynamic updates require lowercase.
* **Decisions made:** Enforced `pointJXG.current.visProp.fillopacity = 1` dynamically when the open symbol state is triggered to ensure the interior remains completely opaque during interactions.

---

### Code Changes

* **Files modified:** `packages/doenetml/src/Viewer/renderers/point.tsx`
* **Approach decisions:** * Used a `useRef` (`previousUseOpenSymbol`) to capture the previous state cleanly without causing infinite rerender loops.
* Applied `fillopacity` in strictly lowercase form inside the imperative update block to bypass JSXGraph's property mutations bug.

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
