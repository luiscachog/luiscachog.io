---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Using the Ansible Stat Module on a Loop"
url: "/using-the-ansible-stat-module-on-a-loop"
subtitle: ""
summary: "How to use the stat module on a Loop"
authors: [ luiscachog ]
tags: [Ansible]
categories: [Ansible, DevOps]
keywords: [Ansible, Stat, File, Loop]
date: 2020-04-12
publishDate: 2020-04-12
lastmod: 2020-04-12
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

{{% toc %}}

# Using the Ansible stat module on a loop

Hi again, it's been a while since I wrote something on this blog.
This time I was working on a Ansible playbook and I get this:

## Problem

I want to verify if a file exists. Depending on the registered output I want to perform some other actions.
On my specific use case, I want to use in in conjunct with the `file` module to define the state on my next task.

{{< diagram >}}
graph TD;
A(Stat over file) --> B{Does the file exists?};
B -->|Yes| C[state: file];
B -->|No| D[state: touch];
{{< /diagram >}}

## Solution

Here is the solution that worked for me, using `loops`, `loop_control`,and `jinja2` filters.

```yaml
- name: Stat over the files
  stat:
    path: "{{ my_loop }}"
    loop:
      - /etc/cron.allow
      - /etc/at.allow
    loop_control:
      loop_var: my_loop
    register: my_stat_var

- name: Create a file if not exists
  file:
    path: "{{ item.my_loop }}"
    owner: root
    group: root
    mode: og-rwx
    state: "{{ 'file' if item.stat.exists else 'touch' }}"
  loop: "{{ my_stat_var.results }}"
```

Basically, I'm using the new `loop` [syntax](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html) to iterate over all the files that I want to check.

In order to avoid some warnings about the loop using `item` I implement the `loop_control` and `loop_var` syntax to control the loop behavior and on this specific case, instead of using the word `item` I will substitute it with the one that I define as my `loop_var` in this case is called `my_loop` (Remember this, I will use it on the next task).

I'm registering the result of the stat task on a variable, for this case is `my_stat_var`

Here is an example of the output, when the 2 files **do not exists:**

```json
ok: [instance-amazon2] => {
    "my_loop": {
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
                "ansible_loop_var": "my_loop",
                "at_cron_restricted_touch": "/etc/cron.allow",
                "changed": false,
                "failed": false,
                "invocation": {
                    "module_args": {
                        "checksum_algorithm": "sha1",
                        "follow": false,
                        "get_attributes": true,
                        "get_checksum": true,
                        "get_md5": false,
                        "get_mime": true,
                        "path": "/etc/cron.allow"
                    }
                },
                "stat": {
                    "exists": false
                }
            },
            {
                "ansible_loop_var": "my_loop",
                "at_cron_restricted_touch": "/etc/at.allow",
                "changed": false,
                "failed": false,
                "invocation": {
                    "module_args": {
                        "checksum_algorithm": "sha1",
                        "follow": false,
                        "get_attributes": true,
                        "get_checksum": true,
                        "get_md5": false,
                        "get_mime": true,
                        "path": "/etc/at.allow"
                    }
                },
                "stat": {
                    "exists": false
                }
            }
        ]
    }
}
```

On the next task I access the results of the registered variable `my_stat_var` using, according to the previous output, I need to use the `results` variable to access it and gives me 2 arrays (each one for each file)

Also, I extract the `path` from the same variable `my_stat_var.results` **but** as it is part of a loop, I will access it using `item.my_loop` as on the the second task loop I'm not using a `loop_control`, also I can access it using `item.item` but I prefer the first syntax. In case you implement a `loop_control` on you can access it as `my_second_loop.my_loop`

Also, I extract if the file exists with the variable `item.stat.exists` **but** I'm putting that on a `jinja2` filter, that way it will evaluate and set the correct option on the `state`

```yaml
# When the file exists
state: file
# When the file does not exists
state: touch
```

This behavior also brings idempotency to your task, because if the files does not exists on the first iteration it will be created and the next time you run the tasks as the files already exists it will works as a `stat` and will return the current state of `path`.

That is all for now. See you soon!
