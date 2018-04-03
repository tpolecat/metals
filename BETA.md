> ⚠️ This project is very alpha stage. Expect bugs and surprising behavior. Ticket reports and patches are welcome!

The following guide provides instructions on how to try this on a real project, with the purpose
of collecting valuable feedback from real-world scenarios.

# Global setup
These steps are required once per machine.

## Step 1 - Create the sbt Plugin
The server needs to access some metadata about the build configuration. This data are produced by
an sbt plugin. This plugin is currently not published, so you will need to copy paste it on your machine.

Here's the source of the plugin: https://github.com/scalameta/metals/blob/master/project/MetalsPlugin.scala

Copy the source to either (depending on your sbt version):
- (sbt 0.13) `~/.sbt/0.13/plugins/MetalsPlugin.scala`
- (sbt 1.0) `~/.sbt/1.0/plugins/MetalsPlugin.scala`

## Step 2 - Build the VSCode Extension
The VSCode extension is not yet published on the Marketplace, so you'll need to build it locally.
For this step, `node` and `npm` are required.

- `cd vscode-extension`
- `npm install`
- `npm run build`
- `code --install-extension vscode-metals-0.0.1.vsix`

You will also need the [Scala Syntax (Official)](https://marketplace.visualstudio.com/items?itemName=scala-lang.scala) plugin for VSCode. This is available in the marketplace.

## Step 3 - Publish the Server Locally
From the repo root run `sbt publishLocal`

# Per-project setup
These steps are required on each project.

## Step 1 - Enable SemanticDB
Some features of the server rely on the artifacts produced by the [`semanticdb-scalac`](http://scalameta.org/tutorial/#semanticdb-scalac) compiler plugin.
There are two alternative ways to install `semanticdb-scalac`.

The first option is to enable `semanticdb-scalac` permanently for your project in `build.sbt` with:

```scala
libraryDependencies += compilerPlugin("org.scalameta" % "semanticdb-scalac" % "2.1.2" cross CrossVersion.full)
scalacOptions += "-Yrangepos"
```

The second option is to enable `semanticdb-scalac` only for an active sbt session by running `semanticdbEnable` from the sbt shell.
```scala
$ sbt
> semanticdbEnable // automatically runs libraryDependencies += compilerPlugin(...)
> compile // re-compile project with semanticdb-scalac compiler plugin
> ...
```
Note that you will need to re-run `semanticdbEnable` next time you open sbt.

## Step 2 - Enable SBT Integration

If you wish to compile on save and show error markers, set the `metals.sbt.enabled` setting in VSCode to `true`.

## Step 3 - Start Editing
Open your project in VSCode (`code .` from your terminal) and open a Scala file; the server will now start.

Please note that it may take a few seconds for the server to start and there's currently no explicit indication that the server has started (other than features starting to work).
To monitor the server activity, we suggest to watch the log file.

- From your project's base directory run `tail -f .metals/metals.log`.

Finally, since most features currently rely on a successful compilation step, make sure you incrementally compile your project by enabling sbt integration (above) or by running `~compile` in `sbt`.
