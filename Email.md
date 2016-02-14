---
title: Email
permalink: /Email/
---

sometimes it is nice to send emails directly from a server. this setup here uses postfix in a setup similar to ssmtp.

sending emails directly
-----------------------

      services.postfix = {
        enable = true;
        setSendmail = true;
      };

testing the setup
-----------------

    sendemail example@example.org
    foo
    ctrl+d

see the logfile
---------------

    journalctl -f -u postfix.service
    Mar 07 01:40:23 lenovo-t530 postfix/pickup[13926]: 92EA5314072: uid=0 from=<root>
    Mar 07 01:40:23 lenovo-t530 postfix/cleanup[14934]: 92EA5314072: message-id=<20150307004023.92EA5314072@lenovo-t530.localdomain>
    Mar 07 01:40:23 lenovo-t530 postfix/qmgr[13927]: 92EA5314072: from=<root@lenovo-t530.localdomain>, size=301, nrcpt=1 (queue active)
    Mar 07 01:40:23 lenovo-t530 postfix/smtp[14806]: 92EA5314072: to=<example@example.org>, relay=mx.example.org[17.14.16.25]:25, delay=22, delays=22/0/0.11/0.16, dsn=2.0.0, status=sent (250 2.0.0 Ok: queued as BE336802DB)
    Mar 07 01:40:23 lenovo-t530 postfix/qmgr[13927]: 92EA5314072: removed