# Deploy solutions
*Work in progress*

## Standalone server
Chicago boss ships with their own high performance web server, it does not require Apache, Nginx, haproxy.
Currently these two web servers are supported:

1. [Misultin](https://github.com/ostinelli/misultin) - Misultin (pronounced mee-sool-téen) is an Erlang library for building fast lightweight HTTP(S) servers, which also supports websockets.
2. [Mochiweb](https://github.com/mochi/mochiweb) - MochiWeb is an Erlang library for building lightweight HTTP servers.

Here's a comparison and some load tests with different web servers under Chicago Boss: [labs.evolope.com](http://labs.evolope.com)

The default port is 8001 (see [[Configuration]] on how to change it), if you want to bind your application to the standard port 80 in unix you will need extra work, ports below 1024 can be opened only by root.

A few solutions:
## Iptables

You can make these changes survive reboots by adding this line to /etc/rc.local
<pre>
<code class="shell">
# iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8001
</code>
</pre>

* *"Privbind":http://manpages.ubuntu.com/manpages/lucid/man1/privbind.1.html* Not tested, 

## Proxy reversed mode
If you need to deploy boss in an existing and shared environment or you need to deploy your app in a sub-url, you can use a proxy reversed solution.

* Apache Example, also includes the config to server the static stuff directly
<pre>
<code class="apache">
<VirtualHost *:80>
    ServerName yourdomain.com
    ProxyRequests Off
    ProxyPass / http://localhost:8001/
    ProxyPassReverse / http://localhost:8001/
    <Proxy *> 
        Order Allow,Deny 
        Allow from all 
    </Proxy>
    # Alias to serve the assets directly by apache
    Alias "/static" /path/to/your/project/static
</VirtualHost>
</code>
</pre>

* Nginx Example
<pre>
<code class="nginx">
server {
    listen 80 default;
    server_name  _;
 
    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_redirect          off;
        proxy_set_header        Host            $host;
        proxy_set_header        X-Real-IP       $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   }
}
</code>
</pre>