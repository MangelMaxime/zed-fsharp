This is a basic extension for Zed to enable F# features.

## Prerequisites
You must have `fsautocomplete` installed, which you can do by running `dotnet tool install --global fsautocomplete`.

## Installation

Inside your [plugin directory](https://zed.dev/docs/extensions/installing-extensions#installation-location), run:

```sh
git clone git@github.com:nathanjcollins/zed-fsharp.git installed/fsharp
```

You can update the plugin to a pre-release version in the Zed installed plugin tab.

## Debugging

The extension ships a debug adapter based on [netcoredbg](https://github.com/Samsung/netcoredbg), which is downloaded automatically from its GitHub releases the first time you start a debug session.

### Debugging a `dotnet run` task (recommended)

If you have a `dotnet run` task in your `.zed/tasks.json`:

```jsonc
[
  {
    "label": "run my app",
    "command": "dotnet",
    // `--project` and arguments after `--` are supported
    "args": ["run", "--project", "src/MyApp"],
  }
]
```

then the task shows up in the debugger's new session modal (`debugger: start`) and can be debugged directly: the extension turns it into a `dotnet build` invocation, asks MSBuild for the built assembly path, and launches that assembly under the debugger. No `debug.json` and no hardcoded DLL path needed.

Note that the task must use `"command": "dotnet"` with the arguments in `"args"` (as above) to be recognized, and resolving the assembly path requires .NET SDK 8 or later. If the project targets multiple frameworks, add e.g. `--framework net9.0` to the task's args.

### Debugging with an explicit configuration

Alternatively, create a `.zed/debug.json` in your project:

```jsonc
[
  {
    "label": "Debug F# app",
    "adapter": "netcoredbg",
    "request": "launch",
    // Path to the assembly produced by `dotnet build`
    "program": "$ZED_WORKTREE_ROOT/bin/Debug/net9.0/MyApp.dll",
    "cwd": "$ZED_WORKTREE_ROOT",
    "args": [],
    "env": {},
    "stopAtEntry": false
  }
]
```

Build your project first (`dotnet build`), or add a `build` step to the configuration so Zed builds before every session:

```jsonc
[
  {
    "label": "Debug F# app (with build)",
    "adapter": "netcoredbg",
    "request": "launch",
    "program": "$ZED_WORKTREE_ROOT/bin/Debug/net9.0/MyApp.dll",
    "build": {
      "command": "dotnet",
      "args": ["build"]
    }
  }
]
```

You can also attach to a running .NET process:

```jsonc
[
  {
    "label": "Attach to F# app",
    "adapter": "netcoredbg",
    "request": "attach",
    "processId": 12345
  }
]
```

Alternatively, launching via the new session modal (`debugger: start`) works without any configuration file.

To use your own netcoredbg build instead of the downloaded one (e.g. on platforms without official builds, such as macOS x64), point Zed at it in your `settings.json`:

```jsonc
{
  "dap": {
    "netcoredbg": {
      "binary": "/absolute/path/to/netcoredbg"
    }
  }
}
```

Feel free to contribute.
