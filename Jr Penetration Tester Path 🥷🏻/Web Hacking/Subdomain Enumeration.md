Process of finding valid subdomains for a domain to find more **points of vulnerability.**

### OSINT

- SSL/TLS Certificates
These public accessible logs every **SSL/TLS** certificate created for a domain name.

https://crt.sh
https://ui.ctsearch.entrust.com/ui/ctsearchui

- Search Engines
Using site: filter can narrow search results, such as **"site:domain.com site:*.domain.com"***

- DNS Bruteforce
Enumeration method of trying thousands of possible subdomains from a pre-defined list of commonly used subdomains, automated with tools.

- Sublist3r

- Virtual Hosts
DNS records can also be kept on a private DNS server or recorded on the developer's machines where:

```
/etc/hosts
```

```
C:\Windows\system32\drivers\etc\hosts
```

Maps domain names to IP address

Web servers can **host multiple websites from one server** when a website is requested from a client, the server knows which website the client wants from the **Host header**, we can utilise this host header by making changes to it and monitoring the response to see if we've discovered a new website.

```
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.102.172
```

The above command uses the **-w** switch to specify the wordlist we are going to use.

The **-H** switch adds/edits a header, we have the **FUZZ** keyword in the space where a subdomain would normally go.

We will need to filter the output here, we can do this by using the page size result with the **-fs** switch.