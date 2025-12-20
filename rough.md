
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
│   │   ├── Blog.jsx
│   │   ├── Contact.jsx
│   │   └── Footer.jsx
│   │
│   ├── data/
│   │   ├── projects.js
│   │   └── skills.js
│   │   ├── blog.js
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

### **main.jsx**

**1. `main.jsx`** – Vite entry point:

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './styles/global.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**2. `App.jsx`** – Root component:

```jsx
import React from 'react';
import Navbar from './components/Navbar';
import Hero from './components/Hero';
import About from './components/About';
import Skills from './components/Skills';
import Projects from './components/Projects';
import Blog from './components/Blog';
import Contact from './components/Contact';
import Footer from './components/Footer';

function App() {
  return (
    <>
      <Navbar />
      <main>
        <Hero />
        <About />
        <Skills />
        <Projects />
        <Blog />
        <Contact />
      </main>
      <Footer />
    </>
  );
}

export default App;
```

**3. `index.html`** – Keep in `public/index.html`:

```html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Outa Agunga | Data Analyst & Developer</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.jsx"></script>
</body>
</html>
```

---

### **Segment 2: Navbar Component (`Navbar.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import '../styles/components.css';

const Navbar = () => {
  const [open, setOpen] = useState(false);

  const links = [
    { label: 'Home', href: '#home' },
    { label: 'About', href: '#about' },
    { label: 'Skills', href: '#skills' },
    { label: 'Projects', href: '#projects' },
    { label: 'Blog', href: '#blog' },
    { label: 'Contact', href: '#contact' }
  ];

  return (
    <header className="navbar">
      <div className="nav-container">
        <a href="#home" className="nav-logo">
          Outa<span>.</span>
        </a>

        <nav className={`nav-links ${open ? 'open' : ''}`}>
          {links.map((link) => (
            <a
              key={link.href}
              href={link.href}
              onClick={() => setOpen(false)}
            >
              {link.label}
            </a>
          ))}
        </nav>

        <button
          className={`nav-toggle ${open ? 'open' : ''}`}
          onClick={() => setOpen(!open)}
          aria-label="Toggle navigation"
        >
          <span />
          <span />
          <span />
        </button>
      </div>
    </header>
  );
};

export default Navbar;

```

---

### **1. Hero Component (`Hero.jsx`)**

```jsx
import React, { useEffect, useRef } from 'react';
import '../styles/components.css';

const stats = [
  { number: '50+', label: 'Projects Completed' },
  { number: '3+', label: 'Years Experience' },
  { number: '95%', label: 'Client Satisfaction' },
];

const Hero = () => {
  const heroRef = useRef(null);

  // Parallax effect on scroll
  useEffect(() => {
    const handleScroll = () => {
      const scrolled = window.pageYOffset;
      if (heroRef.current && scrolled < window.innerHeight) {
        heroRef.current.style.transform = `translateY(${scrolled * 0.3}px)`;
        heroRef.current.style.opacity = 1 - scrolled / 600;
      }
    };
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  return (
    <section className="hero" id="home">
      <div className="hero-bg"></div>
      <div className="hero-content" ref={heroRef}>
        <span className="hero-tag">Available for opportunities</span>
        <h1>
          Transforming Data Into<br />
          <span className="gradient-text">Actionable Insights</span>
        </h1>
        <p className="hero-subtitle">
          Data Analyst | Visualization Expert | Full Stack Developer | Content Writer — 
          Crafting compelling narratives from complex data and building scalable digital solutions.
        </p>
        <div className="hero-cta">
          <a href="#projects" className="btn btn-primary">View My Work</a>
          <a href="#contact" className="btn btn-outline">Get In Touch</a>
        </div>
        <div className="hero-stats">
          {stats.map((stat, index) => (
            <div className="stat" key={index}>
              <div className="stat-number">{stat.number}</div>
              <div className="stat-label">{stat.label}</div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
};

export default Hero;
```

---

### **2. About Component (`About.jsx`)**

dynamic text by storing them in an array of paragraphs. This allows future data-driven updates.

```jsx

import React, { useState, useEffect } from 'react';
import '../styles/components.css';

const aboutText = [
  "I'm Outa Agunga, a self-driven technologist with a passion for turning raw data into meaningful stories and building digital experiences that matter.",
  'My journey began with a simple question: "What can data tell us?" This curiosity evolved into expertise across data analytics, visualization, full-stack development, and content creation. I thrive at the intersection of technology and creativity.',
  'Whether I’m uncovering insights from complex datasets, designing interactive dashboards, architecting scalable web applications, or crafting compelling content, my goal remains the same: solve real problems with innovative solutions.',
  "When I'm not coding or analyzing data, you'll find me exploring new technologies, writing technical articles, or contributing to open-source projects. I believe in continuous learning and sharing knowledge with the community."
];

const About = () => {
  return (
    <section id="about" className="fade-in">
      <div className="section-header">
        <span className="section-tag">About Me</span>
        <h2 className="section-title">My Journey</h2>
        <p className="section-subtitle">From curiosity to mastery — the story behind the craft</p>
      </div>
      <div className="about-content">
        <div className="about-text">
          {aboutText.map((paragraph, index) => (
            <p key={index}>
              {paragraph.includes('Outa Agunga') ? <span className="about-highlight">{paragraph}</span> : paragraph}
            </p>
          ))}
        </div>
        <div className="about-image">
          {/* You can add <img src="/images/profile/yourphoto.jpg" alt="Profile" /> here */}
        </div>
      </div>
    </section>
  );
};

export default About;
```  
---

### **1. `skills.js` (data file)**

```javascript
export const skills = [
  {
    icon: '📊',
    title: 'Data Analytics',
    description: 'Expert in extracting insights from complex datasets using statistical analysis and machine learning techniques.',
    tech: ['Python', 'Pandas', 'NumPy', 'Scikit-learn']
  },
  {
    icon: '📈',
    title: 'Data Visualization',
    description: 'Creating compelling visual narratives that make complex data accessible and actionable for stakeholders.',
    tech: ['Tableau', 'Power BI', 'D3.js', 'Plotly']
  },
  {
    icon: '💻',
    title: 'Full Stack Development',
    description: 'Building scalable web applications from database to deployment with modern frameworks and best practices.',
    tech: ['React', 'Node.js', 'Django', 'PostgreSQL']
  },
  {
    icon: '✍️',
    title: 'Content Writing',
    description: 'Translating technical concepts into engaging narratives that resonate with diverse audiences.',
    tech: ['Technical Writing', 'SEO', 'Documentation', 'Storytelling']
  },
  {
    icon: '🗄️',
    title: 'Database Management',
    description: 'Designing and optimizing database architectures for performance and scalability.',
    tech: ['SQL', 'MongoDB', 'Redis', 'ETL']
  },
  {
    icon: '🎨',
    title: 'UI/UX Design',
    description: 'Creating intuitive user experiences with attention to accessibility and modern design principles.',
    tech: ['Figma', 'Responsive Design', 'CSS3', 'Tailwind']
  }
];
```

---

### **2. Skills Component (`Skills.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import { skills } from '../data/skills';
import '../styles/components.css';

const Skills = () => {
  return (
    <section id="skills" className="fade-in">
      <div className="section-header">
        <span className="section-tag">Expertise</span>
        <h2 className="section-title">Skills & Technologies</h2>
        <p className="section-subtitle">A comprehensive toolkit for modern digital solutions</p>
      </div>

      <div className="skills-grid">
        {skills.map((skill, index) => (
          <div className="skill-card" key={index}>
            <div className="skill-icon">{skill.icon}</div>
            <h3>{skill.title}</h3>
            <p>{skill.description}</p>
            <div className="tech-tags">
              {skill.tech.map((techItem, idx) => (
                <span className="tech-tag" key={idx}>{techItem}</span>
              ))}
            </div>
          </div>
        ))}
      </div>
    </section>
  );
};

export default Skills;
```

---

### **3. `projects.js` (data file)**

```javascript
export const projects = [
  {
    id: 1,
    number: '01',
    title: 'Customer Analytics Dashboard',
    description: 'Interactive dashboard analyzing customer behavior patterns with real-time data visualization. Implemented predictive models to forecast churn and optimize retention strategies.',
    tech: ['Python', 'Tableau', 'SQL', 'Machine Learning'],
    link: '#',
    size: 'large'
  },
  {
    id: 2,
    number: '02',
    title: 'E-Commerce Platform',
    description: 'Full-stack e-commerce solution with payment integration, inventory management, and admin dashboard.',
    tech: ['React', 'Node.js', 'MongoDB'],
    link: '#',
    size: 'medium'
  },
  {
    id: 3,
    number: '03',
    title: 'Sales Forecasting Model',
    description: 'Time series analysis and forecasting using ARIMA models for retail sales prediction.',
    tech: ['Python', 'Statsmodels'],
    link: '#',
    size: 'small'
  },
  {
    id: 4,
    number: '04',
    title: 'Social Media Analytics',
    description: 'Sentiment analysis tool for tracking brand perception across social platforms.',
    tech: ['NLP', 'API Integration'],
    link: '#',
    size: 'small'
  },
  {
    id: 5,
    number: '05',
    title: 'Portfolio Website Generator',
    description: 'SaaS platform for creating professional portfolio websites with customizable templates.',
    tech: ['Next.js', 'Prisma'],
    link: '#',
    size: 'small'
  }
];
```

---

### **4. Projects Component (`Projects.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import { projects } from '../data/projects';
import '../styles/components.css';

const Projects = () => {
  return (
    <section id="projects" className="fade-in">
      <div className="section-header">
        <span className="section-tag">Portfolio</span>
        <h2 className="section-title">Featured Projects</h2>
        <p className="section-subtitle">A showcase of my best work across different domains</p>
      </div>

      <div className="projects-bento">
        {projects.map((project) => (
          <div className={`project-card ${project.size}`} key={project.id}>
            <div className="project-header">
              <div className="project-number">{project.number}</div>
              <h3 className="project-title">{project.title}</h3>
              <p className="project-desc">{project.description}</p>
              <div className="tech-tags">
                {project.tech.map((techItem, idx) => (
                  <span className="tech-tag" key={idx}>{techItem}</span>
                ))}
              </div>
            </div>
            <div className="project-footer">
              <a href={project.link} className="project-link">View Project →</a>
            </div>
          </div>
        ))}
      </div>
    </section>
  );
};

export default Projects;
```  
---

### **1. `blog.js` (data file)**

```javascript
export const blogPosts = [
  {
    id: 1,
    date: 'December 15, 2024',
    title: 'The Future of Data Visualization in 2025',
    excerpt: 'Exploring emerging trends in data visualization and how AI is transforming the way we interact with data...',
    link: '#'
  },
  {
    id: 2,
    date: 'December 8, 2024',
    title: 'Building Scalable APIs with Node.js',
    excerpt: 'A comprehensive guide to designing and implementing RESTful APIs that can handle millions of requests...',
    link: '#'
  },
  {
    id: 3,
    date: 'November 30, 2024',
    title: 'Machine Learning for Business Analytics',
    excerpt: 'How to leverage machine learning algorithms to extract actionable insights from business data...',
    link: '#'
  }
];
```  
---

### **2. Blog Component (`Blog.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import { blogPosts } from '../data/blog';
import '../styles/components.css';

const Blog = () => {
  return (
    <section id="blog" className="fade-in">
      <div className="section-header">
        <span className="section-tag">Writing</span>
        <h2 className="section-title">Latest Articles</h2>
        <p className="section-subtitle">Insights, tutorials, and thoughts on technology</p>
      </div>

      <div className="blog-grid">
        {blogPosts.map((post) => (
          <div className="blog-card" key={post.id}>
            <div className="blog-image"></div>
            <div className="blog-content">
              <div className="blog-date">{post.date}</div>
              <h3 className="blog-title">{post.title}</h3>
              <p className="blog-excerpt">{post.excerpt}</p>
              <a href={post.link} className="blog-link">Read More →</a>
            </div>
          </div>
        ))}
      </div>
    </section>
  );
};

export default Blog;
```

---

### **3. Contact Component (`Contact.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import '../styles/components.css';

const Contact = () => {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    subject: '',
    message: ''
  });

  const [notification, setNotification] = useState(null);

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const isValidEmail = (email) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);

  const handleSubmit = (e) => {
    e.preventDefault();

    const { name, email, subject, message } = formData;

    if (!name || !email || !subject || !message) {
      showNotification('Please fill in all fields', 'error');
      return;
    }

    if (!isValidEmail(email)) {
      showNotification('Please enter a valid email address', 'error');
      return;
    }

    // Simulate form submission
    showNotification('Message sent successfully! I\'ll get back to you soon.', 'success');
    setFormData({ name: '', email: '', subject: '', message: '' });
  };

  const showNotification = (message, type) => {
    setNotification({ message, type });
    setTimeout(() => setNotification(null), 3000);
  };

  return (
    <section id="contact" className="fade-in">
      <div className="section-header">
        <span className="section-tag">Get In Touch</span>
        <h2 className="section-title">Let's Work Together</h2>
        <p className="section-subtitle">Have a project in mind? Let's discuss how I can help</p>
      </div>

      <div className="contact-container">
        <div className="contact-info">
          <h3>Let's create something amazing together</h3>
          <p>
            Whether you need data insights, a web application, or compelling content, 
            I'm here to help bring your vision to life. Drop me a message and let's start a conversation.
          </p>
          <div className="contact-methods">
            <div className="contact-method">
              <div className="contact-icon">📧</div>
              <a href="mailto:outa@example.com">outa@example.com</a>
            </div>
            <div className="contact-method">
              <div className="contact-icon">💼</div>
              <a href="http://www.linkedin.com/in/outa-agunga-1a5630354" target="_blank" rel="noopener noreferrer">LinkedIn Profile</a>
            </div>
            <div className="contact-method">
              <div className="contact-icon">💻</div>
              <a href="https://github.com/outaagunga" target="_blank" rel="noopener noreferrer">GitHub Portfolio</a>
            </div>
          </div>
        </div>

        <form className="contact-form" onSubmit={handleSubmit}>
          <div className="form-group">
            <label htmlFor="name">Your Name</label>
            <input type="text" id="name" name="name" value={formData.name} onChange={handleChange} required />
          </div>
          <div className="form-group">
            <label htmlFor="email">Your Email</label>
            <input type="email" id="email" name="email" value={formData.email} onChange={handleChange} required />
          </div>
          <div className="form-group">
            <label htmlFor="subject">Subject</label>
            <input type="text" id="subject" name="subject" value={formData.subject} onChange={handleChange} required />
          </div>
          <div className="form-group">
            <label htmlFor="message">Message</label>
            <textarea id="message" name="message" value={formData.message} onChange={handleChange} required></textarea>
          </div>
          <button type="submit" className="btn btn-primary">Send Message</button>
        </form>
      </div>

      {notification && (
        <div
          style={{
            position: 'fixed',
            top: '100px',
            right: '20px',
            padding: '1rem 1.5rem',
            background: notification.type === 'success' ? '#00d9ff' : '#f5576c',
            color: '#0a0a0a',
            borderRadius: '12px',
            fontWeight: 600,
            zIndex: 10000,
            transition: 'all 0.3s ease'
          }}
        >
          {notification.message}
        </div>
      )}
    </section>
  );
};

export default Contact;
```

---

### **4. Footer Component (`Footer.jsx`)**

```jsx
import React, { useState, useEffect } from 'react';
import '../styles/components.css';

const Footer = () => {
  return (
    <footer>
      <div className="footer-content">
        <div className="footer-socials">
          <a href="http://www.linkedin.com/in/outa-agunga-1a5630354" target="_blank" rel="noopener noreferrer" className="social-link">in</a>
          <a href="https://github.com/outaagunga" target="_blank" rel="noopener noreferrer" className="social-link">gh</a>
          <a href="#" className="social-link">tw</a>
          <a href="#" className="social-link">yt</a>
        </div>
        <p className="footer-text">
          © 2024 Outa Agunga. Crafted with passion and code.
        </p>
      </div>
    </footer>
  );
};

export default Footer;
```  
---

## ✅ `src/styles/global.css`

This file handles **reset, variables, typography, layout, buttons, and animations**.

```css
/* CSS RESET */
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
  background: #0a0a0a;
  color: #eaeaea;
  line-height: 1.6;
  overflow-x: hidden;
}

/* ROOT VARIABLES */
:root {
  --primary: #00d9ff;
  --secondary: #7f5cff;
  --bg-dark: #0a0a0a;
  --bg-card: #141414;
  --text-muted: #a0a0a0;
  --border: rgba(255, 255, 255, 0.08);
  --radius: 16px;
}

/* GLOBAL ELEMENTS */
section {
  padding: 6rem 8%;
  position: relative;
}

a {
  color: inherit;
  text-decoration: none;
}

img {
  max-width: 100%;
  display: block;
}

/* BUTTONS */
.btn {
  padding: 0.9rem 1.6rem;
  border-radius: 999px;
  font-weight: 600;
  transition: all 0.3s ease;
  display: inline-block;
}

.btn-primary {
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  color: #0a0a0a;
}

.btn-primary:hover {
  transform: translateY(-2px);
  opacity: 0.9;
}

.btn-outline {
  border: 1px solid var(--border);
}

.btn-outline:hover {
  border-color: var(--primary);
  color: var(--primary);
}

/* SECTION HEADERS */
.section-header {
  text-align: center;
  margin-bottom: 4rem;
}

.section-tag {
  color: var(--primary);
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  font-size: 0.85rem;
}

.section-title {
  font-size: clamp(2rem, 4vw, 3rem);
  margin: 0.6rem 0;
}

.section-subtitle {
  color: var(--text-muted);
  max-width: 600px;
  margin: auto;
}

/* ANIMATIONS */
.fade-in {
  animation: fadeUp 0.8s ease both;
}

@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

---

## ✅ `src/styles/components.css`

This file styles **every component** we created.

```css

/* ================= NAVBAR ================= */
.navbar {
  position: sticky;
  top: 0;
  z-index: 9999;
  background: rgba(10, 10, 10, 0.75);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid var(--border);
}

.nav-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 8%;
}

.nav-logo {
  font-size: 1.4rem;
  font-weight: 700;
}

.nav-logo span {
  color: var(--primary);
}

.nav-links {
  display: flex;
  gap: 2rem;
}

.nav-links a {
  color: var(--text-muted);
  font-weight: 500;
  position: relative;
}

.nav-links a::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: -6px;
  width: 0;
  height: 2px;
  background: var(--primary);
  transition: width 0.3s ease;
}

.nav-links a:hover {
  color: #fff;
}

.nav-links a:hover::after {
  width: 100%;
}

/* Hamburger */
.nav-toggle {
  display: none;
  flex-direction: column;
  gap: 5px;
  background: none;
  border: none;
  cursor: pointer;
}

.nav-toggle span {
  width: 24px;
  height: 2px;
  background: #fff;
  transition: all 0.3s ease;
}

/* Mobile */
@media (max-width: 900px) {
  .nav-toggle {
    display: flex;
  }

  .nav-links {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background: #0a0a0a;
    flex-direction: column;
    align-items: center;
    gap: 1.5rem;
    padding: 2rem 0;
    display: none;
    border-bottom: 1px solid var(--border);
  }

  .nav-links.open {
    display: flex;
  }
}



/* ================= HERO ================= */
.hero {
  min-height: 100vh;
  display: flex;
  align-items: center;
  position: relative;
}

.hero-bg {
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at 30% 20%, #1f1f1f, transparent 60%);
  z-index: -1;
}

.hero-content {
  max-width: 900px;
}

.hero-tag {
  color: var(--primary);
  font-weight: 600;
}

.hero h1 {
  font-size: clamp(2.5rem, 6vw, 4rem);
  margin: 1rem 0;
}

.gradient-text {
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.hero-subtitle {
  color: var(--text-muted);
  margin-bottom: 2rem;
}

.hero-cta {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.hero-stats {
  display: flex;
  gap: 2rem;
  margin-top: 3rem;
}

.stat-number {
  font-size: 1.6rem;
  font-weight: 700;
}

.stat-label {
  color: var(--text-muted);
  font-size: 0.85rem;
}

/* ================= ABOUT ================= */
.about-content {
  display: grid;
  grid-template-columns: 1.2fr 0.8fr;
  gap: 4rem;
  align-items: center;
}

.about-text p {
  margin-bottom: 1.2rem;
}

.about-highlight {
  color: var(--primary);
  font-weight: 600;
}

/* ================= SKILLS ================= */
.skills-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 2rem;
}

.skill-card {
  background: var(--bg-card);
  padding: 2rem;
  border-radius: var(--radius);
  border: 1px solid var(--border);
  transition: transform 0.3s ease;
}

.skill-card:hover {
  transform: translateY(-6px);
}

.skill-icon {
  font-size: 2rem;
  margin-bottom: 1rem;
}

/* ================= PROJECTS ================= */
.projects-bento {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
}

.project-card {
  background: var(--bg-card);
  border-radius: var(--radius);
  padding: 2rem;
  border: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.project-card.large {
  grid-column: span 2;
}

.project-card.medium {
  grid-column: span 2;
}

.project-title {
  margin: 1rem 0;
}

.project-desc {
  color: var(--text-muted);
}

.project-link {
  color: var(--primary);
  font-weight: 600;
}

/* ================= BLOG ================= */
.blog-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
}

.blog-card {
  background: var(--bg-card);
  border-radius: var(--radius);
  overflow: hidden;
  border: 1px solid var(--border);
}

.blog-content {
  padding: 1.5rem;
}

.blog-date {
  font-size: 0.8rem;
  color: var(--text-muted);
}

/* ================= CONTACT ================= */
.contact-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
}

.contact-form input,
.contact-form textarea {
  width: 100%;
  padding: 0.8rem;
  border-radius: 10px;
  border: 1px solid var(--border);
  background: #0f0f0f;
  color: #fff;
}

.form-group {
  margin-bottom: 1.2rem;
}

/* ================= FOOTER ================= */
footer {
  padding: 3rem 8%;
  border-top: 1px solid var(--border);
  text-align: center;
}

.footer-socials {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 1rem;
}

.social-link {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 1px solid var(--border);
  display: grid;
  place-items: center;
}

.social-link:hover {
  border-color: var(--primary);
  color: var(--primary);
}

/* ================= RESPONSIVE ================= */
@media (max-width: 900px) {
  .about-content,
  .contact-container {
    grid-template-columns: 1fr;
  }

  .projects-bento {
    grid-template-columns: 1fr;
  }
}
```
---



