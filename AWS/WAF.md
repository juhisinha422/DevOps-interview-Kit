# WAF (Web Application Firewall) – DevOps Notes

## 🚨 Common Misconception
Most engineers say:
👉 “We are using WAF for security”

But miss an important detail:
👉 *Which type of WAF?*

WAF is **not a single thing** — it has different types and configurations.

---

## 🔹 Types of WAF

### 1. Managed Rule WAF
- Pre-built security rules (based on OWASP standards)
- Protects against common attacks:
  - SQL Injection (SQLi)
  - Cross-Site Scripting (XSS)
  - Known vulnerabilities
- Automatically updated by cloud providers (AWS, Azure, etc.)

✅ Advantages:
- Easy to enable
- No manual rule creation
- Good baseline security

---

### 2. Custom Rule WAF
- User-defined rules based on application needs
- Examples:
  - Block specific IP addresses
  - Restrict traffic from certain countries
  - Rate limiting (prevent DDoS/brute force)
  - Block specific request patterns or headers

✅ Advantages:
- Full control
- Tailored to business logic
- Better protection for advanced threats

---

## ⚙️ WAF Modes

### ✔ Detection Mode (Monitor Mode)
- Only logs malicious traffic
- Does NOT block requests
- Used for testing and tuning rules

---

### ✔ Prevention Mode (Block Mode)
- Actively blocks malicious requests
- Provides real protection
- Used in production environments

---

## 🔥 Best Practice (Real DevOps Maturity)

A mature setup includes:
- Combination of **Managed + Custom rules**
- Running in **Prevention mode**
- Continuous monitoring and tuning

---

## 💬 Interview-Ready Answer

❌ Avoid saying:
> “We are using WAF”

✅ Say this instead:
> “We are using Managed and Custom WAF rules in Prevention mode to protect against both common and application-specific threats.”

---

## 🧠 Key Takeaway
WAF is not just about enabling security —
It’s about:
- Choosing the right rule types
- Configuring modes correctly
- Continuously improving protection
