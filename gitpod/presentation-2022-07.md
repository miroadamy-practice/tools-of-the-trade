---
marp: true
theme: gaia
author: miro.adamy@gmail.com
size: 16:9

---

# Gitpod

Ephemeral development environment in the container

See https://www.gitpod.io/docs

To get started, install extension for Github, Bitbucket and Gitlab

Works with browser extension in any [Chromium-based browser](https://chrome.google.com/webstore/detail/gitpod-online-ide/dodmmooeoklaejobgleioelladacbeki) (Google Chrome, Microsoft Edge, Brave) or in Firefox

---

# Open a workspace

Then open a repository in container from the browser:

![w:18cm h:10cm](./img/git-repo.png)


---
# Open a workspace - no plugin

Prefix URL with https://gitpod.io/#
Must be logged in both - BB and Gitpod
Use Web URL, not clone URL:

* OK : https://bitbucket.org/pivotree/oneclick-security/src/master/ 
* Not OK: https://miro-adamy-PVT@bitbucket.org/pivotree/oneclick-security.git

`https://gitpod.io/#https://bitbucket.org/
 pivotree/oneclick-security/src/master/`
---
# Environment 

It is essentially an Visual Studio Code in the cloud.

![w:18cm h:10cm](./img/gitpod-ui.png)


---

# Its free(mium)

![w:18cm h:10cm](./img/plans.png)

---

# Variables

Available to one or more containers

![w:18cm h:10cm](./img/variables.png)

---