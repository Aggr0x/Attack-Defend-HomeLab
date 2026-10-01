# Step 2: Create the NAT Network

**Goal:** Create a VirtualBox NAT Network named `LabNet` (10.10.10.0/24). All three lab VMs join this network so they can talk to each other and download updates, while staying hidden from your home network.

## Why a NAT Network

| Mode | VMs see each other | VMs reach the internet | Home network can reach VMs |
| --- | --- | --- | --- |
| NAT (the default) | No | Yes | No |
| **NAT Network (this lab)** | **Yes** | **Yes** | **No, except through port forwarding rules you create** |
| Bridged Adapter | Yes | Yes | Yes (unsafe for DVWA) |

## Option A: Create it in the VirtualBox window

1. Open VirtualBox and select **File > Tools > Network Manager**.
2. Select the **NAT Networks** tab, then click **Create**.
3. In the properties panel at the bottom, set:
   - **Name:** `LabNet`
   - **IPv4 Prefix:** `10.10.10.0/24`
   - **Enable DHCP:** checked
4. Click **Apply**.

## Option B: Create it with one command

Run in a terminal (on Windows, use PowerShell from `C:\Program Files\Oracle\VirtualBox\`):

```bash
VBoxManage natnetwork add --netname LabNet --network "10.10.10.0/24" --enable --dhcp on
```

## Add port forwarding rules

Port forwarding lets the browser on your host PC reach Splunk and DVWA, and lets you use SSH instead of the VM console. Every rule below binds to `127.0.0.1`, so only your own PC can use it; nothing is opened to your home network.

| Rule name | Host address:port | Guest address:port | Purpose |
| --- | --- | --- | --- |
| `splunk-web` | 127.0.0.1:8000 | 10.10.10.10:8000 | Splunk web interface |
| `splunk-ssh` | 127.0.0.1:2210 | 10.10.10.10:22 | SSH to the Splunk VM |
| `dvwa-web` | 127.0.0.1:8080 | 10.10.10.20:80 | DVWA in your host browser |
| `dvwa-ssh` | 127.0.0.1:2220 | 10.10.10.20:22 | SSH to the DVWA VM |

**In the VirtualBox window:** Network Manager > NAT Networks > select `LabNet` > **Port Forwarding** tab > click the **+** icon for each rule and fill in the columns from the table.

**With commands:**

```bash
VBoxManage natnetwork modify --netname LabNet --port-forward-4 "splunk-web:tcp:[127.0.0.1]:8000:[10.10.10.10]:8000"
VBoxManage natnetwork modify --netname LabNet --port-forward-4 "splunk-ssh:tcp:[127.0.0.1]:2210:[10.10.10.10]:22"
VBoxManage natnetwork modify --netname LabNet --port-forward-4 "dvwa-web:tcp:[127.0.0.1]:8080:[10.10.10.20]:80"
VBoxManage natnetwork modify --netname LabNet --port-forward-4 "dvwa-ssh:tcp:[127.0.0.1]:2220:[10.10.10.20]:22"
```

The rule format is `name:protocol:[host IP]:host port:[guest IP]:guest port`. To remove a rule: `VBoxManage natnetwork modify --netname LabNet --port-forward-4 delete dvwa-web`.

## Check your work

```bash
VBoxManage natnetwork list
```

The output should show `Name: LabNet`, `Network: 10.10.10.0/24`, `DHCP Server: Yes`, and the four port forwarding rules.

## Connecting a VM to LabNet

You will do this for each VM in the next steps:

1. Select the VM and click **Settings > Network > Adapter 1**.
2. Set **Attached to:** `NAT Network`.
3. Set **Name:** `LabNet`.
4. Click **OK**.

> **Isolation note:** A NAT Network hides the VMs from your home network, but the VMs can still start connections outward, including to other devices on your home network and to services on your host PC (VirtualBox exposes the host's loopback interface to guests). Do not run anything sensitive on the host while you practice attacks, and never switch DVWA to a Bridged Adapter.

## Sources

- [Oracle VirtualBox User Guide 7.2: Virtual Networking](https://docs.oracle.com/en/virtualization/virtualbox/7.2/user/networkingdetails.html)
- [VirtualBox manual source: NAT Network service](https://raw.githubusercontent.com/VirtualBox/virtualbox/refs/heads/main/doc/manual/en_US/dita/topics/network_nat_service.dita)
- [VirtualBox manual source: VBoxManage natnetwork](https://raw.githubusercontent.com/VirtualBox/virtualbox/refs/heads/main/doc/manual/en_US/man_VBoxManage-natnetwork.xml)

**Previous:** [Step 1: Install VirtualBox](01-install-virtualbox.md) · **Next:** [Step 3: Create the Kali Linux attack machine](03-kali-attack-machine.md)
