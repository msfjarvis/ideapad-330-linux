# ideapad-330-linux

__WIP: This is not a finished walkthrough yet__

Getting a fully functional Linux setup on the Lenovo IdeaPad 330 is _not_ a straightforward task. This repository serves to act a central source of all the information I trawled from the interwebz over the past couple days to save everybody else the trouble I went through.

## Quirks

This is the list of issues that you're going to face trying to install Linux on the IdeaPad 330 without following the steps in this repository.

- Touchpad refuses to work
- No hardware acceleration (GPU fails to load)
- No WiFi

Pretty major issues, so read on.


### DISCLAIMER

Before we begin -- These are the steps _I_ followed to get my SKU working, YMMV with the different variants Lenovo has put out. I've attempted to generify wherever possible and include instructions on how to adapt the things being done here for your SKU but I provide no guarantees.


## Step 1 - Getting the kernel

Regardless of what distro you choose to run, try getting on the latest stable kernel to see if you actually need to build your own custom kernel. As of writing it is [4.18.14](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/log/?h=v4.18.14). If your **touchpad works**, skip to Step  2.

So you just tried the latest stable kernel and your touchpad still doesn't work. Goddamit ELAN. Thankfully this is easy to remedy. Make sure you have enough bandwidth to clone a full kernel tree (Your WiFi is likely dead too, so grab that ethernet cable).

```shell
git clone https://github.com/MSF-Jarvis/linux -b linux-4.18.y
```

This will clone my Linux fork with the patches for the `ELAN061E`  trackpad included. To see what trackpad your SKU has, open up a terminal and type this:

```shell
sudo acpidump | grep -C3 ELAN
```

The output will be something like this

```shell
msfjarvis@jarvisbox:~$ sudo acpidump | grep -C3 ELAN
  5E60: 44 5B 82 48 1E 54 50 44 30 08 5F 48 49 44 0D 4D  D[.H.TPD0._HID.M
  5E70: 53 46 54 30 30 30 31 00 08 5F 43 49 44 0D 50 4E  SFT0001.._CID.PN
  5E80: 50 30 43 35 30 00 14 4F 04 5F 49 4E 49 00 A0 16  P0C50..O._INI...
  5E90: 93 54 50 54 59 01 70 0D 45 4C 41 4E 30 36 31 45  .TPTY.p.ELAN061E
  5EA0: 00 5F 48 49 44 A0 17 93 54 50 54 59 0A 02 70 0D  ._HID...TPTY..p.
  5EB0: 53 59 4E 41 32 42 34 41 00 5F 48 49 44 A0 16 93  SYNA2B4A._HID...
  5EC0: 54 50 54 59 0A 03 70 0D 41 55 49 31 36 36 38 00  TPTY..p.AUI1668.
```

See the `ELAN061E` there? That's the trackpad's ID. Grab yours, then head back into the kernel tree you just cloned to add support for the trackpad if it's missing. Open up `drivers/input/mouse/elan_i2c_core.c` and navigate to the array at line 1375.

Then, add your ID to the array before the null terminating entry in this format `{"<trackpad_id>", 0 },`. Refer to the top commit of the tree for reference. If the ID is already there, skip the step and move to the kernel compilation.

Assuming the machine hasn't been prepped for compiling kernels, go ahead and grab the dependencies.

```shell
sudo apt install build-essential libelf-dev libssl-dev -y
```

Once that's done, let's compile the actual kernel.

```shell
cp -v /boot/config-$(uname -r) .config
make oldconfig
make -j8
sudo make modules_install
sudo make install
```

Now reboot, and you should boot right into your newly compiled kernel with a working trackpad.


### Step 2 - Getting WiFi working

WiFi is a relatively easy affair. Just grab the `rtl8821ce` driver and install the module. Or just copypasta the commands from below because isn't that what the internet is for?

```shell
git clone https://github.com/tomaspinho/rtl8821ce
cd rtl8821ce
sudo ./dkms-install.sh
```

And, you guessed it, reboot.


### Step 3 - Hardware acceleration

To get hardware acceleration up and running, grab the `master` branch of my [linux tree](https://github.com/MSF-Jarvis/linux). `jarvisbox_defconfig` has been minimally tuned to the IdeaPad 330 hardware, enabling the rtl8821ce driver I imported into the tree along with KVM.

Before installing the kernel, grab the [linux-firmware](https://kernel.googlesource.com/pub/scm/linux/kernel/git/firmware/linux-firmware) repo from kernel.org and run `sudo make install` to install all the needed firmware for the amdgpu module.
