# FreeRTOS-Incremental-Encoder

Project developed for my **Real-Time Systems Design and Development** course.
This project is a FreeRTOS-based porting of an incremental encoder laboratory assignment (originally implemented in RT-POSIX). It simulates an incremental encoder signal and processes it through multiple cooperating periodic tasks to extract physical metrics (such as rising edges and RPM) while monitoring scheduling performance and slack times.

---

## 🏗️ System Architecture

The application is structured into **5 periodic tasks** managed by the FreeRTOS kernel, synchronized using mutexes and scheduled according to their relative priorities:

1. **`enc` (Encoder Task - 5 ms period, Priority 5)**: Simulates an incremental encoder generating a noisy square wave (`slit`) and a reference home signal (`home_slit`) with randomized speed.
2. **`rt_task1` (Rising Edge Task - 2.5 ms period, Priority 6)**: Samples the encoder signal to count rising edges and computes the execution slack time.
3. **`rt_task2` (Round Time Task - 2.5 ms period, Priority 6)**: Detects home-slit transitions to measure elapsed time between consecutive full rotations and monitors slack time.
4. **`scope` (Scope Task - 10 ms period, Priority 2)**: Periodically reads edge counts and rotation intervals to compute and log current **RPM**.
5. **`diagnostic` (Diagnostic Task - 500 ms period, Priority 4)**: Aggregates and averages task slack times over multiple rounds to evaluate system timing performance and print diagnostic statistics.

---

## ⚙️ Synchronization and Concurrency

* **Mutual Exclusion**: Shared data structures (`enc_data`, `rising_edge`, `round_time`, `slack_rt1`, `slack_rt2`) are strictly protected using FreeRTOS Mutex semphores (`SemaphoreHandle_t`) to prevent race conditions.
* **Deterministic Timing**: Uses FreeRTOS delay APIs (`vTaskDelayUntil`) to maintain precise periodic execution for all threads.
* **Deadline & Slack Monitoring**: Measures task completion margins to detect potential deadline misses and calculate average system slack.

## 🚀 Requirements and Execution

### 1. System Prerequisites
* Embedded Linux environment or simulation layer configured with **FreeRTOS** kernel integration (including headers like `FreeRTOS.h`, `task.h`, `timers.h`, `semphr.h`).
* Standard GCC toolchain and makefile setup compatible with the course framework.

### 2. Compilation and Execution
To run the FreeRTOS application, initialize the scheduler inside your main entry point by calling `main_blinky()`, which initializes the synchronization primitives, creates the tasks, and starts the kernel scheduler:

```c
// Example snippet to launch the system
main_blinky();
