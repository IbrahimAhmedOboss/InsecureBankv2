# InsecureBankv2
Android security assessment and findings
# 🔐 Android Security Assessment – InsecureBankv2

## 📄 Overview
This repository contains a professional security assessment of the **InsecureBankv2** Android application.  
The purpose of this test was to identify and document security weaknesses using both **manual testing** and **automated analysis** tools.

---

## 🎯 Scope & Objectives
- Evaluate the security posture of the InsecureBankv2 APK.
- Identify vulnerabilities affecting data confidentiality, integrity, and network communication.
- Provide actionable recommendations for remediation.

---

## 🛠 Tools & Environment
| Category | Tools |
|----------|------|
| Dynamic Analysis | **Burp Suite** |
| Static Analysis | **Apktool**, **Jadx** |
| Automated Scanning | **Mobile Security Framework (MobSF)** |

---

## 🔎 Methodology
1. **Environment Setup**
   - Configured Burp Suite proxy on a real Android device.
   - Installed Burp CA certificate to intercept network traffic.
2. **Dynamic Testing**
   - Monitored login and transaction requests.
   - Captured HTTP requests/responses for analysis.
3. **Static Analysis**
   - Decompiled the APK using Apktool/Jadx.
   - Reviewed the AndroidManifest and source code.
4. **Automated Scanning**
   - Uploaded the APK to MobSF to validate manual findings.

---

## ⚠️ Key Findings

| # | Finding | Impact | Evidence | Recommendation | Severity |
|---|--------|-------|---------|----------------|---------|
| 1 | **Plain-Text Credential Transmission** | Usernames and passwords are transmitted via `http://` without encryption, allowing interception by attackers on the same network. | Burp Suite HTTP request/response screenshots. | Enforce **HTTPS/TLS** for all communication and enable certificate pinning. | 🔴 Critical |
| 2 | **Insecure SharedPreferences Storage** | Sensitive data (server details, session info) stored unencrypted in SharedPreferences can be extracted from backups or rooted devices. | Jadx code review showing `SharedPreferences.putString`. | Use **EncryptedSharedPreferences** or Android Keystore for secure storage. | 🟠 Medium |
| 3 | **Debuggable Mode Enabled** | `android:debuggable="true"` allows attackers to attach a debugger and reverse engineer the app. | AndroidManifest.xml | Disable Debuggable in production builds. | 🟠 Medium |
| 4 | **Hardcoded Server Configuration** | Hardcoded `serverip` and `serverport` expose infrastructure details and facilitate traffic redirection. | Jadx source code | Remove or obfuscate server info; fetch configuration securely from the backend. | 🟡 Low |
| 5 | **Janus Signature Vulnerability** | APK signed using v1 only is vulnerable to Janus attacks. | MobSF report | Sign with **v2/v3** scheme. | 🟠 Medium |
| 6 | **StrandHogg 2.0 Activity Hijacking** | Exported activities (`PostLogin`, `DoTransfer`, etc.) allow task hijacking. | MobSF report | Set `launchMode="singleInstance"`, clear `taskAffinity`, update target SDK ≥ 29. | 🔴 Critical |
| 7 | **Excessive Permissions** | Permissions such as `SEND_SMS`, `READ_CONTACTS`, and `GET_ACCOUNTS` increase the attack surface. | MobSF report | Remove unnecessary permissions and enforce runtime permission checks. | 🟠 Medium |
| 8 | **Backup Enabled** | `android:allowBackup="true"` allows ADB backups of app data. | AndroidManifest.xml | Disable backups in production builds. | 🟡 Low |

---

## 🧮 Risk Summary

| Severity | Count |
|---------|------|
| 🔴 Critical | 2 |
| 🟠 Medium  | 4 |
| 🟡 Low     | 2 |

---

## 💡 Recommendations
1. **Secure Communication** – Enforce HTTPS/TLS with certificate pinning.  
2. **Secure Storage** – Use EncryptedSharedPreferences or Android Keystore.  
3. **App Hardening** – Disable debugging, backups, and unnecessary exported components.  
4. **Update Build Security** – Target SDK ≥ 29 and sign APKs using v2/v3 signature schemes.  
5. **Permission Review** – Remove dangerous or unused permissions.

---

## ✅ Conclusion
The InsecureBankv2 application contains **critical vulnerabilities** that expose user credentials and sensitive data.  
Immediate priority should be given to:
- Migrating all communications to **HTTPS/TLS**  
- Securing local storage  
- Hardening application configurations before production release.

---

> **Attachments (recommended)**  
> • Screenshots of Burp Suite requests showing plaintext credentials.  
> • MobSF static analysis report excerpts confirming key findings.

---
