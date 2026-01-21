# How to Configure the Server On Linux

This page is based on: [Perforce Official Documentation](https://help.perforce.com/helix-core/quickstart/current/Content/quickstart/admin-install-linux.html)

## Installation
__Make sure you have sudo access on the computer where you are installing Helix Core Server.__

* Download the Perforce public key:

```sh
wget https://package.perforce.com/perforce.pubkey
``` 
* Get the fingerprint of the public key:

```sh
gpg -n --import --import-options import-show perforce.pubkey
```
* Verify the downloaded key matches the authentic Perforce fingerprint:

```sh
$ gpg -n --import --import-options import-show perforce.pubkey | grep -q "E58131C0AEA7B082C6DC4C937123CB760FF18869" && echo "true"
```
If the output is true, the key is valid.


* Add the public key to your keyring:
```
$ wget -qO - https://package.perforce.com/perforce.pubkey | sudo apt-key add -
```
we may get a warning says this method is deprecated:
```
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
```
But it will work on Debain.
But if not working, we can try to use the suggested gpg format:

1, Download and convert the key to gpg:

```sh
wget -qO - https://package.perforce.com/perforce.pubkey | gpg --dearmor -o perforce.gpg
```
2, Move the Converted Key to ```/etc/apt/trusted.gpg.d/```
```sh
sudo mv perforce.gpg /etc/apt/trusted.gpg.d/
```
3, Update Your Package List:
```sh
sudo apt-get update
sudo apt update
```


* Create a file for the Perforce repository:

```
$ sudo nano /etc/apt/sources.list.d/perforce.list
```

* To add the new repository to the package manager, add the following line to the file you created:
```
deb http://package.perforce.com/apt/ubuntu noble release
```
Save the file.

* Refresh local packages:

```sh
sudo apt-get update
```
* Install Helix Core Server using the built-in Ubuntu package manager:

```sh
sudo apt-get install helix-p4d
```

* Enter Y to confirm and wait for installation to complete.


## Start a Brand New Server

* Run the configuration in interactive mode:

```sh
sudo /opt/perforce/sbin/configure-helix-p4d.sh
```
The configuration script displays a summary of default settings and any settings optionally set with a command-line argument.

Provide information prompted by the configuration script:

__Service Name:__ Used when starting and stopping the service.

__Server Root:__ P4ROOT where versioned files and metadata are stored.

__Unicode Mode__ Off by default. If you are planning to install Helix Swarm after setting up Helix Core, enable Unicode mode. Swarm requires Unicode mode to ensure that all characters are displayed and handled correctly.


## __Warning:__

If you turn Unicode mode on, you will not be able to turn it off. Be sure you are familiar with Unicode functionality when selecting this mode. See Unicode mode in Helix Core Server Administrator Guide for information.

__Case Sensitivity:__ On by default. In most cases, setting up a case sensitive server with a case-check trigger to avoid case collisions is the best option. If your organization is developing only with and for Windows, you might want to set up a case insensitive server instead.

__Server Address:__ P4PORT, which is the host and port the Helix Core Server listens on, and whether to communicate in plain text or over SSL.

__Superuser login:__ userid for a new user with super level privileges. To learn more about super users, see Access levels in Helix Core Server Administrator Guide.

__Superuser password:__ Password for the new super user. This user has unlimited privileges, so a strong password is required.

When configuration is complete, a summary displays details about what changed and where settings are stored.

You can now connect to the Helix Core Server service, or you can manage the service using the p4dctl utility. For example:
sudo -u perforce p4dctl start -t p4d p4dmaster <server_name>

To learn more about configure-helix-p4d.sh script, see Post-installation configuration in Helix Core Server Administrator Guide. 

* Install the license. move the license file to your perforce server root dir:
``` 
mv /path/to/license /p4root/license
```

## Start From a Backed Up Server

The server is backup with the following steps, follow it to test launch the server:
[Server Backup Routine](../Documentation/ServerBackupRoutine.md)

Stop the server, and we then make a service mannually:

* Create a ```systemd``` service file on the user level:
```sh
mkdir -p ~/.config/systemd/user
```
The -p means it will add the parent diectories if missing, and also ensure it is ok if the directory exists already.

* Create the service file:
```sh
vim ~/.config/systemd/user/p4Server.service
```

Add the following to the file:
```sh
[Unit]
Description=Perforce Server
After=network-online.target
Wants=network-online.target

[Service]
Environment="P4PORT=1666"
Environment="P4USER=username"
ExecStart=/usr/sbin/p4d -r /media/storage/backups/P4ServerFeb28 -p 1666
ExecStop=/opt/perforce/bin/p4 admin stop
KillMode=none
Restart=always
WorkingDirectory=/media/storage/backups/P4ServerFeb28

[Install]
WantedBy=default.target
```
here are the settings and their meaning:
| Setting	|  Purpose    |
|-----------|-------------|
|Description|	Provides a human-readable description.|
After=network.target|	Ensures Perforce starts after the network service is launced.|
Wants=network.target|	Ensures Perforce starts when the network serverice is running.|
Environment="P4PORT=1666"|	All envrionment variable needed for the ```ExecStart``` and ```ExecStop``` needs to be added here|
ExecStart|	Specifies how to start the server.|
ExecStop | Specifies how to stop the server.|
Restart=always|	Ensures the service restarts if it crashes.|
KillMode=none|	Tells the server to not kill the process automatically, our ExecStop will do it so we can stop the server correctly|
LimitNOFILE=4096|Increases file descriptor limits(may not be needed).|
WorkingDirectory="p4ServerRootDir"|Tells the service to use the p4server root as the working directory(may not be needed)|
WantedBy=default.target|	Ensures Perforce starts at boot.|

Save the file, and then reaload the system control daemon:
```sh
systemctl --user daemon-reload
```
enable the service:
```sh
systemctl --user enable perforce
```
now start the server as an service:
```sh
systemctl --user start perforce
```
to check the service status:
```sh
systemctl --user status perforce
```
* If the server status shows fail, it might be because of the permission, we should change the permission of the executables and directories that's been used by the ```ExecStart``` and ```ExecStop``` to be xrw (executable, read, write). the simpliest way is to change owner:
```sh
sudo chown username directory
```

to stop the server service
```sh
systemctl --user stop perforce
```



