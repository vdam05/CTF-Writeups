# OverTheWire Web
Connect to overthewire via the URL: http://natasX.natas.labs.overthewire.org, where X is the level.

We have the username. Find the password to the next level within the website.

Passwords will be clear in where they are that they are passwords.

`*` for levels I needed a little bit of help on.

# Level

Level0: Use `Inspect` mode by right-clicking, the password is a comment in the DOM. 

Level1: Doesn't allow right-clicking, but we can just `Ctrl + U` (get the HTML file) or `Ctrl + Shift + C` (get into DevTools) to do the same thing.

Level2: Did not need to do forensics on the image. It is simply that there are other files in the /files directory of the website. There is a user.txt

Level3*: This is in `robots.txt`. So robots.txt show us what directories the server does not allow bots
to roam by. We go to that single directory for user.txt

Level4*: Okay, here it checks to see if the person who gets to this website comes from `https://natas5.natas.labs.overthewire.org`. First, I look up the HTTP headers that check for this kind of thing. So, using Burp Suite here rather than the ZAP proxy (either is fine) to intercept traffics, I add a new header called the Referer which has the value of that URL to the packet. And then send it.

Level5: Similar method. The packet has a `Cookie` header with key-value pair loggedin = 0. Using Burp Suite, we can change the cookie value to be 1 which is truthy and gets in. 

Level6: Going into this level, we see that there is an input field, asking for a secret. We also see a link to see the source code. Looking at
the PHP source code, it has the line `include "includes/secret.inc";`. So I try to go to that file in the directory and it gives me the secret. Now to just put in the secret password and we get in. 

Level7: Here, we see two links to Home and About. Looking at the HTML, we see that the Home link is in `<a href="index.php?page=home">`. It is similar for About. This tells me that the PHP file most likely takes in a paremeter and sends the user to that directory. We also know that the password file for this level is in /etc/natas_webpass/natas8. Therefore, in the URL, we can edit the relative URL to be `index.php?page=/etc/natas_webpass/natas8` to get that resource.

Level8: This is another input field and source code. We see in the PHP, that it has an `$encodedSecret` and it applies a function called `encodeSecret()` onto the value that we give as `secret` in our packet and then checks whether the result is equal to encodedSecret. Well, for this one, I find online a PHP code runner. And then I copy the code into there and then reverse the function with `base64_decode(strrev(hex2bin($encodedSecret)))` in that order and finally print it out with printf(). This gives us our secret.

Level9: This is also an input field and source code situation. Looking at the source code, it is supposed to get the value that we input and puts that in a command `grep -i $value dictionary.txt` where $value is our input. However, there is no check except only checking if $value is blank. So this is ripe for a Command Injection. So we could input `bonus dictionary.txt; cat /etc/natas_webpass/natas10;`. This would do both of these commands and we know that the password is in /etc/natas_webpass, so the server would output the result of both of these commands.

Level10: Okay, this one is like Level9 but it has a few checks for illegal characters. Tried URL encodings, definitely won't work. So we could try other characters that chain commands in some way. However, the command I use is `"" /etc/natas_webpass/natas11 #`. Here, we are using the command "grep" to find any string on the file at /etc/natas_webpass/natas11. Like in Bash scripting, the "#" is to comment out code. Without this, it would also print the entire dictionary.txt in our grep command, which is very laggy. So we comment out any other files after this.

Level11: