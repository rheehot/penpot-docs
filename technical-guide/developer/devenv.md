---
title: 3.1. Environment
---

# Dev Env

## System requirements

You should have `docker` and `docker-compose` installed in your system
in order to set up properly the development enviroment.

You can [look here][1] for complete instructions.

[1]: /technical-guide/getting-started/#docker


Optionally, increment user watches:

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```


## Getting Started

**Requires a minimum knowledge of tmux usage in order to use that
development environment.**

For start it, staying in this repository, execute:

```bash
./manage.sh pull-devenv
./manage.sh run-devenv
```

This will do the following:

1. Pulls the latest devenv image from dockerhub.
2. Starts all the containers in the background.
3. Attaches to the **devenv** container and executes the tmux session.
4. The tmux session automatically starts all the necessary services.

This is a incomplete list of devenv related subcommands found on
manage.sh script:

```bash
./manage.sh build-devenv # builds the devenv docker image (not necessary in normal circumstances)
./manage.sh start-devenv # starts background running containers
./manage.sh run-devenv   # enters to new tmux session inside of one of the running containers
./manage.sh stop-devenv  # stops background running containers
./manage.sh drop-devenv  # removes all the volumes, containers and networks used by the devenv
```

Having the the container running and tmux opened inside the container,
you are free to execute any commands and open many shells as you want.

You can create a new shell just pressing the **Ctr+b c** shortcut. And
**Ctrl+b w** for switch between windows, **Ctrl+b &** for kill the
current window.

For more info: https://tmuxcheatsheet.com/


<!-- ## Inside the tmux session -->

<!-- By default, the tmux session opens 4 windows: -->

<!-- - **gulp** (0): responsible of build, watch (and other related) of -->
<!--   styles, images, fonts and templates. -->
<!-- - **frontend** (1): responsible of cljs compilation process of frontend. -->
<!-- - **exporter** (2): responsible of cljs compilation process of exporter. -->
<!-- - **backend** (3): responsible of starting the backend jvm process. -->


### Frontend

The frontend build process is located on the tmux **window 0** and
**window 1**. On the **window 0** we have the gulp process responsible
of watching and building styles, fonts, icon-spreads and templates.

On the **window 1** we can found the **shadow-cljs** process that is
responsible on watch and build frontend clojurescript code.

Additionally to the watch process you probably want to be able open a repl
process on the frontend application, for this case you can split the window
and execute the `npx shadow-cljs cljs-repl main` for open the REPL.


### Exporter

The exporter build process is located in the **window 2** and in the
same way as frontend application, it is build and watched using
**shadow-cljs**.

The main difference is that exporter will be executed in a nodejs, on
the server side instead of browser.

There you will found the window split in two slices. On the top slice
you will have the build process and on the bot slice shell ready to
execute the generated bundle.

You can start the exporter process executing `node target/app.js`. The
process does not starts automatically.


### Backend

The backend related process is located in the tmux **window 3**, and
you can go directly to it using `ctrl+b 2` shortcut.

By default the backend will be started in non-interactive mode for
convenience but you can just press `Ctrl+c` and execute `./scripts/repl`
for start the repl.

On the REPL you have this helper functions:
- `(start)`: start all the environment
- `(stop)`: stops the environment
- `(restart)`: stops, reload and start again.

And many other that are defined in the `dev/user.clj` file.

If some exception is raised when code is reloaded, just use
`(repl/refresh-all)` in order to finish correctly the code swaping and
later use `(restart)` again.
