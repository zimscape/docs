# Command Line Interface

The Command Line Interface is intended to make it easier for software developers in particular
to deploy software to development environments on zimscape.com. 

### Git

The Zimscape CLI requires that you have Git installed before hand as we use Git to move your source code. 
If you're new to Git you can have a quick look at it 
[here](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control).


[![Version](https://img.shields.io/npm/v/zimscape-cli.svg)](https://npmjs.org/package/zimscape-cli)
[![Downloads/week](https://img.shields.io/npm/dw/zimscape-cli.svg)](https://npmjs.org/package/zimscape-cli)
[![License](https://img.shields.io/npm/l/zimscape-cli.svg)](https://github.com/vuskhoza/zimscape-cli/blob/master/package.json)

<!-- toc -->
* [Installation](#installation)
* [Available Commands](#available-commands)
<!-- tocstop -->
### Installation
<!-- usage -->
```sh-session
$ npm install -g zimscape-cli
```

Verify your version 

```
$ zimscape -v
zimscape-cli/0.0.8 linux-x64 node-v11.10.1
```
<!-- usagestop -->
### Available Commands

<!-- commands -->
- [`zimscape login`](#zimscape-login)
- [`zimscape ls`](#zimscape-ls)
- [`zimscape push [REMOTE]`](#zimscape-push-remote)
- [`zimscape logs [REMOTE]`](#zimscape-logs-remote)

### `zimscape login`

Login to zimscape.com. Here you'll be required to use the email address and password used to create a zimscape
account.

```
$ zimscape login
Login in to your Zimscape.com account
Account email: email@gmail.com
Password: *********
one moment please...
Login successful :)
```

### `zimscape ls`

List development environments on Zimscape.com. This command give you a snapshot of all development environments
under all currently active projects.

```
$ zimscape ls
one moment please...
Product   Project                       Domain                        Type       Status 
PHP       zimscape.com                  php.zimscape.com              staging    active 

```

### `zimscape push [REMOTE]`

Uses git to push current code base to chosen environment. This command is simply a wrapper of the common 
`git push [remote] [branch]` command. To successfully push to a Zimscape remote you have to have a branch 
named after the respective environment type e.g. in the example above, a `staging` branch. Zimscape
currently offers three environments per project `dev`, `staging` and `master`.

```
$ zimscape push php.zimscape.com
one moment please...
Counting objects: 4, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 355 bytes | 355.00 KiB/s, done.
Total 4 (delta 3), reused 0 (delta 0)
remote: Ref refs/heads/staging received. Deploying 'staging' branch...
remote: Already on 'staging'
remote: Ref refs/heads/staging received and successfully deployed to staging
remote: Stopping service...
remote: Stopping daemon: php-staging.
To gitserver.zimscape.com:php-project.git
   85b2d82..347d647  staging -> staging
```

In the absence of the matching branch you'll encounter an error:

```
one moment please...
error: src refspec staging does not match any.
error: failed to push some refs to 'zimscape@gitserver.zimscape.com:php-project.git'
```

One way to create a new branch from a branch with your latest commits would be:

```
$ git checkout -b staging
```

This command creates a branch `staging` identical to the current branch you're on.

### `zimscape logs [REMOTE]`

Tail logs for chosen environment. This commands gives you insight into what's happening with your service
as well as any other services running in the container environment. The logs returned are an amalgamation of
as most of the activity that happens within an applications environment for example deployment logs, web server
logs and service daemon logs.

```
$ zimscape logs php.zimscape.com
one moment please...
Sun Apr 19 12:36:31 UTC 2020 Ref refs/heads/staging received. Deploying 'staging' branch...
Sun Apr 19 12:36:31 UTC 2020 Ref refs/heads/staging received and successfully deployed to staging

```

<!-- commandsstop -->
