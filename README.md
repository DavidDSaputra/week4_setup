#  Verify Email dengan Firebase + Postman

Dokumentasi cara verifikasi email pakai Firebase Authentication dan testing API-nya pakai Postman.
Ini buat mastiin alur auth Firebase jalan bener sebelum dipasang ke Flutter nanti.

**Nama:** David Saputra
**NIM:** *1123150039*

---

##  Daftar Isi

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

Pertama bikin project baru dulu di Firebase Console.

1. Buka [https://console.firebase.google.com](https://console.firebase.google.com)
2. Klik **Add Project**
3. Masukin nama project-nya
4. Klik **Continue** terus sampai project-nya selesai dibuat

Nanti kalau udah selesai bakal diarahkan ke dashboard Firebase.



---

## 2. Enable Authentication

Sekarang kita aktifin fitur Authentication-nya.

1. Di sidebar Firebase, klik **Build**
2. Pilih menu **Authentication**
3. Klik **Get Started**

<!-- SS menu authentication -->

Terus aktifin metode login **Email/Password** sama **Google**:

1. Masuk ke tab **Sign-in Method**
2. Klik **Email/Password**
3. Aktifin toggle **Enable**
4. Klik **Save**
5. Habis itu klik **Add New Provider**, pilih **Google**

<!-- SS sign-in method -->

---

## 3. Ambil Firebase API Key

Buat pake Firebase API lewat Postman, kita butuh API Key.

1. Klik **Project Settings** (ikon gear di sidebar)
2. Masuk ke tab **General**
3. Scroll ke bagian **Your Apps**
4. Salin nilai **Web API Key** — ini yang bakal dipake di Postman nanti

<!-- SS project settings dan API Key -->

---

## 4. Login ke Postman

Sekarang buka Postman buat testing Firebase REST API.

1. Buka aplikasi **Postman** (download dulu kalau belum punya di [postman.com](https://www.postman.com/downloads/))
2. Kalau belum punya akun, **Sign Up** dulu
3. Kalau udah punya, tinggal **Login**
4. Nanti masuk ke dashboard Postman

<!-- SS halaman postman -->

---

## 5. Buat Pengujian Baru di Postman

Setup environment dulu biar gampang.

1. Buat **Folder** baru di bagian **Collection** sama **Environments**
2. Klik **Environments**
3. Pilih **Create New**, terus ganti namanya jadi `My Environment`
4. Isi variabel dan value-nya kayak gini:

| Variable | Value |
|---|---|
| `FIREBASE_API_KEY` | *(paste API Key dari Firebase)* |

<!-- SS setup environment postman -->

---

## 6. Registrasi User via Firebase REST API

Di tahap ini kita daftarin user baru lewat Firebase Auth API.

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:signUp?key={{FIREBASE_API_KEY}}
```

Ganti `{{FIREBASE_API_KEY}}` sama API Key yang tadi udah dicopy.

### Setup Request di Postman

1. Pilih method **POST**
2. Masukin URL endpoint di atas
3. Masuk ke tab **Headers**, tambahin:

| Key | Value |
|---|---|
| `Content-Type` | `application/json` |

4. Masuk ke tab **Body**
5. Pilih **Raw**
6. Pilih format **JSON**
7. Isi body-nya kayak gini:

```json
{
  "email": "emailkamu@gmail.com",
  "password": "password123",
  "returnSecureToken": true
}
```

8. Klik tab **Script** supaya `idToken` bisa kesimpen otomatis

### Hasil Response

**✅ 200 OK — Berhasil**

Request berhasil diproses. User udah kebikin dan Firebase ngirim balik response berupa `idToken`, `refreshToken`, sama `expiresIn`. Artinya registrasi sukses.

<!-- SS response berhasil -->

**❌ 400 Bad Request — Gagal**

Kalau gagal, biasanya karena:
- Email udah terdaftar sebelumnya
- Format email salah
- Password terlalu pendek (minimal 6 karakter)

<!-- SS response gagal -->

---

## 7. Kirim Email Verification

Setelah user berhasil register, sekarang kirim email verifikasi buat mastiin email-nya bener.

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:sendOobCode?key={{FIREBASE_API_KEY}}
```

### Setup Request di Postman

1. Bikin **New Request**
2. Pilih method **POST**
3. Masukin endpoint di atas
4. Masuk ke tab **Headers**, isi:

| Key | Value |
|---|---|
| `Content-Type` | `application/json` |

5. Masuk ke tab **Body**
6. Pilih **Raw**, format **JSON**
7. Isi body-nya:

```json
{
  "requestType": "VERIFY_EMAIL",
  "idToken": "{{ID_TOKEN}}"
}
```

> `idToken` ambil dari response registrasi tadi (atau dari environment kalau udah di-save otomatis).

8. Klik **Send**, terus cek inbox email. Biasanya masuk ke **Spam**, jadi cek juga folder spam-nya
9. Klik link verifikasi di email tersebut

<!-- SS kirim verifikasi dan email masuk -->

### Ubah Template Email (Opsional)

Kalau mau custom template email verifikasi-nya:

1. Buka **Firebase Console**
2. Pilih **Authentication**
3. Masuk ke tab **Templates**
4. Edit sesuai keinginan

<!-- SS template email firebase -->

---

## 8. Cek Status Verifikasi Email

Terakhir, cek apakah email udah ter-verifikasi atau belum.

**Endpoint:**
```
POST https://identitytoolkit.googleapis.com/v1/accounts:lookup?key={{FIREBASE_API_KEY}}
```

### Setup Request di Postman

1. Bikin **New Request**
2. Pilih method **POST**
3. Masukin endpoint di atas
4. Masuk ke tab **Body**
5. Pilih **Raw**, format **JSON**
6. Isi body-nya:

```json
{
  "idToken": "{{ID_TOKEN}}"
}
```

7. Klik **Send**
8. Cek di response-nya, cari field `emailVerified`
   - `true` = email udah diverifikasi 
   - `false` = belum diverifikasi 

<!-- SS response lookup -->

---

## Setup Repository

```bash
echo "# week4_setup" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/DavidDSaputra/week4_setup.git
git push -u origin main
```

---

Selesai! Kalau ada yang error atau bingung, coba ulang dari awal dan pastiin API Key-nya bener
