---
title: Fix Java PKIX path building issue
date: 2020-07-23 22:05:40
tags: SSLHandshakeException javax.net.ssl
---

You will get ```javax.net.ssl.SSLHandshakeException: PKIX path building failed``` exception if the JDK can't find a valid certificate for the target endpoint which your application running on.
To fix the issue, You must import the target endpoint's certificate into your JDK *cacerts* file.

### Error message
```
javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```

### Steps to import the certificate

- Download the target certificate
```shell script
> openssl s_client -showcerts -connect example.com:port </dev/null 2>/dev/null|openssl x509 -outform PEM >my-cert.pem
```
replace ```example.com``` and ```port``` with actual values

- Convert certificate into DER format
```shell script
> openssl x509 -outform der -in my-cert.pem -out my-cert.der
```

- Import into keystore using ```keytool```
```shell script
> keytool -import -alias your-alias -keystore /path/to/your/cacerts -file my-cert.der
```

On mac you can find the ```cacerts``` file in ```/Library/Java/JavaVirtualMachines/your.jdk.version/Contents/Home/jre/lib/security```.