Installation instructions
=========================

This OpenERP install is based on [openerp.buildout](https://github.com/kdeldycke/openerp.buildout) and was performed on a Debian Squeeze 6.0.


Get openerp.buildout
--------------------

Get a local copy of the openerp.buildout boilerplate:

        $ git clone git://github.com/kdeldycke/openerp.buildout.git
        $ cd ./openerp.buildout

As the repository above is subject to changes, freeze its code to the following stable revision:

        $ git checkout c106f51f266da69cb2a1f6bcd217beda2095a30f


Generate our specific configuration
-----------------------------------

Create a `myproject.cfg` file and put there the following content:

        [buildout]
        extends =
            profiles/openerp-web-6.0.cfg
            profiles/aeroo.cfg


        [openerp]
        urls +=
            lp:openerp-spain/6.0 spain-addons
        server_addons +=
            spain-addons/*


Install OpenERP with buildout
-----------------------------

1. Install required dependencies with your favorite package installer.

        $ sudo apt-get update
        $ sudo apt-get install sudo python-dev gcc bzr subversion git postgresql libxslt1-dev libyaml-dev libjpeg62-dev zlib1g-dev libfreetype6-dev liblcms1-dev libxml2-dev libpq-dev

1. Initialize the buildout environment:

        $ python ./bootstrap.py --distribute

1. Run buildout itself:

        $ sudo ./bin/buildout -c ./myproject.cfg

1. Start the PostgreSQL server:

        $ sudo /etc/init.d/postgresql restart

1. Now you can launch the OpenERP server and web client:

        $ sudo /etc/init.d/openerp-server restart
        $ sudo /etc/init.d/openerp-web-client restart

1. To play with your OpenERP instance, all you have to do is to point your browser to:

        http://127.0.0.1:8080

1. And to monitor everything's all right, keep an eye on the logs:

        $ tail -F /var/log/openerp/openerp-*.log

1. Finally to let the server and the web client starts with the machine, do:

        $ sudo update-rc.d openerp-server defaults 90
        $ sudo update-rc.d openerp-web-client defaults 91


Command helpers
===============

Or how to make your daily usage of buildout painless.


### bin/buildout

This is the command to run to build a new environement.

This is also the command you call to update your environment.

In fact you can safely call it whenever you want. buildout implement an internal mecanism that keeps tracks of what it installed and configured before, that's why it's safe. And that's also why you can re-call it if it crashed in the middle of its execution.


### bin/openerp-server.sh & bin/openerp-web-client.sh

There are script helper available in `./bin` to let you launch OpenERP's server (`./bin/openerp-server.sh`) and web client (`./bin/openerp-web-client.sh`) easily with the right user and configuration.

For example, if you need to update all modules of a database, you can use the following command:

        $ ./bin/openerp-server.sh -d my_database_id -u all


### bin/openerp-shell

All those scripts are based on `./bin/openerp-shell` which is a simple wrapper around your system's Python interpreter, only it add under its scope all modules locally installed by this buildout. So if you're looking to use whatever Python module buildout install for you, forget about your system's Python interpreter and use `./bin/openerp-shell` instead, see:

        $ python
        Python 2.6.6 (r266:84292, Dec 26 2010, 22:31:48)
        [GCC 4.4.5] on linux2
        Type "help", "copyright", "credits" or "license" for more information.
        >>> import mako
        Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
        ImportError: No module named mako
        >>>

        $ ./bin/openerp-shell
        >>> import mako
        >>> repr(mako)
        "<module 'mako' from '/home/openerp.buildout/eggs/Mako-0.4.2-py2.6.egg/mako/__init__.pyc'>"
        >>>


Troubleshooting
===============

### Locale

You may encounter this issue when Bazaar commands are run:

        bzr: warning: unsupported locale setting
          bzr could not set the application locale.
          Although this should be no problem for bzr itself,
          it might cause problems with some plugins.
          To investigate the issue, look at the output
          of the locale(1p) tool available on POSIX systems.

This can be fixed by forcing your locale to more sane defaults.

Here is how I did it on Debian Squeeze ([source](http://wiki.debian.org/Locale)):

        $ echo "LANG=en_US.UTF-8" >> /etc/default/locale
        $ echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
        $ locale-gen

Then I had to log out and back in to have the environment variables set to the new defaults.

And here is another way of fixing this local issue (thanks Xavier for the tip !):

        $ apt-get install --reinstall language-pack-fr
        $ dpkg-reconfigure locales


### Proxy

Behind a proxy ? Just set the appropriate environment variables to let Subversion and Bazaar pass through it:

        $ export  http_proxy=http://10.0.2.1:8080
        $ export https_proxy=http://10.0.2.1:8080

Note that [some versions of Bazaar are affected by a bug](http://bugs.launchpad.net/bzr/+bug/558343) which doesn't let you pass a proxy. In my case, the bzr-2.1.2-1 package available on my Debian Squeeze was affected by such a bug. To fix this, I just applied the patch proposed at http://code.edge.launchpad.net/~lifeless/bzr/bug-558343-wrong-host-with-proxy/+merge/24938 .
