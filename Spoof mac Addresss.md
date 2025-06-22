

sudo ip link set wlan0 down
sudo macchanger -r wlan0
sudo ip link set wlan0 up


### What this does:

- 🔻 **Brings `wlan0` down** (disconnects it temporarily)
    
- 🎲 **Changes the MAC**
    
- 🔺 **Brings it back up** (reconnects it)


## Why This Happens

- When a **wireless interface is "up" or connected**, the system locks it to prevent tampering during use.
    
- You **must bring it down** before changing MAC settings.
    
- This is a safety and stability feature in Linux.