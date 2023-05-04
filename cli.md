---
title: CLI
---
## Apache

### Activer un site

```
$ sudo -s                           # enable sudo on current user
$ a2ensite [conf-name]        # enable site that are in sites-available
$ a2dissite [conf-name]       # disable site that are in sites-available
$ /etc/init.d/apache2 reload # reload Apache
```