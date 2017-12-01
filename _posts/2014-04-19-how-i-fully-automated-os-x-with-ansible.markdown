---
layout: post
title:  "How I Fully Automated OS X With Ansible"
date:   2014-04-19 19:24:32 -0400
---
So I finally got around to doing something I've been meaning to do for a while:
automating my desktop with Ansible.  This has been a goal of mine for some time
and thought I'd share my setup.

First, I install Ansible using brew: `brew install ansible`. Then I set up a
global repo (`~/.ansible.d`) for my playbooks. Then I set up a global
`~/.ansible.d/site.yml` file in that repo.

My `~/.ansible.d/site.yml` file:
```
---
- import_playbook: base_osx.yml
- import_playbook: dotfiles.yml
```

And my `~/.ansible.d/base_osx.yml` file:
```
---
- hosts: base_osx
  roles:
    - base_osx
  gather_facts: False
```

And my `~/.ansible.d/dotfiles.yml` file is similar:
```
---
- hosts: dotfiles
  become: yes
  become_user: "dan"
  roles:
    - dotfiles
  gather_facts: False
```

And my `~/.ansible.d/inventories/osx_local` file:
```
[base_osx]
127.0.0.1

[dotfiles]
127.0.0.1

[osx_local:children]
base_osx
dotfiles
```

Then I set up my group vars.  Here's my `~/.ansible.d/group_vars/osx_local.yml` file:
```
---
system: osx
os: osx

executables:
  brew: /usr/local/bin/brew
  hg: /usr/local/bin/hg
  zsh: /usr/local/bin/zsh

directories:
  etc: /usr/local/etc
  var: /usr/local/var

databases:
  - test_database

dan:
  domain_name: example.com
  user:
    name: dan
    group: staff
    shell: "/usr/local/bin/zsh"

  template_home: home/dan
  home: /Users/dan
  system: osx
  ruby:
    version: "2.1.3"
  rbenv:
    global: "2.1.3"
```

You can find the roles
[here](https://github.com/danieljaouen/ansible-base-osx) and
[here](https://github.com/danieljaouen/ansible-dotfiles) .

Then I run the `~/.ansible.d/site.yml` playbook by running:
```
ansible-playbook -i ~/.ansible.d/inventories/osx_local ~/.ansible.d/site.yml --connection=local`
```

Then, after the initial setup, I cd into the `~/.dotfiles` repo and run `rake`,
which symlinks my dotfiles into place.

And that's it!  Hopefully you found this useful.
