### Manual Discovery

**robot.txt** is a document that tells search engines which pages they are and aren't allowed to show on their search engine results or ban specific search engines from crawling the website altogether.

```
User-agent: *
Allow: /
Disallow: /staff-portal
```

- Favicons
If this part of the installation gets leftover, this can give a clue on what  **framework is in use.**

This favicon can be checked his MD5 value and then looked upon online, such as

https://wiki.owasp.org/index.php/OWASP/favicon_database

- Sitemap.xml (10.10.102.172/sitemap.xml)
Gives a list of every file the website owner wishes to be listed on a search engine.

- HTTP Headers
Headers contain useful information such as the **webserver software** and possibly the programming language in use.

```
curl http://10.10.102.172 -v
```

```
* Rebuilt URL to: http://10.10.102.172/
*   Trying 10.10.102.172...
* TCP_NODELAY set
* Connected to 10.10.102.172 (10.10.102.172) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.10.102.172
> User-Agent: curl/7.58.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.18.0 (Ubuntu)
< Date: Wed, 01 May 2024 23:21:53 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< X-FLAG: THM{HEADER_FLAG}

```

### OSINT 

- Google Hacking / Dorking

This technique uses Google's advanced search engine features, which allows to pick out custom content, for example

**site** | site:tryhackme.com | returns only results from the specified website address
**inurl** | inurl:admin | returns results that have the specified word in the URL
**filetype** | filetype:pdf | returns results which are particular file extension
**intitle** | intitle:admin | returns results that contain the specified word in the title

-  Wappalyzer
Online tool and browser extension that helps identify what technologies a website uses.

-  Wayback Machine
-  Github
-  S3 Buckets
Storage service provided by Amazon AWS.

### Automated Discovery

Process of using tools to discover content rather than doing it manually.

-  Wordlists
Text files that contain a long list of commonly used words, such as [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

In order to target a website with this, use:

```
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.102.172/FUZZ
```

It is better to use Gobuster in order to look for directories

```
gobuster dir --url http://10.10.102.172/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

