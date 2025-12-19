
## 1️⃣ High-Level Structure

```plaintext
portfolio/
│
├── public/
│   ├── images/
│   │   ├── profile/
│   │   ├── projects/
│   │   └── blog/
│   ├── favicon/
│   └── index.html
│
├── src/
│   ├── app/
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── routes.jsx
│   │
│   ├── components/
│   │   ├── Navbar/
│   │   │   ├── Navbar.jsx
│   │   │   ├── Navbar.css
│   │   │   ├── Navbar.js
│   │   │
│   │   ├── Header/
│   │   │   ├── Header.jsx
│   │   │   ├── Header.css
│   │   │   ├── Header.js
│   │   │
│   │   ├── Hero/
│   │   │   ├── Hero.jsx
│   │   │   └── hero.css
│   │   │
│   │   ├── Home/
│   │   │   ├── Home.jsx
│   │   │   └── home.css
│   │   │
│   │   ├── About/
│   │   │   ├── About.jsx
│   │   │   └── about.css
│   │   │
│   │   ├── Skills/
│   │   │   ├── Skills.jsx
│   │   │   └── skills.css
│   │   │
│   │   ├── Projects/
│   │   │   ├── Projects.jsx
│   │   │   ├── ProjectCard.jsx
│   │   │   └── projects.css
│   │   │
│   │   ├── Blog/
│   │   │   ├── Blog.jsx
│   │   │   ├── BlogCard.jsx
│   │   │   └── blog.css
│   │   │
│   │   ├── Contact/
│   │   │   ├── Contact.jsx
│   │   │   └── contact.css
│   │   │
│   │   ├── Footer/
│   │   │   ├── Footer.jsx
│   │   │   ├── Footer.css
│   │   │   ├── Footer.js
│   │   │
│   │   ├── ui/
│   │   │   ├── Button.jsx
│   │   │   ├── Card.jsx
│   │   │   ├── Tag.jsx
│   │   │   └── Modal.jsx
│   │       ├── FadeIn.jsx
│   │       └── Container.jsx
│   ├── data/
│   │   ├── projects.js
│   │   ├── skills.js
│   │   └── blog.js
│   │
│   ├── script/
│   │   ├── useScrollAnimation.js
│   │   ├── useIntersection.js
│   │   └── useForm.js
│   │   ├── dom.js
│   │   ├── animations.js
│   │   ├── validators.js
│   │   └── constants.js
│   │
│   ├── styles/
│   │   ├── base/
│   │   │   ├── reset.css
│   │   │   ├── variables.css
│   │   │   └── typography.css
│   │   └── main.css
│   └── index.css
│
├── .gitignore
├── package.json
├── vite.config.js
└── README.md
```

---

Example:

### `utils/animations.js` (Vanilla JS)

```js
export function fadeInOnScroll(element) {
  element.classList.add('fade-in-visible');
}
```

### `hooks/useScrollAnimation.js`

```js
import { useEffect } from 'react';
import { fadeInOnScroll } from '../utils/animations';

export default function useScrollAnimation(ref) {
  useEffect(() => {
    if (!ref.current) return;
    fadeInOnScroll(ref.current);
  }, []);
}
```
---

## 4️⃣ `App.jsx` Composition

```jsx
import Navbar from '../components/layout/Navbar';
import Hero from '../sections/Hero/Hero';
import About from '../sections/About/About';
import Skills from '../sections/Skills/Skills';
import Projects from '../sections/Projects/Projects';
import Blog from '../sections/Blog/Blog';
import Contact from '../sections/Contact/Contact';
import Footer from '../components/layout/Footer';

export default function App() {
  return (
    <>
      <Navbar />
      <Hero />
      <About />
      <Skills />
      <Projects />
      <Blog />
      <Contact />
      <Footer />
    </>
  );
}
```

---

### `data/projects.js`

```js
export const projects = [
  {
    id: 1,
    title: "Customer Analytics Dashboard",
    description: "Interactive analytics platform...",
    tech: ["Python", "Tableau", "SQL"],
    link: "#"
  }
];
```



