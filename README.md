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

1. Start the PostgreSQL server:

        $ /etc/init.d/postgresql restart

1. Optionnaly, you can update all OpenERP modules:

        $ su openerp -c "./bin/openerp-shell ./parts/openerp/server/bin/openerp-server.py --addons-path=./parts/openerp/server/bin/addons --debug --stop-after-init -u all"

1. Then you can launch the OpenERP server in the background:

        $ su openerp -c "./bin/openerp-shell ./parts/openerp/server/bin/openerp-server.py --addons-path=./parts/openerp/server/bin/addons --debug" &

1. Now you can run the web client the same way:

        $ su openerp -c "./bin/openerp-shell ./parts/openerp/web-client/openerp-web.py" &

1. To play with your OpenERP instance, all you have to do is to point your browser to:

        http://127.0.0.1:8080

1. Finally, if you want to kill OpenERP's server and client processes, you may use the command below:

        $ kill `ps -ef | grep openerp-shell | awk '{print $2}'` 


Proxy
-----

Behind a proxy ? Just set the appropriate environment variables to let Subversion and Bazaar pass through it:

        $ export  http_proxy=http://10.0.2.1:8080
        $ export https_proxy=http://10.0.2.1:8080

Note that some versions of Bazaar are affected by a bug ( http://bugs.launchpad.net/bzr/+bug/558343 ) which doesn't let you pass a proxy. In my case, the bzr-2.1.2-1 package available on my Debian Squeeze was affected by such a bug. To fix this, I just applied the patch proposed at http://code.edge.launchpad.net/~lifeless/bzr/bug-558343-wrong-host-with-proxy/+merge/24938 .


