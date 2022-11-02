# Pixie Plus for Home Assistant 

**First, a warning. Installation of this integration requires a bit of knowledge and is not straight forward - see below for more details. In addition, the 
integration was only tested on my system and is not sure to work on other systems without modifications. It is not supported but I may be able to help.** 

This is a Pixie Plus integration for Home Assistant. Pixie Plus is a line of smart light products and other smart home devices made by an Australian
company called SAL. After I published the initial integration which only supported on/off light switches, I contacted the Pixie Plus team and they kindly
sent me a box with different hardware which I was able to implement in the integration. As such, the integrations currently supports the following device:
    
- Smart Switch G3 - SWL600BTAM
- Smart dimmer G3 - SDD300BTAM
- Smart plug - ESS105/BT
- Flexi smart LED strip - FLP12V2M/RGBBT
- Flexi Streamline - FLP24V2M
- LED Strip Controller - LT8915DIM/BT
- Smart Socket Outlet - SP023/BTAM
- Dual Relay Control - PC206DR/R/BTAM
- Blind & Signal Control - PC206BS/R/BTAM

For compatible devices, it supports dimming, colours and effects. The integration doesn't support built in Pixie Plus groups, scenes, schedules or timers 
because I assume that you would use Home Assistant for those functions anyway. It only works if you have a Pixie Plus Hub and doesn't support Bluetooth
capabilities. All the devices need first to be setup in the Pixie Plus app to work. The integration requires the cloud because of the way they implemented 
their system (no direct access to the local hub IP, only through their servers). 

I am not a programmer, and my code is messy and inefficient. I only wrote this because I didn’t like that I needed SmartThings as a mediator between HA and 
the Pixie Plus lights.  

Installation: 

The easy part – copy the files into the custom_components folder in HA and restart HA. You then add the integration from the UI by clicking on the 
'ADD INTEGRATION' button under Settings / Devices and Services / Integrations and search for Pixie Plus. It will then ask you for your Pixie Plus 
credentials (username and password) and 3 other numbers (see below).

The difficult part – to use the integration you will need 3 parameters that are quite difficult to get. They are called ApplicationID, InstallationID and 
JavaScriptKey. I assume they are generated by the Pixie Plus client (i.e. mobile app) for each user/client combination and are required for communication 
with the server. 

To find those parameters you will need to use mitmproxy and a rooted virtual android phone (I used Android SDK). You will need to upload to the phone the 
mitmproxy CA certificate as a system (not user) certificate. [This link](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/) is 
very helpful. Then install the Pixie Plus app on the virtual phone and set the proxy on the virtual phone to the mitmproxy. Once all this is done you will
be able to see the communication between the Pixie Plus client and the server and locate the needed parameters. From my experience to date, they don't 
change. There may be an easier way to find those parameters so let me know if you discover it. 

Usage: 

Once you provide all the needed credentials, the integration should work, and you should see all your devices. However, a further step is needed for the   
cover platform to support the Blind & Signal Control device. You need to setup the cover device in configuration.yaml, for example:

```
pixie_plus:
  cover:
    blind_sc_171: # name of the covers as in the Pixie Plus app but lower case and underscores (_) instead of spaces
        open: 2 # the number represent the location of the order of the button in the cover control pannel in the Pixie Plus app
        close: 8
        stop: 5
```
The number assigned to the open / close / stop represents the location of the button in the cover control pannel in the Pixie Plus app as follow:
```
1, 2, 3
4, 5, 6
7, 8, 9
```
Note that if you moved a button in the app to a new location you need to use the original location and not the new one. 

Known issues:
- The Smart plug - ESS105/BT has a USB port that can be controlled by the app and the integration. However, I wasn't able to find in the 
current state of the USB (on or off) published by the Pixie Plus hub. However, there is an update if the state changes. As such, I can't determine the 
initial state of the USB when loading the integration. After rebooting AH the USB state will restore the previous state but if the USB state changed when
HA was down, it won't be recorded.

I can’t guarantee the integration will work on your setup. Feel free to use, modify and add to the code as needed. I may be able to answer an occasional 
question (if I know the answer) but generally won’t have much time to spend on that. 
