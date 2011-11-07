h1. Routing System

The routing system is simple, in the file boss.routes you have some special routes and free-hand named routes, the routes are loaded at startup in memory (ets table) and reloaded on /admin/upgrade.

The file contains a list of erlang terms, one per line finished with a dot.

* boss.routes example

<pre><code class="erlang">
%% The {Controller, Action} pair to be used on the home page. Defaults to {"hello", "world"}
%% Format: {"Controller", "Action"}
{front_page, {"hello", "world"}}.
%%
%% The action to call if none is specified. Defaults to "index"
%% Format: "Action" 
{default_action, "index"}.
%%
%% A proplist of default actions to call if none is specified, keyed by controller name
%% Format: [ {"Controller1", "Action"}, {"Controller2", "Action"}]
{default_actions, [{"admin", "model"}]}.
%%
%% Route for not-found actions, is called if not named route is found a the first url token
%% don't pertain to a controller
%% Format: {"Controller", "Action", ["Param1", "ParamN"]}
%% {not_found_route, {"hello", "world", []}}.
%%
%% Named routes, will take precedence over the previous
%% Format: {"Full Url", {"Controller", "Action", ["Param1", "ParamN"]}}
%%{"/login", {"account", "login", []}}.
%%{"/signup", {"accounts", "signup", []}}.
</code></pre>