openerp.buildout
================

This is a buildout-based project to generate OpenERP instances for tests and development.

It is far from mature enough to be used in production, but feel free to send patches to help me improve this situation.


Install OpenERP on a local machine
----------------------------------

This was roughly tested with Ubuntu 11.04 and Debian Squeeze.

1. Install required dependencies with your favorite package installer.

    * Here is for Debian Squeeze:

            $ sudo apt-get update
            $ sudo apt-get install sudo python-dev gcc bzr subversion postgresql libyaml-dev libjpeg62-dev zlib1g-dev libfreetype6-dev liblcms1-dev libxml2-dev libxslt1-dev libpq-dev

1. Initialize the buildout environment:

        $ python ./bootstrap.py --distribute

1. Set the `extend` parameter of the `[buildout]` section of `buildout.cfg` to either `profiles/openerp-trunk.cfg` or `profiles/openerp-6.0.cfg` depending of the version of OpenERP you want to work with.

1. Run buildout itself:

        $ ./bin/buildout

1. Start the PostgreSQL server:

        $ sudo /etc/init.d/postgresql restart

1. Now you can launch the OpenERP server in the background:

        $ su openerp -c "./bin/openerp-shell ./parts/openerp/server/bin/openerp-server.py --config=./parts/openerp_server.conf" &

1. Then you can run the web client the same way:

        $ su openerp -c "./bin/openerp-shell ./parts/openerp/web-client/openerp-web.py" &

1. To play with your OpenERP instance, all you have to do is to point your browser to:

        http://127.0.0.1:8080

1. Finally, if you want to kill OpenERP's server and client processes, you may use the command below:

        $ kill `ps -ef | grep openerp-shell | awk '{print $2}'`


Install OpenERP in the cloud
----------------------------

TODO: use [hostout](http://pypi.python.org/pypi/collective.hostout) to create a fully-working OpenERP instance in the cloud from scratch. See: http://www.slideshare.net/djay/opps-i-deployed-it-again


Proxy
-----

Behind a proxy ? Just set the appropriate environment variables to let Subversion and Bazaar pass through it:

        $ export  http_proxy=http://10.0.2.1:8080
        $ export https_proxy=http://10.0.2.1:8080

Note that some versions of Bazaar are affected by a bug ( http://bugs.launchpad.net/bzr/+bug/558343 ) which doesn't let you pass a proxy. In my case, the bzr-2.1.2-1 package available on my Debian Squeeze was affected by such a bug. To fix this, I just applied the patch proposed at http://code.edge.launchpad.net/~lifeless/bzr/bug-558343-wrong-host-with-proxy/+merge/24938 .


Locale
------

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


Apache
------

If you want to serve OpenERP web client through Apache, a configuration file can be automaticcaly generated. Just add `profiles/apache.cfg` to the `extend` parameter of the `[buildout]` section of `buildout.cfg`.

As this add some caching, you need to activate the appropriate modules:

        $ sudo a2enmod cache disk_cache mem_cache expires

The apache config file is generated in `./parts/apache.conf` and can be activated right away:

        $ sudo ln -s ./parts/apache.conf /etc/apache2/sites-enabled/
        $ sudo /etc/init.d/apache2 reload


TODO
----

  * Automate Apache steps above ?
  * Add shortcuts in `./bin` to launch the OpenERP server and web client
  * Group all system initialization commands in one separate buildout config file. Basically eveything that has to be run as `root` will be moved there. The new `buildout.cfg` will then be able to be run without `root` privileges.
  * Auto-update all modules of all databases on update.
  * Don't call the patch command directly, use http://pypi.python.org/pypi/collective.recipe.patch
  * Consider using and contributing to http://pypi.python.org/pypi/anybox.recipe.openerp
  * Get inspiration from http://github.com/kalymero/OpenERP-Buildout ?
  * Auto-trigger the `pg_createcluster 8.4 main --start` command to fix broken PostgreSQL bad installs ?
  * Automate locale fix ?


Author
------

 * [Kevin Deldycke](http://kevin.deldycke.com) - `kevin@deldycke.com`


Contributors
------------

 * [Xavier Fernandez](http://twitter.com/#!/xavierfernandez)


License
-------

This code is free software: you can redistribute it and/or modify it under the
terms of the GNU General Public License as published by the Free Software
Foundation, version 2.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the GNU General Public License for more details.

For full details, please see the file named COPYING in the top directory of the
source tree. You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.


Embedded external projects
--------------------------

This tool uses external softwares, scripts, libraries and artworks:

        Buildout's bootstrap.py
        Copyright (c) 2006, Zope Corporation and Contributors
        Distributed under the Zope Public License, version 2.1 (ZPL).
        Source: http://svn.zope.org/repos/main/zc.buildout/trunk/bootstrap/bootstrap.py
