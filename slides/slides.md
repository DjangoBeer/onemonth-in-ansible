% title: One month in Ansible
% subtitle: A first dive in Ansible
% author: Iacopo Spalletti
% thankyou: Thanks everyone!
% contact: <span>www</span> <a href="https://www.nephila.it/">Nephila</a>
% contact: <span>github</span> <a href="https://github.com/yakkys">yakkys</a>
% favicon: https://www.nephila.it/favicon.ico

---
title: What the heck is it?
build_lists: true

- An orchestration tool to handle multiple servers
- Control multiple servers with configuration files

    - Stateless
    - Serverless

---
title: why not ssh?
build_lists: true

- raw ssh does not scale

     - try managing dozens of servers with ssh!

- Ansible uses ssh to tunnel commands!

---
title: How it works?
build_lists: true

- inventory of hosts
- plays

    - sets of commands

- playbooks

    - sets of plays

---
title: Stateless?

You don't need to know

---
title: Serverless?
build_lists: true

- no **ansible server**, just a **Control machine**
- **Control machine**: any machine with a set of plays and playbooks

---
title: what are plays?

<pre class="prettyprint" data-lang="yaml">

- hosts: servers
  remote_user: user

  tasks:
  - name: Hello
    command: echo "hello"

</pre>

---
title: what's a command?

Well, almost anything

### shell command

<pre class="prettyprint" data-lang="yaml">
    command: echo "hello"
</pre>

### module command

<pre class="prettyprint" data-lang="yaml">
    apt: pkg=vim state=installed
</pre>

Compiled python scripts transferred via ssh and executed on the remote node 

---
title: Prepare the control machine

<pre class="prettyprint" data-lang="debian/ubuntu">
    $ sudo apt-get install software-properties-common
    $ sudo apt-add-repository ppa:ansible/ansible
    $ sudo apt-get update
    $ sudo apt-get install ansible
</pre>

<pre class="prettyprint" data-lang="arch">
    $ pacman -S ansible
</pre>

<pre class="prettyprint" data-lang="Mac OS">
    $ pip install ansible
</pre>

---
title: server requirements?
build_lists: true

Minimal:

- **ssh**
- **python-simplejson**

---
title: execute command from local shell

## Say hello!

<pre class="prettyprint" data-lang="bash">
    $ ansible -a "/bin/echo hello" -u user 192.168.122.237 -vvvv
</pre>

---
title: execute a play

## Say hello again!

<pre class="prettyprint" data-lang="bash">
    $ ansible-playbook -i hosts -vvvv 1-hello.yml
</pre>

---
title: plays can be appened in a playbook

<pre class="prettyprint" data-lang="bash">
    $ ansible-playbook -i hosts -vvvv 2-two-hellos.yml
</pre>

---
title: Let's do something real!

<pre class="prettyprint" data-lang="bash">
    $ ansible-playbook -i hosts -vvvv 3-do-something.yml
</pre>

---
title: Elements
build_lists: true

- Hosts
- Tasks
- Variables
- Roles
- Templates

---
title: Hosts
build_lists: true

- **Control machine** has an **inventory** of hosts

    <pre class="prettyprint" data-lang="bash">
    192.168.122.237
    192.168.122.102
    
    [webservers]
    192.168.122.237
    
    [database]
    192.168.122.102
    </pre>

- Commands sent to a subset of the hosts in inventory

---
title: Tasks



---
title: Variables

Variables makes playbook very flexible

Defined in:

 * vars files
 * command line
 * facts (host-dependent information
 * tasks (saving output)

<pre class="prettyprint" data-lang="bash">
    $ ansible-playbook -i hosts -e package=less 4-use-variables.yml
</pre>

---
title: Roles

Roles allows to group tasks by "topic"

<pre class="prettyprint" data-lang="bash">

- hosts: webservers
  remote_user: user
  become: yes
  become_user: root

  vars_files:
    - vars.yml

  roles:
    - { role: nginx }

</pre>

---
title: Templates

* Ansible manages services by 
* Uses Jinja2 template engine

<pre class="prettyprint" data-lang="command">
- name: Add basic configuration files for wan servers
  template: src=nginx.con dest=/etc/nginx/sites-available/{{ hostname }}.conf
</pre>

<pre class="prettyprint" data-lang="template">
server {
    listen 80;
    server_name {{ hostname }};

    location / {

        proxy_pass http://unix:{{socket}}:/;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;

    }
}
</pre>
