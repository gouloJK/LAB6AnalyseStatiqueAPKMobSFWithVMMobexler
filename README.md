## LAB6 Static Analysis of `diva.apk` using MobSF Framework

**Security Analyst** : Omayma El Yamani  
**Lab Environment** : Windows 11 / Docker Desktop / WSL2  
**Tool Version** : MobSF v4.5.0  

---

## 📋 Table of Contents

1. [Laboratory Context](#laboratory-context)
2. [Environment Setup](#environment-setup)
3. [MobSF Deployment](#mobsf-deployment)
4. [APK Submission & Analysis](#apk-submission--analysis)
5. [Security Scoring](#security-scoring)
6. [Vulnerability Assessment](#vulnerability-assessment)
7. [Risk Matrix](#risk-matrix)
8. [Remediation Strategy](#remediation-strategy)
9. [Conclusion](#conclusion)
10. [Annexes](#annexes)

---

## 🎯 Laboratory Context

This pedagogical lab focuses on **static analysis** of Android applications using **MobSF (Mobile Security Framework)** , an open-source automated security testing tool.

### Scope of Analysis

| Analysis Type | Status | Description |
|---------------|--------|-------------|
| Manifest Analysis | ✅ Completed | Permissions, exported components, debuggable flag |
| Code Analysis | ✅ Completed | Hardcoded secrets, insecure implementations |
| Network Security | ✅ Completed | Cleartext traffic, certificate validation |
| Resource Analysis | ✅ Completed | Sensitive data in assets and resources |

### Target Application

| Property | Value |
|----------|-------|
| Application | **diva.apk** (Damn Insecure Vulnerable Application) |
| Purpose | Educational vulnerable Android app |
| Source | OWASP Mobile Security Testing Guide |

---

## ⚙️ Environment Setup

### Deployment Method

MobSF was deployed using **Docker Desktop** on Windows 11, leveraging WSL2 backend for native Linux compatibility.

```powershell
# Pull MobSF image from Docker Hub
docker pull opensecurity/mobile-security-framework-mobsf:latest

# Run MobSF container
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
```

### MobSF Deployment
Terminal Output Verification

<img width="604" height="382" alt="image" src="https://github.com/user-attachments/assets/51804d03-ea21-4973-9118-543ecea70c5b" />

Key Observations from Startup Log:

```text
[INFO] Mobile Security Framework v4.5.0
REST API Key: 6560d3115c5e6b9462ac3aa0d011f912e1bd7a46e57c65a4115fb1c6a0d5165c
Default Credentials: mobsf/mobsf
[INFO] No updates available.
```

### Service Accessibility

| Endpoint | Status | Authentication |
|----------|--------|-----------------|
| http://127.0.0.1:8000 | 🟢 Online | Required (mobsf/mobsf) |
| REST API | 🟢 Online | API Key required |

### Login Interface

<img width="931" height="283" alt="image" src="https://github.com/user-attachments/assets/d39054ae-6975-48c4-82c1-3fe8f9967a3e" />

The login page requires credentials before accessing the analysis dashboard.

---

## 📤 APK Submission & Static Analysis

### File Upload

The diva.apk file was submitted through MobSF's web interface using the "Upload & Analyze" feature.

### File Fingerprinting

<img width="731" height="475" alt="image" src="https://github.com/user-attachments/assets/b44614bf-19b5-4ade-9a93-809b614af8c8" />

#### Cryptographic Hashes

| Hash Type | Value |
|-----------|-------|
| MD5 | `82ab8b2193b3cfb1c737e3a786be363a` |
| SHA-1 | `27e849d9d7b86a3a3357fb3e980433a91d416801` |
| SHA-256 | `5cefc51fce9bd760b92ab2340477f4dda84b4ae0c5d04a8c9493e4fe34fab7c5` |

#### File Properties

| Property | Value |
|----------|-------|
| File Name | `diva.apk` |
| File Size | `1.43 MB` |
| Package Name | `com.paypal.diva` |

---

## 📊 Security Scoring

### Global Security Score

| Metric | Score | Interpretation |
|--------|-------|-----------------|
| Overall Security Score | 🔴 **36/100** | Critical - Multiple severe vulnerabilities |
| Trackers Detection | 0/432 | No tracking libraries detected |

⚠️ **Critical Finding**: A score of 36/100 indicates the application fails most security baseline checks. Immediate remediation required before production deployment.

### Component Statistics

| Component Type | Total | Exported | Risk Level |
|----------------|-------|----------|------------|
| Activities | 17 | 2 | 🔴 High |
| Services | 0 | 0 | 🟢 None |
| Broadcast Receivers | To verify | To verify | ⚠️ To verify |
| Content Providers | To verify | To verify | ⚠️ To verify |

---

## 🔍 Vulnerability Assessment

### Exported Components Analysis

<img width="306" height="117" alt="image" src="https://github.com/user-attachments/assets/b28ad912-78de-4b86-a460-fae9775f73de" />

**Critical Finding**: The application exposes **2 activities** with `android:exported="true"` attribute.

#### What are Exported Components?

Exported components can be invoked by any other application installed on the same device. This creates significant attack vectors:

| Attack Vector | Potential Impact |
|---------------|-----------------|
| Intent Injection | Malicious data injection into activities |
| Activity Hijacking | Sensitive UI interception |
| Privilege Escalation | Unauthorized access to protected functions |
| Data Leakage | Extraction of sensitive information through exported providers |

#### Detailed Component Breakdown

Supplementary analysis data showing component structure. This view provides additional technical details about the application's component architecture.

---

## 📋 Risk Matrix

| Vulnerability Category | Severity | CVSS Score (Est.) | Exploitability | Impact | MASVS Reference |
|------------------------|----------|-------------------|-----------------|--------|-----------------|
| Exported Activities | 🔴 High | 7.4 | Easy | Data leakage, component hijacking | MSTG-PLATFORM-2 |
| Low Security Score | 🔴 Critical | N/A | N/A | Multiple vulnerabilities | Multiple |
| Potential Debuggable | 🟡 Medium | 5.5 | Moderate | Code analysis, reverse engineering | MSTG-CODE-2 |
| Potential Backup Enabled | 🟡 Medium | 4.8 | Moderate | Data extraction via ADB | MSTG-STORAGE-2 |

### Severity Legend

| Icon | Severity | Action Required |
|------|----------|-----------------|
| 🔴 | Critical / High | Fix immediately before any release |
| 🟡 | Medium | Fix in next development sprint |
| 🟢 | Low | Fix when convenient |
| ⚪ | Informational | No action required |

---

## 🛠️ Remediation Strategy

### Immediate Actions (Critical & High Priority)

| # | Action | Technical Implementation |
|---|--------|-------------------------|
| 1 | Remove unnecessary exports | Set `android:exported="false"` on all components unless explicitly required |
| 2 | Implement permission protection | Add custom permissions with `android:protectionLevel="signature"` |
| 3 | Review intent-filter configurations | Remove intent-filters that implicitly export components |

### Short-term Actions (Medium Priority)

| # | Action | Technical Implementation |
|---|--------|-------------------------|
| 4 | Disable backups | Set `android:allowBackup="false"` in `<application>` tag |
| 5 | Remove debuggable flag | Ensure `android:debuggable="false"` for release builds |
| 6 | Enable network security config | Add `android:usesCleartextTraffic="false"` |

### Long-term Actions (Best Practices)

| # | Action | Benefit |
|---|--------|---------|
| 7 | Implement certificate pinning | Prevents MITM attacks |
| 8 | Add code obfuscation (ProGuard/R8) | Increases reverse engineering difficulty |
| 9 | Implement root/jailbreak detection | Protects against compromised devices |
| 10 | Regular security testing | Catch vulnerabilities early in SDLC |

---

## 📝 OWASP MASVS Compliance Mapping

| Vulnerability | MASVS Category | Requirement ID | Compliance Status |
|---------------|----------------|----------------|--------------------|
| Exported activities | Platform Interaction | MSTG-PLATFORM-2 | ❌ Non-compliant |
| Missing permission checks | Platform Interaction | MSTG-PLATFORM-1 | ❌ Non-compliant |
| Potential insecure storage | Data Storage | MSTG-STORAGE-1 | ⚠️ Partially compliant |

---

## ✅ Conclusion

### Key Findings Summary

| Finding | Severity | Status |
|---------|----------|--------|
| Security Score 36/100 | 🔴 Critical | Requires immediate attention |
| 2 exported activities | 🔴 High | Must be secured or removed |
| No trackers detected | 🟢 Positive | Good privacy posture |

### Analyst Notes

The analyzed diva.apk is intentionally vulnerable for educational purposes. In a real-world scenario, an application scoring 36/100 on MobSF would **NOT** be approved for production deployment.

### Skills Acquired During This Lab

✅ Deploying MobSF using Docker containers

✅ Performing static analysis on Android APKs

✅ Interpreting MobSF security scores and alerts

✅ Identifying exported components and their risks

✅ Mapping vulnerabilities to OWASP MASVS standards

✅ Formulating actionable remediation recommendations

---

## 📎 Annexes

### A. Tool Versions

| Tool | Version | Purpose |
|------|---------|---------|
| MobSF | 4.5.0 | Static analysis framework |
| Docker Desktop | Latest | Container runtime |
| JADX | 1.5.0 | DEX to Java decompiler |
| APKTool | Latest | APK resource extraction |

### B. Lab Rules Compliance Checklist

| Rule | Status |
|------|--------|
| APK is pedagogical (diva.apk) | ✅ Compliant |
| No commercial applications analyzed | ✅ Compliant |
| Environment isolated (Docker/WSL2) | ✅ Compliant |
| No exploitation of vulnerabilities | ✅ Compliant |
| Traceability documented (hashes, dates) | ✅ Compliant |

### C. References

| Resource | URL |
|----------|-----|
| MobSF GitHub | https://github.com/MobSF/Mobile-Security-Framework-MobSF |
| OWASP MASVS | https://mas.owasp.org/MASVS/ |
| OWASP MASTG | https://mas.owasp.org/MASTG/ |
| Android Security Docs | https://source.android.com/docs/security |

---

## 🔏 Audit Signature

**Analyst Name**: Omayma El Yamani  
**Audit Completion Date**: May 16, 2026  
**Environment**: Windows 11 + Docker Desktop + WSL2  
**Tool Version**: MobSF v4.5.0  

This audit report was produced for educational purposes as part of a mobile security laboratory. All findings are based on static analysis only and should be verified with dynamic analysis for complete assessment.
