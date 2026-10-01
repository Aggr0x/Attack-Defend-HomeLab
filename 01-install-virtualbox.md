# Step 1: Install VirtualBox

**Goal:** Install Oracle VirtualBox 7.2 (current release 7.2.20, September 22, 2026) on a Windows or Linux host.

Before you start on either platform, confirm that hardware virtualization (Intel VT-x or AMD-V) is enabled in your BIOS/UEFI settings. Without it, 64-bit VMs will not start.

---

## Option A: Windows host

1. **Check virtualization.** Open Task Manager > Performance > CPU. The line **Virtualization** must say **Enabled**.
2. **Install the Microsoft Visual C++ Redistributable.** The VirtualBox 7.2 installer stops with a "needs the Microsoft Visual C++ 2019 Redistributable" error if this is missing. Download `vc_redist.x64.exe` from [Microsoft's Visual C++ Redistributable page](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist), run it, and restart the PC.
3. **Download VirtualBox.** Go to the [VirtualBox Downloads page](https://www.virtualbox.org/wiki/Downloads) and select **Windows hosts**.
4. **Run the installer.** Accept the defaults. Your network connection drops for a few seconds while VirtualBox installs its network drivers; this is expected.
5. **Launch VirtualBox** from the Start menu.

> **Tip:** If Hyper-V, Windows Subsystem for Linux 2, or Memory Integrity (Core Isolation) is turned on, VirtualBox still runs but can be noticeably slower. A small green turtle icon in the VM window's status bar indicates this mode.

---

## Option B: Linux host (Ubuntu or Debian)

Run these commands in a terminal. They add Oracle's official package repository so VirtualBox updates arrive through normal system updates.

```bash
# 1. Install build tools and kernel headers (needed to build the VirtualBox kernel modules)
sudo apt update
sudo apt install -y wget gpg build-essential dkms linux-headers-$(uname -r)

# 2. Add Oracle's signing key
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc \
  | sudo gpg --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg --dearmor

# 3. Add the VirtualBox repository for your release (for example noble = Ubuntu 24.04, bookworm = Debian 12)
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.org/virtualbox/debian $(. /etc/os-release && echo "$VERSION_CODENAME") contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list

# 4. Install VirtualBox 7.2
sudo apt update
sudo apt install -y virtualbox-7.2

# 5. Allow your user to use USB passthrough and other VirtualBox features, then log out and back in
sudo usermod -aG vboxusers "$USER"
```

Launch **VirtualBox** from your application menu, or run `virtualbox` in a terminal.

> **Secure Boot note:** If your PC uses UEFI Secure Boot, the VirtualBox kernel modules must be signed before they load. If VMs fail with a `vboxdrv` error, follow the module-signing prompt shown during installation or see the VirtualBox Linux downloads page.
>
> **Other distributions:** Fedora, openSUSE, and others have packages on the [VirtualBox Linux Downloads page](https://www.virtualbox.org/wiki/Linux_Downloads). Linux Mint and other Ubuntu derivatives need the matching Ubuntu codename in step 3 instead of their own.

---

## Check your work

Open a terminal (PowerShell on Windows) and run:

```bash
VBoxManage --version
```

The output should begin with `7.2`. On Windows, if the command is not found, use the full path: `"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" --version`.

## Optional: Extension Pack

The Oracle VirtualBox Extension Pack adds USB 3.0 passthrough, disk encryption, and other extras. This lab does not need it. It is licensed under Oracle's Personal Use and Educational License (PUEL), not the open-source license that covers VirtualBox itself, so read the license before installing it.

## Sources

- [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)
- [VirtualBox Linux Downloads](https://www.virtualbox.org/wiki/Linux_Downloads)
- [VirtualBox Changelog](https://www.virtualbox.org/wiki/Changelog)
- [Microsoft Q&A: Visual C++ Redistributable for VirtualBox 7.2](https://learn.microsoft.com/en-us/answers/questions/5554813/how-to-install-microsoft-visual-redistributable-pa)

**Next:** [Step 2: Create the NAT Network](02-nat-network.md)
