# pcfu

Simple POSIX shell Cloudflare IP address updater

This is my solution to DDNS, just like, without using a super bloated DDNS client.

# Dependencies

* any POSIX-capable shell
* `curl`
* `dig` (from bind-tools / bind-utils)
* `cat` and `sed`.

# Usage

* Update a record using flags:
  `pcfu -z <zone_id> -k <api_token> -t 1 -i https://ip.kris.sh -f /var/cache/pcfu.ip -d kris.sh -p false [-r 1.1.1.1]`
  
* Update a record using a config file:
  `pcfu -c /etc/pcfu.conf`
  
* Request help output:
  `pcfu`, `pcfu help`, any invocation with no flags.

# Installation

Put the `pcfu` script in `$PATH` somewhere. On Linux, a good location tends to be `/usr/local/bin`.

# Options

* `-c` - Specifies a config file that contains all of the other options.
  If this is provided and the given config file is valid, none of the other options are necessary.
  If not provided, all of the other options are **required.**
  
* `-z` - Specifies the Cloudflare Zone ID.
  (Config variable: `zone_id`)
  
* `-k` - Specifies the Cloudflare API token for the given domain.
  For this to work, you must have a Cloudflare API token with `ZONE_DNS_EDIT` permissions.
  (Config variable: `cloudflare_api_key`)
  
* `-t` - Specifies the domain TTL, a value of `1` will represent Auto
  if you do not want to give the domain a specific TTL.
  (Config variable: `ttl`)
  
* `-i` - Specifies the IP provider you wish to use to get your current IP. In order for this to work, curling the IP provider URL
  must return only the IP address. Valid options, for example, may be one of:
  - `https://icanhazip.com`
  - `https://ip.kris.sh`
  (Config variable: `ip_provider`)
  
* `-f` - Specifies the IP address cache file location. This is required so the script can log the current IP
  and later check it for changes, as opposed to sending a request for this on every invocation.
  (Config variable: `cache_file`)
  
* `-d` - Specifies the domain name you wish to update. Example: `kris.sh`.
  (Config variable: `domain_name`)
  
* `-r` - Specifies the resolver to use to check the current IP address assignment of a given domain.
  This defaults to Cloudflare (1.1.1.1) but can be changed to any other DNS server.
  (Config variable: `resolver_address`)
  
* `-p` - Specifies whether or not the domain should have Cloudflare proxying enabled.
  Valid values are `true` or `false`.
  (Config variable: `proxied`)

The order these options are set in does not matter.

# Config File

If you would like to use a config file as opposed to setting your options via the flags,
you may instead provide a path to a config file as a flag instead (see opt -c).
     
This config file should contain the various variables above, set in a shell script compliant way.
Upon launch, pcfu will attempt to source the supplied config file.

Example:

```sh
zone_id='your_zone_id'
cloudflare_api_key='your_api_token_here'
ttl='1'
ip_provider='https://ip.kris.sh'
cache_file='/var/cache/pcfu.ip'
domain_name='kris.sh'
resolver_address='1.1.1.1'
proxied='false'
```

# Cloudflare details
* Where do I get my `zone_id`?

As of the time of writing, go to your domain on the Cloudflare web panel, scroll down, and look for an "API" section with a "Zone ID" value.

* Where do I get my `cloudflare_api_key`?

In the same section as above, click `Get your API token` and create one.
There is an API token template called "Edit zone DNS," which is what you want.
I recommend scoping your token to just the domain you intend on updating with this.
