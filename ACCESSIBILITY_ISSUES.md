# Accessibility Issues Summary
## Quick Reference for Creating GitHub Issues

This document provides a condensed summary of accessibility issues found in the Tech Radar application, formatted for easy creation of GitHub issues.

---

## Critical Priority Issues (P0)

### 🔴 Issue #1: Add Keyboard Navigation to SVG Radar
**Labels:** `accessibility`, `critical`, `keyboard-navigation`, `wcag-a`  
**WCAG:** 2.1.1 Level A | **Effort:** 8-12 hours

**Problem:** SVG radar lacks keyboard navigation - keyboard-only users cannot interact with blips or legend items.

**Solution:**
- Add `tabindex="0"` to all interactive elements
- Add `role="button"` for semantics
- Implement focus/blur event handlers
- Add Enter/Space key activation

**Files:** `radar.js` lines 352-388, 497-545

---

### 🔴 Issue #2: Add ARIA Labels to All Interactive Elements
**Labels:** `accessibility`, `critical`, `screen-reader`, `aria`, `wcag-a`  
**WCAG:** 4.1.2, 1.3.1 Level A | **Effort:** 6-8 hours

**Problem:** Screen readers cannot understand SVG radar content - no semantic labels.

**Solution:**
- Add `role="img"` and `aria-label` to main SVG
- Add comprehensive aria-labels to all blips (name, ring, quadrant, status)
- Add aria-labels to legend items
- Include `<title>` and `<desc>` in SVG

**Files:** `index.html` line 61, `radar.js` lines 200-230, 352-388, 497-545

---

### 🔴 Issue #3: Implement Visible Focus Indicators
**Labels:** `accessibility`, `critical`, `focus`, `css`, `wcag-aa`  
**WCAG:** 2.4.7 Level AA | **Effort:** 2-3 hours

**Problem:** No visible focus indicators - keyboard users cannot see where focus is.

**Solution:**
- Add `:focus-visible` styles with 3px solid outline
- Use high-contrast orange (#F47216) for focus
- Add 2px outline-offset
- Apply to all interactive elements

**Files:** `radar.css` - new section

---

## High Priority Issues (P1)

### 🟠 Issue #4: Fix Color Contrast (Background/Text)
**Labels:** `accessibility`, `high-priority`, `color-contrast`, `wcag-aa`  
**WCAG:** 1.4.3 Level AA | **Effort:** 1-2 hours

**Problem:** Background #858383 vs white text = 4.2:1 (fails WCAG AA minimum 4.5:1)

**Solution:**
- Change `--kleur-achtergrond` to #5a5858 (achieves 7.3:1 contrast)
- OR add text-shadow for better legibility
- Verify with contrast analyzer

**Files:** `radar.css` lines 1-11

---

### 🟠 Issue #5: Make Blips Keyboard Accessible
**Labels:** `accessibility`, `high-priority`, `keyboard`, `svg`, `wcag-a`  
**WCAG:** 2.1.1 Level A | **Effort:** 4-6 hours

**Problem:** Individual blips cannot be focused or activated via keyboard.

**Solution:**
- Add tabindex, role, and aria-label to each blip
- Implement focus/blur handlers
- Add keyboard activation (Enter/Space)
- Show tooltip on focus

**Files:** `radar.js` lines 497-545

---

### 🟠 Issue #6: Create Screen Reader User Guide
**Labels:** `accessibility`, `documentation`, `screen-reader`  
**Effort:** 4-5 hours

**Problem:** No documentation on how to navigate the radar with assistive technologies.

**Solution:**
- Create `ACCESSIBILITY_USER_GUIDE.md`
- Document keyboard shortcuts
- Explain screen reader navigation
- Add accessibility statement to main page

**Files:** New file, update `README.md` and `index.html`

---

## Medium Priority Issues (P2)

### 🟡 Issue #7: Improve Touch Target Sizes
**Labels:** `accessibility`, `mobile`, `touch`, `wcag-aaa`  
**WCAG:** 2.5.5 Level AAA | **Effort:** 3-4 hours

**Problem:** Blips are 18px diameter (WCAG AAA requires 44x44px touch targets)

**Solution:**
- Add invisible 44px circle around blips for larger touch area
- Ensure mobile list links have min-height: 44px
- Add adequate padding

**Files:** `radar.js` line 529, `radar.css` mobile section

---

### 🟡 Issue #8: Add Warnings for New Tab Links
**Labels:** `accessibility`, `links`, `wcag-a`  
**WCAG:** 3.2.2 Level A | **Effort:** 2-3 hours

**Problem:** Links open in new tabs without warning; missing security attributes.

**Solution:**
- Add `rel="noopener noreferrer"` to all external links
- Include "(opens in new tab)" in aria-labels
- Add visual indicator (↗ icon)

**Files:** `radar.js` lines 358, 511; `index.html` lines 127-128

---

### 🟡 Issue #9: Fix Truncated Text Accessibility
**Labels:** `accessibility`, `truncation`, `wcag-a`  
**WCAG:** 1.3.1 Level A | **Effort:** 2 hours

**Problem:** Truncated text ("...") only shows full version on mouseover.

**Solution:**
- Add `title` attribute for native browser tooltip
- Add full text in `aria-label`
- Keep visual truncation for layout
- Works for keyboard and screen reader users

**Files:** `radar.js` lines 374-382

---

### 🟡 Issue #10: Structure Legend Footer Semantically
**Labels:** `accessibility`, `semantic-html`, `wcag-a`  
**WCAG:** 1.3.1 Level A | **Effort:** 2-3 hours

**Problem:** Footer legend ("■ Nieuw ▲ Verplaatst ● Onveranderd") is plain text without structure.

**Solution:**
- Convert to semantic list with role="list"
- Separate each status into individual items
- Add proper aria-labels for symbols

**Files:** `radar.js` lines 318-324

---

## Low Priority Issues (P3)

### 🟢 Issue #11: Add Skip Navigation Link
**Labels:** `accessibility`, `navigation`, `wcag-a`  
**WCAG:** 2.4.1 Level A | **Effort:** 1 hour

**Problem:** No way to skip header and jump to main content.

**Solution:**
- Add skip link as first focusable element
- Visually hidden until focused
- Jumps to `#main-content`

**Files:** `index.html`, `radar.css`

---

### 🟢 Issue #12: Fix Language Attributes
**Labels:** `accessibility`, `i18n`, `wcag-a`  
**WCAG:** 3.1.1, 3.1.2 Level A | **Effort:** 1 hour

**Problem:** HTML declares `lang="en"` but content is Dutch.

**Solution:**
- Change `<html lang="nl">`
- Mark English phrases with `lang="en"`

**Files:** `index.html` line 32

---

### 🟢 Issue #13: Improve Logo Link Context
**Labels:** `accessibility`, `links`, `wcag-a`  
**WCAG:** 2.4.4 Level A | **Effort:** 15 minutes

**Problem:** Logo link purpose unclear from alt text alone.

**Solution:**
- Add `aria-label="Sopra Steria homepage"` to link

**Files:** `index.html` lines 55-57

---

## Issue Creation Template

When creating GitHub issues from this list, use this template:

```markdown
## Description
[Problem statement from above]

## WCAG Guideline
**Level:** [A/AA/AAA]  
**Criteria:** [Number and name]

## Current Behavior
[Describe what currently happens]

## Expected Behavior
[Describe accessible behavior]

## Impact
- [ ] Keyboard-only users
- [ ] Screen reader users
- [ ] Mobile users
- [ ] Users with low vision
- [ ] Users with motor disabilities

## Implementation
[Solution from above]

### Files to Change
[List files and line numbers]

### Code Example
```[language]
[Code snippet from detailed roadmap]
```

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

## Testing Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Related Issues
- #[number] - [title]

## Labels
`accessibility`, `[priority]`, `[category]`, `[wcag-level]`

## Estimate
[hours] hours
```

---

## Prioritization Logic

### Critical (P0) - Must Fix First
- **Keyboard navigation** - Without this, app is unusable for many users
- **ARIA labels** - Required for screen reader access
- **Focus indicators** - Required to see keyboard navigation

### High (P1) - Fix Soon
- **Color contrast** - Quick win, improves readability for all
- **Blip keyboard access** - Extends keyboard nav to full feature set
- **Documentation** - Helps users understand accessibility features

### Medium (P2) - Important but Not Blocking
- **Touch targets** - Mobile UX improvement (AAA, not required for AA)
- **Link warnings** - UX and security improvement
- **Truncation** - Ensures feature parity
- **Footer structure** - Semantic correctness

### Low (P3) - Nice to Have
- **Skip link** - Common pattern but workaround exists (Tab)
- **Language** - Mainly affects pronunciation
- **Logo context** - Minor improvement

---

## Quick Stats

| Priority | Count | Total Effort |
|----------|-------|--------------|
| P0 (Critical) | 3 | 16-23 hours |
| P1 (High) | 3 | 11-15 hours |
| P2 (Medium) | 4 | 9-12 hours |
| P3 (Low) | 3 | 2-3 hours |
| **TOTAL** | **13** | **38-53 hours** |

---

## Recommended Approach

1. **Week 1:** Fix all P0 issues (#1-3)
2. **Week 2:** Fix all P1 issues (#4-6)
3. **Week 3:** Fix P2 issues (#7-10)
4. **Week 4:** Fix P3 issues (#11-13) + comprehensive testing

**Total timeline: 4 weeks**

---

**Document Created:** February 17, 2026  
**For Use:** Creating GitHub issues for accessibility improvements  
**Reference:** See `ACCESSIBILITY_ANALYSIS.md` and `ACCESSIBILITY_IMPROVEMENT_ROADMAP.md` for full details
