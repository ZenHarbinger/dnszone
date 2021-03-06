dnszone
========

Overview
--------

This package is forked from [Easyzone](https://bitbucket.org/chrismiles/easyzone),
created by Chris Miles. I have forked it to include bugfixes for installation
and also to update the package for compatibility with Python 3.

DNSZone is a package to manage the common record types of a
zone file, including SOA records.  This module sits on top of
the dnspython package and provides a higher level abstraction
for common zone file manipulation use cases.

Main features:

* A high-level abstraction on top of dnspython.
* Load a zone file into objects.
* Modify/add/delete zone/record objects.
* Save back to zone file.
* Auto-update serial (if necessary).

Websites:
* http://pypi.python.org/pypi/dnszone
* https://github.com/greg-hellings/dnszone/


Requirements
------------

  * [dnspython](http://www.dnspython.org/)


Build/Test/Install
------------------

Build::

```bash
  $ python setup.py build
```

Test::

```bash
  $ python setup.py test
```

Install::

```bash
  $ python setup.py install
```


OR with setuptools::

```bash
  $ easy_install dnszone
```


Examples
--------

dnszone::

```
  >>> from dnszone import dnszone
  >>> z = dnszone.zone_from_file('example.com', '/var/namedb/example.com')
  >>> z.domain
  'example.com.'
  >>> z.root.soa.serial
  2007012902L
  >>> z.root.records('NS').items
  ['ns1.example.com.', 'ns2.example.com.']
  >>> z.root.records('MX').items
  [(10, 'mail.example.com.'), (20, 'mail2.example.com.')]
  >>> z.names['foo.example.com.'].records('A').items
  ['10.0.0.1']

  >>> ns = z.root.records('NS')
  >>> ns.add('ns3.example.com.')
  >>> ns.items
  ['ns1.example.com.', 'ns2.example.com.', 'ns3.example.com.']
  >>> ns.delete('ns2.example.com')
  >>> ns.items
  ['ns1.example.com.', 'ns3.example.com.']

  >>> z.save(autoserial=True)
```

ZoneCheck::

```
  >>> from dnszone.zone_check import ZoneCheck
  >>> c = ZoneCheck()
  >>> c.isValid('example.com', '/var/named/zones/example.com')
  True
  >>> c.isValid('foo.com', '/var/named/zones/example.com')
  False
  >>> c.error
  'Bad syntax'
  >>>
  >>> c = ZoneCheck(checkzone='/usr/sbin/named-checkzone')
  >>> c.isValid('example.com', '/var/named/zones/example.com')
  True
  >>>
```

ZoneReload::

```
  >>> from dnszone.zone_reload import ZoneReload
  >>> r = ZoneReload()
  >>> r.reload('example.com')
  zone reload up-to-date
  >>> r.reload('foo.com')
  rndc: 'reload' failed: not found
  Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    File "dnszone/zone_reload.py", line 51, in reload
      raise ZoneReloadError("rndc failed with return code %d" % r)
  dnszone.zone_reload.ZoneReloadError: rndc failed with return code 1
  >>>
  >>> r = ZoneReload(rndc='/usr/sbin/rndc')
  >>> r.reload('example.com')
  zone reload up-to-date
  >>>
```
