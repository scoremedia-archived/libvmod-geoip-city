============
vmod_example
============

----------------------
Varnish Example Module
----------------------

:Author: Mina Smart
:Date: 2014-09-24
:Version: 1.0
:Manual section: 3

SYNOPSIS
========

import geoip_city;

DESCRIPTION
===========

This vmod uses the GeoIPCity api provided by libgeoip. You must have the GeoIP
City database (free or paid) installed in order for this module to work. Using
the city api provides much more complete information about IP location over the
base GeoIP api.

FUNCTIONS
=========

locate
------

Prototype
        ::

                locate(STRING IP)
Return value
        VOID
Description
        Call this before any other geoip_city methods. It querys the GeoIP
        database and attaches the location record to the requests thread.
Example
        ::

                geoip_city.locate("123.123.123.123");

locate_ip
---------

Prototype
        ::

                locate_ip(IP IP)
Return value
        VOID
Description
        Provides the same function as the locate method above, but allows you to
        pass a varnish IP struct to the method.
Example
        ::

                geoip_city.locate_ip(client.ip);

clean_up
--------

Prototype
        ::

                clean_up(VOID)
Return value
        VOID
Description
        Call this method after you've saved all of the location information you
        need. It frees the memory used by the location record from the threads
        heap.
Example
        ::

                geoip_city.clean_up();

country_code
------------

Prototype
        ::

                country_code(VOID)
Return value
        STRING
Description
        Returns the two letter country code of the IP passed to `locate` or
        `locate_ip`. "AA" for no record.
Example
        ::

                geoip_city.country_code();

country_code3
-------------

Prototype
        ::

                country_code3(VOID)
Return value
        STRING
Description
        Returns the three letter country code of the IP passed to `locate` or
        `locate_ip`. "AAA" for no record.
Example
        ::

                geoip_city.country_code3();

country_name
------------

Prototype
        ::

                country_name(VOID)
Return value
        STRING
Description
        Returns the country name of the IP passed to `locate` or `locate_ip`.
        "No Record" for no record.
Example
        ::

                geoip_city.country_name();

region
------

Prototype
        ::

                region(VOID)
Return value
        STRING
Description
        Returns the region name of the IP passed to `locate` or `locate_ip`. In
        some cases this is the state or province code. It's the subdivision
        below country, and above city for whatever country the IP is located in.
        "No Record" for no record.
Example
        ::

                geoip_city.region();

city
----

Prototype
        ::

                city(VOID)
Return value
        STRING
Description
        Returns the city name of the IP passed to `locate` or `locate_ip`.
        "No Record" for no record.
Example
        ::

                geoip_city.city();

postal_code
-----------

Prototype
        ::

                postal_code(VOID)
Return value
        STRING
Description
        Returns the postal code or ZIP of the IP passed to `locate` or
        `locate_ip`. "No Record" for no record.
Example
        ::

                geoip_city.postal_code();

latitude
--------

Prototype
        ::

                latitude(VOID)
Return value
        STRING
Description
        Returns the latitude for the IP passed to `locate` or `locate_ip`. Both
        `latitude` and `longitude` methods return "0" for no record. This is
        safe because there's no internet in the Gulf of Guinea.
Example
        ::

                geoip_city.latitude();

longitude
---------

Prototype
        ::

                longitude(VOID)
Return value
        STRING
Description
        Returns the longitude for the IP passed to `locate` or `locate_ip`. Both
        `latitude` and `longitude` methods return "0" for no record. This is
        safe because there's no internet in the Gulf of Guinea.
Example
        ::

                geoip_city.longitude();

area_code
---------

Prototype
        ::

                area_code(VOID)
Return value
        STRING
Description
        Returns the telephone area code for the IP passed to `locate` or
        `locate_ip`. "000" for no record. This is mostly for North America.
Example
        ::

                geoip_city.area_code();

metro_code
----------

Prototype
        ::

                metro_code(VOID)
Return value
        STRING
Description
        Returns the metro code for the IP passed to `locate` or `locate_ip`.
        "000" for no record. These are primarily for IPs in the us. They are the
        same metro codes as the Google Adwords api uses.
Example
        ::

                geoip_city.metro_code();

continent_code
--------------

Prototype
        ::

                continent_code(VOID)
Return value
        STRING
Description
        Returns the two letter continent code for the IP passed to `locate` or
        `locate_ip`. "AA" for no record.
Example
        ::

                geoip_city.continent_code();

INSTALLATION
============

This vmod is ONLY compatible with Varnish 4. There is no 3.0 branch.

The source tree is based on autotools to configure the building, and
does also have the necessary bits in place to do functional unit tests
using the varnishtest tool.

As this is a Varnish 4 module, there is no need to specify the source directory,
or the VMOD directory. Normally pkgconfig should figure it all out.

Usage::

 apt-get install libgeoip-dev
 ./autogen.sh
 ./configure [VARNISHSRC=DIR] [VMODDIR=DIR]
 ./make && && make install

`VARNISHSRC` is the directory of the Varnish source tree for which to
compile your vmod. Both the `VARNISHSRC` and `VARNISHSRC/include`
will be added to the include search paths for your module.

Optionally you can also set the vmod install directory by adding
`VMODDIR=DIR` (defaults to the pkg-config discovered directory from your
Varnish installation).

Make targets:

* make - builds the vmod
* make install - installs your vmod in `VMODDIR`
* make check - runs the unit tests in ``src/tests/*.vtc``

In your VCL you could then use this vmod along the following lines::
        
        import geoip_city;

        sub vcl_deliver {
                geoip_city.locate_ip(client.ip);

                set resp.http.X-Country-Code = geoip_city.country_code();
                set resp.http.X-City = geoip_city.city();
                set resp.http.X-Latitude = geoip_city.latitude();
                set resp.http.X-Longitude = geoip_city.longitude();

                geoip_city.clean_up();
        }

HISTORY
=======

It's brand new!

COPYRIGHT
=========

This document is licensed under the MIT license. See LICENSE for details.

* Copyright (c) 2014 theScore Inc.
