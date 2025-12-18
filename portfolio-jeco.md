
Great choice—this layout is clean, professional, and perfect for a first (or even mid-level) portfolio 👍

I’ve **generated the first segment: `index.html`**, closely inspired by the screenshot you shared and aligned with your project structure (HTML / CSS / JS separated).

### What this segment covers

* Sidebar navigation (like the image)
* Hero section with name highlight and CTA buttons
* Section placeholders: About, Experience, Projects, Skills, Education, Contact
* Clean semantic HTML
* Ready for responsiveness and JS enhancements

### Suggested project structure (final)

```
/portfolio
│
├── index.html
│
├── css/
│   └── style.css
│
├── js/
│   └── main.js
│
├── images/
│   └── profile.jpg
│
├── resume/
│   └── Varad_Bhogayata_Resume.pdf
```
**index.html**  

```html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Varad Bhogayata | Portfolio</title>
  <link rel="stylesheet" href="css/style.css" />
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
</head>
<body>
  <aside class="sidebar">
    <div class="profile">
      <img src="images/profile.jpg" alt="Profile" />
      <h2>Varad</h2>
    </div>
    <nav>
      <a href="#about">About</a>
      <a href="#experience">Experience</a>
      <a href="#projects">Projects</a>
      <a href="#skills">Skills</a>
      <a href="#education">Education</a>
      <a href="#contact">Contact</a>
      <a href="resume/Varad_Bhogayata_Resume.pdf" target="_blank">Resume</a>
    </nav>
  </aside>

  <main class="content">
    <section class="hero" id="about">
      <h1>Hi, I'm <span>Varad Bhogayata.</span></h1>
      <h3>A Python Developer</h3>
      <p>
        Self-driven, quick starter, passionate programmer with a curious mind who enjoys
        solving complex and challenging real-world problems.
      </p>
      <div class="socials">
        <a href="#">LinkedIn</a>
        <a href="#">GitHub</a>
      </div>
      <div class="cta">
        <a href="#projects" class="btn primary">Read More</a>
        <a href="#contact" class="btn outline">Contact Me</a>
      </div>
    </section>

    <section id="experience" class="section">
      <h2>Experience</h2>
      <p>Add your work experience here.</p>
    </section>

    <section id="projects" class="section">
      <h2>Projects</h2>
      <div class="projects-grid">
        <div class="card">Project One</div>
        <div class="card">Project Two</div>
        <div class="card">Project Three</div>
      </div>
    </section>

    <section id="skills" class="section">
      <h2>Skills</h2>
      <ul class="skills-list">
        <li>Python</li>
        <li>JavaScript</li>
        <li>HTML & CSS</li>
      </ul>
    </section>

    <section id="education" class="section">
      <h2>Education</h2>
      <p>Your education details here.</p>
    </section>

    <section id="contact" class="section">
      <h2>Contact</h2>
      <form>
        <input type="text" placeholder="Your Name" required />
        <input type="email" placeholder="Your Email" required />
        <textarea placeholder="Message"></textarea>
        <button type="submit" class="btn primary">Send</button>
      </form>
    </section>
  </main>

  <script src="js/main.js"></script>
</body>
</html>
```

### Next segments (I recommend this order)

1. **Segment 2:** `css/style.css`

   * Color palette matching the green theme
   * Sidebar layout
   * Responsive design (tablet & mobile)
2. **Segment 3:** `js/main.js`

   * Smooth scrolling
   * Active nav highlight
   * Mobile menu toggle
3. **Segment 4:**

   * Polished project cards
   * Skills section UI
   * Accessibility + performance tips

👉 **Tell me to continue with Segment 2 (CSS)** and I’ll build a fully responsive stylesheet that matches the UI in your screenshot exactly.

```css
/* ==============================
   Global Styles
================================ */
:root {
  --primary: #0f766e;
  --primary-light: #5ea5a0;
  --bg-main: #6fa39a;
  --bg-sidebar: #ffffff;
  --text-dark: #1f2933;
  --text-light: #6b7280;
  --white: #ffffff;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Poppins', sans-serif;
  display: flex;
  min-height: 100vh;
  color: var(--text-dark);
}

/* ==============================
   Sidebar
================================ */
.sidebar {
  width: 260px;
  background: var(--bg-sidebar);
  padding: 2rem 1.5rem;
  position: fixed;
  height: 100vh;
  border-right: 1px solid #e5e7eb;
}

.profile {
  text-align: center;
  margin-bottom: 2rem;
}

.profile img {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  object-fit: cover;
  margin-bottom: 0.75rem;
}

.profile h2 {
  font-size: 1.2rem;
  font-weight: 600;
}

.sidebar nav {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.sidebar nav a {
  text-decoration: none;
  color: var(--text-light);
  font-size: 0.95rem;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  transition: all 0.3s ease;
}

.sidebar nav a:hover {
  background: #f0fdfa;
  color: var(--primary);
}

/* ==============================
   Main Content
================================ */
.content {
  margin-left: 260px;
  width: calc(100% - 260px);
}

.hero {
  min-height: 100vh;
  background: var(--bg-main);
  padding: 4rem 6rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.hero h1 {
  font-size: 2.8rem;
  font-weight: 600;
  color: var(--white);
  margin-bottom: 0.5rem;
}

.hero h1 span {
  background: var(--primary);
  padding: 0.2rem 0.6rem;
  border-radius: 6px;
}

.hero h3 {
  font-size: 1.4rem;
  font-weight: 400;
  color: #e5f6f4;
  margin-bottom: 1.5rem;
}

.hero p {
  max-width: 600px;
  font-size: 1rem;
  line-height: 1.8;
  color: #ecfeff;
  margin-bottom: 2rem;
}

/* ==============================
   Buttons & Socials
================================ */
.socials {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}

.socials a {
  background: var(--white);
  color: var(--primary);
  padding: 0.5rem 0.75rem;
  border-radius: 50px;
  font-size: 0.85rem;
  text-decoration: none;
}

.cta {
  display: flex;
  gap: 1rem;
}

.btn {
  padding: 0.75rem 1.6rem;
  border-radius: 8px;
  text-decoration: none;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.btn.primary {
  background: var(--white);
  color: var(--primary);
}

.btn.primary:hover {
  background: #ecfeff;
}

.btn.outline {
  border: 2px solid var(--white);
  color: var(--white);
}

.btn.outline:hover {
  background: rgba(255,255,255,0.15);
}

/* ==============================
   Sections
================================ */
.section {
  padding: 4rem 6rem;
}

.section h2 {
  font-size: 2rem;
  margin-bottom: 1.5rem;
  color: var(--primary);
}

/* ==============================
   Projects
================================ */
.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1.5rem;
}

.card {
  background: var(--white);
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.08);
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-6px);
}

/* ==============================
   Skills
================================ */
.skills-list {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.skills-list li {
  list-style: none;
  background: #f0fdfa;
  color: var(--primary);
  padding: 0.5rem 1rem;
  border-radius: 50px;
  font-size: 0.85rem;
}

/* ==============================
   Contact
================================ */
form {
  max-width: 500px;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

form input,
form textarea {
  padding: 0.75rem 1rem;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  font-family: inherit;
}

form textarea {
  min-height: 120px;
}

/* ==============================
   Responsive
================================ */
@media (max-width: 900px) {
  .sidebar {
    width: 200px;
  }

  .content {
    margin-left: 200px;
    width: calc(100% - 200px);
  }

  .hero,
  .section {
    padding: 3rem;
  }
}

@media (max-width: 700px) {
  body {
    flex-direction: column;
  }

  .sidebar {
    position: relative;
    width: 100%;
    height: auto;
    border-right: none;
    border-bottom: 1px solid #e5e7eb;
  }

  .content {
    margin-left: 0;
    width: 100%;
  }

  .hero h1 {
    font-size: 2.2rem;
  }
}
```

```js
/* ==============================
   Portfolio JavaScript
   Clean, lightweight, no libraries
================================ */

// Smooth scrolling for internal links
const navLinks = document.querySelectorAll('a[href^="#"]');

navLinks.forEach(link => {
  link.addEventListener('click', e => {
    e.preventDefault();
    const targetId = link.getAttribute('href');
    const targetSection = document.querySelector(targetId);

    if (targetSection) {
      targetSection.scrollIntoView({
        behavior: 'smooth',
        block: 'start'
      });
    }
  });
});

/* ==============================
   Active Nav Highlight on Scroll
================================ */
const sections = document.querySelectorAll('section');
const sidebarLinks = document.querySelectorAll('.sidebar nav a');

window.addEventListener('scroll', () => {
  let current = '';

  sections.forEach(section => {
    const sectionTop = section.offsetTop - 150;
    const sectionHeight = section.clientHeight;

    if (scrollY >= sectionTop && scrollY < sectionTop + sectionHeight) {
      current = section.getAttribute('id');
    }
  });

  sidebarLinks.forEach(link => {
    link.classList.remove('active');
    if (link.getAttribute('href') === `#${current}`) {
      link.classList.add('active');
    }
  });
});

/* ==============================
   Optional: Mobile Menu Toggle
   (Future-ready)
================================ */
const sidebar = document.querySelector('.sidebar');

function toggleSidebar() {
  sidebar.classList.toggle('open');
}

/* ==============================
   Contact Form (Basic UX only)
================================ */
const form = document.querySelector('form');

if (form) {
  form.addEventListener('submit', e => {
    e.preventDefault();
    alert('Thanks for reaching out! I will get back to you soon.');
    form.reset();
  });
}
```



