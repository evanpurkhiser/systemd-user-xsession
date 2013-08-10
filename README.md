## Systemd user service

These scripts are used to start a `systemd --user` session.

The service file will do a few different things

 1. Initalize a logind session using the PAM login module on /dev/tty5
 2. Load environment variables from /etc/profile into the systemd --user session
 3. Load user environment variables from $HOME/.config/systemd/environment
 4. Start the systemd --user session as the specified user, starting the
	default.target

## Enabling the service

    systemctl start user-session@username

Enabling the service wil conflflict with the getty on /dev/tty5 and will cause
systemd --user to start on startup.

## Authenticating a user session

Since the systemd --user session will be autostarted on boot as your user it's
not nessicary to login. However, if you like the added security of requiring a
login to access the session it's as easy as changing what the default.target
starts.

Instead of having your default target start x11 and a window manager, just start
the dbus.service (this is required or else `systemctl --user` and `systemd
--user` can't talk).

Then in your `.profile` put something like this:

	if systemctl -q is-active user-session@username
	then
		# Bring up systemd --user to the specified target
		systemctl --user start some-target.target

		# Exit the VT
		clear && logout
	fi

Then you can login via any of your gettys to start your session services. You
may want to script it so different targets are started based on how you login.
For example, when I login from tty1 it starts my graphical.target if it's not
active, otherwise it starts my console.target.

