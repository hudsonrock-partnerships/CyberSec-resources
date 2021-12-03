# Examples of Application Attacks

* * * * * * 

## Privilege Escalation

Obtaining elevated privileges (i.e. Administrator or Root) on the target.

* Dump the SAM (local accounts file) 
* Retrieve /etc/passwd file 
* Look for insecure file shares 
* DLL pre-loading 
* Insecure or weak security on processes

Many vulnerabilities enable an attacker to gain system-level permissions.

### Examples

```
cat /etc/passwd | cut –d: -f1
```

```
pinjector.exe –p <PID of the account you want to mimick permissions from> cmd.exe <port>
```

## Cross Site Scripting (XSS)

Techniques used to hijack sessions:

* Can be **non-persistent** (emails, instant messages, blog posts, etc.) 
* **DOM-based**, which can be persistent or non-persitent
* **Persistent** (server-based) where an attacker doesn't need to actively target a user

### Examples

```
const script = document.createElement('script')
script.innerHTML = 'alert("hacked!")'
document.body.appendChild(script)
```

## SQL Injection

Modifying the SQL query that's passed to web application, SQL server, etc.

Adding code into a data stream: 

* Bypass login screens
* Vulnerable websites return usernames, passwords, etc., with the right SQL injection 
* Cause the application to "throw" an error and crash (allowing an attacked remote access)

### Examples

`USERNAME'#`

`" or ""="`

`105; DROP TABLE TABLENAME`

## DLL Injection

DLL Injection is a process of inserting code into a running process: 
* Attach to the process 
* Allocate Memory within the process 
* Copy the DLL or the DLL Path into the processes memory and determine appropriate memory addresses 
* Instruct the process to execute your DLL

DLL Inejction attacks can be created manually pr Pen Testing Tools like Metasploit can automate the process.

## LDAP Injection

Lightweight Directory Access Protocol (LDAP):

* "Address Book" of user accounts used to authenticate users 
* Identifies level of access, group memberships, etc.

Similar to SQL Injection attacks in that the query that is passed to the web server is modified to include malicious query statements or code.

### Examples

```
"value)(injected_filter"
"value)(injected_filter))(&(1=0"
```

```
(&(attribute=value)(second_filter))
(|(attribute=value)(second_filter))
```

## XML Injection

Attach technique that manipulates the logic of an XML application or service.

Form Input Example:
`<input type="text" size=20 name="userName">Insert the username</input>`
Underlying Code:
```
String ldapSearchQuery = "(cn=" + $userName + ")"; 
    System.out.println(ldapSearchQuery);
```
String passed from the web browser:
`"crees) (| (password = * ) )"`

* Could be used to inject XML into a statement that alters a path to file to disclose sensitive information.

```
<?xml version="1.0"?> 
<!DOCTYPE results [<!ENTITY harmless SYSTEM 
    "file:///var/www/config.ini">]>
    
<results>
    <results>&harmless;</results>
</results>
```

### Examples

```
http://example.com/add_to_cart.php?itemId=5"+perItemPrice="0.00"+quantity="100"+/><item+id="5&quantity=0
```

```
http://www.example.com/create_user.php?name=thomas&password=hfdj7!dn&mail=thomas@mail.com
```

```
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM  "file:///dev/random" >]>
<foo>&xxe;</foo>
```

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<foo>&xxe;</foo>
```

## Pointer Dereference

Vulnerability that can cause an application to throw an exception error, which typically results in the application crashing: 
* Can be leverage for a DoS attack against the entire sytem 
* Remote code execution

C/C++, Assembly or any other language that uses pointers is potentially vulnerable to this type of attack.

## Directory Traversal / Command Injection

Attack that manipulates user input to cause the application to traverse a directory structure and access files not intended to be visible:
* known as the `../` or "dot slash" attack 
* Directory climbing 
* Backtracking

## Buffer Overflow

## Race Conditions

## Time of Check

## Secure Conding Concepts, Error Handling, Input Validation

## Replay Attacks

## Integer Overflow

## Cross Site Request Forgery (XSRF)

## API Attacks

## Resource Exhaustion

## Memory Leak

## SSL Stripping

## Shimming

## Refactoring

## Pass the Hash

* * * * * * 

# Disclaimer

Educational purposes only. Source of information is publicly and/or freely available online.