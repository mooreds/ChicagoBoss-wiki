BossDB is a database abstraction layer used for querying and updating the database. Currently Tokyo Tyrant, Mnesia, MySQL, and PostgreSQL are supported.

Conditions and Comparison Operators

The "find" and "count" functions each take a set of Conditions, which specify search criteria. Similar to Microsoft's LINQ, the Conditions can use a special non-Erlang syntax for conciseness. This special syntax can't be compiled with Erlang's default compiler, so you'll have to let Boss compile your controllers which use it.

Conditions looks like a list, but each element in the list uses a notation very similar to abstract mathematical notation with a left-hand side (an atom corresponding to a record attribute), a single-character operator, and a right-hand side (values to match to the attribute). **The mathematical operators are not all ASCII!** You may want to copy-paste the UTF-8 operators below. As an alternative, you can also specify each condition with a 3-tuple with easier-to-type operator names.

As a quick example, to count the number of people younger than 25 with occupation listed as "student" or "unemployed", you would use:

    boss_db:count(person, 
        [age < 25, occupation ∈ ["student", "unemployed"]]).
This could also be written:

    boss_db:count(person, 
        [{age, 'lt', 25},
           {occupation, 'in', ["student", "unemployed"]}]).
Valid conditions are:

    key = Value
    {key, 'equals', Value}
The "key" attribute is exactly equal to Value.

    key ≠ Value
    {key, 'not_equals', Value}
The "key" attribute is not exactly equal to Value.

    key ∈ [Value1, Value2, ...]
    {key, 'in', [Value1, Value2, ...]}
The "key" attribute is equal to at least one element on the right-hand side.

    key ∉ [Value1, Value2, ...]
    {key, 'not_in', [Value1, Value2, ...]}
The "key" attribute is not equal to any element on the right-hand side.

    key ∈ {Min, Max}
    {key, 'in', {Min, Max}}
The "key" attribute is numerically between Min and Max.

    key ∉ {Min, Max}
    {key, 'not_in', {Min, Max}}
The "key" attribute is not between Min and Max.

    key ∼ RegularExpression
    {key, 'matches', RegularExpression}
The "key" attribute matches the RegularExpression.

    key ≁ RegularExpression
    {key, 'not_matches', RegularExpression}
The "key" attribute does not match the RegularExpression.

    key ∋ Token
    {key, 'contains', Token}
The "key" attribute contains Token.

    key ∌ Token
    {key, 'not_contains', Token}
The "key" attribute does not contain Token.

    key ⊇ [Token1, Token2, ...]
    {key, 'contains_all', [Token1, Token2, ...]}
The "key" attribute contains all tokens on the right-hand side.

    key ⊉ [Token1, Token2, ...]
    {key, 'not_contains_all', [Token1, Token2, ...]}
The "key" attribute does not contain all tokens on the right-hand side.

    key ∩ [Token1, Token2, ...]
    {key, 'contains_any', [Token1, Token2, ...]}
The "key" attribute contains at least one of the tokens on the right-hand side.

    key ⊥ [Token1, Token2, ...]
    {key, 'contains_none', [Token1, Token2, ...]}
The "key" attribute contains none of the tokens on the right-hand side.

    key > Value
    {key, 'gt', Value}
The "key" attribute is greater than Value.

    key < Value
    {key, 'lt', Value}
The "key" attribute is less than Value.

    key ≥ Value
    {key, 'ge', Value}
The "key" attribute is greater than or equal to Value.

    key ≤ Value
    {key, 'le', Value}
The "key" attribute is less than or equal to Value.

## Functions

Functions in the boss_db module include:

    find(Id::string()) -> BossRecord | {error, Reason}
Find a BossRecord with the specified Id.

    find(Type::atom(), Conditions) -> [BossRecord]
Query for BossRecords. Returns all BossRecords of type Type matching all of the given Conditions

    find(Type::atom(), Conditions, Max::integer() | all) -> [BossRecord]
Query for BossRecords. Returns up to Max number of BossRecords of type Type matching all of the given Conditions

    find(Type::atom(), Conditions, Max::integer() | all, Skip::integer()) -> [BossRecord]
Query for BossRecords. Returns up to Max number of BossRecords of type Type matching all of the given Conditions, skipping the first Skip results.

    find(Type::atom(), Conditions, Max::integer() | all, Skip::integer(), Sort::atom()) -> [BossRecord]
Query for BossRecords. Returns up to Max number of BossRecords of type Type matching all of the given Conditions, skipping the first Skip results, sorted on the attribute Sort.

    find(Type::atom(), Conditions, Max::integer() | all, Skip::integer(), Sort::atom(), SortOrder) -> [BossRecord]
Query for BossRecords. Returns up to Max number of BossRecords of type Type matching all of the given Conditions, skipping the first Skip results, sorted on the attribute Sort. SortOrder specifies whether to treat values as strings or as numbers, and whether to sort ascending or descending. (SortOrder = num_ascending, num_descending, str_ascending, or str_descending)

Note that Time attributes are stored internally as numbers, so you should sort them numerically.

    count(Type::atom()) -> ::integer()
Count the number of BossRecords of type Type in the database.

    count(Type::atom(), Conditions) -> ::integer()
Count the number of BossRecords of type Type in the database matching all of the given Conditions.

    counter(Id::string()) -> ::integer()
Treat the record associated with Id as a counter and return its value. Returns 0 if the record does not exist, so to reset a counter just use "delete".

    incr(Id::string()) -> ::integer()
Treat the record associated with Id as a counter and atomically increment its value by 1.

    incr(Id::string(), Increment::integer()) -> ::integer()
Treat the record associated with Id as a counter and atomically increment its value by Increment.

    delete(Id::string()) -> ok | {error, Reason}
Delete the BossRecord with the given Id.

    execute(Commands::iolist()) -> RetVal
Execute raw database commands on SQL databases

    transaction(TransactionFun::function()) -> {atomic, Result} | {aborted, Reason}
Execute a fun inside a transaction.

    save_record(RecordBossRecord) -> {ok, SavedBossRecord} | {error, [ErrorMessages]}
Save (that is, create or update) the given BossRecord in the database. Performs validation first; see **validate_record/1**.

    validate_record(RecordBossRecord) -> ok | {error, [ErrorMessages]}
Validate the given BossRecord without saving it in the database. ErrorMessages are generated from the list of tests returned by the BossRecord's **validation_tests/0** function (if defined). The returned list should consist of {TestFunction, ErrorMessage} tuples, where TestFunction is a fun of arity 0 that returns true if the record is valid or false if it is invalid. ErrorMessage should be a (constant) string which will be included in ErrorMessages if the TestFunction returns false on this particular BossRecord.

    type(Id::string()) -> ::atom()
Returns the type of the BossRecord with Id, or undefined if the record does not exist.