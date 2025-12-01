
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

# Mar Ariyo - Using React and Tailwind
# Edusity — Tailwind Version (Option B: Tailwind + small reusable classes — *Clean Modern*)

Below is the **complete Tailwind-based project** you asked for.
It includes:

* Full project setup (Vite + React + Tailwind)
* `package.json`, `vite.config.js`, `postcss.config.js`, `tailwind.config.js`
* `src` files: `main.jsx`, `index.css` (with small reusable classes), `App.jsx`
* Every component rewritten using **Tailwind utility classes** plus a few small reusable classes (`.container`, `.btn`, `.btn-primary`, `.section-title`) placed in `index.css`
* Minimal custom CSS only where Tailwind can't express the dynamic behavior (a few tiny helpers)
* Testimonials slider implemented using React state & inline style transform (clean and React-friendly)
* Contact form integration with Web3Forms (placeholder `ACCESS_KEY` — **replace** before use)
* Notes on assets and deployment

> ⚠️ **Important**: Replace `YOUR_WEB3FORMS_ACCESS_KEY` with your real key. Put all images listed under `src/assets/` and make sure filenames match exactly.

---  

# 📁 **Complete Project Structure — Tailwind Version**

```
edusity-tailwind/
│
├── package.json
├── vite.config.js
├── postcss.config.js
├── tailwind.config.js
│
├── index.html
│
├── public/
│   └── (empty or favicon assets if you add them)
│
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   ├── index.css        <-- Tailwind + reusable classes (.container, .btn, etc.)
│
│   ├── assets/
│   │   ├── logo.png
│   │   ├── menu-icon.png
│   │   ├── hero.png
│   │   ├── dark-arrow.png
│   │
│   │   ├── program1.png
│   │   ├── program2.png
│   │   ├── program3.png
│   │   ├── program-icon1.png
│   │   ├── program-icon2.png
│   │   ├── program-icon3.png
│   │
│   │   ├── about.png
│   │   ├── play-icon.png
│   │
│   │   ├── gallery1.png
│   │   ├── gallery2.png
│   │   ├── gallery3.png
│   │   ├── gallery4.png
│   │
│   │   ├── next-icon.png
│   │   ├── back-icon.png
│   │
│   │   ├── user1.png
│   │   ├── user2.png
│   │   ├── user3.png
│   │   ├── user4.png
│   │
│   │   ├── msg-icon.png
│   │   ├── mail-icon.png
│   │   ├── phone-icon.png
│   │   ├── location-icon.png
│   │   ├── white-arrow.png
│   │
│   │   ├── colle-video.mp4     <-- optional if video player used
│   │
│   │   └── (any additional images)
│
│
│   ├── components/
│   │
│   │   ├── Navbar/
│   │   │   └── Navbar.jsx
│   │
│   │   ├── Hero/
│   │   │   └── Hero.jsx
│   │
│   │   ├── Title/
│   │   │   └── Title.jsx
│   │
│   │   ├── Programs/
│   │   │   └── Programs.jsx
│   │
│   │   ├── About/
│   │   │   └── About.jsx
│   │
│   │   ├── Campus/
│   │   │   └── Campus.jsx
│   │
│   │   ├── Testimonials/
│   │   │   └── Testimonials.jsx
│   │
│   │   ├── Contact/
│   │   │   └── Contact.jsx
│   │
│   │   ├── Footer/
│   │   │   └── Footer.jsx
│   │
│   │   └── VideoPlayer/
│   │       └── VideoPlayer.jsx
│
│
└── README.md   <-- you may generate updated README using Tailwind version
```

---


# 1 — Project files

## `package.json`

```json
{
  "name": "edusity-tailwind",
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
    "vite": "^4.0.0",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0",
    "tailwindcss": "^3.4.0"
  }
}
```

## `vite.config.js`

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()]
})
```

## `postcss.config.js`

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
}
```

## `tailwind.config.js`

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {
      colors: {
        brand: {
          50: '#eef2ff',
          100: '#e0e7ff',
          500: '#2563eb' /* primary blue used in design */
        }
      }
    }
  },
  plugins: []
}
```

---

# 2 — Public HTML

## `public/index.html`

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Edusity — Build your future</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

---

# 3 — src entry files

## `src/main.jsx`

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

## `src/index.css`

This file contains Tailwind directives plus a few small reusable classes (`.container`, `.btn`, `.btn-primary`, `.section-title`) to keep markup concise and readable.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Google font import (Outfit - clean modern) */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');

:root {
  --brand: #2563eb;
  --text: #0f1724;
  --muted: #6b7280;
}

/* Base */
html, body, #root {
  height: 100%;
}
body {
  font-family: 'Outfit', system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  background: #f8fafc;
  color: var(--text);
}

/* Small reusable helpers (Option B approach) */
.container {
  @apply px-10 mx-auto;
  /* keep content constrained when needed (override with width utilities) */
}

.btn {
  @apply inline-flex items-center gap-3 rounded-full px-5 py-3 text-base font-medium shadow-sm;
}

.btn-primary {
  @apply bg-brand-500 text-white hover:bg-blue-700;
}

.section-title {
  @apply text-center uppercase text-sm font-semibold tracking-widest text-sky-600 mb-3;
}

/* Minimal custom utilities for slider/video modal */
.slider-ul {
  transition: transform 0.5s ease;
}
.video-overlay {
  background: rgba(3,6,20,0.9);
}

/* small responsive tweaks */
@media (max-width: 768px) {
  .container { @apply px-6; }
}
```

---

# 4 — App root

## `src/App.jsx`

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
  const [showVideo, setShowVideo] = useState(false)

  return (
    <>
      <Navbar />
      <main className="mt-20"> {/* give space for fixed navbar */}
        <Hero onPlay={() => setShowVideo(true)} />
        <section className="pt-12">
          <Title subtitle="Our Programs" title="What We Offer" />
          <Programs />
        </section>

        <section className="pt-12">
          <About />
        </section>

        <section className="pt-12">
          <Title subtitle="Gallery" title="Campus Photos" />
          <Campus />
        </section>

        <section className="pt-12">
          <Title subtitle="Testimonials" title="What a Student Says" />
          <Testimonials />
        </section>

        <section className="pt-12">
          <Title subtitle="Contact Us" title="Get in Touch" />
          <Contact />
        </section>

        <Footer />
      </main>

      <VideoPlayer show={showVideo} onClose={() => setShowVideo(false)} />
    </>
  )
}
```

---

# 5 — Components (Tailwind + a few reusable classes)

> For each component folder: create `Component.jsx`. No separate `.css` files except `VideoPlayer` and minimal JS-managed slider.

---

## `src/components/Navbar/Navbar.jsx`

```jsx
import React, { useEffect, useState } from 'react'
import { Link } from 'react-scroll'
import logo from '../../assets/logo.png'
import menuIcon from '../../assets/menu-icon.png'

export default function Navbar() {
  const [sticky, setSticky] = useState(false)
  const [open, setOpen] = useState(false)

  useEffect(() => {
    const handle = () => setSticky(window.scrollY > 50)
    window.addEventListener('scroll', handle)
    return () => window.removeEventListener('scroll', handle)
  }, [])

  return (
    <header className={`fixed top-0 left-0 w-full z-30 transition-colors ${sticky ? 'bg-white shadow' : 'bg-transparent'}`}>
      <div className="container flex items-center justify-between py-4">
        <div className="flex items-center gap-4">
          <img src={logo} alt="Edusity Logo" className="w-40" />
        </div>

        {/* desktop nav */}
        <nav className="hidden md:flex items-center gap-6">
          <Link to="hero" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">Home</Link>
          <Link to="programs" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">Programs</Link>
          <Link to="about" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">About</Link>
          <Link to="campus" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">Campus</Link>
          <Link to="testimonials" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">Testimonials</Link>
          <Link to="contact" spy smooth offset={-80} duration={500} className="text-sm text-gray-700 hover:text-brand-500 cursor-pointer">Contact</Link>

          <button className="btn btn-primary ml-3">
            Contact Us
          </button>
        </nav>

        {/* mobile menu button */}
        <div className="md:hidden flex items-center">
          <button onClick={() => setOpen(!open)} aria-label="Toggle menu">
            <img src={menuIcon} alt="menu" className="w-7 h-7" />
          </button>
        </div>
      </div>

      {/* mobile menu */}
      <div className={`md:hidden bg-white/95 shadow-lg ${open ? 'block' : 'hidden'}`}>
        <div className="p-6 flex flex-col gap-4">
          <Link to="hero" onClick={() => setOpen(false)} className="text-base text-gray-700">Home</Link>
          <Link to="programs" onClick={() => setOpen(false)} className="text-base text-gray-700">Programs</Link>
          <Link to="about" onClick={() => setOpen(false)} className="text-base text-gray-700">About</Link>
          <Link to="campus" onClick={() => setOpen(false)} className="text-base text-gray-700">Campus</Link>
          <Link to="testimonials" onClick={() => setOpen(false)} className="text-base text-gray-700">Testimonials</Link>
          <Link to="contact" onClick={() => setOpen(false)} className="text-base text-gray-700">Contact</Link>
          <button className="btn btn-primary mt-2">Contact Us</button>
        </div>
      </div>
    </header>
  )
}
```

---

## `src/components/Hero/Hero.jsx`

```jsx
import React from 'react'
import heroImg from '../../assets/hero.png'
import darkArrow from '../../assets/dark-arrow.png'

export default function Hero({ onPlay }) {
  return (
    <section id="hero" className="relative">
      <div
        className="h-[80vh] flex items-center"
        style={{
          backgroundImage: `linear-gradient(rgba(2,6,23,0.6), rgba(2,6,23,0.6)), url(${heroImg})`,
          backgroundSize: 'cover',
          backgroundPosition: 'center'
        }}
      >
        <div className="container text-center">
          <h1 className="text-white text-5xl font-semibold leading-tight mb-4">Build your future with us</h1>
          <p className="text-slate-200 max-w-2xl mx-auto mb-6">Join Edusity to get industry-ready skills, hands-on projects and expert mentorship.</p>

          <div className="flex items-center justify-center gap-4">
            <button className="btn btn-primary">
              Explore More
              <img src={darkArrow} alt="arrow" className="w-4" />
            </button>

            <button
              onClick={onPlay}
              className="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-white/10 text-white hover:bg-white/20"
            >
              <svg className="w-5 h-5" viewBox="0 0 24 24" fill="currentColor"><path d="M5 3v18l15-9L5 3z"/></svg>
              Watch Video
            </button>
          </div>
        </div>
      </div>
    </section>
  )
}
```

---

## `src/components/Title/Title.jsx`

```jsx
import React from 'react'

export default function Title({ subtitle, title }) {
  return (
    <div className="py-6">
      <p className="section-title">{subtitle}</p>
      <h2 className="text-3xl md:text-4xl font-semibold text-center text-gray-900">{title}</h2>
    </div>
  )
}
```

---

## `src/components/Programs/Programs.jsx`

```jsx
import React from 'react'
import p1 from '../../assets/program1.png'
import p2 from '../../assets/program2.png'
import p3 from '../../assets/program3.png'
import i1 from '../../assets/program-icon1.png'
import i2 from '../../assets/program-icon2.png'
import i3 from '../../assets/program-icon3.png'

const ProgramCard = ({ img, icon, label }) => (
  <div className="relative group rounded-xl overflow-hidden shadow-sm">
    <img src={img} alt={label} className="w-full h-64 object-cover" />
    <div className="absolute inset-0 bg-sky-900/40 flex flex-col items-center justify-center gap-3 transform translate-y-10 opacity-0 group-hover:translate-y-0 group-hover:opacity-100 transition-all">
      <img src={icon} alt="icon" className="w-14" />
      <p className="text-white font-medium">{label}</p>
    </div>
  </div>
)

export default function Programs() {
  return (
    <section id="programs" className="container grid grid-cols-1 md:grid-cols-3 gap-6">
      <ProgramCard img={p1} icon={i1} label="Graduation Degree" />
      <ProgramCard img={p2} icon={i2} label="Master Degree" />
      <ProgramCard img={p3} icon={i3} label="Post Graduation" />
    </section>
  )
}
```

---

## `src/components/About/About.jsx`

```jsx
import React from 'react'
import aboutImg from '../../assets/about.png'
import playIcon from '../../assets/play-icon.png'

export default function About() {
  return (
    <section id="about" className="container grid md:grid-cols-2 gap-8 items-center py-6">
      <div className="relative">
        <img src={aboutImg} alt="About" className="w-full rounded-xl shadow-md" />
        <button className="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-full p-3 shadow" aria-label="Play video">
          <img src={playIcon} alt="play" className="w-8" />
        </button>
      </div>

      <div>
        <h3 className="text-brand-500 font-semibold mb-2">About University</h3>
        <h2 className="text-2xl md:text-3xl font-semibold mb-4">We provide modern education for everyone</h2>
        <p className="text-slate-700 mb-3">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Praesent commodo cursus magna.</p>
        <p className="text-slate-700 mb-3">Praesent commodo cursus magna, vel scelerisque nisl consectetur et. Sed posuere consectetur est at lobortis.</p>
        <p className="text-slate-700">Integer posuere erat a ante venenatis dapibus posuere velit aliquet.</p>
      </div>
    </section>
  )
}
```

---

## `src/components/Campus/Campus.jsx`

```jsx
import React from 'react'
import g1 from '../../assets/gallery1.png'
import g2 from '../../assets/gallery2.png'
import g3 from '../../assets/gallery3.png'
import g4 from '../../assets/gallery4.png'
import whiteArrow from '../../assets/white-arrow.png'

export default function Campus() {
  return (
    <section id="campus" className="container py-6">
      <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mb-6">
        <img src={g1} alt="g1" className="w-full rounded-lg object-cover h-40" />
        <img src={g2} alt="g2" className="w-full rounded-lg object-cover h-40" />
        <img src={g3} alt="g3" className="w-full rounded-lg object-cover h-40" />
        <img src={g4} alt="g4" className="w-full rounded-lg object-cover h-40" />
      </div>

      <div className="flex justify-center">
        <button className="btn btn-primary">
          See More
          <img src={whiteArrow} className="w-4" alt="arrow" />
        </button>
      </div>
    </section>
  )
}
```

---

## `src/components/Testimonials/Testimonials.jsx`

```jsx
import React, { useState } from 'react'
import nextIcon from '../../assets/next-icon.png'
import backIcon from '../../assets/back-icon.png'
import u1 from '../../assets/user1.png'
import u2 from '../../assets/user2.png'
import u3 from '../../assets/user3.png'
import u4 from '../../assets/user4.png'

const slides = [
  { img: u1, name: 'Jane Doe', title: 'ABC Corp', text: 'Great learning experience — I landed my job after the program.' },
  { img: u2, name: 'John Smith', title: 'XYZ Ltd', text: 'The projects and mentorship made a huge difference.' },
  { img: u3, name: 'Mary Jane', title: 'Acme', text: 'Practical and hands-on. Highly recommend.' },
  { img: u4, name: 'Peter Parker', title: 'WebWorks', text: 'Great support and community. Learned a ton.' }
]

export default function Testimonials() {
  const [tx, setTx] = useState(0) // percent translateX
  // we display two slides at a time: translate step = -25% per move (50% width visible)
  const slideForward = () => setTx(prev => Math.max(prev - 25, -50))
  const slideBackward = () => setTx(prev => Math.min(prev + 25, 0))

  return (
    <section id="testimonials" className="container py-6 relative">
      <div className="absolute -left-2 top-1/2 transform -translate-y-1/2">
        <button onClick={slideBackward} className="bg-white p-3 rounded-full shadow">
          <img src={backIcon} alt="back" className="w-5" />
        </button>
      </div>

      <div className="absolute -right-2 top-1/2 transform -translate-y-1/2">
        <button onClick={slideForward} className="bg-white p-3 rounded-full shadow">
          <img src={nextIcon} alt="next" className="w-5" />
        </button>
      </div>

      <div className="overflow-hidden">
        <ul
          className="flex gap-6 slider-ul"
          style={{ width: '200%', transform: `translateX(${tx}%)` }}
        >
          {slides.map((s, i) => (
            <li key={i} className="w-1/2 bg-white rounded-lg p-6 shadow">
              <div className="flex items-center gap-4 mb-4">
                <img src={s.img} alt={s.name} className="w-16 h-16 rounded-full object-cover" />
                <div>
                  <h3 className="text-brand-500 font-semibold">{s.name}</h3>
                  <span className="text-sm text-slate-500">{s.title}</span>
                </div>
              </div>
              <p className="text-slate-700">{s.text}</p>
            </li>
          ))}
        </ul>
      </div>
    </section>
  )
}
```

---

## `src/components/Contact/Contact.jsx`

```jsx
import React, { useState } from 'react'
import msgIcon from '../../assets/msg-icon.png'
import mailIcon from '../../assets/mail-icon.png'
import phoneIcon from '../../assets/phone-icon.png'
import locationIcon from '../../assets/location-icon.png'
import whiteArrow from '../../assets/white-arrow.png'

export default function Contact() {
  const [result, setResult] = useState('')

  const onSubmit = async (e) => {
    e.preventDefault()
    setResult('Sending...')
    const form = e.target
    const formData = new FormData(form)
    const payload = Object.fromEntries(formData.entries())

    const ACCESS_KEY = 'YOUR_WEB3FORMS_ACCESS_KEY' // <--- REPLACE

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
    <section id="contact" className="container grid md:grid-cols-2 gap-8 py-6">
      <div>
        <h3 className="flex items-center gap-3 text-2xl font-semibold mb-3">
          Send us a message
          <img src={msgIcon} alt="msg" className="w-7" />
        </h3>

        <p className="text-slate-700 mb-4">Have questions? Reach out and we'll get back to you shortly.</p>

        <ul className="space-y-4">
          <li className="flex items-center gap-3">
            <img src={mailIcon} alt="mail" className="w-6" />
            <span>hello@edusity.com</span>
          </li>
          <li className="flex items-center gap-3">
            <img src={phoneIcon} alt="phone" className="w-6" />
            <span>+254 700 000 000</span>
          </li>
          <li className="flex items-center gap-3">
            <img src={locationIcon} alt="loc" className="w-6" />
            <span>Nairobi, Kenya</span>
          </li>
        </ul>
      </div>

      <div>
        <form onSubmit={onSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium mb-1">Your Name</label>
            <input name="name" required className="w-full rounded-lg bg-slate-100 p-3 outline-none" placeholder="Enter your name" />
          </div>

          <div>
            <label className="block text-sm font-medium mb-1">Phone Number</label>
            <input name="phone" type="tel" required className="w-full rounded-lg bg-slate-100 p-3 outline-none" placeholder="Enter your mobile number" />
          </div>

          <div>
            <label className="block text-sm font-medium mb-1">Your Message</label>
            <textarea name="message" rows={6} required className="w-full rounded-lg bg-slate-100 p-3 outline-none" placeholder="Enter your message" />
          </div>

          <div>
            <button type="submit" className="btn btn-primary">
              Submit Now
              <img src={whiteArrow} alt="arrow" className="w-4" />
            </button>
          </div>
        </form>

        <div className="mt-4 text-sm text-slate-600">{result}</div>
      </div>
    </section>
  )
}
```

---

## `src/components/Footer/Footer.jsx`

```jsx
import React from 'react'

export default function Footer() {
  return (
    <footer className="bg-white border-t mt-12">
      <div className="container flex flex-col md:flex-row items-center justify-between py-6 text-sm text-slate-600">
        <p>© {new Date().getFullYear()} Edusity. All rights reserved.</p>
        <ul className="flex gap-6 mt-4 md:mt-0">
          <li className="cursor-pointer hover:text-brand-500">Terms of Service</li>
          <li className="cursor-pointer hover:text-brand-500">Privacy Policy</li>
        </ul>
      </div>
    </footer>
  )
}
```

---

## `src/components/VideoPlayer/VideoPlayer.jsx`

```jsx
import React from 'react'
import videoFile from '../../assets/colle-video.mp4'

export default function VideoPlayer({ show, onClose }) {
  if (!show) return null

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center video-overlay" onClick={onClose}>
      <div className="w-11/12 md:w-3/4 lg:w-2/3">
        <video src={videoFile} controls autoPlay className="w-full rounded-lg" onClick={(e) => e.stopPropagation()} />
      </div>
    </div>
  )
}
```

---

# 6 — Assets

Place the following files under `src/assets/` (names must match code):

* `logo.png`
* `menu-icon.png`
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
* `colle-video.mp4` (optional)

---

# 7 — Install & Run (Tailwind setup)

1. Install dependencies:

```bash
npm install
```

2. Initialize Tailwind (already included in `postcss.config.js` and `tailwind.config.js`). If you need to re-init:

```bash
npx tailwindcss init -p
```

3. Start dev server:

```bash
npm run dev
```

4. Open printed URL (Vite default like `http://localhost:5173`).

---

# 8 — Notes & small guidance

* **Web3Forms**: Go to [https://web3forms.com](https://web3forms.com), create access key and replace `YOUR_WEB3FORMS_ACCESS_KEY` in `Contact.jsx`. Test with your email.
* **Slider**: Implemented as a simple two-at-a-time slider using `translateX` controlled by state. This is deterministic and easily extendable — if you prefer a full-featured slider use a library (e.g., Swiper).
* **Tailwind classes**: We used a few small reusable classes to keep markup readable while still leveraging Tailwind utility classes for the bulk of styling.
* **Images & sizes**: If your images contain different aspect ratios, tweak `object-cover`, `h-40`, `h-64` sizes in components.
* **SEO & metadata**: Add meta tags and favicon in `public/index.html` as needed.
* **Deployment**: `npm run build` produces `dist/`. Deploy to Vercel / Netlify / any static host.

---  
## Mar Adek  
**Delivery Plan**
- Chunk 1 — README Header + Overview + Install Steps + Project Structure
- Chunk 2 — Config Files (package.json, vite.config.js, postcss.config.js, tailwind.config.js)
- Chunk 3 — Core Files (index.html, main.jsx, index.css, App.jsx)
- Chunk 4 — Components Part 1 (Navbar, Hero, Title, Programs)
- Chunk 5 — Components Part 2 (About, Campus, Testimonials)
- Chunk 6 — Components Part 3 (Contact, Footer, VideoPlayer)
- Chunk 7 — Final Notes + End of README





