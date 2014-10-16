=======================
Configuring a web proxy
=======================

:Authors: `Michael JasonSmith`_; `Fabien Hespul`_
:Contact: Michael JasonSmith <mpj17@onlinegroups.net>
:Date: 2014-06-23
:Organization: `GroupServer.org`_
:Copyright: This document is licensed under a
  `Creative Commons Attribution-Share Alike 4.0 International License`_
  by `OnlineGroups.net`_.

.. highlight:: console

Introduction
============

While GroupServer_ *can* run as a stand-alone web-server, it is
**highly recommended** that a proxy is used when making the site
available to the public to provide the following services:

* To mediate between the low level HTTP port (port 80) and the
  high-port that Zope runs on (normally port 8080).
* To rewrite the URL to include a skin directive.
* To rewrite the URL to support virtual hosting.
* To provide caching.

In this document we explain how to `add a virtual host`_ to
either Apache_ or nginx_. Then we explain how to `change the
skin`_.

Add a virtual host
==================

If you have a new domain [#domain]_ that you want to use with
your GroupServer installation first you must `update the
GroupServer configuration`_ and then `add a virtual host to
Apache`_ or `Add a virtual host to nginx`_.

Update the GroupServer configuration
------------------------------------

If you used a host such as ``gstest`` to try out GroupServer then
you will need to update the configuration for GroupServer itself.

#.  Visit the ZMI for your site. Log in if necessary.

#.  Visit the folder ``/groupserver/Content/initial_site/``.

#.  Open the ``DivisionConfiguration``.

#.  Set the ``canonicalHost`` to the domain for your new site.

#.  Set the ``canonicalPort`` to ``80``.

#.  Click the ``Save Changes`` button.

Add a virtual host to Apache
----------------------------

To add a virtual host to Apache carry out the following steps.

#.  Ensure the ``rewrite``, ``proxy``, and ``proxy_httpd``
    modules are enabled in Apache::

      # a2enmod rewrite proxy proxy_http
      # service apache2 restart

#.  Open ``/etc/apache2/sites-available/groupserver`` in a
    text-editor.


#.  Add the following to the file

    .. code-block:: apacheconf

      <VirtualHost *:80>
        ServerAdmin support@example.com
        ServerName groups.example.com

        RewriteEngine on
        RewriteRule ^/(.*) http://localhost:8080/groupserver/Content/initial_site/VirtualHostBase/http/%{HTTP_HOST}:80/VirtualHostRoot/$1 [L,P]

        ProxyVia On

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel info

        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

    * Change the address for the site from ``groups.example.com``
      to that of you new virtual host.

    * Change the email address for ``ServerAdmin`` from
      ``support@example.com`` to the value of the
      ``support_email`` in the ``config.cfg`` file in the
      GroupServer directory.

#.  Link the configuration for your host::

    # cd /etc/apache2/sites-enabled/
    # ln -s ../groupserver 100-groupserver 

#.  Restart Apache::

     # service apache2 restart

Add a virtual host to nginx
---------------------------

Open ``/etc/nginx/sites-avaliable/groupserver`` in a text-editor.

#.  Add the following to the file

    .. code-block:: nginx

      upstream gs {
        server localhost:8080;
      }

      server {
        listen 80;
        server_name groups.example.com;

        location / {
          rewrite /(.*) /VirtualHostBase/http/$host:80/groupserver/Content/initial_site/VirtualHostRoot/$1 break;
          proxy_pass http://gs/;
          include proxy_params;
        }
      }

      server {
        listen 80;
        server_name zmi.groups.example.com;

        location / {
          rewrite /(.*) /VirtualHostBase/http/$host:80/VirtualHostRoot/$1 break;
          proxy_pass http://gs/;
          include proxy_params;
        }
      }

    * Change the ``server_name`` from ``groups.example.com`` to
      that of you new virtual host. 

    * Make a similar change to the second server, keeping the
      ``zmi.`` at the start.

#.  Link the configuration for your host::

    # cd /etc/nginx/sites-enabled/
    # ln -s 100-groupserver ../groupserver

#.  Reload the nginx configuration::

    # service nginx reload

Change the skin
===============

One of the advantages of adding a proxy is it allows the skin to
be easily changed. GroupServer ships with two skins: green and
blue. To change the skin you must alter the rewrite rule. In the
case of nginx the rewrite rule will look like the following

.. code-block:: nginx

  rewrite /(.*) /++skin++blue/VirtualHostBase/http/$host:80/groupserver/Content/initial_site/VirtualHostRoot/$1 break;

In the case of Apache the rewrite rule would look like the following

.. code-block:: apache

  RewriteRule ^/(.*) http://localhost:8080/++skin++green/groupserver/Content/initial_site/VirtualHostBase/http/%{HTTP_HOST}:80/VirtualHostRoot/$1 [L,P]


.. [#domain] Acquiring and configuring a new domain is out of the
             scope for this documentation. However, you want the
             A-record for your new domain to point to the IP of
             your GroupServer site, and the MX-record to also
             point at your new site.

.. [#port] Leave the port set to ``8080``.

..  _GroupServer: http://groupserver.org/
..  _GroupServer.org: http://groupserver.org/
..  _OnlineGroups.net: https://onlinegroups.net/
..  _Apache: http://httpd.apache.org/
..  _nginx: http://nginx.org/
..  _Michael JasonSmith: http://groupserver.org/p/mpj17
..  _Creative Commons Attribution-Share Alike 4.0 International License:
    http://creativecommons.org/licenses/by-sa/4.0/
..  _Fabien Hespul: http://groupserver.org/p/1e38zikXDqFgXFkmCjqC31