# Content Discovery

---
Date: 2021-12-17  
Author: Michael Baumberger  
[TryHackMe link](https://tryhackme.com/room/contentdiscovery)
---

## Task 2 | Manual Discovery - Robots.txt

In the Url http://10.10.62.146/robots.txt we see this content:

    curl http://10.10.62.146/robots.txt
    User-agent: *
    Allow: /
    Disallow: /staff-portal

As we can see they want to hide **'/staff-portal'** from search engines.

## Task 3 | Manual Discovery - Favicon

Download the favicon and pipe it to md5sum:

    curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
    Dload  Upload   Total   Spent    Left  Speed
    100  1406  100  1406    0     0   7808      0 --:--:-- --:--:-- --:--:--  7811
    f276b19aabcb4ae8cda4d22625c6735f  -

Now we can lookup the hash in OWASP favicon database (https://wiki.owasp.org/index.php/OWASP_favicon_database).
We see this:

    f276b19aabcb4ae8cda4d22625c6735f:cgiirc (0.5.9)

## Task 4 | Manual Discovery - Sitemap.xml

We can curl http://10.10.62.146/sitemap.xml and see **'/s3cr3t-area'**.

    curl http://10.10.62.146/sitemap.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <url>
            <loc>http://10.10.62.146/</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>1.00</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/news</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/news/article?id=1</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/news/article?id=2</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/news/article?id=3</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/contact</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/customers/login</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
        <url>
            <loc>http://10.10.62.146/s3cr3t-area</loc>
            <lastmod>2021-07-19T13:07:32+00:00</lastmod>
            <priority>0.80</priority>
        </url>
    </urlset>

## Task 5 | Manual Discovery - HTTP Headers

We can curl **'curl 10.10.62.146 -v' and see a flag in the header**.

## Task 6 | Manual Discovery - Framework Stack

This flag was already found in [walking an application](../walking-an-application/walking-an-application.md) in Task 3 at the end.

## Task 12 | Automated Discovery

Start a gobuster scan:

    gobuster dir --url http://10.10.62.146/ -w /usr/share/dirbuster/wordlists/directory-list-1.0.txt
    ===============================================================
    Gobuster v3.1.0
    by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
    ===============================================================
    [+] Url:                     http://10.10.62.146/
    [+] Method:                  GET
    [+] Threads:                 10
    [+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-1.0.txt
    [+] Negative Status codes:   404
    [+] User Agent:              gobuster/3.1.0
    [+] Timeout:                 10s
    ===============================================================
    2021/12/19 15:25:11 Starting gobuster in directory enumeration mode
    ===============================================================
    /news                 (Status: 200) [Size: 2538]
    /contact              (Status: 200) [Size: 3108]
    /assets               (Status: 301) [Size: 178] [--> http://10.10.62.146/assets/]
    /private              (Status: 301) [Size: 178] [--> http://10.10.62.146/private/]
    /customers            (Status: 302) [Size: 0] [--> /customers/login]
    /monthly              (Status: 200) [Size: 28]
    
    ===============================================================
    2021/12/19 15:40:05 Finished
    ===============================================================

Here we find the **asked folder '/monthly'** but not the log file so we try it again with dirbuster and another wordlist:

    dirb http://10.10.62.146/ /usr/share/wordlists/dirb/common.txt
    
    -----------------
    DIRB v2.22
    By The Dark Raver
    -----------------
    
    START_TIME: Sun Dec 19 15:42:03 2021
    URL_BASE: http://10.10.62.146/
    WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt
    
    -----------------
    
    GENERATED WORDS: 4612

    ---- Scanning URL: http://10.10.62.146/ ----
    ==> DIRECTORY: http://10.10.62.146/assets/
    [+] http://10.10.62.146/contact (CODE:200|SIZE:3108)
    [+] http://10.10.62.146/customers (CODE:302|SIZE:0)
    [+] http://10.10.62.146/development.log (CODE:200|SIZE:27)
    [+] http://10.10.62.146/monthly (CODE:200|SIZE:28)
    [+] http://10.10.62.146/news (CODE:200|SIZE:2538)
      ==> DIRECTORY: http://10.10.62.146/private/
    [+] http://10.10.62.146/robots.txt (CODE:200|SIZE:46)
    [+] http://10.10.62.146/sitemap.xml (CODE:200|SIZE:1383)
    
    ---- Entering directory: http://10.10.62.146/assets/ ----
    ==> DIRECTORY: http://10.10.62.146/assets/avatars/
    
    ---- Entering directory: http://10.10.62.146/private/ ----
    [+] http://10.10.62.146/private/index.php (CODE:200|SIZE:49)
    
    ---- Entering directory: http://10.10.62.146/assets/avatars/ ----
    
    -----------------
    END_TIME: Sun Dec 19 16:03:20 2021
    DOWNLOADED: 18448 - FOUND: 8

This time we find the **file '/development.log'**.