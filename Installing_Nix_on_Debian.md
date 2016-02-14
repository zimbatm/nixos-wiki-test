---
title: Installing Nix on Debian
permalink: /Installing_Nix_on_Debian/
---

This recipe is for installing Nix on Debian or any deb-based platform (such as Ubuntu) in multi-user mode. For single user mode you can just follow the instructions in the nix manual.

Install the Nix package
-----------------------

Download the latest Nix .deb package for your architecture from the nix homepage: <https://nixos.org/nix/download.html>

Install your package with the dpkg tool. Here is an example for an amd64 architecture.

    $ cd /tmp
    $ wget https://hydra.nixos.org/build/10272847/download/1/nix_1.7-1_amd64.deb
    $ sudo dpkg --unpack nix_1.7-1_amd64.deb
    $ sudo aptitude update
    $ sudo aptitude install nix

Create Nix build user accounts
------------------------------

    $ sudo groupadd -g 30000 nixbld
    $ for i in $(seq 1 10)
    do
    sudo useradd -M -N -r -u $(expr 30000 + $i) -g nixbld -G nixbld -c "Nix build user $i" -d /var/empty -s /bin/false nixbld$i
    done

    $ sudo mkdir -p /etc/nix
    $ sudo sh -c 'echo "build-users-group = nixbld" >> /etc/nix/nix.conf'

    $ sudo mkdir -p /nix/store
    $ sudo chown root.nixbld /nix/store
    $ sudo chmod 1775 /nix/store

Configure nix-daemon
--------------------

    $ cat << EOF | sudo sh -c 'cat > /etc/init.d/nix-daemon'
    #! /bin/sh
    ### BEGIN INIT INFO
    # Provides:          skeleton
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: nix-daemon
    # Description:       Nix-daemon runs nixblds to build nix/store items.
    ### END INIT INFO

    # Author: Russell O'Connor <roconnor@theorem.ca>

    # Do NOT "set -e"

    # PATH should only include /usr/* if it runs after the mountnfs.sh script
    PATH=/sbin:/usr/sbin:/bin:/usr/bin
    DESC="nix-deamon"
    NAME=nix-daemon
    DAEMON=/usr/bin/$NAME
    DAEMON_ARGS=""
    PIDFILE=/var/run/$NAME.pid
    SCRIPTNAME=/etc/init.d/$NAME

    # Exit if the package is not installed
    [ -x "$DAEMON" ] || exit 0

    # Read configuration variable file if it is present
    [ -r /etc/default/$NAME ] && . /etc/default/$NAME

    # Load the VERBOSE setting and other rcS variables
    . /lib/init/vars.sh

    # Define LSB log_* functions.
    # Depend on lsb-base (>= 3.2-14) to ensure that this file is present
    # and status_of_proc is working.
    . /lib/lsb/init-functions

    #
    # Function that starts the daemon/service
    #
    do_start()
    {
            # Return
            #   0 if daemon has been started
            #   1 if daemon was already running
            #   2 if daemon could not be started
            start-stop-daemon --start --quiet --pidfile $PIDFILE --exec $DAEMON --test > /dev/null \
                    || return 1
            start-stop-daemon --start --quiet --pidfile $PIDFILE --exec $DAEMON -- \
                    $DAEMON_ARGS \
                    || return 2
    }

    #
    # Function that stops the daemon/service
    #
    do_stop()
    {
            # Return
            #   0 if daemon has been stopped
            #   1 if daemon was already stopped
            #   2 if daemon could not be stopped
            #   other if a failure occurred
            start-stop-daemon --stop --quiet --retry=TERM/30/KILL/5 --pidfile $PIDFILE --name $NAME
            RETVAL="$?"
            [ "$RETVAL" = 2 ] && return 2
            # Wait for children to finish too if this is a daemon that forks
            # and if the daemon is only ever run from this initscript.
            # If the above conditions are not satisfied then add some other code
            # that waits for the process to drop all resources that could be
            # needed by services started subsequently.  A last resort is to
            # sleep for some time.
            start-stop-daemon --stop --quiet --oknodo --retry=0/30/KILL/5 --exec $DAEMON
            [ "$?" = 2 ] && return 2
            # Many daemons don't delete their pidfiles when they exit.
            rm -f $PIDFILE
            return "$RETVAL"
    }

    case "$1" in
      start)
            [ "$VERBOSE" != no ] && log_daemon_msg "Starting $DESC" "$NAME"
            do_start
            case "$?" in
                    0|1) [ "$VERBOSE" != no ] && log_end_msg 0 ;;
                    2) [ "$VERBOSE" != no ] && log_end_msg 1 ;;
            esac
            ;;
      stop)
            [ "$VERBOSE" != no ] && log_daemon_msg "Stopping $DESC" "$NAME"
            do_stop
            case "$?" in
                    0|1) [ "$VERBOSE" != no ] && log_end_msg 0 ;;
                    2) [ "$VERBOSE" != no ] && log_end_msg 1 ;;
            esac
            ;;
      status)
            status_of_proc "$DAEMON" "$NAME" && exit 0 || exit $?
            ;;
      restart|force-reload)
            log_daemon_msg "Restarting $DESC" "$NAME"
            do_stop
            case "$?" in
              0|1)
                    do_start
                    case "$?" in
                            0) log_end_msg 0 ;;
                            1) log_end_msg 1 ;; # Old process is still running
                            *) log_end_msg 1 ;; # Failed to start
                    esac
                    ;;
              *)
                    # Failed to stop
                    log_end_msg 1
                    ;;
            esac
            ;;
      *)
            echo "Usage: $SCRIPTNAME {start|stop|status|restart|force-reload}" >&2
            exit 3
            ;;
    esac

    :
    EOF

    $ sudo chmod 755 /etc/init.d/nix-daemon
    $ sudo update-rc.d nix-daemon defaults
    $ sudo /etc/init.d/nix-daemon start

Setup user accounts
-------------------

    $ cat << EOF | sudo sh -c 'cat >> /etc/profile.d/nix.sh'
    if [ "\$USER" != root -a -e /nix/var/nix/daemon-socket/socket ]; then
    export NIX_REMOTE=daemon
    else
    unset NIX_REMOTE
    fi
    EOF

    $ sudo mkdir -p /nix/var/nix/profiles/per-user/$USER
    $ ln -s /nix/var/nix/profiles/per-user/$USER ~/.nix-profile
    $ nix-channel --add https://nixos.org/channels/nixpkgs-unstable