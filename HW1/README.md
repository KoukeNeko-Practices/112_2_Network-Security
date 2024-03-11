
# RSA 和 AES 加密解密操作手冊

本手冊提供了使用RSA和AES結合進行檔案加密和解密的完整步驟，包括生成帶有密碼保護的RSA金鑰對，以及使用RSA加密的AES對稱金鑰來加密和解密檔案的過程。

## 前提條件

- 安裝OpenSSL：確保你的系統中已經安裝了OpenSSL工具。可以透過在終端機中執行`openssl version`來檢查是否已安裝。

## 操作步驟

### 1. 生成帶密碼的RSA私鑰和對應的公鑰

首先，我們需要生成一對RSA金鑰，其中私鑰被一個密碼所保護。

```bash
openssl genpkey -algorithm RSA -out private_key.pem -aes-256-cbc -pass pass:B11017070 -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in private_key.pem -out public_key.pem -passin pass:B11017070
```

### 2. 生成一個對稱金鑰

使用以下指令生成一個對稱金鑰（AES金鑰），用於檔案加密。

```bash
openssl rand -base64 32 > sym_key.bin
```

### 3. 使用公鑰加密對稱金鑰

接下來，使用RSA公鑰來加密這個對稱金鑰。

```bash
openssl pkeyutl -encrypt -in sym_key.bin -out sym_key_encrypted.bin -pubin -inkey public_key.pem
```

### 4. 使用對稱金鑰加密檔案

現在，我們可以使用對稱金鑰來加密你想保護的檔案了。

```bash
openssl enc -aes-256-cbc -salt -in hw0305.txt -out hw0305.txt.enc -pass file:./sym_key.bin
```

### 5. 使用私鑰解密對稱金鑰

若需要解密檔案，首先使用帶有密碼的私鑰來解密對稱金鑰。

```bash
openssl pkeyutl -decrypt -in sym_key_encrypted.bin -out sym_key_decrypted.bin -inkey private_key.pem -passin pass:B11017070
```

### 6. 使用對稱金鑰解密檔案

最後，使用解密出的對稱金鑰來解密檔案。

```bash
openssl enc -d -aes-256-cbc -in hw0305.txt.enc -out hw0305_decrypted.txt -pass file:./sym_key_decrypted.bin
```

## 結語

在RSA加密中，可以加密的資料量是有限的，依賴於金鑰的大小。對於2048位的RSA金鑰，最大的 Data Block 大小約為245 word。對於更大的文件，通常的做法是使用對稱加密（如AES）來加密文件本身，然後使用RSA加密對稱金鑰。
