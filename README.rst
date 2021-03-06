
========
InfluxDB
========

InfluxData is based on the TICK stack, the first open source platform for managing IoT time-series data at scale.

Sample pillars
==============

Single-node influxdb, enabled http frontend and admin web interface:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        http:
          enabled: true
          bind:
            address: 0.0.0.0
            port: 8086
        admin:
          enabled: true
          bind:
            address: 0.0.0.0
            port: 8083

Single-node influxdb, SSL for http frontend:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        http:
          bind:
            ssl:
              enabled: true
              key_file: /etc/influxdb/ssl/key.pem
              cert_file: /etc/influxdb/ssl/cert.pem

Single-node influxdb where you specify paths for data and metastore directories. You
need to ensure that directories exist:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        data:
          dir: '/opt/influxdb/data'
          wal_dir: '/opt/influxdb/wal'
        meta:
          dir: '/opt/influxdb/meta'

InfluxDB server with customized parameters for the data service:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        data:
          max_series_per_database: 20000000
          cache_max_memory_size: 524288000
          cache_snapshot_memory_size: 26214400
          cache_snapshot_write_cold_duration: "5m"
          compact_full_write_cold_duration: "2h"2h"
          max_values_per_tag: 5000

Single-node influxdb with an admin user:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        http:
          enabled: true
          bind:
            address: 0.0.0.0
            port: 8086
        admin:
          enabled: true
          bind:
            address: 0.0.0.0
            port: 8083
          user:
            enabled: true
            name: root
            password: secret

Single-node influxdb with new users:

.. code-block:: yaml

    influxdb:
      server:
        user:
          user1:
            enabled: true
            admin: true
            name: username1
            password: keepsecret1
          user2:
            enabled: true
            admin: false
            name: username2
            password: keepsecret2

Single-node influxdb with new databases:

.. code-block:: yaml

    influxdb:
      server:
        database:
          mydb1:
            enabled: true
            name: mydb1
          mydb2:
            enabled: true
            name: mydb2

Manage the retention policies for a database:

.. code-block:: yaml

    influxdb:
      server:
        database:
          mydb1:
            enabled: true
            name: mydb1
            retention_policy:
            - name: rp_db1
              duration: 30d
              replication: 1
              is_default: true

Where default values are:

* name = autogen
* duration = INF
* replication = 1
* is_default: false


Here is how to manage grants on database:

.. code-block:: yaml

    influxdb:
      server:
        grant:
          username1_mydb1:
            enabled: true
            user: username1
            database: mydb1
            privilege: all
          username2_mydb1:
            enabled: true
            user: username2
            database: mydb1
            privilege: read
          username2_mydb2:
            enabled: true
            user: username2
            database: mydb2
            privilege: write

InfluxDB relay:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
        http:
          enabled: true
          output:
            idb01:
              location: http://idb01.local:8086/write
              timeout: 10
            idb02:
              location: http://idb02.local:8086/write
              timeout: 10
        udp:
          enabled: true
          output:
            idb01:
              location: idb01.local:9096
            idb02:
              location: idb02.local:9096

InfluxDB cluster:

.. code-block:: yaml

    influxdb:
      server:
        enabled: true
      meta:
        bind:
          address: 0.0.0.0
          port: 8088
          http_address: 0.0.0.0
          http_port: 8091
      cluster:
        members:
          - host: idb01.local
            port: 8091
          - host: idb02.local
            port: 8091
          - host: idb03.local
            port: 8091

Deploy influxdb apt repository (using linux formula):

.. code-block:: yaml

    linux:
      system:
        os: ubuntu
        dist: xenial
        repo:
          influxdb:
            enabled: true
            source: 'deb https://repos.influxdata.com/${linux:system:os} ${linux:system:dist} stable'
            key_url: 'https://repos.influxdata.com/influxdb.key'

InfluxDB client for configuring databases, users and retention policies:

.. code-block:: yaml

    influxdb:
      client:
        enabled: true
        server:
          protocol: http
          host: 127.0.0.1
          port: 8086
          user: admin
          password: foobar
        user:
          user1:
            enabled: true
            admin: true
            name: username1
        database:
          mydb1:
            enabled: true
            name: mydb1
            retention_policy:
            - name: rp_db1
              duration: 30d
              replication: 1
              is_default: true
        grant:
          username1_mydb1:
            enabled: true
            user: username1
            database: mydb1
            privilege: all

InfluxDB relay with HTTP outputs:

.. code-block:: yaml

    influxdb:
      relay:
        enabled: true
        telemetry:
          enabled: true
          bind:
            address: 127.0.0.1
            port: 9196
        listen:
          http_backend:
            type: http
            bind:
              address: 127.0.0.1
              port: 9096
            output:
              server1:
                location: http://server1:8086/write
                timeout: 20s
                buffer_size_mb: 512
                max_batch_kb: 1024
                max_delay_interval: 30s
              server2:
                location: http://server2:8086/write
  
Read more
=========

* https://influxdata.com/time-series-platform/influxdb/

Documentation and Bugs
======================

To learn how to install and update salt-formulas, consult the documentation
available online at:

    http://salt-formulas.readthedocs.io/

In the unfortunate event that bugs are discovered, they should be reported to
the appropriate issue tracker. Use Github issue tracker for specific salt
formula:

    https://github.com/salt-formulas/salt-formula-influxdb/issues

For feature requests, bug reports or blueprints affecting entire ecosystem,
use Launchpad salt-formulas project:

    https://launchpad.net/salt-formulas

You can also join salt-formulas-users team and subscribe to mailing list:

    https://launchpad.net/~salt-formulas-users

Developers wishing to work on the salt-formulas projects should always base
their work on master branch and submit pull request against specific formula.

    https://github.com/salt-formulas/salt-formula-influxdb

Any questions or feedback is always welcome so feel free to join our IRC
channel:

    #salt-formulas @ irc.freenode.net
