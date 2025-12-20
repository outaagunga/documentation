
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

---
---
---

# Version: 1 Static page using Html, CSS and Javascript  
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
│   └── Outa_Jeconia_Resume.pdf
```

---  

**index.html**
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

  <!-- Navigation -->
  <nav class="navbar">
    <div class="nav-container">
      <div class="logo">OA.</div>
      <ul class="nav-links">
        <li><a href="#home">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#skills">Skills</a></li>
        <li><a href="#projects">Projects</a></li>
        <li><a href="#blog">Blog</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
      <div class="hamburger">
        <span></span>
        <span></span>
        <span></span>
      </div>
    </div>
  </nav>

  <!-- Hero Section -->
  <section class="hero" id="home">
    <div class="hero-bg"></div>
    <div class="hero-content">
      <span class="hero-tag">Available for opportunities</span>
      <h1>
        Transforming Data Into<br />
        <span class="gradient-text">Actionable Insights</span>
      </h1>
      <p class="hero-subtitle">
        Data Analyst | Visualization Expert | Full Stack Developer | Content Writer — 
        Crafting compelling narratives from complex data and building scalable digital solutions.
      </p>
      <div class="hero-cta">
        <a href="#projects" class="btn btn-primary">View My Work</a>
        <a href="#contact" class="btn btn-outline">Get In Touch</a>
      </div>
      <div class="hero-stats">
        <div class="stat">
          <div class="stat-number">50+</div>
          <div class="stat-label">Projects Completed</div>
        </div>
        <div class="stat">
          <div class="stat-number">3+</div>
          <div class="stat-label">Years Experience</div>
        </div>
        <div class="stat">
          <div class="stat-number">95%</div>
          <div class="stat-label">Client Satisfaction</div>
        </div>
      </div>
    </div>
  </section>

  <!-- About Section -->
  <section id="about" class="fade-in">
    <div class="section-header">
      <span class="section-tag">About Me</span>
      <h2 class="section-title">My Journey</h2>
      <p class="section-subtitle">From curiosity to mastery — the story behind the craft</p>
    </div>
    <div class="about-content">
      <div class="about-text">
        <p>
          I'm <span class="about-highlight">Outa Agunga</span>, a self-driven technologist with a passion for turning raw data into meaningful stories and building digital experiences that matter.
        </p>
        <p>
          My journey began with a simple question: "What can data tell us?" This curiosity evolved into expertise across data analytics, visualization, full-stack development, and content creation. I thrive at the intersection of technology and creativity.
        </p>
        <p>
          Whether I'm uncovering insights from complex datasets, designing interactive dashboards, architecting scalable web applications, or crafting compelling content, my goal remains the same: <span class="about-highlight">solve real problems with innovative solutions</span>.
        </p>
        <p>
          When I'm not coding or analyzing data, you'll find me exploring new technologies, writing technical articles, or contributing to open-source projects. I believe in continuous learning and sharing knowledge with the community.
        </p>
      </div>
      <div class="about-image">
        <!-- Placeholder for profile image -->
      </div>
    </div>
  </section>

  <!-- Skills Section -->
  <section id="skills" class="fade-in">
    <div class="section-header">
      <span class="section-tag">Expertise</span>
      <h2 class="section-title">Skills & Technologies</h2>
      <p class="section-subtitle">A comprehensive toolkit for modern digital solutions</p>
    </div>
    <div class="skills-grid">
      <div class="skill-card">
        <div class="skill-icon">📊</div>
        <h3>Data Analytics</h3>
        <p>Expert in extracting insights from complex datasets using statistical analysis and machine learning techniques.</p>
        <div class="tech-tags">
          <span class="tech-tag">Python</span>
          <span class="tech-tag">Pandas</span>
          <span class="tech-tag">NumPy</span>
          <span class="tech-tag">Scikit-learn</span>
        </div>
      </div>
      <div class="skill-card">
        <div class="skill-icon">📈</div>
        <h3>Data Visualization</h3>
        <p>Creating compelling visual narratives that make complex data accessible and actionable for stakeholders.</p>
        <div class="tech-tags">
          <span class="tech-tag">Tableau</span>
          <span class="tech-tag">Power BI</span>
          <span class="tech-tag">D3.js</span>
          <span class="tech-tag">Plotly</span>
        </div>
      </div>
      <div class="skill-card">
        <div class="skill-icon">💻</div>
        <h3>Full Stack Development</h3>
        <p>Building scalable web applications from database to deployment with modern frameworks and best practices.</p>
        <div class="tech-tags">
          <span class="tech-tag">React</span>
          <span class="tech-tag">Node.js</span>
          <span class="tech-tag">Django</span>
          <span class="tech-tag">PostgreSQL</span>
        </div>
      </div>
              <div class="skill-card">
        <div class="skill-icon">✍️</div>
        <h3>Content Writing</h3>
        <p>Translating technical concepts into engaging narratives that resonate with diverse audiences.</p>
        <div class="tech-tags">
          <span class="tech-tag">Technical Writing</span>
          <span class="tech-tag">SEO</span>
          <span class="tech-tag">Documentation</span>
          <span class="tech-tag">Storytelling</span>
        </div>
      </div>
      <div class="skill-card">
        <div class="skill-icon">🗄️</div>
        <h3>Database Management</h3>
        <p>Designing and optimizing database architectures for performance and scalability.</p>
        <div class="tech-tags">
          <span class="tech-tag">SQL</span>
          <span class="tech-tag">MongoDB</span>
          <span class="tech-tag">Redis</span>
          <span class="tech-tag">ETL</span>
        </div>
      </div>
      <div class="skill-card">
        <div class="skill-icon">🎨</div>
        <h3>UI/UX Design</h3>
        <p>Creating intuitive user experiences with attention to accessibility and modern design principles.</p>
        <div class="tech-tags">
          <span class="tech-tag">Figma</span>
          <span class="tech-tag">Responsive Design</span>
          <span class="tech-tag">CSS3</span>
          <span class="tech-tag">Tailwind</span>
        </div>
      </div>
    </div>
  </section>

  <!-- Projects Section -->
  <section id="projects" class="fade-in">
    <div class="section-header">
      <span class="section-tag">Portfolio</span>
      <h2 class="section-title">Featured Projects</h2>
      <p class="section-subtitle">A showcase of my best work across different domains</p>
    </div>
    <div class="projects-bento">
      <div class="project-card large">
        <div class="project-header">
          <div class="project-number">01</div>
          <h3 class="project-title">Customer Analytics Dashboard</h3>
          <p class="project-desc">
            Interactive dashboard analyzing customer behavior patterns with real-time data visualization. 
            Implemented predictive models to forecast churn and optimize retention strategies.
          </p>
          <div class="tech-tags">
            <span class="tech-tag">Python</span>
            <span class="tech-tag">Tableau</span>
            <span class="tech-tag">SQL</span>
            <span class="tech-tag">Machine Learning</span>
          </div>
        </div>
        <div class="project-footer">
          <a href="#" class="project-link">View Project →</a>
        </div>
      </div>

      <div class="project-card medium">
        <div class="project-header">
          <div class="project-number">02</div>
          <h3 class="project-title">E-Commerce Platform</h3>
          <p class="project-desc">
            Full-stack e-commerce solution with payment integration, inventory management, and admin dashboard.
          </p>
          <div class="tech-tags">
            <span class="tech-tag">React</span>
            <span class="tech-tag">Node.js</span>
            <span class="tech-tag">MongoDB</span>
          </div>
        </div>
        <div class="project-footer">
          <a href="#" class="project-link">View Project →</a>
        </div>
      </div>

      <div class="project-card small">
        <div class="project-header">
          <div class="project-number">03</div>
          <h3 class="project-title">Sales Forecasting Model</h3>
          <p class="project-desc">
            Time series analysis and forecasting using ARIMA models for retail sales prediction.
          </p>
          <div class="tech-tags">
            <span class="tech-tag">Python</span>
            <span class="tech-tag">Statsmodels</span>
          </div>
        </div>
        <div class="project-footer">
          <a href="#" class="project-link">View Project →</a>
        </div>
      </div>

      <div class="project-card small">
        <div class="project-header">
          <div class="project-number">04</div>
          <h3 class="project-title">Social Media Analytics</h3>
          <p class="project-desc">
            Sentiment analysis tool for tracking brand perception across social platforms.
          </p>
          <div class="tech-tags">
            <span class="tech-tag">NLP</span>
            <span class="tech-tag">API Integration</span>
          </div>
        </div>
        <div class="project-footer">
          <a href="#" class="project-link">View Project →</a>
        </div>
      </div>

      <div class="project-card small">
        <div class="project-header">
          <div class="project-number">05</div>
          <h3 class="project-title">Portfolio Website Generator</h3>
          <p class="project-desc">
            SaaS platform for creating professional portfolio websites with customizable templates.
          </p>
          <div class="tech-tags">
            <span class="tech-tag">Next.js</span>
            <span class="tech-tag">Prisma</span>
          </div>
        </div>
        <div class="project-footer">
          <a href="#" class="project-link">View Project →</a>
        </div>
      </div>
    </div>
  </section>

  <!-- Blog Section -->
  <section id="blog" class="fade-in">
    <div class="section-header">
      <span class="section-tag">Writing</span>
      <h2 class="section-title">Latest Articles</h2>
      <p class="section-subtitle">Insights, tutorials, and thoughts on technology</p>
    </div>
    <div class="blog-grid">
      <div class="blog-card">
        <div class="blog-image"></div>
        <div class="blog-content">
          <div class="blog-date">December 15, 2024</div>
          <h3 class="blog-title">The Future of Data Visualization in 2025</h3>
          <p class="blog-excerpt">
            Exploring emerging trends in data visualization and how AI is transforming the way we interact with data...
          </p>
          <a href="#" class="blog-link">Read More →</a>
        </div>
      </div>

      <div class="blog-card">
        <div class="blog-image"></div>
        <div class="blog-content">
          <div class="blog-date">December 8, 2024</div>
          <h3 class="blog-title">Building Scalable APIs with Node.js</h3>
          <p class="blog-excerpt">
            A comprehensive guide to designing and implementing RESTful APIs that can handle millions of requests...
          </p>
          <a href="#" class="blog-link">Read More →</a>
        </div>
      </div>

      <div class="blog-card">
        <div class="blog-image"></div>
        <div class="blog-content">
          <div class="blog-date">November 30, 2024</div>
          <h3 class="blog-title">Machine Learning for Business Analytics</h3>
          <p class="blog-excerpt">
            How to leverage machine learning algorithms to extract actionable insights from business data...
          </p>
          <a href="#" class="blog-link">Read More →</a>
        </div>
      </div>
    </div>
  </section>

  <!-- Contact Section -->
  <section id="contact" class="fade-in">
    <div class="section-header">
      <span class="section-tag">Get In Touch</span>
      <h2 class="section-title">Let's Work Together</h2>
      <p class="section-subtitle">Have a project in mind? Let's discuss how I can help</p>
    </div>
    <div class="contact-container">
      <div class="contact-info">
        <h3>Let's create something amazing together</h3>
        <p>
          Whether you need data insights, a web application, or compelling content, 
          I'm here to help bring your vision to life. Drop me a message and let's start a conversation.
        </p>
        <div class="contact-methods">
          <div class="contact-method">
            <div class="contact-icon">📧</div>
            <a href="mailto:outa@example.com">outa@example.com</a>
          </div>
          <div class="contact-method">
            <div class="contact-icon">💼</div>
            <a href="http://www.linkedin.com/in/outa-agunga-1a5630354" target="_blank">LinkedIn Profile</a>
          </div>
          <div class="contact-method">
            <div class="contact-icon">💻</div>
            <a href="https://github.com/outaagunga" target="_blank">GitHub Portfolio</a>
          </div>
        </div>
      </div>
      <form class="contact-form" id="contactForm">
        <div class="form-group">
          <label for="name">Your Name</label>
          <input type="text" id="name" name="name" required />
        </div>
        <div class="form-group">
          <label for="email">Your Email</label>
          <input type="email" id="email" name="email" required />
        </div>
        <div class="form-group">
          <label for="subject">Subject</label>
          <input type="text" id="subject" name="subject" required />
        </div>
        <div class="form-group">
          <label for="message">Message</label>
          <textarea id="message" name="message" required></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Send Message</button>
      </form>
    </div>
  </section>

  <!-- Footer -->
  <footer>
    <div class="footer-content">
      <div class="footer-socials">
        <a href="http://www.linkedin.com/in/outa-agunga-1a5630354" target="_blank" class="social-link">in</a>
        <a href="https://github.com/outaagunga" target="_blank" class="social-link">gh</a>
        <a href="#" class="social-link">tw</a>
        <a href="#" class="social-link">yt</a>
      </div>
      <p class="footer-text">
        © 2024 Outa Agunga. Crafted with passion and code.
      </p>
    </div>
  </footer>
</body>
</html>
```
---

**style.css**
```css

    /* ==============================
       CSS Variables & Reset
    ================================ */
    :root {
      --bg-primary: #0a0a0a;
      --bg-secondary: #111111;
      --bg-card: #1a1a1a;
      --accent: #00d9ff;
      --accent-hover: #00b8d4;
      --text-primary: #ffffff;
      --text-secondary: #a0a0a0;
      --text-muted: #666666;
      --border: #2a2a2a;
      --gradient-1: linear-gradient(135deg, #00d9ff 0%, #7b2ff7 100%);
      --gradient-2: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
      --gradient-3: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    html {
      scroll-behavior: smooth;
    }

    body {
      font-family: 'Inter', sans-serif;
      background: var(--bg-primary);
      color: var(--text-primary);
      line-height: 1.6;
      overflow-x: hidden;
    }

    /* ==============================
       Navigation
    ================================ */
    .navbar {
      position: fixed;
      top: 0;
      width: 100%;
      background: rgba(10, 10, 10, 0.8);
      backdrop-filter: blur(20px);
      border-bottom: 1px solid var(--border);
      padding: 1.2rem 0;
      z-index: 1000;
      transition: all 0.3s ease;
    }

    .navbar.scrolled {
      padding: 0.8rem 0;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    }

    .nav-container {
      max-width: 1400px;
      margin: 0 auto;
      padding: 0 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .logo {
      font-family: 'Space Grotesk', sans-serif;
      font-size: 1.5rem;
      font-weight: 700;
      background: var(--gradient-1);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .nav-links {
      display: flex;
      gap: 2.5rem;
      list-style: none;
    }

    .nav-links a {
      color: var(--text-secondary);
      text-decoration: none;
      font-weight: 500;
      font-size: 0.95rem;
      transition: color 0.3s ease;
      position: relative;
    }

    .nav-links a::after {
      content: '';
      position: absolute;
      bottom: -5px;
      left: 0;
      width: 0;
      height: 2px;
      background: var(--accent);
      transition: width 0.3s ease;
    }

    .nav-links a:hover {
      color: var(--text-primary);
    }

    .nav-links a:hover::after {
      width: 100%;
    }

    .hamburger {
      display: none;
      flex-direction: column;
      gap: 5px;
      cursor: pointer;
      z-index: 1001;
    }

    .hamburger span {
      width: 25px;
      height: 2px;
      background: var(--text-primary);
      transition: all 0.3s ease;
    }

    .hamburger.active span:nth-child(1) {
      transform: rotate(45deg) translate(8px, 8px);
    }

    .hamburger.active span:nth-child(2) {
      opacity: 0;
    }

    .hamburger.active span:nth-child(3) {
      transform: rotate(-45deg) translate(7px, -7px);
    }

    /* ==============================
       Hero Section
    ================================ */
    .hero {
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      overflow: hidden;
      padding: 6rem 2rem 4rem;
    }

    .hero-bg {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: radial-gradient(circle at 50% 50%, rgba(0, 217, 255, 0.1) 0%, transparent 50%);
      pointer-events: none;
    }

    .hero-content {
      max-width: 1400px;
      width: 100%;
      z-index: 1;
    }

    .hero-tag {
      display: inline-block;
      padding: 0.5rem 1.2rem;
      background: rgba(0, 217, 255, 0.1);
      border: 1px solid rgba(0, 217, 255, 0.3);
      border-radius: 50px;
      color: var(--accent);
      font-size: 0.85rem;
      font-weight: 500;
      margin-bottom: 2rem;
      animation: fadeInUp 0.8s ease;
    }

    .hero h1 {
      font-family: 'Space Grotesk', sans-serif;
      font-size: clamp(2.5rem, 8vw, 5rem);
      font-weight: 800;
      line-height: 1.1;
      margin-bottom: 1.5rem;
      animation: fadeInUp 0.8s ease 0.2s backwards;
    }

    .hero h1 .gradient-text {
      background: var(--gradient-1);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .hero-subtitle {
      font-size: clamp(1.1rem, 2vw, 1.4rem);
      color: var(--text-secondary);
      margin-bottom: 2rem;
      max-width: 700px;
      animation: fadeInUp 0.8s ease 0.4s backwards;
    }

    .hero-cta {
      display: flex;
      gap: 1.5rem;
      margin-bottom: 3rem;
      animation: fadeInUp 0.8s ease 0.6s backwards;
    }

    .btn {
      padding: 1rem 2rem;
      border-radius: 12px;
      font-weight: 600;
      font-size: 1rem;
      text-decoration: none;
      transition: all 0.3s ease;
      cursor: pointer;
      border: none;
      display: inline-block;
    }

    .btn-primary {
      background: var(--gradient-1);
      color: var(--bg-primary);
      box-shadow: 0 10px 30px rgba(0, 217, 255, 0.3);
    }

    .btn-primary:hover {
      transform: translateY(-3px);
      box-shadow: 0 15px 40px rgba(0, 217, 255, 0.4);
    }

    .btn-outline {
      background: transparent;
      color: var(--text-primary);
      border: 2px solid var(--border);
    }

    .btn-outline:hover {
      border-color: var(--accent);
      background: rgba(0, 217, 255, 0.05);
      transform: translateY(-3px);
    }

    .hero-stats {
      display: flex;
      gap: 3rem;
      animation: fadeInUp 0.8s ease 0.8s backwards;
    }

    .stat {
      text-align: left;
    }

    .stat-number {
      font-size: 2.5rem;
      font-weight: 700;
      background: var(--gradient-1);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .stat-label {
      color: var(--text-muted);
      font-size: 0.9rem;
    }

    /* ==============================
       Section Styles
    ================================ */
    section {
      padding: 6rem 2rem;
      max-width: 1400px;
      margin: 0 auto;
    }

    .section-header {
      text-align: center;
      margin-bottom: 4rem;
    }

    .section-tag {
      display: inline-block;
      padding: 0.4rem 1rem;
      background: rgba(0, 217, 255, 0.1);
      border: 1px solid rgba(0, 217, 255, 0.3);
      border-radius: 50px;
      color: var(--accent);
      font-size: 0.8rem;
      font-weight: 500;
      margin-bottom: 1rem;
    }

    .section-title {
      font-family: 'Space Grotesk', sans-serif;
      font-size: clamp(2rem, 5vw, 3rem);
      font-weight: 700;
      margin-bottom: 1rem;
    }

    .section-subtitle {
      color: var(--text-secondary);
      font-size: 1.1rem;
      max-width: 600px;
      margin: 0 auto;
    }

    /* ==============================
       About Section
    ================================ */
    .about-content {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 4rem;
      align-items: center;
    }

    .about-text {
      font-size: 1.1rem;
      color: var(--text-secondary);
      line-height: 1.8;
    }

    .about-text p {
      margin-bottom: 1.5rem;
    }

    .about-highlight {
      color: var(--accent);
      font-weight: 600;
    }

    .about-image {
      position: relative;
      height: 500px;
      border-radius: 20px;
      overflow: hidden;
      background: var(--gradient-1);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .about-image::before {
      content: '';
      position: absolute;
      top: 20px;
      left: 20px;
      right: 20px;
      bottom: 20px;
      background: var(--bg-card);
      border-radius: 15px;
    }

    /* ==============================
       Skills Section
    ================================ */
    .skills-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
      gap: 2rem;
    }

    .skill-card {
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 20px;
      padding: 2rem;
      transition: all 0.3s ease;
      position: relative;
      overflow: hidden;
    }

    .skill-card::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 3px;
      background: var(--gradient-1);
      transform: scaleX(0);
      transition: transform 0.3s ease;
    }

    .skill-card:hover {
      transform: translateY(-10px);
      border-color: rgba(0, 217, 255, 0.5);
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
    }

    .skill-card:hover::before {
      transform: scaleX(1);
    }

    .skill-icon {
      width: 50px;
      height: 50px;
      background: var(--gradient-1);
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.5rem;
      margin-bottom: 1.5rem;
    }

    .skill-card h3 {
      font-size: 1.3rem;
      margin-bottom: 0.8rem;
      font-family: 'Space Grotesk', sans-serif;
    }

    .skill-card p {
      color: var(--text-secondary);
      font-size: 0.95rem;
      line-height: 1.6;
    }

    .tech-tags {
      display: flex;
      flex-wrap: wrap;
      gap: 0.5rem;
      margin-top: 1rem;
    }

    .tech-tag {
      padding: 0.3rem 0.8rem;
      background: rgba(0, 217, 255, 0.1);
      border: 1px solid rgba(0, 217, 255, 0.2);
      border-radius: 20px;
      font-size: 0.75rem;
      color: var(--accent);
    }

    /* ==============================
       Projects Bento Grid
    ================================ */
    .projects-bento {
      display: grid;
      grid-template-columns: repeat(12, 1fr);
      gap: 1.5rem;
      grid-auto-rows: 300px;
    }

    .project-card {
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 20px;
      padding: 2rem;
      transition: all 0.4s ease;
      position: relative;
      overflow: hidden;
      cursor: pointer;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    .project-card::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: var(--gradient-1);
      opacity: 0;
      transition: opacity 0.4s ease;
    }

    .project-card:hover {
      transform: translateY(-10px);
      box-shadow: 0 30px 60px rgba(0, 0, 0, 0.6);
    }

    .project-card:hover::before {
      opacity: 0.1;
    }

    .project-card.large {
      grid-column: span 8;
      grid-row: span 2;
    }

    .project-card.medium {
      grid-column: span 4;
      grid-row: span 2;
    }

    .project-card.small {
      grid-column: span 4;
    }

    .project-header {
      position: relative;
      z-index: 1;
    }

    .project-number {
      font-size: 0.8rem;
      color: var(--text-muted);
      margin-bottom: 0.5rem;
    }

    .project-title {
      font-family: 'Space Grotesk', sans-serif;
      font-size: 1.5rem;
      font-weight: 700;
      margin-bottom: 1rem;
    }

    .project-desc {
      color: var(--text-secondary);
      font-size: 0.95rem;
      line-height: 1.6;
      margin-bottom: 1.5rem;
    }

    .project-footer {
      position: relative;
      z-index: 1;
    }

    .project-link {
      display: inline-flex;
      align-items: center;
      gap: 0.5rem;
      color: var(--accent);
      font-weight: 600;
      font-size: 0.9rem;
      text-decoration: none;
      transition: gap 0.3s ease;
    }

    .project-link:hover {
      gap: 1rem;
    }

    /* ==============================
       Blog Section
    ================================ */
    .blog-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
      gap: 2rem;
    }

    .blog-card {
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 20px;
      overflow: hidden;
      transition: all 0.3s ease;
      cursor: pointer;
    }

    .blog-card:hover {
      transform: translateY(-10px);
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
    }

    .blog-image {
      width: 100%;
      height: 200px;
      background: var(--gradient-2);
      position: relative;
    }

    .blog-content {
      padding: 1.5rem;
    }

    .blog-date {
      color: var(--text-muted);
      font-size: 0.85rem;
      margin-bottom: 0.5rem;
    }

    .blog-title {
      font-size: 1.2rem;
      font-weight: 600;
      margin-bottom: 0.8rem;
      font-family: 'Space Grotesk', sans-serif;
    }

    .blog-excerpt {
      color: var(--text-secondary);
      font-size: 0.9rem;
      line-height: 1.6;
      margin-bottom: 1rem;
    }

    .blog-link {
      color: var(--accent);
      font-size: 0.9rem;
      font-weight: 600;
      text-decoration: none;
    }

    /* ==============================
       Contact Section
    ================================ */
    .contact-container {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 4rem;
    }

    .contact-info h3 {
      font-size: 1.8rem;
      margin-bottom: 1rem;
      font-family: 'Space Grotesk', sans-serif;
    }

    .contact-info p {
      color: var(--text-secondary);
      font-size: 1.05rem;
      margin-bottom: 2rem;
      line-height: 1.8;
    }

    .contact-methods {
      display: flex;
      flex-direction: column;
      gap: 1.5rem;
    }

    .contact-method {
      display: flex;
      align-items: center;
      gap: 1rem;
      padding: 1rem;
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 12px;
      transition: all 0.3s ease;
    }

    .contact-method:hover {
      border-color: var(--accent);
      transform: translateX(10px);
    }

    .contact-icon {
      width: 40px;
      height: 40px;
      background: var(--gradient-1);
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.2rem;
    }

    .contact-method a {
      color: var(--text-primary);
      text-decoration: none;
      font-weight: 500;
    }

    .contact-form {
      display: flex;
      flex-direction: column;
      gap: 1.5rem;
    }

    .form-group {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
    }

    .form-group label {
      font-weight: 500;
      color: var(--text-secondary);
      font-size: 0.9rem;
    }

    .form-group input,
    .form-group textarea {
      padding: 1rem;
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 12px;
      color: var(--text-primary);
      font-family: inherit;
      font-size: 1rem;
      transition: all 0.3s ease;
    }

    .form-group input:focus,
    .form-group textarea:focus {
      outline: none;
      border-color: var(--accent);
      box-shadow: 0 0 0 3px rgba(0, 217, 255, 0.1);
    }

    .form-group textarea {
      min-height: 150px;
      resize: vertical;
    }

    /* ==============================
       Footer
    ================================ */
    footer {
      background: var(--bg-secondary);
      border-top: 1px solid var(--border);
      padding: 3rem 2rem;
      text-align: center;
    }

    .footer-content {
      max-width: 1400px;
      margin: 0 auto;
    }

    .footer-socials {
      display: flex;
      justify-content: center;
      gap: 1.5rem;
      margin-bottom: 2rem;
    }

    .social-link {
      width: 45px;
      height: 45px;
      background: var(--bg-card);
      border: 1px solid var(--border);
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: var(--text-secondary);
      text-decoration: none;
      transition: all 0.3s ease;
    }

    .social-link:hover {
      background: var(--accent);
      color: var(--bg-primary);
      transform: translateY(-5px);
    }

    .footer-text {
      color: var(--text-muted);
      font-size: 0.9rem;
    }

    /* ==============================
       Animations
    ================================ */
    @keyframes fadeInUp {
      from {
        opacity: 0;
        transform: translateY(30px);
      }
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }

    .fade-in {
      opacity: 0;
      transform: translateY(30px);
      transition: all 0.8s ease;
    }

    .fade-in.visible {
      opacity: 1;
      transform: translateY(0);
    }

    /* ==============================
       Responsive Design
    ================================ */
    @media (max-width: 968px) {
      .nav-links {
        position: fixed;
        top: 0;
        right: -100%;
        width: 70%;
        height: 100vh;
        background: var(--bg-secondary);
        flex-direction: column;
        padding: 6rem 2rem;
        gap: 2rem;
        transition: right 0.4s ease;
        border-left: 1px solid var(--border);
      }

      .nav-links.active {
        right: 0;
      }

      .hamburger {
        display: flex;
      }

      .about-content,
      .contact-container {
        grid-template-columns: 1fr;
        gap: 3rem;
      }

      .projects-bento .project-card {
        grid-column: span 12 !important;
        grid-row: span 1 !important;
      }

      .hero-stats {
        flex-wrap: wrap;
        gap: 2rem;
      }

      .hero-cta {
        flex-direction: column;
      }
    }

    @media (max-width: 640px) {
      section {
        padding: 4rem 1.5rem;
      }

      .skills-grid,
      .blog-grid {
        grid-template-columns: 1fr;
      }

      .stat-number {
        font-size: 2rem;
      }
    }
```

---

**main.js**
```js

    /* ==============================
       Navigation Functionality
    ================================ */
    const navbar = document.querySelector('.navbar');
    const hamburger = document.querySelector('.hamburger');
    const navLinks = document.querySelector('.nav-links');
    const navItems = document.querySelectorAll('.nav-links a');

    // Navbar scroll effect
    window.addEventListener('scroll', () => {
      if (window.scrollY > 100) {
        navbar.classList.add('scrolled');
      } else {
        navbar.classList.remove('scrolled');
      }
    });

    // Mobile menu toggle
    hamburger.addEventListener('click', () => {
      hamburger.classList.toggle('active');
      navLinks.classList.toggle('active');
      document.body.style.overflow = navLinks.classList.contains('active') ? 'hidden' : 'auto';
    });

    // Close mobile menu when clicking nav links
    navItems.forEach(item => {
      item.addEventListener('click', () => {
        hamburger.classList.remove('active');
        navLinks.classList.remove('active');
        document.body.style.overflow = 'auto';
      });
    });

    /* ==============================
       Smooth Scrolling
    ================================ */
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
      anchor.addEventListener('click', function (e) {
        e.preventDefault();
        const target = document.querySelector(this.getAttribute('href'));
        if (target) {
          const offsetTop = target.offsetTop - 80;
          window.scrollTo({
            top: offsetTop,
            behavior: 'smooth'
          });
        }
      });
    });

    /* ==============================
       Scroll Reveal Animations
    ================================ */
    const observerOptions = {
      threshold: 0.1,
      rootMargin: '0px 0px -50px 0px'
    };

    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('visible');
        }
      });
    }, observerOptions);

    document.querySelectorAll('.fade-in').forEach(el => observer.observe(el));

    /* ==============================
       Form Validation & Submission
    ================================ */
    const contactForm = document.getElementById('contactForm');
    
    contactForm.addEventListener('submit', (e) => {
      e.preventDefault();
      
      const formData = new FormData(contactForm);
      const name = formData.get('name');
      const email = formData.get('email');
      const subject = formData.get('subject');
      const message = formData.get('message');

      // Basic validation
      if (!name || !email || !subject || !message) {
        showNotification('Please fill in all fields', 'error');
        return;
      }

      if (!isValidEmail(email)) {
        showNotification('Please enter a valid email address', 'error');
        return;
      }

      // Simulate form submission (replace with actual API call)
      showNotification('Message sent successfully! I\'ll get back to you soon.', 'success');
      contactForm.reset();
    });

    function isValidEmail(email) {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }

    function showNotification(message, type) {
      // Create notification element
      const notification = document.createElement('div');
      notification.textContent = message;
      notification.style.cssText = `
        position: fixed;
        top: 100px;
        right: 20px;
        padding: 1rem 1.5rem;
        background: ${type === 'success' ? '#00d9ff' : '#f5576c'};
        color: #0a0a0a;
        border-radius: 12px;
        font-weight: 600;
        z-index: 10000;
        animation: slideIn 0.3s ease;
        box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
      `;

      document.body.appendChild(notification);

      setTimeout(() => {
        notification.style.animation = 'slideOut 0.3s ease';
        setTimeout(() => notification.remove(), 300);
      }, 3000);
    }

    // Add notification animations
    const style = document.createElement('style');
    style.textContent = `
      @keyframes slideIn {
        from {
          transform: translateX(400px);
          opacity: 0;
        }
        to {
          transform: translateX(0);
          opacity: 1;
        }
      }
      @keyframes slideOut {
        from {
          transform: translateX(0);
          opacity: 1;
        }
        to {
          transform: translateX(400px);
          opacity: 0;
        }
      }
    `;
    document.head.appendChild(style);

    /* ==============================
       Active Navigation Highlight
    ================================ */
    const sections = document.querySelectorAll('section[id]');

    function highlightNavigation() {
      const scrollY = window.pageYOffset;

      sections.forEach(section => {
        const sectionHeight = section.offsetHeight;
        const sectionTop = section.offsetTop - 100;
        const sectionId = section.getAttribute('id');
        const navLink = document.querySelector(`.nav-links a[href="#${sectionId}"]`);

        if (scrollY > sectionTop && scrollY <= sectionTop + sectionHeight) {
          navItems.forEach(item => item.classList.remove('active'));
          if (navLink) navLink.classList.add('active');
        }
      });
    }

    window.addEventListener('scroll', highlightNavigation);

    /* ==============================
       Project Card Interactions
    ================================ */
    const projectCards = document.querySelectorAll('.project-card');

    projectCards.forEach(card => {
      card.addEventListener('mouseenter', function() {
        this.style.transform = 'translateY(-10px) scale(1.02)';
      });

      card.addEventListener('mouseleave', function() {
        this.style.transform = 'translateY(-10px) scale(1)';
      });
    });

    /* ==============================
       Typing Effect for Hero (Optional Enhancement)
    ================================ */
    const heroTitle = document.querySelector('.hero h1');
    const gradientText = heroTitle.querySelector('.gradient-text');
    const originalText = gradientText.textContent;
    
    // Store original text and reset for animation
    setTimeout(() => {
      gradientText.style.opacity = '1';
    }, 800);

    /* ==============================
       Parallax Effect on Scroll
    ================================ */
    window.addEventListener('scroll', () => {
      const scrolled = window.pageYOffset;
      const heroContent = document.querySelector('.hero-content');
      if (heroContent && scrolled < window.innerHeight) {
        heroContent.style.transform = `translateY(${scrolled * 0.3}px)`;
        heroContent.style.opacity = 1 - scrolled / 600;
      }
    });

    /* ==============================
       Initialize on Load
    ================================ */
    window.addEventListener('load', () => {
      highlightNavigation();
      document.querySelectorAll('.fade-in').forEach(el => {
        if (el.getBoundingClientRect().top < window.innerHeight) {
          el.classList.add('visible');
        }
      });
    });

    /* ==============================
       Cursor Trail Effect (Optional)
    ================================ */
    let mouseX = 0;
    let mouseY = 0;
    let cursorX = 0;
    let cursorY = 0;

    document.addEventListener('mousemove', (e) => {
      mouseX = e.clientX;
      mouseY = e.clientY;
    });

    /* ==============================
       Performance: Debounce Scroll
    ================================ */
    function debounce(func, wait) {
      let timeout;
      return function executedFunction(...args) {
        const later = () => {
          clearTimeout(timeout);
          func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
      };
    }

    const debouncedHighlight = debounce(highlightNavigation, 50);
    window.addEventListener('scroll', debouncedHighlight);
```
---
---
---
# Version: 2    Interactive and Responsive portfolio using React and CSS  

## 1. Core Interactivity (Must-Have)

### 🔹 Interactive Navigation

* **Sticky / dynamic navbar**

  * Change background on scroll
  * Highlight active section
  * Shrink or animate on scroll
  * **How**

    * Use `useEffect` + `window.scrollY`
    * Track active section with `IntersectionObserver`
    * Store active link in state

* **Mobile hamburger menu**

  * Toggle open/close menu
  * Animate slide-in menu
  * **How**

    * `useState` for menu open/close
    * CSS transitions / transform animations

---

### 🔹 Smooth Scrolling

* Smooth scroll to sections when clicking nav links
* **How**

  * CSS: `scroll-behavior: smooth`
  * OR use `ref.scrollIntoView({ behavior: "smooth" })`

---

## 2. Page & Section Animations

### 🔹 Scroll-Based Animations

* Fade in sections on scroll
* Slide elements from left/right/bottom
* Stagger animations for lists
* **How**

  * Use `IntersectionObserver`
  * Or use libraries like:

    * `framer-motion`
    * `AOS (Animate on Scroll)`

---

### 🔹 Hover Animations

* Buttons hover effects
* Card lift & shadow on hover
* Image zoom on hover
* **How**

  * CSS `:hover`
  * `transform`, `transition`, `box-shadow`

---

## 3. Projects Section (Highly Interactive)

### 🔹 Project Cards

* Hover overlay with description & links
* Click to expand / modal view
* **How**

  * Store selected project in state
  * Conditionally render a modal component

---

### 🔹 Project Filtering

* Filter by tech (React, Node, CSS, etc.)
* Search projects by name
* **How**

  * Store filter in state
  * Use `Array.filter()` on project data

---

### 🔹 Live Demo & GitHub Buttons

* Open in new tab
* Disable buttons if links are missing
* **How**

  * Conditional rendering in JSX

---

## 4. Contact Section (Major Upgrade)

### 🔹 Contact Form Logic

* Controlled inputs
* Form validation
* Error & success messages
* **How**

  * Use `useState` for inputs
  * Validate before submit
  * Show status messages

---

### 🔹 Form Submission

* Send email or save messages
* **How**

  * Use:

    * EmailJS
    * Formspree
    * Backend API (Node / Firebase)

---

### 🔹 Loading States

* Disable submit button while sending
* Show spinner
* **How**

  * `isLoading` state
  * Conditional rendering

---

## 5. Responsiveness (Critical)

### 🔹 Mobile-First Layout

* Stack sections on small screens
* Adjust padding and spacing
* **How**

  * CSS media queries
  * Flexbox & Grid

---

### 🔹 Responsive Typography

* Scale font sizes with screen size
* **How**

  * `clamp()` in CSS

  ```css
  font-size: clamp(1rem, 2vw, 2rem);
  ```

---

### 🔹 Responsive Images

* Prevent overflow
* Maintain aspect ratio
* **How**

  * `max-width: 100%`
  * `object-fit: cover`

---

## 6. User Experience Enhancements

### 🔹 Dark / Light Mode

* Toggle theme
* Persist user preference
* **How**

  * Context API for theme
  * Store theme in `localStorage`
  * Toggle CSS variables

---

### 🔹 Back-to-Top Button

* Appears after scrolling
* Smooth scroll to top
* **How**

  * Track scroll position
  * Button with `window.scrollTo()`

---

### 🔹 Loading Animations

* Page loader
* Skeleton loaders for content
* **How**

  * Conditional rendering
  * CSS animations

---

## 7. Accessibility (Very Important)

### 🔹 Keyboard Navigation

* Tab navigation
* Focus styles
* **How**

  * Semantic HTML
  * `:focus-visible`

---

### 🔹 ARIA & Semantic Tags

* Proper roles for nav, buttons, modals
* Screen reader support
* **How**

  * Use `<nav>`, `<main>`, `<section>`
  * Add `aria-label`

---

### 🔹 Color Contrast

* Text readable on all backgrounds
* **How**

  * Use WCAG contrast tools

---

## 8. Performance Optimizations

### 🔹 Code Splitting

* Load sections lazily
* **How**

  * `React.lazy()` + `Suspense`

---

### 🔹 Image Optimization

* Use compressed images
* Lazy load images
* **How**

  * `loading="lazy"`

---

### 🔹 Memoization

* Avoid unnecessary re-renders
* **How**

  * `React.memo`
  * `useCallback`

---

## 9. Content Interactivity

### 🔹 Skills Section

* Animated progress bars
* Skill level indicators
* **How**

  * Animate width using CSS
  * Trigger on scroll

---

### 🔹 Blog Section (Optional)

* Expand/collapse posts
* Markdown rendering
* **How**

  * Store posts as JSON or Markdown
  * Toggle content visibility

---

## 10. Final Professional Touches

### 🔹 SEO Basics

* Page titles & meta tags
* Open Graph tags
* **How**

  * `react-helmet`

---

### 🔹 Error Handling

* Fallback UI
* 404 page
* **How**

  * React Router
  * Error boundaries

---

## Suggested Priority Order

1. Responsive layout + mobile navbar
2. Smooth scrolling & scroll animations
3. Interactive projects section
4. Contact form logic
5. Dark mode
6. Accessibility & performance

---

You can:  

* **Map this directly to your existing components**








