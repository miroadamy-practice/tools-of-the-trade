# Gitpod

Ephemeral development environment in the container

See https://www.gitpod.io/docs

To get started, install extension for Github, Bitbucket and Gitlab - Gitpod browser extension in any [Chromium-based browser](https://chrome.google.com/webstore/detail/gitpod-online-ide/dodmmooeoklaejobgleioelladacbeki) such as Google Chrome, Microsoft Edge, Brave, and others, or in Firefox

## Getting started

Then open a repository in container from the browser:

![](./img/git-repo.png)

to get to environment like this:

![](./img/gitpod-ui.png)

It is essentially an Visual Studio Code in the cloud.

![](./img/plans.png)

![](./img/integrations.png)

### Logo for Readme

Code to contribute:

<a href="https://gitpod.io/#<your-project-url>">
  <img
    src="https://img.shields.io/badge/Contribute%20with-Gitpod-908a85?logo=gitpod"
    alt="Contribute with Gitpod"
  />
</a>


```html
<a href="https://gitpod.io/#<your-project-url>">
  <img
    src="https://img.shields.io/badge/Contribute%20with-Gitpod-908a85?logo=gitpod"
    alt="Contribute with Gitpod"
  />
</a>
```

Or

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/miroadamy-practice/tools-of-the-trade.git)

```markdown
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/miroadamy-practice/tools-of-the-trade.git)
```

## Configuration

You can install packages - use sudo

`sudo apt-get install cowsay`

Installing packages with Sudo:

```sh
gitpod /workspace/tools-of-the-trade (cloud) $ cowsay
bash: cowsay: command not found
gitpod /workspace/tools-of-the-trade (cloud) $ sudo apt-get install cowsay
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libtext-charwidth-perl
Suggested packages:
  filters cowsay-off
The following NEW packages will be installed:
  cowsay libtext-charwidth-perl
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 28.4 kB of archives.
After this operation, 135 kB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 libtext-charwidth-perl amd64 0.04-10 [9,872 B]
Get:2 http://archive.ubuntu.com/ubuntu focal/universe amd64 cowsay all 3.03+dfsg2-7 [18.5 kB]
Fetched 28.4 kB in 0s (92.6 kB/s) 
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libtext-charwidth-perl.
(Reading database ... 36091 files and directories currently installed.)
Preparing to unpack .../libtext-charwidth-perl_0.04-10_amd64.deb ...
Unpacking libtext-charwidth-perl (0.04-10) ...
Selecting previously unselected package cowsay.
Preparing to unpack .../cowsay_3.03+dfsg2-7_all.deb ...
Unpacking cowsay (3.03+dfsg2-7) ...
Setting up libtext-charwidth-perl (0.04-10) ...
Setting up cowsay (3.03+dfsg2-7) ...
Processing triggers for man-db (2.9.1-1) ...
gitpod /workspace/tools-of-the-trade (cloud) $ cowsay Hello Gitpod
 ______________
< Hello Gitpod >
 --------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```


### Docker and Docker-Compose support

Default image: `gitpod/workspace-full`

See [this](https://github.com/gitpod-io/workspace-images/blob/481f7600b725e0ab507fbf8377641a562a475625/dazzle.yaml#L18)


```
- name: full
      ref:
      - base
      chunks:
        - lang-c
        - lang-clojure
        - lang-go:1.17.5
        - lang-java:11
        - lang-node:16.13.2
        - lang-python
        - lang-ruby:2.7
        - lang-rust
        - tool-brew
        - tool-nginx
        - tool-nix:2.3.14
```

Add own tooling:

```
# You can find the new timestamped tags here: https://hub.docker.com/r/gitpod/workspace-full/tags
FROM gitpod/workspace-full:2022-05-08-14-31-53

# Install custom tools, runtime, etc.
RUN brew install fzf
```

or use base and go custom:

```
# You can find the new timestamped tags here: https://hub.docker.com/r/gitpod/workspace-base/tags
FROM gitpod/workspace-base:2022-05-08-14-31-53

# Install custom tools, runtime, etc.
# base image only got `apt` as the package manager
# install-packages is a wrapper for `apt` that helps skip a few commands in the docker env.
RUN sudo install-packages shellcheck tree llvm
```

For more [see this](https://www.gitpod.io/docs/config-docker)

```
gitpod /workspace/tools-of-the-trade (cloud) $ docker --version
Docker version 20.10.17, build 100c701

gitpod /workspace/tools-of-the-trade (cloud) $ docker-compose version
Docker Compose version 2.6.1-gitpod.0

gitpod /workspace/tools-of-the-trade (cloud) $ go version
go version go1.18.4 linux/amd64

gitpod /workspace/tools-of-the-trade (cloud) $ npm version
{
  npm: '8.11.0',
  node: '16.16.0',
  v8: '9.4.146.24-node.21',
  uv: '1.43.0',
  zlib: '1.2.11',
  brotli: '1.0.9',
  ares: '1.18.1',
  modules: '93',
  nghttp2: '1.47.0',
  napi: '8',
  llhttp: '6.0.7',
  openssl: '1.1.1q+quic',
  cldr: '40.0',
  icu: '70.1',
  tz: '2021a3',
  unicode: '14.0',
  ngtcp2: '0.1.0-DEV',
  nghttp3: '0.1.0-DEV'
}

gitpod /workspace/tools-of-the-trade (cloud) $ node --version
v16.16.0

gitpod /workspace/tools-of-the-trade (cloud) $ clojure
Picked up JAVA_TOOL_OPTIONS:  -Xmx3435m
Clojure 1.10.3
user=> *clojure-version*
{:major 1, :minor 10, :incremental 3, :qualifier nil}
user=> 

gitpod /workspace/tools-of-the-trade (cloud) $ rustc --version
rustc 1.62.1 (e092d0b6b 2022-07-16)

gitpod /workspace/tools-of-the-trade (cloud) $ python --version
Python 3.8.13

gitpod /workspace/tools-of-the-trade (cloud) $ pip --version
pip 22.2 from /home/gitpod/.pyenv/versions/3.8.13/lib/python3.8/site-packages/pip (python 3.8)

gitpod /workspace/tools-of-the-trade (cloud) $ ruby --version
ruby 3.1.2p20 (2022-04-12 revision 4491bb740a) [x86_64-linux]

```

### Persistence

* everything under `/workspace/` is saved, rest is not

```
gitpod /workspace/tools-of-the-trade (cloud) $ ll /workspace/
total 0
drwxr-xr-x  8 gitpod gitpod 124 Jul 30 16:33 ./
drwxr-xr-x  1 root   root   149 Jul 30 16:33 ../
drwxr-xr-x  3 gitpod gitpod  17 Jul 30 16:17 .cargo/
drwx--x--- 14 root   root   185 Jul 30 16:33 .docker-root/
drwxr-xr-x  2 gitpod gitpod  19 Jul 30 16:17 .gitpod/
drwxr-xr-x  3 gitpod gitpod  22 Jul 30 16:17 .pyenv_mirror/
drwxr-xr-x  4 gitpod gitpod  82 Jul 30 16:17 tools-of-the-trade/
drwx------  4 gitpod gitpod  36 Jul 30 16:17 .vscode-remote/


gitpod /workspace/tools-of-the-trade (cloud) $ du -sh /*
0       /bin
0       /boot
28K     /core.2536
0       /dev
du: cannot read directory '/etc/polkit-1/localauthority': Permission denied
du: cannot read directory '/etc/ssl/private': Permission denied
4.5M    /etc
3.6G    /home
531M    /ide
0       /lib
0       /lib32
0       /lib64
0       /libx32
0       /media
0       /mnt
540M    /nix
0       /opt
0       /proc
du: cannot read directory '/root': Permission denied
0       /root
du: cannot read directory '/run/docker': Permission denied
12K     /run
0       /sbin
0       /srv
0       /sys
4.0K    /tmp
2.4G    /usr
76M     /var
du: cannot read directory '/workspace/.docker-root': Permission denied
62M     /workspace

gitpod /workspace/tools-of-the-trade (cloud) $ df -h
Filesystem        Size  Used Avail Use% Mounted on
/.workspace/mark  1.5T  484G 1016G  33% /
tmpfs              64M     0   64M   0% /dev
/dev/sda1         512G   15G  498G   3% /dev/termination-log
shm                64M     0   64M   0% /dev/shm
tmpfs              32G     0   32G   0% /sys/firmware
/dev/md42          28G   62M   28G   1% /workspace
/dev/md44         1.5T  484G 1016G  33% /etc/hostname
tmpfs              32G  4.0K   32G   1% /tmp
tmpfs              32G     0   32G   0% /proc/acpi
tmpfs              64M     0   64M   0% /proc/keys
tmpfs              32G     0   32G   0% /proc/scsi
```

### Variables

![](./img/variables.png)

Default variables:

* GITPOD_WORKSPACE_ID: The Universally Unique Identifier (UUID) associated with the workspace.
* GITPOD_WORKSPACE_URL: The unique URL of the workspace.
* GITPOD_REPO_ROOT: Path to the directory where your git repository was cloned inside the workspace.

```
 $ env | grep GITPOD_
GITPOD_REPO_ROOT=/workspace/tools-of-the-trade
GITPOD_ANALYTICS_WRITER=segment
GITPOD_PREVENT_METADATA_ACCESS=true
GITPOD_OWNER_ID=0c655b71-ab9f-472d-a4a0-94d9187d7bd8
GITPOD_WORKSPACE_ID=miroadamypr-toolsofthet-yj0hmgx11u1
GITPOD_INTERVAL=30000
GITPOD_WORKSPACE_CONTEXT_URL=https://github.com/miroadamy-practice/tools-of-the-trade
GITPOD_WORKSPACE_CLASS=default
GITPOD_INSTANCE_ID=014e3bf2-2a8d-4918-99f1-5122cf85adbb
GITPOD_REPO_ROOTS=/workspace/tools-of-the-trade
GITPOD_WORKSPACE_URL=https://miroadamypr-toolsofthet-yj0hmgx11u1.ws-eu54.gitpod.io
GITPOD_THEIA_PORT=23000
GITPOD_WORKSPACE_CONTEXT={"isFile":false,"path":"","title":"miroadamy-practice/tools-of-the-trade - main","ref":"main","refType":"branch","revision":"a7b330d51589a35f23b29bbe6dc80d2101b73898","repository":{"cloneUrl":"https://github.com/miroadamy-practice/tools-of-the-trade.git","host":"github.com","name":"tools-of-the-trade","owner":"miroadamy-practice","private":false},"normalizedContextURL":"https://github.com/miroadamy-practice/tools-of-the-trade","checkoutLocation":"tools-of-the-trade","snapshotBucketId":"workspaces/miroadamypr-toolsofthet-nk3rtfjxf0s/snapshot-1659217496072860705.tar@gitpod-prod-user-0c655b71-ab9f-472d-a4a0-94d9187d7bd8","prebuildWorkspaceId":"8e7ea3e3-2ae5-4883-82f3-6ba24c7ca333","wasPrebuilt":true}
GITPOD_CLI_APITOKEN=fU.ZlAT3LMkWZ5OfsjhPIGwdyY44oNGT
GITPOD_WORKSPACE_CLUSTER_HOST=ws-eu54.gitpod.io
GITPOD_GIT_USER_NAME=Miro Adamy
GITPOD_MEMORY=3435
GITPOD_ANALYTICS_SEGMENT_KEY=bUY8IRdJ42KjLOBS9LoIHMYFBD8rSzjU
GITPOD_HOST=https://gitpod.io
GITPOD_IDE_ALIAS=code
GITPOD_GIT_USER_EMAIL=miro.adamy@gmail.com
GITPOD_TASKS=[{"name":"Dev","before":"npm version\nnode --version\n","init":"cd demo-app \nnpm install\ngp sync-done npm-inst \n","command":"echo 'Ready to develop'"},{"name":"Server","before":"npm version\nnode --version\n","init":"echo 'waiting init' && gp sync-await npm-inst","command":"cd demo-app\nnpm start\n"}]

```

Gitpod supports encrypted, user-specific environment variables. They are stored as part of your user settings and can be used to set access tokens, or pass any other kind of user-specific information to your workspaces.

Setting the user-specific vars:


* `gp env foo=bar` . => for the NEXT RUN, gp does NOT see current variables
* `eval $(gp env -e foo=bar)` => update the variable AND terminal
* `eval $(gp env -e)` - refresh terminal

Set using Variables in account settings: https://gitpod.io/variables

Also: PROJECT SPECIFIC - will override user specific (account)

Note that gp env only sets user specific

Terminal specific - in `env:` part of `tasks`

```
tasks:
  - name: Example of passing an environment variable to a command
    env:
      PRINT_ME: "Hello World!"
    command: echo "$PRINT_ME"
```

### Starting templates:

* https://www.gitpod.io/docs/quickstart/typescript
* https://www.gitpod.io/docs/quickstart/go
* https://www.gitpod.io/docs/quickstart/docker-compose
* https://www.gitpod.io/docs/quickstart/flask

### Workspaces - in Dashboard

#### Download
I got:

miroadamy-ekswithcdk-96vdowsc3jv, 90 MB - what is it

#### Connect with:

* ssh key

Token:

```
➜  WorkNotes-Logseq git:(main) ssh 'miroadamypr-toolsofthet-jj4r1g8rk19#yB-_QbqZgZTCncj0Jh2_OJAD12yseERu@miroadamypr-toolsofthet-jj4r1g8rk19.ssh.ws-eu54.gitpod.io'
The authenticity of host 'miroadamypr-toolsofthet-jj4r1g8rk19.ssh.ws-eu54.gitpod.io (35.241.191.216)' can't be established.
ECDSA key fingerprint is SHA256:N8Chm8YwSunjJ4KZVYHT4oq1uBFA8mXXLkxbl7wfpa0.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:23: miroadamy-typescripting-lbcwa6aueyn.ssh.ws-eu46.gitpod.io
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'miroadamypr-toolsofthet-jj4r1g8rk19.ssh.ws-eu54.gitpod.io' (ECDSA) to the list of known hosts.
Welcome to Gitpod: Always ready to code. Try the following commands to get started:

	gp tasks list         List all your defined tasks in .gitpod.yml
	gp tasks attach       Attach your terminal to a workspace task

	gp ports list         Lists workspace ports and their states
	gp stop               Stop current workspace
	gp help               To learn about the gp CLI commands

For more information, see the Gitpod documentation: https://gitpod.io/docs

gitpod /workspace/tools-of-the-trade (cloud) $ gp tasks list
|             TERMINAL ID              |     NAME      |  STATE  |
|--------------------------------------|---------------|---------|
| 3a0aad7a-d191-4e88-9754-52f23cbefa79 | Gitpod Task 1 | running |
gitpod /workspace/tools-of-the-trade (cloud) $ gp ports list
| PORT |   STATUS   |                                URL                                 | NAME & DESCRIPTION |
|------|------------|--------------------------------------------------------------------|--------------------|
| 3000 | not served | https://3000-miroadamypr-toolsofthet-jj4r1g8rk19.ws-eu54.gitpod.io |                    |
gitpod /workspace/tools-of-the-trade (cloud) $ gp help
Command line interface for Gitpod

Usage:
  gp [command]

Available Commands:
  env                 Controls user-defined, persistent environment variables.
  help                Help about any command
  init                Create a Gitpod configuration for this project.
  open                Opens a file in Gitpod
  ports               Interact with workspace ports.
  preview             Opens a URL in the IDE's preview
  snapshot            Take a snapshot of the current workspace
  stop                Stop current workspace
  sync-await          Awaits an event triggered using gp sync-done
  sync-done           Notifies the corresponding gp sync-await calls that this event has happened
  tasks               Interact with workspace tasks
  timeout             Interact with workspace timeout configuration
  top                 Display usage of workspace resources (CPU and memory)
  url                 Prints the URL of this workspace
  version             Prints the version of the CLI

Flags:
  -h, --help   help for gp

Use "gp [command] --help" for more information about a command.

```
and with key - I do have a key
```
➜  WorkNotes-Logseq git:(main) ssh 'miroadamypr-toolsofthet-jj4r1g8rk19@miroadamypr-toolsofthet-jj4r1g8rk19.ssh.ws-eu54.gitpod.io'
Welcome to Gitpod: Always ready to code. Try the following commands to get started:

	gp tasks list         List all your defined tasks in .gitpod.yml
	gp tasks attach       Attach your terminal to a workspace task

	gp ports list         Lists workspace ports and their states
	gp stop               Stop current workspace
	gp help               To learn about the gp CLI commands

For more information, see the Gitpod documentation: https://gitpod.io/docs
```




## Setup automation

```
gitpod /workspace/tools-of-the-trade (cloud) $ gp
Command line interface for Gitpod

Usage:
  gp [command]

Available Commands:
  env                 Controls user-defined, persistent environment variables.
  help                Help about any command
  init                Create a Gitpod configuration for this project.
  open                Opens a file in Gitpod
  ports               Interact with workspace ports.
  preview             Opens a URL in the IDE's preview
  snapshot            Take a snapshot of the current workspace
  stop                Stop current workspace
  sync-await          Awaits an event triggered using gp sync-done
  sync-done           Notifies the corresponding gp sync-await calls that this event has happened
  tasks               Interact with workspace tasks
  timeout             Interact with workspace timeout configuration
  top                 Display usage of workspace resources (CPU and memory)
  url                 Prints the URL of this workspace
  version             Prints the version of the CLI

Flags:
  -h, --help   help for gp

Use "gp [command] --help" for more information about a command.
gitpod /workspace/tools-of-the-trade (cloud) $ gp env
DOCKER_PASSWORD=c5a9506d-3517-4ae6-891d-5b635f714ed8
BBUSER=miro-adamy-pvt
AWS_SECRET_ACCESS_KEY=7************************K
AWS_ACCESS_KEY_ID=AKIAZ7MM55Y5CGYPT24B
BBPASSWORD=9XVdvEkTuyPAGepgWLxP
DOCKER_USERNAME=miroadamy
BBURL=https://api.bitbucket.org/2.0
gitpod /workspace/tools-of-the-trade (cloud) $ gp url
https://miroadamypr-toolsofthet-jj4r1g8rk19.ws-eu54.gitpod.io
gitpod /workspace/tools-of-the-trade (cloud) $ gp version
main.4084
gitpod /workspace/tools-of-the-trade (cloud) $ gp init

```

This generates `.gitpod.yml`

Member of tasks - dash => new terminal. Tasks run on top of Docker images (default or custom)

With Gitpod, you have the following three types of tasks:

* before: Use this for tasks that need to run before init and before command. For example, customize the terminal or install global project dependencies.
* init: Use this for heavy-lifting tasks such as downloading dependencies or compiling source code.
* command: Use this to start your database or development server.


Full syntax:

```
tasks:
  - name: First Terminal
    before: echo Before
    init: echo 'Init script'
    command: echo ' start script - runs on each command'
  - name: Second Terminal
    before echo 'Before Second'
    init: echo 'Second init'
    command: echo ' start in second'

```

Push and open new Gitpod.

Output:

```
Before
Init script
 start script - runs on each command
```

```
Before Second
Second init
 start in second
```

After enabling Prebuild (see https://www.gitpod.io/docs/prebuilds), I get this:

```
gitpod /workspace/tools-of-the-trade (main) $ {
> echo Before
> } && {
> echo 'Init script'
> }; exit
Before
Init script
exit

🤙 This task ran as a workspace prebuild

 start script - runs on each command

 ---
gitpod /workspace/tools-of-the-trade (main) $ {
> echo 'Before Second'
> } && {
> echo 'Second init'
> }; exit
Before Second
Second init
exit

🤙 This task ran as a workspace prebuild

 start in second


```

### Prebuilds

It uses applications - https://github.com/apps/gitpod-io/installations/new

Depends on SCM - 

Any change made in init outside of `/workspace/` is lost

Enable prebuild: New Project - https://gitpod.io/new

Prebuild (if enabled): BEFORE + INIT

New Workspace:

* with prebuild: BEFORE + COMMAND
* no prebuild:  BEFORE + INIT + COMMAND

Restart workspace (with or w/o Prebuild): BEFORE + COMMAND

Start snapshot: BEFORE + COMMAND


### Multi-line tasks

```
tasks:
  - name: Dependencies & Database
    init: |
      npm install
      npm run configure-database
    command: npm run dev
```

### Tasks ordering

```
tasks:
  - name: Rails
    init: >
      bundle install &&
      yarn install --check-files &&
      rails db:setup &&
      gp sync-done bundle # 'bundle' is an arbitrary name
    command: rails server

  - name: Webpack
    init: gp sync-await bundle # wait for the above 'init' to finish
    command: bin/webpack-dev-server

  - name: Redis
    init: gp sync-await bundle
    command: redis-server

  - name: Sidekiq
    init: gp sync-await bundle
    command: sidekiq
```

Wait for the port:

```
tasks:
  - name: Dev Server
    init: npm install
    command: npm run dev

  - name: e2e Tests
    command: |
      gp ports await 3000
      npm run test
```

### Ports

We can open port in `ports` part

```
$ gp url
https://miroadamypr-toolsofthet-yj0hmgx11u1.ws-eu54.gitpod.io

 $ gp url 3000
https://3000-miroadamypr-toolsofthet-yj0hmgx11u1.ws-eu54.gitpod.io

 $ gp ports list
| PORT |     STATUS     |                                URL                                 | NAME & DESCRIPTION |
|------|----------------|--------------------------------------------------------------------|--------------------|
| 3000 | open (private) | https://3000-miroadamypr-toolsofthet-yj0hmgx11u1.ws-eu54.gitpod.io |                    |

```

Go to URL - https://3000-miroadamypr-toolsofthet-yj0hmgx11u1.ws-eu54.gitpod.io/, needs to be logged in

The property onOpen configures port opening behaviors:

* notify (default) - Show a notification for newly detected ports.
* open-preview - Open the port URL in a preview within the editor or IDE.
* open-browser - Open the port URL a browser tab.
* ignore - Ignore default behavior (notify).

Shows all ports in Remote Explorer of IDE, can switch to public 
(or use visibility: public)

The config works for ranges or ports:

```
ports:
  - port: 3000-8999
    onOpen: ignore
```

TODO: Port forwarding - need to try it out, do not quite understand this

https://www.gitpod.io/docs/config-ports

# CONTINUE IN: [README-2](./README-2.md)
