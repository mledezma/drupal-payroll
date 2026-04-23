# Frontend Development Guide

This guide covers frontend development practices for the Drupal 11 Payroll & Employee Management System, focusing on modern web technologies and performance optimization.

## Technology Stack

### Core Technologies

- **TailwindCSS** - Utility-first CSS framework for rapid UI development
- **SCSS/Sass** - CSS preprocessing for advanced styling and theming
- **Vanilla JavaScript** - Modern ES6+ JavaScript without heavy frameworks
- **Webpack** - Module bundling, asset compilation, and optimization
- **Single Directory Components (SDC)** - Drupal's component-based architecture

## Single Directory Components (SDC)

### Component Structure

Each component follows this directory structure:

```
/web/themes/custom/theme_name/components/
├── card/
│   ├── card.component.yml    # Component definition & props
│   ├── card.twig            # Template with TailwindCSS
│   ├── card.example.scss    # Custom SCSS styles
│   └── card.example.js      # JavaScript behavior
└── navigation/
    ├── navigation.component.yml
    ├── navigation.twig
    ├── navigation.example.scss
    └── navigation.example.js
```

### Component Definition Example

**card.component.yml**
```yaml
name: Card Component
description: Reusable card component with flexible content
props:
  properties:
    title:
      type: string
      title: Card Title
      description: The main heading for the card
    content:
      type: string
      title: Card Content
      description: The main content area
    variant:
      type: string
      title: Card Variant
      description: Visual style variant
      enum:
        - default
        - highlighted
        - warning
      default: default
slots:
  content:
    title: Card Content
    description: Main content slot
  actions:
    title: Card Actions
    description: Action buttons or links
```

**card.twig**
```twig
<div class="card {{ variant == 'highlighted' ? 'card--highlighted' : '' }} 
            {{ variant == 'warning' ? 'card--warning' : '' }} 
            bg-white rounded-lg shadow-md p-6 mb-4">
  {% if title %}
    <h3 class="text-lg font-semibold text-gray-900 mb-3">{{ title }}</h3>
  {% endif %}
  
  <div class="card__content text-gray-700">
    {% if content %}
      {{ content }}
    {% endif %}
    {% block content %}{% endblock %}
  </div>
  
  {% if actions %}
    <div class="card__actions mt-4 pt-4 border-t border-gray-200">
      {% block actions %}{{ actions }}{% endblock %}
    </div>
  {% endif %}
</div>
```

**card.example.scss**
```scss
.card {
  // Custom styles beyond TailwindCSS
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  }
  
  &--highlighted {
    @apply border-l-4 border-blue-500 bg-blue-50;
  }
  
  &--warning {
    @apply border-l-4 border-yellow-500 bg-yellow-50;
  }
  
  &__content {
    // Content-specific styles
    img {
      @apply rounded-md;
    }
  }
  
  &__actions {
    display: flex;
    gap: 0.75rem;
    justify-content: flex-end;
  }
}
```

**card.example.js**
```javascript
// Modern ES6+ JavaScript
class Card {
  constructor(element) {
    this.element = element;
    this.init();
  }
  
  init() {
    this.bindEvents();
    this.setupAnimations();
  }
  
  bindEvents() {
    const dismissButton = this.element.querySelector('.card__dismiss');
    if (dismissButton) {
      dismissButton.addEventListener('click', (e) => {
        e.preventDefault();
        this.dismiss();
      });
    }
  }
  
  dismiss() {
    this.element.style.opacity = '0';
    this.element.style.transform = 'scale(0.95)';
    
    setTimeout(() => {
      this.element.remove();
    }, 200);
  }
  
  setupAnimations() {
    // Intersection Observer for scroll animations
    if ('IntersectionObserver' in window) {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('animate-fade-in');
          }
        });
      });
      
      observer.observe(this.element);
    }
  }
}

// Initialize components
document.addEventListener('DOMContentLoaded', () => {
  const cards = document.querySelectorAll('.card');
  cards.forEach(card => new Card(card));
});
```

## Asset Management

### Directory Structure

```
/src/                         # Source files
├── scss/
│   ├── main.scss            # Main SCSS entry point
│   ├── components/          # Component-specific styles
│   ├── utilities/           # Custom utilities
│   └── variables/           # SCSS variables
├── js/
│   ├── main.js              # Main JavaScript entry point
│   ├── components/          # Component JavaScript
│   └── utilities/           # Shared utilities
└── images/                  # Source images

/assets/                     # Compiled output
├── css/
├── js/
└── images/
```

### Webpack Configuration

Key webpack features:

- **Asset optimization** - Minification, compression, cache busting
- **Hot reloading** - Development server with live updates
- **PostCSS processing** - TailwindCSS integration and autoprefixing

### TailwindCSS Integration

**tailwind.config.js**
```javascript
module.exports = {
  content: [
    './web/themes/custom/*/components/**/*.{twig,js}',
    './web/themes/custom/*/templates/**/*.twig',
    './src/**/*.{js,scss}'
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a'
        }
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif']
      }
    }
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography')
  ]
};
```

## Development Workflow

### 1. Component Development Process

1. **Plan the component**
   - Define props and slots needed
   - Consider reusability and variants
   - Plan responsive behavior

2. **Create component structure**
   ```bash
   mkdir web/themes/custom/payroll_theme/components/employee-card
   cd web/themes/custom/payroll_theme/components/employee-card
   touch employee-card.{component.yml,twig,example.scss,example.js}
   ```

3. **Define component schema** in `.component.yml`

4. **Build semantic HTML template** with TailwindCSS utilities

5. **Add custom SCSS** for styles beyond Tailwind in `.example.scss`

6. **Implement JavaScript behavior** if needed in `.example.js`

7. **Test responsiveness and accessibility**

### 2. Build Process

```bash
# Development build with watch mode
ddev npm run dev

# Production build
ddev npm run build

# Lint code
ddev npm run lint

# Fix linting issues
ddev npm run lint:fix
```

### 3. Performance Optimization

**Critical CSS Strategy:**
```scss
// Critical styles (above-the-fold)
@import 'critical';

// Non-critical styles (lazy-loaded)
@import 'non-critical';
```

## Performance Requirements

### Core Web Vitals Targets

- **LCP (Largest Contentful Paint)**: < 2.5s
- **FID (First Input Delay)**: < 100ms  
- **CLS (Cumulative Layout Shift)**: < 0.1

### Optimization Strategies

1. **Critical CSS inline delivery**
2. **Efficient asset loading** (preload, prefetch)
3. **Image optimization** with modern formats
4. **Lazy loading** for images and below-the-fold content

### Image Handling

```html
<!-- Responsive images with modern formats -->
<picture>
  <source srcset="image.webp" type="image/webp">
  <source srcset="image.avif" type="image/avif">
  <img src="image.jpg" alt="Description" loading="lazy" 
       class="w-full h-auto object-cover">
</picture>
```

## Code Quality Standards

### CSS/SCSS Guidelines

- Follow BEM methodology for custom classes
- Use TailwindCSS utilities first, custom SCSS second
- Write mobile-first responsive styles
- Maintain consistent spacing scale

### JavaScript Guidelines

- Use modern ES6+ syntax
- Implement error handling for user interactions
- Use async/await for asynchronous operations
- Follow progressive enhancement principles

### Accessibility Requirements

- Semantic HTML with proper ARIA attributes
- Keyboard navigation support
- Screen reader compatibility
- Color contrast compliance (WCAG AA)
- Focus management for interactive elements

## Testing Checklist

Before committing frontend changes:

- [ ] Test across target browsers (Chrome, Firefox, Safari, Edge)
- [ ] Validate responsive behavior at all breakpoints
- [ ] Check accessibility with screen reader testing
- [ ] Run Lighthouse performance audit
- [ ] Verify component props and slots work correctly
- [ ] Test JavaScript functionality without errors
- [ ] Validate CSS doesn't break existing components

## Development Commands

```bash
# Frontend development
ddev npm run dev              # Start development server
ddev npm run build            # Production build
ddev npm run lint             # Run linters
ddev npm run test             # Run tests

# Drupal cache management
ddev drush cr                 # Clear all caches
ddev drush cc theme-registry  # Clear theme cache

# Asset debugging  
ddev npm run analyze          # Bundle size analysis
ddev npm run debug            # Debug webpack build
```

## Deployment Considerations

1. **Build production assets** before deployment
2. **Optimize images** and compress assets
3. **Enable Drupal CSS/JS aggregation** in production
4. **Configure CDN** for static asset delivery
5. **Monitor Core Web Vitals** post-deployment

## Next Steps

- Review [Application Features](Application-Features.md) to understand data requirements
- Check [Development Workflow](Development-Workflow.md) for contribution process
- See [Troubleshooting](Troubleshooting.md) for common frontend issues