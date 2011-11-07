# Https example setup

## Make directory to hold the certificates
<pre>
mkdir ssl
cd ssl
</pre>

## Generating private key suitable for https serving...
<pre>openssl genrsa -out privkey.pem 1024 -des3</pre>

## Generating a Certficate Signing Request (certreq.csr)…
<pre>openssl req -new -key privkey.pem -out certreq.csr</pre>

## Generating a CA Certificate…
<pre>openssl req -new -x509 -key privkey.pem -out cacert.pem -days 1095</pre>

## Generating self-signed certificate
<pre>openssl x509 -req -days 3650 -in certreq.csr -signkey privkey.pem -out newcert.pem</pre>

## Add this to your boss.config:
<pre>
   {ssl_enable, true},
   {ssl_options,
      [
        {cacertfile, "ssl/cacert.pem"},
        {certfile, "ssl/newcert.pem"},
        {keyfile, "ssl/privkey.pem"},
        {verify, verify_peer},
        {fail_if_no_peer_cert, false}
    ]},
</pre>

## Make you startup script line (start-dev.sh, start.sh) look like this:
<pre>
From:
     -boot start_sasl -config boss -s reloader -s boss \

To:
     -boot start_sasl -config boss -s reloader -s inets -s ssl -s boss \
</pre>
