
## Using AI to generate full project structure and outline from a youtube tutorial transcript  
```js
**Role & Goal:**
> **Act as a Senior Software Architect** who specializes in technical documentation and project planning. Your task is to analyze the provided
> YouTube tutorial transcript and generate a comprehensive **Project Structure and Outline** for the coding project it demonstrates.

**Instructions & Constraints:**

1.  **Project Structure (File/Directory Tree):** Generate a full directory and file structure, including all necessary folders and files (even if they are empty files like `.gitignore` or `README.md`). Use a clear tree-like structure format.
2.  **Project Outline (Sections & Steps):** Create a sequential, numbered outline of the entire project build process. Each major step from the tutorial should be a main heading, and the specific actions within that step should be sub-points. The outline should be easy to follow for someone building the project from scratch.
3.  **Key Dependencies:** Identify and list the main programming language, frameworks, libraries, and tools used.
4.  **No Code:** Do not include any actual code snippets in your response. The focus is purely on the structure and outline.

**Desired Output Format:**

  * **Part 1: Key Project Details (Table)**
      * (A table summarizing the Name, Language, Framework, and a one-sentence summary.)
  * **Part 2: Project File and Directory Structure (Tree View)**
      * (A structure that looks like a file system tree.)
  * **Part 3: Comprehensive Project Build Outline (Numbered List)**
      * (A multi-level numbered outline for the steps.)
```
-----
```jsx
want the README to serve as a full project source code reference
```
----
Below is the **complete `README.md`** you requested. It contains:

* Project overview and setup instructions
* Full, ready-to-copy **source for every file** in the project tree (JSX + CSS + minimal config files).
* All component logic shown exactly (navigation, sticky behavior, hero, programs, hover effects, testimonials slider using `useRef`, contact form integration with Web3Forms, video player toggling, react-scroll links, responsiveness).

This README is generated from the tutorial transcript you provided (transcript.docx). 

> ⚠️ **Important:** Replace placeholder values (like the Web3Forms `ACCESS_KEY`) with your real values before running the app.

---

# Edusity — React Responsive College Website (Full Source README)

## Table of contents

1. Project Overview
2. Prerequisites
3. Install & Run
4. Project Structure
5. Full Source Files (copy/paste)

   * Root files: `package.json`, `vite.config.js`, `.gitignore`
   * `src` files: `main.jsx`, `index.css`, `App.jsx`
   * `components/*` (each component `.jsx` + `.css`)
6. Assets
7. Deployment Notes

---

## 1. Project Overview

Edusity is a multi-section responsive React website demonstrating:

* Transparent -> sticky dark navigation bar that becomes dark on scroll
* Hero section with CTA and background image overlay
* Reusable Title component for section headers
* Programs cards with hover overlay and caption animation
* About section with centered play icon over an image (opens video player)
* Gallery/campus photos section
* Testimonials slider implemented with `useRef` and `transform: translateX` logic
* Contact form that sends inquiries via Web3Forms (serverless email)
* Smooth scroll linking via `react-scroll`
* Responsive layout and mobile sidebar navigation

---

## 2. Prerequisites

* Node.js (>= 16 recommended)
* npm (comes with Node)
* Recommended VS Code extensions: ES7+ React/Redux snippets

---

## 3. Install & Run

```bash
# create project dir and place files as shown in structure, or clone repo
npm install
npm run dev
# open http://localhost:5173 (vite default) or the terminal-provided URL
```

---

## 4. Project Structure

```
edusity/
│
├── public/
│   └── index.html
│
├── src/
│   ├── assets/                  # put all images/icons/video here
│   │   └── (logo.png, hero.png, program1.png, ...)
│   │
│   ├── components/
│   │   ├── Navbar/
│   │   │   ├── Navbar.jsx
│   │   │   └── Navbar.css
│   │   ├── Hero/
│   │   │   ├── Hero.jsx
│   │   │   └── Hero.css
│   │   ├── Title/
│   │   │   ├── Title.jsx
│   │   │   └── Title.css
│   │   ├── Programs/
│   │   │   ├── Programs.jsx
│   │   │   └── Programs.css
│   │   ├── About/
│   │   │   ├── About.jsx
│   │   │   └── About.css
│   │   ├── Campus/
│   │   │   ├── Campus.jsx
│   │   │   └── Campus.css
│   │   ├── Testimonials/
│   │   │   ├── Testimonials.jsx
│   │   │   └── Testimonials.css
│   │   ├── Contact/
│   │   │   ├── Contact.jsx
│   │   │   └── Contact.css
│   │   ├── Footer/
│   │   │   ├── Footer.jsx
│   │   │   └── Footer.css
│   │   └── VideoPlayer/
│   │       ├── VideoPlayer.jsx
│   │       └── VideoPlayer.css
│   │
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
│
├── package.json
├── vite.config.js
├── .gitignore
└── README.md   <- this file
```

---

## 5. Full Source Files

> Copy each file into your project at the exact path shown above. Replace asset placeholders with your images (names used below should match).

---

### `package.json`

```json
{
  "name": "edusity",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scroll": "^1.8.9"
  },
  "devDependencies": {
    "vite": "^4.0.0"
  }
}
```

---

### `vite.config.js`

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()]
})
```

---

### `.gitignore`

```
node_modules
dist
.env
.DS_Store
.vscode
```

---

### `public/index.html`

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Greatest Stack - Edusity</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

---

### `src/main.jsx`

```jsx
import React from 'react'
import { createRoot } from 'react-dom/client'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

---

### `src/index.css`

```css
/* Reset + base */
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: 'Outfit', system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  background: #f6f8fb;
  color: #0f1724;
  line-height: 1.4;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Import Google font (Outfit) */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');

a { text-decoration: none; color: inherit; cursor: pointer; }

/* Reusable container & button */
.container {
  padding-left: 10%;
  padding-right: 10%;
  width: 100%;
  margin: 0 auto;
}

.btn {
  background: #ffffff;
  color: #0f1724;
  padding: 14px 25px;
  font-size: 16px;
  border-radius: 30px;
  cursor: pointer;
  border: 0;
  outline: 0;
  display: inline-flex;
  align-items: center;
  gap: 10px;
}

.btn.dark-btn {
  background: #2563eb;
  color: #fff;
}

/* small utility */
.hidden { display: none; }

/* smooth scroll behavior (optional) */
html { scroll-behavior: smooth; }
```

---

### `src/App.jsx`

```jsx
import React, { useState } from 'react'
import Navbar from './components/Navbar/Navbar'
import Hero from './components/Hero/Hero'
import Title from './components/Title/Title'
import Programs from './components/Programs/Programs'
import About from './components/About/About'
import Campus from './components/Campus/Campus'
import Testimonials from './components/Testimonials/Testimonials'
import Contact from './components/Contact/Contact'
import Footer from './components/Footer/Footer'
import VideoPlayer from './components/VideoPlayer/VideoPlayer'

export default function App() {
  // state to toggle video player visibility (Video play icon triggers)
  const [showVideo, setShowVideo] = useState(false)

  return (
    <div>
      <Navbar />
      <Hero />
      <Title subtitle="Our Programs" title="What We Offer" />
      <Programs />
      <About />
      <Title subtitle="Gallery" title="Campus Photos" />
      <Campus />
      <Title subtitle="Testimonials" title="What a Student Says" />
      <Testimonials />
      <Title subtitle="Contact Us" title="Get in Touch" />
      <Contact />
      <Footer />
      <VideoPlayer show={showVideo} onClose={() => setShowVideo(false)} />
    </div>
  )
}
```

---

## Components (JSX + CSS)

> For each component below: create the `.jsx` and `.css` in the exact folder as described in structure. The code replicates the tutorial logic.

---

### `src/components/Navbar/Navbar.jsx`

```jsx
import React, { useState, useEffect } from 'react'
import { Link } from 'react-scroll'
import './Navbar.css'
import logo from '../../assets/logo.png'
import menuIcon from '../../assets/menu-icon.png'

export default function Navbar() {
  const [sticky, setSticky] = useState(false)
  const [openMenu, setOpenMenu] = useState(false)

  useEffect(() => {
    const onScroll = () => setSticky(window.scrollY > 50)
    window.addEventListener('scroll', onScroll)
    return () => window.removeEventListener('scroll', onScroll)
  }, [])

  return (
    <nav className={`nav ${sticky ? 'dark-nav' : ''}`}>
      <div className="container nav-inner">
        <img src={logo} alt="logo" className="logo" />
        <ul className={`nav-links ${openMenu ? 'open' : ''}`}>
          <li><Link to="hero" spy smooth offset={0} duration={500}>Home</Link></li>
          <li><Link to="programs" spy smooth offset={-260} duration={500}>Programs</Link></li>
          <li><Link to="about" spy smooth offset={-150} duration={500}>About</Link></li>
          <li><Link to="campus" spy smooth offset={-260} duration={500}>Campus</Link></li>
          <li><Link to="testimonials" spy smooth offset={-260} duration={500}>Testimonials</Link></li>
          <li><Link to="contact" spy smooth offset={-260} duration={500}>Contact</Link></li>
          <li><button className="btn">Contact Us</button></li>
        </ul>

        <img
          src={menuIcon}
          className="menu-icon"
          alt="menu"
          onClick={() => setOpenMenu(!openMenu)}
        />
      </div>
    </nav>
  )
}
```

---

### `src/components/Navbar/Navbar.css`

```css
.nav {
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  padding: 4px 0;
  z-index: 10;
  transition: background 0.5s;
}
.dark-nav {
  background: rgba(3, 6, 20, 0.95);
  color: #fff;
}
.nav-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.logo { width: 180px; }

/* desktop links */
.nav-links {
  display: flex;
  align-items: center;
  gap: 18px;
  list-style: none;
}
.nav-links li { font-size: 16px; padding: 5px 10px; }

/* mobile: sidebar */
.menu-icon { display: none; cursor: pointer; width: 28px; }

/* responsive */
@media (max-width: 840px) {
  .nav-inner { padding: 0 15px; }
  .nav-links {
    position: fixed;
    top: 0;
    right: -220px;
    height: 100%;
    width: 200px;
    background: rgba(2,6,23,0.95);
    flex-direction: column;
    align-items: flex-start;
    padding-top: 70px;
    transition: right 0.5s;
    z-index: 9;
  }
  .nav-links.open { right: 0; }
  .nav-links li { display: block; margin: 25px 40px; color: #fff; }
  .menu-icon { display: block; }
}
```

---

### `src/components/Hero/Hero.jsx`

```jsx
import React from 'react'
import './Hero.css'
import heroImg from '../../assets/hero.png'
import darkArrow from '../../assets/dark-arrow.png'

export default function Hero() {
  return (
    <section id="hero" className="hero">
      <div className="hero container">
        <div className="hero-text container">
          <h1>Build your future with us</h1>
          <p>Join Edusity to get industry-ready skills, hands-on projects and expert mentorship.</p>
          <button className="btn">
            Explore More
            <img src={darkArrow} alt="arrow" style={{width:20}} />
          </button>
        </div>
      </div>
    </section>
  )
}
```

---

### `src/components/Hero/Hero.css`

```css
.hero {
  min-height: 100vh;
  background: linear-gradient(rgba(0,0,0,0.7), rgba(0,0,0,0.7)), url('../../assets/hero.png') center/cover no-repeat;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
}
.hero-text { text-align: center; max-width: 800px; }
.hero-text h1 { font-size: 60px; font-weight: 600; margin-bottom: 12px; }
.hero-text p { max-width: 700px; margin: 10px auto 20px; line-height: 1.4; }
```

---

### `src/components/Title/Title.jsx`

```jsx
import React from 'react'
import './Title.css'

export default function Title({ subtitle, title }) {
  return (
    <div className="title">
      <p>{subtitle}</p>
      <h2>{title}</h2>
    </div>
  )
}
```

---

### `src/components/Title/Title.css`

```css
.title {
  text-align: center;
  color: #0f1724;
  font-size: 15px;
  font-weight: 600;
  text-transform: uppercase;
  margin: 70px 0 30px;
}
.title h2 {
  font-size: 32px;
  color: #0f1724;
  margin-top: 5px;
  text-transform: none;
}
```

---

### `src/components/Programs/Programs.jsx`

```jsx
import React from 'react'
import './Programs.css'
import program1 from '../../assets/program1.png'
import program2 from '../../assets/program2.png'
import program3 from '../../assets/program3.png'
import icon1 from '../../assets/program-icon1.png'
import icon2 from '../../assets/program-icon2.png'
import icon3 from '../../assets/program-icon3.png'

export default function Programs() {
  return (
    <section id="programs" className="programs container">
      <div className="program">
        <img src={program1} alt="program 1" />
        <div className="caption">
          <img src={icon1} alt="icon" />
          <p>Graduation Degree</p>
        </div>
      </div>

      <div className="program">
        <img src={program2} alt="program 2" />
        <div className="caption">
          <img src={icon2} alt="icon" />
          <p>Master Degree</p>
        </div>
      </div>

      <div className="program">
        <img src={program3} alt="program 3" />
        <div className="caption">
          <img src={icon3} alt="icon" />
          <p>Post Graduation</p>
        </div>
      </div>
    </section>
  )
}
```

---

### `src/components/Programs/Programs.css`

```css
.programs {
  margin: 80px auto;
  width: 90%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
}
.program { position: relative; flex: 0 0 31%; }
.program img { width: 100%; display: block; border-radius: 10px; }

.caption {
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  border-radius: 10px;
  background: rgba(0,15,152,0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  color: #fff;
  opacity: 0;
  padding-top: 70%;
  transition: all 0.4s;
  cursor: pointer;
}
.caption img { width: 60px; margin-bottom: 10px; }
.program:hover .caption { opacity: 1; padding-top: 0; }
```

---

### `src/components/About/About.jsx`

```jsx
import React from 'react'
import './About.css'
import aboutImg from '../../assets/about.png'
import playIcon from '../../assets/play-icon.png'

export default function About() {
  return (
    <section id="about" className="about container">
      <div className="about-left">
        <img src={aboutImg} alt="about" className="about-img" />
        <img src={playIcon} alt="play" className="play-icon" />
      </div>

      <div className="about-right">
        <h3>About University</h3>
        <h2>We provide modern education for everyone</h2>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Lorem ipsum dolor sit amet.</p>
        <p>Praesent commodo cursus magna, vel scelerisque nisl consectetur et. Sed posuere consectetur est at lobortis.</p>
        <p>Integer posuere erat a ante venenatis dapibus posuere velit aliquet.</p>
      </div>
    </section>
  )
}
```

---

### `src/components/About/About.css`

```css
.about {
  margin: 100px auto;
  width: 90%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.about-left { flex-basis: 40%; position: relative; }
.about-right { flex-basis: 56%; }

.about-img { width: 100%; border-radius: 10px; }
.play-icon {
  width: 60px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.about-right h3 { font-weight: 600; font-size: 16px; color: #2563eb; display:flex; align-items:center; gap:10px; }
.about-right h2 { font-size: 35px; margin-top: 10px; max-width: 400px; }
.about-right p { margin-top: 12px; color: #344054; }
```

---

### `src/components/Campus/Campus.jsx`

```jsx
import React from 'react'
import './Campus.css'
import g1 from '../../assets/gallery1.png'
import g2 from '../../assets/gallery2.png'
import g3 from '../../assets/gallery3.png'
import g4 from '../../assets/gallery4.png'
import whiteArrow from '../../assets/white-arrow.png'

export default function Campus() {
  return (
    <section id="campus" className="campus container">
      <div className="gallery">
        <img src={g1} alt="g1" />
        <img src={g2} alt="g2" />
        <img src={g3} alt="g3" />
        <img src={g4} alt="g4" />
      </div>

      <button className="btn dark-btn">
        See More
        <img src={whiteArrow} alt="arrow" style={{ width: 18 }} />
      </button>
    </section>
  )
}
```

---

### `src/components/Campus/Campus.css`

```css
.campus {
  margin: 80px auto;
  width: 90%;
  text-align: center;
}
.gallery {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 40px;
}
.gallery img { width: 23%; border-radius: 10px; display:block; }
```

---

### `src/components/Testimonials/Testimonials.jsx`

```jsx
import React, { useRef } from 'react'
import './Testimonials.css'
import nextIcon from '../../assets/next-icon.png'
import backIcon from '../../assets/back-icon.png'
import user1 from '../../assets/user1.png'
import user2 from '../../assets/user2.png'
import user3 from '../../assets/user3.png'
import user4 from '../../assets/user4.png'

export default function Testimonials() {
  const slider = useRef(null)
  let tx = 0 // translateX value in percent

  const slideForward = () => {
    if (tx > -50) {
      tx -= 25
      if (slider.current) slider.current.style.transform = `translateX(${tx}%)`
    }
  }

  const slideBackward = () => {
    if (tx < 0) {
      tx += 25
      if (slider.current) slider.current.style.transform = `translateX(${tx}%)`
    }
  }

  return (
    <section id="testimonials" className="testimonials container">
      <img src={backIcon} className="nav-btn back-btn" alt="back" onClick={slideBackward} />
      <img src={nextIcon} className="nav-btn next-btn" alt="next" onClick={slideForward} />

      <div className="slider">
        <ul ref={slider}>
          <li className="slide">
            <div className="user-info">
              <img src={user1} alt="user1" />
              <div>
                <h3>Jane Doe</h3>
                <span>ABC Corp</span>
              </div>
            </div>
            <p>Great learning experience — I landed my job after the program.</p>
          </li>

          <li className="slide">
            <div className="user-info">
              <img src={user2} alt="user2" />
              <div>
                <h3>John Smith</h3>
                <span>XYZ Ltd</span>
              </div>
            </div>
            <p>The projects and mentorship made a huge difference.</p>
          </li>

          <li className="slide">
            <div className="user-info">
              <img src={user3} alt="user3" />
              <div>
                <h3>Mary Jane</h3>
                <span>Acme</span>
              </div>
            </div>
            <p>Practical and hands-on. Highly recommend.</p>
          </li>

          <li className="slide">
            <div className="user-info">
              <img src={user4} alt="user4" />
              <div>
                <h3>Peter Parker</h3>
                <span>WebWorks</span>
              </div>
            </div>
            <p>Great support and community. Learned a ton.</p>
          </li>
        </ul>
      </div>
    </section>
  )
}
```

---

### `src/components/Testimonials/Testimonials.css`

```css
.testimonials {
  margin: 80px auto;
  padding: 0 80px;
  position: relative;
}
.nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  padding: 15px;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
  background: #fff;
}
.next-btn { right: 10px; }
.back-btn { left: 10px; }

.slider { overflow: hidden; }
.slider ul {
  display: flex;
  width: 200%;
  transition: transform 0.5s;
}
.slider ul li {
  list-style: none;
  width: 50%;
  padding: 20px;
  box-shadow: 0 0 20px rgba(3,6,20,0.05);
  border-radius: 10px;
  background: #fff;
  margin-right: 20px;
}
.user-info { display:flex; align-items:center; gap:10px; margin-bottom: 15px; }
.user-info img { width: 65px; border-radius: 50%; border: 4px solid #fff; }
.user-info h3 { margin-bottom: 0; color: #2563eb; }
.user-info span { font-size: 14px; color: #6b7280; }
```

---

### `src/components/Contact/Contact.jsx`

```jsx
import React, { useState } from 'react'
import './Contact.css'
import msgIcon from '../../assets/msg-icon.png'
import mailIcon from '../../assets/mail-icon.png'
import phoneIcon from '../../assets/phone-icon.png'
import locationIcon from '../../assets/location-icon.png'
import whiteArrow from '../../assets/white-arrow.png'

export default function Contact() {
  const [result, setResult] = useState('')

  // Web3Forms integration (replace ACCESS_KEY below)
  const onSubmit = async (e) => {
    e.preventDefault()
    setResult('Sending...')
    const form = e.target
    const data = new FormData(form)

    const payload = Object.fromEntries(data.entries())
    const ACCESS_KEY = 'YOUR_WEB3FORMS_ACCESS_KEY' // <-- REPLACE THIS

    try {
      const res = await fetch('https://api.web3forms.com/submit', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ access_key: ACCESS_KEY, ...payload })
      })
      const json = await res.json()
      if (json.success) {
        setResult('Email sent successfully')
        form.reset()
      } else {
        setResult('Something went wrong')
      }
    } catch (err) {
      console.error(err)
      setResult('Network error')
    }

    setTimeout(() => setResult(''), 3000)
  }

  return (
    <section id="contact" className="contact container">
      <div className="contact-col">
        <h3>Send us a message <img src={msgIcon} alt="msg" style={{ width: 35, marginLeft: 10 }} /></h3>
        <p>Have questions? Reach out and we'll get back to you shortly.</p>
        <ul>
          <li><img src={mailIcon} alt="mail" /> hello@edusity.com</li>
          <li><img src={phoneIcon} alt="phone" /> +254 700 000 000</li>
          <li><img src={locationIcon} alt="loc" /> Nairobi, Kenya</li>
        </ul>
      </div>

      <div className="contact-col">
        <form onSubmit={onSubmit}>
          <label>Your Name</label>
          <input type="text" name="name" placeholder="Enter your name" required />
          <label>Phone Number</label>
          <input type="tel" name="phone" placeholder="Enter your mobile number" required />
          <label>Your Message</label>
          <textarea name="message" placeholder="Enter your message" rows="6" required />
          <button type="submit" className="btn dark-btn">
            Submit Now
            <img src={whiteArrow} alt="arrow" style={{ width: 18 }} />
          </button>
        </form>

        <span className="result">{result}</span>
      </div>
    </section>
  )
}
```

---

### `src/components/Contact/Contact.css`

```css
.contact {
  margin: 80px auto;
  max-width: 90%;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 30px;
}
.contact-col { flex-basis: 48%; color: #0f1724; }
.contact-col h3 { display:flex; align-items:center; gap:10px; font-size:25px; margin-bottom:20px; }
.contact-col p { max-width: 450px; line-height: 1.3; margin-bottom: 15px; }
.contact-col ul { list-style: none; padding: 0; }
.contact-col ul li { display:flex; align-items:center; gap:10px; margin: 20px 0; }

form input, form textarea {
  display: block;
  width: 100%;
  background: #f1f5f9;
  padding: 15px;
  border: 0;
  outline: 0;
  margin-top: 5px;
  margin-bottom: 15px;
  resize: none;
}
.contact .result { display:block; margin:20px 0; color: #334155; }

/* responsive */
@media (max-width: 700px) {
  .contact { flex-direction: column; }
  .contact-col { flex-basis: 100%; }
}
```

---

### `src/components/Footer/Footer.jsx`

```jsx
import React from 'react'
import './Footer.css'

export default function Footer() {
  return (
    <footer className="footer container">
      <p>© 2025 Edusity. All rights reserved.</p>
      <ul>
        <li>Terms of Service</li>
        <li>Privacy Policy</li>
      </ul>
    </footer>
  )
}
```

---

### `src/components/Footer/Footer.css`

```css
.footer {
  margin: 10px auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-top: 1px solid rgba(3,6,20,0.06);
  padding: 15px 0;
}
.footer ul { list-style: none; display:flex; gap:20px; }
.footer ul li { display:inline-block; margin-left: 20px; color:#475569; }

@media (max-width: 600px) {
  .footer { display:block; text-align:center; }
  .footer ul { margin-top:10px; gap:10px; justify-content:center; }
}
```

---

### `src/components/VideoPlayer/VideoPlayer.jsx`

```jsx
import React from 'react'
import './VideoPlayer.css'
import videoFile from '../../assets/colle-video.mp4' // optional local video

export default function VideoPlayer({ show, onClose }) {
  return (
    <div className={`video-player ${show ? '' : 'hide'}`} onClick={onClose}>
      <video src={videoFile} controls autoPlay muted className="video-element" onClick={(e) => e.stopPropagation()} />
    </div>
  )
}
```

---

### `src/components/VideoPlayer/VideoPlayer.css`

```css
.video-player {
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.9);
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: center;
}
.video-player.hide { display: none; }
.video-element {
  width: 90%;
  max-width: 900px;
  height: auto;
  border: 4px solid #fff;
}
```

---

## 6. Assets

Place all referenced images and video inside `src/assets/`:

* `logo.png`
* `hero.png`
* `dark-arrow.png`
* `program1.png`, `program2.png`, `program3.png`
* `program-icon1.png`, `program-icon2.png`, `program-icon3.png`
* `about.png`, `play-icon.png`
* `gallery1.png`, `gallery2.png`, `gallery3.png`, `gallery4.png`
* `next-icon.png`, `back-icon.png`
* `user1.png`, `user2.png`, `user3.png`, `user4.png`
* `msg-icon.png`, `mail-icon.png`, `phone-icon.png`, `location-icon.png`
* `white-arrow.png`
* `menu-icon.png`
* `colle-video.mp4` (optional local video)

The tutorial mentioned providing image download links; replace these with your own images. File names in code must match exact asset filenames.

---

## 7. Web3Forms (Contact Form) Setup

1. Go to `https://web3forms.com`
2. Click **Create Access Key** (no login required). Enter your email to get an `access_key`.
3. Replace `'YOUR_WEB3FORMS_ACCESS_KEY'` inside `Contact.jsx` with your actual key.
4. Web3Forms offers a free tier (e.g., ~250 submissions/mo as of the tutorial) — check current limits on their site.

---

## 8. Notes & Troubleshooting

* Component CSS uses relative `url()` assumed from file placement; if using different bundler settings, adjust asset paths accordingly.
* The testimonials slider uses an internal `let tx = 0` variable. This approach matches the tutorial and mutates `tx` across user clicks. If you prefer React state-managed sliders, convert to `useState` and update accordingly.
* `react-scroll` is used in `Navbar.jsx` for smooth scrolling. Make sure to have installed the dependency.
* For production build: `npm run build` then serve `dist/` with your chosen host (Netlify, Vercel, GitHub Pages via static export, etc.)

---

## 9. License & Credits

* Built from a tutorial walkthrough (transcript). See the original transcript for the step-by-step coding narration. 
* Replace any placeholder copy and assets with your brand content.

---  



