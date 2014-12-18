---
layout: post
title:  "Ansible 'Prompt' handlers"
date:   2014-12-17 18:30:45
excerpt: "An awesome feature in Chef that is not available in Ansible is immediate notification i.e. `notifies :immediately`.
Ansible has notification handlers but they are only triggered at the end of the current playbook unlike Chef's which
can be triggered immediately! This blogpost describes an easier way of having immediate handlers."
category: Linux
tags: ansible
---

An awesome feature in [Chef](https://chef.io) that is not available in [Ansible](http://ansible.com) is immediate notification i.e. `notifies :immediately`.
Ansible has notification handlers<sup>[[1]](http://docs.ansible.com/playbooks_intro.html#handlers-running-operations-on-change)</sup> but they are only triggered at the end of the current playbook unlike Chef's<sup>[[2]](https://docs.chef.io/resource_common.html#notifies-syntax)</sup> which can be  triggered immediately! Moreover, you can configure Chef's notifications to be triggered at specific times i.e. at the very end of a chef-client run i.e. `:delayed` or immediately i.e. `:immediately`

<p>
Now, why I'm going into all these boring theories?? Well, when installing tomcat on Ubuntu, dpkg starts it automatically
once the process is complete. I wanted to stop tomcat7 service, configure it, deploy its webapps & finally start it. So
after installing tomcat7 I added a notification action to call a task that stops tomcat7 service.
</p>

{% highlight yaml %}
{% raw %}
tasks:
    - name: Install tomcat7
      apt: name={{ item }} install_recommends=no update_cache=yes state=present
      with_items:
        - tomcat7
        - tomcat7-admin
      notify:
        - Temporarily stop tomcat7

handlers:
    - name: Temporarily stop tomcat7
    service: name=tomcat7 state=stopped
{% endraw %}
{% endhighlight %}

<p>
Unfortunately, this didn't work because notifications trigger tasks in handlers to run at the end of a playbook.
So I had to come up with a quick fix for this issue.
</p>

My quick fix involved registering a variable in task that installs tomcat packages i.e. `register: tomcat_installed`,
then I would run the next task only if the registered variable has changed i.e. `when: tomcat_installed|changed`.
Basically, handlers use a similar concept to this.

<p>
Here's a snippet from the playbook showing my quick fix:
{% highlight yaml %}
{% raw %}
tasks:
    - name: Install tomcat7
      apt: name={{ item }} install_recommends=no update_cache=yes state=present
      with_items:
        - tomcat7
        - tomcat7-admin
      register: tomcat_installed

    - name: Temporarily stop tomcat7
      service: name=tomcat7 state=stopped
      when: tomcat_installed|changed
{% endraw %}
{% endhighlight %}
</p>

<p>
As you can see from the snippet, I've not used a handler. Yes that's right, inorder to achieve the 'effect' of an immediate handler,
I moved the task that stops tomcat7 service from the handler section to the tasks section.
</p>

<p>
As always, these are my opinions that I don't mind sharing with anyone. Though I'm sure there are better solutions to my original
problem, I think this solution could be useful for other ansible-related problems.
</p>

### Links
1. [Ansible Handlers: Running Operations On Change](http://docs.ansible.com/playbooks_intro.html#handlers-running-operations-on-change)
2. [Chef Notifications](https://docs.chef.io/resource_common.html#notifies-syntax)
