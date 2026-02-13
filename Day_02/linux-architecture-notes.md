Today’s goal is to understand how Linux works under the hood.

You will create a short note that explains:

1. The core components of Linux (kernel, user space, init/systemd)


The core components of the Linux operating system include the kernel, system libraries, system utilities, the shell, user interface (CLI/GUI) and the bootloader. These components work together to provide a robust, multi-user, and multitasking environment, typically packaged with software into a distribution. 

Kernal=> Linu kernal is a basic core part of the operting system that works as a bridge between hardware and software.

System Libraries=> System libraries implement most of the operating system's features and allow application programs or system utilities to access the kernel's functionalities without needing direct, privileged access to hardware.Most system libraries are stored in standard directories such as /lib, /usr/lib, /lib64, and /usr/lib64.

System Utilities=> It is the software functions, through which user manages the system

Shell=> Shell is an interface between a kernal and a user

User Interface=> There are two types of user interfaces in linux: (i) CLI, (ii) GUI

Bootloader=> The bootloader (e.g., GRUB) responsible for loading the kernel during startup. 

2. How processes are created and managed
   
Process Creation in Linux
The creation of a new process in Linux typically involves a two-step mechanism using specific system calls: 
1.	fork(): This system call creates a new, nearly identical child process by duplicating the calling (parent) process's address space and resources in memory.
            •	To optimize memory usage, modern Linux systems use a technique called Copy-on-Write (COW). Both the parent and child initially share the same physical memory pages; a page is only copied if one of the processes attempts to modify it.
            •	The parent process receives the child's unique Process ID (PID) as the return value, while the child receives a return value of 0, allowing them to follow differentexecution paths.
2.	exec(): In most cases, immediately after fork(), the child process calls an exec() system call (e.g., execve, execv). This call replaces the child's current memory space, code, and data with a new program's executable file, but the process's PID remains unchanged. 
The parent process often uses the wait() system call to pause its own execution until the child process terminates and its exit status is collected.

Process Management in Linux:
The Linux kernel manages processes through a data structure called task_struct, which contains all relevant information about a process, including its PID, state, and resource usage. Users and administrators manage these processes using several command-line tools: 
Monitoring Processes
    •	ps: Displays a snapshot of current processes. Using options like ps aux or ps -ef provides detailed information about all processes on the system.
    •	top: Provides a real-time, dynamic view of running processes, sorted by resource usage (CPU and memory).
    •	htop: An enhanced, more user-friendly alternative to top.
    •	pstree: Shows the hierarchical parent-child relationships between processes in a tree format.

Controlling Processes
   •	kill: Sends signals to a process to manage its behavior.
   o	SIGTERM (15): The default and most graceful way to ask a process to terminate.
   o	SIGKILL (9): Forcefully terminates a process that is unresponsive and ignores other signals.
   •	nice and renice: Used to adjust the scheduling priority of a process. The "nice value" ranges from -20 (highest priority) to 19 (lowest priority).
   •	fg and bg: Commands used in the shell for job control to move processes between the foreground and background.
   •	nohup: Allows a command to continue running in the background even after the user logs out of the terminal.

Process States:-
During its lifecycle, a process transitions through various states: 
   •	Running (R): The process is currently executing on the CPU or is in the queue waiting to run.
   •	Sleeping (S/D): The process is waiting for some event or resource (e.g., I/O completion).
   •	Stopped (T): The process has been paused, usually by a job control signal.
   •	Zombie (Z): The process has terminated, but its entry still remains in the process table because the parent process has not yet collected its exit status. 

3. What systemd does and why it matters ?
   Systemd is the default, modern init system and service manager for most Linux distributions, acting as the primary manager (PID 1) that boots the system, starts/stops services, and handles resources via units. It significantly improves boot speeds through parallel processing, simplifies dependency management between services, and provides a unified interface (systemctl) for managing daemons, devices, mounts, and logs.
   
   What systemd Does:
      •	Initializes the System (PID 1): Starts as the first process after the kernel loads, responsible for mounting file systems and launching system services.
      •	Service Management: Controls daemon lifecycle (start, stop, restart, enable, disable) using unit files (.service).
      •	Parallelization: Launches services concurrently, dramatically accelerating boot times compared to older linear methods like SysVinit.
      •	Dependency Management: Ensures services start in the correct order based on dependencies (e.g., waiting for networking to start before starting a web server).
      •	Resource Control: Uses Linux cgroups to track and manage processes, preventing runaway services from freezing the system.
      •	Unified Tooling: Manages logging (journald), device management (udev), network settings, and logins.
   
   Why systemd Matters in Linux:
      •	Standardization: Offers a consistent, unified approach to system management across major distributions (Ubuntu, Fedora, Debian, CentOS).
      •	Efficiency: Designed for modern hardware, optimizing system boot, performance, and resource usage.
      •	Service Reliability: Automatically restarts failed services and handles complex dependencies, increasing system stability.
      •	Simplified Administration: Provides systemctl as a single tool to monitor, manage, and debug the entire system state, from services to mount points.
      •	Advanced Features: Supports socket-based activation, allowing services to start only when needed, which reduces idle resource usage. 
   Commonly used commands include systemctl start/stop/restart/status <service>, systemctl enable/disable for startup, and journalctl for viewing logs. 

