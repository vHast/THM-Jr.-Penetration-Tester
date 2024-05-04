Parameters are query parameter strings attached to the URL that could be used to **retrieve data, or perform actions** based on user input.

![[Pasted image 20240502214850.png]]

![[Pasted image 20240502214951.png]]

For example, if a user wants to access and display their CV within the web application, the requestt may look as http://webapp.thm/get.php?file=userCV.pdf where the **file** is the parameter and the **userCV.pdf**, is the required file to access.

### Path Traversal

Also known as **Directory Traversal**, is a web security vulnerability that allows an attacker to read operating system resources, such as local files on the server running an application.

The attacker exploits this vulnerability by **manipulating and abusing the web application's URL** to locate and access files or directories stored outside the application's root directory.

Path traversal vulnerabilities occur when the user's input is passed to a function such as **file_get_contents** in PHP, it's important to note that the function is not the main contributor to the vulnerability.

![[Pasted image 20240502215959.png]]

### Dot-Dot-Slash attack

Take advantage of moving the directory one step up using the double dots **../** if an attacker finds the entry point which should be **get.php?file=**, then the attacker might send something as

```php
http://webapp.thm/get.php?file=../../../../etc/passwd
```


![[Pasted image 20240502221254.png]]

### Dot Attack on Windows

The attacker this time needs to provide Windows paths, if the attacker wants to read the **boot.ini** file located in **c:\boot.ini**, then the attacker can try the following depending on the target OS

```PHP
http://webapp.thm/get.php?file=../../../../../boot.ini
http://webapp.thm/get.php?file=../../../../../windows/win.ini
```

### Common locations

**/etc/issue** - Contains a message or system identification
**/etc/profile** - Controls system-wide default variables, such as Export
**/proc/version** - Specifies the version of the Linux Kernel
**/etc/passwd** - Has all registered user and password that has access to the system
**/etc/shadow** - System user's password
**/root/.bash_history** 
**/var/log/dmessage** - Global system messages
**/var/mail/root** - All emails for root user
**/root/.ssh/id_rsa** - Private SSH keys
**/var/log/apache2/access.log** - Accessed requests for Apache webserver
**C:\boot.ini** - Boot options for computer with BIOS firmware

### Local File Inclusion (FLI)

With PHP, using functions such as **include, require, include_once** and **require_once** often contribute to vulnerable web applications.

Suppose the web application provideds two languages, can be **EN** and **AR**

```PHP
<?PHP
	include(*$_GET["lang"]);
?>
```

The call can work sending the following HTTP request

```
http://webapp.thm/index.php?lang=AR.php
```

Theorically, we can access and display any readable file on the server from the code above if there isn't any input validation.

```
http://webapp.thm/get.php?file=/etc/passwd
```

```PHP
<?PHP
	include("languages/". $_GET['lang']);
?>
```

In this code we used the **include** function to call PHPpages in the **languages** directory only via **lang** parameters.

If there is no input validation, the attacker can manipulate the URL by replacing the **lang** input with other **OS-sensitive files** such as **/etc/passwd**.

Again, the payload looks similar to the **path traversal**

### Black Box - LFI

Depending on the wrong inputs that you send through the HTTP requests, we can get the following errors

```
Warning: include(languages/THM.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12 
```

Even if it is an error, it discloses important information, such as the **include** function and the **directory path**.

To get out of the current folder, try the following.

```
http://webapp.thm/index.php?lang=../../../../etc/passwd
```

Now we get something like this:

```
Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```

We could move out of the PHP directory, but still, the **include** function reads the input with .php at the end.

We can use a **null byte** which is %00

By adding a null byte at the end of the payload, we tell the **include** function to ignore anything after the null byte which may look like

```
include("languages/../../../../../etc/passwd%00").".php");
```

Which is the equivalent of

```
include("languages/../../../../../etc/passwd");
```

### Advanced filtering

Considering that we're throwing

```
http://webapp.thm/indedx.php?lang=../../../../etc/passwd
```

We get this

```
Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15
```

### Remote File Inclusion (RFI Attack)

Technique to include remote files into a vulnerable application, it occurs when improperly sanitizing user input, allowing an attacker to inject an **external URL into include function**.

**allow_url_fopen** must be **on**

![[Pasted image 20240503001700.png]]

### Remediation

1. Keep systems updated
2. Turn off PHP errors to avoid leaking paths
3. A Web Application Firewall is a good option to help mitigate web application attacks
4. Disable PHP features such as **allow_url_fopen** on and **allow_url_include**
5. Carefully analyze the web application and allow only protocols and PHP wrappers in need
6. Never trust user input.
7. Implement whitelisting for file names and locations as well as blacklisting


https://www.acunetix.com/vulnerabilities/web/local-file-inclusion/