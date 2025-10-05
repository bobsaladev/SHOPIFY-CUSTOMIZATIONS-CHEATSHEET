# @bobsala's Shopify Components Customization Cheatsheet

A comprehensive guide for creating customizable and responsive components in Shopify themes.

## Table of Contents
- [Custom CSS Classes](#custom-css-classes)
- [Responsive Visibility](#responsive-visibility)
- [Device-Specific Components](#device-specific-components)
- [Best Practices](#best-practices)

---

## Custom CSS Classes

### Schema Definition
```json
{
  "type": "text",
  "id": "custom_class",
  "label": "Custom CSS Class",
  "info": "Add custom CSS classes separated by spaces"
}
```

### Liquid Implementation
```liquid
{% comment %} Basic usage {% endcomment %}
<div class="my-base-class {{ section.settings.custom_class }}">
  <!-- Your component content -->
</div>

{% comment %} With conditional check {% endcomment %}
{% if section.settings.custom_class != blank %}
<div class="component {{ section.settings.custom_class }}">
{% else %}
<div class="component">
{% endif %}
  <!-- Your component content -->
</div>
```

### Example Usage
```liquid
<div class="product-card {{ section.settings.custom_class }}">
  <img src="{{ product.featured_image | img_url: '300x' }}" alt="{{ product.title }}">
  <h3>{{ product.title }}</h3>
  <p>{{ product.price | money }}</p>
</div>
```

### CSS Styling
```css
/* Custom class examples */
.featured-product {
  border: 2px solid #000;
  padding: 20px;
}

.highlight-card {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.custom-animation {
  transition: transform 0.3s ease;
}
.custom-animation:hover {
  transform: translateY(-5px);
}
```

---

## Responsive Visibility

### Schema Definition for Visibility Toggles
```json
{
  "type": "header",
  "content": "Responsive Visibility"
},
{
  "type": "checkbox",
  "id": "hide_on_mobile",
  "label": "Hide on mobile",
  "default": false,
  "info": "Hide this section on mobile devices"
},
{
  "type": "checkbox",
  "id": "hide_on_tablet",
  "label": "Hide on tablet",
  "default": false,
  "info": "Hide this section on tablet devices"
},
{
  "type": "checkbox",
  "id": "hide_on_desktop",
  "label": "Hide on desktop", 
  "default": false,
  "info": "Hide this section on desktop devices"
}
```

### Liquid Implementation
```liquid
{% comment %} CSS class approach (recommended) {% endcomment %}
<div class="component-wrapper 
            {{ section.settings.custom_class }}
            {% if section.settings.hide_on_mobile %}hidden-mobile{% endif %} 
            {% if section.settings.hide_on_tablet %}hidden-tablet{% endif %} 
            {% if section.settings.hide_on_desktop %}hidden-desktop{% endif %}">
  <!-- Your component content -->
</div>

{% comment %} Conditional rendering approach {% endcomment %}
{% unless section.settings.hide_on_mobile or section.settings.hide_on_tablet or section.settings.hide_on_desktop %}
  <div class="component {{ section.settings.custom_class }}">
    <!-- Your component content -->
  </div>
{% endunless %}
```

### CSS for Responsive Hiding
```css
/* Responsive visibility utilities */
.hidden-mobile,
.hidden-tablet,
.hidden-desktop {
  display: block;
}

/* Mobile (up to 749px) */
@media screen and (max-width: 749px) {
  .hidden-mobile {
    display: none !important;
  }
}

/* Tablet (750px to 989px) */
@media screen and (min-width: 750px) and (max-width: 989px) {
  .hidden-tablet {
    display: none !important;
  }
}

/* Desktop (990px and up) */
@media screen and (min-width: 990px) {
  .hidden-desktop {
    display: none !important;
  }
}
```

### Alternative: Show/Hide by Device Type
```json
{
  "type": "select",
  "id": "visibility",
  "label": "Show on devices",
  "options": [
    {
      "value": "all",
      "label": "All devices"
    },
    {
      "value": "mobile-only",
      "label": "Mobile only"
    },
    {
      "value": "tablet-only", 
      "label": "Tablet only"
    },
    {
      "value": "desktop-only",
      "label": "Desktop only"
    },
    {
      "value": "mobile-tablet",
      "label": "Mobile & tablet"
    },
    {
      "value": "tablet-desktop",
      "label": "Tablet & desktop"
    }
  ],
  "default": "all"
}
```

```liquid
<div class="component-wrapper 
            {{ section.settings.custom_class }}
            visibility-{{ section.settings.visibility }}">
  <!-- Your component content -->
</div>
```

```css
/* Visibility classes for select approach */
.visibility-all { display: block; }

.visibility-mobile-only,
.visibility-tablet-only,
.visibility-desktop-only,
.visibility-mobile-tablet,
.visibility-tablet-desktop {
  display: none;
}

/* Mobile */
@media screen and (max-width: 749px) {
  .visibility-mobile-only,
  .visibility-mobile-tablet {
    display: block;
  }
}

/* Tablet */
@media screen and (min-width: 750px) and (max-width: 989px) {
  .visibility-tablet-only,
  .visibility-mobile-tablet,
  .visibility-tablet-desktop {
    display: block;
  }
}

/* Desktop */
@media screen and (min-width: 990px) {
  .visibility-desktop-only,
  .visibility-tablet-desktop {
    display: block;
  }
}
```

---

## Device-Specific Components

### Schema for Different Components per Device
```json
{
  "type": "header",
  "content": "Mobile-specific Content"
},
{
  "type": "checkbox", 
  "id": "enable_mobile_component",
  "label": "Use different component on mobile",
  "default": false
},
{
  "type": "html",
  "id": "mobile_component",
  "label": "Mobile component HTML",
  "info": "This will replace the main component on mobile"
},
{
  "type": "html",
  "id": "desktop_component", 
  "label": "Desktop component HTML",
  "info": "Main component for desktop/tablet"
}
```

### Liquid Implementation
```liquid
{% comment %} Device-specific component rendering {% endcomment %}
<div class="desktop-component {{ section.settings.custom_class }}">
  {{ section.settings.desktop_component }}
</div>

{% if section.settings.enable_mobile_component %}
  <div class="mobile-component {{ section.settings.custom_class }}">
    {{ section.settings.mobile_component }}
  </div>
{% endif %}
```

### CSS for Device-Specific Components
```css
/* Show/hide components based on device */
.desktop-component {
  display: block;
}
.mobile-component {
  display: none;
}

@media screen and (max-width: 749px) {
  .desktop-component {
    display: none;
  }
  .mobile-component {
    display: block;
  }
}

/* Optional: Add custom classes for device-specific styling */
@media screen and (max-width: 749px) {
  .mobile-optimized {
    font-size: 16px;
    padding: 15px;
  }
}

@media screen and (min-width: 750px) {
  .desktop-optimized {
    font-size: 18px;
    padding: 25px;
  }
}
```

---

## Complete Example Section

### Full Schema Example
```json
{
  "name": "Responsive Component",
  "settings": [
    {
      "type": "text",
      "id": "custom_class",
      "label": "Custom CSS Class",
      "info": "Add custom CSS classes separated by spaces"
    },
    {
      "type": "header",
      "content": "Responsive Visibility"
    },
    {
      "type": "checkbox",
      "id": "hide_on_mobile",
      "label": "Hide on mobile",
      "default": false
    },
    {
      "type": "checkbox",
      "id": "hide_on_tablet",
      "label": "Hide on tablet",
      "default": false
    },
    {
      "type": "checkbox",
      "id": "hide_on_desktop",
      "label": "Hide on desktop",
      "default": false
    },
    {
      "type": "header",
      "content": "Device-Specific Content"
    },
    {
      "type": "checkbox",
      "id": "enable_mobile_component",
      "label": "Use different component on mobile",
      "default": false
    },
    {
      "type": "html",
      "id": "desktop_component",
      "label": "Desktop Content",
      "default": "<div class='desktop-content'><h2>Desktop Version</h2><p>This content appears on desktop and tablet.</p></div>"
    },
    {
      "type": "html",
      "id": "mobile_component",
      "label": "Mobile Content",
      "default": "<div class='mobile-content'><h2>Mobile Version</h2><p>This content appears only on mobile.</p></div>"
    }
  ],
  "presets": [
    {
      "name": "Responsive Component",
      "category": "Custom Components"
    }
  ]
}
```

### Complete Liquid Template
```liquid
{% if section.settings.enable_mobile_component %}
  {% comment %} Device-specific components {% endcomment %}
  <div class="desktop-component 
              {{ section.settings.custom_class }}
              {% if section.settings.hide_on_desktop %}hidden-desktop{% endif %}
              {% if section.settings.hide_on_tablet %}hidden-tablet{% endif %}">
    {{ section.settings.desktop_component }}
  </div>

  <div class="mobile-component 
              {{ section.settings.custom_class }}
              {% if section.settings.hide_on_mobile %}hidden-mobile{% endif %}">
    {{ section.settings.mobile_component }}
  </div>
{% else %}
  {% comment %} Single component with responsive visibility {% endcomment %}
  <div class="universal-component 
              {{ section.settings.custom_class }}
              {% if section.settings.hide_on_mobile %}hidden-mobile{% endif %}
              {% if section.settings.hide_on_tablet %}hidden-tablet{% endif %}
              {% if section.settings.hide_on_desktop %}hidden-desktop{% endif %}">
    {{ section.settings.desktop_component }}
  </div>
{% endif %}
```

---

## Best Practices

### ✅ Do:
- **Use CSS classes** over Liquid conditionals for better performance
- **Test on actual devices**, not just browser resizing
- **Use mobile-first approach** for styling when possible
- **Combine custom classes** with visibility utilities for maximum flexibility
- **Use semantic breakpoints** that match your theme
- **Validate custom classes** to prevent CSS conflicts

### ⚠️ Avoid:
- **Hiding critical content** on mobile without alternatives
- **Overusing !important** in CSS
- **Creating too many** device-specific variations
- **Forgetting to test** tablet landscape/portrait orientations
- **Using generic class names** that might conflict with theme CSS

### 🔧 Pro Tips:
1. **Combine utilities**: Use both custom classes and visibility toggles together
2. **CSS Grid/Flexbox**: Use modern CSS for natural responsiveness
3. **Progressive enhancement**: Build for mobile, enhance for desktop
4. **Performance**: Minimize Liquid logic in favor of CSS
5. **Accessibility**: Ensure hidden content is properly accessible to screen readers
6. **SEO**: Use `display: none` carefully for SEO-sensitive content

### Common Use Cases:
- **Hero sections**: Different layouts for mobile/desktop
- **Navigation**: Simplified mobile nav vs full desktop nav
- **Product grids**: 1-column mobile vs multi-column desktop
- **Images**: Different crop ratios or sizes per device
- **Text content**: Shorter versions for mobile, detailed for desktop

---

## Browser Support
- CSS Grid: IE11+ (with prefixes)
- Flexbox: IE10+ (with prefixes)
- Media Queries: IE9+

## Notes
- Breakpoints (749px, 989px) are Shopify standard - adjust if your theme uses different values
- Always test your responsive behavior on multiple devices
- Consider using Shopify's built-in responsive utilities if available in your theme

This cheatsheet provides a complete system for creating flexible, responsive components in Shopify with custom styling and device-specific control.
