# MySQL Server Exporter

[![Build Status](https://travis-ci.org/percona/mysqld_exporter.svg?branch=master)](https://travis-ci.org/percona/mysqld_exporter)
[![Go Report Card](https://goreportcard.com/badge/github.com/percona/mysqld_exporter)](https://goreportcard.com/report/github.com/percona/mysqld_exporter)

Prometheus exporter for MySQL server metrics.
Supported MySQL versions: 5.1 and up.
NOTE: Not all collection methods are supported on MySQL < 5.6

## Building and running

### Required Grants

    CREATE USER 'exporter'@'localhost' IDENTIFIED BY 'XXXXXXXX';
    GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost'
      WITH MAX_USER_CONNECTIONS 3;

NOTE: It is recommended to set a max connection limit for the user to avoid overloading the server with monitoring scrapes under heavy load.

### Build

    make

### Running

Running using an environment variable:

    export DATA_SOURCE_NAME='login:password@(hostname:port)/'
    ./mysqld_exporter <flags>

Running using ~/.my.cnf:

    ./mysqld_exporter <flags>

### Collector Flags

Name                                                   | MySQL Version | Description
-------------------------------------------------------|---------------|------------------------------------------------------------------------------------
collect.auto_increment.columns                         | 5.1           | Collect auto_increment columns and max values from information_schema.
collect.binlog_size                                    | 5.1           | Collect the current size of all registered binlog files
collect.engine_innodb_status                           | 5.1           | Collect from SHOW ENGINE INNODB STATUS.
collect.engine_tokudb_status                           | 5.6           | Collect from SHOW ENGINE TOKUDB STATUS.
collect.global_status                                  | 5.1           | Collect from SHOW GLOBAL STATUS (Enabled by default)
collect.global_variables                               | 5.1           | Collect from SHOW GLOBAL VARIABLES (Enabled by default)
collect.info_schema.clientstats                        | 5.5           | If running with userstat=1, set to true to collect client statistics.
collect.info_schema.innodb_metrics                     | 5.6           | Collect metrics from information_schema.innodb_metrics.
collect.info_schema.innodb_tablespaces                 | 5.7           | Collect metrics from information_schema.innodb_sys_tablespaces.
collect.info_schema.processlist                        | 5.1           | Collect thread state counts from information_schema.processlist.
collect.info_schema.processlist.min_time               | 5.1           | Minimum time a thread must be in each state to be counted. (default: 0)
collect.info_schema.query_response_time                | 5.5           | Collect query response time distribution if query_response_time_stats is ON.
collect.info_schema.tables                             | 5.1           | Collect metrics from information_schema.tables (Enabled by default)
collect.info_schema.tables.databases                   | 5.1           | The list of databases to collect table stats for, or '`*`' for all.
collect.info_schema.tablestats                         | 5.1           | If running with userstat=1, set to true to collect table statistics.
collect.info_schema.userstats                          | 5.1           | If running with userstat=1, set to true to collect user statistics.
collect.perf_schema.eventsstatements                   | 5.6           | Collect metrics from performance_schema.events_statements_summary_by_digest.
collect.perf_schema.eventsstatements.digest_text_limit | 5.6           | Maximum length of the normalized statement text. (default: 120)
collect.perf_schema.eventsstatements.limit             | 5.6           | Limit the number of events statements digests by response time. (default: 250)
collect.perf_schema.eventsstatements.timelimit         | 5.6           | Limit how old the 'last_seen' events statements can be, in seconds. (default: 86400)
collect.perf_schema.eventswaits                        | 5.5           | Collect metrics from performance_schema.events_waits_summary_global_by_event_name.
collect.perf_schema.file_events                        | 5.6           | Collect metrics from performance_schema.file_summary_by_event_name.
collect.perf_schema.indexiowaits                       | 5.6           | Collect metrics from performance_schema.table_io_waits_summary_by_index_usage.
collect.perf_schema.tableiowaits                       | 5.6           | Collect metrics from performance_schema.table_io_waits_summary_by_table.
collect.perf_schema.tablelocks                         | 5.6           | Collect metrics from performance_schema.table_lock_waits_summary_by_table.
collect.slave_status                                   | 5.1           | Collect from SHOW SLAVE STATUS (Enabled by default)


### General Flags
Name                                       | Description
-------------------------------------------|--------------------------------------------------------------------------------------------------
config.my-cnf                              | Path to .my.cnf file to read MySQL credentials from. (default: `~/.my.cnf`)
log.level                                  | Logging verbosity (default: info)
log_slow_filter                            | Add a log_slow_filter to avoid exessive MySQL slow logging.  NOTE: Not supported by Oracle MySQL.
web.listen-address                         | Address to listen on for web interface and telemetry.
web.telemetry-path                         | Path under which to expose metrics.
version                                    | Print the version information.

### Setting the MySQL server's data source name

The MySQL server's [data source name](http://en.wikipedia.org/wiki/Data_source_name)
must be set via the `DATA_SOURCE_NAME` environment variable.
The format of this variable is described at https://github.com/go-sql-driver/mysql#dsn-data-source-name.

## Using Docker

You can deploy this exporter using the [prom/mysqld-exporter](https://registry.hub.docker.com/u/prom/mysqld-exporter/) Docker image.

For example:

```bash
docker pull prom/mysqld-exporter

docker run -d -p 9104:9104 --link=my_mysql_container:bdd  \
        -e DATA_SOURCE_NAME="user:password@(bdd:3306)/database" prom/mysqld-exporter
```

## Example Rules

There are some sample rules available in [example.rules](example.rules)

[circleci]: https://circleci.com/gh/prometheus/mysqld_exporter
[hub]: https://hub.docker.com/r/prom/mysqld-exporter/
[travis]: https://travis-ci.org/prometheus/mysqld_exporter
[quay]: https://quay.io/repository/prometheus/mysqld-exporter

## Visualize

There is a Grafana dashboard for MySQL available as a part of [PMM](https://www.percona.com/doc/percona-monitoring-and-management/index.html) project, you can see the demo [here](https://pmmdemo.percona.com/graph/dashboard/db/mysql-overview).

## Submit Bug Report
If you find a bug in Percona MySQL Exporter or one of the related projects, you should submit a report to that project's [JIRA](https://jira.percona.com) issue tracker.

Your first step should be to search the existing set of open tickets for a similar report. If you find that someone else has already reported your problem, then you can upvote that report to increase its visibility.

If there is no existing report, submit a report following these steps:

1.  [Sign in to Percona JIRA.](https://jira.percona.com/login.jsp) You will need to create an account if you do not have one.
2.  [Go to the Create Issue screen and select the relevant project.](https://jira.percona.com/secure/CreateIssueDetails!init.jspa?pid=11600&issuetype=1&priority=3&components=11709)
3.  Fill in the fields of Summary, Description, Steps To Reproduce, and Affects Version to the best you can. If the bug corresponds to a crash, attach the stack trace from the logs.

An excellent resource is [Elika Etemad's article on filing good bug reports.](http://fantasai.inkedblade.net/style/talks/filing-good-bugs/).

As a general rule of thumb, please try to create bug reports that are:

-   *Reproducible.* Include steps to reproduce the problem.
-   *Specific.* Include as much detail as possible: which version, what environment, etc.
-   *Unique.* Do not duplicate existing tickets.
-   *Scoped to a Single Bug.* One bug per report.

