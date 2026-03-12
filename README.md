# Verify Email dengan Firebase + Postman

Dokumentasi cara verifikasi email pakai Firebase Authentication dan testing API-nya pakai Postman.

**Nama:** David Saputra
**NIM:** *1123150039*

---

## Daftar Isi

- [1. Setup Firebase Project](#1-setup-firebase-project)
- [2. Enable Authentication](#2-enable-authentication)
- [3. Ambil Firebase API Key](#3-ambil-firebase-api-key)
- [4. Login ke Postman](#4-login-ke-postman)
- [5. Buat Pengujian Baru di Postman](#5-buat-pengujian-baru-di-postman)
- [6. Registrasi User via Firebase REST API](#6-registrasi-user-via-firebase-rest-api)
- [7. Kirim Email Verification](#7-kirim-email-verification)
- [8. Cek Status Verifikasi Email](#8-cek-status-verifikasi-email)

---

## 1. Setup Firebase Project

1. Buka [https://console.firebase.google.com](https://console.firebase.google.com)
2. Klik **Add Project**
3. Masukin nama project
4. Klik **Continue** sampai project selesai dibuat

![Step 1 - Setup Firebase Project](images/step1(1).png)

---

## 2. Enable Authentication

1. Di sidebar Firebase, klik **Build** > **Authentication**
2. Klik **Get Started**
3. Masuk ke tab **Sign-in Method**
4. Aktifin **Email/Password**, klik **Save**
5. Klik **Add New Provider**, pilih **Google**

<!-- SS sign-in method -->

---

## 3. Ambil Firebase API Key

1. Klik **Project Settings** (ikon gear di sidebar)
2. Tab **General**, scroll ke **Your Apps**
3. Copy **Web API Key** — ini yang dipake di Postman

<!-- SS project settings dan API Key -->

---

## 4. Login ke Postman

1. Buka [Postman](https://www.postman.com/downloads/), login atau sign up
2. Masuk ke dashboard

<!-- SS halaman postman -->

---

## 5. Buat Pengujian Baru di Postman

1. Buat **Folder** baru di **Collection** dan **Environments**
2. Klik **Environments** > **Create New**, rename jadi `My Environment`
3. Tambah variabel:

| Variable | Value |
|---|---|
| `FIREBASE_API_KEY` | *(paste API Key dari Firebase)* |

<!-- SS setup environment postman -->

---

## 6. Registrasi User via Firebase REST API

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:signUp?key={{FIREBASE_API_KEY}}
```

### Setup Request

1. Method **POST**, masukin URL di atas
2. Tab **Headers**:

| Key | Value |
|---|---|
| `Content-Type` | `application/json` |

3. Tab **Body** > **Raw** > **JSON**, isi:

```json
{
  "email": "emailkamu@gmail.com",
  "password": "password123",
  "returnSecureToken": true
}
```

4. Klik tab **Script** biar `idToken` kesimpen otomatis

### Response

- **200 OK** — Registrasi berhasil. Firebase ngirim balik `idToken`, `refreshToken`, dan `expiresIn`.
- **400 Bad Request** — Gagal. Biasanya karena email udah terdaftar, format salah, atau password kurang dari 6 karakter.

<!-- SS response -->

---

## 7. Kirim Email Verification

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:sendOobCode?key={{FIREBASE_API_KEY}}
```

### Setup Request

1. Bikin request baru, method **POST**
2. Header `Content-Type: application/json`
3. Body **Raw** > **JSON**:

```json
{
  "requestType": "VERIFY_EMAIL",
  "idToken": "{{ID_TOKEN}}"
}
```

> `idToken` ambil dari response registrasi tadi.

4. Klik **Send**, cek inbox email (cek folder Spam juga)
5. Klik link verifikasi di email

<!-- SS kirim verifikasi -->

### Ubah Template Email (Opsional)

Buka **Firebase Console** > **Authentication** > tab **Templates**, edit sesuai kebutuhan.

<!-- SS template email firebase -->

---

## 8. Cek Status Verifikasi Email

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:lookup?key={{FIREBASE_API_KEY}}
```

### Setup Request

1. Bikin request baru, method **POST**
2. Body **Raw** > **JSON**:

```json
{
  "idToken": "{{ID_TOKEN}}"
}
```

3. Klik **Send**, cek field `emailVerified` di response:
   - `true` = sudah diverifikasi
   - `false` = belum diverifikasi

<!-- SS response lookup -->
