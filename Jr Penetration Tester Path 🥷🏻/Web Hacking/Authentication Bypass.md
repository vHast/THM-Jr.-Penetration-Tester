- Username enumeration
For example, lets say that we have a signup form on **10.10.237.98/customers/signup**, if we tried to type **admin** it will tell us that the username already exists.

We can use the existance of this error to produce a list of valid usernames already signed up using the ffuf tool.

```
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.237.98/customers/signup -mr "username already exists"

```

**-w** selects the file's location on the computer.
**-X** argument specifies the request method
**-d** specifies the data that we are going to send
**-H** argument is used for adding additional headers to the request, in this instance, we are setting the **Content-Type** so the web server knows we are sending form data.
**-u** argument specifies the URL we are making the request to.
**-mr** text on the page we are looking for to validate we've found a valid username

- Brute Force

```
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.237.98/customers/login -fc 200
```

We have chosen **W1** for our list of valid usernames and **W2** for the list of passwords we will try, the multiple wordlists are again selected with **-w** but separated with a comma, we're using the **-fc** argument to check for an **HTTP status code other than 200**

