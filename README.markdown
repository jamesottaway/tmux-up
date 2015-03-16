# tmux-up

Bootstrap new `tmux` sessions without complex tools, DSLs, or dependencies.

## Install

If you use [fresh](http://freshshell.com/) to manage your dotfiles, just run:

```
fresh jamesottaway/tmux-up tmux-up --bin
```

If not, you might want to try [bpkg](http://www.bpkg.io/):

```
bpkg install jamesottaway/tmux-up
```

Alternatively just fetch the script with either `curl` or `wget`, and make sure it's executable:

```
curl https://raw.githubusercontent.com/jamesottaway/tmux-up/master/tmux-up > /usr/local/bin/tmux-up
wget -O - https://raw.githubusercontent.com/jamesottaway/tmux-up/master/tmux-up > /usr/local/bin/tmux-up
chmod u+x /usr/local/bin/tmux-up
```

## Usage

Define the desired initial state of your `tmux` session in a file, using the standard `tmux` commands.

For example, here is `dev.conf` for a fictional Rails application development environment:

```
send-keys 'git up' C-m
send-keys 'git checkout develop' C-m
send-keys 'bundle install' C-m
new-window
send-keys 'vim' C-m
new-window -n server
send-keys 'rails server' C-m
new-window -n console
send-keys 'rails console' C-m
new-window -n db
send-keys 'psql -d example_development' C-m
```

To create a new `tmux` session using the above configuration just run:

```
~/example ❯ tmux-up dev.conf
```

Under the hood `tmux-up` will:

- Create a `tmux` session named `example/dev`
- Invoke each line in `dev.conf`
- Switch to the first `tmux` window
- Attach to the `example/dev` session

If you detach from the `example/dev` session, simply re-run `tmux-up dev.conf` which will reattach you to the session.

## Alternatives

There are quite a lot of other approaches to this problem already floating around.

### tmuxinator, teamocil, etc.

These tools are very popular, but having a dependency on a working Ruby environment (or similar) for a simple tool like this seems like overkill.

As a contrast, `tmux-up` is a simple shell script, meaning it will run anywhere `tmux` will.

In addition to this, these tools commonly use abstracted formats to define your session configuration, which increases the difficulty of adopting such a tool.

In the case of `tmux-up`, you use native `tmux` commands like `new-window` and `send-keys`.

### Plain ol' tmux

The other end of the spectrum would be to invoke `tmux` directly, but override the configuration using the `-f` flag.

I don't like this approach, for two reasons:

- you need to add `source-file ~/.tmux.conf` (or wherever your default config lives) to ensure your top-level configuration is respected

- you need to remember to append the `attach` command, since `tmux` always calls `new-session` when it starts

To avoid these pitfalls, `tmux-up dev.conf` is functionally identical to `tmux -f dev.conf attach`.
