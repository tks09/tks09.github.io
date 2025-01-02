---
title: "Bashify your DevOps"
date: 2025-01-02T16:45:00+02:00
taxonomies:
  tags: ["complexity", "homelab", "homeautomation"]
---

In the quest for efficient homelab maintenance, I found myself navigating a maze of automation tools, from Ansible to Chef, each promising streamlined management and ease of use. However, what I encountered instead were frequent headaches and endless debug sessions, particularly with Ansible, which seemed to thrive on Python dependency issues that often derailed my efforts. Frustrated by the complexity and overhead of these solutions for my relatively simple local setup, I decided to take matters into my own hands. This led me to the realization that sometimes, the best approach is to strip away the layers of abstraction and craft a custom solution tailored to my needs—enter my own bash script system. In this blog post, I’ll share my journey from convoluted automation frameworks to the simplicity and effectiveness of scripting, and how it transformed my homelab experience.

_Thank you [DuckDuckGo AI](https://duckduckgo.com/chat) for providing this great introduction!_

Basically, the whole thing got the Jist. If continiously had issues with python dependencies and Ansible, especially if the Linux distributions upgraded their provided python versions and therefore things crashed. Since i actually do mostly simple stuff like a good ol `apt-get upgrade`, and all my systems are Linux systems, I decided that I will go with the most basic effort thats possible. 

**I'm very aware, that in very very large professional environments, this might not be the right things to do. Especially, if you're not just focussing on one OS(Linux) and one distribution(Ubuntu).**

To mention this upfront, my homelab is not my [home automation](/reducing_complexity_ha/) which is by now just a HomeAssistant running on a Raspberry Pi. 

My homelab consists of two ProxMox servers (Minisforum and a ThinkCenter). This homelab hosts some general services I sometimes use more or less. To save power on this, the Homelab has some scheduled availability times: 
- from 03:00 to 04:00 both systems are online for backups. Also, my Mac Devices use the HomeLabs OpenMediaVault as a TimeMachine backup resource. 
- from 16:00 to 22:00, where I'm usually at home and want to use the NextCloud or other services. Therefore, only the ThinkCentre is running here.

To manage this, I taught my 24/7 Raspberry Pi to boot and shutdown both machines via Wake-on-LAN.

So what do I want to happen:

- scheduled package upgrades once a week, even if my homelab is offline
- scheduled backups of all systems once a week
- email, when something goes wrong

The process, is the following:

- Every wednesday at 02:50, the homeautomation Raspberry boots the MinisForum backup machine and the ThinkCentre, if they are not online
- On the MinisForum machine, a VM "maintenance" with a bunch of scripts will boot
- at 03:00, the maintenance machines `cron` jobs will start a [maintenance script](https://gitlab.com/tsmt/homelab_devops/). I made this script publicly available. See on gitlab, how it exactly works.
- If the script fails for some reason, an **Email** will be sent to my private mail addres with the fail logs attached. This is done with the `mutt` program on linux. To do this, I use the SMTP of my own mail provider.

This system is not much, but it's now capable of doing everything that was possible before my [home automation reduction journey](/reducing_complexity_ha/). I'm not a big expert of bash scripts since I'm professionally a Developer, not a DevOps person, but I was able to write a well extendable library of bash scripts in **not even a whole working day**. Let's see, how reliable it is. At least also here, I removed a lot of software from my stack.

### Before
- Linux
- Bash
- Ansible + dependencies
- Python (used by ansible) + dependencies
- Ansible Tower as UI (with all it's dependencies)
- NodeRED with Telegram + Email to send alerts

### Now
- Linux
- Bash
- cron
- mutt

Where "mutt" is even the only thing that needs to be installed after creating a new machine.

I think I was very successful with this, and it actually makes me happy to see how simple some things can be.

Links:
- [My HA maintenance scripts Repo on GitLab](https://gitlab.com/tsmt/homelab_devops/)
- [My home automation reduction journey](/reducing_complexity_ha/)