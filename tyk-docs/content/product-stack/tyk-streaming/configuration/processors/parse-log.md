---
title: Parse Log
description: Explains an overview of parse_log processor
tags: [ "Tyk Streams", "Stream Processors", "Processors", "parse_log", "Logs", "Log" ]
---

Parses common log [formats](#formats) into [structured data](#codecs).

## Common

```yml
# Common config fields, showing default values
label: ""
parse_log:
  format: "" # No default (required)
```

## Advanced

```yml
# All config fields, showing default values
label: ""
parse_log:
  format: "" # No default (required)
  best_effort: true
  allow_rfc3339: true
  default_year: current
  default_timezone: UTC
```

## Fields

### format

A common log [format](#formats) to parse.


Type: `string`  
Options: `syslog_rfc5424`, `syslog_rfc3164`.

### best_effort

Still returns partially parsed messages even if an error occurs.


Type: `bool`  
Default: `true`  

### allow_rfc3339

Also accept timestamps in rfc3339 format while parsing. Applicable to format `syslog_rfc3164`.


Type: `bool`  
Default: `true`  

### default_year

Sets the strategy used to set the year for rfc3164 timestamps. Applicable to format `syslog_rfc3164`. When set to `current` the current year will be set, when set to an integer that value will be used. Leave this field empty to not set a default year at all.


Type: `string`  
Default: `"current"`  

### default_timezone

Sets the strategy to decide the timezone for rfc3164 timestamps. Applicable to format `syslog_rfc3164`. This value should follow the [time.LoadLocation](https://golang.org/pkg/time/#LoadLocation) format.


Type: `string`  
Default: `"UTC"`  

## Codecs

Currently the only supported structured data codec is `json`.

## Formats

### syslog_rfc5424

Attempts to parse a log following the [Syslog rfc5424](https://tools.ietf.org/html/rfc5424) spec. The resulting structured document may contain any of the following fields:

- `message` (string)
- `timestamp` (string, RFC3339)
- `facility` (int)
- `severity` (int)
- `priority` (int)
- `version` (int)
- `hostname` (string)
- `procid` (string)
- `appname` (string)
- `msgid` (string)
- `structureddata` (object)

### syslog_rfc3164

Attempts to parse a log following the [Syslog rfc3164](https://tools.ietf.org/html/rfc3164) spec. The resulting structured document may contain any of the following fields:

- `message` (string)
- `timestamp` (string, RFC3339)
- `facility` (int)
- `severity` (int)
- `priority` (int)
- `hostname` (string)
- `procid` (string)
- `appname` (string)
- `msgid` (string)