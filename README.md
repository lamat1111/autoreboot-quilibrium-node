# Autoreboot script for Quilibrium nodes (Unix systems)

> Created by **LaMAt** /// connect with me on [Farcaster](https://warpcast.com/~/invite-page/373160?id=67559391) or [Twitter](https://twitter.com/LaMat1111) /// &#x2661; [Donations](#-want-to-say-thank-you)

This script automates the process of rebooting Unix-like operating systems such as Linux distributions (e.g., Ubuntu, Debian, CentOS) and BSD-based systems (e.g., FreeBSD, macOS). It's specifically built for system hosting Quilibrium nodes, running via a tmux session named *quil*.

If you have installed your node via my guide [Quilibrium node autoinstaller](https://github.com/lamat1111/Quilibrium-Node-Auto-Installer), then the below script will work for you.

### What does it do?
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

## Step 3 - Paste the script
Paste the script in your terminal editor (to paste in your terminal simply right-click with your mouse, do not CTRL+V). Then press CTRL+X, then Y, then ENTER
```bash
#!/bin/bash

# Check if the "System restart required" message is present
if [ -f /var/run/reboot-required ] && \\
   [ $(($(date +%s) - $(stat -c %Z /var/run/reboot-required 2>/dev/null || echo 0))) -ge $((24 * 60 * 60)) ]; then

    # Prompt for reboot with a countdown
    read -t 180 -p "Reboot required - server will reboot in 3 minutes. Press 'n' to abort. " choice

    if [ "$choice" = "n" ]; then
        echo "Reboot aborted."
        exit 1
    fi

    # Kill the tmux session named "quil"
    tmux kill-session -t quil

    # Wait for processes to shut down gracefully
    sleep 10

    # Reboot the server
    echo "Rebooting the server..."
    sudo reboot

else
    echo "No reboot required or not the scheduled time for reboot."
    exit 0
fi
```

## Step 5 - edit autoreboot.sh permissions
Give execute permission to the owner (whcich should be "root")
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

