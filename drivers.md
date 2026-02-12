---
title: Printer Applications and Printer Drivers
layout: doc
---

The upcoming major release of CUPS, CUPS 3, no longer supports classic printer drivers (.ppd files and filters), allowing to use only modern driverless printers.

However that doesn't mean that all older devices will stop working: many existing drivers have been converted to Printer Applications, a special compatibility layer designed to reuse existing code for CUPS 3 in a secure way.

Am I affected by this change?
----------------------

**First, determine if your printer supports one of the driverless standards.**  
If your printer is manufactured after 2011, there's a high chance it supports AirPrint™, IPP Everywhere™ or Mopria® — look for the following logos on your printer or the box it came in:

<table align="center" cellpadding="20">
<tr>
  <td><img src="https://developer.apple.com/assets/elements/icons/airprint/airprint-96x96_2x.png" height="192" alt="AirPrint"></td>
  <td><img src="https://www.pwg.org/ipp/ipp-everywhere-color.svg" height="192" alt="IPP Everywhere"></td>
  <td><img src="https://mopria.org/images/mopria_logo.svg#joomlaImage://local-images/mopria_logo.svg" height="192" alt="Mopria"></td>
</tr>
<tr>
  <td align="center">AirPrint™</td>
  <td align="center">IPP Everywhere™</td>
  <td align="center">Mopria®</td>
</tr>
</table>

You can search for your printer model in the certified device list from:

* Apple: [Search for AirPrint-Enabled Printers](https://mfi.apple.com/account/airprint-search)
* Mopria: [Certified products list](https://mopria.org/certified-products)

**If your printer supports *either* of AirPrint or Mopria, it will work with CUPS
3 out of the box**.

Another way to check for driverless support using your smartphone: if your
printer has wired or wireless network (Wi-Fi) and it's possible to print from
Android or iOS *without any additional application*, it will work with CUPS 3.

**If your printer is not driverless**, check which driver you currently use.

| Driver Name                                         | Supported | Printer Application              |
| --------------------------------------------------- | --------- | -------------------------------- |
| IPP Everywhere or Driverless                        | ✔️   | None required                         |
| Ghostscript (jlet\*, pcl\*, pxl\* [and others][gs]) | ✔️   | [ghostscript-printer-app] |
| foo2zjs (foo2xqx, foo2hbpl, foo2qpdl)               | ✔️   | [ghostscript-printer-app] |
| SpliX (Samsung, HP Laser~~jet~~)                    | ✔️   | [ghostscript-printer-app] |
| brlaser (Brother laser printers)                    | ✔️   | [ghostscript-printer-app] |
| pxljr                                               | ✔️   | [ghostscript-printer-app] |
| fxlinuxprint (Fuji Xerox)                           | ✔️   | [ghostscript-printer-app] |
| c2esp: (Kodak EasyShare)                            | ✔️   | [ghostscript-printer-app] |
| rastertosag-gdi (Ricoh Aficio SP 1000S/1100S)       | ✔️   | [ghostscript-printer-app] |
| dymo                                                | ✔️   | [ghostscript-printer-app] |
| ptouch (Brother P-Touch label printers)             | ✔️   | [ghostscript-printer-app] |
| Postscript (PS)                                     | ✔️   | [ps-printer-app]          |
| hpcups                                              | ✔️   | [hplip-printer-app]       |
| braille                                             | ✔️   | [brf-printer-app]         |
| Gutenprint                                          | ✔️   | [gutenprint-printer-app]  |
| Canon UFRII / UFRII-LT                              | ❌   | Ask printer manufacturer  |
| Canon CAPT                                          | ❌   | Ask printer manufacturer  |
| Pantum GDI (BM18*, BM22*, M65*, P2*)                | ❌   | Ask printer manufacturer  |

[gs]: https://github.com/OpenPrinting/ghostscript-printer-app?tab=readme-ov-file#contained-printer-drivers-in-the-snap
[ghostscript-printer-app]: https://github.com/OpenPrinting/ghostscript-printer-app
[ps-printer-app]: https://github.com/OpenPrinting/ps-printer-app
[hplip-printer-app]: https://github.com/OpenPrinting/hplip-printer-app
[brf-printer-app]: https://github.com/OpenPrinting/braille-printer-app
[gutenprint-printer-app]: https://github.com/OpenPrinting/gutenprint-printer-app


---------------


After much discussion within OpenPrinting, we decided to deprecate
support for raw queues and printer drivers starting with CUPS 2.3 in 2019, and
to work to develop and maintain a collection of *Printer Applications* that
support the printers still requiring those printer drivers.

Printers supporting the various Internet Printing Protocol (IPP) "driverless"
printing standards will continue to function without change, with Printer
Applications bridging the gap to older printers.  Almost every printer
manufactured since 2010 supports IPP/2.0 with standard file formats - the
primary holdouts are industrial label printers and certain "vertical market"
printers.  Look for the following logos on your printer or the box it came in:

<table align="center" cellpadding="20">
<tr>
  <td><img src="https://developer.apple.com/assets/elements/icons/airprint/airprint-96x96_2x.png" height="192" alt="AirPrint"></td>
  <td><img src="https://www.pwg.org/ipp/ipp-everywhere-color.svg" height="192" alt="IPP Everywhere"></td>
  <td><img src="https://mopria.org/images/mopria_logo.svg#joomlaImage://local-images/mopria_logo.svg" height="192" alt="Mopria"></td>
</tr>
<tr>
  <td align="center">AirPrint™</td>
  <td align="center">IPP Everywhere™</td>
  <td align="center">Mopria®</td>
</tr>
</table>


Why Have We Done This?
----------------------

Raw queues are special print queues with no PPD file or driver that typically
used to support special-use printers with custom applications that know about
printer capabilities and how to produce printer-ready (document) data.  For
example, a shipping application might produce ZPL data for a Zebra-compatible
label printer and then run the "lp" command to spool that raw file through CUPS.
While convenient for these applications, it requires CUPS to special-case these
printers and can be confusing for users when they try to use that same print
queue from a regular application and get garbage out of the printer.  Raw queues
also pose problems for printer sharing since the client system is unable to
provide any information about the printer locally.  Finally, allowing programs
to send arbitrary data to a printer is generally not a good idea security-wise.

Printer drivers are composed of special PostScript Printer Description (PPD)
files that provide the printer capabilities and features along with filter
programs that convert standard formats to something the printer can understand.
While these filter programs are run in a restricted environment to minimize the
security risk, the risk is still not zero.  In addition, filters are typically
binary programs and will only run on the platform and operating system they were
built for, which has historically caused compatibility problems and much
frustration.  Finally, PPD files are not capable of expressing the full range of
capabilities of a modern printer and the CUPS printer driver interface does not
allow drivers to provide dynamic state information such as what media is loaded,
the state of consumables such as ink and toner, and so forth.


What Does it Mean to Deprecate Something?
-----------------------------------------

When we deprecate a feature or capability in CUPS, we are announcing the
intention to stop supporting it in the next major release of CUPS, in this case
CUPS 3.0.  Given the historical pace of CUPS development, that usually means you
have years to make the transition.  In the case of printer drivers, we made the
conscious choice to have Printer Applications in place before releasing CUPS
3.0.  And since Linux distributions typically lag behind the "bleeding edge" of
CUPS development, you can expect to be able to use printer drivers for few more
years after CUPS 3.0 is released.


What Are Printer Applications?
------------------------------

Printer Applications are programs that mimic IPP/2.0 printers.  The simplest
printer applications wrap a CUPS printer driver and map PPD options and choices
to the corresponding IPP attributes and values.  [PAPPL][PAPPL] provides a
convenient framework for easily creating these applications and porting existing
CUPS raster drivers.  The following printer applications are already available
or (in the case of Gutenprint) under development:

- [Ghostscript Printer Application][Ghostscript]: PAPPL-based printer
  application for Ghostscript-based printer drivers.
- [Gutenprint][Gutenprint]: A PAPPL-based printer application for all Gutenprint
  printer drivers.
- [hp-printer-app][hp-printer-app]: PAPPL-based PCL printer application based on
  the CUPS rastertohp driver.
- [LPrint][LPrint]: PAPPL-based label printer application, currently supporting
  Zebra and Dymo label printers with plans to support more, based on the CUPS
  rastertolabel driver.  "Raw" printing is supported as well if you have an
  application that produces the native print data format.
- [ps-printer-app][ps-printer-app]: PAPPL-based PostScript printer application
  that supports all CUPS/PostScript printers via PPDs and includes all of the
  Foomatic and HPLIP drivers.


[Ghostscript]: https://github.com/OpenPrinting/ghostscript-printer-app
[Gutenprint]: http://gutenprint.sf.net
[hp-printer-app]: https://github.com/michaelrsweet/hp-printer-app
[LPrint]: https://github.com/michaelrsweet/lprint
[PAPPL]: https://www.msweet.org/pappl/
[ps-printer-app]: https://github.com/openprinting/ps-printer-ps
