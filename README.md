ngx_http_proxy_connect_moduel
=============================

This module provides support for the "CONNECT" HTTP method.  
This method is mainly used to [tunnel SSL requests](https://en.wikipedia.org/wiki/HTTP_tunnel#HTTP_CONNECT_tunneling) through proxy servers.

Example
=======

```
 server {
     listen                         3128;

     # dns resolver used by forward proxying
     resolver                       8.8.8.8;

     # forward proxy for CONNECT request
     proxy_connect;
     proxy_connect_allow            443 563;
     proxy_connect_connect_timeout  10s;
     proxy_connect_read_timeout     10s;
     proxy_connect_send_timeout     10s;

     # forward proxy for non-CONNECT request
     location / {
         proxy_pass http://$host;
         proxy_set_header Host $host;
     }
 }
```

With above configuration, you can get any https website via HTTP CONNECT tunnel.
A simple test with command `curl` is as following:

```
$ curl https://github.com/ -v -x 127.0.0.1:3128

...

```

Also you can configure your browser to use this nginx as PROXY server.

Directive
=========

proxy_connect
-------------

Syntax: **proxy_connect**  
Default: `none`  
Context: `server`  

Enable "CONNECT" HTTP method support.

proxy_connect_allow
-------------------

Syntax: **proxy_connect_allow `[port ...]`**  
Default: `none`  
Context: `server`  

This directive specifies a list of port numbers or ranges to which the proxy CONNECT method may connect.  
All the ports are disabled by default.

proxy_connect_connect_timeout
-----------------------------

Syntax: **proxy_connect_connect_timeout `time`**  
Default: `none`  
Context: `server`  

Defines a timeout for establishing a connection with a proxied server.


proxy_connect_read_timeout
--------------------------

Syntax: **proxy_connect_read_timeout `time`**  
Default: `60s`  
Context: `server`  

Defines a timeout for reading a response from the proxied server.  
The timeout is set only between two successive read operations, not for the transmission of the whole response.  
If the proxied server does not transmit anything within this time, the connection is closed.

proxy_connect_write_timeout
---------------------------

Syntax: **proxy_connect_write_timeout `time`**
Default: `60s`
Context: `server`

Sets a timeout for transmitting a request to the proxied server.  
The timeout is set only between two successive write operations, not for the transmission of the whole request.  
If the proxied server does not receive anything within this time, the connection is closed.

Nginx Compatibility
===================

The latest module is compatible with the following versions of nginx:

* 1.9.2 (stable version of 1.9.x)
* 1.8.1 (stable version of 1.8.x)
* 1.6.3 (stable version of 1.6.x)
* 1.4.7 (stable version of 1.4.x)

Tengine Compatibility
=====================

This module will be merged into tengine soon, see [this pull request](https://github.com/alibaba/tengine/pull/335/).

Install
=======

Install this module from source:

```
$ wget http://nginx.org/download/nginx-1.9.2.tar.gz
$ tar -xzvf nginx-1.9.2.tar.gz
$ cd nginx-1.9.2/
$ patch -p1 < /path/to/ngx_http_proxy_connect_moduel/proxy_connect.patch
$ ./configure --add-module=/path/to/ngx_http_proxy_connect_moduel
$ make && make install
```

Note that `proxy_connect.patch` includes logic in macro NGX_HTTP_RPOXY_CONNECT, and [config](https://github.com/chobis/ngx_http_proxy_connect_moduel/blob/master/config) script will enable this macro automatically.

Author
======
This module was developed by [Peng Qi](https://github.com/jinglong) originally.  
He contributed this module to [Tengine](https://github.com/tengine) in this [pull request](https://github.com/alibaba/tengine/pull/335/).  

I build this module for Nginx proper based on his pull request.

