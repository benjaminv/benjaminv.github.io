---
  layout: post
  title: How to Disable Microsoft Autoupdate App from Auto Launch on MacOS
  featured: true
  author: ben
  tags: [Microsoft, macOS, plist]
  image: '/images/posts/microsoft-autoupdate-app.png'
---

Reference: https://superuser.com/a/1676511/571777 and credit to everyone contributed to this page

![](/images/posts/microsoft-autoupdate-app.png)

I was disrupted on several occasions and I have been ever since annoyed by the Microsoft AutoUpdate App being launched on my MacOS.  

I well understand it is important to keep my M365 Apps up to date but keeping the update daemon auto-launched and constantly running and watching my OS seems really notorious.

How to disable the Microsoft AutoUpdate App from Auto Launch on MacOS and popping up notifications from time to time?

 - following has worked for me for a while HOWEVER I do feel Microsoft had managed to restore the configuration maybe during some updates. Notorious! So check back the plist configuration file and see if it is as what you want.

# Check current config and status
```bash
plutil -p /Library/LaunchAgents/com.microsoft.update.agent.plist

#  you get something like this

# {
#   "Disabled" => 0,          // ENABLED AND RUNNING
#   "Label" => "com.microsoft.update.agent"
#   "MachServices" => {
#     "com.microsoft.update.xpc" => 1
#   }
#   "ProgramArguments" => [
#     0 => "/Library/Application Support/Microsoft/MAU2.0/Microsoft AutoUpdate.app/Contents/MacOS/Microsoft Update Assistant.app/Contents/MacOS/Microsoft Update Assistant"
#     1 => "--launchByAgent"
#   ]
#   "RunAtLoad" => 1          // WILL RUN AT LAUNCH
#   "StartInterval" => 7200   // 2 HOURS INTERVAL TO CHECK FOR UPDATES
# }
```

# Alter the config with new values
```bash
# Disable the update agent
sudo plutil -replace Disabled -integer 1 /Library/LaunchAgents/com.microsoft.update.agent.plist
# Disable auto launch
sudo plutil -replace RunAtLoad -integer 0 /Library/LaunchAgents/com.microsoft.update.agent.plist
# Set the launch interval to 14 days
sudo plutil -replace StartInterval -integer 10368000 /Library/LaunchAgents/com.microsoft.update.agent.plist
```

# Check your new new config
```bash
plutil -p /Library/LaunchAgents/com.microsoft.update.agent.plist

# following is good

# {
#   "Disabled" => 1
#   "Label" => "com.microsoft.update.agent"
#   "MachServices" => {
#     "com.microsoft.update.xpc" => 1
#   }
#   "ProgramArguments" => [
#     0 => "/Library/Application Support/Microsoft/MAU2.0/Microsoft AutoUpdate.app/Contents/MacOS/Microsoft Update Assistant.app/Contents/MacOS/Microsoft Update Assistant"
#     1 => "--launchByAgent"
#   ]
#   "RunAtLoad" => 0
#   "StartInterval" => 10368000
# }
```

Like said Microsoft may be able to restore the config so check back when you start seeing the pop-ups unexpectedly.