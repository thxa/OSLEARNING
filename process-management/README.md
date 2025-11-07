# Process Management
# 🚀 macOS Process Management: A Learning Journey

This repository documents my deep dive into the internals of process management on macOS, based on the book **"Mac OS X and iOS Internals: To the Apple's Core"** by Jonathan Levin.

## 🎯 Goal

The objective is to build a comprehensive, step-by-step understanding of how macOS (XNU) handles processes, from the foundational architecture to modern-day implementation.

## 🗺️ Learning Roadmap

This repo follows a structured learning path. Completed steps are documented and linked.

1.  **[Step 1: The "Big Picture" (Architecture)](STEP_1_ARCHITECTURE.md)**
    * **Reference:** Chapter 2
    * **Concepts:** Darwin, XNU, Mach vs. BSD, The "Two Souls" Concept.

2.  **[Step 2: The User-Mode View of a Process](STEP_2_USER_MODE_PROCESS.md)**
    * **Reference:** Chapter 4
    * **Concepts:** Process Lifecycle (SIDL, SRUN, SSLEEP, SZOMB), UNIX Signals, Process Address Space (ASLR, PAGEZERO), Threads.

3.  **Step 3: Kernel Deep Dive (Upcoming)**
    * **Reference:** Chapters 10, 11, 13
    * **Concepts:** Mach Tasks, Mach Threads, BSD `proc` struct, `fork()`, `exec()`.

4.  **Step 4: The Manager (Upcoming)**
    * **Reference:** Chapter 7
    * **Concepts:** `launchd` (PID 1), Daemons & Agents, XPC Services.

5.  **Step 5: The Rules (Security & IPC) (Upcoming)**
    * **Reference:** Chapters 3, 10
    * **Concepts:** App Sandbox, Entitlements, Mach Ports, IPC.

6.  **Step 6: The Tools (Upcoming)**
    * **Reference:** Chapter 5
    * **Concepts:** `ps`, `top`, `vmmap`, `otool`, `dtrace`.
