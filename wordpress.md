
This is a strong start — but right now it’s more like **notes for a developer** than a **step-by-step system you can actually follow while building**.
Below is a **fully structured, no-guesswork implementation guide** that fills in what’s missing and aligns with how a real WordPress build actually works.

This guide is designed specifically for what you are building at:
**[https://internationalinvestorsdirectory.com](https://internationalinvestorsdirectory.com)** — a gated investor directory.

We’ll do this in **segments**, starting with the foundation.
This message = **Segment 1: Architecture + Data Model (The Engine)**

---

# 🧱 SEGMENT 1 — THE CORE SYSTEM ARCHITECTURE

*(Everything else depends on this)*

Right now you’re thinking in terms of:

> “Investors, guests, admins, fields…”

But WordPress only understands:

* Posts
* Users
* Metadata
* Permissions

So we must map your business idea into WordPress’s data engine correctly.

---

# 1️⃣ Replace Posts with a Custom Database Object: “Investors”

You do **NOT** use blog posts.

You create a **Custom Post Type (CPT)**.

This makes WordPress treat each Investor as a **database record**.

### Install

Plugin:

> **Custom Post Type UI**

Create:

```
Post Type Slug: investor
Plural: Investors
Singular: Investor
Public: YES
Has Archive: YES
Supports: Title only
```

This gives you:

```
/investor/john-smith
/investor/african-venture-capital
```

Each Investor is now:

> A first-class object in WordPress’s database

---

# 2️⃣ Create a Real Database Schema (Using ACF)

This is where most WordPress projects fail.

You are NOT making text boxes.
You are defining **columns in a database table**.

Install:

> **Advanced Custom Fields PRO**

Create a Field Group:

```
Field Group Name: Investor Data
Location Rule: Post Type = Investor
```

Now add these fields:

| Field Label         | Field Name       | Type               | Visibility   |
| ------------------- | ---------------- | ------------------ | ------------ |
| Investor Name       | investor_name    | Text               | Public       |
| Firm Name           | firm_name        | Text               | Public       |
| Industry Focus      | industry_focus   | Taxonomy or Select | Public       |
| Investment Stage    | investment_stage | Select             | Public       |
| Country             | country          | Select             | Public       |
| Website             | website          | URL                | Public       |
| Email               | email            | Email              | Protected    |
| Phone               | phone            | Text               | Protected    |
| WhatsApp            | whatsapp         | Text               | Protected    |
| LinkedIn            | linkedin         | URL                | Protected    |
| Verification Status | verified         | True/False         | Admin only   |
| Featured            | featured         | True/False         | Public logic |

This creates a **proper database schema** for every investor.

You have now replaced Excel.

---

# 3️⃣ Create Industry & Country as TAXONOMIES (Not fields)

Why?
Because you want filtering, searching, and browsing.

Create Taxonomies using CPT UI:

```
Taxonomy: Industry
Attach to: Investor

Taxonomy: Country
Attach to: Investor
```

Now investors can be:

* Fintech
* Real Estate
* Energy
* Kenya
* UAE
* UK

This lets you build:

```
/industry/fintech
/country/kenya
```

This is how real directories work.

---

# 4️⃣ Your Site is Actually THREE Different Websites

Your system must behave differently depending on who is viewing it.

| Visitor        | What They See                         |
| -------------- | ------------------------------------- |
| Guest          | Investor names, industries, countries |
| Logged-in User | All contact info                      |
| Admin          | All data + Edit buttons               |

This means:

> You do NOT make three pages.
> You make **one template** with conditional logic.

---

# 5️⃣ Define the User System

Install:

> **Members (by MemberPress)**

Create roles:

### Guest (default)

Not logged in

### Subscriber

Rename to:

```
Verified User
```

Permissions:

* Read Investors
* Access restricted fields

### Administrator

Full access

This lets WordPress know:

> Who is allowed to see protected data

---

# 6️⃣ Your Investors Are NOT Users

This is critical.

Many people get this wrong.

Your **investors are NOT WordPress users**
They are **Custom Post Type records**

Users are:

* Visitors
* Subscribers
* Admins

Investors are:

* Data entries
* Directory listings

They are completely separate.

---

# 7️⃣ How the Site Will Actually Work

Behind the scenes:

```
User logs in
↓
WordPress checks their role
↓
Investor page loads
↓
Template checks permissions
↓
Shows or hides fields
```

There is no magic.
Everything is controlled in the template.

---

# What You Have Built So Far

At the end of Segment 1 you now have:

✔ A real Investor database
✔ Structured fields
✔ Countries and industries
✔ User roles
✔ The foundation for permissions

This is the engine.

---

# Next Segment

Next I will give you:

**SEGMENT 2 — Frontend Admin System**

> How you add, edit and approve investors **without ever opening wp-admin**

This is where your directory becomes scalable.

Say **“Continue”** when ready.

Perfect — now we turn WordPress into a **real business system**, not a blog.

This is where most projects fail, so read this slowly.

---

# 🧩 SEGMENT 2 — FRONTEND ADMIN SYSTEM

*(How YOU manage everything without wp-admin)*

Right now, WordPress still assumes:

> “Admins sit inside wp-admin and everyone else just reads.”

You want:

> “Admins run everything from the website like a SaaS app.”

So we must build a **private admin control panel** on the frontend.

---

# 1️⃣ You Are Building a Private Admin App Inside Your Website

You will create these pages:

| URL               | Purpose                  |
| ----------------- | ------------------------ |
| `/dashboard`      | Admin home               |
| `/add-investor`   | Create new investor      |
| `/edit-investor`  | Edit existing investor   |
| `/investors-list` | Admin sees all investors |

These pages will NOT be visible to guests or subscribers.

---

# 2️⃣ Install the Tool That Makes This Possible

You already installed ACF Pro in Segment 1.

Now install:

> **ACF Frontend (or ACF Extended / WP User Frontend Pro)**

This lets ACF forms:

* Create CPTs
* Update CPTs
* Edit CPTs
  From the frontend.

---

# 3️⃣ Create the Admin Dashboard Page

Create a page:

```
Title: Dashboard
Slug: dashboard
```

Now restrict it:
Using **Members**

```
Only role allowed: Administrator
```

On this page, add:

* A button to Add Investor
* A list of existing Investors

---

# 4️⃣ Create the “Add Investor” Form

Create page:

```
Title: Add Investor
Slug: add-investor
Restricted to: Administrator
```

Insert an ACF form with this configuration:

```
Create New Post
Post Type: Investor
Post Status: Publish
Fields:
- All fields from Investor Data group
```

This means:

> When you submit the form, WordPress creates a new Investor record.

No wp-admin.
No scary backend.

---

# 5️⃣ Create the “Edit Investor” Page

Create:

```
Title: Edit Investor
Slug: edit-investor
Restricted to: Administrator
```

Insert ACF Frontend form:

```
Post ID: From URL parameter ( ?id= )
Fields: Investor Data
```

So when you go to:

```
/edit-investor?id=248
```

You are editing Investor #248.

---

# 6️⃣ Add Edit Buttons to Every Investor Page

In your investor template:

```
if ( current_user_can('administrator') ) {
   show "Edit Investor" button
}
```

Button links to:

```
/edit-investor?id=THIS_INVESTOR_ID
```

This turns your site into:

> Click → Edit → Save → Live instantly

---

# 7️⃣ Create the Admin Investor List

Create page:

```
Title: Investor Manager
Slug: investors-list
Restricted to: Administrator
```

Insert a loop that shows:

| Investor | Industry | Country | Verified | Edit |
| -------- | -------- | ------- | -------- | ---- |

Each Edit links to:

```
/edit-investor?id=...
```

Now you have a real admin panel.

---

# 8️⃣ Add Verification Workflow

Remember this field:

```
verified (true/false)
```

You will use it to control visibility.

Admins check:
✔ Verified

Unverified investors:

* Appear in admin list
* Do NOT appear to public users

This protects quality.

---

# What You Have Now

At the end of Segment 2 you now have:

✔ A frontend admin dashboard
✔ Add investors
✔ Edit investors
✔ Approve / verify system
✔ No wp-admin required

You’ve basically built a mini CRM inside WordPress.

---

# Next Segment

**SEGMENT 3 — Access Control & Visibility Logic**
This is where we lock down emails, phones, WhatsApp, etc so:

* Guests see blurred data
* Users see everything
* Admins can edit

Say **“Continue”** when ready.

Good — this is the segment that turns your site into a **real gated business**, not just a listing site.

---

# 🔐 SEGMENT 3 — ACCESS CONTROL & VISIBILITY LOGIC

*(Who sees what, and why)*

Your entire business model depends on this:

> Contact data is the asset.
> Access to it is what people pay or register for.

So we enforce this **at the template level**, not by hiding text.

---

# 1️⃣ The Three-Layer Visibility Model

Every Investor page runs the same logic:

| Visitor Type   | What they see                                    |
| -------------- | ------------------------------------------------ |
| Guest          | Name, firm, industry, country                    |
| Logged-in User | Everything (email, phone, WhatsApp, LinkedIn)    |
| Admin          | Everything + Edit button + Verification controls |

This is **not** done in ACF.
This is done in the Investor page template.

---

# 2️⃣ Create a Custom Investor Page Template

Your theme needs:

```
single-investor.php
```

This file controls:

```
/investor/john-smith
/investor/african-venture-capital
```

Inside this file you output:

Public Fields:

* investor_name
* firm_name
* industry
* country
* website

Protected Fields:

* email
* phone
* whatsapp
* linkedin

---

# 3️⃣ The Core Logic (Human Version)

When the page loads, WordPress asks:

```
Is the visitor logged in?
    YES → Show everything
    NO → Hide contact info
```

Then it asks:

```
Is this user an admin?
    YES → Also show Edit button
```

---

# 4️⃣ What Guests Should See

Instead of just hiding data, you show **teasers**:

Example:

```
Email: 🔒 Login to view
Phone: 🔒 Login to view
WhatsApp: 🔒 Login to view
```

Or blur the text using CSS:

```
blurred-email@gmail.com
```

This creates psychological friction → registrations.

---

# 5️⃣ Restricting Data Properly

Do NOT rely on page protection plugins.
Your Investor pages must remain public for SEO.

The restriction happens only on the fields.

So inside your template:

Public fields → always visible
Protected fields → wrapped in:

```
if ( is_user_logged_in() )
```

This means:

* Google can index investor names
* Users must log in to see contacts

Perfect for growth.

---

# 6️⃣ Verification Logic

Remember the field:

```
verified (true/false)
```

Now add this rule:

If investor is NOT verified:

* Only admin sees it
* Public cannot access the page

So before anything displays:

```
if ( NOT verified AND user is NOT admin )
→ redirect or show 404
```

This stops spam and fake investors.

---

# 7️⃣ Featured Investors

Use the field:

```
featured (true/false)
```

On:

* Homepage
* Industry pages
* Country pages

You show:

> Featured investors first

This becomes your monetization lever later.

---

# What You Have Now

At this point your website is no longer a website — it’s a **controlled data platform**.

You now have:

✔ A real investor database
✔ Frontend admin panel
✔ Gated access to contact info
✔ Verification system
✔ Featured listings

---

# Next Segment

**SEGMENT 4 — Search, Filtering & Directory Pages**
This is where users can browse:

* By country
* By industry
* By investment stage
  like a real professional platform.

Say **“Continue”** when ready.

Now we make this feel like a **real investor marketplace** instead of a static directory.

---

# 🔎 SEGMENT 4 — SEARCH, FILTERING & DIRECTORY PAGES

*(How users actually find investors)*

Right now you have:

> Investors stored correctly

But users need to **browse**, **filter**, and **discover**.

This is where 90% of directory sites succeed or fail.

---

# 1️⃣ Your Site Needs These Core Pages

You are not building one big list.
You are building a **navigation system**.

You need:

| URL                 | Purpose              |
| ------------------- | -------------------- |
| `/investors`        | All investors        |
| `/industry/fintech` | Fintech investors    |
| `/country/kenya`    | Kenyan investors     |
| `/stage/seed`       | Seed stage investors |
| `/search`           | Filtered results     |

These must be **auto-generated** from your data.

---

# 2️⃣ Industry & Country Already Work (We Built Them)

Because you created:

* Industry taxonomy
* Country taxonomy

WordPress already gives you:

```
/industry/fintech
/country/kenya
```

Now we just need to make them look good.

Each of these pages should use:

```
archive-investor.php
taxonomy-industry.php
taxonomy-country.php
```

They all show a grid of investors.

---

# 3️⃣ What Shows in Investor Cards

Each investor should appear as a **card**, not a wall of text.

Card fields:

* Investor Name
* Firm Name
* Country
* Industry
* “View Profile” button

Contact details NEVER appear here.

This makes browsing fast and safe.

---

# 4️⃣ Build the Filter System

Install:

> **FacetWP** (best)
> or
> **JetSmartFilters** (if using Elementor)

Filters:

* Country
* Industry
* Investment Stage
* Featured
* Verified

These connect directly to:

* ACF fields
* Taxonomies

Now users can:

> “Show me Fintech investors in Kenya at Seed stage”

This is where your platform becomes valuable.

---

# 5️⃣ Search Bar

Your search must target:

* Investor Name
* Firm Name
* Industry
* Country

NOT:

* Emails
* Phones

So you configure search to:

```
Search CPT: Investor
Fields: Name, Firm, Industry
```

This keeps protected data safe.

---

# 6️⃣ Lock Results for Guests

Guests should see:

* Investor cards
* But NOT full profiles

So:
Clicking a profile → redirects to login page if guest

Logged-in users → full profile.

This creates natural signup flow.

---

# What You Have Now

At this stage your site is:

✔ A searchable investor database
✔ Country & industry navigation
✔ Filters like a professional platform
✔ Guest → Register → Access funnel

You are now very close to something people would pay for.

---

# Next Segment

**SEGMENT 5 — Monetization, Premium Access & Growth Loops**

This is where:

* You decide who pays
* Who gets access
* How the platform scales

Say **“Continue”** when ready.

Now we get to the part that turns this from a project into a **business**.

Up to now, you’ve built:

* A real investor database
* A gated access system
* A discovery engine

Now we decide:

> Who gets what, and for how much.

---

# 💰 SEGMENT 5 — MONETIZATION, ACCESS LEVELS & GROWTH

Right now you have only:

* Guest
* Logged-in user
* Admin

That’s not enough for a business.

We extend this into **access tiers**.

---

# 1️⃣ Define Your Revenue Model

Your site should run on this:

| User Type    | Access                        |
| ------------ | ----------------------------- |
| Guest        | Browse investors (no contact) |
| Free Account | See limited contact info      |
| Paid Member  | See full contact info         |
| Admin        | Manage everything             |

This means:

> We must add at least 2 new roles.

---

# 2️⃣ Create New Roles

Using **Members** plugin:

Create:

### Free Member

* Can log in
* Can view:

  * Email only
  * No phone / WhatsApp

### Premium Member

* Can view:

  * Email
  * Phone
  * WhatsApp
  * LinkedIn

This lets you **sell access**.

---

# 3️⃣ Split Contact Fields by Tier

In ACF:
You already created:

* Email
* Phone
* WhatsApp
* LinkedIn

Now we define:

| Field    | Who sees it    |
| -------- | -------------- |
| Email    | Free + Premium |
| Phone    | Premium only   |
| WhatsApp | Premium only   |
| LinkedIn | Premium only   |

Your template now checks:

```
If user is Premium → show everything  
If user is Free → show email only  
If Guest → show nothing
```

---

# 4️⃣ Add a Membership System

Install:

> **Paid Memberships Pro**
> or
> **MemberPress**

Create plans:

| Plan | Price     | Role           |
| ---- | --------- | -------------- |
| Free | $0        | Free Member    |
| Pro  | $29/month | Premium Member |

Now when someone pays:
WordPress automatically changes their role.

Your template instantly updates what they can see.

No manual work.

---

# 5️⃣ Upgrade Triggers Everywhere

On every Investor page:

If user = Free:
Show:

> “Upgrade to see phone & WhatsApp”

If user = Guest:
Show:

> “Register to contact investors”

This turns every investor profile into a **sales page**.

---

# 6️⃣ Monetize Featured Investors

Remember:

```
featured (true/false)
```

Now you sell:

> “$99 to be featured on homepage & top of search”

Admins toggle:
✔ Featured

Featured investors:

* Appear first in all searches
* Highlighted in UI

This is your B2B revenue stream.

---

# What You Now Own

You now have:

✔ A gated data platform
✔ A freemium → premium upgrade funnel
✔ Investor advertising income
✔ A scalable SaaS-style system

You are no longer building a WordPress site.
You have built a **marketplace engine**.

---

If you want, next I can give you:

**SEGMENT 6 — Investor Self-Registration (let investors submit themselves)**
This is how you scale without manual data entry.

Just say **“Continue”**.

This is where your platform becomes **self-growing** instead of admin-heavy.

Right now:

* You manually add investors.

After this:

* Investors submit themselves.
* You just approve them.

---

# 🚀 SEGMENT 6 — INVESTOR SELF-REGISTRATION & APPROVAL SYSTEM

This turns your site into a **real directory platform**.

---

# 1️⃣ Create the “Submit Your Fund” Page

Create page:

```
Title: List Your Fund
Slug: list-your-fund
Public: YES
```

This page will contain a form where investors submit their details.

---

# 2️⃣ Build the Investor Submission Form

Using **ACF Frontend** (or WP User Frontend):

Form settings:

```
Create New Post
Post Type: Investor
Post Status: Draft
Fields:
- All Investor Data fields EXCEPT:
    - verified
    - featured
```

This means:

> Investors cannot approve themselves.

They enter:

* Name
* Firm
* Industry
* Country
* Email
* Phone
* WhatsApp
  etc.

---

# 3️⃣ What Happens When They Submit

Behind the scenes:

```
Investor submits form
↓
WordPress creates Investor (DRAFT)
↓
Admin gets notified
↓
Admin reviews
↓
Admin checks "Verified"
↓
Post becomes public
```

You control quality.

---

# 4️⃣ Create the “Pending Investors” Queue

Your admin dashboard page should show:

| Investor | Firm | Country | Submitted | Approve |
| -------- | ---- | ------- | --------- | ------- |

Only investors with:

```
verified = false
```

appear here.

Click → Review → Approve → Live.

---

# 5️⃣ Prevent Spam

Add to submission form:

* Google reCAPTCHA
* Email verification

This stops bots.

---

# 6️⃣ Turn This Into a Lead Engine

After submission, redirect investors to:

```
/upgrade-to-featured
```

Offer:

> “Get featured on homepage for $99”

Now your platform generates:

* Listings
* Sales
* Leads

On autopilot.

---

# What You Have Built

You now have:

✔ Investors submit themselves
✔ Admin approves quality
✔ Listings go live automatically
✔ Paid upgrades for exposure

This is exactly how Crunchbase, AngelList and Clutch work internally.

---

If you want, next I can give you:

**SEGMENT 7 — UI, Trust & Conversion Optimization**
How to make this feel like a serious international platform instead of a hobby site.

Just say **“Continue”**.

Now we make people **trust** and **use** this platform.

Right now you have the mechanics.
Segment 7 is about turning it into something that looks and feels **credible, international and investable**.

---

# 🧠 SEGMENT 7 — UI, TRUST & CONVERSION OPTIMIZATION

This is where most directories fail — not because of tech, but because they *look* untrustworthy.

You are asking users to:

* Register
* Possibly pay
* Contact investors

So the site must signal:

> “This is a serious global platform.”

---

# 1️⃣ Your Homepage Must Do 3 Things in 5 Seconds

When someone lands on:
`internationalinvestorsdirectory.com`

They must instantly understand:

1. **What this is**
2. **Why it’s valuable**
3. **What to do next**

Your hero section should say something like:

> “Find Verified Investors by Industry and Country — Connect Directly”

Then two buttons:

* Browse Investors
* List Your Fund

Nothing else.

---

# 2️⃣ Social Proof (Critical)

You need:

* “100+ verified investors”
* “30+ countries”
* “Trusted by founders worldwide”

Even if you’re small now, show:

> Growth metrics

This massively increases registrations.

---

# 3️⃣ Investor Profiles Must Look Premium

Each Investor page must feel like:

> A professional financial profile

Include:

* Logo (add an ACF image field)
* Firm description
* Investment focus
* Countries
* Website
* Contact details (gated)

This builds confidence.

---

# 4️⃣ Trust Signals

Add site-wide:

* SSL (https)
* Privacy Policy
* Terms
* “We verify every investor before publishing”

This protects you legally and psychologically.

---

# 5️⃣ The Conversion Loop

Your site should constantly push users into this flow:

```
Browse investors (guest)
↓
Click profile
↓
Blocked contact info
↓
Register
↓
See email
↓
Want phone / WhatsApp
↓
Upgrade
```

This must happen naturally on every profile.

---

# 6️⃣ The Investor Funnel

Investors experience:

```
Google search
↓
Find your site
↓
List Your Fund
↓
Submit details
↓
Get verified
↓
Appear live
↓
Offered Featured upgrade
```

Now you have **two-sided growth**:

* Founders searching
* Investors joining

That is a marketplace.

---

# You Now Have a Complete System

You have built:

✔ A verified investor database
✔ A gated access platform
✔ A monetization engine
✔ A self-submitting marketplace
✔ A global directory brand

This is no longer a WordPress site.
It’s a **platform business**.

---

If you want, next I can give you:

**SEGMENT 8 — Technical Deployment Checklist**

> Hosting, security, performance, backups, SEO, and how to launch this without breaking anything.

Just say **“Continue”**.

This is the part that separates a **working platform** from a **site that crashes, gets hacked, or disappears**.

You can build the best system in the world — if this is wrong, you lose everything.

---

# 🛡️ SEGMENT 8 — TECHNICAL DEPLOYMENT & LAUNCH CHECKLIST

This is your **production-grade launch framework** for
**internationalinvestorsdirectory.com**

---

# 1️⃣ Hosting (This is Non-Negotiable)

You must use hosting designed for:

* WordPress
* Databases
* Logged-in users
* Dynamic filtering

Use one of:

* Cloudways
* Kinsta
* SiteGround Cloud
* WP Engine

Avoid cheap shared hosting.
You are running a **data platform**, not a blog.

---

# 2️⃣ SSL, Backups & Security

Install:

| Purpose          | Plugin      |
| ---------------- | ----------- |
| SSL & firewall   | Cloudflare  |
| Security         | Wordfence   |
| Backups          | UpdraftPlus |
| Malware scanning | MalCare     |

You must have:

* Daily backups
* Firewall
* Login protection

Because you store:

> Business emails and phone numbers

---

# 3️⃣ Performance

Install:

* WP Rocket
* Redis (server level)

Why?
Because filters + ACF + logged-in users = heavy database queries.

Fast site = higher Google rankings.

---

# 4️⃣ SEO Architecture

Your SEO is already built into your data model.

You have:

* /investor/john-smith
* /industry/fintech
* /country/kenya

Install:

> RankMath or Yoast

Set:

* Investor pages indexable
* Industry pages indexable
* Country pages indexable
* Contact fields NOT indexed

This makes Google send founders to you for free.

---

# 5️⃣ Email System

You must have:

* Account verification
* Password reset
* Admin notifications
* Investor submission alerts

Install:

> WP Mail SMTP
> Connect to:
> Gmail, SendGrid or Mailgun

This makes sure emails actually arrive.

---

# 6️⃣ Final Pre-Launch Checklist

Before going public, check:

✔ Can guests browse investors
✔ Can users register
✔ Can free users see email only
✔ Can premium users see phone/WhatsApp
✔ Can investors submit themselves
✔ Can admins approve
✔ Can admins feature investors
✔ Are backups running
✔ Is SSL active

If all are YES → You are live.

---

# What You Built

You have created:

A **two-sided international investment marketplace**
with:

* Verified data
* Paid access
* Growth loops
* SEO traffic
* Investor submissions

This is the same architecture used by:

* Crunchbase
* AngelList
* Clutch
* PitchBook

Just built on WordPress.

---

If you want, I can now help you:

* Design your homepage copy
* Design your investor profile layout
* Or turn this into a product roadmap

Tell me what you want next.

---
---
---
To implement this logic in WordPress, you need to move beyond standard "Posts" and "Pages" and use a combination of **Custom Post Types**, **Custom Fields**, and **Role Management**.

Since you want the Admin to update everything from the **frontend** (without seeing the scary WordPress backend Dashboard), you will need a "Frontend Dashboard" setup.

### **1. Define Your User Roles**

WordPress comes with default roles, but for your directory, you should customize them using a plugin like **User Role Editor** or **Members**:

* **Administrator:** Full access to the frontend "Update" buttons and the backend.
* **Subscriber (Registered User):** Can see "Verified Emails" and "Direct Contact" info.
* **Guest (Non-Registered):** Can see "Investor Names" and "Industries" but sees a "Login to View" blurred box for contact details.

### **2. Structuring the Data (The Engine)**

Don't just type investor data into a text box. Use a structured database approach so the frontend knows exactly what to display to whom.

* **Custom Post Types (CPT):** Create a CPT called "Investors."
* **Advanced Custom Fields (ACF):** This is the most important tool. Create fields for:
* *Investor Name* (Public)
* *Investment Stage* (Public)
* *Email Address* (Protected - Only for Registered Users)
* *Phone Number* (Protected - Only for Registered Users)



### **3. Frontend Data Entry (For the Admin)**

To allow the Admin to upload/update data without going to the backend, you can use **ACF Frontend Forms**.

* **How it works:** You create a private page (e.g., `yourdomain.com/admin-upload`) that only the Admin role can see.
* **The Form:** You embed an ACF form on that page. When the Admin fills it out, it automatically creates or updates an "Investor" profile in your database.

---

### **4. Controlling Visibility (The Logic)**

You need to wrap your sensitive data in "Conditional Logic." You can do this via code in your template files or using a Page Builder (like Elementor Pro or Bricks).

**The logic looks like this:**

1. **If User = Admin:** Show "Edit Investor" button + All Data.
2. **Else If User = Logged In:** Show All Data (No Edit button).
3. **Else (Guest):** Show Name/Industry + "Please Login to see Contact Details" message.

### **5. Recommended Plugin Stack**

If you want to build this efficiently, I recommend this combination:

| Function | Recommended Plugin |
| --- | --- |
| **Data Structure** | **ACF** (Advanced Custom Fields) |
| **User Roles** | **Members** (by MemberPress) |
| **Frontend Editing** | **ACF Frontend Form** or **WP User Frontend** |
| **Content Restriction** | **Restrict Content Pro** or **Ultimate Member** |

### **Example Code Logic (for your developer or your `single-investor.php` file):**

```php
// Check if user is logged in
if ( is_user_logged_in() ) {
    // Show the sensitive data
    echo get_field('investor_email'); 
    
    // Check if the user is an admin to show the 'Edit' button
    if ( current_user_can('administrator') ) {
        echo '<a href="/edit-investor-page/?id=' . get_the_ID() . '">Edit this Entry</a>';
    }
} else {
    // Show a lock message for guests
    echo "Register to view contact info.";
}

```


