# 📘 Programming in Modern C++

## Week 1 – Module 01: Course Overview & Why C++

---

## 🎯 Purpose of This Module

This opening module does **not teach C++ syntax**.
Instead, it sets the **context** for the entire course by answering four big questions:

1. Why C++ still matters today
2. Where C++ fits among programming languages
3. What *modern C++* actually means
4. How this course is structured, evaluated, and supported

Think of this as the **mental map** you need before writing a single line of modern C++.

---

## 🧭 Programming Languages: Evolution & Paradigms

### Timeline Perspective

Programming languages have evolved since the 1950s, not randomly, but in response to **different programming needs**.

Languages can broadly be grouped by **paradigm**:

| Paradigm        | Key Idea                 | Examples  |
| --------------- | ------------------------ | --------- |
| Imperative      | Algorithms + data        | C         |
| Object-Oriented | Data + behavior          | C++, Java |
| Functional      | Everything is a function | Lisp      |
| Logic           | Facts, rules, queries    | Prolog    |

### Key Takeaway

C++ is special because it **does not restrict you to one paradigm**.
It supports:

* Imperative programming (like C)
* Object-Oriented programming
* Functional programming (modern C++)
* Metaprogramming
* Concurrent programming

👉 This flexibility is both **power** and **complexity**.

---

## 🧬 The C Family of Languages

C++ belongs to the **C-family**, which includes:

* C
* C++
* C#
* Objective-C

These languages share **syntactic roots** but differ significantly in **features, abstractions, and use cases**.

> Important nuance:
> Saying *“C++ includes C”* is **philosophically true**, but **technically false** in many real cases.

A C program:

* may **not compile** in C++
* may compile but **behave differently**

This compatibility issue is important and addressed later in the course (via tutorials).

---

## 📊 Why Learn C++? (Popularity & Relevance)

According to **TIOBE Index (Jan 2021)**:

* C, Java, Python, and C++ consistently dominate usage rankings
* C++ usually ranks in the **top 4–5**

### Strategic Insight

Since C++:

* subsumes most of C
* shares concepts with Java
* influences many modern languages

👉 Mastering C++ gives you exposure to **~25% of mainstream programming ecosystems**.

---

## 🧠 One Language ≠ One System

Modern software systems are **multi-language by design**.

Example: Net banking / Gmail-like systems

* Frontend: HTML, CSS, JavaScript
* Business logic: Java / JavaScript
* Database: SQL
* Performance-critical components: C / C++

### Key Lesson

> **Choosing the right language matters more than knowing many languages.**

C++ shines where:

* performance is critical
* control over memory is required
* systems are complex and large-scale

---

## ⚙️ When C++ Is (and Isn’t) the Right Choice

### Best Use Cases

* Operating systems
* Databases (MySQL, MongoDB, Oracle)
* Compilers & interpreters
* Virtual machines
* Browsers
* Graphics engines
* Embedded systems (with care)

### Not Ideal For

* Frontend UI / graphics directly
* Rapid prototyping
* Pure web development

> Use C++ for **engines**, not **interfaces**.

---

## 🧩 Core Strengths of C++

* **High performance** (close to C)
* **Scalable** (small tools → massive systems)
* **Portable**
* **Multiple abstraction levels**
* **Multi-paradigm**
* **Massive ecosystem & libraries**
* **High industry demand & salaries**

⚠️ Trade-off:

* Steeper learning curve
* Slower to master than Python or Java

---

## 📜 Evolution of C and C++ Standards

### C Language

* 1978: K&R C
* 1989/1990: ANSI C / ISO C
* Later versions: C95, C99, C11, C18

> C18 mostly fixes bugs, no major new features.

### C++ Language

* 1998: C++98 (first standard)
* 2003: C++03 (fixes only)
* 2011: **C++11 — the real revolution**
* 2014, 2017, 2020: incremental but powerful additions

### Course Focus

* First ~9 weeks: C++98 / C++03 foundations
* Last ~3 weeks: **Modern C++ (C++11+)**

---

## 🎯 Course Objectives (What You’re Really Expected to Gain)

This course is **not just about syntax**.

### You are expected to learn:

* C++ language features (classic + modern)
* Object-oriented design
* STL (Standard Template Library)
* Concurrency & functional programming in C++
* Writing **efficient, maintainable code**

### Real Objective

> Build **employable software engineering skills**, not just pass exams.

---

## 📌 Prerequisites (Assumed Knowledge)

### Mandatory

* C programming
* Basic data structures
* Basic algorithms

### Helpful (Not Mandatory)

* Object-oriented analysis & design

📎 Week 0 includes **self-study modules** to refresh C fundamentals.

---

## 🧪 Course Structure

* **12 weeks**
* **60 modules** (5 per week)
* Modules labeled: `M01, M02, …`
* Tutorials labeled: `T01, T02, …`

### Tutorials

* **Complementary**: software practices (build systems, Make, libraries)
* **Supplementary**: language extensions, C–C++ interoperability

⚠️ Tutorials are **not part of exams**, but critical for **real skill**.

---

## 📝 Evaluation Scheme (High-Level)

* Weekly quizzes + programming assignments
* Best **8 out of 12** assignments counted (subject to final announcement)
* **Unproctored programming test**
* **Proctored final exam** (MCQs + short answers)

> Certification criteria may change — always follow the **current NPTEL announcement**.

---

## 📚 Textbooks, References & Blogs

### Core Books

* **C**: Kernighan & Ritchie
* **C++**: Bjarne Stroustrup
* **C++ STL**: Lippman et al.

### Blogs (Very Important)

* Bjarne Stroustrup
* Herb Sutter
* Scott Meyers

⚠️ Not all blogs are reliable — follow **language designers**, not random tutorials.

---

## 🛠 Tools & Environment

### Recommended

* **Linux** + GCC + GDB
* **Windows**: MinGW (GNU tools for Windows)

### Online (for quick checks)

* Compiler Explorer
* CodeBlocks
* Programiz
* OneCompiler

⚠️ Online tools are **not a substitute** for a real build environment.

---

## 🔍 Checking Compiler Standard Version

Every compiler embeds a **magic number** indicating the active language standard.

Use the provided code snippet (from the lecture) to:

* identify which C/C++ standard your compiler is using
* avoid accidental version mismatches

---

## 🧠 Things the Video Assumes (But Doesn’t Explain)

* What “Turing complete” actually means
* Why abstraction levels matter in large systems
* Practical implications of multi-paradigm programming
* How C++ metaprogramming works (introduced much later)

These will be **expanded in later modules**, but you should stay aware of them.

---

## 📦 Final Takeaway

This module answers **“Why C++?”**, not **“How C++?”**.

If you understand this module well:

* You won’t misuse C++
* You’ll know when *not* to use C++
* You’ll approach modern C++ with the right mindset

> **Modern C++ is a tool for serious systems, not a beginner toy.**

