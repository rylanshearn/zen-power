#zen-power
This repo includes scripts for optimising power usage on Asus Zenbook UX31A laptops running Ubuntu (up to 15.10).

![Image of lapton](https://www.asus.com/media/global/products/NOzAOtadWyTCclA9/P_500.jpg)

## Requirements
- These scripts should work on most desktop linux distributions but they have only been tested on Ubuntu. At least one of them `turboboost.sh` does not work on Ubuntu 16.04 but does work on 15.10. If you have any problems please make a new issue.

##Script descriptions

###turboboost.sh
Running this will disable the Intel turbo boost technology for the cpu, giving a huge drop in heat and power consumption.

####Requirements
This script uses **msr-tools**, and we also want to check the state of the cpu with **cpupower**. Install these with:

```sh
sudo apt install linux-tools-generic linux-tools-common
```

####Usage
1. check the state of your cpu with `cpupower frequency-info`
    * you should see "boot state support: Supported: yes Active: yes"
    * we want supported and active to be 'no'
2. make the script executable with `chmod 755 turboboost.sh`
3. run `/.turboboost.sh`
4. check the state of your cpu with `cpupower frequency-info`
    * now you should see: "boot state support: Supported: no Active: no"

####Run at startup
You will need to run this every time after restarting. If you want to set this up permanently, you can set the script to run at startup. To do this, do the following (assuming you are in the cloned directory):

```sh
#make sure it's executable
chmod 755 turboboost.sh
#move to /etc/init.d/
sudo cp turboboost.sh /etc/init.d/
#create symbolic link in graphical runlevel 2 
#	(make sure this is the last script to run in this
#	directory, based on the number next to 'S' !)
sudo ln -s /etc/init.d/turboboost.sh /etc/rc2.d/S06turboboost.sh
```

###zenPower.sh
Modifies a number of power saving settings by adding some kernel boot arguments and enabling Agressive Link Power Management  (DANGER: please test these arguments on your own system before relying on this script!)

####Usage

```sh
#make executable
chmod +x
#run
sudo ./zenPower.sh
```

####Details
#####Kernel boot argument changes
- frame buffer compression - reduces the memory bandwidth on screen refreshes
- Intel RC6 - GPU enters a lower power state when idling
- DRM vblank off delay - Direct Rendering Manager reduces wakeup events and theoretically saves power
- NMI Watchdog - turning off saves a fraction of power

#####Grub boot loader backup
- the grub boot loader will be backed up at ~/Documents/ , copy this backup file back to /etc/default/ if you have problems booting

####WARNING
- This script also enables Agressive Link Power Management (ALPM)
    * There is a possibility that ALPM may aggressively power down the SATA link prematurely or incorrectly and cause data corruption or loss
    * please test on your own system, otherwise apply this at your own risk
- If ALPM is causing problems, you can switch it off with the following line:

```sh
sudo rm /etc/pm/config.d/sata_alpm
```

