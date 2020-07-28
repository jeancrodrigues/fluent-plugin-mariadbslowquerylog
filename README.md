# fluent-plugin-mysqlslowquerylog, a plugin for [Fluentd](http://fluentd.org)

## Component

### MySQL Slow Query Log Output

Fluentd plugin to concat MariaDB slowquerylog. Forked from [fluent-plugin-mysqlslowquerylog](https://github.com/studio3104/fluent-plugin-mysqlslowquerylog). Changed to log format for MariaDB version 10.3.

## Configuration

Input Messages (Slow Query Log)
```
# Time: 130107 11:36:21
# User@Host: root[root] @ localhost []
# Thread_id: 16420  Schema: someschema  QC_hit: No
# Query_time: 0.000378  Lock_time: 0.000111 Rows_sent: 7  Rows_examined: 7
# Rows_affected: 0  Bytes_sent: 4851
# Full_scan: Yes  Full_join: Yes  Tmp_table: No  Tmp_table_on_disk: No
# Filesort: No  Filesort_on_disk: No  Merge_passes: 0  Priority_queue: No
SET timestamp=1357526181;
select * from user;
```

Output Messages
```
{"user":"root[root]",
"host":"[127.0.0.1]",
"thread_id":16420,
"schema":"someschema",
"qc_hit":"No",
"query_time":18.436932,
"lock_time":4.9e-05,
"rows_sent":0,
"rows_examined":849,
"rows_affected":0,
"bytes_sent":4851,
"full_scan":"Yes",
"full_join":"Yes",
"temp_table":"No",
"temp_table_on_disk":"No",
"filesort":"No",
"filesort_on_disk":"No",
"merge_passes":0,
"priority_queue":"No",
"sql":"select * from user u;"}
```

### Example Settings
sender (td-agent)
```
<source>
  type tail
  path   /var/log/mysql/mariadb-slow.log
  format /^(?<message>.+)$/
  tag    mysql.slowlog.db01
</source>
<match>
  type forward
  host log_server
</match>
```

reciever
```
<source>
  type forward
</source>
<match mysql.slowlog.*>
  type mysqlslowquerylog
  add_tag_prefix processed.
</match>
<match processed.mysql.slowlog.*>
  type file
  path /tmp/slowtest
</match>
```

## Installation

Install it yourself as:

    $ gem install fluent-plugin-mysqlslowquerylog

Or if you are using the td-agent install with:
	
    $ td-agent-gem install  fluent-plugin-mysqlslowquerylog

### License
Apache License, Version 2.0
