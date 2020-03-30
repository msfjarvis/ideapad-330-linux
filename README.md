# ideapad-330-linux

Getting a fully functional Linux setup on the Lenovo IdeaPad 330 is _not_ a straightforward task. This repository serves to act a central source of all the information I trawled from the interwebz over the past couple days to save everybody else the trouble I went through.

## Quirks

This is the list of issues that you can expect to face trying to install Linux on the IdeaPad 330 without following the steps in this repository.

- Touchpad refuses to work
- No hardware acceleration (GPU fails to load)
- No WiFi

Pretty major issues, so read on.

## 2020 UPDATE

Things are in much better shape today and you should not need to do a lot to get things functional. Touchpad will work out of the box on Linux 5.x.y kernels, and distro packages of `linux-firmware` are now sufficiently up-to-date to support the GPU. The only remaining pain is the WiFi driver, which continues to require an out-of-tree module that you can install by following the steps [here](https://github.com/tomaspinho/rtl8821ce).
