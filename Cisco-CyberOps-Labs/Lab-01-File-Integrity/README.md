# Lab 01 – File Integrity & Hash Verification

## Overview
This lab demonstrates file integrity verification using SHA256 hashing in a Windows environment.  
The objective was to simulate file tampering and detect changes using cryptographic hash comparison.
## Threat Scenario
An attacker modifies a legitimate file after distribution.  
The goal is to detect unauthorized changes by comparing the current file hash with a previously stored trusted hash value.
## Environment
- Windows 11
- Built-in Windows utility: certutil
- SHA256 hashing algorithm

## Scenario
A file was created and its SHA256 hash was generated and stored as a reference value.  
After modifying the file content, a new hash was calculated and compared against the original reference hash.

## Steps Performed
1. Created a text file using command line.
2. Generated SHA256 hash using `certutil`.
3. Saved the hash output to a `.sha256` file.
4. Modified the original file.
5. Recalculated SHA256 hash.
6. Compared hash values to detect integrity violation.

## Result
After modifying the file content, the SHA256 hash value changed completely, demonstrating the avalanche effect.  
The mismatch confirmed that the file integrity was compromised.

## Security Insight
Hash comparison ensures file integrity but does not guarantee authenticity if both the file and its hash reference are modified by an attacker.  
In real-world environments, digital signatures are used to provide authenticity validation.

---

*Cisco CyberOps – Practical Lab Implementation*
