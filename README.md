# Real-Time Tasks Writing to a Linux Device Driver

A Linux kernel module that implements a character device driver, and a real-time application whose periodic and aperiodic tasks write their execution trace to the device.

> Second assignment of the Real-Time Operating Systems course (1st year, 1st semester), MSc Robotics Engineering, University of Genoa.

## Content

- **`simple.c`: kernel module.** Registers the character device `simple` and implements `open`, `release` and `write`. Each write copies the string from user space (`copy_from_user`), protected by a semaphore, and prints it to the kernel log.
- **`main.c`: real-time application.** Three periodic tasks (300, 500, 800 ms) with Rate Monotonic priorities (`SCHED_FIFO`) and one aperiodic task, woken up by task 2 through a condition variable. Before starting, the program checks schedulability with the utilization test. Each task writes its start and end markers (e.g. `1[` `]1`) to `/dev/simple`, so the execution order can be read with `dmesg`.
- **`Makefile`:** builds the kernel module.

## Usage

Requires Linux with the kernel headers installed and root privileges.

```bash
# Build and load the module
make
sudo insmod simple.ko
dmesg | tail          # read the major number assigned to the device

# Create the device file
sudo mknod /dev/simple c <major> 0
sudo chmod 666 /dev/simple

# Build and run the application
g++ main.c -lpthread -o main
sudo ./main

# Read the execution trace written by the tasks
dmesg

# Unload the module
sudo rmmod simple
```
