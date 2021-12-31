---
  layout: post
  title: Manage Multiple Versions of Node Using nvm
  featured: true
  author: ben
  tags: [node, npm, nvm]
  image: '/images/posts/Homebrew-nvm-node.png'
---

# Content
- [Content](#content)
- [Resources:](#resources)
- [Enviroment:](#enviroment)
- [Installation Steps:](#installation-steps)
  - [Check if you have node running by `node -v`](#check-if-you-have-node-running-by-node--v)
  - [Check if it is installed via `Homebrew`](#check-if-it-is-installed-via-homebrew)
- [Remove existing `node` in `brew`](#remove-existing-node-in-brew)
- [Install `nvm`](#install-nvm)
- [Next, create a directory for NVM.](#next-create-a-directory-for-nvm)
- [Add the following to your `~/.zshrc` file (or `~/.bash_profile` if you are using older macOS).](#add-the-following-to-your-zshrc-file-or-bash_profile-if-you-are-using-older-macos)
- [Now either quit and reopen your Terminal, or run the following command to reload your shell.](#now-either-quit-and-reopen-your-terminal-or-run-the-following-command-to-reload-your-shell)
  - [Verify that `nvm` is working by running the following command.](#verify-that-nvm-is-working-by-running-the-following-command)
- [Now you can install `Node.js` versions.](#now-you-can-install-nodejs-versions)
- [Reference:](#reference)
- [Tips and Tricks:](#tips-and-tricks)


I used to be very resistant to having excessive packages installed on my machine. After too many incidences and difficulties with Node.js version controls, I decided to take a step back and try to manage multiple versions of Node.js using the very popular tool [nvm](https://formulae.brew.sh/formula/nvm)

What's being funny is, I am not alone. For example this [man](https://blog.jamesauble.com/install-nvm-on-mac-with-brew-adb921fb92cc):  
`A friend’s suggestion to try NVM to easily switch between Node environments popped into my head. I’ll admit I was hesitant that I would fall into a serious rabbit hole and divert too much time away from my task at hand, but it turned out to be really simple so I thought I’d share it with you all.`


# Resources:  
https://formulae.brew.sh/formula/nvm

# Enviroment:  
macOS Big Sur 11.3.1, Apple Silicon

# Installation Steps:  
## Check if you have node running by `node -v`  
if you see something like `v12.16.1` then you have node installed and running.  
## Check if it is installed via `Homebrew`  
```ruby
brew list
```  
if you see something like `node` or `node@14` then you have node installed and managed by `Homebrew`.  In an event you are running `node` from a non-Homebrew source, the usggestion is to keep it intact and install `nvm` to manage multiple versions of node. Otherwise you should remove node from `brew` before install `nvm`.  
# Remove existing `node` in `brew`  
In my case I had `node@14` installed and I wanted to remove it.
```ruby
brew uninstall --ignore-dependencies node@14  # if you see an error like no Cellar found then use following one
brew uninstall --force node@14
```
then cleanup the brew cache  
```ruby
brew cleanup -s node@14
```
or even clear all brew cache by  
```ruby
brew cleanup -s
```
and remove any dead symlinks  
```ruby
brew cleanup --prune-prefix
```
# Install `nvm`
```ruby
brew update
brew install nvm
```
# Next, create a directory for NVM.  
```ruby
mkdir ~/.nvm
```
# Add the following to your `~/.zshrc` file (or `~/.bash_profile` if you are using older macOS).
```ruby
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```
# Now either quit and reopen your Terminal, or run the following command to reload your shell.
```ruby
source ~/.zshrc
``` 
use `source ~/.bash_profile` if you are using older macOS.  
## Verify that `nvm` is working by running the following command.
```ruby  
echo $NVM_DIR
# /Users/ben/.nvm
```
and  
```ruby
nvm --version
# v0.39.1
```
# Now you can install `Node.js` versions.
  - Node version availability: `nvm ls-remote`
  - Install a specific version: `nvm install 14`
  - Install latest version: `nvm install latest`
  - Install the latest LTS version: `nvm install --lts`
  - List installed versions: `nvm ls`
  - Switch to a specific version: `nvm use 14`
  - Set default version: `nvm alias default 14`

Enjoy using `nvm`!


# Reference:
https://blog.jamesauble.com/install-nvm-on-mac-with-brew-adb921fb92cc

# Tips and Tricks:
**Q**: `Tab` key does not accept syntax suggestions or Github Copilot suggestions.
**A**: This happens with some markdown plugins including [`markdown-all-in-one`](https://github.com/microsoft/vscode/issues/131953#issuecomment-909900927) as conflicts with the `Tab` key.  

**Solution**: is to update your `keybindings.json` as follwoing instruction ([credit to this](https://github.com/yzhang-gh/vscode-markdown/issues/1011)):  

**Howto**: `Ctrl+Shift+p`, then find Preferences: `Open Keyboard Shortcuts (JSON)`) by adding the following:  
```json
[
  {
    "key": "tab",
    "command": "markdown.extension.onTabKey",
    "when": "editorTextFocus && !inlineSuggestionVisible && !editorReadonly && !editorTabMovesFocus && !hasOtherSuggestions && !hasSnippetCompletions && !inSnippetMode && !suggestWidgetVisible && editorLangId == 'markdown'"
  },
  {
    "key": "tab",
    "command": "-markdown.extension.onTabKey",
    "when": "editorTextFocus && !editorReadonly && !editorTabMovesFocus && !hasOtherSuggestions && !hasSnippetCompletions && !inSnippetMode && !suggestWidgetVisible && editorLangId == 'markdown'"
  }
]
```