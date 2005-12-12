[2005-12-12]
DOS/32 Advanced DOS Extender, version 9.1.1
Diebus fatalibus!


Contents:
  1.0 - Synopsis
  2.0 - What's New?
  3.0 - System Requirements
  4.0 - DOS/32A Distributions
  5.0 - Release Notes
  6.0 - Contact




1.0 - Synopsis
==============
DOS/32 Advanced DOS Extender is a drop-in replacement for the DOS/4GW
DOS Extender and compatibles. Being fast, small and flexible DOS/32A can
be used in many environments, from embedded systems to DOS emulators, by
both developers and end users alike.



2.0 - What's New?
=================
See updates.txt file (included with this distribution) for the list of
recent changes.



3.0 - System Requirements
=========================
. IBM PC compatible hardware
. 80386 processor or better
. 1 MB of memory (up to 2 GB supported)
. DOS version 4.0 or higher
. VGA compatible graphics card



4.0 - DOS/32A Distributions
===========================
The following DOS/32A distributions are available:

1) dos32a-911-bin.zip
2) dos32a-911-src.zip


1) is the binary distribution of the most recent version of DOS/32A. Contains
precompiled binaries of the DOS Extender and its tools.

2) is the source distribution of the most recent version of DOS/32A. Contains
source code of the DOS Extender and its tools.

END-USERS:
----------
Download the latest binary distribution, dos32a-911-bin.zip, and unzip it into
a folder of your choice. No installation is necessary.

DEVELOPERS:
-----------
Download the latest source and binary distributions, dos32a-911-src.zip and
dos32a-911-bin.zip, and unzip both into a folder of your choice. No installation
is necessary. You may also want to download and install the "Liberty Edition",
dos32a-710-installer.zip, which contains DOS/32A SDK including examples and
documentation. Although hopelessly outdated, they may still provide a valuable
source of information.

A rather comprehensive (but regrettably outdated) set of documentation
manuals is available on-line at http://dos32a.narechk.net/manual/ . RTFM!



5.0 - Release Notes (and other useless trivia)
==============================================

ATTENTION Developers:
---------------------
Since DOS/32A v9.1.0 the exception handling code has been rewritten in order to
properly support DPMI v0.9 specification. The changes may break code written to
work exclusively with prior versions of DOS/32A (i.e. \examples\asm_4\simshow.asm
from the ancient DOS/32A SDK v7.1 no longer works). User-defined exception handlers
should now return with RETF (as mandated by DPMI spec), not IRETD instruction.


Building DOS/32A
----------------
The source distribution of DOS/32A only includes the source code (*.asm and *.c files)
of the most recent version of the DOS Extender. Download and install DOS/32A SDK v7.1
(dos32a-710-installer.zip) which provides the build environment, instructions and
necessary files.


XMS/VCPI/DPMI detection order
-----------------------------
DOS/32A initialization sequence will attempt to initialize XMS even though a VCPI
or DPMI systems may be present, unless the DOS Extender detects that it runs in
V86/ProtectedMode. In fact, the detection code will always try the light-weight
systems (INT 15h and XMS) first, before it goes ahead with VCPI (truly an
abomination) and DPMI (some are very good but many are crap).

The current init order is INT 15h, XMS, VCPI then DPMI. This, however is in
conflict with the DPMI spec recommendations which suggest the reverse order.
Extensive tests have not highlighted any problems with DOS/32A's implementation,
but then again, empirical tests are just that: empirical. If you find any
problems with the current setup, let me know.


Exception/IRQ handling
----------------------
The rewrite of the IRQ and exception handling code should improve compatibility
with existing applications. Because of a lot of confusion about exactly how
it all works out in DOS/32A here is a concise rundown:

	assuming default PIC mappings:
	1) INT vectors 00..07h - can be HW exceptions (EXCs), SW interrupts (INTs)
	2) INT vectors 08..0Fh - can be HW exceptions (EXCs), SW interrupts (INTs), HW interrupts (IRQs)
	3) INT vectors 70..7Fh - can be SW interrupts (INTs), HW interrupts (IRQs)
	4) all other INT vectors are SW interrupts (INTs)

	unlike INTs, IRQs are auto-callbacked to Protected Mode using special "turbo-callbacks"
	whenever a Protected Mode handler is installed

	for 1, EXCs are buffered, INTs are written directly into IDT (override buffered EXCs)
	for 2, EXCs are buffered, IRQs are buffered, INTs are sent to the corresponding EXCs handlers,
		kernel always controls IDT entries
	for 3, no buffering, INTs/IRQs are written directly into IDT
	for 4, no buffering, no auto-callbacking

	additionally, DOS/32A will auto-callback the following system critical INTs using
	standard callbacks whenever a Protected Mode handler is installed:
		INT 1Bh (CTRL-BREAK)
		INT 1Ch (Timer)
		INT 23h (CTRL-C)
		INT 24h (DOS critical handler)


Memory Allocation
-----------------
DOS/32A is able to allocate and handle up to 2GB of extended memory when running
under INT 15h (through AX=0E820h with fallback to AX=0E801h, then AH=88h) and XMS
systems. When running under VCPI the DOS Extender's ability to allocate extended
memory is limited by availability of conventional memory (e.g. below 1MB). This is
because the DOS Extender keeps the Page Tables in conventional memory, unfortunately
by design as in the old days some VCPI managers could fiddle with A20 gate during the
most inappropriate times causing Page Tables in extended memory to become inaccessible.


The Debuggers
-------------
The internal debugger has been removed since its utility was limited (probably only
used by me to debug the DOS Extender itself -- a much better job can be done with
emulators nowadays). Incidentally this fixes problems with some applications which
inserted breakpoints along normal code-paths causing DOS/32A to break into the
tracer continuously.

Because of the changes to the exception handling code this release of DOS/32A and
its toolkit does not include the debugger (SD.EXE). If you need it, you can try to
backport the source code from the previous versions.




6.0 - Contact
=============
http://dos32a.narechk.net/


[EOF]
