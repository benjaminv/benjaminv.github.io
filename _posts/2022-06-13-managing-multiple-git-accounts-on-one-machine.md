---
  layout: post
  title: How to Manage Multiple Git Accounts on One Machine (macOS)
  featured: true
  author: ben
  tags: [GitHub, Gitlab, macOS, plist]
  image: '/images/posts/multiple-git-accounts-on-one-machine.png'
---


# Live with multiple Git accounts

## 1. Generate a new SSH key-pair for each of Git account.

```bash
  $ ssh-keygen -t rsa -b 4096 -C "benvee@gmail.com" -f ~/.ssh/id_rsa_benjaminv_github
  $ ssh-keygen -t rsa -b 4096 -C "ben@wearedigital.com.au" -f ~/.ssh/id_rsa_bendhu_github
  $ ssh-keygen -t rsa -b 4096 -C "ben@wearedigital.com.au" -f ~/.ssh/id_rsa_benhu1_gitlab

  # The -C option is a comment to help identify the key.
  # The -f option specifies the file name for the key pair.
```

## 2. Add the SSH keys to your SSH-agent

```bash
  $ ssh-add ~/.ssh/id_rsa_benjaminv_github
  $ ssh-add ~/.ssh/id_rsa_bendhu_github
  $ ssh-add ~/.ssh/id_rsa_benhu1_gitlab
```

And make these persistent for new terminal sessions by enable `AddKeysToAgent yes` in **STEP 4**.

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
  IdentityFile ~/.ssh/id_ed25519_werd_github

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

The global Git configuration file is stored at $HOME/.gitconfig on all platforms.

This defines the default values for all Git commands.

```bash
[user]
  email = ben@wearedigital.com.au
  name = ben
[init]
  defaultBranch = main    # stop using master as default branch
```

to edit it, 
```bash
$ git config --global user.name "ben"
$ git config --global user.email "ben@wearedigital.com.au"
```

## 6. Cloning GitHub repositories using no-default accounts

instead of using `github.com:benjaminv/benjaminv`,   
use `github.com.personal:benjaminv/benjaminv`   
to match the configuration in `~/.ssh/config`

### 6.1 Update repo already cloned before this configuration

#### 6.1.1 Update the remote git url

```bash
% git remote set-url origin "git@github.com.personal:benjaminv/starting-react.git"
# mind the .personal added to github.com
```

#### 6.1.2 Update the local user

```bash
% git config --local user.name "ben"
% git config --local user.email "benvee@gmail.com"
```

verify the changes by 
```bash
% cat .git/config

# It looks like following

# [core]
#         repositoryformatversion = 0
#         filemode = true
#         bare = false
#         logallrefupdates = true
#         ignorecase = true
#         precomposeunicode = true
# [remote "origin"]
#         url = git@github.com.personal:benjaminv/starting-react.git
#         fetch = +refs/heads/*:refs/remotes/origin/*
# [branch "main"]
#         remote = origin
#         merge = refs/heads/main
# [user]
#         name = ben
#         email = benvee@gmail.com
```

## 7. Confirming This is Working

```bash
% ssh -T git@github.com

# Hi benjaminv! You've successfully authenticated, but GitHub does not provide shell access.

# Mind the username here, it is benjaminv NOT my default Git account name
```
