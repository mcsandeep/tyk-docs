---
title: MQTT
description: Explains an overview of configuring MQTT output
tags: [ "Tyk Streams", "Stream Outputs", "Outputs", "MQTT" ]
---

Pushes messages to an MQTT broker.


## Common

```yml
# Common config fields, showing default values
output:
  label: ""
  mqtt:
    urls: [] # No default (required)
    client_id: ""
    connect_timeout: 30s
    topic: "" # No default (required)
    qos: 1
    write_timeout: 3s
    retained: false
    max_in_flight: 64
```

## Advanced

```yml
# All config fields, showing default values
output:
  label: ""
  mqtt:
    urls: [] # No default (required)
    client_id: ""
    dynamic_client_id_suffix: "" # No default (optional)
    connect_timeout: 30s
    will:
      enabled: false
      qos: 0
      retained: false
      topic: ""
      payload: ""
    user: ""
    password: ""
    keepalive: 30
    tls:
      enabled: false
      skip_cert_verify: false
      enable_renegotiation: false
      root_cas: ""
      root_cas_file: ""
      client_certs: []
    topic: "" # No default (required)
    qos: 1
    write_timeout: 3s
    retained: false
    retained_interpolated: "" # No default (optional)
    max_in_flight: 64
```

The `topic` field can be dynamically set using function [interpolations]({{< ref "/product-stack/tyk-streaming/configuration/common-configuration/interpolation#bloblang-queries" >}}). When sending batched messages these interpolations are performed per message part.

## Performance

This output benefits from sending multiple messages in flight in parallel for improved performance. You can tune the max number of in flight messages (or message batches) with the field `max_in_flight`.

## Fields

### urls

A list of URLs to connect to. If an item of the list contains commas it will be expanded into multiple URLs.


Type: `array`  

```yml
# Examples

urls:
  - tcp://localhost:1883
```

### client_id

An identifier for the client connection.


Type: `string`  
Default: `""`  

### dynamic_client_id_suffix

Append a dynamically generated suffix to the specified `client_id` on each run of the pipeline. This can be useful when clustering Tyk Streams producers.


Type: `string`  

| Option | Summary |
|---|---|
| `nanoid` | append a nanoid of length 21 characters |


### connect_timeout

The maximum amount of time to wait in order to establish a connection before the attempt is abandoned.


Type: `string`  
Default: `"30s"`  

```yml
# Examples

connect_timeout: 1s

connect_timeout: 500ms
```

### will

Set last will message in case of Tyk Streams failure


Type: `object`  

### will.enabled

Whether to enable last will messages.


Type: `bool`  
Default: `false`  

### will.qos

Set QoS for last will message. Valid values are: 0, 1, 2.


Type: `int`  
Default: `0`  

### will.retained

Set retained for last will message.


Type: `bool`  
Default: `false`  

### will.topic

Set topic for last will message.


Type: `string`  
Default: `""`  

### will.payload

Set payload for last will message.


Type: `string`  
Default: `""`  

### user

A username to connect with.


Type: `string`  
Default: `""`  

### password

A password to connect with.


Type: `string`  
Default: `""`  

### keepalive

Max seconds of inactivity before a keepalive message is sent.


Type: `int`  
Default: `30`  

### tls

Custom TLS settings can be used to override system defaults.


Type: `object`  

### tls.enabled

Whether custom TLS settings are enabled.


Type: `bool`  
Default: `false`  

### tls.skip_cert_verify

Whether to skip server side certificate verification.


Type: `bool`  
Default: `false`  

### tls.enable_renegotiation

Whether to allow the remote server to repeatedly request renegotiation. Enable this option if you're seeing the error message `local error: tls: no renegotiation`.


Type: `bool`  
Default: `false`  

### tls.root_cas

An optional root certificate authority to use. This is a string, representing a certificate chain from the parent trusted root certificate, to possible intermediate signing certificates, to the host certificate.


Type: `string`  
Default: `""`  

```yml
# Examples

root_cas: |-
  -----BEGIN CERTIFICATE-----
  ...
  -----END CERTIFICATE-----
```

### tls.root_cas_file

An optional path of a root certificate authority file to use. This is a file, often with a .pem extension, containing a certificate chain from the parent trusted root certificate, to possible intermediate signing certificates, to the host certificate.


Type: `string`  
Default: `""`  

```yml
# Examples

root_cas_file: ./root_cas.pem
```

### tls.client_certs

A list of client certificates to use. For each certificate either the fields `cert` and `key`, or `cert_file` and `key_file` should be specified, but not both.


Type: `array`  
Default: `[]`  

```yml
# Examples

client_certs:
  - cert: foo
    key: bar

client_certs:
  - cert_file: ./example.pem
    key_file: ./example.key
```

### tls.client_certs[].cert

A plain text certificate to use.


Type: `string`  
Default: `""`  

### tls.client_certs[].key

A plain text certificate key to use.


Type: `string`  
Default: `""`  

### tls.client_certs[].cert_file

The path of a certificate to use.


Type: `string`  
Default: `""`  

### tls.client_certs[].key_file

The path of a certificate key to use.


Type: `string`  
Default: `""`  

### tls.client_certs[].password

A plain text password for when the private key is password encrypted in PKCS#1 or PKCS#8 format. The obsolete `pbeWithMD5AndDES-CBC` algorithm is not supported for the PKCS#8 format. Warning: Since it does not authenticate the ciphertext, it is vulnerable to padding oracle attacks that can let an attacker recover the plaintext.


Type: `string`  
Default: `""`  

```yml
# Examples

password: foo

password: ${KEY_PASSWORD}
```

### topic

The topic to publish messages to.
This field supports [interpolation functions]({{< ref "/product-stack/tyk-streaming/configuration/common-configuration/interpolation#bloblang-queries" >}}).


Type: `string`  

### qos

The QoS value to set for each message. Has options 0, 1, 2.


Type: `int`  
Default: `1`  

### write_timeout

The maximum amount of time to wait to write data before the attempt is abandoned.


Type: `string`  
Default: `"3s"`  

```yml
# Examples

write_timeout: 1s

write_timeout: 500ms
```

### retained

Set message as retained on the topic.


Type: `bool`  
Default: `false`  

### retained_interpolated

Override the value of `retained` with an interpolable value, this allows it to be dynamically set based on message contents. The value must resolve to either `true` or `false`.
This field supports [interpolation functions]({{< ref "/product-stack/tyk-streaming/configuration/common-configuration/interpolation#bloblang-queries" >}})


Type: `string`  

### max_in_flight

The maximum number of messages to have in flight at a given time. Increase this to improve throughput.


Type: `int`  
Default: `64`  