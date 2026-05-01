# Assignment 3 - Complete Documentation

**Student Name**: [Abdulmajeed Abdu Asiri]  
**Student ID**: [445050104]  
**Date Submitted**: [1 May]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/1Pm0dZek4ua14Kq_oONA21cDX-EN_6AN-/view?usp=sharing (qualilty may be bad at first but gets better later)

**Video filename**: `445050104_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Task 1: implementing a mutex locks for counters - [30 April, 4:10 PM]
**What I implemented**: added a mutex lock to the shared counters 

**Challenges encountered**: choosing wether to add 1 lock for all counters or multiple locks one for each counter

**How I solved it**: choose to add a lock for each counter to improve code performance

**Testing approach**: running the code multiple times to see if i get a consistent results

**Time spent**: 15 minutes

---

### Task 2: Implementing a mutex lock for execution log - [30 April, 4:40 PM]
**What I implemented**:  Implemented a mutex lock to limit access and protect execution log

**Challenges encountered**: at first, i was thinking about reusing a lock for execution log

**How I solved it**: decided to use a seperate lock for execution log for better performance

**Testing approach**: running the code multiple times to check for consistent Total log entries and making sure that no exceptions happen

**Time spent**: 20 minutes

---

### Task 3: Implementing a semaphore for CPU control - [1 May, 3:00 PM]
**What I implemented**: Implemented a  binary semaphore for cpu control with the value 1

**Challenges encountered**: the general structure of implementing the semaphore

**How I solved it**: used a try finally block in order to lock and release the semaphore in order to provide mutual exclusion on CPU control

**Testing approach**: observing the terminal while executing the code multiple times 

**Time spent**: 30 minutes

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[Your answer here - 4-6 sentences with code examples]
the first race condition is involved with the variables contextSwitchCounter, completedProcessCount, and totalWaitingTime. Concurrent access means that 2 processes could try changing these values at the exact same time, which means when doing operations such as contextSwitchCount++ the same time only 1 would go through while the other would be lost.

The second race condition in involved with the arrayList executionLog, concurrent access to it leads to multiple processes trying to add an entry to the log at same time which causes the system to output the wrong number of log entries.
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

the ReentrantLock is used for providing a mutual exclusion to protect data from being accessed by multiple processes at the same time, while the binary semaphore is similar it is used for resource management. I used the ReentrantLock to protect the shared variables: contextSwitchCount, completedProcessCount, etc, while i used the semaphore to protect a CPU control because it is a resource therefore it needs to be managed.

[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

A deadlock happens when a process A is asking for a resource that is being held by process B and process asks for a resource that is held by process A creating a cycle. there is two ways to deal with deadlock either prevention, i used Reentrant locks to control access for each of the shared variables, and binary semaphores to control CPU time by using them inside a try-finally block to ensure that the lock is released even if an exception happens

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

I used separate locks for each countere(fine-grained) approach. I used this approach to get a better performance and faster execution time. The good thing about using a single lock for all counters is that it is much simpler and less  code to write, but that makes it have a worse performance compared the fine-grained approach that give much better performance, And since the three counters are independent then the fine-grained approach provides better concurrency therefore better performance, and the reason for that each counter could changed without having to wait for each other, if they were dependant then a coarse-grained approach should be used in order to give the correct result.

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: because they increment operation (contextSwitchCount++) which is not atomic and need to protected to not get intrrupted and to avoid incorrect output

**Synchronization mechanism used**: a fine-grained Reentrant locks one for each counter

**Code snippet**:
```java
// Paste your implementation here
 public static final ReentrantLock contextSwitchLock = new ReentrantLock();  // Lock for protecting shared resources
    public static final ReentrantLock completedProcessLock = new ReentrantLock();  // Lock for protecting shared resources
    public static final ReentrantLock totalWaitingTimeLock = new ReentrantLock();  // Lock for protecting shared resources

    public static void incrementContextSwitch() {
        // TODO: Protect this critical section with a lock
        // RACE CONDITION: Multiple threads might read and write simultaneously!
        contextSwitchLock.lock();
        try {
            contextSwitchCount++;
        } finally {
            contextSwitchLock.unlock();
        }
    }
```

**Justification**: used a separate lock for each counter because they are independant variables which gives better performance

---

### Critical Section #2: Execution Log

**What resource**: The arrayList named logLock

**Why it needs protection**: in order to avoid processes attemting to add entries into the log at the same time 

**Synchronization mechanism used**: used a separate Reentrant lock in order to protect it 

**Code snippet**:
```java
// Paste your implementation here
 public static final ReentrantLock logLock = new ReentrantLock();  // Lock for protecting execution log
       public static void logExecution(String message) {
        // TODO: Protect this critical section with a lock
        // RACE CONDITION: ArrayList is not thread-safe!
        logLock.lock();
        try {
            executionLog.add(message);
        } finally {
            logLock.unlock();
        }
    }


```

**Justification**: protected to ensure that the arrayList provides a correct value 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: to limit the amount of processes that are able to use the CPU at the same time 

**Number of permits and why**: the number used 1 because we want only one thread to execute on the CPU at a certain time 

**Where implemented**: inside the run() and runToCompletion() methods 

**Code snippet**:
```java
// Paste your implementation here
     try {
            SharedResources.cpuSemaphore.acquire();
            try {
                //execution 
            } finally {
            // TODO #4: Release CPU semaphore here
            // Always release in finally block to prevent deadlocks!
            SharedResources.cpuSemaphore.release();
        }
    
        catch(InterruptedException e) {
            e.printStackTrace();
        }
    }
            
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**:checking for consistency

**Testing procedure**: running the program at least 5 times 
```bash
# Commands used (run the program at least 5 times)
    javac SchedulerSimulationSync.java
    java SchedulerSimulationSync
    java SchedulerSimulationSync
    java SchedulerSimulationSync
    java SchedulerSimulationSync
    java SchedulerSimulationSync
    
```

**Results**:
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 

race conditions could occur in the shared variables such contextSwitchCount, completedProcessesCount, etc, which is when two or more processes access them at the same time and the result will differ based on timing so they need protection to get a correct result
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: The implementation of the locks and the semaphore made sure that the results are consistent

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: running the program multiple times to see if i get an exception

**Results**: ran the program multiple time all went with no exceptions

**What this proves**: this proves that the locks for the arrayList executionLog is working correctly

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: number of completed processes: 15
                     number of context switches: 34
                     number of log entries: 68 
**Actual values**: number of completed processes: 15
                     number of context switches: 34
                     number of log entries: 68 

**Analysis**: the entire program finished correctly with no deadlocks or syncronization problems

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.] added more processes less time quantum

**Purpose**: to see if different inputs would give consistent results

**Results**: Total Context Switches: 36
             Total Completed Processes: 20
             Total log entries: 72
**What I learned**: the locks and semaphore worked correctly with no problems

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]
I learned that syncronization is essential practice to use in every program, syncronization helps with managing and accessing shared resources in order to produce correct output, without it would be difficult and hard to manage resources without impacting the performance of the program 

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: a shoping system like amazon for example when there is a product and there is only 1 piece in stock of that product, when 2 people try to buy that product at the same time, thats where synchronization comes in preventing both people to buy at the same time resulting in an overpurchase so we need a synchronization to prevent that

**Example 2**: a banking system when i try to do two transactions at the same time with the same account but on different devices, without transactions like two withdrawls the same time would cause the system to show incorrect balance, this is where we need synchronization to prevent the user from doing two transactions at the same time

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]
 i would explain it like it is gate keeper that allows only person to enter and the others wait while the first person is inside and when he leaves the second person then can enter, like that

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Abdulmajeed-Asiri-CS/OS-Assignment3-Abdulmajeed-Asiri

**Number of commits**: 5

**Commit messages**: 
1. Set My student ID to :445050104
2. Task 1 445050104: Added ReetrnatLock for counter Protection
3. Task 2 445050104: Added ReentrantLock for execution log
4. Task 4 445050104: Implemented semaphore for CPU control 
5. Completed the Assignment Documentation

---

## Summary

**Total time spent on assignment**: 8 hours

**Key takeaways**: 
1. using different locks for different shared variables if they are independant
2. using the try-finally blocks to lock and release the locks 
3. using binary semaphore vs numbered semaphore

**Most challenging aspect**: documenting the code took a bit of time 

**What I'm most proud of**: the additions made to the original code

---

**End of Documentation**
