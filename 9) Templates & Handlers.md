In Ansible, **templates** and **handlers** are powerful features that enhance the flexibility and efficiency of your automation tasks. 
**Templates** allow you to generate dynamic configuration files using the Jinja2 templating engine, while **handlers** are tasks that run only when notified by other tasks. 
Let’s explore how to use these features effectively.

---

### **Jinja2 Templates**

#### 1. **What Are Jinja2 Templates?**
Jinja2 is a templating engine for Python that allows you to generate dynamic content. In Ansible, Jinja2 templates are used to create configuration files, scripts, or any text-based files that need to be customized for different hosts or environments.

#### 2. **Using Templates in Ansible**
- Templates are processed using the `template` module.
- Template files typically have a `.j2` extension.
- Variables, loops, and conditionals can be used within templates.

#### 3. **Example: Creating a Dynamic Configuration File**
Suppose you want to create an Apache configuration file with a dynamic port number.

- **Template File (`templates/httpd.conf.j2`)**:
  ```jinja2
  Listen {{ http_port }}
  <VirtualHost *:{{ http_port }}>
      DocumentRoot /var/www/html
      ServerName {{ ansible_facts['fqdn'] }}
  </VirtualHost>
  ```

- **Playbook**:
  ```yaml
  ---
  - name: Configure Apache
    hosts: webservers
    vars:
      http_port: 80
    tasks:
      - name: Copy Apache configuration
        template:
          src: templates/httpd.conf.j2
          dest: /etc/httpd/conf/httpd.conf
  ```

#### 4. **Jinja2 Features**
- **Variables**: Use `{{ variable_name }}` to insert variables.
- **Conditionals**: Use `{% if condition %} ... {% endif %}`.
- **Loops**: Use `{% for item in list %} ... {% endfor %}`.

Example:
```jinja2
{% if ansible_facts['os_family'] == "RedHat" %}
Listen 80
{% else %}
Listen 8080
{% endif %}
```

---

### **Handlers**

#### 1. **What Are Handlers?**
Handlers are special tasks that run only when notified by other tasks. They are typically used to restart services or perform actions that should only happen when a change occurs.

#### 2. **Using Handlers**
- Handlers are defined in the `handlers` section of a playbook or role.
- Tasks notify handlers using the `notify` directive.

#### 3. **Example: Restarting Apache**
- **Playbook**:
  ```yaml
  ---
  - name: Configure Apache
    hosts: webservers
    tasks:
      - name: Copy Apache configuration
        template:
          src: templates/httpd.conf.j2
          dest: /etc/httpd/conf/httpd.conf
        notify: Restart Apache

    handlers:
      - name: Restart Apache
        service:
          name: httpd
          state: restarted
  ```

- In this example:
  - The `Copy Apache configuration` task notifies the `Restart Apache` handler.
  - The handler will only run if the `Copy Apache configuration` task results in a change.

#### 4. **Multiple Notifications**
A single handler can be notified by multiple tasks:
```yaml
tasks:
  - name: Update Apache configuration
    template:
      src: templates/httpd.conf.j2
      dest: /etc/httpd/conf/httpd.conf
    notify: Restart Apache

  - name: Update PHP configuration
    template:
      src: templates/php.ini.j2
      dest: /etc/php.ini
    notify: Restart Apache

handlers:
  - name: Restart Apache
    service:
      name: httpd
      state: restarted
```

#### 5. **Handlers in Roles**
Handlers can also be defined in roles. For example, in a role’s `handlers/main.yml`:
```yaml
---
- name: Restart Apache
  service:
    name: httpd
    state: restarted
```

---

### **Combining Templates and Handlers**

#### 1. **Dynamic Configuration with Restart**
Here’s an example that combines templates and handlers to dynamically configure and restart a service:

- **Template File (`templates/nginx.conf.j2`)**:
  ```jinja2
  server {
      listen {{ nginx_port }};
      server_name {{ ansible_facts['fqdn'] }};
      root /var/www/html;
  }
  ```

- **Playbook**:
  ```yaml
  ---
  - name: Configure Nginx
    hosts: webservers
    vars:
      nginx_port: 8080
    tasks:
      - name: Copy Nginx configuration
        template:
          src: templates/nginx.conf.j2
          dest: /etc/nginx/nginx.conf
        notify: Restart Nginx

    handlers:
      - name: Restart Nginx
        service:
          name: nginx
          state: restarted
  ```

#### 2. **Conditional Handlers**
You can use conditionals in handlers to control when they run:
```yaml
handlers:
  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
    when: ansible_facts['os_family'] == "Debian"
```

---

### **Best Practices**

#### 1. **Use Templates for Dynamic Content**
- Use Jinja2 templates to generate configuration files that vary across hosts or environments.

#### 2. **Keep Handlers Idempotent**
- Ensure handlers are idempotent (i.e., they can be run multiple times without causing issues).

#### 3. **Limit Notifications**
- Notify handlers only when necessary to avoid unnecessary restarts or actions.

#### 4. **Organize Handlers in Roles**
- Define handlers in the `handlers/main.yml` file of a role for better organization.

#### 5. **Test Templates**
- Test Jinja2 templates locally before using them in playbooks.

---


### **Conclusion**
Templates and handlers are essential tools in Ansible for creating dynamic configurations and managing service states. 
By leveraging Jinja2 templates, you can generate customized files for different environments, while handlers ensure that services are restarted or reconfigured only when necessary. 
Combining these features allows you to build robust and efficient automation workflows.
