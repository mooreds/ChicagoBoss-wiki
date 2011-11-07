BossRecords are _specially compiled parameterized modules_ that follow the "active record" pattern. BossRecords go into your project's src/model/ folder and will have functions generated for saving them into the database and for accessing related BossRecords. Important aspects of BossRecords:

* The first parameter of a BossRecord should always be called Id, and the other parameters should be CamelCased attributes of your data model.
* All parameters will be available as lower-case, underscored functions, e.g. -module(foo, [Id, TheText]) will generate the getter functions id() and the_text(), and the setters id(NewId) and the_text(NewText). Note that setters do not save the BossRecord.
* To auto-generate an ID, pass the atom 'id' as the first parameter to my_module:new.
* Call new with strings, floats, integers, atoms, or binaries for all other parameters
* Parameters that end in "Time" (e.g., CreationTime, UpdateTime) should be passed either erlang:now() or a DateTime tuple.
* You can add your own functions to a BossRecord module, which will have access to the generated functions described below.
* To see the full API for a BossRecord called foo at any time during development, point your browser to /doc/foo on your dev server.

### Generated Functions
Generated instance functions of a BossRecord include:

`save() -> {ok, SavedBossRecord} | {error, [ErrorMessages]}`
