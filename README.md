# Autoreboot script for Quilibrium nodes (Unix systems)

>[!WARNING]
> The script runs manually but not automatically via cron. The cronjob triggers correctly, but the script doesn't execute. It's likely not a permission issue. Differences in environment or execution context between manual and cron execution might be causing the problem. I haven't had time to fully debug this yet!

>[!TIP]
>This script automates the process of rebooting Unix-like operating systems such as Linux distributions (e.g., Ubuntu, Debian, CentOS) and BSD-based systems (e.g., FreeBSD, macOS). It's specifically built for system hosting Quilibrium nodes, running via a tmux session named "quil".<br>If you have installed your node via my guide [Quilibrium node autoinstaller](https://github.com/lamat1111/Quilibrium-Node-Auto-Installer), then the below script will work for you.

> Created by **LaMAt** /// connect with me on [Farcaster](https://warpcast.com/~/invite-page/373160?id=67559391) or [Twitter](https://twitter.com/LaMat1111) /// &#x2661; [Donations](#-want-to-say-thank-you)

---
### What does the script do?
The script detects when a system restart is required, typically after system updates, and initiates the reboot process automatically.

The script assumes that there is a tmux session named *quil* running, and it terminates it before rebooting. **You need to implement a system to automatically restart the node after any reboot.** For instance, you can follow the steps outlined below (Step 1).

The script will be executed via a cronjob (set by default to run at 2 AM CET every day; adjust the cronjob settings according to your needs). It provides a 3-minute window to abort the reboot process in case you are working in your terminal when it is executed.

## Step 1 - Create cronjob to run the node automatically after a reboot
You only have to run this command once. This will setup a cronjob that will create your tmux session and run the node automatically after every reboot of your server.
Shoutout to Peter Jameson (Quilibrium Discord community creator) for the idea.
```bash
echo '@reboot sleep 10 && bash -lc "export PATH=$PATH:/usr/local/go/bin && cd ~/ceremonyclient/node && tmux new-session -d -s quil '\''./poor_mans_cd.sh'\''"' | crontab -
```
If you need to delete the crontab:<br>
Open the crontab file for editing with <code>crontab -e</code><br>
Locate the line corresponding to the cron job you want to delete and delete it. Press CTRL+X, then Y to save, then ENTER

## Step 2 - Create the autoreboot.sh file
Create a */root/scripts* folder, then create the *autoreboot.sh* file, and open file

```bash
mkdir ~/scripts
```

```bash
touch ~/scripts/autoreboot.sh
```

```bash
nano ~/scripts/autoreboot.sh
```

## Step 3 - Copy/paste the script
**Copy the [autoreboot.sh script](https://github.com/lamat1111/autoreboot-quilibrium-node/blob/main/autoreboot).**

Paste the script in your terminal editor (to paste in your terminal simply right-click with your mouse, do not CTRL+V). Then press CTRL+X, then Y, then ENTER

>[!NOTE]
>The script will log any successful reboot in <code>/root/scripts/autoreboot-log.txt</code>. As long as you have created the "/root/scripts" folder, you don't need to create the "log-autoreboot.txt" file, the script will create it automatically.

## Step 5 - edit autoreboot.sh permissions
Give execute permission to the owner (which should be "root")
```bash
chmod u+x ~/scripts/autoreboot.sh
```
## Step 6 - Add the Cronjob
```bash
crontab -e
```
Paste the below cronjob, then press X, then Y, then ENTER
```bash
0 2 * * * TZ="Europe/Amsterdam" /root/scripts/autoreboot.sh
```
*The script will be executed at 2 AM CET everyday, change the cronjob settings according to your needs.*

---

### &#x2661; Want to say thank you?

If you are happy with all this, you can buy me a cup of something with a small donation.
<details><summary>Donate QUIL</summary>
 
```
coming soon...
```
</details>
<details><summary>Donate ERC20</summary>
 
```
0x0fd383A1cfbcf4d1F493Dd71b798ebca89e8a013
```
Any token that lives on the Ethereum network or Layer2
</details>

