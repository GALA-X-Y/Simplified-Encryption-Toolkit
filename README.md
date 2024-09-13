# Simplified Encryption Toolkit (SET)

Simplified Encryption Toolkit (SET) is a toolkit set designed to increase the ease of using advanced encryptions algorithms from python plugin [cryptography](https://cryptography.io/en/latest/). Besides of working as an independent encryption tool that encrypts files , SET also helps developers apply encryptions in other programs. 

## Key Features

- Object-Oriented Design: SET is divided into modules, which all of them can be imported seperately depends on different situations. Futhermore, classes are defined by layers. The structural design makes tracing errors a more manageable job.

- Straight Forward: SET asks for only necessary inputs of parameters, simplify sophisticated extra options in original plugin functions, and return a single output.

- Easy-to-implement: Only one line of statement is need to call SET's functions, thus developers can easily embed SET into bigger projects.

- Stand-alone Tool: SET can works as a stand-alone tool in handling file encryptions, and operation time counter is embed for direct runs.

## Installation

To use SET, clone the repository or download the source code from GitHub. Ensure you have Python 3 installed on your system, along with the required packages.

```
git clone https://github.com/GALA-X-Y/Simplified-Encryption-Toolkit.git
cd Simplified-Encryption-Toolkit
pip install -r requirements.txt
```

## Direct Usage

SET consists of two python files, `aes.py` and `rsa.py`, implemented with symmetric and asymmetric encryption respectively. 

The main difference between `aes.py` and `rsa.py` is that `aes.py` uses passphrases to perform encryption and decryption, while `rsa.py` rely on a pair of public key and private key to perform encryption and decryption respectively. If you hope to know more details, please refer to [AES Encryption (Symmetrical)](#aes) and [RSA Encryption (Asymmetrical)](#rsa).

For both files, run them by typing command in terminal instead of running in IDE environments.

### Encrypt a File (AES)
To encrypt a file using `aes.py`, use the following command:
```
python aes.py encrypt target.txt output.bin
```
This command encrypts `target.txt` and saves the encrypted bytes in `output.bin`[^3].

### Decrypt a File (AES)
To decrypt a file using `aes.py`, use the following command:
```
python aes.py decrypt encrypted.bin [output.txt]
```
This command decrypts `encrypted.bin` and restore the original file `output.txt`[^4].

### Encrypt a File (RSA)
To encrypt a file using `rsa.py`, use the following command:
```
python rsa.py encrypt target.txt public_key.pem output.bin
```
This command encrypts `target.txt` using public key file `public_key.pem` and saves the encrypted bytes in `output.bin`[^3].

### Decrypt a File (RSA)
To decrypt a file using `rsa.py`, use the following command:
```
python rsa.py decrypt encrypted.bin private_key.pem [output.txt]
```
This command decrypts `encrypted.bin` using private key file `private_key.pem` and restore the original file `output.txt`[^4].

## Encryption Algorithms (For Developers)

<a name="aes"></a>

### AES Encryption (Symmetrical)

`aes.py` is the Python file implemented with AES (Advanced Encryption Standard) symmetric encryption. Inside, an user-custom key is used for symmetrical encryption. Then a more complex key of 256-bit is derived from the custom key using PBKDF2 (Password-Based Key Derivation Function 2) with HMAC-SHA-256 as the hash function. The number of iterations for the key derivation is set to 200,000, which increases the cost of brute-force attacks. 

Other than that, CBC (Cipher Block Chaining) mode that divides plaintext into blocks is used for the AES encryption. A 128-bit Initialization Vector (IV) ensures that identical plaintext blocks will produce different ciphertext blocks, enhancing security.

It consists of two layers: Core Layer (class `AES_BASE`) and File-Encryption Layer (class `AES_on_File`).

#### Core Layer

In Core Layer, the program focuses on the only encryptions and decryptions using AES algorithm. 

The class `AES_BASE()` requires no initialization parameters, and is embed with two functions:

```
encrypt_with_aes(data, session_key)
```

Encrypt data with session key using AES under CBC mode.

**Parameters:**
- data(bytes) : data to be encrypted
- session_key(str/bytes) : the key to be used in AES encryption

**Returns:**
- packed_data(bytes) : encrypted data packed with PBKDF2 salt and CBC IV

**Raises:**
- ValueError - Data or session key are not inputted.

```
decrypt_with_aes(packed_data, session_key)
```

Reverse of `encrypt_with_aes()`.

**Parameters:**
- packed_data(bytes) : encrypted data packed with PBKDF2 salt and CBC IV
- session_key(str/bytes) : the key used in AES encryption

**Returns:**
- decrypted_data(bytes) : data extracted from the packed data

**Raises:**
- ValueError - Packed data or session key are not inputted.
- ValueError(Decryption) - The passpharse entered is incompatible with the encrypted data.

#### File-Encryption Layer

In File-Encryption Layer, the program implemented functions in Core Layer for encrypting file in directories.

The class `AES_on_File()` requires no initialization parameters, and is embed with two functions:

```
file_encrypt(target, output, passphrase)
```

Encrypt target file.

**Parameters:**
- target(str) : the path of the target file to be encrypted
- output(str) : the path of the output file
- passphrase(str) : the passpharse will be used the encryption

**Returns:**
- None

**Raises:**
- ValueError - Any parameters are not inputted.

```
file_decrypt(ciphertext, decrypted, passphrase)
```

Reverse of `file_encrypt()`.

**Parameters:**
- ciphertext(str) : the path of the encrypted file
- decrypted(str) : the path of the output file
- passphrase(str) : the passpharse used to encrypt the file

**Returns:**
- None

**Raises:**
- ValueError - Any parameters are not inputted.
- ValueError(Decryption) - The passpharse entered is incompatible with the encrypted file.

<a name="rsa"></a>

### RSA Encryption (Asymmetrical)

`rsa.py` is the Python file implemented with a mix of RSA asymmetrical encryption (Public-Private Key Pair Encryption) and [AES symmetrical encryption](#aes). Inside, an random-generated key is used for symmetric encryption. Then, the key will be encrypted with a public key serialized in .pem file. The encrypted key will be packed into the data encrypted in symmetric encryption. Encrypted data can only be decrypted with corresponding private key.

Other than that, the file is implemented with a generator of public-private key pair, users may choose further encrypt the private key using a custom passpharse.

It consists of three layers: Key-Generation Layer (class `RSA_GENERATE`), Core Layer (class `RSA_ENCRYPT` and `RSA_DECRYPT`), and File-Encryption Layer (class `RSA_on_File`).

#### Key-Generation Layer

In Key-Generation Layer, the program focuses on the generation of public and private keys pair for encryption using RSA algorithm. 

The class `RSA_GENERATE()` requires no initialization parameters, and is embed with only a single function:

```
serialize_key_pair(path, passphrase)
```

Generate and serialize a public and private keys pair in form of `.pem` files.

**Parameters:**
- path(str) : the path that store the keys pair files
- passphrase(str/bytes) : the key used to secure the private key

**Returns:**
- None

**Raises:**
- ValueError - The output path is not inputted.

#### Core Layer

In Core Layer, the program focuses on the only encryptions and decryptions using RSA algorithm. 

class `RSA_ENCRYPT()` have 3 initialization parameters, and class `RSA_DECRYPT()` have 4 initialization parameters.

For both classes:
- key_in_file(bool) : Whether the key is in a file.
- key_object(rsa.RSAPrivateKey) : The key in object form[^1]
- key_path(str) : The path of the key file[^1]

For `RSA_DECRYPT()`:
- passpharse(str) : The passpharse used to secure private key[^2]

There is only one function in each of the classes.

In `RSA_ENCRYPT()`:

```
encrypt_with_public_key(data)
```

Encrypt data using the public key initialized in `RSA_ENCRYPT()`.

**Parameters:**
- data(bytes) : the data to be encrypted

**Returns:**
- encrypted_data(bytes) : data encrypted using the public key

**Raises:**
- ValueError - The data is not inputted.
- ValueError(Encryption) - The data is bigger than 256 bytes (Constraint of RSA)

In `RSA_DECRYPT()`:

```
decrypt_with_private_key(encrypted_data)
```

Decrypt data using the private key initialized in `RSA_DECRYPT()`.

**Parameters:**
- encrypted_data(bytes) : the encrypted data to be decrypted

**Returns:**
- data(bytes) : data decrypted using the private key

**Raises:**
- ValueError - No or incompatible data is inputted.

#### File-Encryption Layer

In File-Encryption Layer, the program implemented functions in Core Layer for encrypting file in directories.

The class `RSA_on_File()` requires no initialization parameters, and is embed with two functions:

```
file_encrypt(target, key, output)
```

Encrypt target file.

**Parameters:**
- target(str) : the path of the target file to be encrypted
- key(str) : the path of the public key file used to encrypt
- output(str) : the path of the output file

**Returns:**
- None

**Raises:**
- ValueError - Any parameters are not inputted.

```
file_decrypt(ciphertext, key, decrypted)
```

Reverse of `file_encrypt()`.

**Parameters:**
- ciphertext(str) : the path of the encrypted file
- key(str) : the path of the private key file used to decrypt
- decrypted(str) : the path of the output file

**Returns:**
- None

**Raises:**
- ValueError - Any parameters are not inputted.
- ValueError(Decryption) - The passpharse entered is incompatible with the encrypted file.

## License

This program is released under the GNU General Public License v3.0.

Project Developed by Hugo Law.

For more details, see the [LICENSE](https://github.com/x011/SecretPixel?tab=GPL-3.0-1-ov-file) file or visit [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html).

[^1]: Only either one is required.
[^2]: Wrong passphrase can cause errors in decryption.
[^3]: Binary files `.bin` are recommend for the output file format as it requires not formatting of bytes.
[^4]: The original filename is optional. If no output filename is entered, original filename will be restored.
