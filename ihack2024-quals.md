A CTF hosted on the CaptureX platform, interesting experience. Only solved low-hanging fruits this time, perhaps due to me rested for too long, and my pwndbg broke :/

## Table of Contents
- [Lock?](#lock)
- [Character Journey](#Character-Journey)
- [Simple Pimple Shop](#Simple-Pimple-Shop)
- [Employee Attendance](#Employee-Attendance)

## Lock?
We get some Windows Event Log (evtx) dump files and a hard drive image `storageM.img`.<br>
Upon inspection the hard drive image is bitlockered.<br>
Opening the `Windows PowerShell.evtx` we can see the password that is passed (`pa55iPOjLKbMN`) into the bitlocker encryption script `payload.ps1`.<br>
![image](https://github.com/user-attachments/assets/735c9fe0-cad7-4e1c-a450-e76da5c7e04a)<br>
Using the password I successfully decrypted the disk image and only `flag.txt` is inside.<br>
Flag: `ihack24{6f6450f1695e405557486a2be402dc27}`<br>
[*Back to top*](#table-of-contents)

## Character Journey
We get this page upon register.<br>
![image](https://github.com/user-attachments/assets/a771fd21-1b46-4754-b35c-b6436931f33c)<br>
Clicking on `My Account` we get this page:
![image](https://github.com/user-attachments/assets/c2c88850-8268-41e4-8b9a-5e8265058d7d)<br>
Enumerating the `userId` parameter we eventually reach a user where their name is the flag. Classic IDOR.<br>
![image](https://github.com/user-attachments/assets/6044ced0-be68-45bb-a28e-00f6a9a9e1dc)<br>
Flag: `ihack24{655b7b7ae4c62d726a568eff8914573e}`<br>
[*Back to top*](#table-of-contents)

## Simple Pimple Shop
We get a page with many cats.<br>
![image](https://github.com/user-attachments/assets/64b77892-d406-4dc8-9677-538ef524646e)<br>
Only 2 active endpoint here. The search endpoint `GET /?search=` (above) and comment `POST /products/$id/comments` (below).<br>
![image](https://github.com/user-attachments/assets/b8a368b0-572c-4355-94fc-d47ca9744fec)<br>
Thought it was XSS initially due to how easy it is to get XSS and the word `review` after comment submission. But there was no bots.<br>
![image](https://github.com/user-attachments/assets/1410ef1f-9ba2-495e-8f7c-25d098c00814)<br>
Submitting comment to an invalid product revealed partial source code and reminded me that a template engine (Slim) is used.
![image](https://github.com/user-attachments/assets/a9c92e02-4569-41dc-a54a-481b5073da10)<br>
Therefore, do [Slim SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#ruby---code-execution) in comment with `#{ %x|cat flag.txt| }` and get flag.<br>
Flag: `ihack24{c484c41c5b7ffd81178c19391e0544ee}`<br>
[*Back to top*](#table-of-contents)

## Employee Attendance
Upon login with the provided credentials `employee01:password` we get to this simple page to download attendance.<br>
![image](https://github.com/user-attachments/assets/449e260c-0ef7-41dc-9a5d-5e8670aefe77)<br>
Some interesting things in html source:<br>
```html
    <button onclick="downloadJSON()">Download JSON</button>
    <button onclick="logout()">Logout</button>
    <button href="/admin/flag.html" hidden>Flag</button>

    <script>
        async function fetchData(month) {
            // -- SNIP --
        }

        async function displayData() {
            // -- SNIP --
        }

        async function downloadJSON() {
            const monthSelect = document.getElementById('month-select');
            const month = monthSelect.value;
            if (month) {
                window.location.href = '/download?month=' + month;
            } else {
                alert("Please select a month to download the data.");
            }
        }

        function logout() {
            window.location.href = "/";
        }
    </script>
```
Directly accessing `/admin/flag.html` is unauthorised.<br>
![image](https://github.com/user-attachments/assets/ee97c825-0f91-4604-a0da-f82fd22ba137)<br>
Therefore we try to use `/download?month=` to download it.<br>
Tinkering with it a bit we can discover that `/download?month=../index.html` can download `index.html`, that means we can call `/download?month=../admin/flag.html` and get flag.<br>
Flag: `ihack24{8d1f757aa744f459ac7ef07ebe0e2651}`<br>
[*Back to top*](#table-of-contents)
