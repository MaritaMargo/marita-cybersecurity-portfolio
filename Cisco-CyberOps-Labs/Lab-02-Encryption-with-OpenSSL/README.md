# Lab 02 – Data Encryption & Decryption Using OpenSSL

## Overview
This lab demonstrates symmetric encryption and decryption using OpenSSL with the AES-256-CBC algorithm.  
The objective was to understand how confidentiality is implemented using password-based encryption.

## Environment
- Parrot OS (isolated lab environment)
- OpenSSL 3.x
- AES-256-CBC cipher

## Threat Scenario
Sensitive data is stored in plaintext and must be protected from unauthorized access.  
Encryption is applied to ensure confidentiality of the information during storage or transmission.

## Steps Performed
1. Created a plaintext file (`letter.txt`).
2. Encrypted the file using AES-256-CBC.
3. Generated a binary encrypted file (`message.enc`).
4. Re-encrypted using Base64 encoding for ASCII-safe output.
5. Decrypted the encrypted file back to plaintext.
6. Verified that decrypted content matched the original.

## Key Concepts Demonstrated
- Symmetric encryption
- AES-256-CBC cipher mode
- Password-based key derivation
- Base64 encoding
- Salt usage in encryption
- Difference between encryption and hashing

## Result
The plaintext file was successfully encrypted and became unreadable without the correct password.  
Using the same password, the encrypted file was decrypted back to its original form.

## Security Limitation
OpenSSL produced a warning indicating the use of a deprecated key derivation method.  
In production environments, stronger key derivation methods such as PBKDF2 should be used.

Additionally, this encryption method alone does not guarantee data integrity protection.
## Detection & Security Insight

Encryption ensures confidentiality but does not inherently provide integrity verification.

Without additional mechanisms (such as HMAC or authenticated encryption modes like AES-GCM), 
encrypted data may still be modified without detection.
---

*Cisco CyberOps – Practical Lab Implementation*
