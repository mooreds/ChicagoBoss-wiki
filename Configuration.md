All configuration takes place in <code>boss.config</code> in your project directory. Valid configuration options are:

* **assume_locale** - The presumed locale of translatable strings. Defaults to "en".
* **base_url** - The base URL of the application. Defaults to "/".

## Cache

* **cache_adapter** - The cache adapter to use. Currently the only valid value is memcached_bin.
* **cache_enable** - Whether to enable the cache. Defaults to _false_.
* **cache_servers** - A list of cache servers ({Host, Port, PoolSize}). Defaults to [{"localhost", 11211, 1}]. 
* **cache_exp_time** - The maximum time to keep a cache entry, in seconds. Defaults to 60.

## Database

* **db_host** - The hostname of the database. Defaults to "localhost".
* **db_port** - The port of the database. Defaults to 1978.
* **db_username** - The username used for connecting to the database (if needed).
* **db_password** - The password used for connecting to the database (if needed).
* **db_database** - The name of the database to connect to (if needed).
* **db_adapter** - The database adapter to use. Valid values are:
  * **mock** - In-memory (non-persistent) database, useful for testing
  * **mnesia** - Mnesia
  * **mongodb**- MongoDB
  * **mysql** - MySQL
  * **pgsql** - PostgreSQL
  * **riak** - Riak
  * **tyrant** - Tokyo Tyrant
* **db_write_mode** and **db_read_mode** - Options specific to the [[MongoDB Adapter]]
* **db_shards** - A list of proplists with per-shard database configuration. The proplists override the above options, and should contain two additional options:
  * **db_shard_models** - A list of models (atoms) which are stored on the shard.
  * **db_shard_id** - An atom used to identify the shard (required only for MySQL).
    * Example:
        <code><pre>{db_shards, [ 
            [ 
                {db_host, "localhost"}, 
                {db_adapter, mysql}, 
                {db_port, 3306}, 
                {db_username, "dbuser"}, 
                {db_password, "dbpass"}, 
                {db_database, "database"},
                {db_shard_id, shard_id_atom}, 
                {db_shard_models, [model_atom_1, model_atom_2, model_atom_3, etc]} 
            ] 
        ]}</pre></code>

## Logging

* **log_dir** - Directory in which to keep log files. Location is relative to the project directory. Defaults to "log".

## Mail

* **mail_driver** - The email delivery driver to use. Valid values are:
  * **boss_mail_driver_smtp** - SMTP delivery. If <code>mail_relay</code> is present, it is used as a relay, otherwise direct delivery is attempted.
  * **boss_mail_driver_mock** - A black hole, useful for testing.
* **mail_relay_host** - The relay server for SMTP mail deliveries.
* **mail_relay_username** - The username used for connecting to the SMTP relay (if needed).
* **mail_relay_password** - The password used for connecting to the SMTP relay (if needed).

## MessageQueue

* **master_node** - For distributed configurations, the name of the master node.
 The master node runs global services (incoming mail, message queue, events, sessions).
 Should be an atom, e.g. **somenode@somehost**.
 The node name is specified in the **-sname** or **-name** argument in the startup script.
* **mq_max_age** - Maximum age of messages in the [message queue], in seconds. Defaults to 60.

## Webserver

* **port** - The port to run the server on. Defaults to 8001.
* **server** - The HTTP server to use. Valid values are:
  * **mochiweb** - The [Mochiweb](http://code.google.com/p/mochiweb/) Web Server
  * **misultin** - The [Misultin](https://github.com/ostinelli/misultin) Web Server

## Sessions

* **session_adapter** Selects the session driver to use. Valid values:
  * **cache** - Store sessions in the configured cache servers. Requires **cache_enable** to be set to true.
  * **ets** (default)
  * **mnesia**
* **session_enable** - Whether to enable sessions. Defaults to **true**.
* **session_key**: Cookie key for sessions. Defaults to "_boss_session"
* **session_exp_time**: Expiration time for the session cookie. Defaults to 525600 
* **session_mnesia_nodes**: (optional, Mnesia sessions only) - List of Mnesia nodes, defaults to node()

## Application

* **smart_exceptions** - Enable [smart_exceptions](https://github.com/thomasl/smart_exceptions), which provide the line numbers of run-time errors. Defaults to **true** in development, **false** in production.

## Incoming Emails

* **smtp_server_enable** - Enable the SMTP server for incoming mail
* **smtp_server_address** - The address that the SMTP server should bind to. Defaults to {0, 0, 0, 0} (all interfaces).
* **smtp_server_domain** - The domain name of the SMTP server
* **smtp_server_port** - The port that the SMTP server should listen on. Defaults to 25.
* **smtp_server_protocol** - The protocol that the SMTP server should use. Valid values are:
  * **tcp** (default)
  * **ssl**

## SSL

* **ssl_enable** - Enable HTTP over SSL
* **ssl_options** - SSL options; see [ssl(3erl)](http://www.erlang.org/doc/man/ssl.html)
* [[https_example_setup|HTTPS Example Setup]]

*Warning:* From 0.5.2, the frontpage, default_action and default_actions configuration options has been moved to the [[new routing system|Routes]]