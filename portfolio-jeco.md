
## Workflow: Building & Hosting a Personal Portfolio Website

### 1. Buy a Domain Name

* Choose a domain registrar (e.g. **Namecheap**, **Truehost**, or any other trusted provider)
* Search for your desired domain name (e.g. `yourname.dev`, `yourname.com`)
* **Tip:**

  * Search on Google or YouTube for **promo/discount codes** before checkout to save money
* Create an account and fill in your personal details on the domain platform
* Complete payment

  * In Kenya, you can use **M-Pesa GlobalPay** (if supported by the provider)
* Download and keep the **payment/transaction receipt** for future reference

---

### 2. Build Your Portfolio Website

* Create your portfolio using:

  * **HTML** (structure)
  * **CSS** (styling & responsiveness)
  * **JavaScript** (interactivity)
* Organize your project properly (e.g. `/css`, `/js`, `/images`)
* Test responsiveness on different screen sizes
* Initialize Git and push the project to **GitHub**

  ```bash
  git init
  git add .
  git commit -m "Initial portfolio commit"
  git branch -M main
  git remote add origin <your-repo-url>
  git push -u origin main
  ```

---

## Hosting Option 1: GitHub Pages (Free)

### 3. Deploy Using GitHub Pages

* Open your portfolio repository on GitHub
* Go to **Settings → Pages**
* Under **Source**:

  * Select **Deploy from a branch**
  * Choose the **main** branch
  * Select **/root**
* Click **Save**
* GitHub will generate a live URL like:

  ```
  https://username.github.io/repository-name
  ```

### 4. Enable HTTPS

* In **Settings → Pages**
* Ensure **“Enforce HTTPS”** is checked (auto-enabled once DNS is correct)

---

### 5. Connect a Custom Domain (e.g. Namecheap)

> Only do this if you purchased a domain

#### A. Add Custom Domain on GitHub

* In **Settings → Pages**
* Enter your domain (e.g. `www.yourdomain.com`)
* Save — GitHub will create a **CNAME** file automatically

#### B. Configure DNS on Namecheap

* Log in to **Namecheap**
* Go to **Domain List → Manage → Advanced DNS**
* Add the following DNS records (these records you can obtain from github help docs):

**A Records (for root domain)**

| Type | Host | Value (IP Address) |
| ---- | ---- | ------------------ |
| A    | @    | 185.199.108.153    |
| A    | @    | 185.199.109.153    |
| A    | @    | 185.199.110.153    |
| A    | @    | 185.199.111.153    |

**CNAME Record (for www)**

| Type  | Host | Value                |
| ----- | ---- | -------------------- |
| CNAME | www  | `username.github.io` |

> **NB:**  
> * **CNAME target is NOT your domain name**
> * It must point to `username.github.io`

* Save changes and wait **5–30 minutes** (sometimes up to 24 hours)
* Return to GitHub Pages settings and confirm the domain is active with HTTPS enabled

---

## Hosting Option 2: Vercel (Recommended for Modern Projects)

### 6. Deploy Using Vercel

* Go to **vercel.com** and sign up (use GitHub for easiest setup)
* Click **New Project**
* Import your **portfolio GitHub repository**
* Configure:

  * Framework preset: **Other / Static**
  * Build command: *(leave empty)*
  * Output directory: *(leave empty)*
* Click **Deploy**
* Vercel will generate a live URL (e.g. `https://portfolio.vercel.app`)

---

### 7. Connect Custom Domain on Vercel

* Go to **Project Settings → Domains**
* Add your domain (e.g. `yourdomain.com`)
* Vercel will show required DNS records
* Log in to your domain provider (Namecheap / Truehost)
* Add the DNS records exactly as provided by Vercel
* Once verified, HTTPS is enabled automatically

---

## Final Notes & Best Practices

* GitHub Pages → Best for **simple static portfolios**
* Vercel → Better for **performance, auto-deployments, and scalability**
* Always:

  * Keep your GitHub repo clean
  * Use meaningful commit messages
  * Test on mobile and desktop
* Update your portfolio regularly as you grow 🚀

---

### Project structure  

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
---

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

---   
**styles.css** 

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
   UI Polish & Animations
================================ */

/* Active nav link */
.sidebar nav a.active {
  background: #f0fdfa;
  color: var(--primary);
  font-weight: 500;
}

/* Smooth transitions globally */
a, button, .card {
  transition: all 0.3s ease;
}

/* Hero entrance animation */
.hero h1,
.hero h3,
.hero p,
.hero .socials,
.hero .cta {
  opacity: 0;
  animation: fadeUp 0.8s ease forwards;
}

.hero h3 { animation-delay: 0.1s; }
.hero p { animation-delay: 0.2s; }
.hero .socials { animation-delay: 0.3s; }
.hero .cta { animation-delay: 0.4s; }

@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Section reveal on scroll */
.section {
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.6s ease;
}

.section.visible {
  opacity: 1;
  transform: translateY(0);
}

/* Button micro-interactions */
.btn:hover {
  transform: translateY(-2px);
}

/* Project card hover polish */
.card:hover {
  box-shadow: 0 20px 40px rgba(0,0,0,0.12);
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
---  

**main.js**  
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
/* ==============================
   Reveal Sections on Scroll
================================ */
const revealSections = document.querySelectorAll('.section');

const revealOnScroll = () => {
  const triggerBottom = window.innerHeight * 0.85;

  revealSections.forEach(section => {
    const sectionTop = section.getBoundingClientRect().top;

    if (sectionTop < triggerBottom) {
      section.classList.add('visible');
    }
  });
};

window.addEventListener('scroll', revealOnScroll);
revealOnScroll();

```



