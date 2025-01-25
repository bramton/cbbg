---
title: "FreeBSD TiddlyWiki rc.d script"
date: 2024-10-02T09:20:51+02:00
draft: false
tags: [FreeBSD, TiddlyWiki]
---

After manually starting a TiddlyWiki node.js server in a tmux environment for many years at each reboot of my system, I decided it was finally time to create a fancy rc.d for this. TiddlyWiki can be installed using `npm install -g tiddlywiki`.

# The rc.d script
The script should be placed in `/usr/local/etc/rc.d`.
``` sh
#!/bin/sh

# PROVIDE: tiddlywiki
# REQUIRE: LOGIN
# KEYWORD: shutdown

. /etc/rc.subr
load_rc_config $name

: ${tiddlywiki_enable:=no}
: ${tiddlywiki_user:="www"}
: ${tiddlywiki_group:="www"}
: ${tiddlywiki_port:="8080"}

name="tiddlywiki"
desc="TiddlyWiki server"
rcvar="tiddlywiki_enable"
command="/usr/local/bin/${name}"
procname="node"
pidfile="/var/run/${name}.pid"
command_args="${tiddlywiki_dir} --listen port=${tiddlywiki_port}"
start_precmd="tiddlywiki_precmd"
start_cmd="daemon -u ${tiddlywiki_user} -p ${pidfile} -f \
           ${command} ${command_args} ${tiddlywiki_flags}"

PATH="${PATH}:/usr/local/bin" # Otherwise node not found

tiddlywiki_precmd()
{
  if [ ! -d ${tiddlywiki_dir} ]; then
    info "TiddlyWiki dir does not exist. Creating one ..."
    if ! mkdir -p ${tiddlywiki_dir}; then
      err 1 "Failed to create TiddlyWiki dir."
    fi

    chown ${tiddlywiki_user}:${tiddlywiki_group} ${tiddlywiki_dir}
  fi

  if [ ! -f ${tiddlywiki_dir}/tiddlywiki.info ]; then
    info "No tiddlywiki.info found. Creating one ..."
    su -m ${tiddlywiki_user} -c "${command} ${tiddlywiki_dir} --init server"
  fi
}

run_rc_command "$1"
```

The `procname` variable is defined here, to aid the `check_pidfile` to find the correct process. If `procname` is not defined it defaults to `command`. To give an impression, this is how the process look like in the `ps` output:

```
USER  PID  COMMAND
root 82491 daemon: /usr/local/bin/tiddlywiki[82492] (daemon)
www  82492 node /usr/local/bin/tiddlywiki /var/run/www/twbram --listen port=8080
```

The `/var/run/tiddlywiki.pid` file will contain the value 82492, because the daemon was started with the lowercase `p` flag. If this process is killed, also automatically the parent daemon process is killed.

# How to use it
The system `/etc/rc.conf` can be used to automatically start the TiddlyWiki server at boot time. The following lines can be added to this file:
```
tiddlywiki_enable="YES"
tiddlywiki_dir="/var/run/www/twbram"
tiddlywiki_flags='root-tiddler=$:/core/save/lazy-images'
```

The `tiddlywiki_flags` line is optional, but highly recommended. This flag enables lazy loading of the images. Only when required the images will be retreived from the server. By default all images are also transmitted when viewing the wiki in the browser.

# Resources
The official FreeBSD documentation website has a nice article on [Practical rc.d scripting in BSD](https://docs.freebsd.org/en/articles/rc-scripting/). Furthermore the man pages are a great source of information as usual. Check out the man pages for [rc(8)](https://man.freebsd.org/cgi/man.cgi?query=rc&sektion=8&format=html) and [rc.subr(8)](https://man.freebsd.org/cgi/man.cgi?query=rc.subr&sektion=8&format=html) for instance.
