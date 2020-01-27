---
layout: post
title: Server to Client
---

Before anything can happen in a browser, it must first know where to go. There are multiple ways to get somewhere: entering a URL in the address bar, clicking (or tapping) on a link on a page or in another app, or clicking on a favorite. No matter the case, these all result in what’s called a navigation. A navigation is the very first step in any web interaction, as it kicks off a chain reaction of events that culminates in a web page being loaded.

## Initiating the request
Once a URL has been provided to the browser to load, a few things happen under the hood.

### CHECK FOR HSTS
First, the browser needs to determine if the URL specifies the HTTP (non-secure) scheme. If it’s an HTTP request, the browser needs to check if the domain is in the HSTS list (HTTP Strict Transport Security). This list is comprised of both a preloaded list and a list of previously visited sites that opted-in to using HSTS; both are stored in the browser. If the requested HTTP host is in the HSTS list, a request is made to the HTTPS version of the URL instead of HTTP. This is why you’ll notice that even if you try to type http://www.bing.com into a modern browser, it will send you to https://www.bing.com instead.

### CHECK FOR SERVICE WORKERS
Next, the browser needs to determine if a service worker is available to handle the request—this is especially important in the case that the user is offline and does not have a network connection. Service workers are a relatively new feature in browsers. They enable offline-capable web sites by allowing interception of network requests (including the top-level request) so the requests can be served from a script-controlled cache.

A service worker can be registered when a page is visited, a process that records the service worker registration and URL mapping to a local database. Determining whether a service worker is installed is as simple as looking up the navigated URL in that database. If a service worker exists for that given URL, it will be allowed to handle responding to the request. In the case that the new Navigation Preload feature is available in the browser, and the site makes use of it, the browser will simultaneously also consult the network for the initial navigation request. This is beneficial because it allows the browser to not block on a potentially slower service worker start up.

In a case where there is no service worker to handle the initial request (or if Navigation Preload is being used), the browser moves on to consulting the networking layer.

### CHECK THE NETWORK CACHE
The browser, via the network layer, will check if there’s a fresh response in its cache. This is usually defined by the Cache-Control header in the response, where setting a max-age can define how long the cached item is considered fresh, and setting no-store indicates whether it should be cached at all. And of course, if the browser finds nothing in its network cache, then a network request will be required. If there is a fresh response in the cache, it is returned back for the purposes of loading the page. If there’s a resource found but it’s not fresh, the browser may convert the request to a conditional revalidation request, which contains an If-Modified-Since or If-None-Match header that tells the server what version of the content the browser already has in its cache. The server can either tell the browser that its copy is still fresh by returning an HTTP 304 (Not Modified) with no body, or tell the browser that its copy is stale by returning an HTTP 200 (OK) response with the new version of the resource.

### CHECK FOR CONNECTION
If there’s a previously established connection for the host and port for the request, the connection will be reused rather than establishing a new one. If not, the browser consults the networking layer to understand if it needs to do a DNS (Domain Name System) lookup. This would involve looking through the local DNS cache (which is stored on your device), and, depending on the freshness of that cache, remote name servers may also be consulted (they can be hosted by Internet Service Providers), which would eventually result in the correct IP address for the browser to connect to.

In some cases, the browser may be able to predict which domains will be accessed, and connections to those domains can be primed. The page can hint to the browser which to prime connections to by using resource hints such as rel="preconnect” on the link tag. One such scenario where using resource hints is helpful is if a user is on a Bing search results page, and there is an expectation that the first few search results are the most likely to be visited. In this case, priming connections to those domains can help with not having to pay the cost of a DNS lookup and connection setup later on when those links are clicked.

### ESTABLISH CONNECTION
The browser can now establish a connection with the server so the server knows it will be both sending to and receiving from the client. If we’re using TLS, we need to perform a TLS handshake to validate the certificate provided by the server.

### SEND THE REQUEST TO THE SERVER
The first request that will go over this connection is the top-level page request. Typically, this will be an HTML file that gets served from the server back to the client.

### HANDLE THE RESPONSE
As the data is being streamed over to the client, the response data is analyzed. First, the browser checks the headers of the response. HTTP headers are name-value pairs that are sent as part of the HTTP response. If the headers of the response indicate a redirect (e.g., via the Location header), the browser starts the navigation process all over again and returns to the very first step of checking if an HSTS upgrade is required.

If the server response is compressed or chunked, the browser will attempt to decompress and dechunk it.

As the response is being read, the browser will also kick off writing it to the network cache in parallel.

Next, the browser will attempt to understand the MIME type of the file being sent to the browser, so it can appropriately interpret how to load the file. For instance, an image file will just be loaded as an image, while HTML will be parsed and rendered. If the HTML parser is engaged, the contents of the response are scanned for URLs of likely resources to be downloaded so that the browser can start those downloads ahead of time before the page even begins to render. This will be covered in more detail by the next post in this series.

By this point, the requested navigation URL has been entered into the browser history, which makes it available for navigation in the back and forward functionality of the browser.

Here’s a flowchart that gives you an overview of what’s been discussed so far, with a bit more detail:

![_config.yml](https://alistapart.com/d/server-to-client/Flowchart_A_List_Apart_1392w.png)

As you know, the page will continue to make requests, because there are many sub-resources on the page that are important for the overall experience, including images, JavaScript, and style sheets. Additionally, resources that are referenced within those sub-resources, such as background images (referenced in CSS) or other resources initiated by fetch(), import(), or AJAX calls. Without these, we would just have a plain page without much interactivity. As you’ve seen in both the explanation earlier and the flowchart, each resource that is requested is in part impacted by the browser’s caching policies.

## Caching
As mentioned previously, the browser manages a network cache, which allows previously downloaded resources to be reused in many cases. This is particularly useful for largely unchanging resources, such as logos and JavaScript from frameworks. It’s important to take advantage of this cache as much as possible, because it can help reduce the number of outgoing network requests by instead reusing the locally available cached resource. In turn, this helps minimize the otherwise laborious and latent-prone operations that are required, improving the page load time.

Of course, the network cache has a quota that impacts both how many items will be stored and how long they’ll be stored for. This doesn’t mean that the website doesn’t get a say in the matter. Cache-Control headers in responses control the browser’s caching logic. In some cases, it’s prudent to tell the browser to not cache an item at all (such as with Cache-Control: no-store), because it is expected to always be different. In other cases, it makes sense to have the browser cache the item indefinitely via Cache-Control: immutable, because the response for a given URL will never change. In such a case, it makes sense to use different URLs to point to different versions of the same resource rather than making a change to a resource of the same URL since the cached version would always be used.

Of course, the network cache is not the only type of cache in the browser. There are programmatic caches that can be leveraged via JavaScript. Specifically, in the example of the service worker given above, an initial resource request for the top-level page can be intercepted by the service worker and can then use a cached item that was defined by the site by one of its programmatic caches. This is useful, because it gives the web site more control over what cached items to use when. These caches are origin-bound, which means that each domain has its own sandboxed set of caches it can control that are isolated from the caches of another domain.

## Origin model
An origin is simply a tuple consisting of the scheme/protocol, the hostname, and the port. For instance, https://www.bing.com:443 has the HTTPS protocol, www.bing.com hostname, and 443 as the port. If any of those are different when compared to another origin, they are considered to be different origins. For instance, https://images.bing.com:443 and http://www.bing.com:80 are different origins.

The origin is an important concept for the browser, because it defines how data is sandboxed and secured. In most cases, for security purposes, the browser enforces a same-origin policy, which means that one origin cannot access the data of another origin—both would need to be the same origin. Specifically, in the caching case presented earlier, neither https://images.bing.com:443 nor http://www.bing.com:80 can see the programmatic cache of the other.

If bing.com wanted to load a JavaScript file that is from microsoft.com, it would be making a cross-origin resource request on which the browser would enforce the same-origin policy. To allow this behavior, microsoft.com would need to cooperate with bing.com by specifying CORS (Cross-Origin Resource Sharing) headers that enable bing.com to be able to load the JavaScript file from microsoft.com. It’s good practice to set the correct CORS headers so browsers can appropriately deal with the cross-origin resource requests.

## Conclusion
Now that you know how we go from the server to the client—and all the details in between—stay tuned to learn about the next step in loading a web page: how we go from HTML tags to the DOM.