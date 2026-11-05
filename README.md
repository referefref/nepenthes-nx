# nepenthes-nx

Adapted from https://github.com/jrwren/nepenthes (a mirror of `git://git.carnivore.it/nepenthes.git`, the project's own repository; releases on https://sourceforge.net/projects/nepenthes/) by Paul Baecher, Markus Koetter and Georg Wicherski (the Nepenthes development team).

Nepenthes is a low-interaction honeypot that emulates known vulnerabilities in Windows services to collect self-propagating malware. This copy is the upstream code at its last commit (`03e96e6`, 16 October 2009, version 0.2.2) with only the changes needed to build it with a current compiler, plus two safety switches. The modules, responses and logs are unchanged. The original documentation is in `README`, `INSTALL` and `doc/`; the licence (GPL v2) is in `COPYING` and `LICENSE`.

## Changes from upstream

Build (g++ 12 and later, current libcurl):

- `configure.ac` adds `-std=gnu++98`: the code is C++98 and fails under the C++17 default (`register`, `byte` clashes with `std::byte`).
- `-Werror` removed from every `Makefile.am`, so newer compiler warnings do not stop the build.
- Missing `<sys/types.h>` and `<time.h>` includes added to `Nepenthes.hpp`, `SocketManager.hpp`, `EventHandler.hpp` and `Socket.hpp`.
- `#include <curl/types.h>` removed (the header was dropped from libcurl in 2012).

Safety switches (both off by default; set to `"1"` in `nepenthes.conf` to restore upstream behaviour):

- `nepenthes.downloadmanager.download_enabled`: when `"0"`, URLs that attackers or decoded shellcode offer are logged (`Download disabled, not fetching <url> (offered by <ip>)`) and not fetched.
- `nepenthes.socketmanager.allow_outbound`: when `"0"`, every outgoing TCP or UDP connection (connect-back shells, downloads, submit-http, submit-gotek, submit-norman, log-irc) is refused and logged (`Outgoing connection disabled, not connecting to <ip>:<port>`). Set it to `"1"` if you enable a submission or logging module that talks to your own server.

## Build

Debian 12:

    apt-get install build-essential autoconf automake libtool flex bison \
        libcurl4-openssl-dev libmagic-dev zlib1g-dev libpcre3-dev libadns1-dev libcap-dev libpcap-dev
    autoreconf -fi
    ./configure --prefix=/opt/nepenthes
    make && make install
    cd /opt/nepenthes && ./bin/nepenthes -u nepenthes -g nepenthes -c etc/nepenthes/nepenthes.conf

Run it only in an isolated host or container. Captured files in `var/binaries/` are live malware; never execute them.

---

Original introduction (from `README`):

		Nepenthes 
	- the finest collection -

Our documentation and installation instructions is available online at

        http://nepenthes.sourceforge.net/documentation:readme
