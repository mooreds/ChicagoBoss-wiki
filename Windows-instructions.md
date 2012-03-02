### Overview for newbs
Git is source control tool used to pull the latest Rebar and ChicagoBoss source code. Rebar is a build tool used by ChicagoBoss.
### Windows Instructions
Download Erlang Windows Install from http://www.erlang.org/download.html

Install Erlang and add erlang BIN directory to your path

Download and install Git from http://code.google.com/p/msysgit/downloads/list

Open command line and download the latest Rebar source code and ChicagoBoss source code:

`git clone git://github.com/basho/rebar.git`

`git clone git://github.com/evanmiller/ChicagoBoss.git`

`cd rebar`

`bootstrap.bat`

`copy rebar ..\chicagoboss`

`copy rebar.cmd ..\chicagoboss`

`cd ..\chicagoboss`

Here is the deal at this point. CB uses Makefile to invoke bunch of rebar commands in sequence and more. Rebar commands work in Windows command prompts. You can try them by typing:

`rebar get-deps`

`rebar compile`

However "make all" does more than call rebar commands.

At this point, it is easier to just download **make**. Download and install GNU tools from
http://sourceforge.net/projects/getgnuwin32/files/getgnuwin32/0.6.30/...

Add the BIN folder from that install to Windows PATH.

From here forward, do not use Windows Command Prompt, but use the Git Bash.

Open Git Bash to you ChicagoBoss source folder and type: make all

From here, all your make commands in ChicagoBoss and ./init.sh and ./init-dev.sh in your projects will work inthe Git Bash 