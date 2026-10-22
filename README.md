# Secure REST Service with TLS/HTTPS & SHA-256 Verification

[![Java Version](https://img.shields.io/badge/java-17%2B-blue.svg)](https://docs.oracle.com/en/java/)
[![Framework](https://img.shields.io/badge/framework-Spring%20Boot-green.svg)](https://spring.io/projects/spring-boot)
[![Security Standard](https://img.shields.io/badge/security-TLS%20%7C%20HTTPS-red.svg)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
[![Cryptography](https://img.shields.io/badge/crypto-SHA--256-orange.svg)](https://en.wikipedia.org/wiki/SHA-2)
[![Build Automation](https://img.shields.io/badge/build-Apache%20Maven-C71A36.svg)](https://maven.apache.org/)

A hardened Spring Boot microservice engineered for Artemis Financial demonstrating transport-layer confidentiality via an X.509 certificate and automated payload integrity verification using cryptographic SHA-256 message digests. The platform enforces HTTPS transit over port 8443 via an embedded Tomcat container bound to a custom Java KeyStore, while exposing RESTful verification endpoints to validate client-server payloads against cryptographic tampering and transmission corruption.

---

## 🔒 Security Architecture

- **Transport Layer Security (HTTPS):** Enforces encrypted client-server transit across port `8443` using an embedded Tomcat servlet container bound to an X.509 self-signed certificate stored in a Java KeyStore (`keystore.jks`).
- **Cryptographic Integrity (SHA-256):** Implements `java.security.MessageDigest` to compute deterministic 256-bit (32-byte) hashes formatted as 64-character hexadecimal checksums, ensuring detection of payload tampering or transmission errors.
- **Strict Character Encoding:** Explicitly enforces `StandardCharsets.UTF_8` byte conversion prior to digest computation to prevent cross-platform locale encoding anomalies.
- **Static Application Security Testing (SAST):** Scanned with the OWASP Dependency-Check Maven plugin to establish dependency governance against National Vulnerability Database (NVD) CVE baselines without introducing external third-party security vulnerabilities.

---

## 🛠 Tech Stack

- **Language:** Java (JDK 17+)
- **Framework:** Spring Boot (Spring Web, Embedded Tomcat)
- **Security & Cryptography:** Java Keytool, Java Cryptography Architecture (JCA), SHA-256 (`MessageDigest`), TLS/HTTPS
- **Static Analysis:** OWASP Dependency-Check
- **Build Automation:** Apache Maven

---

## 🚀 API Specification

### Endpoint: `/hash`

- **Method:** `GET`
- **Protocol:** `HTTPS` (Port `8443`)
- **Produces:** `text/html`
- **Description:** Ingests the configured verification string, computes the cryptographic SHA-256 digest, and returns an HTML-formatted payload containing the raw string, algorithm specification, and hexadecimal checksum.

#### Request Example
```bash
curl -k https://localhost:8443/hash
```
*(Note: `-k` or `--insecure` allows local development testing with self-signed certificates).*

#### Response Output
```html
<p>data: Grace Jungclas - CS 305 Artemis Financial Checksum Verification Data</p>
<p>Algorithm: SHA-256</p>
<p>Checksum: 1a5615af5336b443a9c9c2e8f6dba64fb996afdb63fa73daab12e825f416f190</p>
```

---

## 💻 Setup & Execution

### Prerequisites
- **Java Development Kit (JDK):** 17+
- **Build Tool:** Apache Maven 3.8+ (or use the included `./mvnw` wrapper)

### 1. Certificate & Keystore Generation
The repository includes a development certificate in `src/main/resources/keystore.jks`[cite: 1]. To generate or inspect a similar self-signed identity certificate via Java `keytool`:

```bash
# Generate key pair and keystore
keytool -genkey -keyalg RSA -alias selfsigned -keypass CS305pwd -keystore keystore.jks -storepass CS305pwd -validity 360 -keysize 2048

# Export the public certificate
keytool -export -alias selfsigned -storepass CS305pwd -file server.cer -keystore keystore.jks

# Verify certificate properties and fingerprints
keytool -printcert -file server.cer
```

### 2. Configuration (`src/main/resources/application.properties`)
```properties
server.port=8443
server.ssl.key-alias=selfsigned
server.ssl.key-store-password=CS305pwd
server.ssl.key-store=classpath:keystore.jks
server.ssl.key-store-type=JKS
```

---

### 3. Build & Run
Compile the application:
```bash
./mvnw clean package
```

Run the microservice:
```bash
./mvnw spring-boot:run
```

Access the endpoint in any browser:
```text
https://localhost:8443/hash
```

### 4. Vulnerability Scanning
Run secondary static analysis using the OWASP Dependency-Check plugin:
```bash
./mvnw dependency-check:check
```
The resulting HTML report generates under `target/dependency-check-report.html`[cite: 1].

---

## 📁 Repository Structure

```text
secure-rest-service/
├── .gitignore
├── HELP.md
├── mvnw
├── mvnw.cmd
├── pom.xml
├── README.md
└── src/
    ├── main/
    │   ├── java/com/snhu/sslserver/
    │   │   └── SslServerApplication.java
    │   └── resources/
    │       ├── application.properties
    │       └── keystore.jks
    └── test/
        └── java/com/snhu/sslserver/
            └── SslServerApplicationTests.java
```
