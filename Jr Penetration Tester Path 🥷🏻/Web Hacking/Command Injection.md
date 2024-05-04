
Is the abuse of an application's behaviour to execute commands on the operating system, using the same privileges that the application on a device is running with.

Also known as **Remote Code Execution (RCE)** because of the ability to remotely execute codee within an application.

Being able to abuse an application to perform the command **whoami** to a list what user account the application is running will be an example of command injection.

```PHP
<?php

$songs = "/var/www/html/songs"

if (isset $ GET["title"])) {
	$title = $_GET["title"];
	$command = "grep $title /var/www/html/songtitle.txt";
	$search = exec($command);
	if ($search == "") {
		$return = "<p>The requested song</p><p> $title does </p><b>not</b><p> exist!</p>";
	} else {
		$return = "<p>The requested song</p><p> $title does </p><b>exist!</b>";
		}
	echo $return
}

?>
```

This sort of information would typically be stored in a database, however, this is just an example of where an application takes **input from a user to interact with the application's operating system**

An attacker could abuse this application by injecting their own commands for the application to execute, rather than using **grep** to search for an entry in **songtitle.txt**, they could ask the application to read sensitive data.

```python
import subprocess

from flask import Flask
app = Flask(_name_)

def execute_command(shell):
	return subprocess.Popen(shell, shell=True, stdout=subprocess.PIPE).stdout.read()

@app.route('/<shell>')
def command_server(shell):
	return execute_command(shell)
```

1. The "flask" package is used to set up a web server
2. A function that uses the "subprocess" package to execute a command on the device
3. We use a route in the webserver that will execute whatever is provided. For example, to execute `whoami`, we'd need to visit http://flaskapp.thm/whoami

### Exploiting Command Injection

It can be detected in one of two ways:

- **Blind command injection**
Where there is no direct output from the application when testing payloads, you will have to investigate the behaviours of the application to determine whether or not your payload was successful.

- **Verbose**
This type of injection is where there is direct feedback from the application once you have tested a payload. (`whoami`)

**Detecting Blind Command Injection**
We will need to use payloads that will cause some **time delay**, for example `ping` and `sleep` commands are significant payloads to test with.

Forcing some output can also be used, this can be done by using redirection operators such as `>`, for example, we can tell the web application to execute commands such as `whoami` and redirect that to a file, or use `cat`.

The `curl` command is a **great way to test for command injection**, this is because you are able to use `curl` to deliver **data to and from an application in your payload**

```bash
curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami
```

**Detecting Verbose Command Injection**

```
whoami  // See what user the application is running under
ls // List contents of the current directory
ping // This command will invoke the application to hang
sleep // Another useful payload in testing an application for blind command
nc // used to spawn a reverse shell onto the vulnerable application
```

```
timeout // Invoke the application to hang
```

### Remediating Command Injection

```PHP
<input type="text" id="ping" name="ping" pattern="[0-9]+"></input>

<?php

echo passthru("/bin/ping -c 4 "$_GET["ping"].);

?>
```

This application will only accept and procecss numbers that are inputted into the form, this means that commands such as `whoami` will not be processed.

**Input sanitisation**

For example, an input field taht only accepts nuemrical data or removes any special characters such as `>`, `&` and `/`

```php
<?php

if (!filter_input(INPUT_GET, "number", FILTER_VALIDATE_NUMBER)) {

}
```

In here, the `filter_input` is used to check whether or not any data submitted via an input form is a number or not, if it is not a number, it must be invalid input.

**Bypassing Filters**

We can abuse the logic behind an application to bypass these filters, for example, an application may **strip out quotation marks**, we can instead use the hexadecimal value of this to achieve the same result

```php
$payload = "\x2f\x65\x74\x64\x2f\x70\x61\x73\x73\x77\x64"
```

Github Cheatsheet : https://github.com/payloadbox/command-injection-payload-list