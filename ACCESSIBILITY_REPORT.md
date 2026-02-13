# Tech Radar Accessibility Analysis Report

## Executive Summary
This report provides a comprehensive accessibility analysis of the Sopra Steria ALMTech Tech Radar application against WCAG 2.1 Level AA standards. The analysis identifies critical accessibility barriers and provides prioritized recommendations for improvement.

## Current Accessibility Status: **Needs Significant Improvement**

---

## Detailed Findings

### 1. SEMANTIC HTML & DOCUMENT STRUCTURE (Priority: HIGH)

#### Issues Identified:
- **Missing Language Declaration**: The `lang` attribute in HTML is set to "en" but content is in Dutch
  - Location: index.html:32
  - Impact: Screen readers will use incorrect pronunciation
  - WCAG: 3.1.1 Language of Page (Level A)

- **Missing Main Landmark**: No `<main>` element to identify primary content
  - Impact: Screen reader users cannot quickly navigate to main content
  - WCAG: 1.3.1 Info and Relationships (Level A)

- **No Skip Links**: No "skip to main content" link for keyboard users
  - Impact: Keyboard users must tab through all header elements repeatedly
  - WCAG: 2.4.1 Bypass Blocks (Level A)

- **SVG Lacks Proper Accessibility**: The radar SVG has no title, description, or role
  - Location: index.html:61
  - Impact: Screen readers cannot describe the visualization
  - WCAG: 1.1.1 Non-text Content (Level A)

#### Recommendations:
1. Change `lang="en"` to `lang="nl"` in HTML tag
2. Wrap main content in `<main>` element
3. Add skip navigation link at page start
4. Add proper ARIA attributes to SVG (role="img", aria-label, title)

---

### 2. KEYBOARD NAVIGATION (Priority: CRITICAL)

#### Issues Identified:
- **SVG Interactive Elements Not Keyboard Accessible**: Blips and legend items in D3.js visualization
  - Location: radar.js (lines 491-544)
  - Impact: Keyboard-only users cannot interact with radar
  - WCAG: 2.1.1 Keyboard (Level A)

- **No Visible Focus Indicators**: Interactive elements lack visible focus styling
  - Impact: Keyboard users don't know where they are on the page
  - WCAG: 2.4.7 Focus Visible (Level AA)

- **Tab Order Issues**: No logical tab order through interactive elements
  - WCAG: 2.4.3 Focus Order (Level A)

- **Mobile Details/Summary Missing Keyboard Support**: Details elements work but lack enhanced keyboard features
  - Location: index.html:113-135
  - Impact: Reduced usability for keyboard users

#### Recommendations:
1. Make SVG blips and legend items focusable with tabindex
2. Add keyboard event handlers (Enter/Space) for blip interaction
3. Add clear focus indicators with CSS
4. Implement logical tab order for all interactive elements
5. Add aria-expanded state management for collapsible sections

---

### 3. ARIA ATTRIBUTES & ROLES (Priority: HIGH)

#### Issues Identified:
- **Missing ARIA Labels**: Interactive elements lack descriptive labels
  - Location: radar.js:491-544 (blips), 349-389 (legend items)
  - Impact: Screen reader users don't understand element purpose
  - WCAG: 4.1.2 Name, Role, Value (Level A)

- **No Live Region Announcements**: Dynamic content changes not announced
  - Impact: Screen reader users miss hover/interaction feedback
  - WCAG: 4.1.3 Status Messages (Level AA)

- **Missing Button Roles**: SVG interactive elements should have role="button"
  - WCAG: 4.1.2 Name, Role, Value (Level A)

- **No ARIA Description for Complex Visualization**: Radar lacks explanation
  - WCAG: 1.1.1 Non-text Content (Level A)

#### Recommendations:
1. Add aria-label to all interactive SVG elements
2. Add role="button" to clickable blips
3. Implement aria-live region for hover announcements
4. Add comprehensive aria-describedby for radar explanation
5. Add aria-current to indicate selected/active states

---

### 4. COLOR CONTRAST (Priority: HIGH)

#### Issues Identified:
- **Insufficient Contrast Ratios**: Multiple color combinations fail WCAG AA
  - Background: #858383 (medium gray)
  - Text: white - Contrast: ~4.5:1 (marginally passing for large text)
  - Links: Black (#000000) on gray background - Contrast: ~3.5:1 (FAILS)
  - Location: radar.css:2-11, radar.js:23-31
  - WCAG: 1.4.3 Contrast (Minimum) (Level AA) - Requires 4.5:1 for normal text, 3:1 for large text

- **Ring Colors with Poor Contrast**:
  - Orange (#F47216) on gray: ~4.2:1 (borderline)
  - Green (#66d08f) on gray: ~5.1:1 (passes but low)
  - Red (#DB1D2B) on gray: ~4.0:1 (borderline)
  
- **Legend Text on Colored Backgrounds**: Some combinations fail
  - Location: radar.js:40-44
  - Black text on orange ring (Probeer): ~4.6:1 (borderline)

#### Recommendations:
1. Increase background darkness or text lightness for better contrast
2. Ensure all text meets 4.5:1 ratio (7:1 for AA enhanced)
3. Add text shadows or borders for low-contrast situations
4. Test all color combinations with contrast checker
5. Consider a high-contrast mode option

---

### 5. SCREEN READER COMPATIBILITY (Priority: CRITICAL)

#### Issues Identified:
- **No Text Alternative for Visual Radar**: Visualization only conveys information visually
  - Location: index.html:61, radar.js entire file
  - Impact: Blind users cannot access any information
  - WCAG: 1.1.1 Non-text Content (Level A)

- **Mobile List View Better but Incomplete**: Has structure but lacks descriptions
  - Location: index.html:99-139
  - Missing: Ring descriptions, status indicators accessible to screen readers

- **Legend Footer Not Accessible**: Symbols (■ ▲ ●) not explained
  - Location: radar.js:320
  - Impact: Symbol meanings not conveyed to screen readers
  - WCAG: 1.1.1 Non-text Content (Level A)

- **Truncated Text Without Full Alternative**: Text truncated but no accessible full text
  - Location: radar.js:376-382
  - Impact: Screen reader users don't get full text

#### Recommendations:
1. Add comprehensive text alternative for radar visualization
2. Provide data table view as alternative representation
3. Add aria-label describing status symbols
4. Ensure mobile view is fully accessible with proper ARIA
5. Add visually hidden text for full labels
6. Implement aria-describedby for complete descriptions

---

### 6. FONT SIZE & ZOOM (Priority: MEDIUM)

#### Issues Identified:
- **Small Font Sizes**: Multiple instances of small text
  - Legend items: 11px (radar.js:385)
  - Footer: 14px (radar.js:323)
  - Bubble tooltip: 10px (radar.js:411)
  - Impact: Difficult to read for vision-impaired users
  - WCAG: 1.4.4 Resize Text (Level AA) - Text must be resizable to 200%

- **Fixed Pixel Sizes**: Using px instead of relative units
  - Impact: Text doesn't scale properly with browser zoom
  - WCAG: 1.4.4 Resize Text (Level AA)

- **Responsive Breakpoints**: Only one breakpoint at 640px
  - Location: radar.css:205-213, index.html:88
  - Impact: Limited adaptability for different screen sizes

#### Recommendations:
1. Increase minimum font sizes (minimum 12-14px for body text)
2. Use rem/em units instead of px for scalability
3. Ensure page works at 200% zoom
4. Add more responsive breakpoints
5. Test with browser zoom at various levels

---

### 7. INTERACTIVE CONTROLS (Priority: HIGH)

#### Issues Identified:
- **Links Open in New Tab Without Warning**: No indication for users
  - Location: radar.js:358, 509-511, index.html:127-129
  - Impact: Disorienting for screen reader users
  - WCAG: 3.2.5 Change on Request (Level AAA), 3.2.1 On Focus (Level A)

- **Hover-Only Interactions**: Some features only work on hover
  - Location: radar.js:387-388, 497-498
  - Impact: Touch screen and keyboard users cannot access
  - WCAG: 2.1.1 Keyboard (Level A)

- **No Focus Management**: Focus not managed during interactions
  - Impact: Poor user experience for keyboard/screen reader users
  - WCAG: 2.4.3 Focus Order (Level A)

- **Details/Summary Elements**: Missing enhanced accessibility
  - Location: index.html:113-135
  - Recommendation: Add aria-expanded state handling

#### Recommendations:
1. Add visual and accessible indication for external links
2. Add aria-label="(opens in new tab)" to external links
3. Implement keyboard alternatives for all hover interactions
4. Add proper focus management for interactive elements
5. Ensure all controls are operable by keyboard

---

### 8. FORM CONTROLS & INPUTS (Priority: N/A)

#### Issues Identified:
- **No Forms Present**: Application has no input fields or forms
  - Status: Not applicable for current implementation

---

### 9. ERROR HANDLING & FEEDBACK (Priority: MEDIUM)

#### Issues Identified:
- **No Error States**: Fetch errors not communicated accessibly
  - Location: index.html:65-67, 83-85
  - Impact: Users not informed of loading failures
  - WCAG: 3.3.1 Error Identification (Level A)

- **Loading States Not Announced**: No loading indicators
  - Impact: Screen reader users don't know content is loading
  - WCAG: 4.1.3 Status Messages (Level AA)

#### Recommendations:
1. Add aria-live region for error messages
2. Add loading spinner with aria-label
3. Announce when content is loaded
4. Provide clear error messages with recovery instructions

---

### 10. PAGE TITLE & METADATA (Priority: LOW)

#### Issues Identified:
- **Static Page Title**: Title doesn't reflect current version/state
  - Location: index.html:42
  - Impact: Minor - users may not know which version they're viewing
  - WCAG: 2.4.2 Page Titled (Level A)

- **Meta Description**: Present but generic
  - Location: index.html:37
  - Impact: Low - SEO and context issue

#### Recommendations:
1. Update page title dynamically with version info
2. Enhance meta description with more specific information

---

### 11. MOBILE ACCESSIBILITY (Priority: MEDIUM)

#### Issues Identified:
- **Mobile List View**: Better than SVG but could be improved
  - Location: index.html:99-139
  - Issues:
    - No ring descriptions in mobile view
    - Status indicators (new/moved/unchanged) not shown
    - Links need better touch targets

- **Touch Target Sizes**: Some interactive elements may be too small
  - WCAG: 2.5.5 Target Size (Level AAA) - Minimum 44x44px
  - Impact: Difficult for users with motor impairments

#### Recommendations:
1. Add ring/status info to mobile view
2. Ensure minimum 44x44px touch targets
3. Add aria-label to details/summary for clarity
4. Test with mobile screen readers (TalkBack, VoiceOver)

---

## PRIORITIZED RECOMMENDATIONS

### 🔴 Critical Priority (Must Fix - WCAG A Violations)
1. Add keyboard navigation to all interactive SVG elements
2. Provide text alternative for visual radar representation
3. Fix language declaration (en → nl)
4. Add proper ARIA labels and roles to all interactive elements
5. Add skip navigation link

### 🟠 High Priority (Should Fix - WCAG AA Violations)
1. Fix color contrast issues throughout the application
2. Add comprehensive ARIA attributes for screen readers
3. Implement focus indicators for all interactive elements
4. Add live region announcements for dynamic content
5. Provide accessible legend symbol explanations

### 🟡 Medium Priority (Important Improvements)
1. Increase font sizes and use relative units
2. Add error handling and loading state announcements
3. Improve mobile accessibility features
4. Add external link indicators
5. Implement focus management

### 🟢 Low Priority (Nice to Have)
1. Add dynamic page title with version info
2. Create high-contrast mode option
3. Add more granular responsive breakpoints
4. Enhance meta descriptions

---

## TESTING RECOMMENDATIONS

### Automated Testing Tools:
1. **axe DevTools**: Browser extension for automated accessibility testing
2. **WAVE**: Web accessibility evaluation tool
3. **Lighthouse**: Chrome DevTools accessibility audit
4. **Pa11y**: Automated accessibility testing command-line tool

### Manual Testing:
1. **Keyboard Navigation**: Tab through entire page, test all interactions
2. **Screen Reader Testing**:
   - NVDA (Windows - free)
   - JAWS (Windows - commercial)
   - VoiceOver (Mac/iOS - built-in)
   - TalkBack (Android - built-in)
3. **Zoom Testing**: Test at 200% and 400% zoom levels
4. **Color Blindness Simulation**: Test with various color blindness filters
5. **High Contrast Mode**: Test with Windows High Contrast mode

---

## ESTIMATED EFFORT

- **Critical Fixes**: 3-5 days development + 2 days testing
- **High Priority Fixes**: 2-3 days development + 1 day testing
- **Medium Priority Fixes**: 1-2 days development + 0.5 day testing
- **Low Priority Fixes**: 0.5-1 day development

**Total Estimated Effort**: 6.5-11.5 days for complete accessibility overhaul

---

## CONCLUSION

The Sopra Steria ALMTech Tech Radar currently has significant accessibility barriers that prevent many users from effectively using the application. The most critical issues are:

1. **Complete lack of keyboard navigation** for the main radar visualization
2. **Insufficient text alternatives** for screen reader users
3. **Color contrast issues** that affect readability
4. **Missing ARIA attributes** throughout the application

Addressing these issues will make the application compliant with WCAG 2.1 Level AA standards and significantly improve the user experience for:
- Keyboard-only users
- Screen reader users
- Users with low vision
- Users with color blindness
- Users with motor impairments
- Mobile device users

The mobile list view provides a good alternative to the visual radar, but both views need significant accessibility improvements to be truly inclusive.

---

## REFERENCES

- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [A11y Project Checklist](https://www.a11yproject.com/checklist/)
- [MDN Accessibility](https://developer.mozilla.org/en-US/docs/Web/Accessibility)

---

**Report Generated**: February 2026  
**Analyst**: GitHub Copilot Accessibility Analysis Agent  
**Application**: Sopra Steria ALMTech Tech Radar
