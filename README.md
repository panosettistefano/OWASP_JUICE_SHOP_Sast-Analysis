# OWASP Juice Shop - SAST Cybersecurity Assessment Report

## Overview

This repository contains a comprehensive **Static Application Security Testing (SAST)** report conducted on the intentionally vulnerable web application [OWASP Juice Shop](https://github.com/juice-shop/juice-shop) (built with TypeScript, Angular, and Node.js).

The primary objective of this assessment was to evaluate the application's attack surface, identify critical security flaws using automated tools integrated into a simulated pipeline, and perform manual triage to outline effective remediation strategies based on the CIA Triad (Confidentiality, Integrity, Availability).

---

## Summary of Identified Vulnerabilities

The static code analysis executed via **SonarQube** and **SonarScanner** revealed significant security risks across the codebase:

- **Security Open Issues:** 14 (Rating E — Critical Severity)
- **Security Hotspots:** 107 (Rating E — Manual Review Required)
- **Reliability Issues:** 153 (Rating D)
- **Maintainability Issues:** 4.9k Code Smells (Rating A)
- **Test Coverage:** 0.0% across \~33k lines of code
- **Duplications:** 9.5% across 117k lines of code

---

## Featured Technical Cases \& Remediation Strategies

The report delivers detailed technical breakdowns and code-level remediation examples for five representative vulnerabilities:

### 1. Hardcoded Private RSA Key

- **CWE Mapping:** `CWE-798` (Use of Hard-coded Credentials)
- **OWASP Top 10:** A02:2021 – Cryptographic Failures
- **File:** `lib/insecurity.ts`
- **Issue:** An RSA private key was hardcoded in plaintext within the backend logic.
- **Remediation:** Revoked the exposed key and externalized secret loading via `fs.readFileSync` at runtime.

### 2. Front-End Plaintext Credentials

- **CWE Mapping:** `CWE-798` (Use of Hard-coded Credentials)
- **OWASP Top 10:** A07:2021 – Identification and Authentication Failures
- **File:** `frontend/src/app/login/login.component.ts`
- **Issue:** Test credentials were embedded directly inside the Angular login component.
- **Remediation:** Centralized settings in `environment.ts` for local builds, with recommendations for Secret Vaulting in production.

### 3. Stored Cross-Site Scripting (XSS)

- **CWE Mapping:** `CWE-79` (Improper Neutralization of Input During Web Page Generation)
- **OWASP Top 10:** A03:2021 – Injection
- **File:** `frontend/src/app/about/about.component.ts`
- **Issue:** Bypassed Angular's built-in sanitizer using `bypassSecurityTrustHtml` on user feedback comments.
- **Remediation:** Removed the sanitizer bypass to let Angular safely encode user input as plain text.

### 4. Remote Code Execution (RCE) via Safe Eval

- **CWE Mapping:** `CWE-95` (Eval Injection)
- **OWASP Top 10:** A03:2021 – Injection
- **File:** `routes/b2bOrder.ts`
- **Issue:** Executed untrusted code using `vm.runInContext` and the vulnerable `notevil` library.
- **Remediation:** Replaced dynamic code evaluation with strict JSON schema parsing (`JSON.parse`).

### 5. Denial of Service (DoS) via Unrestricted Uploads

- **CWE Mapping:** `CWE-400` / `CWE-770` (Uncontrolled Resource Consumption)
- **OWASP Top 10:** A05:2021 – Security Misconfiguration
- **File:** `server.ts`
- **Issue:** `multer` middleware was configured without file size restrictions.
- **Remediation:** Enforced an explicit upload limit (`limits: { fileSize: 8000000 }` / 8MB) per OWASP guidelines.

---

## Author Information

- **Author:** Stefano Panosetti
- **Role:** ICT Student
- **Institute:** ITS Academy
- **Document Version:** 2.0
- **Date:** June 3, 2026
