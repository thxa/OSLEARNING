# Step 1: The "Big Picture" (Architecture)

**Reference:** Chapter 2: E Pluribus Unum: Architecture of OS X and iOS

## 📝 Summary

This step is the essential foundation. It explains the "playing field" on which processes run. The core concept is that the macOS kernel, **XNU**, is a **hybrid** kernel, combining two different philosophies. This is the "Two Souls" concept.



### 1. Darwin - The UNIX Core (p. 22)
* **Darwin** is the core open-source operating system that lives beneath the graphical UI of macOS.
* It is a **"full-fledged UNIX implementation,"** which is why macOS is officially UNIX-certified and you can use standard commands like `ls` and `ps`.

### 2. The XNU Kernel (p. 51)
* **XNU** is the name of the kernel within Darwin.
* It is a **hybrid** kernel, meaning it's a mix of different kernel designs. It's composed of two main parts:
    1.  **The Mach Microkernel**
    2.  **The BSD Layer**

### 3. The "Two Souls": Mach vs. BSD (p. 51)

This is the most critical concept to grasp. XNU gives "two souls" to every process.

#### a. Mach (The "Engine Room" or Core)
Mach is the low-level microkernel foundation. It only handles the most primitive, abstract concepts:

* **Tasks:** A `Task` is simply a container for resources (like virtual memory). It is *not* a process.
* **Threads:** This is the *actual* unit of execution that gets scheduled on the CPU.
* **Virtual Memory:** Manages the process's address space.
* **IPC (Inter-Process Communication):** Provides the basic message-passing system (`Mach Ports`).

Mach knows *nothing* about "users," "permissions," or "files."

#### b. The BSD Layer (The "Personality" or Management)
The BSD layer (from FreeBSD) sits *on top* of Mach and provides the familiar **UNIX personality**.

* **Process:** BSD provides the high-level "process" concept, assigning it a **PID**.
* **Users & Permissions:** Manages User IDs (UID), Group IDs (GID), and rules.
* **Filesystem:** Manages files, directories, and VFS.
* **Networking:** Manages the socket API and network stack.

### 💡 The Main Takeaway
When a process is created in macOS:
1.  **BSD** creates a `proc` structure to hold the "identity" (like PID, UID).
2.  **BSD** then asks **Mach** to create a `Task` (resource container) and at least one `Thread` (for execution).

Therefore, a macOS **Process** is a combination of:
**`proc` (BSD identity) + `Task` (Mach resources) + `Thread` (Mach execution)**
