sa-utility-rsyslog-sentry-bridge
================================

[![Build Status](https://travis-ci.org/softasap/sa-utility-rsyslog-sentry-bridge.svg?branch=master)](https://travis-ci.org/softasap/sa-utility-rsyslog-sentry-bridge)


On a client box

Configure rsyslog to forward entries to sentry, for example

```
emacs /usr/local/etc/rsyslog.conf
$ModLoad imfile

# Nginx
$InputFileName /var/log/nginx-error.log
$InputFileTag nginx-error:
$InputFileStateFile stat-nginx-error
$InputFileSeverity info

$InputRunFileMonitor

$ActionQueueType           LinkedList   # use asynchronous processing
$ActionQueueFileName       test         # set file name, also enables disk mode
$ActionResumeRetryCount    -1           # infinite retries on insert failure
$ActionQueueSaveOnShutdown on           # save in-memory data if rsyslog shuts down

# Transfer all of the logs to this UDP port
*.* @@127.0.0.1:10514;RSYSLOG_SyslogProtocol23Format
```

Alternative syntax
```
action(type="omfwd" Target="127.0.0.1" Port="10514" Protocol="udp" Template="RSYSLOG_SyslogProtocol23Format" )
```

Audit log forwarding:

if you plan to forward auditlog, makesure syslog has read access

chown -R root:syslog /var/log/audit


Potential rsyslog configuration to forward CRITICAL events to sentry would be 

```
$ModLoad imfile
$InputFileName /var/log/audit/audit.log
$InputFileTag Auditd
$InputFileStateFile /var/spool/rsyslog
$InputFileFacility local4
$InputRunFileMonitor

local4.* /var/log/debugfmt;RSYSLOG_DebugFormat

:msg,contains,"type=EXECVE"
:msg,contains,"passwd"
local4.* @@127.0.0.1:10514;RSYSLOG_SyslogProtocol23Format

:msg,contains,"DEL_GROUP"
local4.* @@127.0.0.1:10514;RSYSLOG_SyslogProtocol23Format
```


Example of use: check box-example

Simple:

```YAML


     - {
         role: "sa-utility-rsyslog-sentry-bridge"
       }

```


Advanced:

```YAML


     - {
         role: "sa-utility-rsyslog-sentry-bridge",
         option_install_python: false
       }

```


Copyright and license
---------------------


Code licensed under the [BSD 3 clause] (https://opensource.org/licenses/BSD-3-Clause) or the [MIT License] (http://opensource.org/licenses/MIT).

Subscribe for roles updates at [FB] (https://www.facebook.com/SoftAsap/)
