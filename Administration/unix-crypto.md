---
title: Unix Cryptography Commands
description: Basics Commands for crypting data, storage, volumes with UNIX
published: true
date: 2025-04-16T13:05:40.245Z
tags: cryptography, linux
editor: markdown
dateCreated: 2025-04-16T13:05:37.590Z
---

# Basics of Cryptography

Cryptography is a fundamental element of computer security, allowing data protection by transforming it to make it unreadable for any unauthorized person. Here is an overview of key concepts and commonly used encryption algorithms.

Python program to encrypt a file to send to a recipient through an unsecured channel: Kryptodoc

## 1. Fundamentals of Encryption

**Encryption (or ciphering)**: Process of converting data (plaintext) into an encoded form (ciphertext) using an algorithm and a key. The goal is to make the data unreadable without the appropriate decryption key.

**Decryption**: The reverse process of encryption, where ciphertext is converted back to plaintext using the decryption key.

**Key**: Bit sequence used by an encryption algorithm to transform data. The security of encryption largely depends on the key.

**Encryption Algorithm**: Mathematical method or procedure used to encrypt and decrypt data.

## 2. Types of Encryption

### Symmetric Encryption:

Uses the same key for encryption and decryption.
- **Advantages**: Faster and more efficient for large amounts of data.
- **Disadvantages**: The key must be securely shared between parties.
- **Common algorithms**:
  - **AES (Advanced Encryption Standard)**: Modern, secure, and fast encryption algorithm, using 128, 192, or 256-bit keys.
  - **DES (Data Encryption Standard)**: Older symmetric encryption standard, less secure nowadays.
  - **3DES (Triple DES)**: Improvement of DES, applying DES encryption three times to increase security.

### Asymmetric Encryption:

Uses a key pair: a public key for encryption and a private key for decryption.
- **Advantages**: Allows secure key exchange without needing a secure channel for key distribution.
- **Disadvantages**: Slower and less efficient for large amounts of data.
- **Common algorithms**:
  - **RSA (Rivest-Shamir-Adleman)**: Widely used asymmetric encryption algorithm, based on the difficulty of factoring large prime numbers.
  - **ECC (Elliptic Curve Cryptography)**: Algorithm using elliptic curve properties to offer security equivalent to RSA with shorter keys and faster calculations.

## 3. Hash Functions

Hash functions generate a fixed value (hash) from variable-length data, often used to verify data integrity.

### Properties of hash functions:

- **One-way**: Difficult to reconstruct original data from the hash.
- **Sensitivity to changes**: A small modification of the original data produces a completely different hash.
- **Minimal collisions**: Two different data sets should not produce the same hash.

### Common algorithms:

- **SHA-2 (Secure Hash Algorithm 2)**: Family of hash functions including SHA-256 and SHA-512, widely used for its robust security.
- **SHA-3**: Recent hash algorithm, alternative to SHA-2, based on a different design for more cryptographic diversity.
- **MD5 (Message Digest 5)**: Older hash algorithm, now considered insecure due to its vulnerability to collisions.

## Unix Cryptography Tools

### GPG

GPG (GNU Privacy Guard) is a powerful tool for data encryption and signing, offering a wide variety of advanced options for increased security and flexibility.

Here is a detailed guide to its advanced features and their usage.

Manual page for GPG: https://www.gnupg.org/documentation/manuals/gnupg24/gpg.1.html

#### 1. Installing GPG

On Unix/Linux systems, GPG is often pre-installed. If not, you can install it using your distribution's package managers.

Debian/Ubuntu:
```
sudo apt-get install gnupg
```

Fedora:
```
sudo dnf install gnupg
```

Arch Linux:
```
sudo pacman -S gnupg
```

#### 2. Generating GPG Keys

Basic command:
```
gpg --gen-key
```

Specify key parameters:
```
gpg --full-gen-key
```

This allows you to choose the key type (RSA, DSA, etc.), key size, and validity period.

Generate a 4096-bit RSA key without user interaction:
```
gpg --batch --gen-key <<EOF
%no-protection
Key-Type: RSA
Key-Length: 4096
Name-Real: John Doe
Name-Email: john.doe@example.com
Expire-Date: 0
%commit
EOF
```

#### 3. Key Management

Export public key:
```
gpg --export -a 'John Doe' > public.key
```

Export private key:
```
gpg --export-secret-keys -a 'John Doe' > private.key
```

Import keys:
```
gpg --import public.key
gpg --import private.key
```

Key revocation:

Generate a revocation certificate:
```
gpg --output revoke.asc --gen-revoke 'John Doe'
```

Import and use the revocation certificate:
```
gpg --import revoke.asc
```

#### 4. Encrypting and Decrypting Files

Encrypt a file:
```
gpg -c file
```

Use a specific algorithm (AES256):
```
gpg --cipher-algo AES256 -c file
```

Encrypt a file for a specific recipient using their public key:
```
gpg --encrypt --recipient 'John Doe' file
```

Decrypt a file:
```
gpg file.gpg
```

#### 5. Digital Signatures

Sign a file:
```
gpg --sign file
```

Sign and encrypt a file simultaneously:
```
gpg --sign --encrypt --recipient 'John Doe' file
```

Create a detached signature (useful for verifying integrity without modifying the original file):
```
gpg --detach-sign file
```

Verify a signature:
```
gpg --verify file.gpg
```

Or for a detached signature:
```
gpg --verify file.sig file
```

#### 6. Managing Keyrings

List public keys:
```
gpg --list-keys
```

List private keys:
```
gpg --list-secret-keys
```

Delete a public key:
```
gpg --delete-key 'John Doe'
```

Delete a private key:
```
gpg --delete-secret-key 'John Doe'
```

#### 7. Using GPG-Agent for Passphrase Management

Start GPG-Agent:
```
gpg-agent --daemon
```

Configure GPG to use GPG-Agent:

Add the following lines to ~/.gnupg/gpg.conf:
```
use-agent
```

Add the following lines to ~/.gnupg/gpg-agent.conf:
```
default-cache-ttl 600
max-cache-ttl 7200
```

#### 8. Integrating GPG with Applications

Email:

Use plugins like Enigmail for Thunderbird or GPG integrations for email clients like Mutt.

Git:

Sign commits with GPG:
```
git config --global user.signingkey <your_key>
git commit -S -m "Your commit message"
```

### OpenSSL

OpenSSL is a robust toolkit for encryption and digital certificate management. It provides commands for working with encryption algorithms, generating keys, and creating and verifying certificates. Here is a detailed guide on advanced options and using OpenSSL.

#### 1. Installing OpenSSL

On Unix/Linux systems, OpenSSL is often pre-installed. If not, you can install it using your distribution's package managers.

Debian/Ubuntu:
```
sudo apt-get install openssl
```

Fedora:
```
sudo dnf install openssl
```

Arch Linux:
```
sudo pacman -S openssl
```

#### 2. Key Generation

Generate an RSA private key:
```
openssl genpkey -algorithm RSA -out private_key.pem -aes256
```

Advanced options:
- `-algorithm RSA`: Specifies the RSA key algorithm.
- `-aes256`: Encrypts the private key with AES-256 for increased protection.

Generate a public key from a private key:
```
openssl rsa -pubout -in private_key.pem -out public_key.pem
```

Generate an EC (Elliptic Curve) key:
```
openssl ecparam -name prime256v1 -genkey -noout -out ec_private_key.pem
```
- `-name prime256v1`: Specifies the elliptic curve to use.

#### 3. File Encryption and Decryption

Encrypt a file with AES-256:
```
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc
```

Advanced options:
- `-aes-256-cbc`: Uses the AES algorithm in CBC mode with a 256-bit key.
- `-salt`: Adds salt to strengthen encryption security.

Decrypt a file:
```
openssl enc -d -aes-256-cbc -in file.enc -out file.txt
```

Asymmetric encryption with RSA:
```
openssl rsautl -encrypt -inkey public_key.pem -pubin -in file.txt -out file.enc
```

Asymmetric decryption with RSA:
```
openssl rsautl -decrypt -inkey private_key.pem -in file.enc -out file.txt
```

#### 4. Certificate Creation and Management

Generate a certificate signing request (CSR):
```
openssl req -new -key private_key.pem -out request.csr
```

Advanced options:
- `-new`: Creates a new certificate signing request.
- `-key private_key.pem`: Uses the private key to sign the request.

Generate a self-signed certificate:
```
openssl req -new -x509 -key private_key.pem -out certificate.crt -days 365
```
- `-x509`: Creates a self-signed certificate.
- `-days 365`: Specifies the certificate validity period.

Display certificate details:
```
openssl x509 -in certificate.crt -text -noout
```

Verify the correspondence between a key and a certificate:
```
openssl x509 -noout -modulus -in certificate.crt | openssl md5
```
```
openssl rsa -noout -modulus -in private_key.pem | openssl md5
```

The MD5 values must match to validate that the key and certificate correspond.

#### 5. Hash Creation and Verification

Generate an MD5, SHA256, etc. hash:
```
openssl dgst -md5 file.txt
```
```
openssl dgst -sha256 file.txt
```

Verify a hash:

Compare the generated hash with an expected value to verify file integrity.

#### 6. Using Initialization Vectors (IV)

Encrypt with a custom IV:
```
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -iv 00000000000000000000000000000000
```
- `-iv`: Specifies a custom initialization vector in hexadecimal.

Decrypt with a custom IV:
```
openssl enc -d -aes-256-cbc -in file.enc -out file.txt -iv 00000000000000000000000000000000
```

#### 7. Certificate and Key Management

Convert certificate formats:

PEM to DER:
```
openssl x509 -in certificate.pem -outform DER -out certificate.der
```

DER to PEM:
```
openssl x509 -in certificate.der -inform DER -out certificate.pem
```

Extract private key from a PKCS#12 file:
```
openssl pkcs12 -in keystore.p12 -nocerts -out private_key.pem
```

Extract certificate from a PKCS#12 file:
```
openssl pkcs12 -in keystore.p12 -clcerts -nokeys -out certificate.crt
```

#### 8. Creating Certificate Chains

Create a certificate chain:

Combine multiple certificates into a single PEM file:
```
cat intermediate.crt root.crt > chain.pem
```

Verify a certificate chain:
```
openssl verify -CAfile chain.pem certificate.crt
```

#### 9. Using OpenSSL for SSL/TLS

Test SSL/TLS connection to a server:
```
openssl s_client -connect example.com:443
```

Display protocols supported by a server:
```
openssl s_client -connect example.com:443 -tls1_2
```

Verify a server's SSL/TLS configuration:
```
openssl s_client -connect example.com:443 -showcerts
```

### cryptsetup

cryptsetup is a powerful utility for managing disk volume encryption under Linux, especially with LUKS (Linux Unified Key Setup).

Here is a detailed guide on advanced features and their usage.

#### 1. Installing cryptsetup

On Unix/Linux systems, cryptsetup is often pre-installed.

If not, it can be installed via your distribution's package managers.

Debian/Ubuntu:
```
sudo apt-get install cryptsetup
```

Fedora:
```
sudo dnf install cryptsetup
```

Arch Linux:
```
sudo pacman -S cryptsetup
```

#### 2. Creating an Encrypted Volume with LUKS

To initialize a volume with LUKS, use the command:
```
cryptsetup luksFormat /dev/sdX
```

You can specify advanced options such as:
- `--cipher`: Choose the encryption algorithm, for example, AES-256-CBC.
- `--hash`: Define the hash algorithm, for example, SHA-512.
- `--key-size`: Specify the key size, for example, 256 bits for AES-256.

To open an encrypted volume, use:
```
cryptsetup luksOpen /dev/sdX volume_name
```

You can add options such as:
- `--key-file`: Use a key file to unlock the volume instead of a passphrase.
- `--header`: Specify an alternative LUKS header, useful for backups and restorations.

#### 3. Key Management

To add a key to a LUKS volume, use:
```
cryptsetup luksAddKey /dev/sdX
```

which will ask for the old key before adding the new one. To remove a key, use the following command by providing the key you want to remove:
```
cryptsetup luksRemoveKey /dev/sdX
```

To change the passphrase, use:
```
cryptsetup luksChangeKey /dev/sdX
```

#### 4. Manipulating Encrypted Volumes

After opening an encrypted volume, you can create a file system with:
```
mkfs.ext4 /dev/mapper/volume_name
```

and mount the volume with:
```
mount /dev/mapper/volume_name /mnt/mount_point
```

To unmount and close the volume, use:
```
umount /mnt/mount_point
cryptsetup luksClose volume_name
```

#### 5. Encrypting and Decrypting Entire Disks

To encrypt an entire partition, initialize and open the volume with:
```
cryptsetup luksFormat /dev/sdX
cryptsetup luksOpen /dev/sdX volume_name
```

Format the volume with the following command then mount it:
```
mkfs.ext4 /dev/mapper/volume_name
```

You can also encrypt an entire disk using LUKS2 with:
```
cryptsetup luksFormat --type luks2 /dev/sdX
```

#### 6. Backing Up and Restoring LUKS Headers

To back up the LUKS header, use:
```
cryptsetup luksHeaderBackup /dev/sdX --header-backup-file /path/to/backup.img
```

To restore the header, use:
```
cryptsetup luksHeaderRestore /dev/sdX --header-backup-file /path/to/backup.img
```

Check the header status with:
```
cryptsetup luksDump /dev/sdX
```

#### 7. Advanced Operations

To encrypt a hot device, you can create an encrypted volume in a file, then format and mount this volume. Check the volume status with:
```
cryptsetup status volume_name
```

To convert between LUKS1 and LUKS2, use:
```
cryptsetup convert --type luks2 /dev/sdX
```

### ssh-keygen

ssh-keygen is an essential tool for creating and managing keys used with SSH (Secure Shell).

Here is a guide on its advanced features and their usage.

#### 1. Generating SSH Keys

To generate a new SSH key pair, use:
```
ssh-keygen
```

Advanced options include:
- `-t`: Specifies the key type, such as RSA, DSA, ECDSA, or Ed25519.
- `-b`: Sets the key size in bits, for example, 4096 for RSA.
- `-C`: Adds a comment to the key, such as an email address.
- `-f`: Specifies the output file for the key (default ~/.ssh/id_rsa).

For example, to generate a 4096-bit RSA key with a comment, use the appropriate options with ssh-keygen.

#### 2. SSH Key Management

To display the public key associated with a private key, use the option to display the public key. If the key is protected by a passphrase, you will need to provide it.

To convert between key formats, such as PEM to OpenSSH or vice versa, use the specific options. To change the passphrase of a private key, use the option to modify the passphrase.

To display the fingerprint of a public key, use the option to display the fingerprint.

#### 3. Advanced Key Usage

To create a key with a specific algorithm, such as Ed25519, use the appropriate options with ssh-keygen.

To create a key with custom parameters, such as a more secure key format or a high number of hash iterations for the passphrase, use the available advanced options.

#### 4. Managing Public and Private Keys

To add a public key to a server, add the key to the authorized_keys file on the server. You can use ssh-copy-id to automate this process.

To manage multiple SSH keys, edit the SSH configuration file (~/.ssh/config). Add sections to specify which key to use for which server.

#### 5. Debugging and Verification

To verify if a public key is accepted by a server, try connecting with ssh by specifying the key. To debug an SSH connection, use the option to display detailed debugging information.

#### 6. Key Backup and Restoration

To back up keys, copy the private and public key files to a secure location. Use tools like scp or rsync to securely transfer the keys.

To restore keys, ensure that the file permissions are correctly set and that public keys are added to the authorized_keys file of the appropriate servers.
