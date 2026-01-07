# Aether button variant implementation

## Overview

This document describes the implementation of the `aether` variant for `sp-button`, which includes a glassmorphic visual effect and particle animation on click.

## Implementation files

### 1. Button.ts

**Location:** `packages/button/src/Button.ts`

**Changes made:**

1. Added `aetherParticles` property (line 187-188):
   - Enables/disables particle effects
   - Default: `true`
   - Attribute: `aether-particles`

2. Modified `renderButton()` method to render custom markup for aether variant (lines 207-225)
   - Creates nested structure with effect layers
   - Includes `.ether`, `.ether-wrapper`, `.ether-blur`, `.ether-gradient`, `.ether-reflection`
   - Nested `.button` div contains the icon slot and label

3. Added `handleAetherClick()` method (lines 227-236):
   - Handles click events for aether variant
   - Creates 30 particles on each click
   - Respects `aetherParticles` property
   - Checks for Web Animations API support

4. Added `createParticle()` method (lines 238-275):
   - Creates individual particle elements
   - Appends to `document.body` (light DOM) to escape component bounds
   - Random size (2-12px), color (blue/purple palette), and destination
   - Uses Web Animations API for smooth animation
   - Auto-removes particles after animation completes

### 2. button.css

**Location:** `packages/button/src/button.css`

**Changes made:**

Added comprehensive styling for the aether variant (lines 106-223):

1. **Custom properties** (line 106-109):
   - `--mod-button-min-width: 48px`
   - `--mod-button-block-size: 32px`

2. **Container structure** (lines 111-119):
   - `.ether`: Flex container with border radius and overflow
   - `.ether-wrapper`: Positioned context with box shadow and transition

3. **Visual effect layers** (lines 121-151):
   - `.ether-blur`: Backdrop filter blur effect
   - `.ether-gradient`: Color gradient overlay (purple/magenta)
   - `.ether-reflection`: Inner highlight for glass effect

4. **Button content** (lines 153-167):
   - `.button`: Transparent background, flex layout
   - Icon sizing and z-index management
   - Label styling

5. **Interactive states** (lines 169-191):
   - `:hover`: Enhanced shadow, slight lift effect
   - `:active`: Reduced shadow, returns to base position
   - `[disabled]`: Reduced opacity

6. **Border gradient** (lines 193-223):
   - `:before` pseudo-element creates gradient border
   - Uses mask properties for border effect
   - Vendor prefixes included for cross-browser support

### 3. preview-head.html

**Location:** `storybook/preview-head.html`

**Changes made:**

Added global CSS for particle elements (lines 43-49):
```css
.aether-particle {
    border-radius: 50%;
    left: 0;
    pointer-events: none;
    position: fixed;
    top: 0;
    opacity: 0;
    z-index: 9999;
}
```

**Why global?** Particles are rendered in light DOM to escape shadow DOM boundaries and appear over the entire page.

## Usage

### Basic usage

```html
<sp-button variant="aether">
    Click me
</sp-button>
```

### With icon

```html
<sp-button variant="aether">
    <sp-icon-help slot="icon"></sp-icon-help>
    Click me
</sp-button>
```

### Disable particles

```html
<sp-button variant="aether" aether-particles="false">
    Click me
</sp-button>
```

### With sizing

```html
<sp-button variant="aether" size="l">
    Click me
</sp-button>
```

## Visual structure

The aether variant renders with this shadow DOM structure:

```
sp-button[variant="aether"]
  #shadow-root
    .ether
      .ether-wrapper
        .ether-blur          (backdrop-filter layer)
        .ether-gradient      (color overlay)
        .ether-reflection    (inner highlight)
        .button              (content container)
          slot[name="icon"]
          span.label
            slot
```

## Particle effect behavior

- **Trigger:** Click on the button
- **Count:** 30 particles per click
- **Color:** Random blue/purple hues (180-270° hue range)
- **Size:** Random 2-12px diameter
- **Duration:** Random 500-1500ms
- **Delay:** Random 0-200ms per particle
- **Distance:** Random direction within 75px radius
- **Easing:** `cubic-bezier(0, .9, .57, 1)`
- **Cleanup:** Automatic removal after animation completes

## Browser compatibility

### Web Animations API

Required for particle effects. Supported in:
- Chrome 36+
- Firefox 48+
- Safari 13.1+
- Edge 79+

The component gracefully degrades if the API is not available.

### CSS features

- **backdrop-filter:** Modern browsers (may have limited support in Firefox)
- **mask/mask-composite:** Modern browsers with vendor prefixes for Safari
- **inset:** Modern browsers (IE not supported)

## Testing

### Storybook story

A story file already exists at:
`packages/button/stories/button-aether.stories.ts`

To view the aether variant:

1. Run storybook: `yarn storybook`
2. Navigate to: Button → Aether → Fill

### Manual testing checklist

- [ ] Visual appearance matches design
- [ ] Particle effects trigger on click
- [ ] Particles render across entire viewport
- [ ] Hover state works correctly
- [ ] Active/pressed state works correctly
- [ ] Disabled state shows properly
- [ ] Works with icon slot
- [ ] Works with different sizes
- [ ] `aether-particles="false"` disables particles
- [ ] Graceful degradation without Web Animations API
- [ ] Keyboard interaction still works (Space/Enter)
- [ ] Focus states work correctly

## Known limitations

1. **Particle boundary:** Particles render in light DOM at `document.body`, so they may overlap other content depending on z-index
2. **Performance:** 30 particles per click could impact performance on lower-end devices
3. **Backdrop filter:** Limited Firefox support for backdrop-filter
4. **Pending state:** Not yet implemented for aether variant (intentionally deferred)

## Future enhancements

Potential improvements for consideration:

1. Configurable particle count via property
2. Configurable particle colors via custom properties
3. Pending state implementation for aether variant
4. Reduce particle count on lower-end devices
5. Add prefers-reduced-motion support to disable particles
6. Consider alternative visual effect for browsers without backdrop-filter support

## Notes

- The aether variant does NOT work with anchor functionality (`href` attribute) - this is intentional for the initial implementation
- The nested `.button` element is a `<div>`, not a `<button>`, because the host element handles all interaction
- Click handling remains on the host element via `ButtonBase` to maintain consistency with other variants
