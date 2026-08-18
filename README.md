# Linux Process Management & Producer-Consumer Kernel Module

A Linux kernel module implementing a producer-consumer system for monitoring and processing Linux processes using kernel threads, synchronization primitives, and a shared circular buffer.

> **Source Code Notice:** The source code for this project is intentionally not included because it was completed as part of university coursework. This repository documents the project's design, functionality, and results without distributing an assignment solution that could be reused by current students.

## Overview

This project was developed as part of my Operating Systems coursework at Arizona State University.

The project implements the producer-consumer pattern within the Linux kernel. Producer and consumer kernel threads communicate through a shared bounded buffer while synchronization mechanisms coordinate access to shared resources.

The producer identifies processes associated with a specified user and places process information into the shared buffer. Consumer threads remove processes from the buffer and calculate information about their execution time.

The project provided hands-on experience with Linux kernel programming, concurrency, process management, kernel threads, and synchronization.

## Core Functionality

The kernel module supports configurable parameters for:

- Number of producer threads
- Number of consumer threads
- Size of the shared buffer
- User ID used for process filtering

The implementation includes:

- Linux kernel threads for producer and consumer execution
- A bounded circular buffer shared between threads
- Semaphore-based synchronization
- Mutual exclusion for shared resources
- Linux process enumeration and filtering
- Process runtime calculations
- Thread-safe accumulation of processing statistics
- Graceful kernel-thread termination and cleanup

## Architecture

The module follows a traditional producer-consumer architecture:

```text
               Linux Process List
                       |
                       v
                Producer Thread
                       |
                       | Process Information
                       v
              +-------------------+
              |   Shared Circular |
              |       Buffer      |
              +-------------------+
                       |
                 Semaphore-Based
                 Synchronization
                       |
              +--------+--------+
              |        |        |
              v        v        v
          Consumer  Consumer  Consumer
           Thread    Thread    Thread
              |        |        |
              +--------+--------+
                       |
                       v
              Process Runtime Data
```

## Synchronization

Synchronization is a central component of the project.

The producer and consumer threads coordinate access to the bounded buffer using semaphores representing:

- **Empty slots** — tracks available positions in the buffer
- **Filled slots** — tracks entries available for consumers
- **Mutual exclusion** — prevents concurrent modification of shared buffer state

This ensures that producers do not insert data into a full buffer and consumers do not attempt to remove data from an empty buffer.

Additional synchronization protects shared runtime statistics when multiple consumer threads are active.

## Producer Thread

The producer thread examines Linux processes and identifies processes belonging to the configured user.

For each matching process, the producer:

1. Waits for an available position in the shared buffer.
2. Acquires exclusive access to the buffer.
3. Stores the relevant process information.
4. Advances the circular-buffer position.
5. Releases access to the buffer.
6. Signals that a new item is available for a consumer.

This allows process discovery to operate independently from the processing performed by consumer threads.

## Consumer Threads

Consumer threads retrieve process information placed into the shared buffer by the producer.

Each consumer:

1. Waits for an available item.
2. Acquires exclusive access to the shared buffer.
3. Removes the next process entry.
4. Advances the circular-buffer position.
5. Releases the buffer.
6. Signals that another buffer position is available.
7. Calculates the elapsed execution time associated with the process.

Multiple consumer threads can operate concurrently while synchronization prevents corruption of shared state.

## Circular Buffer

The shared buffer is implemented as a bounded circular buffer.

Separate producer and consumer indexes track where items should be inserted and removed. When either index reaches the end of the buffer, it wraps back to the beginning.

This design allows a fixed-size buffer to continuously support communication between producer and consumer kernel threads.

## Testing & Results

The module was tested under multiple configurations to verify its behavior with different workloads and thread configurations.

Testing included configurations involving:

- 10 processes
- 100 processes
- 500 processes
- Different shared-buffer sizes
- Multiple consumer threads

The testing demonstrated that the module could coordinate producer and consumer kernel threads while processing increasing numbers of Linux processes.

Detailed output from the project testing is available here:

**[View Project Output Screenshots](./Project-2-Output-Screenshots.pdf)**

## Technologies & Concepts

- C
- Linux
- Linux Kernel Programming
- Linux Kernel Modules
- Kernel Threads
- Process Management
- Producer-Consumer Pattern
- Concurrency
- Semaphores
- Mutual Exclusion
- Circular Buffers
- Process Enumeration
- Thread Synchronization
- Kernel Memory Management

## What I Learned

This project strengthened my understanding of:

- Creating and managing threads within the Linux kernel
- Applying the producer-consumer pattern to a kernel-level problem
- Coordinating concurrent threads using semaphores
- Protecting shared resources from race conditions
- Designing and managing bounded circular buffers
- Working with Linux process structures
- Filtering and processing operating-system processes
- Calculating process execution information
- Managing shared state across multiple kernel threads
- Safely terminating threads and cleaning up kernel resources

The project also provided practical experience applying operating-systems concepts that are typically studied theoretically, particularly synchronization, concurrency, process management, and resource sharing.

## Academic Project Notice

This project was completed as part of Operating Systems coursework at Arizona State University.

The source code is intentionally excluded to protect the academic integrity of the assignment and prevent the implementation from being reused as a solution by current or future students. The repository is provided solely as a portfolio demonstration of the project's architecture, technical concepts, and successful execution.
