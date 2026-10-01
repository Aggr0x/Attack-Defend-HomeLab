# Step 4: Build the Ubuntu Base VMs

**Goal:** Create two Ubuntu Server 24.04 LTS VMs with fixed IP addresses on `LabNet`:

| VM name | Hostname | IP address | vCPU | RAM | Disk | Used in |
| --- | --- | --- | --- | --- | --- | --- |
| `splunk` | `splunk` | 10.10.10.10 | 2 | 8192 MB | 60 GB | [Step 6](06-splunk-setup.md) |
| `dvwa` | `dvwa` | 10.10.10.20 | 1 | 2048 MB | 25 GB | [Step 5](05-dvwa-vulnerable-web-app.md) |

**Why Ubuntu 24.04 and not 26.04:** Splunk Enterprise 10.2 lists Ubuntu 22.04 and 24.04 as supported Linux platforms; 26.04 is not on that list yet. Using the same OS on both VMs keeps the steps identical.

## 1. Download and verify the ISO

1. Download `ubuntu-24.04.5-live-server-amd64.iso` (the latest 24.04 point release as of September 30, 2026) from **<https://releases.ubuntu.com/24.04/>**.
2. Verify it against the `SHA256SUMS` file on the same page:
   - **Windows:** `Get-FileHash .\ubuntu-24.04.5-live-server-amd64.iso -Algorithm SHA256`
   - **Linux:** `sha256sum ubuntu-24.04.5-live-server-amd64.iso`

   The result must match `97f3d7ffb032c3eb3b23d2c8be9cc76e60c2c1f2c0146ba5ba9fe01cafae0fd8`.

## 2. Create the VM in VirtualBox

Do this once for `splunk`, then again for `dvwa` using the values from the table above.

1. Click **Machine > New**.
2. **Name:** `splunk` · **ISO Image:** the Ubuntu ISO · check **Skip Unattended Installation** (you will set the static IP address by hand).
3. **Hardware:** set the RAM and processors from the table.
4. **Hard Disk:** create a virtual hard disk of the size in the table (the default dynamically allocated VDI is fine).
5. Click **Finish**, then open **Settings > Network > Adapter 1** and set **Attached to:** `NAT Network`, **Name:** `LabNet`.
6. Click **Start**.

## 3. Install Ubuntu Server

Accept the defaults except for these screens:

| Installer screen | What to enter |
| --- | --- |
| **Network configuration** | Select the network interface (usually `enp0s3`) > **Edit IPv4** > **Manual**. Subnet `10.10.10.0/24`, Address `10.10.10.10` (or `10.10.10.20` for `dvwa`), Gateway `10.10.10.1`, Name servers `1.1.1.1,8.8.8.8` |
| **Profile setup** | Your name, server name `splunk` (or `dvwa`), username `labadmin`, and a strong password |
| **SSH configuration** | Check **Install OpenSSH server** |
| **Featured server snaps** | Select none |

When the installer finishes, choose **Reboot Now**. If it asks you to remove the installation medium, press Enter; VirtualBox ejects the ISO automatically.

> **Missed the network screen?** Set the static address after installation. Create `/etc/netplan/60-labnet.yaml` with the content below (change `.10` to `.20` on `dvwa`), then run `sudo chmod 600 /etc/netplan/60-labnet.yaml && sudo netplan apply`.
>
> ```yaml
> network:
>   version: 2
>   ethernets:
>     enp0s3:
>       dhcp4: false
>       addresses: [10.10.10.10/24]
>       routes:
>         - to: default
>           via: 10.10.10.1
>       nameservers:
>         addresses: [1.1.1.1, 8.8.8.8]
> ```

## 4. First boot tasks

Log in as `labadmin` and run:

```bash
sudo apt update && sudo apt full-upgrade -y
sudo timedatectl set-timezone America/New_York   # use your own time zone so Splunk timestamps line up
sudo reboot
```

## Check your work

From your **host PC**, connect over SSH through the port forwarding rules from Step 2:

```bash
ssh -p 2210 labadmin@127.0.0.1   # splunk VM
ssh -p 2220 labadmin@127.0.0.1   # dvwa VM
```

On each VM, confirm:

```bash
ip -4 addr show enp0s3   # shows 10.10.10.10 or 10.10.10.20
ping -c 3 10.10.10.1     # gateway answers
ping -c 3 ubuntu.com     # internet access works
```

From `splunk`, `ping -c 3 10.10.10.20` should succeed, and the reverse from `dvwa`. Shut both VMs down and take a `clean-install` snapshot of each.

## Sources

- [Ubuntu 24.04 release downloads and SHA256SUMS](https://releases.ubuntu.com/24.04/)
- [Splunk Enterprise 10.2 system requirements](https://help.splunk.com/en/splunk-enterprise/get-started/install-and-upgrade/10.2/plan-your-splunk-enterprise-installation/system-requirements-for-use-of-splunk-enterprise-on-premises)

**Previous:** [Step 3: Create the Kali Linux attack machine](03-kali-attack-machine.md) · **Next:** [Step 5: Install DVWA](05-dvwa-vulnerable-web-app.md)
