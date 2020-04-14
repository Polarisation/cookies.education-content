---
next: 'cookie-types'
title: 'HTTP Cookies: the technical details  | Cookies.education'
shortname: 'Technical details'
lang: 'en'
---

*This page is for people who want to get an in-depth technical understanding of cookies on the web. It is provided for those who would not be satisfied by a high-level conceptual description, and prefer to understand the technical details. For most people, our non-technical page, [What are Web Cookies?](./cookies-definition.md), will be more suitable. If you have some understanding of what HTML, URLs, and HTTP are, then read on...*

*This should be regarded as only an introduction to the technical side of cookies. We link to appropriate resources should you wish to learn more.*

### A brief history of HTTP Cookies

In the early years of the World Wide Web, when the three code elements of the web (HTML, URLs, and HTTP) were well established, there were no cookies. Any "state" (memory of previous events) that was to persist across web pages has to be passed around as part of the URL.

The simplicity, transparency, and convenience of having all state stored in the URL was a key to the swift take off of the web. As Tim Berners-Lee describes in his book, *Weaving the Web*, this was a huge advantage over previous protocols like FTP. Instead of having to provide a list of instructions ("directions") to access some resource, one can simply share the address (the URL).

However, in 1994, as the web started to move beyond informational pages towards applications, this felt limiting. It is possible to implement functionality such as a shopping cart by storing state on the server and passing a unique ID in the URLs, something like this:
 
     http://store.example.com/?cart_id=8ab3eb1849f0cff
 
 but this requires all links to be dynamically generated, and isn't as robust: that unique ID (and therefore the items in the cart) would be lost if the user left the website and returned to the website's homepage later.

Cookies provided a way to store state on the user's computer. Often, only a unique identifier would be stored to match the user to some state recorded in a database on the server. In this case, the data in a cookie might look something like:
 
    cart_id=8ab3eb1849f0cff

But cookies can also store all the state without any need for the server to do so, in which case the cookie might look like this:

    item[1]=Toothpaste,quantity[1]=2,item[2]=Toothbrush,quantity[2]=1
    
In fact, the scenario for which cookies were invented was a virtual shopping cart, and the state was to be entirely stored within cookies. The unique ID approach is perhaps more common these days because of the benefits to companies of collecting thousands of state records on the server.

Since the 1990s, the technical details of HTTP cookies have barely changed. There have been a few new developments to improve security or privacy, but the basic mechanism has not changed.

### HTTP Cookies by example

Cookies are set and accessed by servers using the HTTP protocol. They can also be set or accessed by JavaScript. In this section, we'll show examples of how they work.

#### Setting a cookie (via HTTP)

When a web browser loads a page (in this example, `http://www.example.com/contact/`), it sends a GET request using the HTTP protocol:

```$http
GET /contact/ HTTP/1.1

Host: www.example.com
[Additional HTTP headers]
```

The server will then return the page and may set cookies via HTTP headers:

```$http
HTTP/1.0 200 OK

Set-Cookie: uid=8ab3eb1849f0cff; Path=/; Max-Age=86400
Set-Cookie: session_id=263932539492; Path=/
[Additional HTTP headers]

[HTML page content]
```

In the example above, there are two `Set-Cookie` headers, each of which sets a different cookie:
 
  - The first sets a cookie with value `uid=8ab3eb1849f0cff`, with a `Max-Age` of `86400` seconds, which means it will expire one year from the current date and time. In this case, the cookie contains a unique identifier so could be used to track the user over time.
  - The second sets a cookie with value `session_id=263932539492`. In this case, since no `Max-Age` or `Expiry` is set, the cookie is treated as a *session* cookie, which means the web browser should delete it at the end of the current session (when it closes). This cookie also contains an ID, but since it is a session cookie it can only be used to track the user for a short time.
  
Both of the cookies were set for path `/`, which means that they will be accessible by any page on `www.example.com`.

#### Reading a cookie (via HTTP)

Once a cookie is set, a server does need to explicitly request a cookie from a web browser. Rather, the cookie is automatically sent to the server with every request, based on the rules in the HTTP cookie specification.

Let's see what happens when the browser loads the page `http://www.example.com/`. Firs the browser requests the page from the server

```$http
GET / HTTP/1.1

Host: www.example.com
Cookie: uid=8ab3eb1849f0cff; session_id=263932539492
[Additional HTTP headers]
```

As you can see, both the cookies that were set earlier are sent to the server. Not every cookie would be sent for every request. A cookie will only be sent if it is still active, and if the domain and path match the requested page.

The server will respond to the request as normal, but could send additional `Set-Cookie` headers in order to set a new cookie, update a cookie, or delete a cookie.

#### Setting a cookie (via JavaScript)

In JavaScript running in web browser, cookies can be accessed via the `document.cookie` property.

To set a cookie, assign to `document.cookie` like this:

```
document.cookie = "new_cookie=new_value;domain=example.com;path=/"
```

This consists of the cookie name `new_cookie`, the value `new_value`, the domain `example.com` and the path `/`. Other standard cookie properties can be specified in the same way as with the HTTP protocol.

It is only possible to set a single cookie with one assignment, but you can set multiple cookies using multiple assignments, and the other cookies will not be overwritten unless the properties (name, domain, etc) match.

#### Reading a cookie (via JavaScript)

You can read all cookies using `document.cookie`:

```
const cookies = document.cookie;
console.log(cookies);

> "new_cookie=new_value; uid=8ab3eb1849f0cff; session_id=263932539492"
```

It is not possible to view the properties of these cookies. This makes cookie manipulation in JavaScript somewhat limited. In many cases, there are better options for frontend storage, such as [the Web Storage API (localStorage / sessionStorage)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API).

For more tips on manipulating cookies via JavaScript, see [the MDN page on document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie).

### Cookie specifications

If you would like to study the technical details of cookies, all the relevant specifications are freely available online.

The current specification is [RFC 6265 - HTTP State Management Mechanism](https://tools.ietf.org/html/rfc6265), published in 2011. Older (and obsolete) specifications include [RFC 2965](https://tools.ietf.org/html/rfc2965) (2000), [RFC 2109](https://tools.ietf.org/html/rfc2109) (1997), and an earlier [preliminary specification from Netscape](https://web.archive.org/web/20070805052634/http://wp.netscape.com/newsref/std/cookie_spec.html).

The *Do Not Track* feature is commonly implemented by web browsers in some form, but the [Do Not Track specification (2011)](https://tools.ietf.org/html/draft-mayer-do-not-track-00) only reached the draft stage before being abandoned, due to concerns about the effectiveness of this tool.
