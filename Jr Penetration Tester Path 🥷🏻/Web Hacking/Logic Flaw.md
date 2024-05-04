 A logic flaw is when the typical logical path of an application is either bypassed, circumvented or manipulated by a hacker.

```php
if(url.substr(0,6) === '/admin') {
   // Code to check user is an admin
} else {
	// view page
}
```

This PHP code is looking for an exact match on the string, include same letter casing, it presents a logic flaw because an **unauthenticated user** requesting **/adMin** will not have their privileges checked and have the page displayed to them.

**Using curl to make requests to the webserver:**

```bash
curl 'http://10.10.221.174/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'
```

We are setting the **Content-Type** to **application/x-www-form-urlencoded** which lets the web server know we are sending form data.

### Cookie Tampering

The contents of some cookies can be in plain text or not.

```bash
curl http://10.10.225.18/cookie-test
```

This will return a message of **not logged in**

While we can manipulate cookies in order to set some values to true...

```BASH
curl -H "Cookie: logged_in=true; admin=false"
```

Finally we will send a request setting both the logged_in and admin cookie to true:

```bash
curl -H "Cookie: logged_in=true; admin=true"
```

### Hashing

Sometimes cookie values can look like a long string of random characters, these are hashed values.

A hash is irreversible, however, the same output is produced every time

https://crackstation.net

### Encoding

