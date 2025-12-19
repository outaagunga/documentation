
## Project Structure (React + Vite)

```
portfolio/
│
├── public/
│   ├── images/
│   │   ├── profile/
│   │   └── projects/
│   └── index.html
│
├── src/
│   │
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── Hero.jsx
│   │   ├── About.jsx
│   │   ├── Skills.jsx
│   │   ├── Projects.jsx
│   │   ├── Contact.jsx
│   │   └── Footer.jsx
│   │
│   ├── data/
│   │   ├── projects.js
│   │   └── skills.js
│   │
│   ├── styles/
│   │   ├── global.css
│   │   └── components.css
│   │
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
│
├── .gitignore
├── package.json
├── vite.config.js
└── README.md
```

---

## App.jsx (Beginner Style)

```jsx
import Navbar from "./components/Navbar";
import Hero from "./components/Hero";
import About from "./components/About";
import Projects from "./components/Projects";
import Contact from "./components/Contact";
import Footer from "./components/Footer";

import "./styles/global.css";
import "./styles/components.css";

function App() {
  return (
    <>
      <Navbar />
      <Hero />
      <About />
      <Projects />
      <Contact />
      <Footer />
    </>
  );
}

export default App;
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



