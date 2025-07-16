# OpenSSL

OpenSSL is a powerful toolkit for encryption and digital certificate management. It provides commands to work with cryptographic algorithms, generate and manage keys, create and verify certificates, and test SSL/TLS configurations.

---

## 1. Installation

Most Unix/Linux distributions ship with OpenSSL pre-installed. To install or upgrade:

```bash
# Debian / Ubuntu
sudo apt-get update
sudo apt-get install openssl

# Fedora
sudo dnf install openssl

# Arch Linux
sudo pacman -S openssl
```

---

## 2. Key Generation

### 2.1 RSA Private Key

Generate a 2048-bit RSA private key encrypted with AES-256:

```bash
openssl genpkey \
  -algorithm RSA \
  -out private_key.pem \
  -aes256
```

Options:

- `-algorithm RSA` – use RSA  
- `-aes256`         – encrypt the key with AES-256

### 2.2 Extract Public Key

```bash
openssl rsa \
  -pubout \
  -in private_key.pem \
  -out public_key.pem
```

### 2.3 Elliptic Curve (EC) Key

Generate a private EC key using the `prime256v1` curve:

```bash
openssl ecparam \
  -name prime256v1 \
  -genkey \
  -noout \
  -out ec_private_key.pem
```

---

## 3. File Encryption & Decryption

### 3.1 Symmetric Encryption (AES-256-CBC)

**Encrypt**:

```bash
openssl enc \
  -aes-256-cbc \
  -salt \
  -in file.txt \
  -out file.enc
```

**Decrypt**:

```bash
openssl enc \
  -d \
  -aes-256-cbc \
  -in file.enc \
  -out file.txt
```

Options:

- `-salt`      – add randomness to strengthen security  
- `-iv <hex>`  – specify a custom initialization vector (see §6)

### 3.2 Asymmetric Encryption (RSA)

**Encrypt** with a recipient’s public key:

```bash
openssl rsautl \
  -encrypt \
  -inkey public_key.pem \
  -pubin \
  -in file.txt \
  -out file.enc
```

**Decrypt** with your private key:

```bash
openssl rsautl \
  -decrypt \
  -inkey private_key.pem \
  -in file.enc \
  -out file.txt
```

---

## 4. Certificate Creation & Management

### 4.1 CSR (Certificate Signing Request)

```bash
openssl req \
  -new \
  -key private_key.pem \
  -out request.csr
```

### 4.2 Self-Signed Certificate

```bash
openssl req \
  -new \
  -x509 \
  -key private_key.pem \
  -out certificate.crt \
  -days 365
```

Options:

- `-x509` – generate an X.509 certificate directly  
- `-days` – validity period in days

### 4.3 Inspect a Certificate

```bash
openssl x509 \
  -in certificate.crt \
  -text \
  -noout
```

### 4.4 Verify Key ↔ Certificate Match

```bash
openssl x509 -noout -modulus -in certificate.crt | openssl md5
openssl rsa  -noout -modulus -in private_key.pem  | openssl md5
```

Hashes must match.

---

## 5. Hashing

Generate and verify file digests:

```bash
openssl dgst -md5    file.txt
openssl dgst -sha256 file.txt
```

Compare against a known good hash to confirm integrity.

---

## 6. Initialization Vectors (IV)

Specify a custom IV (128-bit hex) for AES-CBC:

```bash
openssl enc \
  -aes-256-cbc \
  -salt \
  -in file.txt \
  -out file.enc \
  -iv 00000000000000000000000000000000

openssl enc \
  -d \
  -aes-256-cbc \
  -in file.enc \
  -out file.txt \
  -iv 00000000000000000000000000000000
```

---

## 7. Certificate & Key Format Conversion

| Conversion       | Command                                                                 |
|------------------|-------------------------------------------------------------------------|
| **PEM → DER**    | `openssl x509 -in cert.pem -outform DER -out cert.der`                 |
| **DER → PEM**    | `openssl x509 -in cert.der -inform DER -out cert.pem`                  |
| **PKCS#12 → PEM**| `openssl pkcs12 -in keystore.p12 -nocerts -out private_key.pem`        |
| **PKCS#12 → CRT**| `openssl pkcs12 -in keystore.p12 -clcerts -nokeys -out certificate.crt`|

---

## 8. Certificate Chains

Combine intermediate and root certificates:

```bash
cat intermediate.crt root.crt > chain.pem
```

Verify a leaf certificate against the chain:

```bash
openssl verify -CAfile chain.pem certificate.crt
```

---

## 9. SSL/TLS Testing

### 9.1 Connect & Retrieve Server Certificates

```bash
openssl s_client -connect example.com:443 -showcerts
```

### 9.2 Force a Specific Protocol

```bash
openssl s_client -connect example.com:443 -tls1_2
```

Use these commands to debug SSL/TLS setups, inspect cipher negotiation, and validate certificate chains.
