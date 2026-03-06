
**The solution is `tmux` or `screen` — run the upgrade inside a session that survives disconnections:**

```
apt install tmux
```

```
tmux new -s upgrade
```

Now inside tmux run the upgrade:

```
apt update && apt dist-upgrade --dry-run
```

```
apt dist-upgrade
```

**If your SSH drops**, just reconnect and type:

```
tmux attach -t upgrade
```

Your upgrade will still be running untouched, you just reattach to it.