---
layout: default
title: Installing
nav_order: 2
---

# Installing sk-burglary 3.3.5

## You need the following required resources by default.

> - **qb-core**
> 
> - **qb-target**
>
> - **qb-menu**
>
> - **qb-skillbar**
>
> - **qb-lockpick**
>
> - **qb-minigames**

## Please read the installation guide below carefully.

{: .important }
It is **strongly recommended** to use the latest recommended FiveM server artifact version, which is currently **7290**. However, this resource should be compatible with server versions starting from **3245** and onwards. For the game build, it is advised to use version **3095** or newer. Please note that **OneSync** must be enabled.

> 1. Extract the 3.3 resource and copy it into either the `qb` or `standalone` folder. This step is optional; you can place the resource anywhere, as long as it's started after the required resources.
>
> 2. Configuration might be required in the `config.lua` file. Check the `Config.DefaultResources` table and ensure that the resource names match those on your server.
>
> 3. Add the items from `items_ox.md` or `items_qb.md` to your item list depending on your inventory resource.
>
> 4. Add the item images from the `img` subfolder to your inventory images folder.
>
> 5. For a more detailed guide on configuration, please visit the [Main Config](https://mknzz.github.io/burglary-docs/config.html#configuring-sk-burglary-31) section of our docs.

## You can also use the following optional resources for enhanced functionality.

> - **ox_lib** - [Github](https://github.com/overextended/ox_lib)
>
> - **ox_target** - [Github](https://github.com/overextended/ox_target)
>
> - **ox_inventory** - [Github](https://github.com/overextended/ox_inventory)
>
> - **oxmysql** - [Github](https://github.com/overextended/oxmysql)
>
> - **ps-ui** - [Github](https://github.com/Project-Sloth/ps-ui)
>
> - **pd-safe** - [Github](https://github.com/VHall1/pd-safe)
>
> - **sk-menu** - [Github](https://github.com/mknzz/sk-menu)
>
> - **lb-phone**
>
> - **qs-smartphone** - `client/funcs.lua`, replace all instances of `qb-phone` with `qs-smartphone`.

## Using with QBox

**QBox** has been tested and is working. You can find the tested version here: [QBox txAdminRecipe](https://github.com/Qbox-project/txAdminRecipe/blob/main/qbox.yaml).

### Configuration Steps:
1. **Disable qb-target, qb-inventory, and qb-menu** by setting `enabled = false` in the main `config.lua`.
2. **Enable ox_lib, ox_target, and ox_inventory** by setting `enabled = true` in the main `config.lua`.

### For your **QBox** server config:
Make sure to set: `setr qbx:enableBridge "true"` - This enables the qb-core bridge for `qbx_core`. It should be **true** by default.

Alternatively, you can use the [Auto Setup option](https://mknzz.github.io/burglary-docs/config.html#configuring-sk-burglary-31).