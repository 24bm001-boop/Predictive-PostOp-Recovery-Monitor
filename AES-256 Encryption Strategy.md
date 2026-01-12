**AES-256 Encryption Strategy** 

\# 🔐 AES-256 Encryption Implementation  
\#\# Overview  
To protect patient data during transmission, this project implements \*\*AES-256 (Advanced Encryption Standard)\*\* in CBC (Cipher Block Chaining) mode. This ensures that even if a malicious actor intercepts the WiFi packets, the physiological data remains unreadable.

\#\# 🛡️ Implementation Details

\#\#\# Why AES-256?  
\* \*\*Standard:\*\* It is the industry standard for medical data (HIPAA compatible).  
\* \*\*Performance:\*\* The ESP32 has a hardware crypto accelerator, allowing it to encrypt data without slowing down the sensor readings.

\#\#\# Data Flow  
1\.  \*\*Generation:\*\* Sensor reads \`HeartRate: 72\`.  
2\.  \*\*Encryption (Edge):\*\* ESP32 encrypts the payload using a pre-shared 256-bit key.  
    \* \*Input:\* \`{"hr": 72, "spo2": 98}\`  
    \* \*Output:\* \`a9f3c8... (ciphertext)\`  
3\.  \*\*Transmission:\*\* Ciphertext is sent via MQTT/HTTPs.  
4\.  \*\*Decryption (Cloud):\*\* The Backend API uses the same key to decrypt and store the data.

\#\# 🔑 Key Management  
\* \*\*Storage:\*\* The encryption key is \*\*not\*\* hardcoded in the main code. It is stored in the ESP32's non-volatile storage (NVS) or passed via a secure config header that is \`.gitignore\`'d.  
\* \*\*Rotation:\*\* Keys are versioned to allow future updates without breaking legacy data.

\#\# 💻 Code Snippet (ESP32)

\`\`\`cpp  
\#include "mbedtls/aes.h"  
void encryptPayload(char \* plainText, char \* outputBuffer) {  
    mbedtls\_aes\_context aes;  
    mbedtls\_aes\_init( \&aes );  
      
    // key must be 32 bytes (256 bits)  
    mbedtls\_aes\_setkey\_enc( \&aes, (const unsigned char\*) secretKey, 256 );  
        // Encrypt the block  
    mbedtls\_aes\_crypt\_ecb( \&aes, MBEDTLS\_AES\_ENCRYPT, (const unsigned char\*)plainText, (unsigned char\*)outputBuffer );  
      
    mbedtls\_aes\_free( \&aes );  
}  
