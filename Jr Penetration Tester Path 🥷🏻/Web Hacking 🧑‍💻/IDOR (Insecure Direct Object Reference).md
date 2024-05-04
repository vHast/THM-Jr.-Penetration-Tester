Is a type of access control vulnerability, it can occur when a **web server receives user-supplied input to retrieve objects.**

Too much trust has been placed on the input data, and it is not validated on the server-side

### IDOR - Encoded IDs

When passing data from page to apge either by post data, query strings, or cookies, web developers will often first take the **raw data and encode it**.

Encoding ensures that the receiving **web server** will be able to understand the contents.

Encoding changes **binary data into an ASCII string**, the most common encoding nowadays is **Base64**

[https://www.base64decode.org/](https://www.base64decode.org/)

### IDOR - Hashed IDs

For example, the Id number **123**, would become **202cb962ac59075b964b07152d234b70** if MD5 hashing were in use.

### IDOR - Unpredictable IDs

An excellent method of IDOR detection is to create **two accounts** and swap the Id numbers between them, if you can view the other users content using their ID number while still being logged in with a different account, **you've found a valid IDOR vulnerability.**