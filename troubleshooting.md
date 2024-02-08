---
layout: default
title: Troubleshooting
nav_order: 6
---

# Troubleshooting sk-burglary 3.1

Please make sure you have followed the [Installing](https://mknzz.github.io/burglary-docs/install.html) and [Main Config](https://mknzz.github.io/burglary-docs/config.html) docs.

## The break in minigame is not working

If you’re targeting a door to break in and nothing is happening, please try the following steps:

> - **Check the client console**: There should be an error message in the console indicating the missing minigame. However, in some cases, it may not print any errors.
>
> - **Check the minigame option**: Make sure that the minigame variable in `Config.T3_BreakInMinigame` (Whichever tier the issue occured) is set to a valid option (“circle”, “square”, or “lockpick”).
>
> - **Check the required minigame resources**: If you’re using the “lockpick” or “circle” minigame, ensure that the required resources (qb-lockpick for “lockpick”, `ps-ui` or `ox_lib `for “circle”) are present and enabled in the appropriate tables in `config.lua`.

For more break in minigame information, please visit the [Houses Config](https://mknzz.github.io/burglary-docs/tier_config.html) section of our docs.

## Blackscreen loop after entering



