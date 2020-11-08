# TeardownLuaTester

This mod allows you to edit a `run.lua` file inside Teardown's data folder and execute it in-game from a button in the pause menu (without restarting the map). It also displays any `print()` output in the center of your screen, or if the script errors, the error message and line number. This is useful for quickly testing a chunk of code.

There are a few new API functions accessible in the ``run.lua`` file:
```
- <number FunctionIndex> AddTickFunction(func Calback)
- <bool Success> RemoveTickFunction(number FunctionIndex)
- <nil> ClearTickFunctions()
```

With this API, you can hook functions into the ``hud.lua``'s ``draw()`` function meaning you can externally draw UI and run other tasks that can't be executed immediately.

**How to use:**

After installing the mod files, edit ``run.lua`` inside ``Teardown/game``. You can edit this file while in-game, save it, and press the "Execute code" button in the pause menu, and everything should work as expected.

If you are using tick functions, it is suggested that you add ``ClearTickFunctions()`` to the top of your code so that any code executed previously stops. If you have use cases where you need to leave that code running, don't call ``ClearTickFunctions()`` and it should continue to run until you reload the map or exit to the menu.

**Example code:**

Both of the following code blocks run ``ClearTickFunctions()`` to make sure any previously running hooks created by code in run.lua are stopped.

This code block displays the current player position as a notification until ``ClearTickFunctions()`` is called next. This (and the code block below it) demonstrates use of ``AddTickFunction()``.
```lua
ClearTickFunctions()

function Round(n)
    return math.floor(n*1000)/1000
end

AddTickFunction(function()
    local pos = GetPlayerTransform().pos
    local str = "X: ".. Round(pos[1]).."\nY: ".. Round(pos[2]) .."\nZ: ".. Round(pos[3])
    UiPush()
        UiTranslate(UiCenter(), 150)
        UiAlign("center middle")

        UiFont("font/bold.ttf", 24)
        local w,h = UiGetTextSize(str)
        UiColor(0,0,0, 0.7)
        UiImageBox("common/box-solid-10.png", w+32, h+16, 10, 10)
        UiColor(1,1,1, 1)
        UiText(str)
    UiPop()
end)
```

The following code block will display a timer at the top of your screen for 5 seconds, and will respawn your character after the time has finished. This demonstrates assigning ``AddTickFunction()`` to a variable (which produces a number index), and use of ``RemoveTickFunction()`` on that variable.
```lua
ClearTickFunctions()

local clock = 1
local timeRemaining = 5
local func

func = AddTickFunction(function()
    clock = clock - GetTimeStep()
    if clock < 0 then
        clock = 1
        notify(timeRemaining.." seconds remaining", 1)
        UiSound("common/click.ogg")
        timeRemaining = timeRemaining - 1
        if timeRemaining < 0 then
            Command("game.respawn")
            RemoveTickFunction(func)
        end
    end
end)
```
