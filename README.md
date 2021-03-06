referrer-spam-blocker [![Build Status](https://travis-ci.org/Stevie-Ray/referrer-spam-blocker.svg)](https://travis-ci.org/Stevie-Ray/referrer-spam-blocker)
====================================

Apache, Nginx, IIS & Varnish blacklist plus Google Analytics segment to prevent referrer spam traffic 👾

- - - -

## Apache: .htaccess

.htaccess is a configuration file for use on web servers running Apache. This file is usually found in the root “public_html” folder of your website. The .htaccess file uses two modules to prevent referral spam, mod_rewrite and mod_setenvif. Decide which method is most suitable with your Apache server configuration. This file is **Apache 2.4** ready, where mod_authz_host got deprecated.


## Nginx: referral-spam.conf

With `referral-spam.conf` in `/etc/nginx`, include it globally from within `/etc/nginx/nginx.conf`:

```conf
http {
	include referral-spam.conf;
}
```

Add the following to each `/etc/nginx/site-available/your-site.conf` that needs protection:

```conf
server {
	if ($bad_referer) {
		return 444;
	}
}
```


## Varnish: .refferal-spam.vcl

Add `referral-spam.vcl` to **Varnish 4** default file: `default.vcl` by adding the following code right underneath your default backend definitions

```conf
include "referral-spam.vcl";
sub vcl_recv { call block_referral_spam; }
```


## Internet Information Services (IIS): web.config 

The web.config file is located in the root directory of your Windows Server web application.


## Options for Google Analytics 'ghost' spam

The above methods don't stop the Google Analytics **ghost** referral spam (because they are hitting Analytics directly and don't touching your website). You should use filters in Analytics to prevent **ghost** referral spam. 


Navigate to your Google Analytics Admin panel and add a Segment:

Filter | Session | **Include**
------------ | ------------- | -------------
Hostname | matches regex | ```your-website\.com|www\.your-website\.com```

Filter | Session | **Exclude**
------------ | ------------- | -------------
Source | matches regex |Copy all the domains from [google-exclude.txt](https://raw.githubusercontent.com/Stevie-Ray/referrer-spam-blocker/master/google-exclude.txt) to this field

You can also prevent **ghost** referral spam by:

  * [Adding a filter](https://support.google.com/analytics/answer/1033162)
  * [Enabeling bot and Spider Filtering](https://plus.google.com/+GoogleAnalytics/posts/2tJ79CkfnZk) 

## Intregrate in a Dockerfile

You can also integrate these configuration file in your Docker repo, so you will get always the most updated version when you build your image.
For `Apache, Nginx, Varnish 4` or `IIS` add the following line to your `Dockerfile`

```conf
# Apache: Download referral-spam.conf to /usr/local/apache2/htdocs/
ADD https://raw.githubusercontent.com/Stevie-Ray/referrer-spam-blocker/master/.htaccess /usr/local/apache2/htdocs/
 
# Nginx: Download referral-spam.conf to /etc/nginx/
ADD https://raw.githubusercontent.com/Stevie-Ray/referrer-spam-blocker/master/referral-spam.conf /etc/nginx/
 
# Varnish 4: Download referral-spam.vcl to /etc/varnish/
ADD https://raw.githubusercontent.com/Stevie-Ray/referrer-spam-blocker/master/referral-spam.vcl /etc/varnish/
 
# IIS: Download web.config to /sitepath/ (change sitepath accordingly)
ADD https://raw.githubusercontent.com/Stevie-Ray/referrer-spam-blocker/master/web.config /sitepath/
```

## Like it?

- [Buy me a beer](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=4XC7KX75K6636) 🍺
