# 🐧 dickmanpious - Custom Debian/Ubuntu Package

A custom Debian package demonstrating the complete Linux packaging lifecycle: from source code and GPG cryptographic signing to publishing on a Launchpad PPA (Personal Package Archive). 

This project proves the ability to build, sign, and distribute software for the Ubuntu/Debian ecosystem using native `apt` commands.

## 🚀 Installation (Ubuntu / Debian)

You can install this package directly via `apt`:

```bash
# 1. Add the PPA
sudo add-apt-repository ppa:dkpious31/ppa

# 2. Update package list
sudo apt update

# 3. Install the package
sudo apt install dickmanpious
