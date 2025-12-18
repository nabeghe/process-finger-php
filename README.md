# Process Finger

**Process Finger** is a PHP utility library for interacting with system processes. It provides a unified, cross-platform interface to retrieve process information, manage processes, and inspect system resources.

> ⚠️ **Warning:** This library can terminate processes and manipulate system resources. Use responsibly. Improper usage may cause data loss or system instability.

## Features

* Get current process ID and parent process ID
* Retrieve process script paths and process names
* Check if a process exists or is running as root/admin
* Detect zombie processes
* Retrieve memory and CPU usage of processes
* List child processes of a given PID
* Kill, wait for, and restart processes
* Access environment variables of processes (Unix only)
* Fully compatible with Linux, macOS, BSD, and Windows (with limitations)

## Installation

Via Composer:

```bash
composer require nabeghe/process-finger
```

Or manually include the `ProcessFinger.php` file in your project.

## Usage

```php
use Nabeghe\ProcessFinger;

// Get current PID
$pid = Process::getId();

// Check if a process exists
$exists = Process::exists($pid);

// Kill a process
Process::kill($pid, true);

// Restart a process
$newPid = Process::restart($pid, 'php script.php');

// Get memory usage
$memory = Process::getMemoryUsage($pid);

// Get CPU usage
$cpu = Process::getCpuUsage($pid);

// Get process environment (Unix only)
$env = Process::getProcessEnv($pid);

// List child processes
$children = Process::listChildren($pid);

// Get process name
$name = Process::getProcessName($pid);

// Check if running as root/admin
$isRoot = Process::isRunningAsRoot();

// Check if process is zombie
$isZombie = Process::isZombie($pid);

// Wait for process to exit
Process::wait($pid, 5); // Wait up to 5 seconds
```

## Methods Overview

| Method                                                   | Description                                        | Notes                                               |
| -------------------------------------------------------- | -------------------------------------------------- | --------------------------------------------------- |
| `getId()`                                                | Returns current process ID                         |                                                     |
| `getParentId(?int $pid = null)`                          | Returns parent PID                                 | Defaults to current process                         |
| `getScriptPath(?int $pid = null)`                        | Absolute path of the process script                | Requires `shell_exec` enabled                       |
| `getProcessName(?int $pid = null)`                       | Main command / process name                        |                                                     |
| `exists(int $pid)`                                       | Check if a process exists                          | Returns true/false/null                             |
| `isRunningAsRoot()`                                      | Check if current process has root/admin privileges | Windows uses `net session` command                  |
| `isZombie(int $pid)`                                     | Detect zombie processes                            | Unix only                                           |
| `getMemoryUsage(?int $pid = null)`                       | Memory usage in bytes                              |                                                     |
| `getCpuUsage(?int $pid = null)`                          | CPU usage percent                                  | Limited precision on Windows                        |
| `getProcessEnv(?int $pid = null)`                        | Get process environment variables                  | Unix only                                           |
| `listChildren(?int $pid = null)`                         | List child process IDs                             |                                                     |
| `kill(int $pid, bool $force = true)`                     | Terminate a process                                | Uses SIGKILL/SIGTERM on Unix, `taskkill` on Windows |
| `wait(int $pid, int $timeout = 0)`                       | Wait until process exits                           | Timeout in seconds, 0 = infinite                    |
| `restart(int $pid, string $command, bool $force = true)` | Kill and restart a process                         | Returns new PID if started successfully             |

## Security & Risks

* **Process Termination:** Misuse of `kill()` or `restart()` may terminate critical system processes. Always double-check PIDs before calling these methods.
* **Privilege Checks:** Running with root/admin privileges increases risk. Avoid using these methods on critical processes.
* **Environment Variables:** `getProcessEnv()` is only available on Unix-like systems. Accessing environment data may contain sensitive information.
* **Zombie Detection:** Only meaningful on Unix-like systems. Windows does not support zombie processes.
* **Cross-Platform Differences:** Some commands behave differently on Windows vs Linux/macOS. Always test on your target OS.

## 📖 License

Licensed under the MIT license, see [LICENSE.md](LICENSE.md) for details.
