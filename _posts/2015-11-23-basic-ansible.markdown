---
layout: post
title:  "getting started with ansible"
date:   2015-11-23
permalink: /getting-started-with-ansible
categories:
  - devops
  - ansible
---
## Installing Python and Ansible
Your best bet is to read though the installation docs [here](http://docs.ansible.com/ansible/intro_installation.html). Though I've included the commands below if you're impatient and using OS X or Ubuntu/Debian. If ansible is already installed, feel free to jump down to the next section.

Many users users will already have python/pip already installed on their machines. If you find that the python or pip commands aren't available you can run the following:

OS X

{% highlight console %}
$ brew install python
{% endhighlight %}

Ubuntu/Debian

{% highlight console %}
$ sudo apt-get install python-dev python-pip
{% endhighlight %}

Once you have python/pip installed you can install ansible with:

{% highlight console %}
$ pip install ansible
{% endhighlight %}

---

##Running a Simple playbook
Now that ansible is installed, let's run a simple playbook!

Create a file named `main.yml` that looks like this:


{% highlight yaml %}
---
- hosts: localhost
  connection: local

  tasks:
    - name: say hello with /bin/sh
      shell: echo 'hello ansible'
{% endhighlight %}

Ansible uses yaml to create "playbooks" which are responsible for running commands on local or remote machines. A playbook is essentially a bunch of keys and values that help describe what hosts we want to target and what tasks we want to run. With that in mind, let's break down what we've written.

`hosts: localhost` tells ansible that we want to run our playbook against `localhost`. Normally if you're running ansible to configure servers you'll either want to specify an IP or create an [inventory file](http://docs.ansible.com/ansible/intro_inventory.html).

`connection: local` tells ansible to connect locally. Generally you don't need to provide this setting since you'll normally want to ssh into a box.

{% highlight yaml %}
  tasks:
    - name: say hello with /bin/sh
      shell: echo 'hello ansible'
{% endhighlight %}

What we've done here is created a tasks list that allows us to tell ansible what commands we want to run. The `name` key is optional, but super useful when debugging your own playbooks. The next line where we put `shell: echo 'hello ansible'` tells ansible to use the [shell](http://docs.ansible.com/ansible/shell_module.html) module to run `echo 'hello ansible'` using `/bin/sh`.

---

Now that we've created a little playbook, we can run it by issuing the ansible-playbook command with our playbook file:

{% highlight console %}
$ ansible-playbook main.yml
{% endhighlight %}

The output should look something like this:

{% highlight console %}
zzsmnzn@thecloud:~/ansible_stuff$ ansible-playbook main.yml
 [WARNING]: provided hosts list is empty, only localhost is available


PLAY [localhost] **************************************************************

GATHERING FACTS ***************************************************************
ok: [localhost]

TASK: [say hello with /bin/sh] ************************************************
changed: [localhost]

PLAY RECAP ********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
{% endhighlight %}

So the command ran, but where is our 'hello ansible' string? As it turns out, ansible will capture the stdout and stderr of commands you run. Let's rerun this command once more, but with the `-v` (verbose) flag.

{% highlight console %}
$ ansible-playbook main.yml -v
{% endhighlight %}

It should output something close to this:

 {% highlight console %}
 TASK: [say hello with /bin/sh] ************************************************
 changed: [localhost] => {"changed": true, "cmd": "echo 'hello ansible'", "delta": "0:00:00.005328", "end": "2015-11-18 16:25:46.473834", "rc": 0, "start": "2015-11-18 16:25:46.468506", "stderr": "", "stdout": "hello ansible", "warnings": []}
{% endhighlight %}

And now if we inspect the output we can see `stdout` is `'hello ansible'`!

## Next steps

Running a some shell commands is nice enough, though we haven't really dug into leveraging inventories, roles, templates, and using the modules/plugins that ansible comes with for provisioning servers and orchestrating deployments.

I highly recommend reading [about modules](http://docs.ansible.com/ansible/modules.html) from the ansible documentation.

