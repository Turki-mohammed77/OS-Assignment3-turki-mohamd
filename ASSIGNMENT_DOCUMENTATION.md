# Assignment 3 - Complete Documentation

**Student Name**: [turki mohammed alshabanat]  
**Student ID**: [445050223]  
**Date Submitted**: [4/29/2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/14v2C5vgDrx0EvpQw01LP3f0_8XwXqyAE/view?usp=drivesdk

**Video filename**: `[445050223]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [25 April 2026 04:00 PM]
**What I implemented**: 

**Challenges encountered**: Setup the Process class and basic variables

**How I solved it**: Tracking process progress

**Testing approach**: Used simple math to print a visual progress bar

**Time spent**: 1 hour

---

### Entry 2 - [26 April 2026 05:30 PM]
**What I implemented**: Added multithreading to simulate concurrent processes

**Challenges encountered**: Output was messy and counters were inaccurate

**How I solved it**:  Realized I needed synchronization to fix race conditions

**Testing approach**: Ran multiple dummy threads to check if counter values were lost

**Time spent**: 1 hour

---

### Entry 3 - [27 April 2026 08:00 PM]
**What I implemented**:  Added ReentrantLock for counters and a Semaphore

**Challenges encountered**: Forgetting to release locks, causing threads to freeze

**How I solved it**: Wrapped all critical sections in try-finally blocks

**Testing approach**: Forced an exception in a thread to check if locks were still safely released

**Time spent**: 1 hour

---

### Entry 4 - [28 April 2026, 03:00 PM]
**What I implemented**: CPU yielding logic using cpuSemaphore.acquire

**Challenges encountered**: Calculating the waiting time correctly after multiple pauses

**How I solved it**:  Fixed the formula: (completionTime - creationTime) - burstTime

**Testing approach**: Ran 5 processes and manually calculated waiting time to verify the code's output

**Time spent**: 1.5 hour

---

### Entry 5 - [[29 April 2026, 01:00 AM]
**What I implemented**: Formatted the statistics summary table

**Challenges encountered**: Aligning the table columns with multiple threads printing

**How I solved it**:  Used String.format() for clean spacing and ANSI colors

**Testing approach**: *Testing approach:* Full system test with 20 processes to ensure smooth execution and proper termination

**Time spent**: 1.5 hour


---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[ 1. *contextSwitchCount++:* If two threads increment this at the same time, one count gets lost because ++ is not an atomic operation.
 2. *executionLog.add(message):* ArrayList is not thread-safe. Concurrent additions can corrupt the list or cause an ArrayIndexOutOfBoundsException
one count gets lost because ++ is not an atomic operation]
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[A ReentrantLock allows only one thread at a time (mutual exclusion); I used it to protect my shared counters. A Semaphore manages a set number of permits; I used it for the CPU (cpuSemaphore with 1 permit) to control how many processes can run simultaneously]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock is when threads are stuck forever waiting for each other. I prevented it by:
 1. Always using try-finally to ensure locks are released even if an error occurs.
 2. Avoiding nested locks (threads only hold one lock at a time)]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[
I chose Fine-Grained Locking by creating separate locks for each resource (contextSwitchLock, logLock, etc.). This is better because these counters are 

independent. Using one "Big Lock" (Coarse-Grained) would mean a thread updating the log would block another thread just trying to increment a context switch, 

which slows down the simulation. Fine-grained locking allows higher concurrency because threads only block each other if they are touching the exact same variable.
]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount

**Why they need protection**: To prevent lost updates during concurrent increments

**Synchronization mechanism used**:  ReentrantLock (contextSwitchLock

**Code snippet**:
```java
  contextSwitchLock.lock();
   try { contextSwitchCount++; } 
   finally { contextSwitchLock.unlock(); }
```

**Justification**: Ensures accurate counting by allowing only one thread to increment at a time

---

### Critical Section #2: Execution Log

**What resource**: executionLog (ArrayList)

**Why it needs protection**:  Standard ArrayList crashes with concurrent modifications

**Synchronization mechanism used**: ReentrantLock (logLock)

**Code snippet**:
```java
logLock.lock();
   try { executionLog.add(message); } 
   finally { logLock.unlock(); }
```

**Justification**: 
Prevents data corruption during concurrent writes to the list
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: To limit CPU access

**Number of permits and why**: 1, because we are simulating a single-core CPU

**Where implemented**: Inside 

**Code snippet**:
`   java
   SharedResources.cpuSemaphore.acquire();
   try { /* execution logic */ } 
   finally { SharedResources.cpuSemaphore.release(); }
```

**Effect on program behavior**: 
Queues threads so they use the CPU one by one
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running the program multiple times

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
Consistent outputs and matching final numbers every time
**Why synchronization is necessary**: 
Without it, numbers would randomly change every run due to race conditions
**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Forced an error inside a thread to check locks

**Testing procedure**: Inserted a temporary throw new RuntimeException() inside a locked section

**Results**: The lock was still released, and other threads continued normally

**What this proves**: The try-finally implementation successfully prevents deadlocks

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values

**Expected values**: Number of completed processes should equal generated processe

**Actual values**: Number of completed processes should equal generated processes

**Analysis**: The completedProcessLock accurately tracked thread completions without dropping counts

---

### Test 4: Different Scenarios
**Scenario tested**: Simulating a large number of processes (e.g. 50)

**Purpose**: Stress test the locks

**Results**: Ran smoothly without freezing or thread starvation

**What I learned**: Fine-grained locks handle high concurrency traffic efficiently.
 Part 5: Reflection and Learning

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

multithreading is easy to write but hard to make safe. I learned the critical difference between Locks (for mutual exclusion) and Semaphores (for resource pooling). Using fine-grained locks improves performance but requires careful implementation using try-finally to avoid breaking the system entirely
---

### Real-world applications:

Example 1:* *Banking Systems:* Preventing double-spending or lost funds during simultaneous balance transfers.
 * *Example 2:* *Database Connection Pools:* Managing connection limits using a Semaphore so the database server doesn't crash from too many concurrent queries.

---

### How I would explain synchronization to others:

Imagine a single public restroom with one key. Synchronization is the rule that you must take the key (the Lock) to enter. This prevents multiple people from entering at the same time and causing chaos (a Race Condition
---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Turki-mohammed77/OS-Assignment3-turki-mohamd.git

**Number of commits**: 16

**Commit messages**: 
1. p2
2. p5,4
3. edit
4. t2

---

## Summary

**Total time spent on assignment**: 5.5 hours

**Key takeaways**: 
1. ArrayList is not thread-safe and must be locked
2. Always use try-finally blocks
3. Fine-grained locks are faster but require more code

**Most challenging aspect**: 

**What I'm most proud of**: 

Making the simulation 100% thread safe with a clean console UI
End of Documentation

**End of Documentation**
