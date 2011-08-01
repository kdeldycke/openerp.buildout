openerp.buildout
================

This is a quick and dirty buildout to help me generate OpenERP instances for tests and development.

It is far from mature enough to be used in production, but feel free to send patches to help me improve this situation.

This was roughly tested with Ubuntu 11.04 and Debian Squeeze.

Other similar projects:

* http://github.com/kalymero/OpenERP-Buildout


Installation
------------

I know it's bad, but for convenience, all the commands below should be run as root.

1. Initialize the buildout environment:

        $ python ./bootstrap.py --distribute

1. Set the `extend` parameter of the `[buildout]` section of `buildout.cfg` to either `openerp-trunk.cfg` or `openerp-6.0.cfg` depending of the version of OpenERP you want to work with.

1. Run buildout itself:

        $ ./bin/buildout

1. Launch the servers:

        $ /etc/init.d/postgresql restart
        $ su openerp -c "./bin/openerp-shell ./parts/openerp/server/openerp-server" &
        $ su openerp -c "./bin/openerp-shell ./parts/openerp/web-client/openerp-web.py" &

1. Then with your browser, go to:

        http://127.0.0.1:8080
