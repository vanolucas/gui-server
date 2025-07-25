---
tags:
  - ongoing
parents:
  - "[[orga]]"
  - "[[Guillaume Smets]]"
created: 2025-07-24
---

## Prepare

### Requirements

- [ ] Plug keyboard and mouse
- [ ] WiFi or Ethernet?
- [ ] WiFi key
- [ ] Plug USB stick

### BIOS setup

- [ ] Enter BIOS
- [ ] Set USB as first boot prio
- [ ] Enable virtualization settings
- [ ] Enable wake after power cutoff

### Router setup

- [ ] Set an assigned IP in router DHCP settings for the server, e.g. `192.168.1.10`

### Password manager

Because different passwords for Debian, Syncthing, Jellyfin, Immich, etc.

- [ ] [KeepassXC](https://keepassxc.org/download/)

## Install Debian

### Main system setup

- [ ] In the GRUB menu, choose Install
- [ ] Language: EN or FR?
- [ ] Country: other > Europe > Belgium
- [ ] Default locale settings: fr_BE.UTF-8
- [ ] Keymap: BE/FR Azerty?
- [ ] Primary network interface: wifi or ethernet?
- [ ] Hostname: `guiserv` (lower case)
- [ ] Domain name: `guillaumesmets.be`
- [ ] Root password (memorize or add to password manager)
- [ ] Full name of user
- [ ] Username: `gui`
- [ ] User password (different from root, memorize or add to password manager)

### Disks partitioning

#### System disk

- [ ] Select the device > Create **new empty partition table** > Yes.
- [ ] Free space > Create a new partition > **100 MB** > Beginning.
	- Use as: **EFI System Partition**.
	- Name: **`efi`**.
	- Bootable flag: **on**.
	- Done setting up the partition.
- [ ] Free space > Create a new partition > **4 GB** > Beginning.
	- Use as: **swap area**.
	- Name: **`swap`**.
	- Bootable flag: **off**.
	- Done setting up the partition.
- [ ] Free space > Create a new partition > size: **max** > Beginning.
	- Use as: **Ext4 journaling file system**.
	- Name: **`root`**.
	- Mount point: **`/`**.
	- Mount options: **defaults**.
	- Label: **none**.
	- Bootable flag: **off**.
	- Done setting up the partition.

#### Data disk

- [ ] Select **`data`** partition.
	- Use as: **Ext4 journaling file system**.
	- Name: **`hdd`** or whatever.
	- Format the partition: **yes**.
	- Mount point: Enter manually > **`/hdd`**.
	- Mount options: **defaults**.
	- Bootable flag: **off**.
	- Done setting up the partition.

### Configure APT package manager

- [ ] Debian archive mirror country: **Belgium**.
- [ ] Debian archive mirror: `deb.debian.org`.
- [ ] HTTP proxy: leave blank.
- [ ] Participate in the package usage survey: No.
- [ ] Choose software to install: **deselect all** (press `Space`) except **SSH server**.

### Boot Debian

- [ ] Remove the USB stick and reboot into the BIOS.
- [ ] Change the boot order to set the system disk as first priority.
- [ ] Save and reboot into the freshly installed Debian.

## Setup Debian

### Setup `sudo`

- [ ] Login as `gui`
- [ ] Become root:

```sh
su -
```

- [ ] Install `sudo`:

```sh
apt install sudo
```

- [ ] Allow `gui` to use `sudo`:

```sh
adduser gui sudo
```

- [ ] Log out then log in as `gui`

```sh
exit
exit
```

### Set data partition ownership

- [ ] Make `gui` owner of the mounted data partition

```sh
sudo chown -R gui:gui /hdd
```

### Update the system

```sh
sudo apt update && sudo apt upgrade
```

### Setup NTP

- [ ] Install NTP:

```sh
sudo apt install ntp
```

- [ ] Check current time:

```sh
date -R
```

### Install `nano`

```sh
sudo apt install nano
```

### Setup locales

```sh
sudo dpkg-reconfigure locales
```

- [ ] Enable `fr_BE`, `fr_FR`, `en_US` (UTF-8)
- [ ] Set default system locale to `None`

### Install useful CLI tools

```sh
sudo apt install tree btop wget curl git ncdu rsync screen tmux ca-certificates
```

## Setup GNOME

### Install GNOME

- [ ] Install desktop environment ([GNOME](https://wiki.debian.org/Gnome))

```sh
sudo apt install gnome-core
```

- [ ] reboot

```sh
sudo systemctl reboot
```

### GNOME settings

- [ ] Switch off Wi-Fi and Bluetooth if they are not needed.
- [ ] Power:
	- Automatic Suspend: **Off** (for a computer that is used as server).
	- Power Button Behavior: **Nothing**.
- [ ] Keyboard:
	- Input Sources:
		- Add the layout: + > Language > Layout variant.
		- Move down the English (US) layout.
		- Remove the English (US) layout.
- [ ] Region & Language:
	- Language
	- Formats:
		- Belgium.
- [ ] Reboot

### Nautilus settings

- Set view mode to **list**.
- Add bookmark to `hdd` data directory in the sidebar.

## Download prepared files

```sh
cd
cd Downloads/
git clone https://github.com/vanolucas/gui-server.git
```

## Install Firefox

- [ ] Install Firefox (optionally `-l10n-fr` for French)

```sh
sudo apt install firefox-esr(-l10n-fr)
```

## Setup Docker

- [ ] Install using [the APT repo](https://docs.docker.com/engine/install/debian/#install-using-the-repository)

```sh
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- [ ] Add `gui` to group `docker`:

```sh
adduser gui docker
```

- [ ] Enable Docker service at boot:

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

- [ ] Configure logging

```sh
sudo nano /etc/docker/daemon.json
```

```json
{
  "log-driver": "local",
  "log-opts": {
    "max-size": "10m"
  }
}
```

- [ ] Reboot

- [ ] Check running containers:

```sh
docker ps -a
```

## Setup Syncthing

- [ ] Create dir for Syncthing Docker server:

```sh
mv ~/Downloads/gui-server/syncthing/ /hdd/
```

- [ ] Edit bind mount path in `docker-compose.yml` if needed
- [ ] Run Syncthing

```sh
cd /hdd/syncthing/
docker compose up -d
```

- [ ] Open [Syncthing web GUI](http://localhost:8384/)
- [ ] Set user password (store in password manager)
- [ ] Create a `rsync` script to bkp from syncthing to permanent

## Setup Tailscale

## Setup Jellyfin
