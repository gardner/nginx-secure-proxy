# Optimized container for Nginx with very secure SSL and mod_security enabled

The image contails [Nginx](https://nginx.org) v1.10.1 (stable) and [mod_security](https://github.com/SpiderLabs/ModSecurity) v2.9.1 with [OWASP basic ruleset](https://github.com/SpiderLabs/owasp-modsecurity-crs).

This image *does not contain* following nginx modules in contrast to default nginx contailer:
- http_xslt_module
- http_perl_module

## Environment variables and defaults

* __DH\_SIZE__
 * default: 2048 (which takes a long time to create), for demo or unsecure applications you can use smaller values like 512

## Running nginx-mod_security Container

This Dockerfile is not really made for direct usage. It should be used as base-image for your nginx project. But you can run it anyways.

You should overwrite the _/etc/nginx/external/_ with a folder, containing your nginx __\*.conf__ files, certs and a __dh.pem__.
_If you forget the dh.pem file, it will be created at the first start - but this can/will take a long time!_

    docker run -d \
    -p 80:80 -p 443:443 \
    -e 'DH_SIZE=512' \
    -v $EXT_DIR:/etc/nginx/external/ \
    nginx-mod_security

## Based on

This Dockerfile is based on the Alpine Official Image.

## Cheat Sheet

### Creating the dh4096.pem with openssl

To create a Diffie-Hellman cert, you can use the following command

    openssl dhparam -out dh4096.pem 4096

### Creating a high secure SSL CSR with openssl

This cert might be incompatible with Windows 2000, XP and older IE Versions

    openssl req -nodes -new -newkey rsa:4096 -out csr.pem -sha256

### Creating a self-signed ssl cert

Please note, that the Common Name (CN) is important and should be the FQDN to the secured server:

    openssl req -x509 -newkey rsa:4086 \
    -keyout key.pem -out cert.pem \
    -days 3650 -nodes -sha256

## Credits

This image was inspired by the work done on https://github.com/nginxinc/docker-nginx and https://hub.docker.com/r/dan78uk/nginx-ssl-mod-security