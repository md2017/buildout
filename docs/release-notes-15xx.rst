===============================================
GroupServer 15.xx — Rakı with an eye on history
===============================================

:Authors: `Michael JasonSmith`_;
:Contact: Michael JasonSmith <mpj17@onlinegroups.net>
:Date: 2015-mm-dd
:Organization: `GroupServer.org`_
:Copyright: This document is licensed under a
  `Creative Commons Attribution-Share Alike 4.0 International
  License`_ by `OnlineGroups.net`_.

.. highlight:: console

------------
Introduction
------------

The major `changes to GroupServer`_ in the Rakı release is the
rebuilt email processing sub-system.  You can `get Rakı`_
immediately.

----------------------
Changes to GroupServer
----------------------

The most extensive change in Rakı is the new `email processing`_
subsystem. There have been some `minor code improvements`_.

Email processing
================

The components that process email coming into a GroupServer
group, and sends email from the group, have been completely
changed. This update closes `Feature 387`_.

.. _Feature 387: https://redmine.iopen.net/issues/387

**Receiving email** is now handled by code that has been
thoroughly unit-tested to ensure that it works with multiple
character-sets and encodings. The **checks** to see if a message
should be processed have been refactored and are now much more
*extensible*, less *coupled*, and more *cohesive*, thanks to
`Bill Bushey`_.

The system for **building the email message** before it is sent
has been totally rebuilt. Now the message is created using
page-templates. This allows the techniques used to customise web
pages in GroupServer to be used to customise email messages,
including the *prologue* that appears at the top of every
message, and at the bottom the *file notifications* and the
*footer*.

The **headers** have been refactored to be more extensible. It is
now easy to add to the headers, and to change the behaviour of a
header. The ``Reply-to`` header, in particular, has changed to
allow the default behaviour of a group to change between

* Reply to the group,
* Reply to the original author, and
* Reply to both.

Finally, the **sending** of an email from a group has been
tweaked. However, there *should* be no noticeable change.

Minor code improvements
=======================

* The XML for the *Email settings* page has been updated
* The CSS that is used in the notifications has been fixed

--------
Get Rakı
--------

To get Rakı go to `the Downloads page for GroupServer`_ and
follow `the GroupServer Installation documentation`_. Those who
already have a functioning installation can `update an existing
GroupServer system`_.

..  _The Downloads page for GroupServer: http://groupserver.org/downloads
..  _The GroupServer Installation documentation: 
    http://groupserver.readthedocs.org/

Update an Existing GroupServer System
=====================================

To update a system running the Calvados release of GroupServer
(14.11) to Rakı (15.xx) carry out the following steps.

#.  Download the Calvados tar-ball from `the GroupServer
    download page <http://groupserver.org/downloads>`_.

#.  Uncompress the tar-ball::

      $ tar cfz groupserver-15.xx.tar.gz

#.  Change to the directory that contains your existing
    GroupServer installation.

#.  Copy the new version-configuration files to your existing
    GroupServer installation::

      $ cp ../groupserver-15.xx/[biv]*cfg  .

#.  In your existing GroupServer installation run::

      $ ./bin/buildout -n

#.  Restart your GroupServer instance.

---------
Resources
---------

- Code repository: https://github.com/groupserver/
- Questions and comments to http://groupserver.org/groups/development
- Report bugs at https://redmine.iopen.net/projects/groupserver

..  _GroupServer: http://groupserver.org/
..  _GroupServer.org: http://groupserver.org/
..  _OnlineGroups.Net: https://onlinegroups.net/
..  _Creative Commons Attribution-Share Alike 4.0 International License:
    http://creativecommons.org/licenses/by-sa/4.0/
..  _Michael JasonSmith: http://groupserver.org/p/mpj17
..  _Dan Randow: http://groupserver.org/p/danr
..  _Bill Bushey: http://groupserver.org/p/wbushey
..  _Alice Murphy: http://groupserver.org/p/alice
..  _E-Democracy.org: http://forums.e-democracy.org/

..  LocalWords:  refactored iopen JPEG redmine jQuery jquery async Rakı Bushey
..  LocalWords:  Randow Organization sectnum Slivovica DMARC CSS Calvados
..  LocalWords:  SMTP smtp