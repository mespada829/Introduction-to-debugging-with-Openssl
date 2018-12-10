# Introduction-to-debugging-with-Openssl

In my current role working as a TLS support engineer I work with Openssl constantly. In most ocassions I used it to debug TLS connections, test a customer's origin server, measure tls connections among others. This software library is used to secure communications, even though you might find others Openssl is probably the most popular in the security industry.

I would always keep some sort of cheatsheet with most of the commands that I would use daily, since at times I would not remember the exact syntax.
Below you will find my own cheatsheet as per uses cases, or at least as I have them categorized. In my examples I'm using different tech companies to test them.
If you have any other useful commands feel free to email me.


# Troubleshooting - TLS client connecting to a remote server

If you are testing if you can connect to a remote server you could use the following:

openssl s_client -connect google.com:443
openssl s_client -host linkedin.com -port 443


Thie will override SNI (Server Name Indication) with another server name. This command is more common now that SNI is accepted, althoguh not in China at the moment due to goverment restrictions. 

openssl s_client -servername www.example.com -host example.com -port 443

Connect to a server and display the full certificate chain for a particular host name:

openssl s_client -showcerts -host twitter.com -port 443 </dev/null

openssl s_client -servername www.google.com -host google.com -port 443

The command below is useful to test if the server would be able to use different ciphers versus the preffered ones. Let's go over this in more detail. If you run for example - sslscan www.google.com you can see in the ouptut the preferred and accepted ciphers. By running - openssl s_client -host google.com -port 443 -cipher ECDHE-RSA-AES256-GCM-SHA384 2>&1 </dev/null you can test the accepted cipher vs the preffered one. 

Supported Server Cipher(s):
Preferred TLSv1.2  128 bits  ECDHE-RSA-AES128-GCM-SHA256   Curve P-256 DHE 256
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve P-256 DHE 256
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-SHA          Curve P-256 DHE 256
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA          Curve P-256 DHE 256
Accepted  TLSv1.2  128 bits  AES128-GCM-SHA256
Accepted  TLSv1.2  256 bits  AES256-GCM-SHA384
Accepted  TLSv1.2  128 bits  AES128-SHA
Accepted  TLSv1.2  256 bits  AES256-SHA
Accepted  TLSv1.2  112 bits  DES-CBC3-SHA
Preferred TLSv1.1  128 bits  ECDHE-RSA-AES128-SHA          Curve P-256 DHE 256
Accepted  TLSv1.1  256 bits  ECDHE-RSA-AES256-SHA          Curve P-256 DHE 256
Accepted  TLSv1.1  128 bits  AES128-SHA
Accepted  TLSv1.1  256 bits  AES256-SHA
Accepted  TLSv1.1  112 bits  DES-CBC3-SHA
Preferred TLSv1.0  128 bits  ECDHE-RSA-AES128-SHA          Curve P-256 DHE 256
Accepted  TLSv1.0  256 bits  ECDHE-RSA-AES256-SHA          Curve P-256 DHE 256
Accepted  TLSv1.0  128 bits  AES128-SHA
Accepted  TLSv1.0  256 bits  AES256-SHA
Accepted  TLSv1.0  112 bits  DES-CBC3-SHA

This command is good to understand the list of ciphers available to use with Openssl 

openssl ciphers -v

In some cases when I'm troubleshooting TLS connections I do have to measure TLS connection times to see if we have some discrepancies. 
The command below would help with that.

openssl s_time -connect google.com:443 -new
openssl s_time -connect facebook.com:443 -reuse


# Converting to PEM format 

In order to use this command you might need to understand the differnce between DER and PEM encondings, they may also be used as extensions.

.DER = The DER extension is used for binary DER encoded certificates. These files may also bear the CER or the CRT extension.   Proper English usage would be “I have a DER encoded certificate” not “I have a DER certificate”.

.PEM = The PEM extension is used for different types of X.509v3 files which contain ASCII (Base64) armored data prefixed with a “—– BEGIN …” line.

Below in the Open SSL command to conver betwen them:

openssl x509 -in example.pem -outform der -out example.der
openssl x509 -in example.der -inform der -out example.pem

Even though I have dated this post, as I continue my journey learning more about Openssl I will document my commands as well.


