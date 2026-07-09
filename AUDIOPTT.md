# Networked Audio and Push-to-Talk

This is my setup to allow [two-way networked audio](#networked-audio) and a [PTT trigger](#ptt-trigger) from the MSFS PC and the BATC PC.

> For more seamless use, you could also use [Mouse Without Borders](https://www.microsoft.com/en-in/download/details.aspx?id=35460) or [Input Leap](https://github.com/input-leap/input-leap) for full mouse & keyboard from the sim pc.

# Networked Audio

I use [Voicemeeter](https://voicemeeter.com/), a network-capable software audio mixer, to link the BATC output and microphone input in my remote-batc setup.

Voicemeeter has a donationware license, which I'd highly recommend purchasing to support the dev.

## Voicemeeter Setup

For the purposes of this tutorial, I use Voicemeeter Potato, but other versions may also work. Detailed guidance on setting VM up with your whole PC/Sim is out of scope of this tutorial, please refer to the detailed user guide, or good tutorials on the VM website.

### MSFS PC

Voicemeeter Main Settings:
- Add your mic as a hardware input device on the first `Strip[0]`, and enable output to Bus B1 (`Bus[5]`) virtual output.
- Don't select an input device on the second `Strip[1]`, but enable output to Bus A1 (`Bus[0]`)
- Left click **Bus A1** (on the top Hardware Out section), and choose your headphones/physical speaker you want BATC audio to go to.

VBAN Settings:
- Open VBAN settings, left click the button at the top of the VM window.
- Set the first Incoming Stream, give it the name `fromBATCtoMSFS`, then add the BATC PC's IP address, and select Destination as `In#2` (this sends the incoming BATC audio to `Strip[1]`)
- Set the first Outgoing Stream, give it a different name `fromMSFStoBATC`, then add the BATC PC's IP as per the last step, and select Source Bus B1 (`Bus[5]`) (this sends the Mic -> Strip[0] -> Bus[5] -> VBAN over the network -> BATC PC)
- Ensure VBAN is `On`, toggle the button top left of the VBAN settings. Also toggle `On` next to both the incoming and outgoing streams.

### BATC PC

You need to mirror the settings from the MSFS PC.

Voicemeeter Main Settings
- Don't select an input device on the first `Strip[0]`, but enable output to Bus B1 (`Bus[5]`)
- Set the first Virtual Input `Strip[5]` to output to Bus B2 (`Bus[6]`)

VBAN settings:
- Open VBAN settings
- Set the first Incoming Stream, give it the name matching corresponding name `fromMSFStoBATC`, then add the MSFS PC's IP address, and select Destination as In#1 (this sends the incoming Mic audio to `Strip[0]`)
- Set the first Outgoing Stream, give it the matching corresponding name `fromBATCtoMSFS`, then add the MSFS PC's IP address as above, and select Source Bus B2 (`Bus[6]`)
- Ensure VBAN and the configured streams are `On`

Windows/BATC settings:
- In BeyondATC settings you now need to set the **Input Device** as the Bus B1 (`Bus[5]`) input device, Windows calls this `Voicemeeter Out B1`.
- In Windows Volume mixer, set the **BeyondATC.exe Output** to the `Voicemeeter VAIO` (`Strip[5]`) virtual output (this sends BATC Output -> `Strip[5]` -> VBAN -> MSFS PC)

---

Once configured on both PCs, which can be a little confusing at first with Bus/Strip naming conventions across both VM/VBAN you should have something like this:

```
BATC PC                                                        MSFS PC
BATC -> Strip[5] -> Bus[6]   ----> VBAN (fromBATCtoMSFS) ----> Strip[1] -> Bus[0]   -> your_ears
BATC <- Bus[5]   <- Strip[0] <---- VBAN (fromMSFStoBATC) <---- Bus[5]   <- Strip[0] <- your_mouth
```

---

# PTT Trigger

Using Voicemeeter Macros over the VBAN network, it is possible to have a hardware or keyboard button trigger on the MSFS PC trigger BATC PTT.

## MSFS PC

- Open Voicemeeter Macro Buttons app - it is installed alongside Voicemeeter
- Open the settings of the first button by right clicking it
- Give it a name `PTT`
- Select `Push Button` as Button Type
- Set Keyboard Shortcut or HID Device Button depending on your PTT key/method
- Request for Button ON: `SendText("vban1",Command.Button[0].State=1;);`
- Request for Button OFF: `SendText("vban1",Command.Button[0].State=0;);`

On pressing the shortcut or joystick button this will send a command over the VBAN link to the BATC PC, which will trigger `Button[0]` on its Macro Buttons.

## BATC PC

- Open the first button on in the Macro Buttons app
- Give it a name `PTT`
- Select `Push Button` as Button Type
- Request for Button ON: `System.KeyDown("F9")`
- Request for Button OFF: `System.KeyUp("F9")`

- Set F9 as the PTT key in BATC settings


This will press/release keyboard key `F9` and the BATC PTT trigger when the MSFS PC PTT/Macro Button is pressed.
