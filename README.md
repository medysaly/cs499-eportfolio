# Mehdi Salhi
**Cloud / AI Engineer — CS-499 Computer Science Capstone ePortfolio**

Welcome. This ePortfolio documents my capstone for the Bachelor of Science in Computer Science at Southern New Hampshire University. It opens with a professional self-assessment, then presents my informal code review and three enhancements made to a single artifact, Unkommon, across software design and engineering, algorithms and data structures, and databases.

## Contents
- [Professional Self-Assessment](#professional-self-assessment)
- [Code Review](#code-review)
- [The Artifact: Unkommon](#the-artifact-unkommon)
- [Enhancement One: Software Design and Engineering](#enhancement-one-software-design-and-engineering)
- [Enhancement Two: Algorithms and Data Structures](#enhancement-two-algorithms-and-data-structures)
- [Enhancement Three: Databases](#enhancement-three-databases)
- [Contact](#contact)

---

## Professional Self-Assessment

I came to computer science as a career changer, drawn by the chance to build systems that solve real problems. Over the Computer Science program at SNHU I moved from broad curiosity to a clear specialization: cloud engineering on Amazon Web Services, with artificial intelligence as a capability I build on top of it. The work in this ePortfolio, and the project behind it, is the evidence for that focus.

The clearest example of my growth is Unkommon, a serverless AI platform I designed and built on AWS. It runs a website chatbot on a large language model through Amazon Bedrock and books real appointments, and it is the single artifact I enhanced across all three capstone categories. Choosing one real, working system instead of three separate exercises let me show depth: how decisions in software design, algorithms, and data modeling interact inside one production codebase.

**Collaborating in a team environment.** This is the area I have grown in most deliberately, since much of my recent work has been solo. In CS-250 Software Development Lifecycle I learned and applied Agile and Scrum practices, sprint planning, team roles, and sprint reviews, which gave me the structure and vocabulary of working on a development team. My code review for this capstone was addressed to the peers-and-manager audience the assignment specified, which made me practice explaining technical decisions to people who did not write the code. The people-facing work I did before computer science taught me to coordinate with a team under real-time pressure, and I bring that habit of clear, direct communication to technical teams. Even on solo projects, I treat code review and documentation as the heart of collaboration, and I write as if a teammate will inherit the code.

**Communicating with stakeholders.** Building Unkommon meant thinking like the businesses it serves. The chatbot represents a company to its customers, so I designed its behavior, tone, and guardrails around what a real client would want and what their customers would actually ask. Translating fuzzy business needs into concrete technical requirements, and then explaining technical tradeoffs back in plain language, is a skill I started building in CS-250 Software Development Lifecycle, where turning requirements into a design plan was central, and one I apply directly in my own product today.

**Data structures and algorithms.** My Algorithms enhancement added a Trie-based intent classifier that answers common questions before the system ever calls the language model. I chose the data structure to fit the access pattern, analyzed its O(m) time complexity, and measured the outcome: common questions are answered in microseconds instead of hundreds of milliseconds, at no per-call cost. Built on the foundation from CS-300 Data Structures and Algorithms, that work shows I can select and justify a data structure against real constraints, not just implement one.

**Software engineering and databases.** My Software Design enhancement refactored a single large function into a layered architecture with automated tests and a continuous integration pipeline, which made the codebase maintainable and safe to change. My Databases enhancement redesigned the data layer on DynamoDB, adding tables and secondary indexes built around the questions the application actually needs to answer. Together they show that I can both structure software professionally and model data deliberately, drawing on testing practice from CS-320 Software Testing and the NoSQL database work from CS-340 Client/Server Development.

**Security.** Security was a thread through every enhancement, not a separate step. Unkommon enforces request rate limiting, prompt-injection guards, and webhook verification, and in my Databases enhancement I applied least-privilege IAM so each function can reach only the resources it needs. Grounded in the software-security principles from CS-305 Software Security, I design with the assumption that any input can be hostile and any access should be the minimum required.

**How the artifacts fit together.** The three enhancements are not separate projects. They are three views of the same production system: the software design work makes it maintainable, the algorithms work makes it fast and inexpensive, and the database work makes its data durable and queryable. Read together, they show that I can take a real cloud application from an idea to a tested, secure, and observable system on AWS, which is exactly the work I want to do as a cloud engineer. I hold the AWS Certified Cloud Practitioner credential and am completing the AWS Certified Solutions Architect Associate to formalize that path. The technical artifacts and their narratives follow this introduction.

---

## Code Review

Before making any changes, I recorded an informal code review of the original artifact: a walkthrough of how the code worked, the weaknesses I found, and the enhancements I planned across the three categories.

[![Watch the code review](https://img.youtube.com/vi/zc1jV-mlyek/0.jpg)](https://www.youtube.com/watch?v=zc1jV-mlyek)

[Watch the code review on YouTube](https://www.youtube.com/watch?v=zc1jV-mlyek)

---

## The Artifact: Unkommon

Unkommon is a serverless AI platform I built on AWS. It runs a website chatbot on a large language model through Amazon Bedrock, with tool-calling that checks a calendar and books appointments, conversation history in DynamoDB, and a security layer (WAF rate limiting, prompt-injection guards, webhook verification). I enhanced this one artifact across all three capstone categories, so the before-and-after is visible in one real codebase.

- **Original artifact (before enhancements):** [github.com/medysaly/unkommon (original-artifact tag)](https://github.com/medysaly/unkommon/tree/original-artifact)
- **Enhanced artifact (current):** [github.com/medysaly/unkommon](https://github.com/medysaly/unkommon)

---

## Enhancement One: Software Design and Engineering

**The artifact.** The Unkommon chatbot began as a single Lambda file of roughly 590 lines that handled HTTP parsing, security checks, the LLM tool-calling loop, calendar calls, and email all in one place.

**The enhancement.** I refactored it into a layered architecture with clear responsibilities: a handler layer (HTTP and guards), a service layer (the chat logic), a domain layer (the system prompt and tools), and a data layer (DynamoDB, Bedrock, calendar, and email wrappers). I added a pytest suite that mocks every external dependency and a GitHub Actions workflow that runs the tests on every push. During a request, the service layer orchestrates the flow and calls the data-layer wrappers for each external system, so the business logic never touches boto3 or HTTP directly.

**Skills and outcomes.** This work demonstrates separation of concerns, testability, and automated quality gates, and it maps to Outcome 4 (well-founded, innovative tools) and Outcome 2 (professional communication, through the accompanying narrative and code review). A deliberate tradeoff: I chose a layered design over keeping a single file because it makes the system maintainable and safe to change, at the cost of more files to navigate.

- Original code: [original-artifact tag](https://github.com/medysaly/unkommon/tree/original-artifact)
- Enhanced code: [main branch](https://github.com/medysaly/unkommon)

---

## Enhancement Two: Algorithms and Data Structures

**The artifact.** The same Unkommon chatbot. Every user message, even a simple "where are you based," was sent to the language model, paying roughly 200 to 400 milliseconds of latency and a per-token cost each time.

**The enhancement.** I added an intent-classification layer that runs before the Bedrock call, using a Trie (prefix tree). Common FAQ phrasings are matched in O(m) time, where m is the number of words in the message, and answered from a precomputed, deterministic response. Only messages that do not match fall through to the language model. I measured the result with a benchmark: the Trie lookup averages about 1.6 microseconds versus roughly 300 milliseconds for a model call, and on a representative sample it handled over half the messages, cutting average latency and per-conversation cost by the same share. (The Trie time is a real measurement; the Bedrock latency and cost figures are documented assumptions.)

**Skills and outcomes.** This demonstrates access-pattern-driven data structure selection, time-complexity analysis, and performance measurement, mapping to Outcome 3 (algorithmic principles and tradeoffs) and Outcome 4 (industry value through measurable latency and cost savings). The honest tradeoff: prefix matching is fast and deterministic but only catches phrasings that start with a known pattern, so it trades some flexibility for speed and predictability.

- Original code: [original-artifact tag](https://github.com/medysaly/unkommon/tree/original-artifact)
- Enhanced code: [main branch](https://github.com/medysaly/unkommon)

---

## Enhancement Three: Databases

**The artifact.** The same Unkommon chatbot. It used one DynamoDB table for conversation history; captured leads were only emailed, and appointments were only written to Google Calendar, so neither was stored in a way the business could query.

**The enhancement.** I redesigned the data layer on DynamoDB. I added leads and appointments tables with Global Secondary Indexes built around the questions the application actually needs to answer: recent leads by date, appointments for a given customer, and appointments in a date range. Each write is now persisted through repository modules, and I applied least-privilege IAM so the function can reach only its own tables. I also designed an analytics layer (DynamoDB Streams to S3 to Athena) for historical reporting and documented it; I did not deploy it, because at the project's current scale it is more than the product needs, and I would rather build it when there is real data volume to justify it.

**Skills and outcomes.** This demonstrates NoSQL data modeling, index design around access patterns, and a security mindset, mapping to Outcome 3 (design tradeoffs), Outcome 4 (innovative tools), and Outcome 5 (least-privilege security). The tradeoff I document: I chose a multi-table design over single-table design for clarity and maintainability at this scale.

- Original code: [original-artifact tag](https://github.com/medysaly/unkommon/tree/original-artifact)
- Enhanced code: [main branch](https://github.com/medysaly/unkommon)

---

## Contact

- **GitHub:** [github.com/medysaly](https://github.com/medysaly)
- **LinkedIn:** [linkedin.com/in/mehdi-salhi-work](https://www.linkedin.com/in/mehdi-salhi-work)
- **Website:** [mehdisalhi.com](https://www.mehdisalhi.com)
