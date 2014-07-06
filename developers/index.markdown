---
layout: default
Title: Developer Corner
---

# Setting Up your Development Environment
{:.no_toc}

* Contents
{:toc}

\BlueLaTeX is built using [sbt](http://www.scala-sbt.org/), so you obviously need to have it installed. This is written in scala but you only need a jvm installed, as all dependencies, including the correct scala compiler and library version will be managed by sbt.

## Compiling \BlueLaTeX

To start working on \BlueLaTeX, you will need to execute these commands:

```shell
$ git clone git@github.com:gnieh/bluelatex.git
$ cd bluelatex
$ sbt
> compile
```

This will download all necessary dependencies, compile the entire project and should simply work.

Sbt has a great feature for incremental compilation, you can simply launch the command prefixed with a `~`

```shell
> ~compile
```

And incremental compilation will be launched every time a file changes in the source tree.

## Launching Test Server

The sbt configuration of \BlueLaTeX allows you to start a test instance of the server. This test instance is launched as a daemon on Unix systems using [jsvc](http://commons.apache.org/proper/commons-daemon/jsvc.html) (this should be possible on windows too, but it is not tested yet, any help is welcome).

in the sbt console just type:

```shell
> blueStart
```

The application will be packaged and the test couchdb and \BlueLaTeX servers will be started. You can then access the web application by going to [http://localhost:18080/web/](http://localhost:18080/web/).

The rationale behind starting our own couchdb instance is that integration tests should not interfere with any existing couchdb instance. We'll see below how to change this and use the system couchdb instance.

To stop the test server just type:

```shell
> blueStop
```

When stopping the test server, the couchdb server is stopped as well and its data are cleaned up. Hence whenever you restart the server, you start with an empty database.
Once again this well adapted is for automatic tests.

## Customize your Building Environment

It is possible to override default settings by creating a local `build.sbt` file at the root of the project.

For example, let's say you don't want to start a custom couchdb instance, but use the system one instead. You just need to add these lines in your build.sbt file:

```scala
couchdb := None

couchPort := 5984
```

**Note** it is important to have a [blank line between all commands in this sbt file](http://www.scala-sbt.org/release/docs/Getting-Started/Basic-Def.html#how-build-sbt-defines-settings), forgetting it may lead to some strange behaviors and headaches.

All customization are not described here, and is behind scope of this wiki page, as it would lead to have a sbt tutorial. But you can basically override any settings key in this file.

Every time you change a setting, you must either execute the `reload` command in the sbt console or restart it.

## Troubleshooting

### Jsvc won't start

Starting at some version of jsvc, it is required to invoke the command with full path and an explicit working directory. If you are in this case and see the following error message:

```
JSVC re-exec requires execution with an absolute or relative path
```

you will have to add this line in your `build.sbt`

```scala
launchExe := "/usr/bin/jsvc"
```

Or whatever path is returned by `which jsvc`

### Unable to connect to the server 

If the `blueStart` command was correctly executed but you cannot connect to the server. Have a look at content of file `/tmp/bluelatex.err`. If you see something like the following lines in it:

```
No config.properties found.
```
or
```
ERROR: Unable to create cache directory: ./felix-cache
ERROR: Error creating bundle cache. (java.lang.RuntimeException: Unable to create cache directory.)
```

It means that you have a version of jsvc that requires setting explicitly the working directory.
Once again this can be solved by overriding the setting key configuring the start options in `build.sbt`:

```scala
startOptions <<= (startOptions, bluePack) map { (opt, pack) => "-cwd" :: pack.getCanonicalPath :: opt }
```


This line is a bit more complex than the previous ones but just says: the value of the `startOptions` setting key is whatever its value was **plus** the option `-cwd` followed by the full path of where the application was packaged.
This directory contains the configuration etc (by default it is packed in `target/pack`). stop your current \BlueLaTeX instance by typing `blueStop`  in the sbt console, reload the configuration, and restart. It should work.
