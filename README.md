# TeardownLuaRunTool

This mod allows you to edit a `run.lua` file inside Teardown's data folder and execute it in-game from a button in the pause menu (without restarting the map). It also displays any `print()` output in the center of your screen, or if the script errors, the error message and line number. This is useful for quickly testing a chunk of code.

There are a few new API functions accessible in the ``run.lua`` file:
```
- <number FunctionIndex> AddTickFunction(func Calback)
- <bool Success> RemoveTickFunction(number FunctionIndex)
- <nil> ClearTickFunctions()
```

With this API, you can hook functions into the ``hud.lua``'s ``draw()`` function meaning you can externally draw UI and run other tasks that can't be executed immediately.

Documentation and mod files will be posted soon.
