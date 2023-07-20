# SSH directly to Slurm job allocations via SSH ProxyCommand

Want to SSH directly to a Slurm job on your cluster?  This does that.
It was designed for use with the VSCode as a Remote-SSH, but it's a
broadly applicable tools.


## Remote side

Place `slurm-ssh-node-proxycommand` on the cluster somewhere that it
can be executed.


## User side

Add the following to you ssh config file `.ssh/config`.  Configure the
Slurm resources you request on the line of the ProxyCommand.  The path
to `slurm-ssh-node-proxycommand` must be set to match what is on your
cluster.

```
Host YOUR-CLUSTER-SHELL
    ProxyCommand ssh YOUR-CLUSTER /share/apps/slurm-ssh-node-proxycommand --partition=interactive --time=1:00:00
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
    User YOUR_USER_NAME

# You also need a cluster alias, unless you write the stuff directly
# in ProxyCommand line ssh command above:

Host YOUR-CLUSTER
    HostName YOUR-CLUSTER.INSTITUTION.EU
    # ... any other SSH config options you need for connecting to the
	# cluster...
```


## Design considerations

- With VSCode, the ssh connection must go directly to the destination
  computer, because vscode uses the ssh `-D`, `-L`, etc. to forward
  connections to the destination node, and thus chained ssh:es without
  ProxyCommand don't work..  Thus, a possibly simpler `RemoteCommand`
  in the `.ssh/config` file wont' work (`-D` only gets you to the
  intermediate host, not the final host)


## Status

Seem to work but in testing.  Contributions welcome but no warranties
on it working.

* May not work on a Windows client (`/dev/null` in the ssh config file
  - what should this become?)  **TODO**
* `srun` or `sbatch` may work weird, because `SLURM_JOB_ID` and other
  environment variables are set inside.  Because of the way this is
  set up, it's a bit hard to fix perfectly.  **TODO**
  * A possible solution: `RequestTTY yes` and `RemoteCommand='unset
    SLURM_JOB_ID SLURM_JOBID; bash'`, but the interaction with vscode
    needs testing.
* If the ssh connection dies, the background job will be terminated.
  You will lose your state and not be able to save.
* If the job dies due to time or memory exceeded, the same as above
  will happen: your job will die and there is no time to save.
* Resources are reserved for the whole time you are connected.
  Disconnect when not in use.



# See also

(none yet)
