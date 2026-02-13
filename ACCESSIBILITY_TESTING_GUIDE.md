# Accessibility Testing Quick Guide

This guide helps you quickly test the accessibility improvements made to the Tech Radar application.

## Quick Keyboard Navigation Test

### 1. Skip Link
1. Load the page
2. Press **Tab** once
3. You should see: "Ga naar hoofdinhoud"
4. Press **Enter** - focus should jump to main content

✅ **Expected**: Skip link visible on focus and works correctly

### 2. Navigate Through Radar
1. Continue pressing **Tab** to move through interactive elements
2. You should see a clear **3px black outline** on focused elements
3. Navigate to a radar blip or legend item
4. Press **Enter** or **Space** to activate

✅ **Expected**: All elements focusable, focus visible, keyboard activation works

### 3. Mobile View (resize to < 640px)
1. Resize browser to mobile width
2. Press **Tab** to navigate through list items
3. Test collapsible sections
4. Verify links are easy to click/tap

✅ **Expected**: All links focusable with good touch targets

---

## Screen Reader Test (Basic)

### Windows (NVDA - Free)
1. Download NVDA: https://www.nvaccess.org/download/
2. Start NVDA
3. Load the Tech Radar page
4. Press **Insert + Down Arrow** to read page
5. Use **Tab** to navigate interactive elements
6. Listen to announcements for each blip and legend item

✅ **Expected**: 
- Page language announced as Dutch
- Skip link announced
- SVG described as "Tech Radar visualisatie"
- Each blip announces: number, name, ring, quadrant, status
- External links announce "opent in nieuw tabblad"

### Mac (VoiceOver - Built-in)
1. Press **Cmd + F5** to start VoiceOver
2. Load the Tech Radar page
3. Press **Cmd + Shift + Down Arrow** to start reading
4. Use **Tab** to navigate
5. Press **Control + Option + Space** to activate

✅ **Expected**: Same as NVDA test above

---

## Browser DevTools Test

### Chrome/Edge Lighthouse
1. Open DevTools (**F12**)
2. Go to **Lighthouse** tab
3. Check **Accessibility**
4. Click **Analyze page load**
5. Review score and issues

✅ **Expected**: Score ≥ 90, minimal issues

### Chrome/Edge Accessibility Tree
1. Open DevTools (**F12**)
2. Go to **Elements** tab
3. Click **Accessibility** panel (bottom pane)
4. Inspect SVG and interactive elements
5. Verify ARIA labels and roles are present

✅ **Expected**: 
- SVG has role="img" and accessible name
- Blips have role="button" and accessible name
- Legend items have role="button" and accessible name

---

## Automated Testing Tools

### axe DevTools (Browser Extension)
1. Install: Chrome/Firefox/Edge extension
2. Open DevTools, go to **axe DevTools** tab
3. Click **Scan ALL of my page**
4. Review violations

✅ **Expected**: 0 critical issues, minimal minor issues

### WAVE (Web Accessibility Evaluation Tool)
1. Visit: https://wave.webaim.org/
2. Enter your Tech Radar URL
3. Review errors and alerts

✅ **Expected**: 0 errors, alerts only for design considerations

---

## Visual Focus Test

### Test Focus Indicators
1. Load page
2. Press **Tab** repeatedly
3. Watch for focus indicator on each element:
   - Skip link: Black outline
   - Header logo link: Black outline
   - SVG blips: Black outline
   - Legend items: Black outline
   - Page links: Black outline
   - Mobile links: Black outline with background

✅ **Expected**: Every interactive element shows clear focus indicator

### Test High Contrast Mode (Windows)
1. Press **Left Alt + Left Shift + Print Screen**
2. Confirm to enable High Contrast
3. Review page - focus indicators should still be visible

✅ **Expected**: Page remains usable and focus visible

---

## Mobile Touch Target Test

### Using Chrome DevTools Device Mode
1. Open DevTools (**F12**)
2. Toggle device toolbar (**Ctrl + Shift + M**)
3. Select a mobile device (e.g., iPhone 12)
4. Navigate to mobile list view
5. Hover over links - verify they're easy to tap

✅ **Expected**: 
- Links have minimum 44px height
- Adequate padding (12px)
- Clear touch area

---

## Color Contrast Check

### Using Chrome DevTools
1. Open DevTools (**F12**)
2. Go to **Elements** tab
3. Select text elements
4. Check **Styles** panel for contrast ratio
5. Look for ⚠️ or ✓ indicators

✅ **Expected**: Most text meets 4.5:1 ratio (some brand colors may be borderline)

### Using Online Tool
1. Visit: https://webaim.org/resources/contrastchecker/
2. Test these combinations:
   - White text on #858383 background
   - Black text on #858383 background
   - Green (#66d08f) on #858383
   - Orange (#F47216) on #858383
   - Red (#DB1D2B) on #858383

⚠️ **Note**: Some combinations may be borderline - this is documented in ACCESSIBILITY_REPORT.md

---

## Quick Feature Checklist

Test these specific accessibility features:

- [ ] Skip navigation link appears on first Tab and works
- [ ] HTML lang attribute is "nl" (Dutch)
- [ ] Page has header and main landmarks
- [ ] SVG has role="img" with aria-label
- [ ] All blips are keyboard accessible (Tab, Enter/Space)
- [ ] All legend items are keyboard accessible
- [ ] Focus indicators are clearly visible (3px black outline)
- [ ] ARIA labels announce item number, name, ring, quadrant, status
- [ ] External links announce "opent in nieuw tabblad"
- [ ] External links have rel="noopener noreferrer"
- [ ] Mobile list view has proper ARIA labels
- [ ] Mobile touch targets are ≥44px
- [ ] Details/summary elements work with keyboard
- [ ] Keyboard navigation matches mouse navigation (hover = focus)

---

## Common Issues to Watch For

### ❌ Issues that Should NOT Occur
1. **Focus indicator missing** - Every interactive element should show focus
2. **Skip link not working** - Should jump to main content
3. **Keyboard trap** - User should be able to Tab through entire page
4. **Missing ARIA labels** - Screen reader should announce element purpose
5. **Broken keyboard navigation** - Enter/Space should activate elements

### ✓ Expected Minor Issues
1. **Color contrast warnings** - Some brand colors have borderline contrast (documented)
2. **Small font sizes** - Some text is small but readable (documented)
3. **SVG complexity** - D3.js visualization is inherently complex

---

## Regression Testing

When making future changes, verify:

1. **Don't remove ARIA attributes** from blips or legend items
2. **Don't remove tabindex** from interactive elements
3. **Don't remove keyboard event handlers** (keydown for Enter/Space)
4. **Don't remove focus/blur handlers** from interactive elements
5. **Don't remove semantic HTML** (header, main, role attributes)
6. **Don't remove skip link** or hide it permanently

---

## Reporting Issues

If you find accessibility issues after testing:

1. **Identify the issue**: What doesn't work? For whom?
2. **WCAG reference**: Which guideline does it violate?
3. **Steps to reproduce**: How to observe the issue
4. **Expected vs actual**: What should happen vs what does happen
5. **Priority**: Critical (Level A) / High (Level AA) / Medium / Low

---

## Resources

### Testing Tools
- **NVDA**: https://www.nvaccess.org/download/
- **axe DevTools**: https://www.deque.com/axe/devtools/
- **WAVE**: https://wave.webaim.org/
- **Lighthouse**: Built into Chrome/Edge DevTools
- **Contrast Checker**: https://webaim.org/resources/contrastchecker/

### Guidelines
- **WCAG 2.1**: https://www.w3.org/WAI/WCAG21/quickref/
- **ARIA Practices**: https://www.w3.org/WAI/ARIA/apg/
- **MDN Accessibility**: https://developer.mozilla.org/en-US/docs/Web/Accessibility

### For Detailed Information
- See **ACCESSIBILITY_REPORT.md** for comprehensive analysis
- See **ACCESSIBILITY_IMPLEMENTATION.md** for implementation details

---

**Last Updated**: February 2026  
**Application**: Sopra Steria ALMTech Tech Radar
