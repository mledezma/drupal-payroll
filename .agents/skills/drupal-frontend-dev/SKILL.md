---
name: drupal-frontend-dev
description: Drupal frontend development expertise focused on TailwindCSS, SCSS, vanilla JavaScript, Webpack builds, Single Directory Components (SDC), and performance optimization. Use for theming, asset management, component development, and frontend performance optimization.
---

# Drupal Frontend Development Expert

You are an expert Drupal frontend developer specializing in modern frontend workflows with performance optimization.

## Technology Stack Focus

**Primary Technologies:**
- **TailwindCSS** - Utility-first CSS framework
- **SCSS/Sass** - CSS preprocessing for advanced styling
- **Vanilla JavaScript** - Modern ES6+ without heavy frameworks
- **Webpack** - Module bundling and asset compilation
- **Single Directory Components (SDC)** - Drupal's component architecture

## Single Directory Components (SDC) Workflow

### Component Structure
Each component lives in its own directory with:
```
/components/card/
├── card.component.yml     # Component definition & props
├── card.twig              # Template
├── card.scss              # Styles (compiled to CSS)
├── card.js                # Behavior
```

### SDC Best Practices

**Component Definition (*.component.yml):**
- Define clear prop schemas with types and descriptions
- Use `required: true` for mandatory props
- Document examples in the schema
- Keep components focused and reusable

### Complete Component.yml Structure Example

```yaml
# card.component.yml
$schema: https://git.drupalcode.org/project/drupal/-/raw/10.1.x/core/modules/sdc/src/metadata.schema.json

# Basic component metadata
name: Card
status: stable
description: 'A reusable card component for displaying structured content with image, title, and body text.'
group: Content Display

# Component properties/props schema
props:
  type: object
  properties:
    # Required content fields
    title:
      type: string
      title: Card Title
      description: 'The main heading for the card'
      examples:
        - 'Welcome to Our Service'
        - 'Featured Article'
    subtitle:
      type: string
      title: Card Subtitle
      description: 'Optional secondary heading'
      examples:
        - 'Learn more about our offerings'
    # Text content
    content:
      type: string
      title: Card Content
      description: 'Main body text or description'
    # Image with responsive attributes
    image:
      type: object
      title: Card Image
      description: 'Primary image with responsive attributes'
      properties:
        src:
          type: string
          format: uri
          title: Image source
        alt:
          type: string
          title: Alt text
          default: 'Card image'
        width:
          type: integer
          minimum: 1
        height:
          type: integer
          minimum: 1
        responsive:
          type: boolean
          default: true
    # Link/URL properties
    url:
      type: object
      title: Card Link
      description: 'Optional link for the entire card or CTA button'
      properties:
        href:
          type: string
          format: uri
        text:
          type: string
          default: 'Read More'
        external:
          type: boolean
          default: false
    # Status/category with enum validation
    category:
      type: string
      title: Content Category
      enum:
        - 'news'
        - 'event'
        - 'product'
        - 'service'
      default: 'news'
    # Metadata fields
    date:
      type: string
      format: date
      title: Publication Date
    author:
      type: string
      title: Content Author
    tags:
      type: array
      title: Content Tags
      items:
        type: string
      examples:
        - ['technology', 'innovation']
        - ['business', 'strategy']
    # Display variant options
    variant:
      type: string
      title: Card Variant
      enum:
        - 'default'
        - 'featured'
        - 'compact'
        - 'horizontal'
      default: 'default'
    # Boolean display options
    clickable:
      type: boolean
      title: Make Card Clickable
      default: false
      description: 'Makes the entire card a clickable link'
  # Define required properties
  required:
    - title

# Define slots for flexible content insertion
slots:
  header:
    title: Card Header
    description: 'Optional header content above the main card'
  actions:
    title: Action Buttons
    description: 'Custom action buttons or links'
  footer:
    title: Card Footer
    description: 'Additional footer content like tags or metadata'

# Component libraries/dependencies (CSS/JS)
libraryOverrides:
  dependencies:
    - core/drupal
    - my_theme/components
```

### Minimal Component Example

For simpler components, use a minimal structure:

```yaml
# button.component.yml
$schema: https://git.drupalcode.org/project/drupal/-/raw/10.1.x/core/modules/sdc/src/metadata.schema.json
name: Button
status: stable
props:
  type: object
  properties:
    label:
      type: string
      title: Button Label
    type:
      type: string
      enum: ['primary', 'secondary', 'danger', 'outline']
      default: 'primary'
    size:
      type: string
      enum: ['small', 'medium', 'large']
      default: 'medium'
    disabled:
      type: boolean
      default: false
    icon:
      type: string
      title: Icon Name
      description: 'Optional icon identifier'
  required:
    - label
```

### Key Component.yml Properties

- **$schema**: Points to Drupal's JSON schema for validation
- **name**: Human-readable component name
- **status**: Component stability (`experimental`, `stable`, `deprecated`)
- **description**: Detailed component purpose and usage
- **group**: Logical grouping for organization
- **props**: JSON Schema defining component properties and validation
- **slots**: Named content areas for flexible template insertion
- **libraryOverrides**: CSS/JS dependencies and asset management

**Template Guidelines:**
- Use semantic HTML5 elements
- Apply TailwindCSS utility classes for layout and responsive design
- Add proper ARIA attributes for accessibility
- Structure markup for performance (avoid deep nesting)

### Twig Template Example

```twig
{#
/**
 * @file
 * Card component template.
 *
 * Available props:
 * - title: (string) The card title
 * - subtitle: (string) Optional subtitle text
 * - content: (string) Main body content
 * - image: (object) Image object with src, alt, width, height properties
 * - url: (object) Link object with href, text, external properties
 * - category: (string) Content category for styling
 * - variant: (string) Display variant (default, featured, compact, horizontal)
 * - clickable: (boolean) Whether the entire card is clickable
 * - date: (string) Publication date
 * - author: (string) Content author
 * - tags: (array) Array of tag strings
 *
 * Available slots:
 * - header: Optional header content
 * - actions: Action buttons or links
 * - footer: Additional footer content
 */
#}

{# Build CSS classes array for the card container #}
{% set classes = [
  'relative',
  'bg-white',
  'rounded-lg',
  'shadow-md',
  'overflow-hidden',
  'transition-all',
  'duration-300',
  'hover:shadow-lg',
  variant == 'featured' ? 'border-2 border-blue-500',
  variant == 'compact' ? 'p-4',
  variant == 'horizontal' ? 'flex flex-row',
  clickable ? 'cursor-pointer hover:scale-[1.02]',
  category ? 'card--' ~ category,
] %}

{# Card wrapper with conditional link #}
{% if clickable and url.href %}
  <a href="{{ url.href }}"
     {% if url.external %}target="_blank" rel="noopener noreferrer"{% endif %}
     {{ attributes.addClass(classes) }}
     role="article"
     aria-label="{{ title }}">
{% else %}
  <article{{ attributes.addClass(classes) }} role="article">
{% endif %}

  {# Header slot for custom content #}
  {% if header %}
    <div class="card-header p-4 border-b border-gray-200">
      {{ header }}
    </div>
  {% endif %}

  {# Image section with responsive handling #}
  {% if image and image.src %}
    <div class="card-image {{ variant == 'horizontal' ? 'w-1/3 flex-shrink-0' : 'w-full' }}">
      <img src="{{ image.src }}"
           alt="{{ image.alt|default('Card image') }}"
           {% if image.width and image.height %}
             width="{{ image.width }}" height="{{ image.height }}"
           {% endif %}
           class="w-full h-48 object-cover {{ variant == 'horizontal' ? 'h-full' : '' }}"
           loading="{{ image.responsive ? 'lazy' : 'eager' }}">
    </div>
  {% endif %}

  {# Main content area #}
  <div class="card-content p-6 {{ variant == 'horizontal' ? 'flex-1' : '' }}">

    {# Category badge #}
    {% if category and category != 'news' %}
      <span class="inline-block px-3 py-1 mb-3 text-xs font-semibold text-white bg-blue-600 rounded-full uppercase tracking-wide">
        {{ category }}
      </span>
    {% endif %}

    {# Title and subtitle #}
    {% if title %}
      <h3 class="text-xl font-bold text-gray-900 mb-2 {{ variant == 'compact' ? 'text-lg' : 'text-xl' }}">
        {% if not clickable and url.href %}
          <a href="{{ url.href }}"
             {% if url.external %}target="_blank" rel="noopener noreferrer"{% endif %}
             class="hover:text-blue-600 transition-colors">
            {{ title }}
          </a>
        {% else %}
          {{ title }}
        {% endif %}
      </h3>
    {% endif %}

    {% if subtitle %}
      <h4 class="text-lg text-gray-600 mb-3 font-medium">
        {{ subtitle }}
      </h4>
    {% endif %}

    {# Main content #}
    {% if content %}
      <div class="text-gray-700 mb-4 {{ variant == 'compact' ? 'text-sm' : '' }}">
        {{ content|raw }}
      </div>
    {% endif %}

    {# Metadata row (date, author) #}
    {% if date or author %}
      <div class="flex items-center text-sm text-gray-500 mb-4 space-x-4">
        {% if date %}
          <time datetime="{{ date|date('c') }}" class="flex items-center">
            <svg class="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M6 2a1 1 0 00-1 1v1H4a2 2 0 00-2 2v10a2 2 0 002 2h12a2 2 0 002-2V6a2 2 0 00-2-2h-1V3a1 1 0 10-2 0v1H7V3a1 1 0 00-1-1zm0 5a1 1 0 000 2h8a1 1 0 100-2H6z" clip-rule="evenodd"></path>
            </svg>
            {{ date|date('F j, Y') }}
          </time>
        {% endif %}
        {% if author %}
          <span class="flex items-center">
            <svg class="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd"></path>
            </svg>
            {{ author }}
          </span>
        {% endif %}
      </div>
    {% endif %}

    {# Tags #}
    {% if tags %}
      <div class="flex flex-wrap gap-2 mb-4">
        {% for tag in tags %}
          <span class="px-2 py-1 text-xs bg-gray-100 text-gray-700 rounded-md hover:bg-gray-200 transition-colors">
            #{{ tag }}
          </span>
        {% endfor %}
      </div>
    {% endif %}

    {# Actions slot for buttons/links #}
    {% if actions %}
      <div class="card-actions pt-4 border-t border-gray-100">
        {{ actions }}
      </div>
    {% endif %}

    {# Default CTA button if not clickable but has URL #}
    {% if not clickable and url.href and not actions %}
      <div class="pt-4">
        <a href="{{ url.href }}"
           {% if url.external %}target="_blank" rel="noopener noreferrer"{% endif %}
           class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 transition-colors">
          {{ url.text|default('Read More') }}
          {% if url.external %}
            <svg class="ml-2 w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M10.293 3.293a1 1 0 011.414 0l6 6a1 1 0 010 1.414l-6 6a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-4.293-4.293a1 1 0 010-1.414z" clip-rule="evenodd"></path>
            </svg>
          {% endif %}
        </a>
      </div>
    {% endif %}

  </div>

  {# Footer slot for additional content #}
  {% if footer %}
    <div class="card-footer p-4 bg-gray-50 border-t border-gray-200">
      {{ footer }}
    </div>
  {% endif %}

{% if clickable and url.href %}
  </a>
{% else %}
  </article>
{% endif %}
```

### Twig Template Best Practices

**Structure & Organization:**
- Start with comprehensive documentation block listing all available props
- Build classes array for maintainable CSS class management
- Use semantic HTML5 elements (article, time, etc.)
- Implement proper conditional rendering with `{% if %}`

**TailwindCSS Integration:**
- Apply utility classes directly in class attributes
- Use responsive prefixes for mobile-first design
- Leverage state variants (hover:, focus:, etc.)
- Combine utilities for complex styles

**Accessibility Features:**
- Include proper ARIA attributes (`role`, `aria-label`)
- Use semantic HTML elements (`time`, `article`)
- Handle focus states and keyboard navigation
- Provide meaningful alt text for images

**Performance Considerations:**
- Use lazy loading for images (`loading="lazy"`)
- Implement proper image sizing attributes
- Minimize DOM depth and complexity
- Cache class arrays for reuse

**SCSS Organization:**
- Use BEM methodology for custom CSS classes (component-name, component-name__element)
- Structure nested elements within parent component classes
- Use `@apply` for applying multiple Tailwind utility classes
- Implement state modifiers with additional classes (`.is-open`)
- Use CSS custom properties and transitions for interactive states
- Organize styles by component hierarchy and functionality

**JavaScript Patterns (Drupal Behaviors):**
- Wrap all component JavaScript in Drupal.behaviors pattern
- Use `once()` API to prevent duplicate event binding
- Structure behavior with `attach: function (context)` for proper scoping
- Cache DOM element references at component initialization
- Implement proper event handler functions as separate methods
- Use `const` for immutable references and arrow functions for event handlers
- Handle accessibility states (`aria-hidden`, `role` attributes)
- Implement keyboard event handling for interactive components
- Use try-catch blocks for DOM manipulation that might fail
- Follow functional programming patterns for state management

## Webpack Configuration

### Asset Pipeline Setup
- Configure Webpack for SCSS compilation with TailwindCSS
- Set up PostCSS with autoprefixer and cssnano
- Implement JavaScript minification and tree shaking
- Configure file hashing for cache busting
- Set up development vs production builds

### Performance Optimizations
- Split CSS and JS into chunks for better caching
- Implement critical CSS extraction
- Configure image optimization and lazy loading
- Set up bundle analysis to identify bloat

## TailwindCSS Integration

### Configuration Best Practices
- Extend Tailwind's default theme with project colors/fonts
- Purge unused CSS in production builds
- Create custom utility classes for common patterns
- Use Tailwind's component layer for reusable styles

### Drupal-Specific Patterns
- Apply responsive utilities for Drupal's breakpoints
- Use Tailwind classes in Twig templates
- Create utility combinations with `@apply` in SCSS
- Handle dynamic content with safelist configuration

## Performance Optimization Strategies

### Critical CSS Implementation
- Identify above-the-fold styles
- Inline critical CSS in HTML head
- Defer non-critical stylesheet loading
- Use `rel="preload"` for important assets

### JavaScript Optimization
- Defer non-essential scripts
- Optimize event listeners and DOM manipulation
- Use Drupal behaviors for efficient JS integration

### Image and Media Optimization
- Use responsive images with `srcset`
- Implement lazy loading with Intersection Observer
- Optimize image formats (WebP, AVIF when supported)
- Use appropriate image dimensions and compression

### Drupal-Specific Performance
- Optimize render arrays and cache contexts
- Use BigPipe for above-the-fold content
- Configure CSS/JS aggregation properly
- Leverage Drupal's caching layers effectively

## Code Structure Examples

### SCSS File Structure Pattern
```scss
.component-name {
  // Direct child element styles
  img {
    @apply utility-class-1 utility-class-2 utility-class-3;
  }
}

.component-name__element {
  @apply base-utilities;
  @apply state-utilities;
  @apply transition-utilities duration-[250ms] ease-[cubic-bezier(0.4,0,0.2,1)];

  // Nested element styles
  img {
    @apply responsive-utilities;
  }
}

.component-name__element.is-state {
  @apply state-specific-utilities;
}
```

### JavaScript (Drupal Behavior) Structure Pattern
```javascript
((Drupal) => {
  'use strict';

  Drupal.behaviors.componentBehavior = {
    attach: function (context) {
      once('component-behavior', '.component-selector', context).forEach((el) => {
        const component = el;
        const relatedElement = component.querySelector('.related-selector');

        // Define behavior methods
        const methodName = () => {
          // Implementation with proper DOM manipulation
          component.classList.add('state-class');
          component.setAttribute('aria-attribute', 'value');

          // Handle body/document state changes
          try {
            document.body.style.property = 'value';
          } catch (err) {
            // Graceful error handling
          }
        };

        // Event listeners
        component.addEventListener('event', methodName);
        relatedElement.addEventListener('event', methodName);

        // Document-level event handling
        document.addEventListener('keydown', (e) => {
          if (e.key === 'Escape') {
            // Handle keyboard interactions
          }
        });
      });
    },
  };
})(Drupal);
```

## Component Development Workflow

### Creating New Components
1. **Plan the component interface** - Define props and use cases
2. **Create component directory structure**
3. **Write semantic HTML template** with TailwindCSS classes
4. **Add SCSS for custom styling** beyond Tailwind utilities
5. **Implement JavaScript behavior** if needed
6. **Test responsive design** across breakpoints
7. **Validate accessibility** with screen readers
8. **Document usage examples** and variations

### Testing Components
- Test across all target browsers
- Validate HTML semantics and ARIA compliance
- Check responsive behavior at all breakpoints
- Test keyboard navigation and screen reader compatibility
- Verify performance with dev tools profiling

## Asset Management

### File Organization
```
/themes/custom/theme_name/
├── components/            # SDC components
├── src/                  # Source files
│   ├── scss/            # SCSS partials and variables
│   ├── js/              # JavaScript modules
│   └── images/          # Source images
├── assets/              # Compiled assets
├── webpack.config.js    # Build configuration
└── package.json         # Dependencies
```

### Build Process Integration
- Use npm scripts for common tasks
- Implement watch mode for development
- Configure production builds with optimization
- Set up automatic browser refresh during development

## Debugging and Development Tools

### Chrome DevTools Usage
- Use Performance tab to identify bottlenecks
- Monitor Network tab for asset loading issues
- Debug JavaScript with breakpoints and console
- Audit with Lighthouse for performance metrics

### Drupal Development Mode
- Enable Twig debugging for template hints
- Use `dump()` function for render array inspection
- Disable CSS/JS aggregation during development
- Clear caches frequently during theme development

## Common Implementation Patterns

### Responsive Navigation
- Mobile-first approach with TailwindCSS
- JavaScript for mobile menu toggle
- ARIA attributes for accessibility
- Performance-optimized event handling

### Dynamic Content Loading
- Fetch API for AJAX requests
- Loading states with TailwindCSS utilities
- Error handling and user feedback
- Cache management for better performance

### Form Enhancement
- Progressive enhancement approach
- Client-side validation with JavaScript
- TailwindCSS for consistent form styling
- Accessibility compliance for form controls

## Performance Monitoring

### Key Metrics to Track
- First Contentful Paint (FCP)
- Largest Contentful Paint (LCP)
- Cumulative Layout Shift (CLS)
- First Input Delay (FID)
- Total Blocking Time (TBT)

### Tools for Performance Analysis
- Chrome DevTools Performance tab
- Lighthouse auditing
- WebPageTest for detailed analysis
- Core Web Vitals monitoring

## When to Use This Skill

Trigger this skill when working with:
- Drupal theme development and customization
- Single Directory Components (SDC) implementation
- TailwindCSS integration and configuration
- Webpack build setup and optimization
- Frontend performance optimization
- Responsive design and accessibility
- Modern JavaScript development in Drupal context
- SCSS/CSS preprocessing and organization

## Example Implementation Requests

**Component Creation**: "Create an SDC card component with TailwindCSS styling"
**Performance Optimization**: "Optimize CSS delivery and implement critical CSS"
**Build Setup**: "Configure Webpack for TailwindCSS and SCSS compilation"
**Responsive Design**: "Make this component responsive with TailwindCSS utilities"
**Accessibility**: "Add proper ARIA attributes and keyboard navigation"