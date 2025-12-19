
### Project Structure

```plaintext
portfolio/
│
├── public/
│   ├── images/
│   │   ├── profile/
│   │   ├── projects/
│   │   └── blog/
│   ├── favicon.ico
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
│   │   │   └── Navbar.css
│   │   │
│   │   ├── Header/
│   │   │   ├── Header.jsx
│   │   │   └── Header.css
│   │   │
│   │   ├── Hero/
│   │   │   ├── Hero.jsx
│   │   │   └── Hero.css
│   │   │
│   │   ├── Home/
│   │   │   ├── Home.jsx
│   │   │   └── Home.css
│   │   │
│   │   ├── About/
│   │   │   ├── About.jsx
│   │   │   └── About.css
│   │   │
│   │   ├── Skills/
│   │   │   ├── Skills.jsx
│   │   │   └── Skills.css
│   │   │
│   │   ├── Projects/
│   │   │   ├── Projects.jsx
│   │   │   └── Projects.css
│   │   │
│   │   ├── Blog/
│   │   │   ├── Blog.jsx
│   │   │   └── Blog.css
│   │   │
│   │   ├── Contact/
│   │   │   ├── Contact.jsx
│   │   │   └── Contact.css
│   │   │
│   │   ├── Footer/
│   │   │   ├── Footer.jsx
│   │   │   └── Footer.css
│   │   │
│   │   └── ui/
│   │       ├── Button.jsx
│   │       ├── Card.jsx
│   │       ├── Tag.jsx
│   │       └── Modal.jsx
│   │
│   ├── script/
│   │   ├── data/
│   │   │   ├── projects.js
│   │   │   ├── skills.js
│   │   │   └── blog.js
│   │   │
│   │   ├── hooks/
│   │   │   ├── useScrollAnimation.js
│   │   │   ├── useIntersection.js
│   │   │   └── useForm.js
│   │   │
│   │   └── utils/
│   │       ├── dom.js
│   │       ├── animations.js
│   │       ├── validators.js
│   │       └── constants.js
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



