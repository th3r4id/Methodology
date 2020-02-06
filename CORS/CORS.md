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

1. 
2. 
3. 
4. 
5. Access Internal Resources Even if Credential are not true

