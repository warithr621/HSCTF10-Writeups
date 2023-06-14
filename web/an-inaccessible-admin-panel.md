# web/an-inaccessible-admin-panel

## Problem: 

The Joker is on the loose again in Gotham City! Police have found a web application where the Joker had allegedly tampered with. This mysterious web application has login page, but it has been behaving abnormally lately. Some time ago, an admin panel was created, but unfortunately, the password was lost to time. Unless you can find it...

Can you prove that the Joker had tampered with the website?

Default login info: `Username: default` `Password: password123`

Link to login page: [https://login-web-challenge.hsctf.com/](https://login-web-challenge.hsctf.com/)

## Solution: 

Darn, time to stop the Joker!! We're given some login info, so let's just put that in and see where it takes us.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/56e5ae58-4a87-4acd-ba11-7c3a24b3eda4)

?????? Well that's weird. Let's open up Chrome Devtools and see if we can find anything. After browsing through, we find source code for `login.js` that most likely controls the login verification.

```JavaScript
        var username = document.getElementById("username").value;
        var password = document.getElementById("password").value;
    
        function fii(num){
            return num / 2 + fee(num);
        }
        function fee(num){
            return foo(num * 5, square(num));
        }
        function foo(x, y){
            return x*x + y*y + 2*x*y;
        }
        function square(num){
            return num * num;
        }

        var key = [32421672.5, 160022555, 197009354, 184036413, 165791431.5, 110250050, 203747134.5, 106007665.5, 114618486.5, 1401872, 20702532.5, 1401872, 37896374, 133402552.5, 197009354, 197009354, 148937670, 114618486.5, 1401872, 20702532.5, 160022555, 97891284.5, 184036413, 106007665.5, 128504948, 232440576.5, 4648358, 1401872, 58522542.5, 171714872, 190440057.5, 114618486.5, 197009354, 1401872, 55890618, 128504948, 114618486.5, 1401872, 26071270.5, 190440057.5, 197009354, 97891284.5, 101888885, 148937670, 133402552.5, 190440057.5, 128504948, 114618486.5, 110250050, 1401872, 44036535.5, 184036413, 110250050, 114618486.5, 184036413, 4648358, 1401872, 20702532.5, 160022555, 110250050, 1401872, 26071270.5, 210656255, 114618486.5, 184036413, 232440576.5, 197009354, 128504948, 133402552.5, 160022555, 123743427.5, 1401872, 21958629, 114618486.5, 106007665.5, 165791431.5, 154405530.5, 114618486.5, 190440057.5, 1401872, 23271009.5, 128504948, 97891284.5, 165791431.5, 190440057.5, 1572532.5, 1572532.5];

        function validatePassword(password){
            var encryption = password.split('').map(function(char) {
                return char.charCodeAt(0);
            });
            var checker = [];
            for (var i = 0; i < encryption.length; i++) {
                var a = encryption[i];
                var b = fii(a);
                checker.push(b);
            }
            console.log(checker);
            
            if (key.length !== checker.length) {
                return false;
            }
            
            for (var i = 0; i < key.length; i++) {
                if (key[i] !== checker[i]) {
                    return false;
                }
            }
            return true;
        }


        if (username === "Admin" && validatePassword(password)) {
            alert("Login successful. Redirecting to admin panel...");
            window.location.href = "admin_panel.html";
        }
        else if (username === "default" && password === "password123") {
            var websiteNames = ["Google", "YouTube", "Minecraft", "Discord", "Twitter"];
            var websiteURLs = ["https://www.google.com", "https://www.youtube.com", "https://www.minecraft.net", "https://www.discord.com", "https://www.twitter.com"];
            var randomNum = Math.floor(Math.random() * websiteNames.length);
            alert("Login successful. Redirecting to " + websiteNames[randomNum] + "...");
            window.location.href = websiteURLs[randomNum];
        } else {
            alert("Invalid credentials. Please try again.");
        }
```

Let's analyze this real quick
- `validatePassword()` converts the inputted password into an array of each character's ASCII value
- For each of these ASCII values `a`, it calculates `fii(a)` and checks if it matches with the corresponding element in `checker`
- If all of these match and the username is `Admin`, it opens up the admin panel
- If not, it picks a random website to go to (Google, YouTube, Minecraft, Discord, or Twitter) and redirects there (which explains why we went to Discord)

Analyzing the code further, we can figure out exactly what `fii(a)` calculates with the help of `fee()` `foo()` and `square()`
```JavaScript
// Alternative declaration for fii() that gives the same result
function fii(num) {
    // fii(x) = x / 2 + fee(x)
    // fee(x) = foo(5x, x^2)
    // foo(a, b) = a^2 + b^2 + 2ab = (a + b)^2
    // foo(5x, x^2) = (x^2 + 5x)^2 = [x(x+5)]^2
    // fii(x) = x / 2 + [x(x+5)]^2
    return num / 2 + (num * (num + 5)) * (num * (num + 5));
    // how annoying of an equation
}
```

This seems really painful to figure out what `num` should be based on the value of `fii(num)`. To make this easier to do, let's just precompute all the pairs of `(num, fii(num))`, then go through each element in `key` and figure out what it should match to.

```Python
def fii(x):
    return x / 2 + (x * (x+5)) * (x * (x+5))

key = [32421672.5, 160022555, 197009354, 184036413, 165791431.5, 110250050, 203747134.5, 106007665.5, 114618486.5, 1401872, 20702532.5, 1401872, 37896374, 133402552.5, 197009354, 197009354, 148937670, 114618486.5, 1401872, 20702532.5, 160022555, 97891284.5, 184036413, 106007665.5, 128504948, 232440576.5, 4648358, 1401872, 58522542.5, 171714872, 190440057.5, 114618486.5, 197009354, 1401872, 55890618, 128504948, 114618486.5, 1401872, 26071270.5, 190440057.5, 197009354, 97891284.5, 101888885, 148937670, 133402552.5, 190440057.5, 128504948, 114618486.5, 110250050, 1401872, 44036535.5, 184036413, 110250050, 114618486.5, 184036413, 4648358, 1401872, 20702532.5, 160022555, 110250050, 1401872, 26071270.5, 210656255, 114618486.5, 184036413, 232440576.5, 197009354, 128504948, 133402552.5, 160022555, 123743427.5, 1401872, 21958629, 114618486.5, 106007665.5, 165791431.5, 154405530.5, 114618486.5, 190440057.5, 1401872, 23271009.5, 128504948, 97891284.5, 165791431.5, 190440057.5, 1572532.5, 1572532.5]

a = dict() # store all the pairs

for i in range(128):
    a[fii(i)] = i
    # if `key` stores values for fii(i), we can store these pairs in the dictionary based on fii(i)
    # this way when we call a[x] for each `x` in `key`, we get the corresponding ASCII value and convert it into a char
    
pwd = ""
for x in key:
    c = a[x]
    pwd += chr(c)
    
print(pwd)
```
```
Introduce A Little Anarchy, Upset The Established Order, And Everything Becomes Chaos!!
```

Plugging this password (a quote from the Joker himself) in with the right username (`Admin`), we get to `admin_panel.html` and get our flag.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/c1de3f14-b5b7-438d-a9df-4dbce6101782)

Huh, what a long flag.

## Flag:

`flag{Admin, Introduce A Little Anarchy, Upset The Established Order, And Everything Becomes Chaos!!}`