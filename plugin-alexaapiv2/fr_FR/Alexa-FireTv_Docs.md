[Sigalou](https://www.sigalou-domotique.fr/author/sigalou) [Domotique](https://www.sigalou-domotique.fr/category/domotique), [Jeedom](https://www.sigalou-domotique.fr/category/domotique/jeedom-2)

Alexa-Fire TV Documentation
---------------------------

![](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2020/11/alexafiretv_icon.png)

*   **Documentation**
*   Todo-List / Changelog
*   Forum dédié
*   [Chat beta-testeurs](http://alexaapi.slack.com)

Présentation du plugin
----------------------

\[Doc en cours de rédaction\]

Installation du Plugin
----------------------

Très important, Alexa-Fire TV a besoin du [plugin Alexa-API](http://jeedom.sigalou-domotique.fr/alexa-api-documentation) _(gratuit)_ pour dialoguer avec vos Alexa donc :

### Étape 1 : Installer le [plugin Alexa-API](http://jeedom.sigalou-domotique.fr/alexa-api-documentation)

### Étape 2 : Installer le Plugin Alexa-Fire TV

#### depuis le Market, catégorie Multimédia ou en tapant son nom dans le champ de recherche

**Note sur les versions :**

Vous avez le choix entre la version **Stable** ou la version **Beta**.

Beaucoup de nouvelles fonctionnalités sont toujours plus présentes sur la Beta que sur la Stable mais elles sont en test.

Si vous êtes joueur et curieux, vous pouvez installer la version Béta.

Nota  Vous n’avez pas besoin d’installer Jeedom en Beta (c’est plutôt déconseillé d’ailleurs) pour installer le plugin en Béta.

Vous pouvez assez facilement passer d’une version Beta à une version Stable et réciproquement, il suffit de réinstaller par dessus l’autre version.

### Étape 3 : Activer le Plugin

###  ![Screenshot 2020 02 04 Alexa API Jeedom](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexa_-_API_-_Jeedom.png)

### Étape 4 : Installer les dépendances

![](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2020/11/dependances.png)

### Étape 5 : Retourner dans le [plugin Alexa-API](http://jeedom.sigalou-domotique.fr/alexa-api-documentation)

### ![Screenshot 2020 02 04 Alexaamazonmusic Jeedom](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexaamazonmusic_-_Jeedom.png)

### Étape 6 : Relancer le SCAN

![Screenshot 2020 02 04 Alexaapi Jeedom](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexaapi_-_Jeedom.png)

Les devices Fire TV vont être générés automatiquement

Allez dans Alexa-Fire TV pour les visualiser

Autoriser l’accès ADB sur chaque Fire-TV
========================================

Cette section n’est pas documentée pour l’instant, utilisez l’excellent Tuto de AtlasWeb pour [**récupérer l’adresse IP de votre Fire-TV et activer ADB**](https://www.atlasweb.net/installer-et-configurer-adblink-pour-amazon-fire-tv-et-stick/).

> [Installer et configurer adbLink pour Amazon Fire TV et Stick.](https://atlasweb.net/installer-et-configurer-adblink-pour-amazon-fire-tv-et-stick/)

A noter que certains beta testeurs avaient un message « unautorised » et ont dû désactiver et réactiver 1 ou 2 fois pour que cela fonctionne.

Renseigner l’IP de vos Fire-TV
==============================

L’adresse IP est donnée par la procédure décrite au paragraphe précédent ou par votre routeur.

![](http://jeedom.sigalou-domotique.fr/wp-content/uploads/2020/11/ipfiretv.png)

En cas de souci
===============

Tester une connexion simple à ADB en SSH
----------------------------------------

Ouvrir une connexion SSH sur votre Jeedom (Puty, Moxaxterm,…)

Vérifier la liste des devices connectés :

> adb devices

Essayer de connecter son Fire TV (remplacer par l’IP de votre Fire TV)

> adb connect 192.168.0.50

Re-vérifier la liste des devices connectés :

> adb devices

Si votre Fire TV apparait avec la mention **device**, c’est bon

Si vous avez un message d’erreur **Connection refused**, c’est que l’autorisation ADB n’est pas opérationnelle, reprenez le paragraphe _Autoriser l’accès ADB sur chaque Fire-TV_

Documentation Beta-Testeurs et Développeurs
===========================================

Cette partie permet aux développeurs ou beta-testeurs d’avoir un aide mémoire

adb shell dumpsys window windows | grep -E 'mFocusedApp'| cut -d / -f 1 | cut -d ' ' -f 7

*   accueil firetv : com.amazon.tv.launcher
*   kodi : org.xbmc.kodi
*   netflix : com.netflix.ninja
*   disney+ : com.disney.disneyplus
*   amazon video : com.amazon.firebat
*   molotov : tv.molotov.app
*   youtube : com.amazon.firetv.youtube
*   amazon music : com.amazon.bueller.music

### commandes media

adb shell media dispatch pause

avec :

play, pause, play-pause, mute, headsethook,  
stop, next, previous, rewind, record, fast-forword.

sudo adb -s 192.168.0.38:5555 shell dumpsys power -h

> POWER MANAGER (dumpsys power)
> 
> Power Manager State:  
> mDirty=0x0  
> mWakefulness=Awake  
> mWakefulnessChanging=false  
> mIsPowered=true  
> mPlugType=2  
> mBatteryLevel=100  
> mBatteryLevelWhenDreamStarted=100  
> mDockState=0  
> mStayOn=false  
> mProximityPositive=false  
> mBootCompleted=true  
> mSystemReady=true  
> mHalAutoSuspendModeEnabled=false  
> mHalInteractiveModeEnabled=true  
> mWakeLockSummary=0x0  
> mUserActivitySummary=0x2  
> mRequestWaitForNegativeProximity=false  
> mSandmanScheduled=false  
> mSandmanSummoned=false  
> mLowPowerModeEnabled=false  
> mBatteryLevelLow=false  
> mLastWakeTime=14066670 (296411 ms ago)  
> mLastSleepTime=14041490 (321591 ms ago)  
> mLastUserActivityTime=14066670 (296411 ms ago)  
> mLastUserActivityTimeNoChangeLights=9264189 (5098892 ms ago)  
> mLastInteractivePowerHintTime=14066670 (296411 ms ago)  
> mLastScreenBrightnessBoostTime=0 (14363081 ms ago)  
> mScreenBrightnessBoostInProgress=false  
> mDisplayReady=true  
> mHoldingWakeLockSuspendBlocker=false  
> mHoldingDisplaySuspendBlocker=true
> 
> Settings and Configuration:  
> mDecoupleHalAutoSuspendModeFromDisplayConfig=false  
> mDecoupleHalInteractiveModeFromDisplayConfig=false  
> mWakeUpWhenPluggedOrUnpluggedConfig=true  
> mWakeUpWhenPluggedOrUnpluggedInTheaterModeConfig=false  
> mTheaterModeEnabled=false  
> mSuspendWhenScreenOffDueToProximityConfig=false  
> mDreamsSupportedConfig=true  
> mDreamsEnabledByDefaultConfig=true  
> mDreamsActivatedOnSleepByDefaultConfig=true  
> mDreamsActivatedOnDockByDefaultConfig=true  
> mDreamsEnabledOnBatteryConfig=false  
> mDreamsBatteryLevelMinimumWhenPoweredConfig=-1  
> mDreamsBatteryLevelMinimumWhenNotPoweredConfig=15  
> mDreamsBatteryLevelDrainCutoffConfig=5  
> mDreamsEnabledSetting=true  
> mDreamsActivateOnSleepSetting=true  
> mDreamsActivateOnDockSetting=true  
> mDozeAfterScreenOffConfig=false  
> mLowPowerModeSetting=false  
> mAutoLowPowerModeConfigured=false  
> mAutoLowPowerModeSnoozing=false  
> mMinimumScreenOffTimeoutConfig=10000  
> mMaximumScreenDimDurationConfig=7000  
> mMaximumScreenDimRatioConfig=0.20000005  
> mScreenOffTimeoutSetting=300000  
> mSleepTimeoutSetting=1200000  
> mMaximumScreenOffTimeoutFromDeviceAdmin=2147483647 (enforced=false)  
> mStayOnWhilePluggedInSetting=0  
> mScreenBrightnessSetting=107  
> mScreenAutoBrightnessAdjustmentSetting=0.0  
> mScreenBrightnessModeSetting=0  
> mScreenBrightnessOverrideFromWindowManager=-1  
> mUserActivityTimeoutOverrideFromWindowManager=-1  
> mTemporaryScreenBrightnessSettingOverride=-1  
> mTemporaryScreenAutoBrightnessAdjustmentSettingOverride=NaN  
> mDozeScreenStateOverrideFromDreamManager=0  
> mDozeScreenBrightnessOverrideFromDreamManager=-1  
> mScreenBrightnessSettingMinimum=10  
> mScreenBrightnessSettingMaximum=255  
> mScreenBrightnessSettingDefault=171
> 
> Sleep timeout: 1200000 ms  
> Screen off timeout: 300000 ms  
> Screen dim duration: 7000 ms
> 
> Wake Locks: size=0
> 
> Suspend Blockers: size=4  
> PowerManagerService.WakeLocks: ref count=0  
> PowerManagerService.Display: ref count=1  
> PowerManagerService.Broadcasts: ref count=0  
> PowerManagerService.WirelessChargerDetector: ref count=0
> 
> Display Power: state=ON
> 
> Wireless Charger Detector State:  
> mGravitySensor=null  
> mPoweredWirelessly=false  
> mAtRest=false  
> mRestX=0.0, mRestY=0.0, mRestZ=0.0  
> mDetectionInProgress=false  
> mDetectionStartTime=0 (never)  
> mMustUpdateRestPosition=false  
> mTotalSamples=0  
> mMovingSamples=0  
> mFirstSampleX=0.0, mFirstSampleY=0.0, mFirstSampleZ=0.0  
> mLastSampleX=0.0, mLastSampleY=0.0, mLastSampleZ=0.0

