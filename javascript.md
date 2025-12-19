
**DOM manipulation reference**

## **Segment 1: Element Selection (Complete & Refined)**

### **Modern (Recommended)**

* `document.querySelector(selector)`
* `document.querySelectorAll(selector)`

### **Legacy / Collection-based**

* `document.getElementById(id)`
* `document.getElementsByClassName(className)`
* `document.getElementsByTagName(tagName)`
* `document.getElementsByName(name)`

📌 **Notes**

* `querySelector(All)` supports **any valid CSS selector**
* `getElementsBy*` return **live collections** (auto-update)

---

## **Segment 2: DOM Traversal (Expanded)**

### **Parent / Child Traversal**

* `element.parentNode`
* `element.parentElement`
* `element.children`
* `element.childNodes`
* `element.firstChild`
* `element.lastChild`
* `element.firstElementChild`
* `element.lastElementChild`

### **Sibling Traversal**

* `element.nextSibling`
* `element.previousSibling`
* `element.nextElementSibling`
* `element.previousElementSibling`

### **Searching Within Elements**

* `element.querySelector(selector)`
* `element.querySelectorAll(selector)`
* `element.closest(selector)` ✅ **very important**

---

## **Segment 3: Creating, Inserting & Removing Elements (Modernized)**

### **Creation**

* `document.createElement(tagName)`
* `document.createTextNode(text)`
* `document.createDocumentFragment()`

### **Insertion**

* `parent.appendChild(child)`
* `parent.append(...nodes)` ✅ modern
* `parent.prepend(...nodes)`
* `element.before(...nodes)`
* `element.after(...nodes)`
* `parent.insertBefore(newNode, referenceNode)`
* `parent.replaceChild(newNode, oldNode)`
* `element.replaceWith(...nodes)`

### **Removal**

* `element.remove()`
* `parent.removeChild(child)`
* `element.replaceChildren(...nodes)`

📌 **Best Practice**
Prefer `append`, `prepend`, `before`, `after`, `remove` over older APIs when possible.

---

## **Segment 4: Modifying Content (Full Coverage)**

### **Text & HTML**

* `element.innerHTML`
* `element.outerHTML`
* `element.textContent`
* `element.innerText`

⚠️ `innerHTML` → **XSS risk** if user input is involved

### **Value-based Elements**

* `input.value`
* `input.checked`
* `input.disabled`
* `select.selectedIndex`

---

## **Segment 5: Attributes, Properties & Data Attributes**

### **Attributes**

* `element.setAttribute(name, value)`
* `element.getAttribute(name)`
* `element.hasAttribute(name)`
* `element.removeAttribute(name)`
* `element.toggleAttribute(name, force)`

### **Properties**

* `element.id`
* `element.className`
* `element.href`
* `element.src`

### **Data Attributes**

* `element.dataset`
* `element.dataset.key = value`

---

## **Segment 6: Class Manipulation (Critical in Modern UI)**

* `element.classList.add(className)`
* `element.classList.remove(className)`
* `element.classList.toggle(className, force)`
* `element.classList.contains(className)`
* `element.classList.replace(oldClass, newClass)`

📌 **Always prefer `classList` over `className`**

---

## **Segment 7: Styling & Layout (Advanced)**

### **Inline Styles**

* `element.style.property`
* `element.style.setProperty(name, value)`
* `element.style.removeProperty(name)`

### **Computed Styles**

* `window.getComputedStyle(element)`

### **Layout & Dimensions**

* `element.offsetWidth`
* `element.offsetHeight`
* `element.clientWidth`
* `element.clientHeight`
* `element.scrollWidth`
* `element.scrollHeight`
* `element.getBoundingClientRect()`

---

## **Segment 8: Event Handling (Comprehensive)**

### **Basic Events**

* `element.addEventListener(type, handler, options)`
* `element.removeEventListener(type, handler)`

### **Event Object**

* `event.target`
* `event.currentTarget`
* `event.preventDefault()`
* `event.stopPropagation()`
* `event.stopImmediatePropagation()`

### **Event Options**

* `{ once: true }`
* `{ passive: true }`
* `{ capture: true }`

### **Common Events**

* `click`
* `submit`
* `input`
* `change`
* `keydown`, `keyup`
* `mouseover`, `mouseenter`
* `scroll`
* `load`
* `DOMContentLoaded`

---

## **Segment 9: Forms & Input Handling (Extended)**

* `new FormData(form)`
* `form.elements`
* `form.reset()`
* `form.submit()`
* `input.focus()`
* `input.blur()`
* `input.select()`

---

## **Segment 10: DOM State & Utilities**

* `document.readyState`
* `document.body`
* `document.documentElement`
* `document.title`
* `document.activeElement`
* `document.contains(node)`
* `element.contains(node)`

---

## **Segment 11: Performance & Observation (Advanced / Pro Level)**

### **Mutation & Visibility**

* `MutationObserver`
* `IntersectionObserver`
* `ResizeObserver`

### **Performance Helpers**

* `requestAnimationFrame(callback)`
* `requestIdleCallback(callback)`

---

## **Segment 12: Cloning, Importing & Templates**

* `element.cloneNode(deep)`
* `document.importNode(node, deep)`
* `<template>` + `template.content.cloneNode(true)`

---

If you want, I can:  
* ✅ Add **code examples for each section**
* ✅ Map these methods to **real portfolio projects**
* ✅ Show **which methods you actually use 80% of the time**

Excellent choice 👍
This is **exactly how senior developers think**: *tools mapped to real use cases*.

Below is a **practical mapping of DOM methods → real portfolio website features**, based on the kind of portfolio you’re building (Home, About, Projects, Skills, Contact, etc.).

---

# **DOM Methods Mapped to Portfolio Projects (Real-World Use Cases)**

---

## **1. Navigation & Section Switching (SPA-like behavior)**

### **Use case**

* Click navbar links → smoothly scroll or show/hide sections.

### **DOM Methods Used**

* `querySelector(All)`
* `addEventListener`
* `classList`
* `scrollIntoView`
* `closest`

### **Example**

```js
document.querySelectorAll('.nav-link').forEach(link => {
  link.addEventListener('click', e => {
    e.preventDefault();

    const targetId = link.getAttribute('href');
    document.querySelector(targetId).scrollIntoView({
      behavior: 'smooth'
    });
  });
});
```

---

## **2. Active Navigation Highlight (UX Polish)**

### **Use case**

* Highlight active section while scrolling.

### **DOM Methods Used**

* `IntersectionObserver`
* `classList`
* `dataset`

### **Example**

```js
const sections = document.querySelectorAll('section');
const navLinks = document.querySelectorAll('.nav-link');

const observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      navLinks.forEach(link =>
        link.classList.toggle(
          'active',
          link.dataset.section === entry.target.id
        )
      );
    }
  });
}, { threshold: 0.6 });

sections.forEach(section => observer.observe(section));
```

---

## **3. Project Cards Rendering (Dynamic Content)**

### **Use case**

* Load projects from JS object instead of hardcoding HTML.

### **DOM Methods Used**

* `createElement`
* `append`
* `textContent`
* `classList`

### **Example**

```js
const projects = [
  { title: 'Portfolio Website', tech: 'HTML, CSS, JS' },
  { title: 'Data Dashboard', tech: 'Python, SQL' }
];

const container = document.querySelector('#projects');

projects.forEach(project => {
  const card = document.createElement('div');
  card.classList.add('project-card');

  card.innerHTML = `
    <h3>${project.title}</h3>
    <p>${project.tech}</p>
  `;

  container.append(card);
});
```

---

## **4. Project Filter (Skills / Categories)**

### **Use case**

* Filter projects by skill (JS, Python, SQL).

### **DOM Methods Used**

* `dataset`
* `classList`
* `addEventListener`
* `querySelectorAll`

### **Example**

```js
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const filter = btn.dataset.filter;

    document.querySelectorAll('.project-card').forEach(card => {
      card.classList.toggle(
        'hidden',
        filter !== 'all' && card.dataset.tech !== filter
      );
    });
  });
});
```

---

## **5. Skills Progress Bars (Animation on Scroll)**

### **Use case**

* Animate skill bars when section enters viewport.

### **DOM Methods Used**

* `IntersectionObserver`
* `style`
* `dataset`

### **Example**

```js
const skills = document.querySelectorAll('.skill-bar');

const skillObserver = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.style.width = entry.target.dataset.level;
    }
  });
}, { threshold: 0.5 });

skills.forEach(skill => skillObserver.observe(skill));
```

---

## **6. Theme Toggle (Dark / Light Mode)**

### **Use case**

* Toggle dark mode and persist preference.

### **DOM Methods Used**

* `classList`
* `localStorage`
* `addEventListener`

### **Example**

```js
const toggle = document.querySelector('#theme-toggle');

toggle.addEventListener('click', () => {
  document.body.classList.toggle('dark');

  localStorage.setItem(
    'theme',
    document.body.classList.contains('dark') ? 'dark' : 'light'
  );
});
```

---

## **7. Mobile Menu (Responsive Navigation)**

### **Use case**

* Hamburger menu toggle on small screens.

### **DOM Methods Used**

* `classList`
* `addEventListener`
* `closest`

### **Example**

```js
const menuBtn = document.querySelector('.menu-btn');
const nav = document.querySelector('.nav');

menuBtn.addEventListener('click', () => {
  nav.classList.toggle('open');
});
```

---

## **8. Contact Form Validation (Client-Side)**

### **Use case**

* Prevent empty submissions.
* Show error messages.

### **DOM Methods Used**

* `addEventListener`
* `FormData`
* `textContent`
* `classList`

### **Example**

```js
const form = document.querySelector('#contact-form');
const error = document.querySelector('.error');

form.addEventListener('submit', e => {
  e.preventDefault();

  const data = new FormData(form);
  if (!data.get('email')) {
    error.textContent = 'Email is required';
    error.classList.add('show');
    return;
  }

  error.classList.remove('show');
  form.reset();
});
```

---

## **9. Modal Popups (Project Details)**

### **Use case**

* Click project → open modal.

### **DOM Methods Used**

* `classList`
* `addEventListener`
* `closest`

### **Example**

```js
document.addEventListener('click', e => {
  const card = e.target.closest('.project-card');
  if (!card) return;

  document.querySelector('.modal').classList.add('open');
});

document.querySelector('.modal-close')
  .addEventListener('click', () => {
    document.querySelector('.modal').classList.remove('open');
  });
```

---

## **10. Lazy Loading Images (Performance)**

### **Use case**

* Load images only when visible.

### **DOM Methods Used**

* `IntersectionObserver`
* `dataset`
* `setAttribute`

### **Example**

```js
const images = document.querySelectorAll('img[data-src]');

const imgObserver = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.src = entry.target.dataset.src;
      imgObserver.unobserve(entry.target);
    }
  });
});

images.forEach(img => imgObserver.observe(img));
```

---

## **11. Scroll to Top Button**

### **Use case**

* Show button after scrolling.

### **DOM Methods Used**

* `scrollY`
* `classList`
* `addEventListener`

### **Example**

```js
const btn = document.querySelector('.scroll-top');

window.addEventListener('scroll', () => {
  btn.classList.toggle('show', window.scrollY > 300);
});

btn.addEventListener('click', () => {
  window.scrollTo({ top: 0, behavior: 'smooth' });
});
```

---

## **12. Page Load Animations**

### **Use case**

* Animate hero section on load.

### **DOM Methods Used**

* `DOMContentLoaded`
* `classList`

### **Example**

```js
document.addEventListener('DOMContentLoaded', () => {
  document.body.classList.add('loaded');
});
```

---

## **How This Looks in a Real Portfolio**

Your portfolio demonstrates:

* ✅ DOM mastery (not just syntax)
* ✅ Performance awareness
* ✅ UX best practices
* ✅ Interview-ready code patterns

---

### Want next?

* 🔥 **Refactor this into modules (clean architecture)**
* 🔥 **Add accessibility (ARIA + keyboard handling)**
* 🔥 **Turn this into a GitHub-ready project template**

Excellent move 👏
Accessibility (a11y) is a **senior-level skill** and interviewers *absolutely notice it*. Below is a **practical, portfolio-focused guide** showing **ARIA + keyboard handling mapped to real UI features**.

Everything here is **Vanilla JS**, production- including **code you can drop into your portfolio**.

---

# **Accessibility in Portfolio Projects (ARIA + Keyboard Handling)**

---

## **1. Semantic HTML First (Baseline Rule)**

> ARIA **does NOT replace** semantic HTML — it *enhances it*.

### Prefer:

```html
<nav></nav>
<header></header>
<main></main>
<section></section>
<button></button>
<a></a>
<form></form>
```

### Avoid:

```html
<div onclick="..."></div>
```

If you must use non-semantic elements → **add ARIA + keyboard support**.

---

## **2. Accessible Navigation (Keyboard + Screen Readers)**

### **Use Case**

* Users navigate navbar with **Tab + Enter**

### **HTML**

```html
<nav aria-label="Main navigation">
  <a href="#home" class="nav-link">Home</a>
  <a href="#projects" class="nav-link">Projects</a>
</nav>
```

### **Keyboard Enhancement**

```js
document.querySelectorAll('.nav-link').forEach(link => {
  link.addEventListener('keydown', e => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      link.click();
    }
  });
});
```

✅ Links already support keyboard — this is **extra safety**

---

## **3. Mobile Menu (ARIA + Focus Control)**

### **Use Case**

* Hamburger menu toggled by mouse or keyboard

### **HTML**

```html
<button id="menuBtn"
  aria-expanded="false"
  aria-controls="mobileMenu"
  aria-label="Open menu">
  ☰
</button>

<nav id="mobileMenu" hidden>
  <a href="#about">About</a>
  <a href="#contact">Contact</a>
</nav>
```

### **JS**

```js
const menuBtn = document.getElementById('menuBtn');
const menu = document.getElementById('mobileMenu');

menuBtn.addEventListener('click', toggleMenu);
menuBtn.addEventListener('keydown', e => {
  if (e.key === 'Enter' || e.key === ' ') toggleMenu();
});

function toggleMenu() {
  const isOpen = menuBtn.getAttribute('aria-expanded') === 'true';

  menuBtn.setAttribute('aria-expanded', !isOpen);
  menu.hidden = isOpen;
}
```

---

## **4. Modal (Dialog Role + Focus Trap)**

### **Use Case**

* Project details modal

### **HTML**

```html
<div class="modal" role="dialog" aria-modal="true" aria-hidden="true">
  <button class="close">Close</button>
  <h2>Project Title</h2>
</div>
```

### **JS**

```js
const modal = document.querySelector('.modal');
const closeBtn = modal.querySelector('.close');

function openModal() {
  modal.setAttribute('aria-hidden', 'false');
  modal.classList.add('open');
  closeBtn.focus();
}

function closeModal() {
  modal.setAttribute('aria-hidden', 'true');
  modal.classList.remove('open');
}

closeBtn.addEventListener('click', closeModal);

document.addEventListener('keydown', e => {
  if (e.key === 'Escape' && modal.classList.contains('open')) {
    closeModal();
  }
});
```

✅ Escape key
✅ Focus management
✅ Screen-reader friendly

---

## **5. Project Cards (Clickable + Keyboard Accessible)**

### **Use Case**

* Click card or press Enter

### **HTML**

```html
<div class="project-card" tabindex="0" role="button"
     aria-label="View project details">
  Project A
</div>
```

### **JS**

```js
document.querySelectorAll('.project-card').forEach(card => {
  card.addEventListener('click', openModal);

  card.addEventListener('keydown', e => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      openModal();
    }
  });
});
```

---

## **6. Form Accessibility (Errors + Live Regions)**

### **Use Case**

* Screen reader announces validation errors

### **HTML**

```html
<input id="email" aria-describedby="emailError">
<p id="emailError" role="alert"></p>
```

### **JS**

```js
const error = document.getElementById('emailError');

if (!email.value) {
  error.textContent = 'Email is required';
} else {
  error.textContent = '';
}
```

✅ `role="alert"` announces instantly

---

## **7. Buttons vs Divs (Correct Role Mapping)**

### **If you must use div**

```html
<div role="button" tabindex="0" aria-pressed="false">Toggle</div>
```

```js
const btn = document.querySelector('[role="button"]');

btn.addEventListener('click', toggle);
btn.addEventListener('keydown', e => {
  if (e.key === 'Enter' || e.key === ' ') toggle();
});

function toggle() {
  const pressed = btn.getAttribute('aria-pressed') === 'true';
  btn.setAttribute('aria-pressed', !pressed);
}
```

---

## **8. Theme Toggle (ARIA State Management)**

### **HTML**

```html
<button id="themeToggle" aria-pressed="false">
  Toggle theme
</button>
```

### **JS**

```js
const toggle = document.getElementById('themeToggle');

toggle.addEventListener('click', () => {
  const isDark = document.body.classList.toggle('dark');
  toggle.setAttribute('aria-pressed', isDark);
});
```

---

## **9. Accessible Tabs (Skills / Experience)**

### **HTML**

```html
<div role="tablist">
  <button role="tab" aria-selected="true">Skills</button>
  <button role="tab">Experience</button>
</div>
```

### **JS**

```js
const tabs = document.querySelectorAll('[role="tab"]');

tabs.forEach(tab => {
  tab.addEventListener('click', () => {
    tabs.forEach(t => t.setAttribute('aria-selected', 'false'));
    tab.setAttribute('aria-selected', 'true');
  });
});
```

---

## **10. Keyboard Shortcuts (Power Users)**

### **Use Case**

* `Alt + M` opens menu

```js
document.addEventListener('keydown', e => {
  if (e.altKey && e.key === 'm') {
    toggleMenu();
  }
});
```

---

## **11. Focus Styles (DO NOT REMOVE)**

### **CSS**

```css
:focus {
  outline: 3px solid #00f;
}

:focus-visible {
  outline: 3px solid #00f;
}
```

❌ Never do:

```css
*:focus { outline: none; }
```

---

## **12. Accessibility Checklist for Your Portfolio**

✅ Keyboard-only navigation
✅ Proper ARIA roles
✅ Focus management
✅ Escape to close modals
✅ Screen reader announcements
✅ Semantic HTML first

---

## **What Interviewers Love Hearing**

> “I always start with semantic HTML, then add ARIA only where necessary, and I test using keyboard-only navigation and screen readers.”

---

### Want next?

* 🔥 **Accessibility audit checklist**
* 🔥 **Keyboard navigation patterns**
* 🔥 **How to test accessibility (NVDA, Lighthouse)**

Excellent — **keyboard navigation patterns** are a *core accessibility competency* and come up often in **senior frontend interviews**.

Below is a **practical, portfolio-ready guide** covering the **most important keyboard interaction patterns**, aligned with **WAI-ARIA Authoring Practices**, with **real code examples** you can directly use.

---

# **Keyboard Navigation Patterns (ARIA-Aligned, Portfolio-Ready)**

---

## **Global Rules (Apply Everywhere)**

### **Standard Keys & Meaning**

| Key           | Expected Behavior           |
| ------------- | --------------------------- |
| `Tab`         | Move focus forward          |
| `Shift + Tab` | Move focus backward         |
| `Enter`       | Activate buttons/links      |
| `Space`       | Toggle buttons / checkboxes |
| `Escape`      | Close dialogs, menus        |
| `Arrow keys`  | Navigate within components  |

📌 **Never override Tab order unless absolutely necessary**

---

## **1. Buttons & Clickable Elements**

### **Pattern**

* `Tab` → focus
* `Enter` / `Space` → activate

### **HTML (Best)**

```html
<button>Submit</button>
```

### **If Using Non-Semantic Element**

```html
<div role="button" tabindex="0">Submit</div>
```

```js
const btn = document.querySelector('[role="button"]');

btn.addEventListener('keydown', e => {
  if (e.key === 'Enter' || e.key === ' ') {
    e.preventDefault();
    btn.click();
  }
});
```

---

## **2. Navigation Menus (Horizontal / Vertical)**

### **Pattern**

* `Tab` → enter menu
* `Arrow keys` → move between items
* `Enter` → activate link
* `Escape` → exit menu

### **HTML**

```html
<nav>
  <ul role="menubar">
    <li role="menuitem" tabindex="0">Home</li>
    <li role="menuitem" tabindex="-1">Projects</li>
  </ul>
</nav>
```

### **JS**

```js
const items = document.querySelectorAll('[role="menuitem"]');

items.forEach((item, index) => {
  item.addEventListener('keydown', e => {
    if (e.key === 'ArrowRight') items[index + 1]?.focus();
    if (e.key === 'ArrowLeft') items[index - 1]?.focus();
  });
});
```

---

## **3. Mobile Menu / Drawer**

### **Pattern**

* `Enter` → open
* Focus moves into menu
* `Escape` → close
* Focus returns to toggle

```js
function openMenu() {
  menu.hidden = false;
  firstLink.focus();
}

function closeMenu() {
  menu.hidden = true;
  menuBtn.focus();
}

document.addEventListener('keydown', e => {
  if (e.key === 'Escape' && !menu.hidden) closeMenu();
});
```

---

## **4. Modal / Dialog (Critical Pattern)**

### **Pattern**

* Focus trapped inside modal
* `Escape` closes modal
* Focus restored to opener

```js
const focusable = modal.querySelectorAll(
  'button, a, input, [tabindex]:not([tabindex="-1"])'
);

modal.addEventListener('keydown', e => {
  if (e.key === 'Tab') {
    const first = focusable[0];
    const last = focusable[focusable.length - 1];

    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    }

    if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  }
});
```

---

## **5. Tabs Component (Skills / Experience)**

### **Pattern**

* `ArrowLeft / ArrowRight` → switch tabs
* `Home / End` → jump to first / last
* `Enter / Space` → activate

```js
const tabs = document.querySelectorAll('[role="tab"]');

tabs.forEach((tab, index) => {
  tab.addEventListener('keydown', e => {
    if (e.key === 'ArrowRight') tabs[index + 1]?.focus();
    if (e.key === 'ArrowLeft') tabs[index - 1]?.focus();
    if (e.key === 'Home') tabs[0].focus();
    if (e.key === 'End') tabs[tabs.length - 1].focus();
  });
});
```

---

## **6. Dropdown / Select-Like Component**

### **Pattern**

* `Enter` / `Space` → open
* `Arrow keys` → navigate
* `Escape` → close

```js
dropdown.addEventListener('keydown', e => {
  if (e.key === 'Enter') openDropdown();
  if (e.key === 'Escape') closeDropdown();
});
```

---

## **7. Lists & Grids (Projects, Cards)**

### **Pattern**

* Arrow keys move between items
* Enter opens item

```html
<div role="grid">
  <div role="gridcell" tabindex="0">Project 1</div>
  <div role="gridcell" tabindex="-1">Project 2</div>
</div>
```

```js
const cells = document.querySelectorAll('[role="gridcell"]');

cells.forEach((cell, index) => {
  cell.addEventListener('keydown', e => {
    if (e.key === 'ArrowDown') cells[index + 1]?.focus();
    if (e.key === 'ArrowUp') cells[index - 1]?.focus();
  });
});
```

---

## **8. Accordion (FAQ / Experience)**

### **Pattern**

* `Enter` / `Space` toggles
* `ArrowUp / ArrowDown` moves between headers

```js
headers.forEach((header, i) => {
  header.addEventListener('keydown', e => {
    if (e.key === 'ArrowDown') headers[i + 1]?.focus();
    if (e.key === 'ArrowUp') headers[i - 1]?.focus();
  });
});
```

---

## **9. Form Fields & Validation**

### **Pattern**

* Natural tab order
* Error focus moves to first invalid input

```js
if (!email.value) {
  email.focus();
}
```

---

## **10. Skip Links (Very Important)**

### **Pattern**

* First Tab jumps to main content

```html
<a href="#main" class="skip-link">Skip to content</a>
<main id="main"></main>
```

```css
.skip-link {
  position: absolute;
  top: -40px;
}
.skip-link:focus {
  top: 0;
}
```

---

## **11. Keyboard Shortcuts (Optional / Advanced)**

```js
document.addEventListener('keydown', e => {
  if (e.altKey && e.key === 'p') {
    document.querySelector('#projects').scrollIntoView();
  }
});
```

---

## **12. Keyboard Navigation Checklist**

✅ All actions reachable by keyboard
✅ No keyboard traps
✅ Logical tab order
✅ Focus visible
✅ Escape works
✅ Arrow keys behave predictably

---

## **Interview-Ready Summary**

> “I follow WAI-ARIA keyboard patterns: Tab for navigation, arrow keys within components, Escape to exit, and I always manage focus when UI state changes.”

---

### Want next?

* 🔥 **Accessibility testing (NVDA, Lighthouse, Axe)**
* 🔥 **Common keyboard accessibility mistakes**




