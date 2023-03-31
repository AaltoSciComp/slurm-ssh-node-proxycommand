# SSH directly to Slurm job allocations via SSH ProxyCommand

Want to SSH directly to a Slurm job on your cluster?  This does that.
It was designed for use with VSCode as a Remote ssh.


## Remote side

Place `slurm-ssh-node-proxycommand` somewhere that it can be executed.


## User side

Add the following to you ssh config file.  Configure the  Slurm
resources on the command line of the ProxyCommand.  The path to
`slurm-ssh-node-proxycommand` must be set.

```
Host YOUR-CLUSTER-SHELL
    ProxyCommand ssh YOUR-CLUSTER /share/apps/slurm-ssh-node-proxycommand --partition=interactive --time=1:00:00
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
    User YOUR_USER_NAME

# You also need a cluster alias, unless you write the stuff directly
# in ProxyCommand above:

Host YOUR-CLUSTER
    HostName YOUR-CLUSTER.INSTITUTION.EU
    # ... any other SSH config options you need for connecting to the
	# cluster...
```


## Design considerations

- SSH must directly connect from client to the node, since vscode uses
  `-D`, `-L`, etc. to forward connections there.  Chained ssh:es
  without ProxyCommand don't work.


## Status

Seem to work but in testing.  Contributions welcome but no warranties
on it working.


* May not work on a Windows client (`/dev/null` in the ssh config file
  - what should this become?)


# See also

(none yet)
