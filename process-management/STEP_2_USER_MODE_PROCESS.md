# Step 2: The User-Mode View of a Process

**Reference:** Chapter 4: Parts of the Process: Mach-O, Process, and Thread Internals

## 📝 Summary

After understanding the underlying architecture (Mach + BSD), this step examines the "process" as an entity from the user-mode perspective: its definitions, its lifecycle, and its "home" in memory.

### 1. Core Definitions (p. 91)
* **Process:** The "container" or "house." It is an instance of an executing program that *owns* resources (memory, file descriptors).
* **Thread:** The "worker" inside the house. It is the *actual* unit of execution. A process can have one or more threads, all sharing the same resources.

### 2. The Process Lifecycle (p. 92)

A process isn't just "on" or "off." It has a complex lifecycle (see Figure 4-1):



* **SIDL (Initializing):** The "newborn" state. The process is being set up by the kernel after a `fork()` but hasn't run yet.
* **SRUN (Running/Runnable):**
    * **Running:** *Actually* executing on a CPU core right now.
    * **Runnable:** Ready to execute, but waiting in a queue for its turn on the CPU.
* **SSLEEP (Sleeping):** Voluntarily "asleep" because it's waiting for a resource (e.g., waiting for a network packet or a disk read).
* **SSTOP (Stopped):** Forcibly "frozen" by an external signal (like `SIGSTOP` or `Ctrl+Z`). It's in a deep sleep and can only be woken by `SIGCONT`.
* **SZOMB (Zombie):**
    * This is a **dead** process. It has finished executing and has freed all its resources.
    * It remains as an "empty shell" (just its `proc` struct) because its "meaning of life" is to return an exit code to its parent.
    * It stays a zombie until its parent process "collects" this exit code by calling `wait()`. If the parent never calls `wait()`, it remains a zombie.

### 3. UNIX Signals (p. 95)
Signals are simple "software interrupts" sent to a process to notify it of an event (see Table 4-1).

* **`SIGSEGV` (Signal 11):** "Segmentation Fault." Sent when the process tries to access memory it doesn't own (like a `NULL` pointer).
* **`SIGKILL` (Signal 9):** The "9mm bullet." Kills the process immediately. It cannot be caught or ignored.
* **`SIGTERM` (Signal 15):** The "polite" request to terminate. The process can "catch" this signal to clean up its files before exiting.
* **`SIGCHLD` (Signal 20):** "Child death." Sent to a parent process when one of its children exits, signaling the parent to call `wait()` (and prevent a zombie).

### 4. Process Address Space (p. 130)
This is the "layout" of the process's "house" (its private virtual memory).

* **ASLR (Address Space Layout Randomization) (p. 131):**
    * A critical security feature. In the past, the layout was predictable, which was a "boon for hackers."
    * ASLR "shuffles" or "slides" the memory addresses (for `TEXT`, `DATA`, etc.) randomly every time the program runs. This makes it much harder for an attacker to guess where to inject malicious code.

* **General Layout (p. 133-134):**
    * **`PAGEZERO`:** The most important security feature. This is a massive, forbidden "moat" at the very beginning of the address space (the first 4GB in 64-bit systems).
    * Its permissions are `---/---` (no read, no write, no execute).
    * It acts as a "trap" for `NULL` pointer bugs. If a program tries to use a `NULL` pointer (address `0x0`), it falls into this "moat," triggering a hardware fault (`SIGBUS`) that kills the process. This prevents the bug from being exploited.
    * **`TEXT`:** The program's actual code. It's marked read-only and execute-only (`r-x`).
    * **`DATA`:** The program's global variables. It's marked read/write (`rw-`).

### 5. Threads (p. 143)
This section revisits *why* threads are essential.

* **The Problem:** The old single-process model was "rigid." When a process made a blocking I/O call (like `read()` from the network), the *entire process* would freeze.
* **The Solution:** Threads "maximize the process timeslice." If **Thread A** is sleeping (`SSLEEP`) while waiting for a file, **Thread B** (in the same process) can continue running and keep the UI responsive.
* **Context Switching:**
    * **Thread Switch (cheap):** Just save the CPU registers.
    * **Process Switch (expensive):** Must save registers *and* swap out the entire virtual memory map (flushing the TLB, etc.), which is much slower.
