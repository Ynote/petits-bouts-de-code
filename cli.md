---
title: CLI
---
Pour les configurations générales de mes environnements, j'ai un dépôt Git dédié appelé [dotfiles](https://gitlab.com/ynote_hk/dotfiles/-/tree/main/).

## Apache

### Activer un site

```
$ sudo -s                    # enable sudo on current user
$ a2ensite [conf-name]       # enable site that are in sites-available
$ a2dissite [conf-name]      # disable site that are in sites-available
$ /etc/init.d/apache2 reload # reload Apache
```