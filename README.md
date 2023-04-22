# PLECAK MOCY


**I'll drop the source code next week, kinda busy right now (now=2023-04-22))**

I bought a dumb phone and as it turns out they have even less features than feature phones.

That's good for avoiding distractions, but when I go outside to ~~touch grass~~
work remotely I do need a Wi-Fi hotspot. I also like to be able to get around
the city and I completely lack the ability to navigate without GPS/jakdojade
(jakdojade is an app that looks up tram routes).

So I figured I can just buy a big-ass powerbank ([big
ass-powerbank](https://xkcd.com/37/)), hook up an old cheap android phone I
have lying around to it and just make it be a hotspot 24/7. Drop that in a
backpack (plecak mocy means "the backpack of power" in Polish), and I am a
walking access point, which is kinda funny. I think I'll just pay the 20 EUR it
costs to get unlimited mobile data here (this is the one thing I like about
Poland lmao) and give out the hostspot password to all my friends.

That solves the hotspot issue, but not the navigation issue, but my phone is
rooted and can run Termux - which means I can host a service right in the
backpack which receives SMS commands from the dumb phone and replies with the
results! (Did I mention that sending/receiving SMS here is also usually just
unlimited, for like, 5 EUR?)

So I quickly bodged some scripts together which listen to commands sent via SMS
and run stuff on the backpack-phone.

[![play](https://user-images.githubusercontent.com/1735193/233795291-5920d0fc-ac20-43a4-a698-6cf5f583fe5c.png)](https://youtube.com/shorts/ATDekq7nJP8)

Currently I have three commands:

* `where` - responds with backpack location from GPS
* `on` - turns on Wi-Fi hotspot (requires root)
* `off` - turns off the hotspot (also, root)

Ah, the phone is also hooked up to [Tailscale](https://tailscale.com/) and runs
sshd in [Termux](https://termux.dev), so I can just type `ssh backpack` and I'm in.

**ALSO:** I'd like to design a case for the thingy that allows me to just pull
the powerbank out and plug it in for charging, and then plop it back in, with
the cables being inserted automatically.


## TODO

- [ ] drop the source code

- [ ] write a simple framework to extend the backpack with new commands

something like:

```python
@backpack.command('where')
def where(message):
  loc = backpack.termux('termux-location')
  message.reply(f'{loc.longitude} {loc.latitude}')
  # or the other way around, I never remember which is which
  
  # TODO: turn this into a human readable address, too - there's no maps on the
  # dumb phone
```

- [ ] don't crash the whole thing when a command fails - figure out a shorter
  format for the stack trace to reply with if an Exception is caught

- [ ] implement some kind of an API to send SMS from other services, so that I
  can for example hook up notifications about Telegram/IRC/Slack mentions to
  SMS, of course making sure only actually important stuff comes through to the
  dumb phone

- [ ] implement rate limiting for SMS - I don't want to get the mobile carrier
  angry at me if the code accidentaly gets stuck in an infinite loop


- [ ] implement a way for the messages to contain "actions" which are just
  clickable links, and once clicked the framework allows to handle them with
  a callback or something

  - The dumb phone I have, [Nokia 110
    4G](https://www.nokia.com/phones/en_int/nokia-110-4g?sku=16LYRB01A01) has
    Opera Mini 4, which, while inadequate for most browsing (that's a good
    thing), can make an HTTP request and links in SMS messages are clickable.

- [ ] Figure out if I can rent city bikes ([Nextbike](https://nextbike.pl/)
  through code, if so I just want to be able to sent an SMS with the bike's ID
  from the dumb phone.
  - The bikes can be rented by just tapping an RFID card connected to the
    account, but that usually just doesn't work and you have to use the app
    anyway.

- [ ] Figure out some API that can give me public transport directions and
  implement commands for that.
  - jakdojade signs all it's API calls, so maybe I can find something easier to
    "hack"

- [ ] Add a command for posting on fedi!!

- [ ] Event notifications from caldav calendars through SMS
    - The calendar on the phone doesn't support syncing and it doesn't store
    it's data on the SD card (Some apps do, which is something I'd like to look
    into in the future, maybe that's a nice way to break stuff c:)

- [ ] Commands to manipulate caldav calendars

- [ ] Phone number spoofing protection - I don't want people to be able to run
  code on my backpack just because they spoofed my number. I think for
  sensitive commands I could make it require clicking a link in the message and
  entering a password through Opera Mini - if it supports cookies it should be
  quite seamless after first log in.

- [ ] backup the phone that runs the code currently and wipe it clean,
  replacing the ROM it runs with something ligher

  - *Where we're going, we don't need Google Play Services*

- [ ] write scripts to deploy Termux with `sshd` and required permissions
  granted through `adb`
  - can Tailscale deployment also be automated?

- [ ] once `sshd` is bootstraped, use something like ansible to deploy the
  solution

- [ ] when we have the deployment automated and we can quickly get up an
  running, maybe I should try compiling LineageOS (or just the kernel) with all
  the modules for Docker? Then my backpack would be able to run stuff without
  weird Android-specific stuff. (postmarketOS is [not well
  supported](https://wiki.postmarketos.org/wiki/Xiaomi_Redmi_Note_7_(xiaomi-lavender))
  and while it would be nice to port it, I am not currently too much into doing
  the work. Recompiling a kernel should be easier.)

## Other ideas:

- If I figure out a way to get live call sound (maybe replace the android phone
  with pinephone to do so?) I could auto-accept calls and get DTMF codes!
  - The dumb phone can save contacts with numbers like `666666666P2137`,
    calling that results in the phone dialing `666666666`, waiting for the call
    to be answered and then sending 2137 through DTMF. This would mean I could
    have commands on speed dial!

- The phone has a camera... The powerbank has 4 LEDs that indicate the
  charge... I could take a picture every now and then and could monitor the
  battery level of the powerbank.


