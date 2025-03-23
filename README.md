# Cryptographic API Implementation

This project is a Flask-based web service that provides a variety of cryptographic operations including key generation, encryption/decryption for symmetric and asymmetric key cryptography, and hash generation/verification. The service supports multiple algorithms and securely manages keys with an SQLite database and MySQL database hosted by [ freesqldatabase.com]( freesqldatabase.com). The APIs are deployed on [Vercel](https://vercel.com/) and accessible at the base URL [https://cryptographicapi.vercel.app](https://cryptographicapi.vercel.app).

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Installation](#installation)
- [Deployment](#deployment)
  - [Vercel Deployment](#vercel-deployment)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
  - [Generate Key](#generate-key)
  - [Encrypt Data](#encrypt-data)
  - [Decrypt Data](#decrypt-data)
  - [Generate Hash](#generate-hash)
  - [Verify Hash](#verify-hash)
- [Database Structure](#database-structure)

---

## Overview

This project implements a RESTful API for cryptographic functions. It allows you to:
- **Generate keys** for symmetric algorithms (AES, DES, 3DES) and asymmetric RSA.
- **Encrypt** plaintext using the generated keys.
- **Decrypt** ciphertext back to plaintext.
- **Generate and verify hashes** using SHA-256 and SHA-512.

Keys and IVs are stored in a database, and all cryptographic functions are implemented using Cryptography and PyCrypto libraries. The `key_Gen_Enc_Dec_db_local.py` uses SQLite database by ceeating a local database and the `key_Gen_Enc_Dec_db_remote.py` uses a remote MySQL database hosted by [freesqldatabase.com]( freesqldatabase.com).

---

## Features

- **Key Generation:** Supports symmetric key algorithms (AES, DES, 3DES) with configurable key sizes and RSA asymmetric key generation.
- **Encryption/Decryption:** Uses CBC mode for symmetric encryption and OAEP padding for RSA.
- **Hashing:** Provides endpoints to generate and verify hashes using SHA-256 and SHA-512.
- **Database Integration:** Automatically creates and updates an SQLite database/ MySQL database hosted by [ freesqldatabase.com]( freesqldatabase.com)  to store keys and IVs.
- **Flask API:** Simple and lightweight RESTful API built with Flask.
- **Cloud Deployment:** Deployed on Vercel for easy access.

---

## Technologies Used

- **Python:** Version 3.13.2
- **Flask:** 3.1.0 – For creating the API endpoints.
- **cryptography:** 44.0.2 – For AES, RSA, and 3DES.
- **pycryptodome:** 3.22.0 – For DES.
- **requests:** 2.32.3 – For making HTTP requests (if needed).
- **SQLite:** For SQLite database connection.
- **pymysql:** For MySQL database connection.
- **python-dotenv:** For environment variable management.
- **Vercel:** For serverless deployment.

---

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/PRABUDDHIKAMWR/EN4720-Security-in-Cyber-Physical-Systems.git
   cd EN4720-Security-in-Cyber-Physical-Systems
   ```

2. **Set up a virtual environment (recommended):**
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Install the required dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
   *Alternatively, install dependencies manually:*
   ```bash
   pip install Flask==3.1.0 cryptography==44.0.2 pycryptodome==3.22.0 requests==2.32.3 PyMySQL==1.1.1 python-dotenv==1.0.1
   ```

4. **Create a .env file in the root directory with your database credentials:**
   ```bash
   DATABASE=your_database
   HOST=host_name
   USER=your_username
   PASSWORD=your_password
   CHARSET=charset(ex:utf8mb4)
   ```

 5. **Run the application locally:**
   ```bash
   python app.py
   ```
   The service will start on [http://127.0.0.1:5000](http://127.0.0.1:5000).

---

## Deployment

### Vercel Deployment

The API is deployed on Vercel and accessible at the following endpoints:

- **Base URL:** https://cryptographicapi.vercel.app
- **Generate Key:** https://cryptographicapi.vercel.app/generate-key
- **Encrypt:** https://cryptographicapi.vercel.app/encrypt
- **Decrypt:** https://cryptographicapi.vercel.app/decrypt
- **Generate Hash:** https://cryptographicapi.vercel.app/generate-hash
- **Verify Hash:** https://cryptographicapi.vercel.app/verify-hash

To deploy your own version:

1. **Create a `vercel.json` file:**
```bash 
{
  "version": 2,
  "builds": [
    {
      "src": "app.py",
      "use": "@vercel/python"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "app.py"
    }
  ]
}
```

2. **Deploy to Vercel:**
   
```bash
npm install -g vercel
vercel login
vercel
```

3. **Set environment variables** in the Vercel dashboard.

Note: The serverless nature of Vercel means your functions will "cold start" after periods of inactivity.

---

## Usage

Once the service is running, you can interact with the API using tools like [Postman](https://www.postman.com) or [cURL](https://curl.se).

### Example: Generate a key using cURL

```bash
curl -X POST http://127.0.0.1:5000/generate-key \
     -H "Content-Type: application/json" \
     -d '{"key_type": "AES", "key_size": 256}'
```
or
```bash
curl -X POST https://cryptographicapi.vercel.app/generate-key \
     -H "Content-Type: application/json" \
     -d '{"key_type": "AES", "key_size": 256}'
```

Refer `test.ipynb` for more detailed evaluation.

---

## API Endpoints

### Generate Key

**Endpoint:** `/generate-key`  
**Method:** `POST`  
**Request Body Parameters:**
- `key_type` (string): Type of key to generate. Options: `AES`, `DES`, `3DES`, `RSA`.
- `key_size` (integer or string): Size of the key.  
  - **AES:** `128`, `192`, `256`
  - **DES:** `56` or `64`
  - **3DES:** `56`, `64`, `112`, `128`, `168`, or `192`
  - **RSA:** `1024`, `2048`, `3072`, or `4096`

**Response:**
- Returns JSON with `key_id` and `key_value` (Base64 encoded).

---

### Encrypt Data

**Endpoint:** `/encrypt`  
**Method:** `POST`  
**Request Body Parameters:**
- `algorithm` (string): Algorithm to use for encryption (`AES`, `DES`, `3DES`, `RSA`).
- `key_id` (integer): The ID of the key from the database.
- `plaintext` (string): The plaintext to encrypt.

**Response:**
- Returns JSON with the `ciphertext` (Base64 encoded).

---

### Decrypt Data

**Endpoint:** `/decrypt`  
**Method:** `POST`  
**Request Body Parameters:**
- `algorithm` (string): Algorithm to use for decryption (`AES`, `DES`, `3DES`, `RSA`).
- `key_id` (integer): The ID of the key from the database.
- `ciphertext` (string): The ciphertext to decrypt (Base64 encoded).

**Response:**
- Returns JSON with the `plaintext`.

---

### Generate Hash

**Endpoint:** `/generate-hash`  
**Method:** `POST`  
**Request Body Parameters:**
- `data` (string): Data to hash.
- `algorithm` (string): Hash algorithm (`SHA-256` or `SHA-512`).

**Response:**
- Returns JSON with the `hash_value` (Base64 encoded) and the algorithm used.

---

### Verify Hash

**Endpoint:** `/verify-hash`  
**Method:** `POST`  
**Request Body Parameters:**
- `data` (string): Original data.
- `hash_value` (string): Hash value to verify (Base64 encoded).
- `algorithm` (string): Hash algorithm (`SHA-256` or `SHA-512`).

**Response:**
- Returns JSON indicating if the hash matches the data (`is_valid` flag) along with a corresponding message.

---

## Database Structure

The application uses a MySQL database with a single table named `APIkeys`:

- **id**: Primary key (INTEGER AUTO_INCREMENT).
- **private_key**: The private key stored as a Base64 encoded blob.
- **public_key**: The public key for RSA (Base64 encoded), if applicable.
- **algorithm**: The encryption algorithm associated with the key.
- **iv**: BLOB - Initialization vector for symmetric algorithms (stored as a blob).

The application uses an SQLite database (`key_database.sqlite`) with a single table named `keys`:

- **id**: Primary key (INTEGER).
- **private_key**: The private key stored as a Base64 encoded blob.
- **public_key**: The public key for RSA (Base64 encoded), if applicable.
- **algorithm**: The encryption algorithm associated with the key.
- **iv**: Initialization vector for symmetric algorithms (stored as a blob).

The table is automatically created when a key is generated if it does not already exist.

*This README provides an overview of the service and instructions for setup and usage. For any issues or contributions, please open an issue or pull request in the repository.*
