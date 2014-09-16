Nginx Google IP Ranges Updater
=============================

## Introduction ##

If you use [Google PageSpeed Service](https://developers.google.com/speed/pagespeed/service) or any reverse proxy in front of Nginx (Varnish cache, PageSpeed service, Cloud DDOS proxy, CloudFlare, etc.), you will need to use [Nginx's HttpRealIpModule](http://wiki.nginx.org/HttpRealIpModule) which should be compiled at the time of Nginx installation or you can rebuild if you don't have it installed already and set the `set_real_ip_from` and `real_ip_header` to properly allow Nginx to see the visitor's real IP address and not the IP address of the reverse proxy or Google PageSpeed Service IP addresses in this case.

Google also explains this in their [Google PageSpeed Service FAQ](https://developers.google.com/speed/pagespeed/service/faq#clientip). They don't provide a convenient list of IP addresses though, you have to do some work as explained in '[Google's IP addresses](http://support.google.com/a/bin/answer.py?hl=en&answer=60764)' page. When you run the `nslookup` commands on that page you get first the list of netblocks then the list of Google IP addresses from each of those netblocks. 

The IP addresses change from time to time. So, it’s vital to fetch and synchronise them. This script does exactly that and automates the process, so you can set a cron job and let it automatically pull all [Google IP ranges](https://support.google.com/a/answer/60764?hl=en) and update the file for you. Currently it fetches and updates both IPv4 and IPv6 Ranges.


## Requirements ##

* Nginx
* [HttpRealIpModule](http://wiki.nginx.org/HttpRealIpModule)
* bind-utils (CentOS for `nslookup`) - `yum -q -y install bind-utils`
* Nginx should be compiled with IPv6 Support (`--with-ipv6`) to get the IPv6 IPs working.

## Installation ##


Download:

```
$ wget -qO google-ip-ranges-updater.sh  http://lk.gd/1qaTM9R
```

Edit the file and update `GOOGLE_IP_RANGES_FILE_PATH` bash variable to the appropriate path where the IPs list should be saved. Generally not required. (Optional)


Set the script's file permissions to be executable by all users:

```
$ chmod a+x ./google-ip-ranges-updater.sh
```

Run:

```
$ ./google-ip-ranges-updater.sh
```

It should now automatically create/update `google_ips.conf` file at `/usr/local/nginx/conf/conf.d/google_ips.conf` (Default path).

## Cron ##

It is highly recommended to setup a cron job to automatically execute the script.

If you want to update the Google IP Ranges every `24:00 hours` as `root` user you should add the following at the end of your system's `crontab` file:

`/etc/crontab`:

```
# Google IP Ranges Automatic Updater
# Every 24:00 hours at 04:00am UTC
00 04 * * * root /path/to/google-ip-ranges-updater.sh >/dev/null 2>&1
```

It's also possible to set time intervals weekly or several times a month, a day or even hours:

```
# Every 30 minutes
*/30 * * * * root /path/to/google-ip-ranges-updater.sh >/dev/null 2>&1
# or
# Every 7 days at 02:30am UTC
30 02 1,8,15,22,28 * root /path/to/google-ip-ranges-updater.sh >/dev/null 2>&1
```

Finally, Save the file and restart the `cron` service:

```
$ service cron restart
```

## License ##

[MIT](http://opensource.org/licenses/MIT) &copy; [Syed I.R](https://github.com/irazasyed)

## Contributions ##

Contributions are welcome!
