# mod_vhost_maxclients  [![Build Status](https://travis-ci.org/matsumotory/mod_vhost_maxclients.svg?branch=master)](https://travis-ci.org/matsumotory/mod_vhost_maxclients)

mod_vhost_maxclinets provides the feature of `MaxClients` per vhost, no using shared memory and global lock. This module use only scoreboad for stability(no dead-lock), high compatibility and simple implementation.

It supports Apache httpd __2.2.x and 2.4.x__, and __worker, prefork and event mpm__.

If you have a performance issue using mod_vhost_maxclients like over-head of serving static contents, You should use `IgnoreVhostMaxClientsExt` for ignoring the static contents.

__Notice:__ vhost length in scoreboard is 32 bytes. it's versy short. If you control more length of vhost name by mod_vhost_maxclients, you should apply a patch like [example for httpd 2.4.x](https://gist.github.com/matsumoto/1ef9b14ac5908ebfb4f1).

# Quick Install
- build

```
make
make install
```

- conf.d/mod_vhost_maxclients.conf

```apache
LoadModule vhost_maxclients_module modules/mod_vhost_maxclients.so
# mod_vhost_maxclinets use scoreboard
ExtendedStatus On
include /path/to/vhost.conf
```

- /path/to/vhost.conf

```apache
# separate vhost maxclients
# VhostMaxClientsLogPath /usr/local/apache/logs/vhost_maxclients.log

<VirtualHost *>
    DocumentRoot /path/to/web
    ServerName test001.example.jp

    # MaxClients per vhost using mod_vhost_maxclients
    VhostMaxClients 30
    # Ignore extensions from VhostMaxClients for a performance issue
    # IgnoreVhostMaxClientsExt .html .js .css

    # MaxClinets per client ip-address to the same vhost
    # VhostMaxClientsPerIP 5

    # dry-run option which don't return 503, logging only
    # VhostMaxClientsDryRun On

    # logging only option which don't return 503, logging only
    # VhostMaxClientsLogOnly 10
    
    # Time to enable the VhostMaxClients from 17:00 to 23:00 
    # VhostMaxClientsTimeSlot 1700 2300

    # Ignore Requests if it match regular expression.
    # IgnoreVhostMaxClientsRequestRegexp '[jpg|gif|png] HTTP/1.[01]$'


</VirtualHost>
```

- logggin
```
[Thu Aug 27 21:16:54.540685 2015] [vhost_maxclients:debug] [pid 23981] mod_vhost_maxclients.c(93): DEBUG: (increment test001.example.jp:80): 1/3
[Thu Aug 27 21:16:54.540708 2015] [vhost_maxclients:debug] [pid 23981] mod_vhost_maxclients.c(93): DEBUG: (increment test001.example.jp:80): 2/3
[Thu Aug 27 21:16:54.540713 2015] [vhost_maxclients:debug] [pid 23981] mod_vhost_maxclients.c(93): DEBUG: (increment test001.example.jp:80): 3/3
[Thu Aug 27 21:16:54.540716 2015] [vhost_maxclients:debug] [pid 23981] mod_vhost_maxclients.c(93): DEBUG: (increment test001.example.jp:80): 4/3
[Thu Aug 27 21:16:54.540726 2015] [vhost_maxclients:notice] [pid 23981] NOTICE: (return 503 from test001.example.jp:80): 4/3
```

# License
under the MIT License:

* http://www.opensource.org/licenses/mit-license.php

