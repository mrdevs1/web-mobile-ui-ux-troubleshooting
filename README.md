# Web Mobile UI/UX Troubleshooting

A practical guide to diagnosing and fixing mobile UI/UX issues, responsive layouts, navigation problems, touch interactions, forms, accessibility, and cross-device usability.

This repository provides troubleshooting techniques, CSS examples, debugging commands, and testing checklists for developers working on websites, e-commerce stores, and web applications.

## Table of Contents

* [Common Mobile UI/UX Problems](#common-mobile-uiux-problems)
* [Responsive Layout Issues](#responsive-layout-issues)
* [Horizontal Scrolling Problems](#horizontal-scrolling-problems)
* [Mobile Navigation Problems](#mobile-navigation-problems)
* [Button and Touch Target Issues](#button-and-touch-target-issues)
* [Form Usability Problems](#form-usability-problems)
* [Modal and Popup Issues](#modal-and-popup-issues)
* [Typography and Readability](#typography-and-readability)
* [Image Scaling Problems](#image-scaling-problems)
* [Mobile Tables and Product Grids](#mobile-tables-and-product-grids)
* [Sticky Headers and Floating Elements](#sticky-headers-and-floating-elements)
* [Mobile Keyboard Problems](#mobile-keyboard-problems)
* [CSS Debugging Techniques](#css-debugging-techniques)
* [Accessibility Checks](#accessibility-checks)
* [Chrome DevTools Responsive Testing](#chrome-devtools-responsive-testing)
* [Common CSS Fixes](#common-css-fixes)
* [Mobile UX Testing Checklist](#mobile-ux-testing-checklist)
* [Troubleshooting Workflow](#troubleshooting-workflow)
* [Important Notes](#important-notes)

---

## Common Mobile UI/UX Problems

Mobile interfaces can behave differently from desktop layouts because of limited screen width, touch interactions, browser viewport behavior, and virtual keyboards.

Common issues include:

* Content extending beyond the screen
* Horizontal scrolling
* Broken responsive layouts
* Navigation menus not opening
* Dropdowns appearing outside the viewport
* Buttons that are difficult to tap
* Form fields covered by the keyboard
* Text that is too small or difficult to read
* Images overflowing their containers
* Product cards displaying incorrectly
* Sticky elements covering page content
* Popups that cannot be closed easily
* Inconsistent spacing between components
* Low color contrast
* Missing keyboard navigation
* Layout differences between browsers

Before applying a fix, identify the affected page, device size, browser, and interaction.

---

## Responsive Layout Issues

A responsive layout should adapt to different viewport widths without losing content or functionality.

### Common causes

* Fixed-width containers
* Oversized images
* Hardcoded margins
* Incorrect CSS grid definitions
* Missing viewport metadata
* Excessive minimum widths
* Desktop-specific positioning
* Long text or URLs that cannot wrap

### Check the viewport meta tag

A standard responsive page should include:

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

Without appropriate viewport configuration, mobile browsers may render the page at an unexpected layout width.

### Avoid fixed-width containers

Problematic CSS:

```css
.container {
    width: 1200px;
}
```

A more flexible approach:

```css
.container {
    width: 100%;
    max-width: 1200px;
    margin-inline: auto;
    padding-inline: 16px;
    box-sizing: border-box;
}
```

### Use responsive breakpoints

```css
.content-grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 20px;
}

@media (max-width: 768px) {
    .content-grid {
        grid-template-columns: repeat(2, minmax(0, 1fr));
        gap: 14px;
    }
}

@media (max-width: 480px) {
    .content-grid {
        grid-template-columns: 1fr;
    }
}
```

Adjust breakpoints according to the content and layout rather than targeting specific device names.

---

## Horizontal Scrolling Problems

Unexpected horizontal scrolling is a common mobile usability problem.

### Identify the cause

Check for:

* Fixed-width elements
* Images wider than their containers
* Long unbroken text
* Tables with many columns
* Negative margins
* Absolutely positioned elements
* Elements using `100vw` with additional padding
* Grid or flex children that cannot shrink

### Debug overflowing elements

In Chrome DevTools, inspect elements that extend beyond the viewport.

You can also temporarily use:

```css
* {
    outline: 1px solid red;
}
```

This makes element boundaries easier to inspect. Remove the rule after debugging.

### Fix common overflow problems

```css
img,
video,
iframe {
    max-width: 100%;
}

*,
*::before,
*::after {
    box-sizing: border-box;
}

.text-content {
    overflow-wrap: anywhere;
}

.flex-child {
    min-width: 0;
}
```

Avoid applying `overflow-x: hidden` to the entire page as the first solution. It can hide content without fixing the underlying layout problem.

---

## Mobile Navigation Problems

Navigation is a major part of mobile usability.

### Common problems

* Menu button does not respond
* Dropdown opens outside the screen
* Navigation covers important content
* Menu cannot be closed
* Links are too close together
* Menu does not work with a keyboard
* Background scrolling continues behind an open menu

### Troubleshooting steps

1. Inspect the navigation HTML.
2. Verify the menu button's event handler.
3. Check `z-index` and positioning.
4. Inspect responsive CSS rules.
5. Check for JavaScript errors.
6. Test keyboard and touch interactions.
7. Verify that the open and closed states are communicated correctly.

### Example responsive navigation

```html
<button
    class="menu-toggle"
    type="button"
    aria-expanded="false"
    aria-controls="main-navigation"
>
    Menu
</button>

<nav id="main-navigation" hidden>
    <a href="/">Home</a>
    <a href="/products/">Products</a>
    <a href="/contact/">Contact</a>
</nav>

<script>
const button = document.querySelector(".menu-toggle");
const navigation = document.querySelector("#main-navigation");

button.addEventListener("click", () => {
    const isExpanded =
        button.getAttribute("aria-expanded") === "true";

    button.setAttribute("aria-expanded", String(!isExpanded));
    navigation.hidden = isExpanded;
});
</script>
```

This is a minimal example. Production navigation may also need Escape-key handling, focus management, and additional behavior depending on the design.

---

## Button and Touch Target Issues

Buttons should be easy to identify, understand, and activate on touch devices.

### Common problems

* Buttons are too small
* Text is difficult to read
* Clickable areas are too close together
* The visual button does not match its actual clickable area
* Hover-only interactions do not work on touch devices
* Disabled buttons look identical to active buttons

### Example

```css
.button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    min-height: 44px;
    padding: 12px 18px;
    border: 0;
    border-radius: 6px;
    cursor: pointer;
}

.button:focus-visible {
    outline: 3px solid #2563eb;
    outline-offset: 3px;
}
```

The `44px` minimum is a practical design target, not a universal accessibility requirement. WCAG 2.2 includes a 24-by-24 CSS pixel minimum target-size criterion with exceptions, and larger controls can improve usability.

### Check

* Is the action clear?
* Is the clickable area large enough?
* Is there adequate spacing?
* Is keyboard focus visible?
* Does the control provide feedback?
* Does it work without hover?

---

## Form Usability Problems

Forms are often difficult to use on small screens.

### Common problems

* Labels are missing
* Inputs overflow their containers
* Error messages are hidden
* Required fields are unclear
* The keyboard covers the active input
* Validation messages shift the layout unexpectedly
* The wrong mobile keyboard appears
* Placeholder text is used instead of a label

### Responsive form example

```html
<form>
    <div class="form-field">
        <label for="email">Email address</label>
        <input
            id="email"
            name="email"
            type="email"
            autocomplete="email"
            required
        >
    </div>

    <button type="submit">Continue</button>
</form>
```

```css
.form-field {
    display: flex;
    flex-direction: column;
    gap: 6px;
    margin-bottom: 16px;
}

.form-field input {
    width: 100%;
    min-height: 44px;
    padding: 10px 12px;
    font: inherit;
    border: 1px solid #777;
    border-radius: 6px;
    box-sizing: border-box;
}
```

### Form troubleshooting checklist

* Use visible labels.
* Use suitable input types.
* Explain validation errors.
* Preserve entered values after validation failures.
* Avoid unnecessarily long forms.
* Ensure error messages are accessible.
* Test keyboard navigation.
* Test form submission on mobile browsers.

---

## Modal and Popup Issues

Modals and popups can create significant usability problems on mobile screens.

### Common issues

* Popup exceeds the viewport
* Close button is inaccessible
* Content cannot scroll
* Background page scrolls behind the popup
* Popup appears under other elements
* Keyboard covers important controls
* Focus moves behind the modal
* Popup is difficult to dismiss

### Example responsive modal layout

```css
.modal {
    position: fixed;
    inset: 0;
    z-index: 1000;
    display: grid;
    place-items: center;
    padding: 16px;
    background: rgb(0 0 0 / 55%);
}

.modal-content {
    width: 100%;
    max-width: 520px;
    max-height: 85vh;
    max-height: 85dvh;
    overflow-y: auto;
    padding: 20px;
    background: white;
    border-radius: 10px;
    box-sizing: border-box;
}
```

This example covers layout only. A production modal also needs appropriate semantics, keyboard support, focus management, and a reliable close mechanism.

### Test

* Open the popup on a narrow viewport.
* Verify that all important content is reachable.
* Test scrolling within the popup.
* Test closing with a visible button.
* Test Escape-key behavior where appropriate.
* Check the experience with the mobile keyboard open.

---

## Typography and Readability

Text that looks acceptable on desktop may be difficult to read on mobile.

### Common problems

* Font size is too small
* Line height is too tight
* Text columns are too wide
* Headings overflow
* Long product names break the layout
* Low contrast between text and background
* Text size is fixed without considering user preferences

### Example

```css
body {
    font-family: system-ui, sans-serif;
    font-size: 16px;
    line-height: 1.6;
}

h1 {
    font-size: clamp(1.75rem, 5vw, 2.75rem);
    line-height: 1.2;
    overflow-wrap: anywhere;
}

p {
    line-height: 1.6;
}
```

### Check

* Can users read the text without zooming unnecessarily?
* Are headings distinguishable?
* Is there sufficient contrast?
* Can text resize without breaking the layout?
* Do long titles wrap properly?
* Does the page remain usable with increased text spacing?

---

## Image Scaling Problems

Images can break responsive layouts when they use fixed dimensions or incorrect aspect ratios.

### Common problems

* Image extends beyond its container
* Product thumbnails have inconsistent sizes
* Images look stretched
* Large images slow page loading
* Images cause layout shifts
* Incorrect responsive image is loaded

### Responsive image example

```css
.responsive-image {
    display: block;
    max-width: 100%;
    height: auto;
}
```

For product cards:

```css
.product-image {
    width: 100%;
    aspect-ratio: 1 / 1;
    object-fit: contain;
    display: block;
}
```

Use `object-fit: cover` when cropping is intended, and `contain` when the entire product needs to remain visible.

### Performance considerations

Use appropriately sized images and, where suitable, modern formats such as WebP or AVIF.

Avoid lazy-loading images that are immediately visible at the top of the page if doing so delays the main visual content.

---

## Mobile Tables and Product Grids

Tables and product grids often need special treatment on smaller screens.

### Responsive table

```css
.table-wrapper {
    max-width: 100%;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
}

.table-wrapper table {
    width: 100%;
    border-collapse: collapse;
}
```

```html
<div class="table-wrapper">
    <table>
        <thead>
            <tr>
                <th>Product</th>
                <th>Price</th>
                <th>Availability</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Example Product</td>
                <td>$25</td>
                <td>Available</td>
            </tr>
        </tbody>
    </table>
</div>
```

When a table is too wide, a horizontally scrollable wrapper can preserve its structure. Make the scrolling behavior apparent and test keyboard accessibility.

### Responsive product grid

```css
.product-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    gap: 20px;
}

@media (max-width: 992px) {
    .product-grid {
        grid-template-columns: repeat(3, minmax(0, 1fr));
    }
}

@media (max-width: 768px) {
    .product-grid {
        grid-template-columns: repeat(2, minmax(0, 1fr));
        gap: 12px;
    }
}
```

For very narrow screens, check whether product names, prices, ratings, and action buttons remain readable and usable.

---

## Sticky Headers and Floating Elements

Sticky headers, floating chat buttons, cookie notices, and bottom navigation can overlap page content.

### Common problems

* Header covers anchor targets
* Floating button covers checkout controls
* Cookie banner hides important content
* Sticky navigation takes too much vertical space
* Multiple sticky elements overlap
* Mobile browser controls change the visible viewport

### Example

```css
html {
    scroll-padding-top: 80px;
}

.sticky-header {
    position: sticky;
    top: 0;
    z-index: 100;
}

.floating-button {
    position: fixed;
    right: 16px;
    bottom: calc(16px + env(safe-area-inset-bottom, 0px));
    z-index: 200;
}
```

The `scroll-padding-top` value should reflect the actual header height.

Test floating elements on small screens, especially on checkout pages and forms.

---

## Mobile Keyboard Problems

The virtual keyboard can cover active form fields, submit buttons, and validation messages.

### Troubleshooting steps

1. Test on an actual mobile device.
2. Focus an input near the bottom of the page.
3. Open the virtual keyboard.
4. Check whether the field remains visible.
5. Test scrolling while the keyboard is open.
6. Check fixed-position elements.
7. Verify that form submission remains accessible.

### Useful CSS

```css
.form-container {
    width: 100%;
    max-width: 600px;
    margin-inline: auto;
    padding: 16px;
    box-sizing: border-box;
}

.form-container input,
.form-container textarea,
.form-container select {
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
}
```

Avoid relying on a fixed viewport height for layouts that need to accommodate the mobile keyboard.

Modern browsers support dynamic viewport units such as `dvh`, but behavior should still be tested on target devices.

---

## CSS Debugging Techniques

When a mobile layout breaks, inspect the actual element before modifying CSS.

### Check computed styles

In browser DevTools:

1. Select the affected element.
2. Open the Computed panel.
3. Inspect width, height, margin, padding, positioning, and overflow.
4. Review active media queries.
5. Identify styles that override the intended layout.

### Find fixed widths

Search your CSS for rules such as:

```css
width: 1200px;
min-width: 1000px;
position: absolute;
white-space: nowrap;
```

These declarations are not always incorrect, but they deserve investigation when content overflows on mobile.

### Inspect JavaScript errors

Open the browser Console and check for:

* Uncaught exceptions
* Missing scripts
* Failed network requests
* Event-handler errors
* Plugin conflicts

### Inspect network requests

Use the Network panel to investigate:

* Missing CSS
* Missing JavaScript
* Failed image requests
* Slow API responses
* Font loading failures
* Excessive asset sizes

---

## Accessibility Checks

A usable mobile interface should also support people using keyboards, assistive technologies, magnification, and alternative input methods.

Check the following:

* [ ] Every form control has an accessible name.
* [ ] Buttons have descriptive labels.
* [ ] Keyboard focus is visible.
* [ ] Interactive elements work with a keyboard.
* [ ] Text has sufficient contrast.
* [ ] Content reflows at narrow widths.
* [ ] Text can be resized.
* [ ] Errors are clearly identified.
* [ ] Links are distinguishable from surrounding text.
* [ ] Images have appropriate alternative text.
* [ ] Menus and dialogs expose their state correctly.
* [ ] Motion and animations do not create usability problems.
* [ ] Touch controls have adequate size and spacing.

For reference, review the [W3C Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/TR/WCAG22/).

Automated accessibility tools can identify some problems, but manual keyboard and screen-reader testing remain important.

---

## Chrome DevTools Responsive Testing

Chrome DevTools can simulate different viewport dimensions and help investigate responsive problems.

### Basic workflow

1. Open the website in Chrome.
2. Open DevTools with `F12`.
3. Enable the device toolbar with `Ctrl + Shift + M`.
4. Select a device preset or enter custom dimensions.
5. Reload the page.
6. Inspect layout, console errors, and network requests.
7. Test navigation, forms, menus, and popups.

### Test multiple viewport widths

For example:

| Viewport width | Test focus                      |
| -------------- | ------------------------------- |
| 320px          | Very narrow layouts             |
| 375px          | Compact mobile layout           |
| 390px          | Common mobile viewport          |
| 480px          | Larger mobile layout            |
| 768px          | Tablet or breakpoint transition |
| 1024px         | Wider tablet and small desktop  |
| 1440px         | Desktop layout                  |

These are test dimensions, not a complete list of real devices.

### Important limitation

Device emulation does not fully reproduce real touch behavior, mobile browser interfaces, keyboard behavior, device performance, or all browser-specific issues.

Always test critical workflows on actual devices when possible.

---

## Common CSS Fixes

### Prevent images from overflowing

```css
img {
    max-width: 100%;
    height: auto;
}
```

### Allow flex items to shrink

```css
.flex-item {
    min-width: 0;
}
```

### Allow grid items to shrink

```css
.grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
}
```

### Wrap long text

```css
.text {
    overflow-wrap: anywhere;
}
```

### Make form controls responsive

```css
input,
select,
textarea,
button {
    max-width: 100%;
    box-sizing: border-box;
}
```

### Use responsive spacing

```css
.section {
    padding: clamp(16px, 4vw, 40px);
}
```

### Reduce excessive desktop spacing on mobile

```css
@media (max-width: 768px) {
    .section {
        margin-block: 16px;
        padding: 16px;
    }
}
```

### Respect reduced-motion preferences

```css
@media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
        scroll-behavior: auto !important;
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}
```

Use these examples as starting points. Inspect the existing styles before adding overrides, particularly when working with a theme or third-party component library.

---

## Troubleshooting Workflow

Follow this sequence when investigating a mobile UI/UX issue.

```text
1. Reproduce the issue
        |
        v
2. Identify the affected page and interaction
        |
        v
3. Record viewport size and browser
        |
        v
4. Inspect HTML and computed CSS
        |
        v
5. Check console and network errors
        |
        v
6. Identify the actual cause
        |
        v
7. Apply one targeted fix
        |
        v
8. Test nearby viewport widths
        |
        v
9. Test keyboard and touch interactions
        |
        v
10. Verify desktop layout remains functional
        |
        v
11. Check accessibility and performance
        |
        v
12. Document the result
```

Avoid changing multiple unrelated CSS rules at once. Small, targeted changes make it easier to identify which fix resolved the problem.

---

## Mobile UX Testing Checklist

### Layout

* [ ] No unintended horizontal scrolling
* [ ] Content fits the viewport
* [ ] Images scale correctly
* [ ] Text wraps properly
* [ ] Grid layouts adapt to smaller widths
* [ ] Sticky elements do not hide content

### Navigation

* [ ] Menu opens and closes correctly
* [ ] Navigation links are usable
* [ ] Dropdowns remain inside the viewport
* [ ] Keyboard navigation works
* [ ] Focus is visible

### Forms

* [ ] Labels are visible
* [ ] Input fields fit the screen
* [ ] Validation errors are understandable
* [ ] Mobile keyboard does not block critical actions
* [ ] Submission works correctly

### Interactive elements

* [ ] Buttons are easy to activate
* [ ] Touch targets have adequate spacing
* [ ] Hover is not the only interaction
* [ ] Modals can be dismissed
* [ ] Loading and error states are clear

### Accessibility

* [ ] Text contrast is sufficient
* [ ] Images have appropriate alt text
* [ ] Controls have accessible names
* [ ] Page remains usable when text is enlarged
* [ ] Reduced-motion preferences are respected

### Performance

* [ ] Images are appropriately sized
* [ ] Unnecessary assets are minimized
* [ ] No repeated JavaScript errors
* [ ] Important content loads promptly
* [ ] Layout does not shift excessively during loading

---

## Recommended Resources

* [W3C Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
* [MDN Responsive Design](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/CSS_layout/Responsive_Design)
* [Chrome DevTools Device Mode](https://developer.chrome.com/docs/devtools/device-mode/)
* [MDN CSS Media Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries)
* [web.dev Learn Accessibility](https://web.dev/learn/accessibility/)

---

## Important Notes

* Always reproduce the issue before changing code.
* Test on multiple viewport sizes.
* Prefer responsive CSS over fixed dimensions.
* Avoid hiding overflow without understanding its cause.
* Test critical interactions on real mobile devices.
* Check accessibility alongside visual appearance.
* Back up existing theme or stylesheet files before making production changes.
* When modifying WordPress themes, prefer a child theme or a suitable custom CSS mechanism.
* Avoid editing third-party theme files directly because updates may overwrite the changes.

---

## Final Principle

**Good mobile UX is not just about fitting content onto a smaller screen.**

It requires clear navigation, readable content, usable controls, predictable interactions, accessibility, and reliable performance.

The recommended approach is to reproduce the issue, inspect the underlying cause, apply a targeted fix, and test across devices and interaction methods.

---

## Disclaimer

This repository is intended for educational and troubleshooting purposes. Code examples may require adjustments for your specific website, framework, theme, browser support requirements, or design system. Test changes before deploying them to production.
