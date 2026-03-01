# Accessibility Improvement Roadmap
## Sopra Steria ALMTech Radar

This document provides a prioritized, actionable roadmap for implementing accessibility improvements to the Tech Radar application.

---

## Quick Reference: Prioritized Issues

| Priority | Issue | WCAG Level | Effort | Impact |
|----------|-------|------------|--------|--------|
| 🔴 P0 | Add keyboard navigation to SVG radar | A | High | Critical |
| 🔴 P0 | Add ARIA labels to all interactive elements | A | Medium | Critical |
| 🔴 P0 | Implement visible focus indicators | AA | Low | Critical |
| 🟠 P1 | Fix color contrast (background/text) | AA | Low | High |
| 🟠 P1 | Make blips keyboard accessible | A | Medium | High |
| 🟠 P1 | Add comprehensive screen reader support | A | High | High |
| 🟡 P2 | Improve touch target sizes | AAA | Medium | Medium |
| 🟡 P2 | Add warnings for new tab links | A | Low | Medium |
| 🟡 P2 | Fix truncated text accessibility | A | Low | Medium |
| 🟢 P3 | Add skip navigation link | A | Low | Low |
| 🟢 P3 | Fix language attributes | A | Low | Low |
| 🟢 P3 | Improve logo link context | A | Low | Low |

---

## Detailed Issue Specifications

### ISSUE #1: Add Keyboard Navigation to SVG Radar Elements
**Priority:** P0 (Critical)  
**WCAG:** 2.1.1 Level A  
**Effort:** High (8-12 hours)  
**Labels:** `accessibility`, `critical`, `keyboard-navigation`, `wcag-a`

#### Description
The SVG radar visualization lacks keyboard navigation support, preventing keyboard-only users and screen reader users from accessing the interactive elements.

#### Acceptance Criteria
- [ ] All legend items are keyboard focusable (tabindex="0")
- [ ] All blips on the radar are keyboard focusable
- [ ] Focus moves logically through elements (quadrant by quadrant)
- [ ] Enter and Space keys activate links
- [ ] Focus/blur events show tooltips (same as mouse hover)
- [ ] Tab order follows logical reading order
- [ ] Escape key can close expanded information if applicable

#### Technical Implementation
**Files to modify:**
- `radar.js` - lines 352-388 (legend items)
- `radar.js` - lines 497-545 (blips)

**Code changes required:**

```javascript
// For legend items
.attr("tabindex", "0")
.attr("role", "button")
.on("focus", function (d) { showBubble(d); highlightLegendItem(d); })
.on("blur", function (d) { hideBubble(d); unhighlightLegendItem(d); })
.on("keydown", function(d) {
  if (d3.event.keyCode === 13 || d3.event.keyCode === 32) {
    if (d.link) {
      window.open(d.link, config.links_in_new_tabs ? '_blank' : '_self');
    }
    d3.event.preventDefault();
  }
});

// For blips
blips.attr("tabindex", "0")
     .attr("role", "button")
     .on("focus", function (d) { showBubble(d); })
     .on("blur", function (d) { hideBubble(d); })
     .on("keydown", function(d) {
       if (d3.event.keyCode === 13 || d3.event.keyCode === 32) {
         if (d.link) {
           window.open(d.link, config.links_in_new_tabs ? '_blank' : '_self');
         }
         d3.event.preventDefault();
       }
     });
```

#### Testing Steps
1. Disconnect mouse
2. Use only keyboard (Tab, Shift+Tab, Enter, Space)
3. Verify all blips and legend items are reachable
4. Verify Enter/Space activate links
5. Test with NVDA/JAWS screen reader
6. Verify logical tab order

#### Related Issues
- Issue #2 (ARIA labels)
- Issue #3 (Focus indicators)

---

### ISSUE #2: Add Comprehensive ARIA Labels and Screen Reader Support
**Priority:** P0 (Critical)  
**WCAG:** 4.1.2 Level A, 1.3.1 Level A  
**Effort:** Medium (6-8 hours)  
**Labels:** `accessibility`, `critical`, `screen-reader`, `aria`, `wcag-a`

#### Description
Interactive SVG elements lack proper ARIA labels, preventing screen reader users from understanding the content and context of technologies in the radar.

#### Acceptance Criteria
- [ ] Main SVG has role="img" and descriptive aria-label
- [ ] SVG includes `<title>` and `<desc>` elements
- [ ] Each blip has comprehensive aria-label with:
  - Item number
  - Technology name
  - Ring name
  - Quadrant name
  - Status (New/Moved/Unchanged)
- [ ] Legend items have full context in aria-label
- [ ] Footer legend has semantic structure (role="list")
- [ ] All interactive elements announce their role properly

#### Technical Implementation
**Files to modify:**
- `index.html` - line 61 (SVG element)
- `radar.js` - lines 200-230 (SVG initialization)
- `radar.js` - lines 352-388 (legend items)
- `radar.js` - lines 497-545 (blips)
- `radar.js` - lines 318-324 (footer legend)

**Code changes required:**

```html
<!-- In index.html or via JavaScript -->
<svg id="radar" 
     role="img" 
     aria-label="Sopra Steria Technology Radar showing technology adoption across four quadrants and four rings"
     style="display: block; margin: auto">
  <title>Technology Radar Visualization</title>
  <desc>Interactive radar chart displaying technologies categorized into Infrastructuur & Platformen, Talen & Frameworks, Architectuur & Cultuur, and Ondersteuning & Tools. Each technology is placed in one of four rings: Gebruik, Probeer, Onderzoek, or Verminder.</desc>
  <!-- ... existing radar content ... -->
</svg>
```

```javascript
// For blips
.attr("aria-label", function(d) {
  var statusText = d.status === 1 ? "New" : d.status === 2 ? "Moved" : "Unchanged";
  return "Item " + d.id + ": " + d.label + " - " + statusText + 
         " technology in " + config.rings[d.ring].name + " ring, " +
         config.quadrants[d.quadrant].name + " quadrant" + 
         (d.link ? ". Press Enter to open documentation" : "");
})

// For legend items
.attr("aria-label", function(d) {
  var statusText = d.status === 1 ? "New" : d.status === 2 ? "Moved" : "Unchanged";
  return "Item " + d.id + ": " + d.label + 
         " - " + statusText + " in " + 
         config.rings[ring].name + " ring, " +
         config.quadrants[quadrant].name + " quadrant" +
         (d.link ? ". Press Enter to open" : "");
})
```

#### Testing Steps
1. Enable NVDA screen reader (Windows) or VoiceOver (Mac)
2. Navigate through radar using Tab key
3. Verify each element announces its full context
4. Verify status information is announced
5. Test with JAWS screen reader
6. Test mobile with TalkBack (Android) or VoiceOver (iOS)

#### Related Issues
- Issue #1 (Keyboard navigation)
- Issue #11 (Footer legend structure)

---

### ISSUE #3: Implement Visible Focus Indicators
**Priority:** P0 (Critical)  
**WCAG:** 2.4.7 Level AA  
**Effort:** Low (2-3 hours)  
**Labels:** `accessibility`, `critical`, `focus`, `css`, `wcag-aa`

#### Description
The application lacks visible focus indicators, making it impossible for keyboard users to track which element has focus.

#### Acceptance Criteria
- [ ] All focusable elements have visible focus indicator
- [ ] Focus indicator has minimum 3px solid outline
- [ ] Focus indicator uses high-contrast color (not default blue)
- [ ] Focus indicator has adequate offset (2px minimum)
- [ ] Focus styles work across all browsers
- [ ] Focus is never hidden or opacity 0
- [ ] Focus-visible polyfill works for older browsers

#### Technical Implementation
**Files to modify:**
- `radar.css` - add new focus styles section

**Code changes required:**

```css
/* ===== FOCUS INDICATORS ===== */

/* Universal focus reset - remove default browser outlines */
*:focus {
    outline: none;
}

/* High-contrast focus indicator for all interactive elements */
*:focus-visible {
    outline: 3px solid #F47216; /* Orange accent */
    outline-offset: 2px;
}

/* Link focus styles */
a:focus-visible {
    outline: 3px solid #F47216;
    outline-offset: 2px;
    background-color: rgba(244, 114, 22, 0.15);
}

/* SVG element focus - for blips and legend items */
.blip:focus,
.legend-item:focus {
    outline: 3px solid #F47216;
    outline-offset: 3px;
    filter: drop-shadow(0 0 4px rgba(244, 114, 22, 0.8));
}

/* Button focus */
button:focus-visible {
    outline: 3px solid #F47216;
    outline-offset: 2px;
}

/* Details/summary focus for mobile */
summary:focus-visible {
    outline: 3px solid #F47216;
    outline-offset: 2px;
    background-color: rgba(244, 114, 22, 0.1);
}

/* Ensure focus never disappears */
:focus:not(:focus-visible) {
    outline: none;
}

/* High contrast mode support */
@media (prefers-contrast: high) {
    *:focus-visible {
        outline: 4px solid currentColor;
        outline-offset: 3px;
    }
}
```

#### Testing Steps
1. Navigate page using Tab key only
2. Verify focus indicator visible on every element
3. Check contrast of focus indicator against background
4. Test in Chrome, Firefox, Safari, Edge
5. Test with Windows High Contrast mode
6. Verify offset doesn't clip indicator

#### Dependencies
- Works best after Issue #1 (keyboard navigation) is implemented

---

### ISSUE #4: Fix Color Contrast Issues
**Priority:** P1 (High)  
**WCAG:** 1.4.3 Level AA, 1.4.6 Level AAA  
**Effort:** Low (1-2 hours)  
**Labels:** `accessibility`, `high-priority`, `color-contrast`, `wcag-aa`

#### Description
The gray background (#858383) and white text combination fails WCAG AA contrast requirements (4.2:1 vs required 4.5:1).

#### Acceptance Criteria
- [ ] Background/text contrast meets WCAG AA (4.5:1 minimum)
- [ ] Preferably meets WCAG AAA (7:1 for enhanced accessibility)
- [ ] All text remains readable
- [ ] Brand colors preserved where possible
- [ ] Verified with contrast analyzer tool

#### Technical Implementation
**Files to modify:**
- `radar.css` - lines 1-11 (CSS variables)

**Option 1: Darken background (Recommended)**
```css
:root {
    --kleur-achtergrond: #5a5858; /* Darker gray - 7.3:1 contrast with white */
    --kleur-tekst: white;
}
```

**Option 2: Add text shadow for better legibility**
```css
:root {
    --kleur-achtergrond: #858383;
    --kleur-tekst: white;
}

body, h1, h2, h3, p, li {
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
}
```

**Option 3: Use darker text on lighter background**
```css
:root {
    --kleur-achtergrond: #e8e8e8; /* Light gray */
    --kleur-tekst: #1a1a1a; /* Very dark gray */
}
```

#### Testing Steps
1. Use WebAIM Contrast Checker
2. Test with Color Contrast Analyzer app
3. Verify all text passes WCAG AA
4. Test with browser zoom at 200%
5. Test in bright sunlight (mobile)
6. Get stakeholder approval for color change

#### Contrast Calculations
- Current: #858383 vs white = 4.2:1 (FAIL AA)
- Option 1: #5a5858 vs white = 7.3:1 (PASS AAA)
- Option 2: #858383 vs white with shadow = Improved readability
- Option 3: #e8e8e8 vs #1a1a1a = 12.6:1 (PASS AAA)

---

### ISSUE #5: Make Individual Blips Keyboard Accessible with Improved Interaction
**Priority:** P1 (High)  
**WCAG:** 2.1.1 Level A  
**Effort:** Medium (4-6 hours)  
**Labels:** `accessibility`, `high-priority`, `keyboard`, `svg`, `wcag-a`

#### Description
Individual technology blips on the radar cannot be accessed via keyboard, preventing non-mouse users from exploring the visualization interactively.

#### Acceptance Criteria
- [ ] Each blip has tabindex="0"
- [ ] Each blip has role="button"
- [ ] Blips receive focus in logical order
- [ ] Focus shows tooltip/bubble (same as hover)
- [ ] Enter/Space key opens technology link
- [ ] Blips have comprehensive aria-label
- [ ] Visual focus indicator on focused blip

#### Technical Implementation
**Files to modify:**
- `radar.js` - lines 497-545 (blips.each function)

**Code changes required:**

```javascript
// In blips.each function, after selecting blip
blips.each(function (d) {
    var blip = d3.select(this);
    
    // Make keyboard accessible
    blip.attr("tabindex", "0")
        .attr("role", "button")
        .attr("aria-label", function() {
            var statusText = d.status === 1 ? "Nieuw" : 
                           d.status === 2 ? "Verplaatst" : "Onveranderd";
            return "Item " + d.id + ": " + d.label + 
                   " - " + statusText + " technologie in " + 
                   config.rings[d.ring].name + " ring, " +
                   config.quadrants[d.quadrant].name + " kwadrant" +
                   (d.link ? ". Druk op Enter om te openen" : "");
        });
    
    // Add keyboard event handlers
    blip.on("focus", function() { 
        showBubble(d); 
        // Add visual emphasis
        d3.select(this).style("filter", "drop-shadow(0 0 4px rgba(244, 114, 22, 0.8))");
    })
    .on("blur", function() { 
        hideBubble(d); 
        // Remove visual emphasis
        d3.select(this).style("filter", null);
    })
    .on("keydown", function() {
        if (d3.event.keyCode === 13 || d3.event.keyCode === 32) {
            if (d.link) {
                window.open(d.link, config.links_in_new_tabs ? '_blank' : '_self');
            }
            d3.event.preventDefault();
        }
    });
    
    // Keep existing mouseover/mouseout for mouse users
    blip.on("mouseover", function() { showBubble(d); })
        .on("mouseout", function() { hideBubble(d); });
    
    // ... rest of blip rendering (shapes, text)
});
```

#### Testing Steps
1. Tab through all blips on radar
2. Verify logical focus order (by quadrant/ring)
3. Verify tooltip appears on focus
4. Press Enter on focused blip with link
5. Verify link opens correctly
6. Test with screen reader
7. Verify aria-label announces full context

---

### ISSUE #6: Add Comprehensive Screen Reader Experience Documentation
**Priority:** P1 (High)  
**WCAG:** Multiple (supporting documentation)  
**Effort:** Medium (4-5 hours)  
**Labels:** `accessibility`, `documentation`, `screen-reader`

#### Description
Create user guide for screen reader users explaining how to navigate and use the Tech Radar.

#### Acceptance Criteria
- [ ] Document created with screen reader navigation guide
- [ ] Keyboard shortcuts documented
- [ ] Known limitations documented
- [ ] Alternative access methods explained (mobile list view)
- [ ] Testing results with popular screen readers included

#### Deliverables
- `ACCESSIBILITY_USER_GUIDE.md` - User-facing documentation
- Update `README.md` with accessibility section
- Add accessibility statement to `index.html`

---

### ISSUE #7: Improve Touch Target Sizes for Mobile
**Priority:** P2 (Medium)  
**WCAG:** 2.5.5 Level AAA  
**Effort:** Medium (3-4 hours)  
**Labels:** `accessibility`, `mobile`, `touch`, `wcag-aaa`

#### Description
SVG blips are 18px diameter, below the WCAG AAA recommended 44x44px touch target size.

#### Acceptance Criteria
- [ ] Touch targets minimum 44x44px on mobile
- [ ] Invisible padding added to increase touch area
- [ ] No visual change to blip size
- [ ] Touch targets don't overlap
- [ ] Mobile list links have adequate padding

#### Technical Implementation

**For SVG blips:**
```javascript
// Add invisible larger touch target
if (d.hasOwnProperty("link") && d.link) {
    blip.append("circle")
        .attr("r", 22)  // 44px diameter
        .attr("fill", "transparent")
        .attr("pointer-events", "all")
        .style("cursor", "pointer");
}
```

**For mobile list links (radar.css):**
```css
.mobile-lists a {
    display: block;
    padding: 12px 8px;
    min-height: 44px;  /* Ensure WCAG AAA compliance */
    text-decoration: none;
}
```

#### Testing Steps
1. Test on actual mobile devices
2. Use finger to tap all blips
3. Verify no mis-taps on adjacent items
4. Test with users with motor disabilities
5. Measure actual touch target sizes

---

### ISSUE #8: Add Warnings for Links Opening in New Tabs
**Priority:** P2 (Medium)  
**WCAG:** 3.2.2 Level A  
**Effort:** Low (2-3 hours)  
**Labels:** `accessibility`, `links`, `wcag-a`

#### Description
Links that open in new tabs lack user warning and security attributes.

#### Acceptance Criteria
- [ ] All external links have `rel="noopener noreferrer"`
- [ ] Links indicate "opens in new tab" in aria-label
- [ ] Visual indicator (icon) for external links
- [ ] User can identify new-tab links before clicking

#### Technical Implementation

**For radar.js links:**
```javascript
.attr("rel", function(d, i) {
    return (d.link && config.links_in_new_tabs) ? "noopener noreferrer" : null;
})
.attr("aria-label", function(d) {
    var label = "Item " + d.id + ": " + d.label;
    if (d.link && config.links_in_new_tabs) {
        label += " (opent in nieuw tabblad)";
    }
    return label;
});
```

**For HTML links (index.html):**
```html
<a href="https://github.com/zalando/tech-radar" 
   target="_blank" 
   rel="noopener noreferrer"
   aria-label="Zalando TechRadar (opent in nieuw tabblad)">
   Zalando TechRadar <span aria-hidden="true">↗</span>
</a>
```

---

### ISSUE #9: Fix Truncated Text Accessibility
**Priority:** P2 (Medium)  
**WCAG:** 1.3.1 Level A  
**Effort:** Low (2 hours)  
**Labels:** `accessibility`, `truncation`, `wcag-a`

#### Description
Truncated legend text with "..." only shows full text on mouseover, inaccessible to keyboard users.

#### Acceptance Criteria
- [ ] Full text available via title attribute (native tooltip)
- [ ] Full text in aria-label for screen readers
- [ ] Visual text remains truncated for layout
- [ ] Keyboard users can access full text

#### Technical Implementation

```javascript
.each(function(d, i) {
    var fullText = d.id + ". " + d.label;
    var truncatedText = truncateText(fullText, config.legend_text_max_length);
    
    d3.select(this)
        .attr("data-full-text", fullText)
        .attr("data-truncated-text", truncatedText)
        .attr("title", fullText)  // Native browser tooltip
        .attr("aria-label", fullText)  // Screen readers get full text
        .text(truncatedText);
})
```

---

### ISSUE #10: Add Skip Navigation Link
**Priority:** P3 (Low)  
**WCAG:** 2.4.1 Level A  
**Effort:** Low (1 hour)  
**Labels:** `accessibility`, `navigation`, `wcag-a`

#### Description
Add skip-to-content link for keyboard users to bypass header.

#### Acceptance Criteria
- [ ] Skip link is first focusable element
- [ ] Skip link hidden until focused
- [ ] Skip link jumps to main content
- [ ] Visually appears when focused

#### Technical Implementation

**HTML (index.html):**
```html
<body>
    <a href="#main-content" class="skip-link">
        Spring naar hoofdinhoud
    </a>
    
    <!-- existing header -->
    
    <main id="main-content" role="main">
        <svg id="radar">...</svg>
        <!-- ... -->
    </main>
</body>
```

**CSS (radar.css):**
```css
.skip-link {
    position: absolute;
    top: -40px;
    left: 0;
    background: #F47216;
    color: white;
    padding: 8px 16px;
    text-decoration: none;
    z-index: 100;
    font-weight: bold;
}

.skip-link:focus {
    top: 0;
}
```

---

### ISSUE #11: Fix Language Attributes
**Priority:** P3 (Low)  
**WCAG:** 3.1.1, 3.1.2 Level A  
**Effort:** Low (1 hour)  
**Labels:** `accessibility`, `i18n`, `wcag-a`

#### Description
HTML declares lang="en" but content is Dutch.

#### Acceptance Criteria
- [ ] HTML lang attribute is "nl"
- [ ] English phrases marked with lang="en"
- [ ] Screen readers use correct pronunciation

#### Technical Implementation

```html
<!DOCTYPE html>
<html lang="nl">

<!-- Mark English sections -->
<em lang="en">
    <a href="https://www.thoughtworks.com/radar">ThoughtWorks</a>,
    <a href="https://github.com/zalando/tech-radar">Zalando TechRadar</a>
</em>
```

---

### ISSUE #12: Improve Logo Link Context
**Priority:** P3 (Low)  
**WCAG:** 2.4.4 Level A  
**Effort:** Low (15 minutes)  
**Labels:** `accessibility`, `links`, `wcag-a`

#### Description
Logo link lacks clear purpose indication.

#### Acceptance Criteria
- [ ] Logo link has descriptive aria-label
- [ ] Purpose clear without visual context

#### Technical Implementation

```html
<a href="https://www.soprasteria.nl/" 
   aria-label="Sopra Steria homepage">
    <img src="sopra-steria-logo.svg" alt="Sopra Steria logo">
</a>
```

---

## Implementation Timeline

### Week 1: Critical Fixes (P0)
- **Day 1-2:** Issue #1 - Keyboard navigation
- **Day 3:** Issue #2 - ARIA labels  
- **Day 4:** Issue #3 - Focus indicators
- **Day 5:** Testing and bug fixes

### Week 2: High Priority (P1)
- **Day 1:** Issue #4 - Color contrast
- **Day 2-3:** Issue #5 - Blip keyboard access
- **Day 4:** Issue #6 - Documentation
- **Day 5:** Testing and validation

### Week 3: Medium Priority (P2)
- **Day 1:** Issue #7 - Touch targets
- **Day 2:** Issue #8 - Link warnings
- **Day 3:** Issue #9 - Truncated text
- **Day 4-5:** Comprehensive testing

### Week 4: Low Priority (P3) + Final Testing
- **Day 1:** Issues #10, #11, #12
- **Day 2-3:** Full accessibility audit
- **Day 4:** Fix any remaining issues
- **Day 5:** Final testing & documentation

---

## Success Metrics

### Quantitative Metrics
- [ ] 100% keyboard navigation coverage
- [ ] WCAG 2.1 Level AA compliance: 100%
- [ ] Axe DevTools: 0 critical/serious issues
- [ ] Lighthouse accessibility score: >95
- [ ] Color contrast: All text ≥4.5:1 (AA)

### Qualitative Metrics
- [ ] Successful NVDA navigation test
- [ ] Successful JAWS navigation test
- [ ] Successful VoiceOver navigation test
- [ ] User testing with keyboard-only users
- [ ] User testing with screen reader users

---

## Testing Checklist

### Automated Testing
- [ ] Run Axe DevTools
- [ ] Run WAVE evaluation
- [ ] Run Lighthouse audit
- [ ] Run Pa11y CI
- [ ] Validate HTML
- [ ] Check color contrast

### Manual Testing
- [ ] Keyboard-only navigation
- [ ] NVDA screen reader (Windows)
- [ ] JAWS screen reader (Windows)
- [ ] VoiceOver screen reader (macOS)
- [ ] TalkBack (Android mobile)
- [ ] VoiceOver (iOS mobile)
- [ ] Zoom to 200%
- [ ] Windows High Contrast mode
- [ ] Browser zoom to 400%

### Cross-Browser Testing
- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge
- [ ] Mobile Safari
- [ ] Mobile Chrome

---

## Resources

### Development Tools
- [Axe DevTools Browser Extension](https://www.deque.com/axe/devtools/)
- [WAVE Evaluation Tool](https://wave.webaim.org/extension/)
- [Color Contrast Analyzer](https://www.tpgi.com/color-contrast-checker/)

### Screen Readers
- [NVDA (Free)](https://www.nvaccess.org/download/)
- [JAWS (Trial)](https://www.freedomscientific.com/products/software/jaws/)
- VoiceOver (Built into macOS/iOS)
- TalkBack (Built into Android)

### Documentation
- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM Articles](https://webaim.org/articles/)
- [Deque University](https://dequeuniversity.com/)
- [A11y Project](https://www.a11yproject.com/)

---

**Last Updated:** February 17, 2026  
**Total Estimated Effort:** 36-54 hours  
**Target Completion:** 4 weeks from start
