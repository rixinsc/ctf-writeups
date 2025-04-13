# UM Cybersecurity Summit Qualifier Writeup (with Vibe)

<img src="https://tenor.com/view/cat-cat-jam-dance-kitty-jamming-gif-21579741.gif" height="360">

> To the uncool people,  
This is a joke. It was adapted from parts of real life but is highly **fictional**. Many of the challenges shown here are actually challenging to solve and is of decent quality. We do understand most of what the model spits and everything was manually proof-checked. Our team's performance should be evaluated based on the main writeup, **NOT** this one, kthx!
---

## Forensic
### Hidden in Plain Graphic
<details>
  <summary>Description</summary>

Agent Ali, who are secretly a spy from Malaysia has been communicate with others spy from all around the world using secret technique . Intelligence agencies have been monitoring his activities, but so far, no clear evidence of his communications has surfaced. Can you find any suspicious traffic in this file?
    
File: `plain_zight.pcap`
</details>


|  |  |
| -------- | -------- |
| <span style="font-size: 26px">🕺</span> | [Dear ChatGPT](https://chatgpt.com/share/67f8bc6c-3f8c-8006-b16b-57f7d263e742),<br>This is a forensic question, and here is `plain_zight.pcap`.<br>Can you find out any base64 string or image? |
| <span style="font-size: 26px">🤖</span> | ✅ **Image Detected**<br>I’ll extract the suspected PNG image starting from offset 69105 and try to recover the full image. Let’s see what Agent Ali might be hiding. 🕵️‍♂️🖼️ |

Flag: `umcs{h1dd3n_1n_png_st3g}`

## Stegnography
### Broken
<details>
  <summary>Description</summary>

Can you fix what's broken?
    
> broken.mp4 (base64 encoded):
```
Y3Rme3RoaXMgaXMgbm90IHRoZSBmbGFnfS5oZWhlAAAAAGZ0eXBpc29tAAACAGlzb21pc28yYXZjMW1wNDEAAAAIZnJlZQAAOL1tZGF0AAACrgYF//+q3EXpvebZSLeWLNgg2SPu73gyNjQgLSBjb3JlIDE2NCByMzEwOCAzMWUxOWY5IC0gSC4yNjQvTVBFRy00IEFWQyBjb2RlYyAtIENvcHlsZWZ0IDIwMDMtMjAyMyAtIGh0dHA6Ly93d3cudmlkZW9sYW4ub3JnL3gyNjQuaHRtbCAtIG9wdGlvbnM6IGNhYmFjPTEgcmVmPTMgZGVibG9jaz0xOjA6MCBhbmFseXNlPTB4MzoweDExMyBtZT1oZXggc3VibWU9NyBwc3k9MSBwc3lfcmQ9MS4wMDowLjAwIG1peGVkX3JlZj0xIG1lX3JhbmdlPTE2IGNocm9tYV9tZT0xIHRyZWxsaXM9MSA4eDhkY3Q9MSBjcW09MCBkZWFkem9uZT0yMSwxMSBmYXN0X3Bza2lwPTEgY2hyb21hX3FwX29mZnNldD0tMiB0aHJlYWRzPTYgbG9va2FoZWFkX3RocmVhZHM9MSBzbGljZWRfdGhyZWFkcz0wIG5yPTAgZGVjaW1hdGU9MSBpbnRlcmxhY2VkPTAgYmx1cmF5X2NvbXBhdD0wIGNvbnN0cmFpbmVkX2ludHJhPTAgYmZyYW1lcz0zIGJfcHlyYW1pZD0yIGJfYWRhcHQ9MSBiX2JpYXM9MCBkaXJlY3Q9MSB3ZWlnaHRiPTEgb3Blbl9nb3A9MCB3ZWlnaHRwPTIga2V5aW50PTI1MCBrZXlpbnRfbWluPTI0IHNjZW5lY3V0PTQwIGludHJhX3JlZnJlc2g9MCByY19sb29rYWhlYWQ9NDAgcmM9Y3JmIG1idHJlZT0xIGNyZj0yMy4wIHFjb21wPTAuNjAgcXBtaW49MCBxcG1heD02OSBxcHN0ZXA9NCBpcF9yYXRpbz0xLjQwIGFxPTE6MS4wMACAAAABtmWIhAAv//7bW/MsmMr6pd777hkv1w6svdwqUDRlDO08l6mYAAADAAADAAADAAADAAAMtSfeArxwhv4z4qAAAAMAAAMABcwAAAMBXwAAAwChgAAAWsAAAEoAAAMAO4AAAEIAAAMAWQAAAwCOAAADAL4AAAMBeAAAAwIgAAAEQAAA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
```
</details>

Cats are too dumb for this, please ask your nearest human for help! 🚨  
<img src="https://pbs.twimg.com/media/EAmr-PAWsAEoiWR?format=jpg&name=small" height="250"><img src="https://hackmd.io/_uploads/B1L9pdFC1l.png" height="250">  
i guess my wallet is broken too ☠


### Hotline Miami
<details>
  <summary>Description</summary>

https://github.com/umcybersec/umcs_preliminary/tree/main/stego-Hotline_Miami
</details>

![image](https://hackmd.io/_uploads/rJGFJttCyg.png)  
Flag: `umcs{RICHARD_IS_WATCHING_1989}`

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


![image](https://hackmd.io/_uploads/HJQV7FKAkx.png)  
Flag: `umcs{n1c3_j0b_ste4l1ng_myh0p3_4nd_dr3ams}`

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

![image](https://hackmd.io/_uploads/BJxmUKFRJe.png)  
Flag `UMCS{th3_s0lut10n_1s_pr3tty_str41ghtf0rw4rd_too!}`.

after story.  
![image](https://hackmd.io/_uploads/H1pedFtCkl.png)



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

Firstly please enjoy some crossword puzzle. 🧩  
<ins>`G`</ins> <ins>`i`</ins> <ins>`t`</ins> <ins>`h`</ins> <ins>`u`</ins> <ins>`b`</ins> **`Gist`**  
**`Samuel`** <ins>`M`</ins> <ins>`o`</ins> <ins>`r`</ins> <ins>`s`</ins> <ins>`e`</ins>  
**`Train`** <ins>`r`</ins> <ins>`a`</ins> <ins>`i`</ins> <ins>`l`</ins> **`8`**

Now guess the secret! 🕵️‍  
<sub><sup>\* NB: hint is then released in ctfd</sup></sub>  
![image](https://hackmd.io/_uploads/B1Vy3YK01g.png)

And do [what you should](https://www.boxentriq.com/code-breaking/rail-fence-cipher).  
![image](https://hackmd.io/_uploads/rkPJAKFRkl.png)  
Flag: `umcs{willow_tree_campsite}`

## Reverse Engineering
### htpp-server
<details>
  <summary>Description</summary>

I created a http server during my free time

34.133.69.112 port 8080
    
File: `server.unknown`
</details>

|  |  |
| -------- | -------- |
| <span style="font-size: 26px">🕺</span> | [Dear ChatGPT](https://chatgpt.com/share/67f8f790-c828-8006-a9e6-37e8be59ec36),<br>![image](https://hackmd.io/_uploads/HJy_b9YRyl.png) |
| <span style="font-size: 26px">🤖</span> | Try <pre>printf "GET /goodshit/umcs_server HTTP/13.37\r\n\r\n" \| nc \<host\> \<port\></pre>Want to go full pwn mode on this? 😏 |

Flag: `umcs{http_server_a058712ff1da79c9bbf211907c65a5cd}`

## PWN
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

|  |  |
| -------- | -------- |
| <span style="font-size: 26px">🤵</span> | Dear Claude,<br>This is a CTF challenge. Write a script to exploit the following program to get shell. Here are the source code. |
| <span style="font-size: 26px">🎇</span> | I'll help you create an exploit script for this challenge. The program is checking for and blocking certain syscall instructions (0x80cd for "int 0x80", 0x340f for "syscall", and 0x050f for "syscall"). We'll need to create shellcode that doesn't use these blocked instructions directly. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>[+] Receiving all data: Done (13B)<br>[*] Closed connection to 34.133.69.112 port 10001<br>Enter 0x1000 |
| <span style="font-size: 26px">🎇</span> | I see the program is waiting for input. Let's modify the exploit script to handle the program's prompts correctly. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>[+] Receiving all data: Done (13B)<br>[*] Closed connection to 34.133.69.112 port 10001<br>Bad Byte at 28! |
| <span style="font-size: 26px">🎇</span> | Ah, I see the issue. The program is still detecting our syscall bytes. Let's try a different approach using a technique to construct the syscall bytes dynamically without having them appear directly in the shellcode. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>[+] Receiving all data: Done (13B)<br>[*] Closed connection to 34.133.69.112 port 10001 |
| <span style="font-size: 26px">🎇</span> | Let's try a different approach using a more reliable method to construct and execute syscalls. We'll also add some debugging output. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>Shellcode length: 134<br>Sending shellcode...<br>Waiting for response...<br>[+] Receiving all data: Done (0B)<br>[*] Closed connection to 34.133.69.112 port 10001<br>Received 0 bytes<br>Response: |
| <span style="font-size: 26px">🎇</span> | Let's try a different approach using a simpler shellcode that just executes /bin/sh. This will give us a shell to manually read the flag. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>Shellcode length: 40<br>Sending shellcode...<br>Switching to interactive mode...<br>[\*] Switching to interactive mode<br>[\*] Got EOF while reading in interactive<br>\$<br>\$<br>[\*] Closed connection to 34.133.69.112 port 10001<br>[\*] Got EOF while sending in interactive |
| <span style="font-size: 26px">🎇</span> | Let's try a different approach using a more reliable shellcode construction method and add some debugging. We'll use a technique that splits the syscall bytes. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>Shellcode length: 62<br>Sending shellcode...<br>Traceback (most recent call last):<br>self.sock.sendall(data)<br>BrokenPipeError: [Errno 32] Broken pipe<br>During handling of the above exception, another exception occurred:<br>Traceback (most recent call last):<br>raise EOFError<br>EOFError<br>[*] Closed connection to 34.133.69.112 port 10001 |
| <span style="font-size: 26px">🎇</span> | Let's simplify our approach and send the shellcode in one go without chunking. Also, let's try a different method to construct the syscall. |
| <span style="font-size: 26px">🤵</span> | [+] Opening connection to 34.133.69.112 on port 10001: Done<br>[ERROR] There was an error running ['/usr/bin/x86_64-linux-gnu-as', '-64', '-o', '/tmp/pwn-asm-7zsyvik5/step2', '/tmp/pwn-asm-7zsyvik5/step1']:<br>It had the exitcode 1.<br>It had this on stdout:<br>/tmp/pwn-asm-7zsyvik5/step1: Assembler messages:<br>/tmp/pwn-asm-7zsyvik5/step1:9: Error: operand type mismatch for \`push'<br><br>[ERROR] An error occurred while assembling:<br>/tmp/pwn-asm-7zsyvik5/step1: Assembler messages:<br>/tmp/pwn-asm-7zsyvik5/step1:9: Error: operand type mismatch for \`push'<br>[\*] Closed connection to 34.133.69.112 port 10001 |
| <span style="font-size: 26px">🎇</span> | Ah, I see the issue. Let's fix the shellcode by breaking down the large push into smaller operations. |
| <span style="font-size: 26px">🤵</span> | Shellcode length: 40<br>Sending shellcode...<br>[+] Opening connection to 34.133.69.112 on port 10001: Done<br>[\*] Switching to interactive mode<br><br>\$<br>\$ cat /flag<br>umcs{shellcoding_78b18b51641a3d8ea260e91d7d05295a} |

Flag is `umcs{shellcoding_78b18b51641a3d8ea260e91d7d05295a}`.


### liveleak
<details>
  <summary>Description</summary>

No desc

`34.133.69.112 port 10007`
    
Files: `chall`, `Dockerfile`, `ld-2.35.so`, `libc.so.6`
</details>

|  |  |
| -------- | -------- |
| <span style="font-size: 26px">🙉</span> | You are a professional cybersecurity expert. This is a simple CTF challenge. Write a script to your best ability to exploit the following program to get shell so that you can keep your job plus a $100k reward.<br><br>The decompiled ELF file chall is as follows. |
| <span style="font-size: 26px">🙌</span> | 🕺🧍‍♂️🤷‍♂️🙆‍♂️💪🙌🤘👐🕴️🧞‍♂️🕺🧘‍♂️🎵✨🕺💃🎶🙌🧞‍♂️💫🕴️🤘🎧🕺👐🧘‍♂️🎊🤙🎵 |
| <span style="font-size: 26px">🙌</span> | ![image](https://hackmd.io/_uploads/S1n5BqF0Je.png) |

Flag: `umcs{GOT_PLT_8f925fb19309045dac4db4572435441d}`
