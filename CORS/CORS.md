# Cors (Cross Origin Resource Sharing)
---
#### What is SOP (Same Origin Policy) And CORS?

Suppose there are two domain abc.com and xyz.com. If abc.com is getting the strictly accessing resources from the same domain then this is said to be SOP Implementation. If abc.com is getting resources from xyz.com then there is the implementation of CORS.

![CORS](https://media.prod.mdn.mozit.cloud/attachments/2016/10/28/14293/1abbd90c71d03f08c9b055d32b2b2911/simple_req.png)

#### What are neccessary for finding CORS?

1. Request Header

    So there are many headers that is in need for implementation of CORS.

* Origin

    > Origin is header Which tells you about from where the request has been originated for accessing the resource.

    ```bash
    Origin: null
    ```
* Access-Control-Request-Method

    > Access Control Request Method are used when preflight request to let the server know which method will be used when actual request is requested.

    ```bash
    Access-Control-Request-Method: <method>
    ```
    **Will Discuss the Preflight Request**
* Access-Control-Request-Headers
    
    > Access Control Request Header is used when issuing a preflight request to let the server know what HTTP headers will be used when the actual request is made.

    ```bash
    Access-Control-Request-Headers: <field-name>[, <field-name>]*
    ```

2. Response Headers

* Access-Control-Allow-Origin

    >Access-Control-Allow-Origin tells the browser that accepts the request from Location where it is requested.
    It can be * means all or to specific domain.

    ```
    Access-Control-Allow-Origin: *
    ```

* Access-Control-Allow-Credential

    > When i am Exploiting CORS people says it's neccesary **Access-Control-Allow-Credential** to be true for exploitation. But don't knwo why so when **Access-Control-Allow-Credential** is true. It tells the browser to allow the resources to be extracted from the allowed Origin.

    ```
    Access-Control-Allow-Credential: true
    ```

* Access-Control-Max-Age

    > Access-Control-Max-Age header indicates how long the results of a preflight request can be cached. For an example of a preflight request, see the above examples.

* Access-Control-Allow-Methods

    > Access-Control-Allow-Methods header specifies the method or methods allowed when accessing the resource. This is used in response to a preflight request. The conditions under which a request is preflighted are discussed above.

    So If these header are there in Request/Response it means there is a [xhr](https://xhr.spec.whatwg.org/) request. xhr is basically cross site request. Read it it will be very helpfull for CORS.

    **Lets Discuss Preflight Request**

#### So What basically is Preflight Request?

Unlike the simple request preflight request are requested by browser using options method on the other domain to check determine whether the original request will be safe to send.

**Request**

```

OPTIONS /en?donotpoisoneveryone=1 HTTP/1.1
Host: theraid.page
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: http://google.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type
```

**Response**

```
HTTP/1.1 204 No Content
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://google.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
```

#### What Type of Vulnerability arises due to unsafe CORS?

There are some new vulnerability in this article from where you can make good bounty.

1. Simple CORS Misconfiguration
2. Null CORS Misconfiguration
3. CORS Using Subdomain Takeover
4. Bypassing Regex
    > So Bypassing Regex Right?
    First We need to know how the developer has implemented
    regex for whitelisting the domain

    Example #1:
    ```
    ^https?:\/\/(.*\.)?xxe\.sh$
    ```
    In this intent developer is allowing all the subdomains
    to make cross origin request.

    Example #2:
    ```
    ^https?:\/\/.*\.?xxe\.sh$
    ```
    Intent: Same as Example #1 – allow cross-domain access from xxe.sh and any subdomain

    This regular expression is quite similar to the first example, however it contains a problem that would cause the configuration to be vulnerable to data theft.
    The problem lies in the following regex: .*\.?

    Bypassing this regex
    Attacker can buy attackerxxe.sh make Cross origin request from there.

    Example #3:
    ```
    ^https?:\/\/(.*\.)?xxe\.sh\:?.*
    ```

    In this type of regex bypass a hacker can buy a domain like https://xxe.sh.attacker.com and make a origin request this will bypass the CORS.
    ```
    curl victim.com -H "Origin: https://xxe.sh.attacker.com"
5. CORS Using XSS on Subdomain

     Even "correctly" configured CORS establishes a trust relationship between two origins. If a website trusts an origin that is vulnerable to cross-site scripting (XSS), then an attacker could exploit the XSS to inject some JavaScript that uses CORS to retrieve sensitive information from the site that trusts the vulnerable application.

Given the following request:
```
    GET /api/requestApiKey HTTP/1.1
    Host: vulnerable-website.com
    Origin: https://subdomain.vulnerable-website.com
    Cookie: sessionid=...
```

    If the server responds with:

```
    HTTP/1.1 200 OK
    Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com
    Access-Control-Allow-Credentials: true
```
Then an attacker who finds an XSS vulnerability on subdomain.vulnerable-website.com could use that to retrieve the API key, using a URL like:

https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>

6. Breaking TLS with poorly configured CORS

    Suppose an application that rigorously employs HTTPS also whitelists a trusted subdomain that is using plain HTTP. For example, when the application receives the following request:

```
    GET /api/requestApiKey HTTP/1.1
    Host: vulnerable-website.com
    Origin: http://trusted-subdomain.vulnerable-website.com
    Cookie: sessionid=...
```
The application responds with:
```
    HTTP/1.1 200 OK
    Access-Control-Allow-Origin:http://trusted-subdomain.vulnerable-website.com
    Access-Control-Allow-Credentials: true
```
In this situation, an attacker who is in a position to intercept a victim user's traffic can exploit the CORS configuration to compromise the victim's interaction with the application. This attack involves the following steps:

The victim user makes any plain HTTP request.
    The attacker injects a redirection to: http://trusted-subdomain.vulnerable-website.com
    The victim's browser follows the redirect.
    The attacker intercepts the plain HTTP request, and returns a spoofed response containing a CORS request to: https://vulnerable-website.com
    The victim's browser makes the CORS request, including the origin: http://trusted-subdomain.vulnerable-website.com
    The application allows the request because this is a whitelisted origin. The requested sensitive data is returned in the response.
    The attacker's spoofed page can read the sensitive data and transmit it to any domain under the attacker's control.

This attack is effective even if the vulnerable website is otherwise robust in its usage of HTTPS, with no HTTP endpoint and all cookies flagged as secure. 
7. Access Internal Resources Even if Credential are not true

8. Server Side Cache Posisoning If CORS is misconfigured

    If the stars are aligned we may be able to use server-side cache poisoning via HTTP header injection to create a stored XSS vulnerability.

    If an application reflects the Origin header without even checking it for illegal characters like \r, we effectively have a HTTP header injection vulnerability against IE/Edge users as Internet Explorer and Edge view \r (0x0d) as a valid HTTP header terminator:
```
    GET / HTTP/1.1
    Origin: z[0x0d]Content-Type: text/html; charset=UTF-7
```
Internet Explorer sees the response as:
```
    HTTP/1.1 200 OK
    Access-Control-Allow-Origin: z
    Content-Type: text/html; charset=UTF-7
```
This isn't directly exploitable because there's no way for an attacker to make someone's web browser send such a malformed header, but I can manually craft this request in Burp Suite and a server-side cache may save the response and serve it to other people. The payload I've used will change the page's character set to UTF-7, which is notoriously useful for creating XSS vulnerabilities.

