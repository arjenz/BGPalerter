[![Build Status](https://api.travis-ci.org/nttgin/bgpalerter.svg)](https://travis-ci.org/nttgin/bgpalerter)
# BGPalerter
Real-time BGP monitoring tool, pre-configured for visibility loss and hijacks detection.

It can deliver alerts on files, by email, on slack, and more.

![BGPalerter](https://massimocandela.com/img/bgpalerter_github_image.png)

## TL;DR
> This section is useful if you don't care about the source code but you just want to run the monitor.
If you want to know more about the source code (which is completely open) please see the following sections.

1. Download the binary [here](https://github.com/nttgin/BGPalerter/releases) (be sure to select the one for your OS)

2. Download [`config.yml.example`](https://raw.githubusercontent.com/nttgin/BGPalerter/master/config.yml.example) as `config.yaml` and [`prefixes.yml`](https://raw.githubusercontent.com/nttgin/BGPalerter/master/prefixes.yml.example) as `prefixes.yml`, and place them in the same directory of the executable (if you skip this step, some default configuration files will be generated during the first execution)

3. Modify `prefixes.yml` and add the prefixes you want to monitor (or see below how to auto generate this file)

4. Run the executable

5. See the alerts in `logs/reports-YYYY-MM-DD.log`

In `config.yml` you can find other reporting mechanisms (e.g. email and slack) in addition to logging to files. 
Please uncomment the related section and configure according to your needs.

If you enable email reporting, download also the directory `reports/email_templates` in the same directory of the executable.


## More information for users

- [Monitored prefixes List](docs/prefixes.md#prefixes)
  - [Auto-generate](docs/prefixes.md#generate)
  - [Fields and meaning](docs/prefixes.md#prefixes-fields)

### Composition

You can compose the tool with 3 main components: connectors, monitors, and reports.

Connectors retrieve/listen to the data from different sources and transform them to a common format.
Monitors analyze the data flow and produce alerts. Different monitors try to detect different issues.
Reports send/store the alerts, e.g. by email or to a file.

##### connectors

Possible connectors are:

* _connectorRIS_, for real-time data from RIPE RIS (https://ris-live.ripe.net/)

* _connectorTest_, for testing purposes, it provokes all types of alerting

##### monitors

Possible monitors are:

* _monitorHijack_, for monitoring hijacks

* _monitorVisibility_, for monitoring prefixes' visibility (you will get notified when withdrawals make monitored routes disappear). A threshold can be specified in config.yml to trigger an alert only if the issue is visible from a certain amount of peers.

* _monitorNewPrefix_, for monitoring if new more specifics (of the monitored prefixes) start to be announced

##### reports

Possible reports are:

* _reportEmail_, to send alerts by email. SMTP configurations are in config.yml

* _reportFile_, to log the alerts to files. File directory, format, and log rotation configurations are in config.yml

* _reportSlack_, to send alerts in Slack. Hook URL is configurable in config.yml


## More information for developers

To start development:

1. git clone this repo

2. install Node.js (version >= 10.16) and npm ([installing node and npm](https://nodejs.org/en/download/))

3. execute `npm install` or `yarn` to install all dependencies ([installing yarn](https://yarnpkg.com/lang/en/docs/install))

4. run `npm run watch-and-serve` to run the application. At every file change it will self-reload.

### All npm commands

* `npm run watch-and-serve` to run the application from source code and monitor for file changes

* `npm run serve` to run the application from the source

* `npm run start|stop|restart` to run the application as a service in background

* `npm run status` to see the status of the service

* `npm run test` to run the tests

* `npm run build` to compile and build OS native applications

* `npm run generate-prefixes -- --a ASN(S) --o OUTPUT_FILE` to generate the monitored prefixes file

### Composition

You can compose the tool with 3 main components: connectors, monitors, and reports.

> **Important:**
All connectors MUST extend the class Connector. Monitors extend the class Monitor. Reports extend the class Report.
From the superclass they will inherit various generic methods while some have to be implemented.

Reports don't receive only alerts but also the data that provoked such alerts (so you can store the data and replay the accident later).

In `config.yml`, for each collection of components:

* `file` - refers to the file name which contains the class

* `channels` - refer to what channel(s) will be used to send or receive messages

* `params` - whatever parameters may be needed by the component at creation time




