# auto-enter-pin
This script is meant to automatically enter the SIM pin on startup on SailfishOS. It should also work in Ubuntu Touch which uses the same telephony stack.

The script can handle dual-SIM configurations, and even controling in which order the SIMs should come up.

## Why do I want this? 

* Whenever I boot my phone I want it to be online.
 Even if sailfish boots fast, I just do not want to wait until I can enter my pin. 
 With this script I just press the power button and put the phone back in my pocket.

* There is no reduced security : the phone lock code (with automatic lock) already
 protects against unwanted usage of the phone, including for calling.
(But you do not want to disable pin, so that the sim is protected against usage in another phone.)

* It seems I'm not the only one to want this behavior. See on together.jolla.com [here](https://together.jolla.com/question/189508/auto-unlock-sim-card-dont-ask-for-the-sim-pin/) and [here](https://together.jolla.com/question/76289/reboot-without-pin-code-query/).

* Suppose you loose your phone, and by the time you realize, it's battery ran empty.
However if you're lucky, some nice person finds it, recharges it and turns it on...
Tada, now you can get in touch with that person!

* If your phone gets stollen, the thief will eventually on/off the phone to disconnect it from the 
carrier. With this auto-reconnection you can still contact the phone and, if you have a script
that whatches for specific sms messages, you can do whatever you have planned _in advance_ for
such event (erase data, get the gps coordinates...). OTOH, if the phone is flashed or factory-reset... 

* In dual SIM phones, controlling which SIM comes up first [may have some importance for Bluethooth pairing](https://together.jolla.com/question/168601/dual-sim-choose-sim-for-bluetooth-hfp-connection/).

## How does it work?

At startup, systemd lauches the script automatically

The script watches for dbus signals from ofono that indicate the modem is
coming online and ready to receive pin code, then we enter the pin.

If this sequence does not occur for a given time,
either the pin is not locked or it has already been entered and we quit.

The script needs only ordinary user rights for execution and installation

## installation 

Download the auto-enter-pin folder to the home directory (`/home/nemo/`)

*Edit configuration :*
- Edit the content of `auto-enter-pin.conf` and enter your pin code
_Yes, I know, this is not very safe. OTOH if someone has access to this file
inside your phone, you are already pretty much screwed!_

- YOU MUST ALSO edit the `simcardidentifier` or comment it out otherwise the script won't do anything!
You can get your own sim identifier by running 
`list-modems` in the terminal (the script `list-modems` is provided in the repo).

- Set or remove the sim priority according to your needs.

*Manual install of the service :*
- Copy the file `auto-enter-pin.service` to `/home/nemo/.config/systemd/user/`
- Then, in the terminal :
```
systemctl --user enable auto-enter-pin
systemctl --user start auto-enter-pin
```

*Automatic install of the service :*
- in a terminal run `sh installer`

After reboot, the phone should get online alone after showing the phone unlock screen 
for a few seconds. If it does not work, look inside the `auto-enter-pin.log` to see
what went wrong.

**If it does not work right away check that the pin is correct and check the log. BE CAREFUL THAT YOU CAN LOCK YOUR SIM!**

If you want to deactivate the script, in the terminal :
```
systemctl --user disable auto-enter-pin
```
