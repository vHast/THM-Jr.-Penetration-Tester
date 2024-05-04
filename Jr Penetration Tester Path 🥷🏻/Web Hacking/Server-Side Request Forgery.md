Vulnerability t hat allows a malicious user to cause the webserver to make an additional or edited **HTTP request** to the resource of the attacker's choosing.

### Finding an SSRF

When a full URL is used in a parameter in the address bar:

![[Pasted image 20240503184139.png]]

A hidden field in a form:

![[Pasted image 20240503184210.png]]

A partial URL such ash justt the hostname

![[Pasted image 20240503184236.png]]

Only the path of the URL

![[Pasted image 20240503184257.png]]
### Common SSRF Defenses

**Deny List**: 
Where all requests are accepted apart from resources specified in a list or matching a particular pattern, a web application may employ a deny list to protect **sensitive endpoints**

**Allow List:**
Where all requests get denied unless they appear on a list or match a particular pattern, such as a rule that an URL used in a parameter must begin with **https://website.thm**, an attacker could quickly circumvent this rule by creatingg a subdomain on an attacker's domain name.

**Open Redirect:**
Open redirect is an **endpoint on t he server where the website visitor gets automatically redirected to another website address**.

For example

```
https://website.thm/link?url=https://tryhackme.com
```

This endpoint was created to record the number of times visitors have clicked on this link for marketing purposes.

If there was a a **potential SSRF vulnerability** with stringent rules which only allowed URLs beginning with https://website.thm an attacker could utilise the above feature to redirect the internal HTTP request to a domain of the attacker's choice.

