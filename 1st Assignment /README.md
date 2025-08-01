## **Assignment 14: Sudo Usage Logging**

### **Objective**

To monitor and log all `sudo` command usage on Kali Linux every 30 seconds, ensuring administrative actions are captured for security auditing and analysis.

---

### **Methodology**

#### **Script Development**

* Created a Python 3 script named `sudo_monitor.py`.

* Implemented a loop running every 30 seconds to execute:

  ```bash
  journalctl _COMM=sudo --since=-1min
  ```

  using `subprocess.run(..., shell=True)` to fetch sudo entries from the past minute.

* Parsed each non-empty output line, prefixed it with a timestamp, and appended it to `sudo_usage_log.txt`.

#### **Execution**

* Ran the script with:

  ```bash
  sudo python3 sudo_monitor.py
  ```
* In a separate terminal, executed various `sudo` commands (e.g., `sudo ls`) to generate log entries.

#### **Verification**

* After approximately 30 seconds, stopped the script with `Ctrl+C`.
* Reviewed `sudo_usage_log.txt` to confirm that the expected entries were captured.

---

### **Findings**

* Successfully logged both the invocation and session details of `sudo` usage.

* Each entry includes:

  * Username
  * TTY
  * Working directory
  * Target user
  * Executed command

* The script reliably recorded each new `sudo` attempt during each 30-second interval.

---

### **Screenshot**

![sudo usage output](/images/output.png)

---

### **Code Access**

import time
import subprocess
from datetime import datetime

f = open("sudo_usage_log.txt", "a")

while True:
    result = subprocess.run(
        "journalctl _COMM=sudo --since=-1min",
        shell=True,
        capture_output=True,
        text=True
    )
    out = result.stdout

    for line in out.split('\n'):
        if line.strip():
            f.write(f"{datetime.now()} - {line.strip()}\n")

    f.flush()
    time.sleep(30)

---

### **Conclusion**

This lightweight Python-based monitoring tool offers an efficient way to log administrative activity. By timestamping each `sudo` usage event, it enhances system visibility, aids in detecting unauthorized privilege escalations, and supports forensic and security investigations.

---

