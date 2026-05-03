🧠 Employee & Social Media Recon Cheat Sheet

>[!info] 👥 Why Employees Matter

💡 Employees reveal:

Technologies used internally
Programming languages & frameworks
Security tools & practices
Company structure & teams

🎯 You’re mapping people → tech stack → attack surface

🌐 Finding Employees

>[!tip] 🔍 Where to Search

LinkedIn
Xing
Company websites (About/Team pages)

💡 Use filters: company name, role, location, skills

>[!success] 🎯 What to Look For

Developers (backend, frontend)
DevOps engineers
Security engineers
IT administrators

💡 These roles expose core infrastructure details

💼 Job Post Intelligence

>[!info] 📄 What Job Posts Reveal

💡 From a single job post, you can extract:

Languages → Java, Python, C#, PHP
Databases → MySQL, PostgreSQL, Oracle
Frameworks → Django, Flask, Spring, ASP.NET
Dev practices → CI/CD, Agile

🎯 This = blueprint of the backend

>[!tip] 🧠 Hidden Insights

ORM tools → Hibernate, SQLAlchemy
APIs → REST architecture
Version control → Git, SVN
Containers → Docker, Kubernetes

💡 These tell you:

👉 How apps are built
👉 How they are deployed
👉 Where weaknesses may exist

🧑‍💻 Employee Profiles

>[!success] 🔎 What Profiles Reveal

💡 Example insights:

Frontend → React, Angular, Svelte
Backend → Node.js, Django
Standards → W3C, APIs
Public repos → GitHub links

🎯 You now know exact technologies in use

>[!warning] ⚠️ Common Mistakes by Employees

Hardcoded secrets (API keys, JWTs)
Personal emails exposed
Public repositories with sensitive data

💡 These can lead to:

👉 Credential leaks
👉 Unauthorized access
👉 Full system compromise

🧪 GitHub & Code Leakage

>[!danger] 💥 What to Hunt For

API keys
JWT tokens
Database credentials
Private endpoints

💡 Example risk:

Hardcoded JWT → Direct API access

🎯 This can bypass authentication entirely

>[!tip] 🔍 Learn From Their Stack

💡 If company uses Django:

Study Django vulnerabilities
Check OWASP Top 10 for Django

👉 Same applies to any framework found

🧩 Connecting the Dots

>[!success] 🧠 Build the Bigger Picture

💡 Combine:

Job posts
Employee profiles
GitHub projects

👉 To map:

PEOPLE → SKILLS → TECHNOLOGY → TARGETS
🎯 Recon Strategy

>[!tip] 🔎 Smart Approach

Focus on technical staff
Prioritize dev + security roles
Analyze skills + tools

💡 This helps you predict:

👉 Infrastructure
👉 Security controls
👉 Possible vulnerabilities

⚡ Real-World Mindset

>[!quote] 🎯 Think Like This

EMPLOYEES → TECH STACK → MISCONFIGURATIONS → ENTRY POINT

>[!danger] 💥 Key Takeaway

💡 Companies don’t just leak data through systems…

👉 They leak through people