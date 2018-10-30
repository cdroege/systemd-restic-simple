# Simple systemd Service Units Files for Restic

This repository contains some simple restic systemd service unit files to manage daily backups with [restic](https://restic.net/). There are also other approaches for this:

 * [erikw/restic-systemd-automatic-backup](https://github.com/erikw/restic-systemd-automatic-backup) 
 * [larsks/restic-systemd-units](https://github.com/larsks/restic-systemd-units)

There are a few differences compared to the other approaches. The service files restrict the permissions, e.g. restic runs as a stand-alone user with only read access to the whole file system. Additionally, the user that runs restic is created dynamically (so no sysuser.d file is needed) and the cache directory is also handled in the service file itself. To use the service files, it is not required to install additional scripts.

## Prerequisites

 * systemd as the init system
 * restic
 * an initialized restic repo. You can create one with the following command:
 ```
 restic init --repo $RESTIC_REPOSITORY
 ```

## Installation

Copy the service files to /etc/systemd/system/ and the configuration files to /etc/restic/
```
git clone https://github.com/cdroege/systemd-restic-simple.git
cd systemd-restic-simple
sudo cp restic-* /etc/systemd/system/
sudo cp -r etc/restic /etc/
sudo chmod 600 /etc/restic/restic.conf
```

To configure restic you can change the sample restic.conf. You can set every [environment variable](https://restic.readthedocs.io/en/latest/040_backup.html#environment-variables) that is supported by restic here. For example, the `RESTIC_REPOSITORY` variable can be used to specify a restic repository where restic should save the backup data (see [Preparing a new repository](https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html)). You can also exclude some files and directory in your backups with the supplied exclude-file.conf.

The timer unit always performs a daily backup. You can enable it as follows:
```
sudo systemctl enable --now restic-backup.timer
```

To see the status of the backup you can execute the following command:
```
sudo journalctl -u restic-backup -f
```
