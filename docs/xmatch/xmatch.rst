.. _astroquery_xmatch:

************************************
xMatch Queries (`astroquery.xmatch`)
************************************

Getting started
===============
The xMatch_ service is a tool to cross-identify sources between very large data
sets or between a user-uploaded list and a large catalogue. An example for the
latter case can be found below.

First of all, a new CSV file is created which stores a list of coordinates. It
has the following content::

    ra,dec
    267.22029,-20.35869
    274.83971,-25.42714
    275.92229,-30.36572
    283.26621,-8.70756
    306.01575,33.86756
    322.493,12.16703

.. testsetup::

   >>> with open('pos_list.csv', 'w') as f:    # doctest: +IGNORE_OUTPUT
   ...    f.write("""ra,dec
   ... 267.22029,-20.35869
   ... 274.83971,-25.42714
   ... 275.92229,-30.36572
   ... 283.26621,-8.70756
   ... 306.01575,33.86756
   ... 322.493,12.16703""")

Next, the xMatch service will be used to find cross matches between the
uploaded file and a VizieR catalogue.  The parameters ``cat1`` and ``cat2``
define the catalogues where one of them may point to a local file (in this
example, the CSV file is stored in
``'pos_list.csv'``). ``max_distance`` denotes the maximum distance in
arcsec to look for counterparts; it is used here to limit the number of rows
in the resulting table for demonstration purposes.  Finally, ``colRa1`` and
``colDec1`` are used to denote the column names in the input file.

.. doctest-remote-data::

    >>> from astropy import units as u
    >>> from astroquery.xmatch import XMatch
    >>> from astropy.table import Table
    >>> input_table = Table.read('pos_list.csv')
    >>> table = XMatch.query(cat1=input_table,
    ...                      cat2='vizier:II/246/out',
    ...                      max_distance=5 * u.arcsec, colRA1='ra',
    ...                      colDec1='dec')
    >>> type(table)
    <class 'astropy.table.table.Table'>
    >>> print(table)
    angDist      ra       dec         2MASS       ... Qfl Rfl  X   MeasureJD
     arcsec                                       ...                  d
    -------- --------- --------- ---------------- ... --- --- --- ------------
    1.352044 267.22029 -20.35869 17485281-2021323 ... EEU 226   2 2450950.8609
    1.578188 267.22029 -20.35869 17485288-2021328 ... UUB 662   2 2450950.8609
    3.699368 267.22029 -20.35869 17485264-2021294 ... UUB 662   2 2450950.8609
    3.822922 267.22029 -20.35869 17485299-2021279 ... EBA 222   2 2450950.8609
    4.576677 267.22029 -20.35869 17485255-2021326 ... CEU 226   2 2450950.8609
    0.219609 274.83971 -25.42714 18192154-2525377 ... AAA 211   0 2451407.5033
    1.633225 275.92229 -30.36572 18234133-3021582 ... EEE 222   2 2451021.7212
    0.536998 283.26621  -8.70756 18530390-0842276 ... AAA 222   0 2451301.7945
    1.178542 306.01575  33.86756 20240382+3352021 ... AAA 222   0 2450948.9708
    0.853178   322.493  12.16703 21295836+1210007 ... EEA 222   0 2451080.6935
     4.50395   322.493  12.16703 21295861+1210023 ... EEE 222   0 2451080.6935


.. testcleanup::

    >>> from astroquery.utils import cleanup_saved_downloads
    >>> cleanup_saved_downloads(['pos_list.csv'])


Troubleshooting
===============

403 Forbidden
-------------

If you are getting a 403 Forbidden error, then your IP address has been banned from
XMatch's server. This means that you sent too many cross-matching jobs in parallel to 
the service, blocking other astronomers. Please contact the CDS team at
cds-question[at]unistra.fr to find a solution.

Out of date results
-------------------

If you are repeatedly getting failed queries, or bad/out-of-date results, try clearing your cache:

.. code-block:: python

    >>> from astroquery.xmatch import XMatch
    >>> XMatch.clear_cache()

If this function is unavailable, upgrade your version of astroquery. 
The ``clear_cache`` function was introduced in version 0.4.7.dev8479.

    
Reference/API
=============

.. automodapi:: astroquery.xmatch
    :no-inheritance-diagram:


.. _xMatch: http://cdsxmatch.u-strasbg.fr/xmatch/doc/
