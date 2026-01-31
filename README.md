<img width="150" height="150" alt="1000454560" src="https://github.com/user-attachments/assets/4464ecdb-613e-45e3-9b9e-317261535aab" />

# mc-tool
Minecraft Manifest Json Editor / UUID Generator


## Usage
- Offline use: Download [Source.zip Here](https://github.com/xetsue/mc-tool/releases/) , unzip, open `index.html` in any browser.

- [Online Use: Website](https://xetsue.github.io/mc-tool/)

v1.1 Changelong
> Added support to load any Manifest.json files.

> Added option to export edited manifest.json to fully structured folders of a complete pack as `.zip" files.

> Updated UUID key generation using deterministic algorithm. Refer to [UUID Generation Documentation.](https://github.com/xetsue/mc-tool/blob/main/README.md#uuid-generations)

> As opposed to the older version that uses mathematical entropy and randomized seed based generation of seperate data. This method caused complications and it was impossible to retrieve keys by using the generated UUID. With the new algorithm, a single UUID can be decrypted back to obtain the key along the pair of said UUID. 


## Previews
>vx-17, vy-89
> <img width="540" height="374" alt="1000454611" src="https://github.com/user-attachments/assets/ab7a8cc0-da81-4839-b1ee-75f14097d780" />
> <img width="540" height="906" alt="1000454605" src="https://github.com/user-attachments/assets/b4493f84-5e02-458d-85a4-2eda62b48401" />
> <img width="540" height="1033" alt="1000454607" src="https://github.com/user-attachments/assets/276783a5-f2bf-4e1b-a031-e65910ab5e55" />
> <img width="540" height="519" alt="1000454609" src="https://github.com/user-attachments/assets/78dd72c9-5006-483c-9d69-c847a78b8800" />

---

# UUID Generations
1. Key to Version 4 UUID Generation (Using Forward Path)
The process involved uses deterministic algorithm, meaning the same key will always produces the same UUID in an order of 3 stages and relying on modular arithmetic and character-to-byte mapping.
--- 
 I. Buffer Expansion (Deterministic Padding)
When a submitted key is shorter <16 characters, the system expands it using a linear congruential logic to ensure enough entropy for a 32-character hex string.
- **Formula:** $NewChar = ((OriginalCharCode \times 17) \pmod{94}) + 33$
- **Logic:** Multiplying by a prime number (17) shuffles the bits so that similar keys (e.g., "Key1" and "Key2") produce significantly different buffers.

 II. Offset Transformation
The system uses unique **Offsets** to ensure the same key produces different UUIDs for different components (Header vs. Module).
- **Header Offset:** VALUE_X
- **Module Offset:** VALUE_Y
- **Transformation:** $ByteValue = (CharCode + Offset) \pmod{256}$



 III. UUID Version 4 Compliance
The resulting 32-character hex string is forced into the standard UUID structure.
- **Format:** `8-4-4-4-12` Used as a reference of structural basis. 
- **Version Bits:** The 13th character is hardcoded to `4`.
- **Variant Bits:** The 17th character is hardcoded to `a`.
- **Result:** `xxxxxxxx-xxxx-4xxx-axxx-xxxxxxxxxxxx` 

---
> Forward path mentioned early on is a Simplified explanation where one value is brought forward which made it possible to reverse the same math to backtrace the original key using the same exact conditions.

### Hex to Byte Conversion
The system strips the hyphens from the UUID and converts the first 24 hex characters back into a 12 decimal bytes.

### Inverse Offset Calculation
To recover the original character code, the system subtracts the known offset used during generation as following:
- **Formula:** $RecoveredCode = (ByteValue - Offset)$
- **Handling Underflow:** If the result falls below the printable ASCII range (32), the system wraps it back around:
  - $If (RecoveredCode < 32) \implies RecoveredCode + 256$



---
### **Limitations & Extra Notes**

> 1.  **Truncation:** The UUID format only has room for 128 bits of data. Consequently, only the first 12 characters of any key can be perfectly recovered which is a specific requirement for this use case only.

> 2.  **Hardcoded Constants:** The "Security" of the key depends entirely on the secrecy of the offsets (VALUE_X and VALUE_Y). If the offsets are known, the "encryption" is functionally equivalent to a Caesar Cipher. The match of this sequence was unintentional but was interesting enough to be included nonetheless. 

> 3.  **Forced Bits:** Since characters 13 and 17 in the UUID are hardcoded to `4` and `a` for compliance, the data originally mapped to those positions is lost and cannot be recovered during the backtrace.

> 4. The UUID Key usage allows alphanumeric characters along with printable ascii (Typically used for password combinations) up to 12 characters. Although seems like this is simplified, the possible simple combination of printable ascii combination (Small/Capital alphabets, numbers, symbols, spaces, etc.) goes up to `546,108,599,233,516,079,517,120` or `546 Septillion` or `5.4x10^(23)`. Human brain can barely comprehend this number. To put into perspective, brute-force tool tests around 100 billion combinations per second, even so it would still take over 170,000 years to complete this 1-12 combination range.  Safe to say users can take their time to come up with a unique combination ranging from 1 to 12 characters with no problem.

> The math is done with (Number Of Character Variations) Powered by (Length of this combination), varies from 1 to 12 as the length and summed up as total value. 
