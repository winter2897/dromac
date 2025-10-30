# 🚀 Setup Guide: Multipass Ubuntu 22.04 → ArduPilot SITL → Alias for macOS

This guide explains how to set up a lightweight **Ubuntu 22.04 VM** on macOS using **Multipass**, install **ArduPilot SITL**, and create convenient **alias commands** to start everything easily with a single word like `jammy` or `sitl`.

-----

## 1️⃣ Install Multipass and Create Ubuntu 22.04 VM

### Install Multipass (via Homebrew)

```bash
brew install --cask multipass
```

### 🖥️ Launch Ubuntu 22.04 VM

Create a lightweight Ubuntu 22.04 virtual machine (VM) optimized for SITL testing:

```bash
multipass launch 22.04 --name jammy --cpus 2 --mem 4G --disk 20G
```

### 🔍 Parameter Breakdown

| Parameter | Description | Recommended Adjustment |
| :--- | :--- | :--- |
| `22.04` | Specifies the Ubuntu LTS version (Jammy Jellyfish). | You can replace with `24.04` if you want the latest version. |
| `--name jammy` | Sets the VM name to `jammy`. | This name will be used for your alias later. |
| `--cpus 2` | Allocates 2 CPU cores to the VM. | Enough for SITL; increase to 4 for faster builds. |
| `--mem 4G` | Allocates 4 GB of RAM. | Minimum recommended; increase to 6–8 GB for heavy workloads. |
| `--disk 20G` | Creates a 20 GB virtual disk. | Sufficient for SITL + ArduPilot; use 40 GB for larger projects. |

### 💡 Example Variants

**Performance build environment**

```bash
multipass launch 22.04 --name jammy --cpus 4 --mem 8G --disk 40G
```

**Minimal test environment**

```bash
multipass launch 22.04 --name jammy --cpus 1 --mem 2G --disk 10G
```

### ✅ After creation:

```bash
multipass list
multipass shell jammy
```

The first command checks VM status;
the second one opens your Ubuntu 22.04 shell.

## 2️⃣ Install Development Environment in Ubuntu

Update and install dependencies

```bash
sudo apt update && sudo apt upgrade -y
```

Clone and initialize ArduPilot

```bash
git clone --recurse-submodules https://github.com/ArduPilot/ardupilot.git
cd ardupilot
```

Install ArduPilot prerequisites

```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
. ~/.profile
```

## 3️⃣ Configure SITL Location

Create a custom location file for SITL:

```bash
mkdir -p ~/.config/ardupilot
echo '<YOUR_LOCATION_NAME>=<YOUR_LAT>,<YOUR_LON>,<YOUR_ALT>,<YOUR_HEADING>' > ~/.config/ardupilot/locations.txt
```

**Example:**

```bash
echo 'mycity=10.8500000,106.7700000,100,0' > ~/.config/ardupilot/locations.txt
```

## 4️⃣ Run SITL Simulator

Run ArduCopter SITL manually to test:

```bash
cd ~/ardupilot/Tools/autotest
./sim_vehicle.py -v ArduCopter -f quad -L <YOUR_LOCATION_NAME> --out=udp:<YOUR_HOST_IP>:14550
```

> 🔹 `--out` sends MAVLink telemetry to your GCS (Mission Planner, QGroundControl, or MAVProxy).
>
> 🔹 Replace `<YOUR_HOST_IP>` with your Mac’s IP address if needed.

## 5️⃣ Create Alias inside Ubuntu (shortcut for SITL)

Open your shell config:

```bash
nano ~/.bashrc
```

Add:

```bash
alias sitl='cd ~/ardupilot/Tools/autotest && ./sim_vehicle.py -v ArduCopter -f quad -L <YOUR_LOCATION_NAME> --out=udp:<YOUR_HOST_IP>:14550'
```

Reload and test:

```bash
source ~/.bashrc
sitl
```

✅ Now you can type `sitl` anytime inside Ubuntu to start the simulator.

## 6️⃣ Create macOS Alias to Start Ubuntu VM

Open Terminal on macOS and edit:

```bash
nano ~/.zshrc
```

Add:

```bash
alias jammy='multipass start jammy && multipass shell jammy'
```

Reload:

```bash
source ~/.zshrc
```

✅ From now on, typing:

```bash
jammy
```

will automatically start and open your Ubuntu 22.04 VM.

## 7️⃣ Optional: Global Command on macOS

If you want `jammy` to work system-wide (usable in any shell or script):

```bash
sudo nano /usr/local/bin/jammy
```

Paste:

```bash
#!/bin/bash
multipass start jammy
multipass shell jammy
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/jammy
```

-----

## ✅ Final Workflow Summary

| Action | Command |
| :--- | :--- |
| Open Ubuntu 22.04 VM | `jammy` |
| Run SITL in Ubuntu | `sitl` |
| Stop VM | `multipass stop jammy` |
| List all VMs | `multipass list` |

### 🎯 Quick Recap

1.  Install Multipass on macOS
2.  Create Ubuntu 22.04 VM (`jammy`)
3.  Install ArduPilot + dependencies
4.  Add location file (`<YOUR_LOCATION_NAME>`)
5.  Test SITL manually
6.  Create `sitl` alias in Ubuntu
7.  Create `jammy` alias in macOS

Now, your workflow is:

```bash
# On macOS
jammy  # open Ubuntu 22.04

# Inside Ubuntu
sitl   # launch ArduPilot SITL
```

Perfect for a clean, fast, and repeatable development environment 🚁
