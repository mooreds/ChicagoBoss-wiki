In the case you are using Mnesia as a database backend for ChicagoBoss and boss_db, you might want to automatically initialize Mnesia schema and it's tables as you implement new data models.

This work could be done by creating something like `priv/init/my_application_util.erl` that may look like this:

    -module (my_application_util).
    -compile (export_all).
    -define (APPNAME, my_application). % is it possible to get it automatically somewhere in CB?
    
    init() ->
      init_db (),
      ok.
    
    init_db () ->
      init_db ([node ()]). % only for local node? what about nodes()++[node()]?
    init_db (Nodes) ->
      mnesia:create_schema (Nodes),
      mnesia:change_table_copy_type (schema, node(), disc_copies), % only for local node?
      mnesia:start (),
      ModelList = [ list_to_atom (M) || M <- boss_files:model_list (?APPNAME) ],
      ExistingTables = mnesia:system_info(tables),
      Tables = (ModelList ++ ['_ids_']) -- ExistingTables,
      create_model_tables (Nodes, Tables).
    
    % create all the tables
    create_model_tables (_, []) -> ok;
    create_model_tables (Nodes, [Model | Models]) ->
      [create_model_table (Nodes, Model)] ++
       create_model_tables (Nodes, Models).
    
    % specific tables (not generated from model)
    create_model_table (Nodes, '_ids_') ->
      create_table (Nodes, '_ids_', [type, id]);
    
    % tables generated from model
    create_model_table (Nodes, Model) ->
      Record = boss_record_lib:dummy_record (Model),
      { Model, create_table (Nodes, Model, Record:attribute_names ()) }.
    
    % single table creator wrapper
    create_table (Nodes, Table, Attribs) ->
      mnesia:create_table (Table,
        [ { disc_copies, Nodes   },
          { attributes,  Attribs } ]).
    
    %% vim: fdm=syntax:fdn=3:tw=74:ts=2:syn=erlang

Such a code ensures the creation of schema and all the tables that are not present in database yet. Once you start ChicagoBoss, this init script will run automatically. Alternatively, you can invoke it from a running CB by issuing `my_application_util:init().`

The only problem is that Mnesia is not schema-less, like for example MUMPS (GT.M, Caché, ..) databases are, so you cannot rebuild a schema of existing table in any straightforward way :-( So any change to data model is to be followed by one of these steps:

* issue `mnesia:delete_table (table_to_alter), my_application_util:init().` command on `erl>` shell;
* construct old record and new record by hand and use `mnesia:transform_table()` to restructure the existing content.

Note, that the first one is possible only in early stages of application development, but is not so convenient in production use like application upgrade, for example.
In general, I am not aware of any way how to alter Mnesia table automatically (add/delete "columns" + set default value for new column when altering "old" records).

The only solution I am thinking about might be to make a callback for boss_compiler that will call us every time the compiler recompiles any data model. We can get old structure and new structure, and compare them.
(Note, that this is not possible to ensure every time as the compiler may not have access to a "previous version.")