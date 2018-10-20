---
layout: post
title:  "Rabbitmq monitor"
date:   2018-10-19 21:00:05
categories: rabbitmq
tags:  rabbitmq monitor
excerpt: "Rabbitmq 监控"
---

Rabbitmq监控相关
====


  * 官方文档： http://www.rabbitmq.com/monitoring.html

# 1. Management UI

启用了rabbitmq_management 插件后，默认在 15672端口打开一个web服务 Management UI，登录进入后可以进行监控和管理。

![](/images/posts/2018/rabbitmq1.png)

上图Overview页面即可看到rabbitmq集群的总体运行情况，了解各种容量信息。

  * （1） 上半部为当前rabbitmq中间件的总体情况：排队的消息数量，连接、通道、路由、队列等对象的数量
  * （2） 节点系统资源使用情况，通过系统资源使用情况可了解集群总体的容量情况。

### 问题

目前 rabbitmq 中的 队列对象的数量超过了一万多个，vhost 也有 5400多个，连接36000多，Management UI 打开很慢需要等很久。

# 2. HTTP API

启用了rabbitmq_management 插件后，默认在 15672端口打开一个web服务，该服务除了web管理页面外，还提供有API接口。

## 2.1. HTTP API说明

   * http://rabbitmqhost:15672/api/
   * http://rabbitmqhost:15672/doc/stats.html

## 2.2. 使用范例

GET  /api/overview

通过 /api/overview 接口，我们可以了解节点的总体情况。返回结果类似management页面overview的第（1）部分。

```
$ curl -i -u username:password http://rabbitmqhost:15672/api/overview
HTTP/1.1 200 OK
Server: MochiWeb/1.1 WebMachine/1.10.0 (never breaks eye contact)
Date: Wed, 10 Oct 2018 00:12:31 GMT
Content-Type: application/json
Content-Length: 2285
Cache-Control: no-cache
{"management_version":"3.6.1","rates_mode":"basic",..............（略）
```

返回的json串为：

``` json
{"management_version":"3.6.1","rates_mode":"basic","exchange_types":[{"name":"fanout","description":"AMQP fanout exchange, as per the AMQP specification","enabled":true},{"name":"headers","description":"AMQP headers exchange, as per the AMQP specification","enabled":true},{"name":"direct","description":"AMQP direct exchange, as per the AMQP specification","enabled":true},{"name":"topic","description":"AMQP topic exchange, as per the AMQP specification","enabled":true}],"rabbitmq_version":"3.6.1","cluster_name":"rabbitmqhost","erlang_version":"18.2","erlang_full_version":"Erlang/OTP 18 [erts-7.2] [source] [64-bit] [smp:64:64] [async-threads:768] [hipe] [kernel-poll:true]","message_stats":{"publish":73583,"publish_details":{"rate":0.0},"ack":3152147,"ack_details":{"rate":0.0},"deliver_get":3156011,"deliver_get_details":{"rate":0.0},"confirm":29238,"confirm_details":{"rate":0.0},"redeliver":285,"redeliver_details":{"rate":0.0},"deliver":364238,"deliver_details":{"rate":0.0},"deliver_no_ack":3174,"deliver_no_ack_details":{"rate":0.0},"get":2788599,"get_details":{"rate":0.0}},"queue_totals":{"messages":120314,"messages_details":{"rate":0.0},"messages_ready":120305,"messages_ready_details":{"rate":0.0},"messages_unacknowledged":9,"messages_unacknowledged_details":{"rate":0.0}},"object_totals":{"consumers":625,"queues":10676,"exchanges":38055,"connections":36187,"channels":1965},"statistics_db_event_queue":4073948,"node":"rabbitmqhost","statistics_db_node":"rabbitmqhost","listeners":[{"node":"rabbit@rabbitmq_cluster01","protocol":"amqp","ip_address":"::","port":5672},{"node":"rabbit@rabbitmq_cluster02","protocol":"amqp","ip_address":"::","port":5672},{"node":"rabbit@rabbitmq_cluster01","protocol":"amqp/ssl","ip_address":"::","port":5671},{"node":"rabbit@rabbitmq_cluster02","protocol":"amqp/ssl","ip_address":"::","port":5671},{"node":"rabbit@rabbitmq_cluster01","protocol":"clustering","ip_address":"::","port":25672},{"node":"rabbit@rabbitmq_cluster02","protocol":"clustering","ip_address":"::","port":25672}],"contexts":[{"node":"rabbit@rabbitmq_cluster01","description":"RabbitMQ Management","path":"/","port":"15672"},{"node":"rabbit@rabbitmq_cluster02","description":"RabbitMQ Management","path":"/","port":"15672"}]}
```

![](/images/posts/2018/rabbitmq2.png)

### 问题

目前 rabbitmq 中的 队列对象的数量超过了一万多个，vhost 也有 5400多个，连接36000多，management 页面和 HTTP API 的执行效率都很低。打开很慢需要等很久。

# 3. rabbitmqctl

rabbitmqctl 是 rabbitmq自带的命令行工具，可以实现对rabbitmq进行管理和监控。

## 3.1. 用法说明

```
Usage:
rabbitmqctl [-n <node>] [-t <timeout>] [-q] <command> [<command options>]

Options:
    -n node
    -q
    -t timeout

Default node is "rabbit@server", where server is the local host. On a host
named "server.example.com", the node name of the RabbitMQ Erlang node will
usually be rabbit@server (unless RABBITMQ_NODENAME has been set to some
non-default value at broker startup time). The output of hostname -s is usually
the correct suffix to use after the "@" sign. See rabbitmq-server(1) for
details of configuring the RabbitMQ broker.

Quiet output mode is selected with the "-q" flag. Informational messages are
suppressed when quiet mode is in effect.

Operation timeout in seconds. Only applicable to "list" commands. Default is
"infinity".

Commands:
    stop [<pid_file>]
    stop_app
    start_app
    wait <pid_file>
    reset
    force_reset
    rotate_logs <suffix>

    join_cluster <clusternode> [--ram]
    cluster_status
    change_cluster_node_type disc | ram
    forget_cluster_node [--offline]
    rename_cluster_node oldnode1 newnode1 [oldnode2] [newnode2 ...]
    update_cluster_nodes clusternode
    force_boot
    sync_queue [-p <vhost>] queue
    cancel_sync_queue [-p <vhost>] queue
    purge_queue [-p <vhost>] queue
    set_cluster_name name

    add_user <username> <password>
    delete_user <username>
    change_password <username> <newpassword>
    clear_password <username>

            authenticate_user <username> <password>

    set_user_tags <username> <tag> ...
    list_users

    add_vhost <vhost>
    delete_vhost <vhost>
    list_vhosts [<vhostinfoitem> ...]
    set_permissions [-p <vhost>] <user> <conf> <write> <read>
    clear_permissions [-p <vhost>] <username>
    list_permissions [-p <vhost>]
    list_user_permissions <username>

    set_parameter [-p <vhost>] <component_name> <name> <value>
    clear_parameter [-p <vhost>] <component_name> <key>
    list_parameters [-p <vhost>]

    set_policy [-p <vhost>] [--priority <priority>] [--apply-to <apply-to>]
<name> <pattern>  <definition>
    clear_policy [-p <vhost>] <name>
    list_policies [-p <vhost>]

    list_queues [-p <vhost>] [<queueinfoitem> ...]
    list_exchanges [-p <vhost>] [<exchangeinfoitem> ...]
    list_bindings [-p <vhost>] [<bindinginfoitem> ...]
    list_connections [<connectioninfoitem> ...]
    list_channels [<channelinfoitem> ...]
    list_consumers [-p <vhost>]
    status
    environment
    report
    eval <expr>

    close_connection <connectionpid> <explanation>
    trace_on [-p <vhost>]
    trace_off [-p <vhost>]
    set_vm_memory_high_watermark <fraction>
    set_vm_memory_high_watermark absolute <memory_limit>
    set_disk_free_limit <disk_limit>
    set_disk_free_limit mem_relative <fraction>

<vhostinfoitem> must be a member of the list [name, tracing].

The list_queues, list_exchanges and list_bindings commands accept an optional
virtual host parameter for which to display results. The default value is "/".

<queueinfoitem> must be a member of the list [name, durable, auto_delete,
arguments, policy, pid, owner_pid, exclusive, exclusive_consumer_pid,
exclusive_consumer_tag, messages_ready, messages_unacknowledged, messages,
messages_ready_ram, messages_unacknowledged_ram, messages_ram,
messages_persistent, message_bytes, message_bytes_ready,
message_bytes_unacknowledged, message_bytes_ram, message_bytes_persistent,
head_message_timestamp, disk_reads, disk_writes, consumers,
consumer_utilisation, memory, slave_pids, synchronised_slave_pids, state].

<exchangeinfoitem> must be a member of the list [name, type, durable,
auto_delete, internal, arguments, policy].

<bindinginfoitem> must be a member of the list [source_name, source_kind,
destination_name, destination_kind, routing_key, arguments].

<connectioninfoitem> must be a member of the list [pid, name, port, host,
peer_port, peer_host, ssl, ssl_protocol, ssl_key_exchange, ssl_cipher,
ssl_hash, peer_cert_subject, peer_cert_issuer, peer_cert_validity, state,
channels, protocol, auth_mechanism, user, vhost, timeout, frame_max,
channel_max, client_properties, recv_oct, recv_cnt, send_oct, send_cnt,
send_pend, connected_at].

<channelinfoitem> must be a member of the list [pid, connection, name, number,
user, vhost, transactional, confirm, consumer_count, messages_unacknowledged,
messages_uncommitted, acks_uncommitted, messages_unconfirmed, prefetch_count,
global_prefetch_count].
```

## 3.2. rabbitmqctl status

以上命令可以很快返回节点系统总体状态，返回结果里有内存、文件描述符、磁盘空间等资源的使用情况，相当于management页面overview的第（2）部分。

通过对相关内存、文件描述符、磁盘空间等资源的使用情况进行监控，我们可以及时掌握rabbitmq的总体容量情况，当资源不足时及时报警。

```bash
$ rabbitmqctl status

Status of node rabbitmqhost ...
[{pid,12345},
 {running_applications,
     [{rabbitmq_management,"RabbitMQ Management Console","3.6.1"},
      {rabbitmq_management_agent,"RabbitMQ Management Agent","3.6.1"},
      {rabbit,"RabbitMQ","3.6.1"},
      {amqp_client,"RabbitMQ AMQP Client","3.6.1"},
      {rabbitmq_web_dispatch,"RabbitMQ Web Dispatcher","3.6.1"},
      {webmachine,"webmachine","1.10.3"},
      {rabbit_common,[],"3.6.1"},
      {mochiweb,"MochiMedia Web Server","2.13.0"},
      {ssl,"Erlang/OTP SSL application","7.2"},
      {public_key,"Public key infrastructure","1.1"},
      {crypto,"CRYPTO","3.6.2"},
      {xmerl,"XML parser","1.3.9"},
      {os_mon,"CPO  CXC 138 46","2.4"},
      {mnesia,"MNESIA  CXC 138 12","4.13.2"},
      {syntax_tools,"Syntax tools","1.7"},
      {ranch,"Socket acceptor pool for TCP protocols.","1.2.1"},
      {inets,"INETS  CXC 138 49","6.1"},
      {asn1,"The Erlang ASN1 compiler version 4.0.1","4.0.1"},
      {compiler,"ERTS  CXC 138 10","6.0.2"},
      {sasl,"SASL  CXC 138 11","2.6.1"},
      {stdlib,"ERTS  CXC 138 10","2.7"},
      {kernel,"ERTS  CXC 138 10","4.1.1"}]},
 {os,{unix,linux}},
 {erlang_version,
     "Erlang/OTP 18 [erts-7.2] [source] [64-bit] [smp:64:64] [async-threads:768] [hipe] [kernel-poll:true]\n"},
 {memory,
     [{total,5515794552},
      {connection_readers,850979240},
      {connection_writers,3377232},
      {connection_channels,12324200},
      {connection_other,1028799136},
      {queue_procs,352967664},
      {queue_slave_procs,8481944},
      {plugins,10036440},
      {other_proc,112254856},
      {mnesia,107768400},
      {mgmt_db,2808},
      {msg_index,17941376},
      {other_ets,25702016},
      {binary,2797202984},
      {code,30976673},
      {atom,1090729},
      {other_system,155888854}]},
 {alarms,[]},
 {listeners,[{clustering,25672,"::"},{amqp,5672,"::"},{'amqp/ssl',5671,"::"}]},
 {vm_memory_high_watermark,0.8},
 {vm_memory_limit,216626436505},
 {disk_free_limit,135391522816},
 {disk_free,222246518784},
 {file_descriptors,
     [{total_limit,102300},
      {total_used,32652},
      {sockets_limit,92068},
      {sockets_used,28780}]},
 {processes,[{limit,1048576},{used,250368}]},
 {run_queue,0},
 {uptime,6088508},
 {kernel,{net_ticktime,60}}]
```
