---
layout: default
title: Troubleshooting
nav_order: 5
---

# Troubleshooting sk-burglary 3.3.6

Please make sure you have followed [Installing](https://mknzz.github.io/burglary-docs/install.html) and [Main Config](https://mknzz.github.io/burglary-docs/config.html).

## The break in minigame is not working

If you're attempting to break into a door and nothing happens, follow these steps:

> - **Check the client console**: An error message should appear, indicating the missing minigame. However, in some cases, no error may be printed.
>
> - **Check the minigame option**: Ensure that the minigame variable in `Config.T3_BreakInMinigame` (for the tier where the issue occurred) is set to a valid option. The valid options are: 
>   - "circle"
>   - "square"
>   - "lockpick"
>
> - **Check the required minigame resources**: 
>   - For the "lockpick" or "circle" minigames, ensure the necessary resources are enabled in your `config.lua`:
>     - `qb-lockpick` or `qb-minigames` (for "lockpick")
>     - `ps-ui` or `ox_lib` (for "circle")
>

For more break in minigame information, please visit the [Houses Config](https://mknzz.github.io/burglary-docs/tier_config.html) section of our docs.

---

## Errors using LB Phone

If you’re experiencing errors or are unable to receive or accept a job when using LB-Phone, please read the following:

> - **Cause**: Recent changes to how LB-Phone handles mail caused issues. Originally, it used the qb send mail event, but LB-Phone did not work as expected. To resolve this, the lb-phone option was added to the `Config.OptionalResources` table to directly use the `lb sendmail` export. Additionally, LB-Phone's method for handling button data was altered. 
> 
> - **Solution**: 
>   - If you're running an up-to-date version of LB-Phone, **disable** the lb-phone option in `Config.OptionalResources`. It should work correctly afterward.
>   - If your LB-Phone version is outdated, **enable** the lb-phone option in `Config.OptionalResources`.

---
