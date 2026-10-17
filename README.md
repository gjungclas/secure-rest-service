# Secure REST Service with SSL/TLS & SHA-256 Validation
> [!NOTE]
> 🚧 **Work in Progress:** This project is actively being populated and refactored for production standards. Core endpoints and documentation will be fully available soon.

A secure Spring Boot microservice demonstrating transport-layer security via self-signed SSL/TLS certificates and payload integrity verification using cryptographic SHA-256 hashing.

---

## 🔒 Security Architecture

- **Transport Layer Security (HTTPS):** Configured via embedded Tomcat using an asymmetric `PKCS12` keystore generated with Java `keytool`.
- **Cryptographic Verification:** Endpoints accept arbitrary payloads, compute cryptographic digests using `java.security.MessageDigest`, and validate client-supplied SHA-256 hashes against calculated digests.
- **Defensive Error Handling:** Standardized error responses to prevent stack trace leakage and injection vectors.

---

## 🛠 Tech Stack

- **Language:** Java 17+ (or 21)
- **Framework:** Spring Boot 3.x (Spring Web, Spring Security)
- **Security & Crypto:** Java Keytool, SHA-256 (`MessageDigest`), HTTPS/TLS 1.3
- **Build Tool:** Maven

---

## 🚀 API Specification

| Method | Endpoint | Description | Sample Payload | Success Response |
| :--- | :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/hash/generate` | Generates SHA-256 checksum for payload | `{"data": "sample text"}` | `200 OK` + Checksum |
| `POST` | `/api/v1/hash/verify` | Compares payload hash against incoming hash | `{"data": "...", "hash": "..."}` | `200 OK` (Boolean Match) |

---

## 💻 Getting Started

### Prerequisites
- JDK 17+
- Maven 3.8+

### Setup & Execution
1. Generate the local development certificate:
   ```bash
   keytool -genkeypair -alias secureboot -keyalg RSA -keysize 2048 -storetype PKCS12 -keystore src/main/resources/keystore.p12 -validity 365
