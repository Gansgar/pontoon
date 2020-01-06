# Pontoon &mdash; Mozilla's Localization Platform

Pontoon is a translation management system used and developed by the
[Mozilla localization community](https://pontoon.mozilla.org/). It
specializes in open source localization that is driven by the community and
uses version-control systems for storing translations.

Check out our documentation to learn how to:

* [Localize with Pontoon](https://mozilla-l10n.github.io/localizer-documentation/tools/pontoon/)
* [Make your projects localizable with Pontoon](https://mozilla-pontoon.readthedocs.io/en/latest/user/localizing-your-projects.html)

## Installation

Our [installation documentation](https://mozilla-pontoon.readthedocs.io/) is available on `Read the Docs`:

* For local development, see [developer setup](http://mozilla-pontoon.readthedocs.io/en/latest/dev/setup.html) using `Docker`
* For production installation, see [deployment documentation](http://mozilla-pontoon.readthedocs.io/en/latest/admin/deployment.html)
* For quick and easy deployment on the `Heroku` platform, click this button:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

## Contributing

Do you want to help us make Pontoon better? We are very glad!

Start by [installing Pontoon locally using Docker](https://mozilla-pontoon.readthedocs.io/en/latest/dev/setup.html). Once you have a working local environment, take a look at our [mentored bugs](https://wiki.mozilla.org/Webdev/GetInvolved/pontoon.mozilla.org). It is often better to start with those bugs, as they tend to be easier, and someone is committed to helping you get it done. To start working on a bug, verify that it isn't already assigned to someone else, and then ask on the bug for it to be assigned to you.

Before you start writing code, make sure to read our [contribution docs](https://mozilla-pontoon.readthedocs.io/en/latest/dev/contributing.html). It contains information on how to style code, how to run tests, how to name your commits, etc. All the things you need to know if you want your work to be merged into Pontoon!

Pontoon developers hang around in the `#pontoon` channel on [Mozilla's IRC server](https://wiki.mozilla.org/IRC). Please [join us there](https://cbe001.chat.mibbit.com/?url=irc:%2F%2Firc.mozilla.org%2Fpontoon) if you want to ask questions!

If you want to go further, you can:

* Check out development roadmap on the [wiki](http://wiki.mozilla.org/Pontoon)
* File a [bug](https://bugzilla.mozilla.org/enter_bug.cgi?product=Webtools&component=Pontoon&rep_platform=all&op_sys=all)
* Check [existing bugs](https://bugzilla.mozilla.org/buglist.cgi?product=Webtools&component=Pontoon&resolution=---&list_id=13740920)

## Servers (used for Mozilla projects only)

* [Staging](https://mozilla-pontoon-staging.herokuapp.com/)
* [Production](https://pontoon.mozilla.org/)

## License

This software is licensed under the
[New BSD License](http://creativecommons.org/licenses/BSD/). For more
information, read [LICENSE](https://github.com/mozilla/pontoon/blob/master/LICENSE).


# Custom stuff

## Running the repo

To get it running on docker follow these steps:
  1. setup your ssh 
    - use `ssh-keyscan -H HOST >> ssh/known_hosts` for known hosts
    - add your keys
    - setup `ssh_config`
  2. create folder `data` (location of Postgres database)
  3. configure ports in `docker-compose.yml` 
    - currently 35146 for django server and 35147 for webpack (default is 8000 / 3000)
    - I use it in combination with reverse proxy to resolve the ports
  4. configure your environments in `~/docker/config/webapp.env`
    - important are `SECRET_KEY` and `SITE_URL`
    - see [mozilla docs](https://mozilla-pontoon.readthedocs.io/en/latest/admin/deployment.html#environment-variables) for reference (some keys don't work like sshkeys or sshconfig)
  5. set your python version in `./.env` file 
    - e.g. `PYTHON_VERSION=3.7.5`
  6. use `docker-compose up` to build and start server

## Combine it with reverse proxy

Example nginx config
```
server {
  listen 80;
  listen [::]:80;

  server_name pontoon.webpage.de;

  location / {  # for django server
    proxy_pass http://localhost:35146;
  }

  location /sockjs-node {  # for websocket connection
    proxy_pass http://localhost:35147/sockjs-node;
    proxy_http_version 1.1;

    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }
}
```
