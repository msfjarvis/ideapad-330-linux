# ideapad-330-linux

Getting a fully functional Linux setup on the Lenovo IdeaPad 330 is _not_ a straightforward task. This repository serves to act a central source of all the information I trawled from the interwebz over the past couple days to save everybody else the trouble I went through.

## Quirks

This is the list of issues that you're going to face trying to install Linux on the IdeaPad 330 without following the steps in this repository.

- Touchpad refuses to work
- No hardware acceleration (GPU fails to load)
- No WiFi

Pretty major issues, so read on.

### DISCLAIMER

Before we begin -- These are the steps _I_ followed to get my SKU working, YMMV with the different variants Lenovo has put out. I've attempted to generify wherever possible and include instructions on how to adapt the things being done here for your SKU but I provide no guarantees.

I've broken down the guide into two variants, a detailed one with separate steps to resolve each quirk described above, and the ready-to-go one below where you simply have to clone my kernel tree and build it. People who are not interested in the stuff that went behind getting all this together can just follow along, for people looking into reading about the technical aspect should head over to the detailed guide [here](DETAILED_GUIDE.md).

## Installation

After installing your distro and updating all packages, install the `linux-firmware` package from the distro's package manager or clone the upstream repository from [here](https://kernel.googlesource.com/pub/scm/linux/kernel/git/firmware/linux-firmware) and then run `sudo make install` from the root directory of the locally cloned repo.

Now to install the kernel and get everything functional, run the following commands. Ensure that your system is setup to compile the kernel before continuing.

```shell
git clone git://github.com/MSF-Jarvis/linux -b linux-5.0.y
cd linux
make jarvisbox_defconfig
make -j$(nproc --all)
make headers_install
sudo make modules_install install
reboot
```

This should work for most SKUs. If your trackpad is still not functional, please refer to the [detailed guide](DETAILED_GUIDE.md) to see how to add support for your variant in the kernel before compiling.
