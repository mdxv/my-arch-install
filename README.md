# my-arch-install
📔 A little guide to install the Arch Linux for me

## Keyboard
First, check if my keyboard is in the correct layout, since the Arch installation comes with the keyboard in en-US layout by default.

To change the layout, use the following command:

```bash
loadkeys br-abnt2
```

## Internet Connection
I usually use my computer directly wired, but there are some cases where I will need to use Wi-Fi. In that case, I use the [iwd](https://wiki.archlinux.org/title/iwd) package.

To use it, start with the command:
```bash
iwctl
```
Find out the name of the Wi-Fi network device on your computer with the command:
```
[iwd]# device list
```
![iwctl](https://i.imgur.com/GGxULsZ.png)

Now that we know our device is called wlan0, we use the following command to scan for nearby networks:
```
[iwd]# station wlan0 scan
```