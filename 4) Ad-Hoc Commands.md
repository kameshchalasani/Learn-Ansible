Ad-hoc commands in Ansible are used to execute quick, one-time tasks on remote nodes without the need for writing a full playbook. They are ideal for tasks like checking the status of a service, copying files, or rebooting servers. Ad-hoc commands are executed using the `ansible` command-line tool.

### Basic Syntax
```bash
ansible <pattern> -m <module> -a "<module arguments>" [options]
```

- **`<pattern>`**: Specifies the target hosts or groups defined in your inventory.
- **`-m <module>`**: Specifies the Ansible module to use (e.g., `ping`, `shell`, `copy`).
- **`-a "<module arguments>"`**: Provides arguments for the module.
- **`[options]`**: Additional options like `-i` for inventory, `-u` for user, `-b` for become (sudo), etc.

---

### Common Use Cases

#### 1. Ping All Hosts
Check connectivity to all hosts in the inventory:
```bash
ansible all -m ping
```

#### 2. Run a Shell Command
Execute a shell command on all hosts:
```bash
ansible all -m shell -a "uptime"
```

#### 3. Copy a File
Copy a file from the control machine to remote hosts:
```bash
ansible all -m copy -a "src=/path/to/local/file dest=/path/to/remote/file"
```

#### 4. Install a Package
Install a package using the `yum` or `apt` module:
```bash
ansible all -m yum -a "name=httpd state=present" -b
```
- `-b`: Run with elevated privileges (sudo).

#### 5. Restart a Service
Restart a service on all hosts:
```bash
ansible all -m service -a "name=httpd state=restarted" -b
```

#### 6. Check Disk Space
Check disk space on all hosts:
```bash
ansible all -m shell -a "df -h"
```

#### 7. Gather Facts
Collect facts about the remote hosts:
```bash
ansible all -m setup
```

#### 8. Reboot Hosts
Reboot all hosts:
```bash
ansible all -m reboot
```

---

### Options for Ad-Hoc Commands

- **`-i <inventory>`**: Specify a custom inventory file.
- **`-u <username>`**: Run the command as a specific user.
- **`-b`**: Become (sudo) to execute the command with elevated privileges.
- **`--become-user=<user>`**: Become a specific user (e.g., root).
- **`-f <fork_count>`**: Set the number of parallel processes (default is 5).
- **`-v`**: Increase verbosity (use `-vvv` for more details).

---

### Example with Options
Run a command as the `root` user on a specific group of hosts:
```bash
ansible webservers -m shell -a "systemctl status nginx" -u admin -b --become-user=root
```

---

### When to Use Ad-Hoc Commands
- Quick, one-time tasks.
- Testing connectivity or module functionality.
- Simple operations that don’t require the complexity of a playbook.

For more complex or repeatable tasks, consider using Ansible playbooks.
