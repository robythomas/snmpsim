SNMP Simulator
--------------
[![PyPI](https://img.shields.io/pypi/v/snmpsim.svg?maxAge=2592000)](https://pypi.org/project/thola-snmpsim/)
[![Python Versions](https://img.shields.io/pypi/pyversions/snmpsim.svg)](https://pypi.org/project/thola-snmpsim/)
[![GitHub license](https://img.shields.io/badge/license-BSD-blue.svg)](https://raw.githubusercontent.com/inexio/snmpsim/master/LICENSE.txt)

This is a pure-Python, open source and free implementation of SNMP agents simulator
distributed under 2-clause [BSD license](http://snmplabs.thola.io/snmpsim/license.html).


Why this fork?
--------------
[Original project](https://github.com/etingof/snmpsim) by [Ilya Etingof](https://github.com/etingof) seems not to be continued anymore.
Because of that, we try to maintain / enhance SNMP simulator. 

Features
--------

* SNMPv1/v2c/v3 support
* SNMPv3 USM supports MD5/SHA/SHA224/SHA256/SHA384/SHA512 auth and
  DES/3DES/AES128/AES192/AES256 privacy crypto algorithms
* Runs over IPv4 and/or IPv6 transports
* Simulates many EngineID's, each with its own set of simulated objects
* Varies response based on SNMP Community, Context, source/destination addresses and ports
* Can gather and store snapshots of SNMP Agents for later simulation
* Can run simulation based on MIB files, snmpwalk and sapwalk output
* Can gather simulation data from network traffic or tcpdump snoops
* Can gather simulation data from external program invocation or a SQL database
* Can trigger SNMP TRAP/INFORMs on SET operations
* Capable to simultaneously simulate tens of thousands of Agents
* Offers REST API based [control plane](http://snmplabs.thola.io/snmpsim-control-plane)
* Gathers and reports extensive activity metrics
* Pure-Python, easy to deploy and highly portable
* Can be extended by loadable Python snippets

Related projects
--------
If you are looking for simulating the command line interface (CLI) of a network device, please look at our [NESi project](https://github.com/inexio/NESi).

[Thola](https://github.com/inexio/thola) is the opposite of NESi and SNMP simulator. Thola is an unified interface for communication with network devices (manufacturer and model should be abstracted). Thola is using NESi for its automatic integration tests.


Download
--------

SNMP simulator software is freely available for download from
[PyPI](https://pypi.org/project/thola-snmpsim/) and
[project site](http://snmplabs.thola.io/snmpsim/download.html).

Installation
------------

Just run:

```bash
$ pip install https://github.com/inexio/snmpsim/archive/master.zip
```

How to use SNMP simulator
-------------------------
You can use snmpsim in different ways:
- for smaller environments, it is sufficient to use the snmpsim-command-responder
- in larger environments (for example lab environments) the so-called [snmpsim-control-plane](https://github.com/inexio/snmpsim-control-plane) is often used: 
This is a management service that has a REST-API and manages the snmpsim-command-responder instances. 
If you want to know how to set up this, please have a look in our documentation on the [snmpsim-control-plane](https://github.com/inexio/snmpsim-control-plane/blob/master/setting-up-snmpsim-control-plane.md) repo.

There are also two clients based on the REST API of the snmpsim-control-plane. 
- [UI for snmpsim](https://github.com/inexio/snmp-sim-ui)
- [CLI manager for snmpsim](https://github.com/inexio/snmpsim-cli-manager)

Once installed, invoke `snmpsim-command-responder` and point it to a directory
with simulation data:

```
$ snmpsim-command-responder --data-dir=./data --agent-udpv4-endpoint=127.0.0.1:1024
```

Simulation data is stored in simple plaint-text files having OID|TYPE|VALUE
format:

```
$ cat ./data/public.snmprec
1.3.6.1.2.1.1.1.0|4|Linux 2.6.25.5-smp SMP Tue Jun 19 14:58:11 CDT 2007 i686
1.3.6.1.2.1.1.2.0|6|1.3.6.1.4.1.8072.3.2.10
1.3.6.1.2.1.1.3.0|67|233425120
1.3.6.1.2.1.2.2.1.6.2|4x|00127962f940
1.3.6.1.2.1.4.22.1.3.2.192.21.54.7|64x|c3dafe61
...
```

Simulator maps query parameters like SNMP community names, SNMPv3 contexts or
IP addresses into data files.

You can immediately generate simulation data file by querying existing SNMP agent:

```
$ snmpsim-record-commands --agent-udpv4-endpoint=demo.snmplabs.com \
    --output-file=./data/public.snmprec
SNMP version 2c, Community name: public
Querying UDP/IPv4 agent at 195.218.195.228:161
Agent response timeout: 3.00 secs, retries: 3
Sending initial GETNEXT request for 1.3.6 (stop at <end-of-mib>)....
OIDs dumped: 182, elapsed: 11.97 sec, rate: 7.00 OIDs/sec, errors: 0
```

Alternatively, you could build simulation data from a MIB file:

```
$ snmpsim-record-mibs --output-file=./data/public.snmprec \
    --mib-module=IF-MIB
# MIB module: IF-MIB, from the beginning till the end
# Starting table IF-MIB::ifTable (1.3.6.1.2.1.2.2)
# Synthesizing row #1 of table 1.3.6.1.2.1.2.2.1
...
# Finished table 1.3.6.1.2.1.2.2.1 (10 rows)
# End of IF-MIB, 177 OID(s) dumped
```

Or even sniff on the wire, recover SNMP traffic there and build simulation
data from it.

Besides static files, SNMP simulator can be configured to call its plugin modules
for simulation data. We ship plugins to interface SQL and noSQL databases, file-based
key-value stores and other sources of information.

Besides stand-alone deployment described above, third-party
[SNMP Simulator control plane](https://github.com/inexio/snmpsim-control-plane)
project offers REST API managed mass deployment of multiple `snmpsim-command-responder`
instances.

Documentation
-------------

Detailed information on SNMP simulator usage could be found at
[snmpsim site](http://snmplabs.thola.io/snmpsim/).

Getting help
------------

If something does not work as expected,
[open an issue](https://github.com/inexio/snmpsim/issues) at GitHub or
post your question [on Stack Overflow](https://stackoverflow.com/questions/ask).

Feedback and collaboration
--------------------------

I'm interested in bug reports, fixes, suggestions and improvements. Your
pull requests are very welcome!

If you want to contact us, please mail to the [Thola Team](mailto:snmplabs@thola.io)

Copyright (c) 2010-2019, [Ilya Etingof](mailto:etingof@gmail.com). All rights reserved.
