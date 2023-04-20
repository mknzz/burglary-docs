---
layout: default
title: Installing
nav_order: 2
---

# Installing qb-burglary


{: .required }
> - **qb-core** This resource name can be changed in config.lua, you can also change the core object name
> 
> - **qb-target** This resource name can be changed in config.lua, anything qb-target related can be found in cl_public.lua
>
> - **qb-menu** This resource name can be changed in config.lua, anything qb-menu related can be found in cl_public.lua
>
> - **oxmysql** This is only required if you are using levels
>
> - Server version **3245+** / OneSync enabled

{: .note }
If you have renamed your qb-target resource, or you are using another target you must change the dependency name in fxmanifest.lua. We recommend using the latest qb-target.

## Add qb-burglary to your resource folder
It is recommended that you add qb-burglary to the [qb] folder.

## Add the inventory images
You can find the images in the qb-burglary folder.
Take the provided images and add them to your qb-inventory images folder, qb-inventory -> html -> images

## Add the required items
```
--TV prop item
['bigtv'] 				 	     = {['name'] = 'bigtv', 			  	  		['label'] = 'TV', 						['weight'] = 70000, 	['type'] = 'item', 		['image'] = 'stolentv.png', 			['unique'] = true, 		['useable'] = true, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Definitely not stolen'},
--PC prop item
['computer'] 				 	 = {['name'] = 'computer', 			  	  		['label'] = 'Computer', 				['weight'] = 70000, 	['type'] = 'item', 		['image'] = 'stolencomputer.png', 		['unique'] = true, 		['useable'] = true, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Definitely not stolen'},
--Microwave prop item
['microwave'] 				 	 = {['name'] = 'microwave', 			  	  	['label'] = 'Microwave', 				['weight'] = 70000, 	['type'] = 'item', 		['image'] = 'stolenmicro.png', 			['unique'] = true, 		['useable'] = true, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Definitely not stolen'},
--Stereo prop item
['stereo'] 				 	     = {['name'] = 'stereo', 			  	  		['label'] = 'HIFI', 					['weight'] = 70000, 	['type'] = 'item', 		['image'] = 'stolenstereo.png', 		['unique'] = true, 		['useable'] = true, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Definitely not stolen'},
```

```
--T1 safe key item
['t1_safe_key'] 			 	 = {['name'] = 't1_safe_key', 			 		['label'] = 'Key', 		['weight'] = 500, 		['type'] = 'item', 		['image'] = 'safe_key.png', 			['unique'] = true, 		['useable'] = false, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Probably unlocks something'},
--T2 safe key item
['t2_safe_key'] 			 	 = {['name'] = 't2_safe_key', 			 		['label'] = 'Key', 		['weight'] = 500, 		['type'] = 'item', 		['image'] = 'safe_key.png', 			['unique'] = true, 		['useable'] = false, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Probably unlocks something'},
--T3 safe key item
['t3_safe_key'] 			 	 = {['name'] = 't3_safe_key', 			 		['label'] = 'Key', 		['weight'] = 500, 		['type'] = 'item', 		['image'] = 'safe_key.png', 			['unique'] = true, 		['useable'] = false, 	['shouldClose'] = true,	   ['combinable'] = nil,   ['description'] = 'Probably unlocks something'},
```

You must add all of the items above to your item list, qb-core -> shared -> items.lua or shared.lua

## Finding the bossman

You can change the bossman coords and other options in the main config.lua.

```
-- A random boss will be chosen onResourceStart in sv_public.lua
Config.Bossman = {
    [1] = {
        ["model"] = "ig_malc",
        ["location"] = vector4(246.25, 370.86, 105.74 - 1.0, 158.99),
        ["scenario"] = "WORLD_HUMAN_DRUG_DEALER",
    },
    [2] = {
        ["model"] = "ig_malc",
        ["location"] = vector4(-194.57, -1669.65, 33.57 - 1.0, 254.98),
        ["scenario"] = "WORLD_HUMAN_DRUG_DEALER",
    }
}
```

If you want to edit any of the boss related code you can do so in cl_public.lua, starting at line 300.

## Enabling XP/Levels
You can find the boss_reputation.sql file in the qb-burglary folder.
You must add the boss_reputation table to your database, you can do this by importing the "boss_reputation.sql" file.
Once you have imported the sql file set Config.Reputation = true in the main config.lua