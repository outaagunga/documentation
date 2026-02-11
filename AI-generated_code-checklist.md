
# ✅ AI-Generated Code & Explanation Review Checklist

Use this checklist whenever reviewing AI-generated code, architecture suggestions, or technical explanations.

---

## 🔐 1. Security Review

* [ ] Does the code validate all external inputs?
* [ ] Are there risks of SQL injection, XSS, CSRF, command injection, etc.?
* [ ] Are authentication and authorization handled correctly?
* [ ] Are secrets (API keys, tokens, passwords) exposed or hardcoded?
* [ ] Does it follow the principle of least privilege?
* [ ] Are dependencies safe and from trusted sources?
* [ ] Does it avoid unsafe deserialization?
* [ ] Are error messages leaking sensitive information?

**Ask:** *“If this were deployed today, how could it be attacked?”*

---

## 📏 2. Coding Standards & Style

* [ ] Does it follow our team’s coding standards?
* [ ] Is naming consistent and meaningful?
* [ ] Is the formatting consistent with project style guidelines?
* [ ] Does it follow our architecture patterns?
* [ ] Are comments helpful and accurate (not redundant or misleading)?
* [ ] Does it match the language/framework version we use?

**Ask:** *“Would this pass our normal code review?”*

---

## 🧠 3. Logic & Correctness

* [ ] Does it actually solve the requested problem?
* [ ] Are there logical errors or incorrect assumptions?
* [ ] Are edge cases handled?
* [ ] Does it handle null/undefined/empty inputs?
* [ ] Are error conditions handled gracefully?
* [ ] Does it consider concurrency or race conditions (if applicable)?
* [ ] Are timezones, locales, or encoding issues handled correctly?

**Ask:** *“What inputs would break this?”*

---

## ⚡ 4. Performance & Scalability

* [ ] Are there obvious performance bottlenecks?
* [ ] Does it avoid unnecessary loops or expensive operations?
* [ ] Are database queries efficient?
* [ ] Is caching needed?
* [ ] Does it scale with large datasets?
* [ ] Are memory usage implications considered?

**Ask:** *“What happens at 10x scale?”*

---

## 🧪 5. Testing & Reliability

* [ ] Are unit tests provided or suggested?
* [ ] Are edge cases covered in tests?
* [ ] Does it handle failure scenarios?
* [ ] Are retries or timeouts implemented where needed?
* [ ] Is logging appropriate and useful?

**Ask:** *“How would I verify this works?”*

---

## 🔄 6. Maintainability & Readability

* [ ] Is the code overly complex?
* [ ] Can this be simplified?
* [ ] Is duplication avoided?
* [ ] Is the solution modular and reusable?
* [ ] Does it introduce technical debt?
* [ ] Would another developer easily understand this in 6 months?

**Ask:** *“Would I want to maintain this?”*

---

## 📦 7. Dependency & Integration Check

* [ ] Are new dependencies necessary and approved?
* [ ] Are versions compatible with our system?
* [ ] Does it break backward compatibility?
* [ ] Does it integrate properly with existing services?
* [ ] Are API contracts respected?

---

## 📖 8. AI-Specific Validation

* [ ] Does the explanation contain fabricated APIs or libraries?
* [ ] Are referenced methods/functions real?
* [ ] Does it assume features not available in our stack?
* [ ] Does it cite outdated practices?
* [ ] Does it oversimplify complex requirements?

**Ask:** *“Is the AI confidently wrong anywhere?”*

---

# 🚩 High-Risk Red Flags

If you see any of these, review extra carefully:

* Confident tone with no error handling
* Missing input validation
* Hardcoded secrets
* Deprecated or imaginary APIs
* No edge case consideration
* Security-sensitive code generated in one pass
* “This should work” with no verification guidance

---

# 🏢 Team Implementation Recommendation

To operationalize this checklist:

1. Add it to your engineering handbook.
2. Require AI-generated code to be labeled in PRs.
3. Add a PR template section:

   > “AI-generated code reviewed using AI checklist? (Yes/No)”
4. Consider automated scanning (SAST, linters, dependency scanners).
5. Conduct occasional team reviews of AI usage patterns.

---

# 💡 Optional: 5-Question Quick Review (Fast Mode)

If short on time, at minimum ask:

1. Is this secure?
2. Does it match our standards?
3. What edge cases are missing?
4. Will this scale?
5. Would I approve this in a real PR?

---
