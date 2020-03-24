### Install debian in virtual box

#### Installation

- oracle vm manager
- debian-<version>-netinst
- follow the steps in the vm manager, add netinst in the iso and install
- Problem: cannot proceed because visualization support is not enabled in windows 10 home
    - hyper-V is only applicable in windows 10 pro
    - enable visualization in Bios mode
        - varies between different models, for mine
        - press F2 during reboot, enter into Bios
        - F7 to Advanced
        - find visualization (tecnology) or VT-x, enable
        - maybe in system configuration
        - save the changes and reboot
- keep installing debian, leave proxy alone, most personal networks don't have proxies
- better skip installing desktop manager such as xcfe/cinnamon at this step
    - I chose gdm as the default display manager during installation
    - seems it does not work well with xfce session
    - not sure why, but using xfce session to log in break the display manager and network manager
    
#### Settings
- Problem: sudo does not work for current user
    - `su -` log in as super user
    - `adduser <user> <group>` in this case, group is sudo
    - reboot
    
- Problem: network-manager is not running
    - `su -` log in as super user
    - `less /var/logs/messages` check the logs
    - ```
        Mar 23 21:53:40 debian xfce4-session[1030]: xfsm_manager_load_session: Something wrong with /home/jiahui/.cache/sessions/xfce4-session-debian:0, D
        oes it exist? Permissions issue?
        Mar 23 21:53:41 debian xfsettingsd[1061]: g_str_has_prefix: assertion 'prefix != NULL' failed
        Mar 23 21:53:41 debian /usr/lib/gdm3/gdm-x-session[961]: (uint32 1,)
        Mar 23 21:53:41 debian xfsettingsd[1061]: g_value_get_string: assertion 'G_VALUE_HOLDS_STRING (value)' failed
        Mar 23 21:53:41 debian xfsettingsd[1061]: g_value_get_string: assertion 'G_VALUE_HOLDS_STRING (value)' failed
        Mar 23 21:53:41 debian kernel: [  695.180435] fuse init (API version 7.27)
        Mar 23 21:53:41 debian xfwm4[1051]: g_str_has_prefix: assertion 'prefix != NULL' failed
        Mar 23 21:53:41 debian xfsettingsd[1061]: Failed to get the _NET_NUMBER_OF_DESKTOPS property.
        Mar 23 21:53:41 debian xfwm4[1051]: The property '/general/double_click_distance' of type int is not supported
        ......
        Mar 23 21:53:50 debian gnome-session[678]: gnome-session-binary[678]: WARNING: App 'org.gnome.Shell.desktop' exited with code 1
        Mar 23 21:53:50 debian gdm3: Child process -656 was already dead.
        Mar 23 21:53:50 debian /usr/lib/gdm3/gdm-x-session[961]: # watch_fast: "/org/gnome/terminal/legacy/" (establishing: 0, active: 0)
        Mar 23 21:53:50 debian /usr/lib/gdm3/gdm-x-session[961]: # unwatch_fast: "/org/gnome/terminal/legacy/" (active: 0, establishing: 1)
        Mar 23 21:53:50 debian /usr/lib/gdm3/gdm-x-session[961]: # watch_established: "/org/gnome/terminal/legacy/" (establishing: 0)
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]: blueman-applet version 2.0.8 starting
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]: /usr/lib/python3/dist-packages/blueman/plugins/applet/AppIndicator.py:8: PyGIWarning: AppIndicator3 was imported without specifying a version first. Use gi.require_version('AppIndicator3', '0.1') before import to ensure that the right version gets loaded.
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:   from gi.repository import AppIndicator3 as girAppIndicator
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]: Traceback (most recent call last):
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:   File "/usr/lib/python3/dist-packages/dbus/bus.py", line 175, in activate_name_owner
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:     return self.get_name_owner(bus_name)
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:   File "/usr/lib/python3/dist-packages/dbus/bus.py", line 361, in get_name_owner
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:     's', (bus_name,), **keywords)
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:   File "/usr/lib/python3/dist-packages/dbus/connection.py", line 651, in call_blocking
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]:     message, timeout)
        Mar 23 21:54:06 debian /usr/lib/gdm3/gdm-x-session[961]: dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NameHasNoOwner: Could not get owner of name 'org.bluez': no such name

      ```
     - reboot and it got stuck in initiating for a very long time, and there is no graphical interface since then.
     - `sudo apt-get install <package>` failed, it cannot resolve  ```cannot resolve deb.debian.org```
     - solve the problem
        - reboot the vm -> hold shift and select advance -> recovery mode
        - log in 
        - `sudo dhclient -v -4`
        - `apt-get install nm-tray network-manager`
        - cannot find gdm, lightdm is broker or not installed
        - choose lightdm as default display manager
        - `apt-get remove lightdm`
        - `apt-get install lightdm`
        - reboot and everything works now
        - I may need to work around this later
        - [reference](https://askubuntu.com/questions/1045278/ubuntu-server-18-04-temporary-failure-in-name-resolution)
        
- custom resolution
    - for my monitor, no default resolution works, 1920x960 works best
    - `xrandr` find the screen name Virtual1
    - `cvt 1920 960`
    - `xrandr --newmode "1920x960_60.00"  152.00  1920 2032 2232 2544  960 963 973 996 -hsync +vsync` after new mode is the output from cvt
    - `xrandr -addmode 1920x960_60.00`
    - right click -> applications -> settings -> display, select the right resolution
    
- necessary packages and commands
    - `sudo apt-get install git-all`
    - `sudo apt-get install python3-pip`
    
- check the default display manager
    - `su -`
    - `less /etc/X11/default-display-manager`