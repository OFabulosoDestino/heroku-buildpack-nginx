nginx buildpack for Heroku.

## Installation

* Add a `conf/nginx.conf.erb` file to your app. There is no default
  configuration file, so you must do this. See below for a basic example.
* Add `web: bin/start_nginx` to your `Procfile`.

The compilation step uses a precompiled nginx binary (bin/nginx). See below.

### Basic example configuration file

```nginx
# https://gist.github.com/leklund/5877422
worker_processes  4;
daemon  off;

events {
	worker_connections  1024;
}

http {
	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
										'$status $body_bytes_sent "$http_referer" '
										'"$http_user_agent" "$http_x_forwarded_for"';

	sendfile  on;
	tcp_nopush  on;
	keepalive_timeout  30;
	gzip  on;

	include  mime.types;
	default_type  application/octet-stream;

	server {
		server_name  localhost;
		listen  <%= ENV['PORT'] %>;
		root  <%= ENV['HOME'] %>/html/;
		index  index.html;
	}
}
```

## Compiling a new nginx binary

Fork this repo. Change the nginx version in `support/build_nginx` as desired,
and run the the script in a one-off dyno. Scp the new binary to your forked
repo.

## Credits

Would not have been able to put this together without ruthlessly stealing from
these two buildpacks:

* https://github.com/neilmiddleton/heroku-buildpack-nginx
* https://github.com/ryandotsmith/nginx-buildpack

Thanks a ton!
