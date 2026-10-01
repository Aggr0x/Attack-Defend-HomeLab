## Install the Attack Machine- Kali Linux

For anyone unfamiliar, [Kali Linux](https://www.kali.org/) is an open-source Operating System for digital forensics and ethical hacking. It's developed, funded, and maintained by [Offsec](https://www.offsec.com/). It's very versatile and easy to set up, coming preloaded with almost every tool you need for your tasks, which in this case will be ethical hacking. I highly recommend taking any of OffSec's training courses as they're, although challenging, extremely educational and their certifications, such as the OCP, are renowned throughout the industry. 

As of September 30, 2026, the latest Kali release is **Kali Linux 2026.2** (released June 29, 2026). Kali is a rolling distribution, so the update step at the end brings the VM fully current even after a newer image is published. Check the [Kali release history](https://www.kali.org/releases/) before downloading; if a newer version is listed, use it and substitute its version number below.

## Download the official image

Go to the official download page: **<https://www.kali.org/get-kali/#kali-virtual-machines>**.
   I highly recommend downloading the Installer Image for this set up as it's easy and supports Snapshots.
   <img width="478" height="404" alt="image" src="https://github.com/user-attachments/assets/115ea605-681a-4a87-946a-dae6f13ccde6" />

   Go to the Virtualbox download and download the Zip file
   <img width="478" height="404" alt="image" src="https://github.com/user-attachments/assets/88d109d9-3d3e-4724-9ad8-b5241c48e1d6" />


Under **Virtual Machines**, select **VirtualBox** (64-bit) and download the `.7z` file, for example `kali-linux-2026.2-virtualbox-amd64.7z`.
4. Copy the **SHA256** checksum shown next to the download link.

## 2. Verify the download

The computed hash must exactly match the SHA256 value on the Kali site. If it does not match, delete the file and download it again.

**Windows (PowerShell):**

```powershell
Get-FileHash .\kali-linux-2026.2-virtualbox-amd64.7z -Algorithm SHA256
```

**Linux:**

```bash
sha256sum kali-linux-2026.2-virtualbox-amd64.7z
```

## 3. Extract the image

**Windows:** Install [7-Zip](https://www.7-zip.org/), right-click the `.7z` file, and choose **7-Zip > Extract Here**.

**Linux:**

```bash
sudo apt install -y p7zip-full
7z x kali-linux-2026.2-virtualbox-amd64.7z
```

Extraction produces a folder containing a `.vbox` file (the VM definition) and a `.vdi` file (the virtual disk). Move the folder to a permanent location first; VirtualBox runs the VM from wherever the files sit.

## 4. Import into VirtualBox

1. In VirtualBox, select **Machine > Add** (Ctrl+A).
2. Browse to the extracted folder and open the `.vbox` file.
3. Select the new VM, open **Settings**, and set:

| Setting | Location | Value |
| --- | --- | --- |
| Name | General > Basic | `kali` |
| Base Memory | System > Motherboard | 4096 MB |
| Processors | System > Processor | 2 |
| Network | Network > Adapter 1 | Attached to **NAT Network**, Name **LabNet** |

4. Click **OK**, then **Start**.

## 5. First login and update

1. Log in with the default credentials: **username `kali`, password `kali`**.
2. Open a terminal and change the password immediately:

   ```bash
   passwd
   ```

3. Update every package to the current rolling release, then reboot:

   ```bash
   sudo apt update
   sudo apt full-upgrade -y
   sudo reboot
   ```

## Check your work

After the reboot, open a terminal and run:

```bash
grep VERSION= /etc/os-release   # shows 2026.2 or newer
ip -4 addr show eth0            # shows an address in 10.10.10.0/24
ping -c 3 10.10.10.1            # the LabNet gateway answers
ping -c 3 kali.org              # internet access works
```

When all four checks pass, shut the VM down and take a snapshot: select the VM, click the menu icon next to it > **Snapshots > Take**, and name it `clean-install`.

## Sources

- [Kali Linux: Get Kali (virtual machine images)](https://www.kali.org/get-kali/#kali-virtual-machines)
- [Kali Linux Release History](https://www.kali.org/releases/)
- [Kali Linux 2026.2 release announcement](https://www.kali.org/blog/kali-linux-2026-2-release/)

**Previous:** [Step 2: Create the NAT Network](02-nat-network.md) · **Next:** [Step 4: Build the Ubuntu base VMs](04-ubuntu-base-vms.md)
