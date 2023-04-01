---
layout: default
title: helloWatt changelog
lang: fr_FR
pluginId: helloWatt
---
# helloWatt 

Plugin allowing the recovery of consumption of the communicating meter ** by querying the customer account *helloWatt*. As the data is not made available in real time, the plugin retrieves the gaz consumption data from the day before each day.

2 types of consumption data are accessible :
- the **daily consumption** *(in kWh and m3)*.
- the **monthly consumption** *(in kWh and m3)*.

>**Important**      
>You must have a helloWatt customer account. The plugin retrieves information from the game *my space* <a href="https://www.hellowatt.fr/mon-compte" target="_blank">of the helloWatt website</a>, you must therefore check that you have access to it with your usual identifiers and that the data is visible there. Otherwise, the plugin will not work.

# Configuration

## Plugin configuration

The plugin **helloWatt** does not require any specific configuration and should only be activated after installation.

Two options are available in the plugin's configuration to manage behavior when a captcha is detected at connection time:
- Add an entry in the message center (checked by default)
- Disable the equipment

You can also specify the number of days delay generally witnessed in order to prevent the plugin querying the website for nothing.

The webste allows you to define thresholds for each mnth (in kWh). The plugin is retrieving these values and stores them in a dedicated command.
If you prefer to compute thresholds based on last year consumptions, uncheck the option.

The data is checked every hour between 4 a.m. and 10 p.m. and updated only if not available in Jeedom.

## Equipment configuration

To access the different equipment **helloWatt**, go to the menu **Plugins → Energy → helloWatt **.

> **To know**    
> The button **+ Add** allows you to add a new account **helloWatt **.

On the equipment page, fill in the'**Login** as well as the **Password** of your customer account *helloWatt* then click on the button **Save**.

You can also specify the PCE if you own several gas meters.

The option **Force data retrieval** to force data retrieval even if data is already present for concerned periods.

The plugin will then check the correct connection to the site *helloWatt* and retrieve and insert in history :
- **daily consumption** : the last 365 days,
- **monthly consumption** : the last 12 months,
- **maximum, minimum and average monthly consumptions of similar profiles of the last 12 months**
- **monthly thresholds** as defined on the website

Four widget's tmplates are available. The ones with comparison inform you how your previous month consumption compares with similar profiles.

# Potential issues

From time to time, a captccha might be required to connect to the website.
It will be mentioned in the plugin's logs and the plugin will react depending on the plugin's configuration you defined.
In this case, you need to connect to the website "manually" and resolve the captcha.

# Remarks

During tests, it appeared that the helloWatt website is quite "unstable" with direct impacts on the plugin. On Jeedom, the plugin is configured to gather data every hour. It may happen that it does not work each time: no issue, just wait for the next scheduled run.

This plugin heavily relies on how the helloWatt website is structured/designed. Any change on the website will most probably break the plugin and will then require to perform code changes on the plugin.

# Contributions

This plugin is opened for contributions and even encouraged! Please submit your pull requests for improvements/fixes on <a href="https://github.com/hugoKs3/plugin-helloWatt" target="_blank">Github</a>

# Credits

This plugin has been inspired by the work done by:

-   [Jeedom](https://github.com/jeedom)  through their Enedis plugin:  [plugin-enedis](https://github.com/jeedom/plugin-enedis)
-   [empierre](https://github.com/empierre)  through his similar work done for Domoticz:  [domoticz_gaspar](https://github.com/empierre/domoticz_gaspar)

# Disclaimer

-   This code does not pretend to be bug-free
-   Although it should not harm your Jeedom system, it is provided without any warranty or liability

# ChangeLog
Available [here](./changelog.html).
