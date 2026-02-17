# Accessibility Analysis Report
## Sopra Steria ALMTech Radar

**Analysis Date:** February 17, 2026  
**Analyst:** Automated Accessibility Review  
**Standards Referenced:** WCAG 2.1 Level AA & AAA

---

## Executive Summary

This report provides a comprehensive accessibility analysis of the Sopra Steria ALMTech Radar web application. The analysis identifies critical barriers preventing users with disabilities from fully accessing and interacting with the technology radar visualization.

### Key Findings
- **Critical Issues:** 3 (primarily keyboard accessibility)
- **High Priority Issues:** 4 (color contrast, focus indicators, touch targets)
- **Medium Priority Issues:** 4 (ARIA labels, link warnings, truncation)
- **Low Priority Issues:** 3 (language attributes, skip links, image context)

### Overall Assessment
The application has significant accessibility gaps that prevent keyboard-only users and screen reader users from effectively navigating the radar visualization. While the mobile list view provides some accessibility, the main SVG radar lacks fundamental accessibility features required by WCAG 2.1 Level AA.

---

## Critical Issues (Must Fix)

### 1. SVG Radar Not Accessible to Screen Readers
**Severity:** CRITICAL  
**WCAG Guidelines:** 1.3.1 (Info and Relationships), 4.1.2 (Name, Role, Value)  
**Location:** `index.html` line 61, `radar.js` SVG generation

#### Problem
The main SVG radar visualization lacks semantic information for assistive technologies:
- No `role="img"` or `role="application"` attribute
- No `aria-label` describing the radar's purpose
- Missing `<title>` and `<desc>` elements within the SVG
- Interactive elements (blips, legend items) are not properly exposed to screen readers

#### Current Code
```html
<svg id="radar" style="display: block; margin: auto"></svg>
```

#### Impact
- Screen reader users cannot understand what the SVG represents
- No alternative way to access the radar data visualization
- Fails WCAG 2.1 Level A compliance

#### Recommendation
Add proper SVG accessibility attributes:
```html
<svg id="radar" 
     role="img" 
     aria-label="Sopra Steria Technology Radar - Interactive visualization showing technology adoption status across four quadrants"
     style="display: block; margin: auto">
  <title>Technology Radar</title>
  <desc>Interactive radar chart showing technologies categorized into four quadrants and four rings representing adoption stages</desc>
</svg>
```

---

### 2. No Keyboard Navigation Support for Interactive SVG Elements
**Severity:** CRITICAL  
**WCAG Guidelines:** 2.1.1 (Keyboard Accessible), 2.1.2 (No Keyboard Trap)  
**Location:** `radar.js` lines 387-388, 497-498

#### Problem
All interactive elements in the radar rely exclusively on mouse events:
- Legend items only respond to `mouseover`/`mouseout` events
- Blips (technology markers) cannot be focused or activated via keyboard
- No `tabindex` attributes on SVG interactive elements
- Missing keyboard event handlers (focus, blur, keydown)

#### Current Code
```javascript
// Legend items - mouse-only interaction
.on("mouseover", function (d) { showBubble(d); highlightLegendItem(d); })
.on("mouseout", function (d) { hideBubble(d); unhighlightLegendItem(d); });
```

#### Impact
- Keyboard-only users cannot interact with the radar
- Screen reader users cannot access technology information
- Fails WCAG 2.1 Level A compliance
- Violates Section 508 standards

#### Recommendation
Add keyboard support to all interactive elements:
```javascript
// Add to legend items and blips
.attr("tabindex", "0")
.attr("role", "button")
.attr("aria-label", function(d) { 
  return d.id + ". " + d.label + " in " + config.rings[d.ring].name + " ring, " + 
         config.quadrants[d.quadrant].name + " quadrant. Status: " + 
         (d.status === 1 ? "New" : d.status === 2 ? "Moved" : "Unchanged");
})
.on("focus", function (d) { showBubble(d); highlightLegendItem(d); })
.on("blur", function (d) { hideBubble(d); unhighlightLegendItem(d); })
.on("keydown", function(d) {
  if (d3.event.keyCode === 13 || d3.event.keyCode === 32) { // Enter or Space
    if (d.link) window.open(d.link, config.links_in_new_tabs ? '_blank' : '_self');
    d3.event.preventDefault();
  }
});
```

---

### 3. Blips Lack Keyboard Focus and ARIA Labels
**Severity:** CRITICAL  
**WCAG Guidelines:** 2.1.1 (Keyboard), 4.1.2 (Name, Role, Value)  
**Location:** `radar.js` lines 497-545

#### Problem
Individual technology blips on the radar:
- Cannot receive keyboard focus
- Have no ARIA labels or accessible names
- Only respond to mouse hover
- Don't announce their position or status to screen readers

#### Current Code
```javascript
// Blips configuration - no accessibility attributes
blips.each(function (d) {
  var blip = d3.select(this);
  // ... shape rendering only
});
```

#### Impact
- Screen reader users cannot discover or interact with individual technologies
- Keyboard users cannot navigate between blips
- No way to understand technology placement without vision
- Fails multiple WCAG Level A criteria

#### Recommendation
Make blips keyboard accessible with proper ARIA attributes:
```javascript
blips.each(function (d) {
  var blip = d3.select(this);
  
  // Add accessibility attributes
  blip.attr("tabindex", "0")
      .attr("role", "button")
      .attr("aria-label", function() {
        var statusText = d.status === 1 ? "New" : d.status === 2 ? "Moved" : "Unchanged";
        return d.id + ". " + d.label + " - " + statusText + 
               " technology in " + config.rings[d.ring].name + " ring, " +
               config.quadrants[d.quadrant].name + " quadrant";
      })
      .on("focus", function() { showBubble(d); })
      .on("blur", function() { hideBubble(d); })
      .on("keydown", function() {
        if (d3.event.keyCode === 13 || d3.event.keyCode === 32) {
          if (d.link) window.open(d.link, config.links_in_new_tabs ? '_blank' : '_self');
          d3.event.preventDefault();
        }
      });
  
  // ... rest of blip rendering
});
```

---

## High Priority Issues

### 4. Insufficient Color Contrast
**Severity:** HIGH  
**WCAG Guidelines:** 1.4.3 (Contrast Minimum - Level AA), 1.4.6 (Contrast Enhanced - Level AAA)  
**Location:** `radar.css` lines 2, 17-18

#### Problem
The background and text color combination fails WCAG contrast requirements:
- Background: `#858383` (gray)
- Text: `white`
- Contrast ratio: ~4.2:1 (fails AA for normal text which requires 4.5:1)
- Fails AAA standard which requires 7:1

#### Current Code
```css
:root {
    --kleur-achtergrond: #858383;
    --kleur-tekst: white;
}

body {
    background-color: var(--kleur-achtergrond);
    color: var(--kleur-tekst);
}
```

#### Impact
- Users with low vision struggle to read text
- Text becomes invisible under certain lighting conditions
- Fails WCAG 2.1 Level AA compliance
- Creates barriers for users with color vision deficiencies

#### Recommendation
Improve contrast by using a darker background:
```css
:root {
    --kleur-achtergrond: #3a3a3a;  /* Darker gray - achieves 12.6:1 contrast */
    --kleur-tekst: white;
}
```

Or lighter text with current background:
```css
:root {
    --kleur-achtergrond: #858383;
    --kleur-tekst: #ffffff;  /* Already white, but ensure no transparency */
}

/* Ensure body text has sufficient contrast */
body {
    background-color: var(--kleur-achtergrond);
    color: var(--kleur-tekst);
}

/* Consider adding text shadow for better legibility */
h1, h2, h3 {
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
}
```

---

### 5. No Visible Focus Indicators
**Severity:** HIGH  
**WCAG Guidelines:** 2.4.7 (Focus Visible - Level AA)  
**Location:** `radar.css` - missing throughout

#### Problem
The application lacks visible focus indicators for:
- Links in the header
- SVG interactive elements (when keyboard support is added)
- Mobile list links
- Details/summary elements

#### Current Code
No focus styles defined in the CSS

#### Impact
- Keyboard users cannot see which element has focus
- Impossible to track navigation position
- Fails WCAG 2.1 Level AA compliance
- Creates confusion and navigation difficulties

#### Recommendation
Add comprehensive focus indicators:
```css
/* Focus indicators for all interactive elements */
a:focus,
button:focus,
summary:focus {
    outline: 3px solid #F47216; /* Orange accent color */
    outline-offset: 2px;
}

/* High contrast focus for links */
a:focus {
    background-color: rgba(244, 114, 22, 0.2);
    text-decoration: underline;
}

/* SVG element focus indicators (when keyboard support added) */
.blip:focus,
.legend-item:focus {
    outline: 3px solid #F47216;
    outline-offset: 3px;
}

/* Ensure focus is never hidden */
*:focus {
    outline: none; /* Remove default */
}

*:focus-visible {
    outline: 3px solid #F47216;
    outline-offset: 2px;
}
```

---

### 6. Touch Targets Too Small on Mobile
**Severity:** HIGH  
**WCAG Guidelines:** 2.5.5 (Target Size - Level AAA)  
**Location:** `radar.js` line 529 (blip circles)

#### Problem
SVG blips have a radius of 9px (18px diameter total):
- WCAG AAA requires minimum 44×44px for touch targets
- Current size: 18×18px
- Does not meet mobile accessibility standards

#### Current Code
```javascript
blip.append("circle")
    .attr("r", 9)  // 18px diameter - too small
    .attr("fill", d.color);
```

#### Impact
- Users with motor disabilities struggle to tap small targets
- Older users have difficulty with precise touch
- Fails WCAG 2.1 Level AAA (2.5.5)
- Poor mobile user experience

#### Recommendation
Since the radar visualization requires compact spacing, consider:

**Option 1:** Increase touch target size with invisible padding
```javascript
blip.append("circle")
    .attr("r", 9)
    .attr("fill", d.color);

// Add invisible touch target
blip.append("circle")
    .attr("r", 22)  // 44px diameter for touch
    .attr("fill", "transparent")
    .attr("pointer-events", "all");
```

**Option 2:** For mobile view (already using list), ensure links have adequate padding
```css
.mobile-lists a {
    display: block;
    padding: 12px 8px;  /* Ensures 44px min height */
    min-height: 44px;
    text-decoration: none;
}
```

---

### 7. Legend Items Missing Comprehensive ARIA Labels
**Severity:** HIGH  
**WCAG Guidelines:** 1.3.1 (Info and Relationships), 4.1.2 (Name, Role, Value)  
**Location:** `radar.js` lines 370-388

#### Problem
Legend items only display truncated text without full context:
- No indication of which ring or quadrant
- Status information not exposed to screen readers
- Truncated text (with `...`) not explained
- Full text only available on mouseover

#### Current Code
```javascript
.append("text")
.attr("transform", function (d, i) { return legend_transform(quadrant, ring, i); })
.attr("class", "legend" + quadrant + ring + " legend-item")
.attr("id", function (d, i) { return "legendItem" + d.id; })
.each(function(d, i) {
    var fullText = d.id + ". " + d.label;
    var truncatedText = truncateText(fullText, config.legend_text_max_length);
    d3.select(this)
        .attr("data-full-text", fullText)
        .attr("data-truncated-text", truncatedText)
        .text(truncatedText);
})
```

#### Impact
- Screen readers only announce truncated text
- Users don't know which ring/quadrant item belongs to
- Status information (new, moved, unchanged) not announced
- Fails to provide equal access to information

#### Recommendation
Add comprehensive ARIA labels:
```javascript
.append("text")
.attr("transform", function (d, i) { return legend_transform(quadrant, ring, i); })
.attr("class", "legend" + quadrant + ring + " legend-item")
.attr("id", function (d, i) { return "legendItem" + d.id; })
.attr("aria-label", function(d) {
    var statusText = d.status === 1 ? "New" : d.status === 2 ? "Moved" : "Unchanged";
    return "Item " + d.id + ": " + d.label + 
           " - " + statusText + " technology in " + 
           config.rings[ring].name + " ring, " +
           config.quadrants[quadrant].name + " quadrant";
})
.each(function(d, i) {
    var fullText = d.id + ". " + d.label;
    var truncatedText = truncateText(fullText, config.legend_text_max_length);
    d3.select(this)
        .attr("data-full-text", fullText)
        .attr("data-truncated-text", truncatedText)
        .attr("title", fullText)  // Browser tooltip for non-SR users
        .text(truncatedText);
})
```

---

## Medium Priority Issues

### 8. Links Open in New Tabs Without Warning
**Severity:** MEDIUM  
**WCAG Guidelines:** 3.2.2 (On Input), G201 (Giving users advanced warning)  
**Location:** `index.html` lines 127-128, `radar.js` lines 358, 511

#### Problem
Multiple links open in new tabs/windows without warning users:
- External documentation links
- Technology detail links
- Footer reference links

#### Current Code
```javascript
// In radar.js
.attr("target", function (d, i) {
    return (d.link && config.links_in_new_tabs) ? "_blank" : null;
})
```

```html
<!-- In index.html -->
<a href="https://github.com/zalando/tech-radar"> Zalando TechRadar</a>
```

#### Impact
- Unexpected context change confuses screen reader users
- Browser history navigation becomes unpredictable
- Security risk: missing `rel="noopener noreferrer"`
- Fails WCAG 2.1 Level A (3.2.2)

#### Recommendation
Add warnings and security attributes:
```javascript
// In radar.js
.attr("target", function (d, i) {
    return (d.link && config.links_in_new_tabs) ? "_blank" : null;
})
.attr("rel", function(d, i) {
    return (d.link && config.links_in_new_tabs) ? "noopener noreferrer" : null;
})
.attr("aria-label", function(d) {
    var label = d.label;
    if (d.link && config.links_in_new_tabs) {
        label += " (opens in new tab)";
    }
    return label;
});
```

```html
<!-- Add visual indicator -->
<a href="https://github.com/zalando/tech-radar" 
   target="_blank" 
   rel="noopener noreferrer">
   Zalando TechRadar
   <span aria-label="opens in new tab">↗</span>
</a>
```

---

### 9. Truncated Text Inaccessible to Keyboard Users
**Severity:** MEDIUM  
**WCAG Guidelines:** 1.3.1 (Info and Relationships)  
**Location:** `radar.js` lines 299-303, 374-382, 446-450

#### Problem
Full text for truncated legend items only appears on mouseover:
- Keyboard users cannot see full text
- Full text stored in data attributes but not accessible
- `title` attribute not used (would provide browser tooltip)

#### Current Code
```javascript
function truncateText(text, maxLength) {
    if (text.length > maxLength) {
        return text.substring(0, maxLength - 3) + '...';
    }
    return text;
}

// On hover - mouse only
function highlightLegendItem(d) {
    var legendItem = d3.select("#legendItem" + d.id);
    legendItem.attr("filter", "url(#solid)");
    legendItem.attr("fill", config.colors.background);
    
    var fullText = legendItem.attr("data-full-text");
    if (fullText) {
        legendItem.text(fullText);
    }
}
```

#### Impact
- Keyboard users see truncated text with `...` but no way to reveal full text
- Inconsistent experience between mouse and keyboard users
- Important information hidden

#### Recommendation
Use `title` attribute for browser tooltips and ARIA:
```javascript
.each(function(d, i) {
    var fullText = d.id + ". " + d.label;
    var truncatedText = truncateText(fullText, config.legend_text_max_length);
    
    d3.select(this)
        .attr("data-full-text", fullText)
        .attr("data-truncated-text", truncatedText)
        .attr("title", fullText)  // Native browser tooltip works for keyboard
        .attr("aria-label", fullText)  // Screen readers get full text
        .text(truncatedText);
})
```

Also update highlight functions to work on focus:
```javascript
function highlightLegendItem(d) {
    var legendItem = d3.select("#legendItem" + d.id);
    legendItem.attr("filter", "url(#solid)");
    legendItem.attr("fill", config.colors.background);
    // Keep visual text truncated to maintain layout
    // Full text available via title and aria-label
}
```

---

### 10. Mobile Details/Summary Elements May Lack Full Keyboard Support
**Severity:** MEDIUM  
**WCAG Guidelines:** 2.1.1 (Keyboard)  
**Location:** `index.html` lines 113-135

#### Problem
While `<details>`/`<summary>` are generally keyboard accessible:
- Arrow key navigation not explicitly provided
- Focus management when expanding/collapsing not tested
- May have inconsistent behavior across browsers

#### Current Code
```javascript
const details = document.createElement('details');
const summary = document.createElement('summary');
summary.textContent = qName;
details.appendChild(summary);
```

#### Impact
- Some screen reader users may have difficulty navigating collapsed sections
- Potential inconsistent keyboard behavior across browsers
- May not meet full keyboard accessibility requirements

#### Recommendation
Enhance with explicit ARIA and keyboard support:
```javascript
const details = document.createElement('details');
const summary = document.createElement('summary');
summary.textContent = qName;
summary.setAttribute('aria-expanded', 'false');

// Add keyboard and state management
details.addEventListener('toggle', function() {
    const isOpen = details.open;
    summary.setAttribute('aria-expanded', isOpen.toString());
});

details.appendChild(summary);
```

---

### 11. Legend Footer Lacks Semantic Structure
**Severity:** MEDIUM  
**WCAG Guidelines:** 1.3.1 (Info and Relationships)  
**Location:** `radar.js` lines 318-324

#### Problem
The legend footer explaining symbols is plain text without semantic structure:
- No list structure for the three symbols
- No association between symbol and meaning
- Not marked up for screen reader comprehension

#### Current Code
```javascript
radar.append("text")
    .attr("transform", translate(footer_offset.x, footer_offset.y))
    .text("■ Nieuw ▲ Verplaatst ● Onveranderd")
    .attr("xml:space", "preserve")
    .style("font-family", "Raleway")
    .style("font-size", "14px")
    .style("fill", config.colors.text);
```

#### Impact
- Screen reader users hear symbols read as Unicode character names
- Association between symbol and status not clear
- Difficult to understand legend without visual context

#### Recommendation
Create structured legend with proper markup:
```javascript
// Create a legend group with title and list
var footerLegend = radar.append("g")
    .attr("role", "list")
    .attr("aria-label", "Technology status indicators");

// Add title
footerLegend.append("text")
    .attr("transform", translate(footer_offset.x, footer_offset.y - 20))
    .text("Status Legend:")
    .style("font-weight", "bold")
    .style("font-family", "Raleway")
    .style("font-size", "14px")
    .style("fill", config.colors.text);

// Add individual status items
var statuses = [
    { symbol: "■", text: "Nieuw", x: 0 },
    { symbol: "▲", text: "Verplaatst", x: 80 },
    { symbol: "●", text: "Onveranderd", x: 180 }
];

statuses.forEach(function(status) {
    var item = footerLegend.append("g")
        .attr("role", "listitem");
    
    item.append("text")
        .attr("transform", translate(footer_offset.x + status.x, footer_offset.y))
        .attr("aria-label", status.text + " status")
        .text(status.symbol + " " + status.text)
        .style("font-family", "Raleway")
        .style("font-size", "14px")
        .style("fill", config.colors.text);
});
```

---

## Low Priority Issues

### 12. Inconsistent Language Attributes
**Severity:** LOW  
**WCAG Guidelines:** 3.1.1 (Language of Page), 3.1.2 (Language of Parts)  
**Location:** `index.html` line 32

#### Problem
HTML declares `lang="en"` but content is primarily Dutch:
- Page title, headings, and body text are in Dutch
- No `lang="nl"` attributes on Dutch sections
- Mixed language content not properly marked up

#### Current Code
```html
<!DOCTYPE html>
<html lang="en">
<!-- Content in Dutch follows -->
```

#### Impact
- Screen readers may use wrong pronunciation
- Translation tools may not work correctly
- Fails WCAG 2.1 Level A (3.1.1)

#### Recommendation
Change primary language and mark English sections:
```html
<!DOCTYPE html>
<html lang="nl">

<!-- Mark English sections -->
<a href="https://www.thoughtworks.com/radar" lang="en">ThoughtWorks</a>
<a href="https://github.com/zalando/tech-radar" lang="en">Zalando TechRadar</a>
```

---

### 13. Missing Skip Navigation Link
**Severity:** LOW  
**WCAG Guidelines:** 2.4.1 (Bypass Blocks)  
**Location:** `index.html` - missing from header

#### Problem
No "skip to main content" link present:
- Keyboard users must tab through entire header
- No quick way to jump to main radar visualization
- Particularly important given complex SVG navigation (when keyboard support added)

#### Current Code
No skip link exists

#### Impact
- Time-consuming navigation for keyboard users
- Repetitive tabbing through header on each page load
- Fails WCAG 2.1 Level A best practices

#### Recommendation
Add skip navigation link:
```html
<body>
    <!-- Skip link - first focusable element -->
    <a href="#main-content" class="skip-link">
        Skip to main content
    </a>

    <div class="center-box header-box">
        <!-- existing header content -->
    </div>

    <main id="main-content" role="main">
        <svg id="radar" style="display: block; margin: auto"></svg>
        <div id="mobile-lists" class="mobile-lists"></div>
    </main>
    
    <!-- rest of content -->
</body>
```

```css
/* Visually hidden until focused */
.skip-link {
    position: absolute;
    top: -40px;
    left: 0;
    background: var(--kleur-accent);
    color: white;
    padding: 8px;
    text-decoration: none;
    z-index: 100;
}

.skip-link:focus {
    top: 0;
}
```

---

### 14. Header Logo Link Lacks Context
**Severity:** LOW  
**WCAG Guidelines:** 1.1.1 (Non-text Content), 2.4.4 (Link Purpose)  
**Location:** `index.html` lines 55-57

#### Problem
Logo link has alt text but lacks additional context:
- Not clear it's a link to homepage
- Purpose unclear without visual context

#### Current Code
```html
<a href="https://www.soprasteria.nl/">
    <img src="sopra-steria-logo.svg" alt="Sopra Steria logo">
</a>
```

#### Impact
- Screen reader users may not understand link purpose
- Link purpose should be determinable from link text alone

#### Recommendation
Add aria-label for clarity:
```html
<a href="https://www.soprasteria.nl/" 
   aria-label="Sopra Steria homepage">
    <img src="sopra-steria-logo.svg" 
         alt="Sopra Steria logo"
         role="img">
</a>
```

---

## Positive Accessibility Features Found

Despite the issues identified, the application does have some accessibility considerations:

1. **Responsive Design**: Mobile view switches to accessible list format at 640px
2. **Semantic HTML Elements**: Uses `<details>` and `<summary>` for mobile navigation
3. **Font Scaling**: Uses relative units where appropriate
4. **Alt Text on Images**: Logo has alt attribute
5. **Descriptive Link Text**: Most links have meaningful text (not "click here")
6. **Color Is Not Only Indicator**: Symbols (■▲●) used alongside colors for status

---

## Recommended Implementation Priority

### Phase 1: Critical Fixes (Immediate - Week 1)
1. **Add keyboard navigation to SVG radar**
   - Tabindex on all interactive elements
   - Focus/blur event handlers
   - Keydown handlers for Enter/Space
   
2. **Add ARIA labels to SVG**
   - Main SVG role and label
   - Individual blip labels with full context
   - Legend item labels

3. **Implement focus indicators**
   - Visible outlines on all focusable elements
   - High contrast focus styles

### Phase 2: High Priority (Week 2-3)
4. **Fix color contrast issues**
   - Adjust background color or add overlays
   - Ensure 4.5:1 minimum ratio
   
5. **Improve touch targets**
   - Add invisible larger touch areas on blips
   - Ensure mobile links meet 44px minimum

6. **Add comprehensive tooltips**
   - Title attributes for truncated text
   - Full information available to all users

### Phase 3: Medium Priority (Week 4)
7. **Add link warnings**
   - Indicate external/new tab links
   - Add security attributes

8. **Enhance mobile accessibility**
   - Explicit ARIA on details/summary
   - Test with screen readers

9. **Structure legend semantically**
   - Convert footer to proper list structure

### Phase 4: Low Priority (Ongoing)
10. **Fix language attributes**
    - Change to `lang="nl"`
    - Mark English phrases

11. **Add skip navigation**
    - Skip to main content link

12. **Improve logo link context**
    - Add aria-label

---

## Testing Recommendations

### Automated Testing
- **Axe DevTools**: Run automated accessibility scanner
- **WAVE**: Web Accessibility Evaluation Tool
- **Lighthouse**: Chrome DevTools accessibility audit
- **Pa11y**: Command-line accessibility testing

### Manual Testing
1. **Keyboard Navigation**
   - Tab through all interactive elements
   - Ensure all functionality available via keyboard
   - Verify focus indicators visible
   - Test Enter and Space key activation

2. **Screen Reader Testing**
   - Test with NVDA (Windows)
   - Test with JAWS (Windows)
   - Test with VoiceOver (macOS/iOS)
   - Test with TalkBack (Android)

3. **Color Contrast**
   - Use Color Contrast Analyzer
   - Test with browser zoom at 200%
   - Verify in different lighting conditions

4. **Mobile Testing**
   - Test touch targets with finger
   - Verify mobile list accessibility
   - Test with mobile screen readers

5. **Zoom Testing**
   - Test at 200% zoom
   - Test at 400% zoom (WCAG AAA)
   - Verify no horizontal scrolling

---

## WCAG 2.1 Compliance Matrix

| Criterion | Level | Status | Issues |
|-----------|-------|--------|--------|
| 1.1.1 Non-text Content | A | ⚠️ Partial | Issue #14 |
| 1.3.1 Info and Relationships | A | ❌ Fail | Issues #1, #7, #8, #11 |
| 1.4.3 Contrast (Minimum) | AA | ❌ Fail | Issue #4 |
| 1.4.6 Contrast (Enhanced) | AAA | ❌ Fail | Issue #4 |
| 2.1.1 Keyboard | A | ❌ Fail | Issues #2, #3, #10 |
| 2.1.2 No Keyboard Trap | A | ✅ Pass | - |
| 2.4.1 Bypass Blocks | A | ⚠️ Partial | Issue #13 |
| 2.4.3 Focus Order | A | ❌ Fail | Issue #2 |
| 2.4.4 Link Purpose | A | ⚠️ Partial | Issue #14 |
| 2.4.7 Focus Visible | AA | ❌ Fail | Issue #5 |
| 2.5.5 Target Size | AAA | ❌ Fail | Issue #6 |
| 3.1.1 Language of Page | A | ❌ Fail | Issue #12 |
| 3.1.2 Language of Parts | AA | ⚠️ Partial | Issue #12 |
| 3.2.2 On Input | A | ⚠️ Partial | Issue #8 |
| 4.1.2 Name, Role, Value | A | ❌ Fail | Issues #1, #2, #3, #7 |

**Legend:**  
✅ Pass | ⚠️ Partial | ❌ Fail

---

## Resources and References

### WCAG 2.1 Guidelines
- [WCAG 2.1 Overview](https://www.w3.org/WAI/WCAG21/quickref/)
- [Understanding WCAG 2.1](https://www.w3.org/WAI/WCAG21/Understanding/)
- [How to Meet WCAG (Quick Reference)](https://www.w3.org/WAI/WCAG21/quickref/)

### SVG Accessibility
- [Accessible SVGs](https://css-tricks.com/accessible-svgs/)
- [SVG Accessibility](https://www.w3.org/TR/svg-aam-1.0/)
- [Using ARIA with SVG](https://www.w3.org/TR/svg-aam-1.0/)

### Testing Tools
- [Axe DevTools](https://www.deque.com/axe/devtools/)
- [WAVE Web Accessibility Tool](https://wave.webaim.org/)
- [Color Contrast Analyzer](https://www.tpgi.com/color-contrast-checker/)
- [NVDA Screen Reader](https://www.nvaccess.org/)

### D3.js Accessibility
- [Data Visualization Accessibility](https://www.w3.org/WAI/tutorials/charts/)
- [Accessible D3 Charts](https://fossheim.io/writing/posts/accessible-dataviz-d3-intro/)

---

## Conclusion

The Sopra Steria ALMTech Radar requires significant accessibility improvements to meet WCAG 2.1 Level AA standards. The most critical barrier is the lack of keyboard navigation and screen reader support for the main SVG radar visualization. 

**Immediate Actions Required:**
1. Implement keyboard navigation for all interactive SVG elements
2. Add comprehensive ARIA labels throughout the radar
3. Implement visible focus indicators
4. Fix color contrast issues

**Estimated Effort:**
- Critical fixes: 16-24 hours
- High priority: 8-12 hours  
- Medium priority: 8-12 hours
- Low priority: 4-6 hours
- **Total: 36-54 hours** (approximately 1-1.5 weeks)

Implementing these recommendations will ensure the Tech Radar is accessible to all users, including those using assistive technologies, keyboard navigation, or who have visual impairments.

---

**Document Version:** 1.0  
**Last Updated:** February 17, 2026  
**Next Review:** After implementation of Phase 1 recommendations
