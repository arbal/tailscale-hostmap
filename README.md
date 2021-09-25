# tailscale-hostmap

Automatically map Tailscale hosts to DNS names in `/etc/hosts`.

This script modifies your system's hosts file to include all your Tailscale machines, allowing you to e.g. ssh into them as if they were on your local network (with optional domain suffix).

Running it again will update the existing configuration. Designed to take 10 seconds to setup.

**Q: Isn't this similar to Tailscale's own [Magic DNS](https://tailscale.com/kb/1081/magicdns/)?**  
A: Yes, but:
- has some extra flexibility, as you can also add DNS entries for shared machines, and you can specify a DNS domain to use (e.g. a host `raspberrypi` can become `raspberrypi.ts`)
- Is faster to resolve - Magic DNS is slow in my experience
- Can be enabled for just one or two machines, instead of your entire network.

## 10-second Setup

```bash
wget https://raw.githubusercontent.com/mxbi/tailscale-hostmap/main/tailscale-hostmap.py
echo "*/5 * * * * /usr/bin/python3 `pwd`/tailscale-hostmap.py --domain ts -s" | sudo tee /etc/cron.d/tailscale-hostmap
```

The configuration above includes shared machines, and adds a `.ts` domain to hostnames. So if a host on your network is called `lynx`, you'll be able to `ssh lynx.ts`. The configuration will be updated every 5 minutes.

## Usage/Installation

To update entries e.g. every 5 minutes, download `tailscale-hostmap.py` and add it as a cronjob, for example by creating a file in `/etc/cron.d` and adding a new line:

```bash
*/5 * * * * /usr/bin/python3 PATH_TO_FILE/tailscale-hostmap.py --domain ts -s
```

You can use [a crontab generator](https://crontab-generator.org/) to help with this!

There are some optional flags:

```
usage: tailscale-hostmap.py [-h] [--domain DOMAIN] [-s]
                            [--ts-binary TS_BINARY]

optional arguments:
  -h, --help            show this help message and exit
  --domain DOMAIN       The domain to append to the hostname. For example,
                        `pi` becomes `pi.ts` when domain=`ts`. Defaults to no
                        domain
  -s, --include-shared  Add this flag to also include shared machines in DNS
  --ts-binary TS_BINARY
                        The location of the tailscale binary to call. Defaults
                        to /usr/bin/tailscale
```

Note that the script needs to run with sudo as `/etc/hosts` is a priveliged file. No requirements needed except Python3.