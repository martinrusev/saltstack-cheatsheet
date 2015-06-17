# SaltStack Cheat Sheet


**Table of Contents** 

- [Initial Configuration](#initial-configuration)
- [Minions](#minions)
  - [Minion status](#minion-status)
  - [Target minion with state files](#target-minion-with-state-files)
  - [Grains](#grains)
- [Jobs in Salt](#jobs-in-salt)
- [Sysadmin specific](#sysadmin-specific)
  - [System and status](#system-and-status)
  - [Packages](#packages)
  - [Check status of a service and manipulate services](#check-status-of-a-service-and-manipulate-services)

# Initial Configuration
In `/etc/salt/master` delete all the entries, in the beginning you only need the following: 

```
interface: 0.0.0.0
max_open_files: 100000
file_roots:
  base:
    - /salt/states/base
```

Minion configuration `/etc/salt/minion`, the bare minimum: 

```
master: master_ip
#id:
```

# Minions

## Minion status
You can also use several commands to check if minions are alive and kicking but I prefer manage.status/up/down.

```
salt-run manage.status  # What is the status of all my minions? (both up and down)
salt-run manage.up      # Any minions that are up?
salt-run manage.down    # Any minions that are down?
salt '*' test.ping      # Use test module to check if minion is up and responding.
                        # (Not an ICMP ping!)
```

## Target minion with state files
Apply a specific state file to a (group of..) minion(s). Do not use the .sls extension. (just like in the state files!)

```
salt '*' state.sls mystatefile           # mystatefile.sls will be applied to *
salt 'minion1' state.sls prod.somefile  # prod/somefile.sls will be applied to minion1
```

Grouping minions by id in /etc/salt/master


```
nodegroups:
  deb: 'debian8, ubuntu1404'
  rpm: 'centos7, centos6'

salt -N deb state.sls vim
```


## Grains

List all grains on all minions

```
salt '*' grains.ls
```

Look at a single grains item to list the values.

```
salt '*' grains.item os      # Show the value of the OS grain for every minion
salt '*' grains.item roles   # Show the value of the roles grain for every minion
```

Manipulate grains.

```
salt 'minion1' grains.setval mygrain True  # Set mygrain to True (create if it doesn't exist yet)
salt 'minion1' grains.delval mygrain       # Delete the value of the grain
```

# Jobs in Salt
Some jobs operations that are often used. (http://docs.saltstack.com/en/latest/topics/jobs/)

```
salt-run jobs.active      # get list of active jobs
salt-run jobs.list_jobs   # get list of historic jobs
salt-run jobs.lookup_jid <job id number>  # get details of this specific job
```

# Sysadmin specific
Some stuff that is specifically of interest for sysadmins.

## System and status
```
salt 'minion-x-*' system.reboot  # Let's reboot all the minions that match minion-x-*
salt '*' status.uptime           # Get the uptime of all our minions
```

## Packages

```
salt '*' pkg.list_upgrades             # get a list of packages that need to be upgrade
salt '*' pkg.upgrade                   # Upgrades all packages via apt-get dist-upgrade (or similar)

salt '*' pkg.version bash              # get current version of the bash package
salt '*' pkg.install bash              # install or upgrade bash package
salt '*' pkg.install bash refresh=True # install or upgrade bash package but
                                      # refresh the package database before installing.
```

## Check status of a service and manipulate services

```
salt '*' service.status <service name>
salt '*' service.available <service name>
salt '*' service.start <service name>
salt '*' service.restart <service name>
salt '*' service.stop <service name>
```
