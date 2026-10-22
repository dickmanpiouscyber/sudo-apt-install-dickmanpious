

```markdown
# 🐧 dickmanpious - Custom Debian/Ubuntu Package

A custom Debian package demonstrating the complete Linux packaging lifecycle: from source code and GPG cryptographic signing to publishing on a Launchpad PPA (Personal Package Archive). 

This project proves the ability to build, sign, and distribute software for the Ubuntu/Debian ecosystem using native `apt` commands.

---

## ⚠️ OS Compatibility (Why Ubuntu?)

**A crucial part of this project is the environment.** Launchpad PPAs and the `debuild` toolchain are designed specifically for **Ubuntu**. 

*   **Kali Linux:** Does **not** support Launchpad PPAs (it is Debian-based). Trying to add a PPA in Kali will result in a `403 Forbidden` error.
*   **Windows:** Does not have native `apt` or `debuild` tools. 
*   **The Solution:** We use **WSL (Windows Subsystem for Linux) running Ubuntu** on Windows to get a native Ubuntu environment without needing a full Virtual Machine.

---

## 🚀 Step 1: Environment Setup (WSL Ubuntu)

If you are on Windows, install Ubuntu via PowerShell:
```powershell
wsl --install -d Ubuntu
```
Once installed, open the Ubuntu terminal and update the system:
```bash
sudo apt update && sudo apt upgrade -y
```

## 🛠️ Step 2: Install Build Tools
Install the necessary Debian packaging tools inside Ubuntu:
```bash
sudo apt install devscripts debhelper dh-make build-essential -y
```

## 🔐 Step 3: Generate GPG Key
Launchpad requires cryptographically signed packages. Generate a new GPG key:
```bash
gpg --gen-key
```
*Follow the prompts (Real Name, Email, and Passphrase).*

Get your new Key ID:
```bash
gpg --list-secret-keys --keyid-format LONG
```
*(Look for the `sec rsaXXXX/KEY_ID` line and copy the `KEY_ID`)*.

Send the key to the Ubuntu Keyserver:
```bash
gpg --keyserver keyserver.ubuntu.com --send-keys YOUR_KEY_ID
```
Go to `https://launchpad.net/~yourusername/+editpgpkeys`, paste your key ID, and confirm via the email Launchpad sends you.

---

## 📁 Step 4: Project Structure

Create the following folder structure for your package:

```text
dickmanpious-1.0/
├── debian/
│   ├── changelog      # Version history and distribution targeting
│   ├── control        # Package metadata, dependencies, and description
│   ├── rules          # The Makefile that tells dh how to build the package
│   └── source/
│       └── format     # Source format (3.0 quilt)
├── index.js           # The core application logic
└── README.md          # This file
```

---

## 📝 Step 5: Create Package Files

**1. Create the source file:**
```bash
echo "echo 'Hello from my custom apt package!'" > index.js
```

**2. Create `debian/control`:**
```bash
nano debian/control
```
Paste:
```text
Source: dickmanpious
Section: misc
Priority: optional
Maintainer: YOUR NAME <your.email@example.com>
Build-Depends: debhelper-compat (= 13)
Standards-Version: 4.6.2

Package: dickmanpious
Architecture: all
Description: Test package for apt lifecycle
 This is a dummy package to test uploading to Launchpad
 and installing via apt.
```

**3. Create `debian/rules` (Make sure to use TAB, not spaces):**
```bash
nano debian/rules
```
Paste:
```make
#!/usr/bin/make -f
%:
	dh $@
```
Make it executable:
```bash
chmod +x debian/rules
```

**4. Create `debian/changelog`:**
```bash
nano debian/changelog
```
Paste (Change `jammy` to your Ubuntu version, e.g., `resolute` for 26.04, `noble` for 24.04, `jammy` for 22.04):
```text
dickmanpious (1.0-1) jammy; urgency=medium

  * Initial release.

 -- YOUR NAME <your.email@example.com>  Thu, 22 Oct 2026 12:00:00 +0000
```

**5. Create `debian/source/format`:**
```bash
echo "3.0 (quilt)" > debian/source/format
```

**6. Create the original tarball (Required for 3.0 quilt):**
```bash
cd ..
tar -czf dickmanpious_1.0.orig.tar.gz dickmanpious-1.0/
cd dickmanpious-1.0
```

---

## ⚙️ Step 6: Build and Sign

Set your environment variables:
```bash
export DEBFULLNAME="YOUR NAME"
export DEBEMAIL="your.email@example.com"
export DEBKEY="YOUR_KEY_ID"
```

Build and sign the package:
```bash
debuild -S -sa
```
*(You will be prompted for your GPG passphrase).*

---

## ☁️ Step 7: Upload to Launchpad PPA

Create a `dput.cf` configuration file:
```bash
cd ..
echo "[yourusername-ppa]
fqdn = ppa.launchpad.net
method = ftp
incoming = ~yourusername/ubuntu/ppa/
login = anonymous" > dput.cf
```

Upload the signed `.changes` file:
```bash
dput -c dput.cf yourusername-ppa dickmanpious_1.0-1_source.changes
```

**Wait 5-10 minutes** for Launchpad to build and publish the package. You will receive an email when it's done.

---

## 🚀 Step 8: Install via APT

Once Launchpad shows a green checkmark (Published), you can install it on any Ubuntu machine:

```bash
sudo add-apt-repository ppa:yourusername/ppa
sudo apt update
sudo apt install dickmanpious
```

---

## 🛠️ Technical Stack & Skills Demonstrated

This project was not just about writing code; it was about understanding the Linux operating system at a deeper level. 

*   **Linux System Administration:** Managed permissions, file structures (`/usr/bin`, `/usr/share`), and packaging rules.
*   **Cryptography (GPG):** Generated OpenPGP keys, signed packages (`debsign`), and registered keys with the Ubuntu Keyserver for Launchpad verification.
*   **Debian Packaging Tools:** Used `dh_make`, `debuild`, and `dpkg-source` to create `.dsc`, `.orig.tar.gz`, and `.changes` files.
*   **CI/CD & Distribution:** Utilized `dput` to upload to Launchpad, leveraging Canonical's automated build farm to compile and publish the package.
*   **Troubleshooting:** Solved cross-platform issues (Windows/Kali to Ubuntu) and resolved GPG signature/keyring errors.

## 🔐 Security & Verification

All packages uploaded to this PPA are cryptographically signed. 
*   **GPG Fingerprint:** `YOUR_GPG_FINGERPRINT`
*   **Launchpad PPA:** `https://launchpad.net/~yourusername/+archive/ubuntu/ppa`

## 👨‍💻 Author

**YOUR NAME**
- GitHub: [@yourusername](https://github.com/yourusername)
- Launchpad: [yourusername](https://launchpad.net/~yourusername)

## 📄 License

This project is licensed under the MIT License.
```
