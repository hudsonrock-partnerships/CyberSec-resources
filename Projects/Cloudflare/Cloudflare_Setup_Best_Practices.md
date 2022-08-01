# WORK IN PROGRESS...

# Cloudflare

A brief and general guide on how to **set up a Cloudflare account**, **add a Zone (domain)**, as well as some **Best Practices** on Security and Performance solutions, including some Admin best practices.

Another very good starting place is the [Core Setup](https://www.cloudflare.com/welcome-center/core-setup/) guide or the [Considerations for enhancing cyber-readiness](https://www.cloudflare.com/lp/cyberattackreadiness/) on Cloudflare.

_Disclaimer: This is for educational purposes only, and depending on the situation, some configurations or steps might differ, or change, or have different impacts._

***

# Table of Contents

[Security Best Practices](#securitybestpractices)  
[Performance Best Practices](#performancebestpractices)  
[Administrative Best Practices](#adminbestpractices)  
[Common Questions](#commonquestions)  

Inspiration / Source: _CloudFlare Best Practices v2.0, https://vsip.info/cloudflare-best-practices-v20-pdf-free.html_

* * * *

[Full or CNAME Setup](#fullsetup)  
[Troubleshooting](#troubleshooting)  
[Allow Cloudflare IPs](#allowips)  
[Log Retention](#logs)  
[Domain Lookup](#domainlookup)  

* * * *
* * * *

<a name="securitybestpractices"></a>

# Enterprise Best Practices

## Secure Origin IP Addresses

_Orange-Cloud all DNS Records for HTTP(S) traffic from your origin._

How a **Grey-Clouded** record looks like:
```
dig greycloud.theburritobot.com @woz.ns.cloudflare.com +short
```

How an **Orange-Cloude** record looks like:
```
dig orangecloud.theburritobot.com @woz.ns.cloudflare.com +short
```

## Configure your Security Level selectively

On **Page Rules**, select the URL pattern and set the Security Level Setting to High.

_Note:_ decrease the Security Level for non-sensitive paths or APIs to reduce false positives.

_Security Level settings are aligned with a threat scores that IP addresses acquire with malicious behavior on our network. A threat score above 10 is considered high and 50 is really bad._

## Activate your Web Application Firewall safely

1. First, set the **OWASP ModSecurity** sensitivity to High with an action of Simulate in order to log any false positives.

2. Second, activate **Managed Rules** (Cloudflare Specials) and any platform-specific groups you use.

3. Finally, turn your WAF ON with the global setting.

* * * *

<a name="performancebestpractices"></a>

# Performance Best Practices

## Activate Web Content Optimization (WCO) Features

* Enable **Polish** for image compression.

* Enable **minify HTML, CSS and JavaScript**. 

* Test **Mirage** (mobile specific image optimization) and **Rocket Loader** (forces JavaScript to be loaded asynchronously) with your system.

* Enable **Cache Everything** for static HTML webpages on **Page Rules**.

* Utilize conservative **TTLs (Time-to-Lives)** for content that changes occasionally.

Check the response header `CF-Cache-Status`:
```
curl -I theburritobot.com | grep -Fi CF-Cache-Status
```

* Accelerate your dynamic content with **Railgun** (compresses previously unreachable web objects).

* * * *

<a name="adminbestpractices"></a>

# Admin Best Practices

## Manage your brand by customizing Cloudflare pages

* **Domain-wide**: within the settings of each domain, you can go to the Customize application to change the default pages for each and every page we could potentially show your users.

* **Organization-wide**: if you have many domains within your CloudFlare account and would like to create Custom pages for all of them, you may do so within your Organization settings.

## Enforce 2-factor authentication across your entire organization

On the upper right hand corner, select **My Profile** > Authentication > Two-Factor Authentication.

Alternatively, go on **Manage Account** > Members > Member 2FA enforcement, in order to require all members to have two-factor authentication enabled.

* * * *
* * * *
* * * *

# Interesting / Useful Stuff

<a name="fullsetup"></a>

## Full Setup

Full Setup: use the setup wizard on Cloudflare's website when you register, follow the steps, and change your Domain's Nameservers.

Add A Record:
```
A	www	IP_ADDRESS	Auto	Proxied
```

Alternatively, one can do a [CNAME Setup](https://support.cloudflare.com/hc/en-us/articles/360020348832-Understanding-a-CNAME-Setup)

***

<a name="troubleshooting"></a>

## Troubleshooting

### MTU Size

Get the Maximum Transmission Unit (MTU) size on your device:
```
networksetup -getMTU en0
```

Do a Browser Integrity Check (BIC), which should return a 403 status code:
```
curl http://cf-testing.com --header "User-Agent: CloudFlare BIC Test" --verbose --silent 2>&1 | egrep -i "< HTTP|< Server:|< CF|<title>|signature"
```

### Check DNS Propagation & Response

Check A Record for a **Full Setup**: https://dnschecker.org/#A/staging.dt-cname.cf.cdn.cloudflare.net OR `curl -svo /dev/null/ http://staging.dt-cname.cf/ 2>&1 | grep 'HTTP'`

cURL is a command line tool used to transport data using the URL syntax:
```
curl -svo /dev/null/ https://staging.dt-cname.cf/
```

Use cURL option to check the origin response directly:
```
curl -svo /dev/null/ https://staging.dt-cname.cf/ --connect-to ::35.234.81.115
```

Check TXT record for a **CNAME Setup**: https://dnschecker.org/#TXT/CLOUDFLARE-VERIFY.dt-cname.cf

https://staging.dt-cname.cf/cdn-cgi/trace 


Check the Cloudflare Response:
```
curl -svo /dev/null/ https://staging.dt-cname.cf/ 
```
```
Server: cloudflare
```

### Check Origin Server Response

Check the Origin Response:
```
curl -svo /dev/null/ http://35.234.81.115/
```
```
Server: Apache/2.4.18 (Ubuntu) (bypasses Cloudflare)
```

### Check Nameservers

Check Nameservers:
```
dig +short NS cf-testing.com
host -t NS cf-testing.com
```

Run DNS queries and check DNS records:
```
dig @1.1.1.1 https://staging.dt-cname.cf/
```

### traceroute / mtr

traceroute and tracert are computer network diagnostic commands for displaying possible routes (paths) and measuring transit delays of packets:
```
traceroute staging.dt-cname.cf
```

mtr is a network based command line tools used to measure performance/latency on a particular path to a given host/destination:
```
sudo mtr staging.dt-cname.cf
```

*** 

<a name="allowips"></a>

## Allow Cloudflare IPs

Allow Cloudflare IP Addresses:
https://support.cloudflare.com/hc/en-us/articles/201897700-Allowing-Cloudflare-IP-addresses 

Restoring original visitor IPs:
https://support.cloudflare.com/hc/en-us/articles/200170786-Restoring-original-visitor-IPs 

Display all current settings of the IP packet filter:
```
sudo iptables -L -nv
```

Input Cloudflare IP Addresses (See Screenshot)

***

<a name="logs"></a>

## Log Retention

Log Retention is off by default.
https://developers.cloudflare.com/logs/get-started 
Activate via API.
https://developers.cloudflare.com/logs/logpull/enabling-log-retention 

Scan Open Ports on Origin Server:
https://pentest-tools.com/network-vulnerability-scanning/tcp-port-scanner-online-nmap# 

Turn on the WAF, and then try a PHP Code Injection Test:
```
curl -svo /dev/null/ "https://www.dt-cname.cf/file.php?cmd=echo(shell_exec(%22ls%20/etc/var%22))"
```
```
for i in {1..100}; do curl -svo /dev/null/ -H "exploit: true" "https://www.dt-cname.cf/file.php?cmd=echo(shell_exec(%22ls%20/etc/var%22))" 2>&1 | grep "< HTTP"; done;
```

*** 

## Create a Rate Limiting Rule.

Try out the Rate Limiting:
```
for i in {1..200}; do curl -svo /dev/null/ -H "requestflood: true" "https://www.dt-cname.cf/" 2>&1 | grep "< HTTP"; done;
```
This shouldn’t impact any good & verified bots like Google, pingdom, etc.

Page Rules to cache everything.

Test the Website Performance:
https://www.webpagetest.org/ 

Custom Purge. (See screenshot)


***
***

https://support.cloudflare.com/hc/en-us/articles/360029779472 

https://support.cloudflare.com/hc/en-us/articles/115003011431-Troubleshooting-Cloudflare-5XX-errors 

https://support.cloudflare.com/hc/en-us/articles/203118044-How-do-I-generate-a-HAR-file- 

***

DNSSEC
https://dnsviz.net/d/staging.cf-testing.com/dnssec/ 

======
======
======

GitHub

https://shields.io/category/other 
https://github.com/pabloqc/pabloqc/blob/main/README.md 

***

<a name="domainlookup"></a>

## Domain Lookup

https://rdap.cloudflare.com/

* * * *
* * * *
* * * *

<a name="commonquestions"></a>

# Common Questions

## DNS

### Upload multiple DNS records

Upload multiple DNS records under the Advanced section with a TXT file in BIND format.

### CNAME Setup

A partial setup or CNAME setup allows you to route only the traffic you want through Cloudflare, allowing you to keep your Authoritative DNS.

### Orange Clouded Record

Orange Clouded DNS records return Cloudflare's anycast IPs, obfuscating origin IPs and allowing SSL/TLS termination at Cloudflare's Edge to apply security and performance features. Gray Clouded DNS records return the actual DNS record.

## SSL

### Use your own SSL Certificate

You can upload your own SSL Certificate and Private Keys into the Dashboard. Additionally, you can use [Data Localization](https://www.cloudflare.com/data-localization/) to comply with evolving regional data privacy requirements.

### Full SSL settings

On the SSL/TLS tab, it is recommended to select the "Full (strict)" or "Strict (SSL-Only Origin Pull)" option which validates that the certificate at your Origin Server is from a Certificate Authority, has not expired, and contains the hostname for the request coming from the visitor.

### Minimum TLS Version

The minimum TLS version by default is TLS 1.0. However, it is recommended to set the option to TLS 1.2 or above, like TLS 1.3.

### Universal SSL vs Dedicated SSL

You can choose if you want to use Universal SSL or Dedicated SSL, which will have your zone's domain name.

## Firewall

### Country Block

You can block any specific country, as well as apply Captcha Challenge or JavaScript Challenge, which presents a complex math problem to the browser and requires JS to be enabled in order to pass the challenge.

### Rate Limiting

Protects specific sites from brute force attacks.

### WAF Rules

Cloudflare offers a variety of WAF Rules, such as OWASP Top 10, managed Rulesets (maintained by Cloudflare's Security Engineers), and more. Additionally, you can write your own rules to i.e. block specific IPs or create more complex rules.

## Caching

### Purge Cached Items

Normally it takes less than 30 seconds for a cache purge across the entire Cloudflare network.

## Page Rules

## Traffic

## Speed

## Workers

## Multi-User Organization

## Analytics

## General
