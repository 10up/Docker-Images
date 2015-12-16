# 10up Docker Library

The Dockerfiles in this repository can be used to compile Docker images intended to be used as PHP upstreams for WordPress sites.

## What's Included

PHP-FPM at:

- 5.3.29 (compiled without openssl support for now)
- 5.4.45
- 5.5.30
- 5.6.16
- 7.0.0

## How to use it

First, run a container:

```sh
docker run 
```

Then, set your PHP upstreams in Nginx to point to the container. For clarity, each container listens on a different port:

- 5.3 => 9053
- 5.4 => 9054
- 5.5 => 9055
- 5.6 => 9056
- 7.0 => 9070

The `--host=net` directive above will map all host ports into the container, so if PHP is running globally on port 9000, the containers won't conflict.

In addition, this means the PHP process in each container can communicate with Memcached (port 11211) and MySQL (port 3306) on the Docker host as well.
 
Each container will attempt to install the Pecl extensions for caching backends:
- Redis
- Memcached
- Memcache
- APCu

(Unless they are unsupported. APCu is PHP7 only. Redis and Memcached are not yet PHP7-compatible.)

## What's Next

Repair the 5.3 container's OpenSSL support. Currently, it's trying to use 1.0.0 instead of 0.9.8 and this is causing problems when compiling PHP. OpenSSL is merely disabled for now to compensate but needs to be re-enabled.

## License

10up's Docker Library is free software; you can redistribute it and/or modify it under the terms of the [GNU General
Public License](http://www.gnu.org/licenses/gpl-2.0.html) as published by the Free Software Foundation; either version
2 of the License, or (at your option) any later version.