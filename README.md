# emqtt

[![Hex Version](https://img.shields.io/hexpm/v/emqtt.svg)](https://hex.pm/packages/emqtt)
[![Hex Docs](https://img.shields.io/badge/hex-docs-informational.svg)](https://hexdocs.pm/emqtt)
[![License](https://img.shields.io/hexpm/l/emqtt.svg)](https://hex.pm/packages/emqtt)

MQTT client library implemented in Erlang that supports MQTT v5.0/3.1.1/3.1.

# Getting started

## As a Dependency Library

### Add to rebar3 project

Add to `rebar.config`

```erlang
...
{deps, [{emqtt, "~> 1.14"}]}.
...
```

### Install from Hex

```erlang
...
{deps, [{emqtt, "~> 1.14"}]}.
...
```

### Build

```
$ rebar3 compile
```

### Data Types

<span id="option">**option()**</span>

```
option() = {name, atom()} |
           {owner, pid()} |
           {host, host()} |
           {hosts, [{host(), inet:port_number()}]} |
           {port, inet:port_number()} |
           {tcp_opts, [gen_tcp:option()]} |
           {ssl, boolean()} |
           {ssl_opts, [ssl:tls_client_option()]} |

           {ws_path, string()} |
           {connect_timeout, pos_integer()} |
           {bridge_mode, boolean()} |
           {clientid, iodata()} |
           {clean_start, boolean()} |
           {username, iodata()} |
           {password, iodata()} |
           {proto_ver, v3 | v4 | v5} |
           {keepalive, non_neg_integer()} |
           {max_inflight, pos_integer()} |
           {retry_interval, pos_integer()} |
           {will_topic, iodata()} |
           {will_payload, iodate()} |
           {will_retain, boolean()} |
           {will_qos, qos()} |
           {will_props, properties()} |
           {auto_ack, boolean()} |
           {ack_timeout, pos_integer()} |
           {force_ping, boolean()} |
           {low_mem, boolean()} |
           {reconnect, infinity | non_neg_integer()} |
           {reconnect_timeout, pos_integer()} |
           {properties, properties()} |
           {custom_auth_callbacks, map()}
```

<span id="client">**client()**</span>

```
client() = pid() | atom()
```

<span id="host">**host()**</span>

```
host() = inet:ip_address() | inet:hostname()
```

<span id="properties">**properties()**</span>

```
properties() = #{'Payload-Format-Indicator' => 0..1,
                 'Message-Expiry-Interval' => 0..16#FFFFFFFF,
                 'Content-Type' => binary(),
                 'Response-Topic' => binary(),
                 'Correlation-Data' => binary(),
                 'Subscription-Identifier' => 1..16#FFFFFFF | [1..16#FFFFFFF, ...],
                 'Session-Expiry-Interval' => 0..16#FFFFFFFF,
                 'Assigned-Client-Identifier' => binary(),
                 'Server-Keep-Alive' => 0..16#FFFF,
                 'Authentication-Method' => binary(),
                 'Authentication-Data' => binary(),
                 'Request-Problem-Information' => 0..1,
                 'Will-Delay-Interval' => 0..16#FFFFFFFF,
                 'Request-Response-Information' => 0..1,
                 'Response-Information' => binary(),
                 'Server-Reference' => binary(),
                 'Reason-String' => binary(),
                 'Receive-Maximum' => 1..16#FFFF,
                 'Topic-Alias-Maximum' => 0..16#FFFF,
                 'Topic-Alias' => 1..16#FFFF,
                 'Maximum-QoS' => 0..1,
                 'Retain-Available' => 0..1,
                 'User-Property' => [{binary(), binary()}],
                 'Maximum-Packet-Size' => 1..16#FFFFFFFF,
                 'Wildcard-Subscription-Available' => 0..1,
                 'Subscription-Identifier-Available' => 0..1,
                 'Shared-Subscription-Available' => 0..1}
```

<span id="qos">**qos()**</span>

```
qos() = 0 | 1 | 2
```

**qos_name()**

```
qos_name() = qos0 | at_most_once |
             qos1 | at_least_once |
             qos2 | exactly_once
```

<span id="topic">**topic()**</span>

```
topic() = binary()
```

<span id="payload">**payload()**</span>

```
payload() = iodata()
```

<span id="packetid">**packet_id()**</span>

```
packet_id() = 0..16#FFFF
```

<span id="subopt">**subopt()**</span>

```
subopt() = {rh, 0 | 1 | 2} |
           {rap, boolean()} |
           {nl, boolean()} |
           {qos, qos() | qos_name()}
```

<span id="pubopt">**pubopt()**</span>

```
pubopt() = {retain, boolean()} |
           {qos, qos() | qos_name()}
```

<span id="reasoncode">**reason_code()**</span>

```
reason_code() = 0..16#FF
```

### Exports

**emqtt:start_link() -> {ok, Pid} | ignore | {error, Reason}**

**emqtt:start_link(Options) -> {ok, Pid} | ignore | {error, Reason}**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Pid = pid()**

&ensp;&ensp;&ensp;&ensp;**Reason = term()**

&ensp;&ensp;&ensp;&ensp;**Options = [[option()](#option)]**

Start MQTT client process with specified options. `Options` will be used in connecting and running.

The following options are available:

`{name, Name}`

If a name is provided, the gen_statem will be registered with this name. For details see the documentation for the first argument of `gen_statem:start_link/4`.

`{owner, Pid}`

Client process will send messages like `{diconnected, ReasonCode, Properties}` to the owner process.

`{host, Host}`

The host of the MQTT server to be connected. Host can be a hostname or an IP address. Defaults to `localhost`

`{hosts, [{Host, Port}]}`

A list of hosts to connect to. If the connection to the first host fails, the client will try the next host in the list. If the connection to all hosts fails, the client will return an error. Setting this option will override the `host` option.

`{port, Port}`

The port of the MQTT server to be connected. If not given, the default of 1883 for MQTT or 8883 for MQTT over TLS will be used.

`{tcp_opts, Options}`

Additional options for `gen_tcp:connect/3`.

`{ssl, boolean()}`

Enable SSL/TLS transport or not. Defaults to false.

`{ssl_opts, Options}`

Additional options for `ssl:connect/3`.

`{ws_path, Path}`

Path to the resource. Defaults to `/mqtt`

`{connect_timeout, Timeout}`

The maximum time to wait to connect to the server and the server returns a CONNACK. Defaults to 60s.

`{bridge_mode, boolean()}`

Enable bridge mode or not. Defaults to false.

`{clientid, ClientID}`

Specify the client identifier. If not given, the client identifier will use the value assigned by the server in MQTT v5 or be automatically generated by internal code in MQTT v3.1/v3.1.1.

`{clean_start, CleanStart}`

Whether the server should discard any existing session and start a new session. Defaults to true.

`{username, Username}`

Username used by the server for authentication and authorization.

`{password, Password}`

Password used by the server for authentication and authorization.

`{proto_ver, ProtocolVersion}`

MQTT protocol version. Defaults to `v4`.

`{keepalive, Keepalive}`

Maximum time interval that is permitted to elapse between the point at which the Client finishes transmitting one MQTT Control Packet and the point it starts sending the next. It will be replaced by server keep alive returned from MQTT server.

`{max_inflight, MaxInflight}`

Max number of QoS 1 and QoS 2 packets in flight. In other words, the number of packets that were sent but not yet acked. Defaults to `infinity`, which means there's no hard limit. However, Server may have its own idea and advertise it through the `Receive-Maximum` property in a `CONNACK` packet. In that case the lesser of the two values will act as the limit.

Once inflight window is full, messages will be queued in the Client process and sent when slots in the inflight window become available. There's no limit on the queue size, so flow control measures and/or setting reasonable publish timeouts are recommended if you expect the Server's throughput to be insufficient.

`{retry_interval, RetryInterval}`

Interval to retry sending packets that have been sent but not received a response. Defaults to 30s.

`{will_topic, WillTopic}`

Topic of will message.

`{will_payload, WillPayload}`

Payload of will message.

`{will_retain, WillRetain}`

Whether the server should publish the will message as a retained message. Defaults to false.

`{will_qos, WillQoS}`

QoS of will message. Defaults to 0.

`{will_props, WillProperties}`

Properties of will message.

`{auto_ack, boolean()}`

If true (the default), cliean process will automatically send ack packet like PUBACK when it receives a packet from the server. If false, application decides what to do.

`{ack_timeout, AckTimeout}`

Maximum time to wait for a reply message. Defaults to 30s.

`{force_ping, boolean()}`

If false (the default), if any other packet is sent during keep alive interval, the ping packet will not be sent this time. If true, the ping packet will be sent every time.

`{low_mem, boolean()}`

If true, the client will try to reduce memory usage by garbage collecting more frequently. Defaults to false.

`{reconnect, infinity | non_neg_integer()}`

The maximum number of reconnection attempts. Defaults to 0, means no reconnection.

`{reconnect_timeout, pos_integer()}`

The time interval between reconnection attempts. Defaults to 5s.

`{properties, Properties}`

Properties of CONNECT packet.

`{custom_auth_callbacks, Callbacks}`

This configuration option enables enhanced authentication mechanisms in MQTT v5 by specifying custom callback functions.

See [Enhanced Authentication](#EnhancedAuthentication) below for more details.

**emqtt:connect(Client) -> {ok, Properties} | {error, Reason}**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

&ensp;&ensp;&ensp;&ensp;**Reason = timeout | inet:posix() | any()**

Connect to the MQTT server over TCP or TLS and send a `CONNECT` packet with the options specified in `start_link/1, 2`. `Client` must be a pid returned from `start_link/1, 2` or a name specified in `start_link/1, 2`.

Returns:

- `{ok, Properties}` if a MQTT connection is established. `Properties` is propterties in CONNACK packet returned from MQTT server.

- `{error, timeout}` if connection can't be established within the specified time

- `{error, inet:posix()}` A POSIX error value if something else goes wrong.

**emqtt:ws_connect(Client) -> {ok, Properties} | {error, Reason}**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;Same as `emqtt:connect/1`

Connect to the MQTT server over Websocket and send a `CONNECT` packet with the options specified in `start_link/1, 2`. `Client` must be a pid returned from `start_link/1, 2` or a name specified in `start_link/1, 2`.

**emqtt:disconnect(Client) -> ok | {error, Reason}**

**emqtt:disconnect(Client, ReasonCode) -> ok | {error, Reason}**

**emqtt:disconnect(Client, ReasonCode, Properties) -> ok | {error, Reason}**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**ReasonCode = [reason_code()](#reasoncode)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

&ensp;&ensp;&ensp;&ensp;**Reason = closed | inet:posix()**

Send a `DISCONNECT` packet to the MQTT server. `ReasonCode` specifies a MQTT reason code for `DISCONNECT` packet, defaults to 0 meaning normal disconnection. `Properties` specifies properties for `DISCONNECT` packet, defaults to `#{}` meaning no properties are attached.

**emqtt:ping(Client) -> pong | {error, Reason}**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Reason = ack_timeout**

Send a `PINGREQ` packet to the MQTT server. If `PINGRESP` packet is received from the server within the timeout period, `pong` is returned. If not, `{error, ack_timeout}` is returned. 

**emqtt:subscribe(Client, Properties, Subscriptions) -> {ok, Properties, ReasonCodes} | {error, Reason})**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

&ensp;&ensp;&ensp;&ensp;**Subscriptions = [{[topic()](#topic), [[subopt()](#subopt)]}]**

&ensp;&ensp;&ensp;&ensp;**ReasonCodes = [[reason_code()](#reasoncode)]**

&ensp;&ensp;&ensp;&ensp;**Reason = term()**

Send a `SUBSCRIBE` packet to the MQTT server. `Properties` specifies properties for `SUBSCRIBE` packet, defaults to `#{}` meaning no properties are attached. `Subscriptions` specifies pairs of topic filter and subscription options. The topic filter is requried, the subscription options can be `[]`, equivalent to `[{rh, 0}, {rap, 0}, {nl, 0}, {qos, 0}]`.

**emqtt:unsubscribe(Client, Properties, Topics) -> {ok, Properties, ReasonCodes} | {error, Reason})**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

&ensp;&ensp;&ensp;&ensp;**Topics = [[topic()](#topic)]**

&ensp;&ensp;&ensp;&ensp;**ReasonCodes = [[reason_code()](#reasoncode)]**

&ensp;&ensp;&ensp;&ensp;**Reason = term()**

Send a `UNSUBSCRIBE` packet to the MQTT server. `Properties` specifies properties for `SUBSCRIBE` packet, defaults to `#{}` meaning no properties are attached. `Topics` specifies a list of topic filter with at least one topic filter.

**emqtt:publish(Client, Topic, Properties, Payload, PubOpts) -> ok | {ok, PacketId} | {error, Reason})**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Topic = [topic()](#topic)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

&ensp;&ensp;&ensp;&ensp;**Payload = [payload()](#payload)**

&ensp;&ensp;&ensp;&ensp;**PubOpts = [[pubopt()](#pubopt)]**

&ensp;&ensp;&ensp;&ensp;**PacketId = [packet_id()](#packetid)**

&ensp;&ensp;&ensp;&ensp;**Reason = term()**

Send a `PUBLISH` packet to the MQTT server. `Topic`, `Properties` and `Payload` specify topic, properties and payload for `PUBLISH` packet. `PubOpts` specifies qos and retain flag for `PUBLISH` packet, defaults to `[]`, equivalent to `[{qos, 0}, {retain, false}]`.

Returns:

- `ok` Ii a QoS 0 packet is sent.

- `{ok, PacketId}` if a QoS 1/2 packet is sent, the packet identifier will be returned.

- `{error, Reason}` if something goes wrong.

**emqtt:puback(Client, PacketId) -> ok**

**emqtt:puback(Client, PacketId, ReasonCode) -> ok**

**emqtt:puback(Client, PacketId, ReasonCode, Properties) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**PacketId = [packet_id()](#packetid)**

&ensp;&ensp;&ensp;&ensp;**ReasonCode = [reason_code()](#reasoncode)**

&ensp;&ensp;&ensp;&ensp;**Properties = [properties()](#properties)**

Send a `PUBACK` packet to the MQTT server. `PacketId`, `ReasonCode` and `Properties` specify packet identifier, reason code and properties for `PUBACK` packet.

**emqtt:pubrec(Client, PacketId) -> ok**

**emqtt:pubrec(Client, PacketId, ReasonCode) -> ok**

**emqtt:pubrec(Client, PacketId, ReasonCode, Properties) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;Same as `emqtt:puback/2, 3, 4`.

Send a `PUBREC` packet to the MQTT server. `PacketId`, `ReasonCode` and `Properties` specify packet identifier, reason code and properties for `PUBREC` packet.

**emqtt:pubrel(Client, PacketId) -> ok**

**emqtt:pubrel(Client, PacketId, ReasonCode) -> ok**

**emqtt:pubrel(Client, PacketId, ReasonCode, Properties) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;Same as `emqtt:puback/2, 3, 4`.

Send a `PUBREL` packet to the MQTT server. `PacketId`, `ReasonCode` and `Properties` specify packet identifier, reason code and properties for `PUBREL` packet.

**emqtt:pubcomp(Client, PacketId) -> ok**

**emqtt:pubcomp(Client, PacketId, ReasonCode) -> ok**

**emqtt:pubcomp(Client, PacketId, ReasonCode, Properties) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;Same as `emqtt:puback/2, 3, 4`.

Send a `PUBCOMP` packet to the MQTT server. `PacketId`, `ReasonCode` and `Properties` specify packet identifier, reason code and properties for `PUBCOMP` packet.

**emqtt:subscriptions(Client) -> Subscriptions**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

&ensp;&ensp;&ensp;&ensp;**Subscriptions = [{[topic()](#topic), [[subopt()](#subopt)]}]**

Return all subscriptions.

**emqtt:stop(Client) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

Stop a client process.

**emqtt:pause(Client) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

Pause the client process. The paused client process will ignore all `PUBLISH` packets received and not send `PINGREQ` packet if `force_ping` is set to false.

**emqtt:resume(Client) -> ok**

&ensp;&ensp;**Types**

&ensp;&ensp;&ensp;&ensp;**Client = [client()](#client)**

Resume a client process from a paused state.

### Examples

```
{ok, ConnPid} = emqtt:start_link([{clientid, ClientId}]).
{ok, _Props} = emqtt:connect(ConnPid).

SubOpts = [{qos, 1}].
{ok, _Props, _ReasonCodes} = emqtt:subscribe(ConnPid, #{}, [{<<"hello">>, SubOpts}]).

ok = emqtt:publish(ConnPid, <<"hello">>, #{}, <<"Hello World!">>, [{qos, 0}]).
{ok, _PktId} = emqtt:publish(ConnPid, <<"hello">>, #{}, <<"Hello World!">>, [{qos, 1}]).

receive
    {disconnect, ReasonCode, Properties} ->
        io:format("Recv a DISONNECT packet - ReasonCode: ~p, Properties: ~p~n", [ReasonCode, Properties]);
    {publish, PUBLISH} ->
        io:format("Recv a PUBLISH packet: ~p~n", [PUBLISH]);
    {puback, {PacketId, ReasonCode, Properties}} ->
        io:format("Recv a PUBACK packet - PacketId: ~p, ReasonCode: ~p, Properties: ~p~n", [PacketId, ReasonCode, Properties])
end.

{ok, _Props, _ReasonCode} = emqtt:unsubscribe(ConnPid, #{}, <<"hello">).

ok = emqtt:disconnect(ConnPid).
ok = emqtt:stop(ConnPid).
```
## Enhanced Authentication

As a MQTT client CLI, `emqtt` currently does not support enhanced authentication.

As a MQTT client library, `emqtt` supports enhanced authentication with caller provided
callbacks.

The callbacks should be provided as a `start_link` option `{custom_auth_callbacks, Callbacks}`,
where the `Callbacks` parameter should be a map structured as follows:

```erlang
#{
  init => {InitFunc :: function(), InitArgs :: list()},
  handle_auth => HandleAuth :: function()
}.
```


### InitFunc

This function is executed with InitArgs as arguments. It must return a tuple `{AuthProps, AuthState}`, where:

- `AuthProps` is a map containing the initial authentication properties, including `'Authentication-Method'` and `'Authentication-Data'`.

- `AuthState` is a term that is used in subsequent authentication steps.

### HandleAuth

This function is responsible for handling the continuation of the authentication process. It accepts the following parameters:

- `AuthState`: The current state of authentication.
- `continue_authentication | ErrorCode`: A directive to continue authentication or an error code indicating the failure reason.
- `AuthProps`: A map containing properties for authentication, which must always include `'Authentication-Method'` and `'Authentication-Data'` at each step of the authentication process.

The function should return a tuple in the form of: `{continue, {?RC_CONTINUE_AUTHENTICATION, AuthProps}, AuthState}` or `{stop, Reason}` to abort.

### Examples

For practical implementations of these callbacks, refer to the following test suites in this repository:

- `test/emqtt_scram_auth_SUITE.erl`
- `test/emqtt_kerberos_auth_SUITE.erl`

These examples demonstrate how to configure the authentication callbacks for different SASL mechanisms supported by EMQTT.
