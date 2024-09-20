---
  layout: post
  title: How to Manage Multiple Git Accounts on One Machine (macOS)
  featured: true
  author: ben
  tags: [GitHub, Gitlab, macOS, plist]
  image: '/images/posts/multiple-git-accounts-on-one-machine.png'
---


# Final & Working guide for living with multiple Git accounts

permlink: `https://gist.github.com/benjaminv/092f3d87afcbe589ca2a03fa9358050f`  
first publish: `Jun 13, 2022`  
updated: `Sep 20, 2024`

## 1. Generate a new SSH key-pair for each of Git account.

```bash
$ ssh-keygen -t rsa -b 4096 -C "benvee@gmail.com" -f ~/.ssh/id_rsa_benjaminv_github
$ ssh-keygen -t rsa -b 4096 -C "ben@mycompany.com.au" -f ~/.ssh/id_rsa_bendhu_github
$ ssh-keygen -t rsa -b 4096 -C "ben@mycompany.com.au" -f ~/.ssh/id_rsa_benhu1_gitlab

# The -C option is a comment to help identify the key.
# The -f option specifies the file name for the key pair.
```

  

## 2. Add the SSH keys to your SSH-agent

```bash
$ ssh-add ~/.ssh/id_rsa_benjaminv_github
$ ssh-add ~/.ssh/id_rsa_bendhu_github
$ ssh-add ~/.ssh/id_rsa_benhu1_gitlab
```

Verified by `$ ssh-add -l`, the result should look like follows,

```bash
# work github
4096 SHA256:UY1sId6R**********************6faASkLkUI9BY ben@mycompany.com.au (RSA)
# work gitlab
4096 SHA256:+k2fsrBl**********************olO/obXFSNOA ben@mycompany.com.au (RSA)
# personal github
4096 SHA256:FcTyaa8T**********************CfKUt5J1lMIIY benvee@gmail.com (RSA)
```

And make these persistent for new terminal sessions by enable `AddKeysToAgent yes` in **Section 4**.
## 3. Add the public keys to your GitHub accounts respectively.
Visit https://github.com/settings/keys and use "New SSH Key"

```bash
# copy public key to clipboard without opening it
% pbcopy < ~/.ssh/id_rsa_benjaminv_github.pub
% pbcopy < ~/.ssh/id_rsa_bendhu_github.pub
% pbcopy < ~/.ssh/id_rsa_benhu1_gitlab.pub
```

paste the public key to your GitHub account and add a name to identify it.
## 4. Managing Overall SSH-Keys
By default the file needed is located at `~/.ssh/config`

add following lines to the end of the file

```bash
# Work github account
Host github.com
HostName github.com
User git
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_ed25519_work_github

# Work gitlab account
Host gitlab.com
HostName gitlab.com
User bgit
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_rsa_gitlab

# Personal github account
Host github.com.personal
HostName github.com
User git
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_rsa_benjaminv_github
```

## 5. Which Account to Use by Default?
The global Git configuration file is stored at `$HOME/.gitconfig` on all platforms.
This defines the default values for all Git commands.

```bash
[user]
email = ben@mycompany.com.au
name = ben
[init]
defaultBranch = main # stop using master as default branch
```

to edit it,

```bash
$ git config --global user.name "ben"
$ git config --global user.email "ben@mycompany.com.au"
```
## 6. Config User Account for Project
**this whole Section 6 is no longer needed after you complete Section 9**
### 6.1 Cloning GitHub repositories using no-default accounts
instead of using `github.com:benjaminv/benjaminv`,
use `github.com.personal:benjaminv/benjaminv`
to match the configuration in `~/.ssh/config`

```bash
% git clone git@github.com.personal:benjaminv/react-crash-2021.git
# mind the HostName github.com.personal
```
### 6.2 Update the local user
use `cat .git/config` to check the current git config
Then

```bash
% git config --local user.name "ben"
% git config --local user.email "benvee@gmail.com"
```
### 6.3 Update the remote git url
for repo already cloned before this configuration

```bash
% git remote set-url origin "git@github.com.personal:benjaminv/starting-react.git"
# mind the .personal added to github.com
```
### 6.4 Add remote git url
for newly created repo and local project

```bash
% git remote add origin "git@github.com.personal:benjaminv/starting-react.git"
# mind the .personal added to github.com
```
### 6.5 verify the local repo config

```bash
% cat .git/config

# It looks like following

[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]       # @github.com.personal:
        url = git@github.com.personal:benjaminv/starting-react.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
[user]                  # current user
        name = ben
        email = benvee@gmail.com
```

## 7. Confirming the Other Account is Working

```bash
% ssh -T git@github.com.personal
# Hi benjaminv! You've successfully authenticated, but GitHub does not provide shell access.
# Mind the username here, it is benjaminv NOT my default Git account name
```
## 8. Push to remote repository

```bash
# change local branch name if necessary
% git branch -M <new-branch>
# push to remote repository
% git push origin <new-branch>
```
## 9. Want to auto-match of git profile? Pro only

Say, all my codes are in `~/Documents/__Code__` and my work projects sit in `~/Documents/__Code__/Work/` while my hobby projects sit in `~/Documents/__Code__/Ben/`. How may I make my current `Terminal` shell knowing that which `git` user should it use when I conduct `git` operations, such as `git fetch` `git pull` `git push`?

The ultimate setup will be like this,
### Step 1: Edit your global (default) git config

```bash
# path: ~/.gitconfig
[user]
        email = ben@mycompany.com.au
        name = ben
[init]
        defaultBranch = main
[http]
        postBuffer = 500M
        maxRequestBuffer = 100M
[core]
        compression = 0
[credential]
        helper = store

[includeIf "gitdir:~/Documents/__Codes__/Work/"]
    path = ~/Documents/__Codes__/Work/.gitconfig

[includeIf "gitdir:~/Documents/__Codes__/Ben/"]
    path = ~/Documents/__Codes__/Ben/.gitconfig
```

`!important`

- if your `OS` is case-sensitive you will have to match the file path by `gitdir:~/My_Project_Path` no space in the string
- if your `OS` is NOT case-sensitive you might consider using `/i` param to allow free cases in the path string, e.g., `gitdir/i:~/My_Project_Path` no space in
- according to [docs](#) you need the tailing `/` at end of path, i.e. `~/Documents/__Codes__/Work/`
### Step 2: Make your local (path specific) git configs

Working git user:

```bash
# path: ~/Documents/__Codes__/Work/.gitconfig

[user]
        email = ben@mycompany.com.au
        name = ben
[init]
        defaultBranch = main
[http]
        postBuffer = 500M
        maxRequestBuffer = 100M
[core]
        compression = 0
[credential]
        helper = store
```

  

Personal git user:

```bash
# path: ~/Documents/__Codes__/Ben/.gitconfig
[user]
        email = benvee@gmail.com
        name = benjaminv
[init]
        defaultBranch = main
[http]
        postBuffer = 500M
        maxRequestBuffer = 100M
[core]
        compression = 0
[credential]
        helper = store
[url "git@github.com.personal"]
        insteadOf = git@github.com
```

Then you are good to go. Thankfully, this works for both CLI and VSCode GUI (yes!!! regardless which github user you logged in on VSCode)
#### Although...
There is one more glitch here, VSCode GUI uses `https` protocols instead of `ssh` ONLY when you push local repo directly to GitHub by automatically create a public / private repository for you. 
	Instead of adding `git@github.com:benjaminv/shopify-dawn-theme.git` as your repo remote, it adds `https://github.com/benjaminv/shopify-dawn-theme.git`
	you can verify this by `git remote -v`

##### The real fix
Update in VSCode settings git protocol from default `https` to `ssh`
![](https://i.imgur.com/Wd5YNx4.png)


##### ~~The fix~~
````
git remote set-url origin git@github.com:benjaminv/shopify-dawn-theme.git
````

##### TL;DR
```
// desired, so that url matching in ~/Documents/__Codes__/Ben/.gitconfig can work like a magic

git remote -v
...
[remote "origin"]
        url = git@github.com:benjaminv/shopify-dawn-theme.git
        fetch = +refs/heads/*:refs/remotes/origin/*
...
```

```
// wrong, this will be different from all (global, then company and personal) .gitconfig profiles

git remote -v
...
[remote "origin"]
        url = https://github.com/benjaminv/shopify-dawn-theme.git
        fetch = +refs/heads/*:refs/remotes/origin/*
...

// it will result this,
> git push -u origin main
remote: Repository not found.
fatal: repository 'https://github.com/benjaminv/shopify-shopify-theme.git/' not found
```

![](https://i.imgur.com/OBoYB8e.png)



### p.s.
Don't confuse yourself with **git repo authentication** and your **local git user** switch.
### git repo authentication
The first part of this guide is dealing with **git authentication**, regardless your current path, check with `pwd`, you still need add `.personal` to git hostname, as we defined in `ssh` auth profiles, e.g, `git clone git@github.com.personal:benjaminv/my-hobby-project.git` to connect to your repo
### local git user switch by path
The section `9. Want to auto-match of git profile? Pro only` is dealing with local git user switch.


## References
- https://gist.github.com/jexchan/2351996?permalink_comment_id=4456452#gistcomment-4456452