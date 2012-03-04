Notes on setting up CB on a DreamPlug, http://www.globalscaletechnologies.com/t-dreamplugdetails.aspx

I ordered a JTAG connector to go with it, but so far I haven't needed it because I do everything over SSH.

To start I just plugged the computer's Ethernet into the LAN, and plugged in the computer. It booted up all by itself.

The DreamPlug will get an IP address from DHCP by default. I looked at my router configuration to figure out what that IP address was, and ssh'd into it:

    ssh root@192.168.1.117

The default password is `nosoup4u`

DNS was broken on my installation due to an IP address conflict with the built-in WiFi. To disable the built-in WiFi, I commented out the last line in /etc/rc.local:

    # /root/init_setup.sh

Then reboot:

    # reboot

The APT sources list is broken, so you need to fix it. Comment out the last line in /etc/apt/sources.list

    # deb http://10.82.108.51/kedars/sheevaplug_wifi/builds/packages/ binary/

A couple of packages are out of date and will cause errors, so you need to upgrade them first:

    # apt-get update
    # apt-get install debian-keyring-archive man-db

Next you can upgrade the system to use the latest packages. For some reason there is some package cruft on the default installation, so you should do an autoremove before a dist-upgrade

    # apt-get autoremove
    # apt-get dist-upgrade

This takes a little while, so go make yourself some coffee. Next I installed Erlang and rsync:

    # apt-get install erlang-base erlang-inets rsync

Next, from another machine, I copied over a (compiled) CB folder and application:

    $ rsync -ave ssh ChicagoBoss-0.7.3/ 192.168.1.117:ChicagoBoss-0.7.3
    $ rsync -ave ssh cb_test/ 192.168.1.117:cb_test

Back at the DreamPlug, start the server and cross your fingers!

    $ cd cb_test
    $ ./init.sh start

With a basic "Hello, world!" test using ab I get about 130 requests/second.