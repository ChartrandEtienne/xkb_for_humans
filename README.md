As Doug Palmer said, geometry is the most useless part of the xkb. The only purpose I could find to this config is the xkbprint utility, which, well, prints out a map of one's current xkb configuration, with some flags such as what modifier levels should be included, and the output file formats, which are afaict all pretty annoying and pretty 90's in flavour. [include my own nice xkbprint output here]

Keycodes is already more interesting, or rather, it's kind of fundamental. Let me first present the xev. You already have it installed on your machine, but being aimed, I guess, at people who are *seriously* hacking the X instead of just tweaking their keymaps, it's kind of a firehose. It looks like it returns every single input event that the X receives at that point. I decided to modify this nice lil' util so that it prints out only the keyboard events, which is much more manageable. 

The xkb, then, prints out a lot of stuff, but here we're interested in the _keycode_ element. Keycodes, it seems, are what the X receives in terms of pre-processed, raw key input events. For example, my q key produces KeyPress 24, then KeyRelease 24. The key chord "shift q" merely produces KeyPress 50, KeyPress 24, KeyRelease 24 then KeyRelease 50. So far, it looks like all keycodes are small integers. The job of the keycodes config, then, is to translate those keycodes into the xkb's symbolic representation.


[nota: I don't know how to call that mid-tier representation, so it's gonna be xxx for a future regex's sake]
[nota 2: Doug Palmer calls those "symbolic keycodes". Nice]

As typical for the X style of overengineered software, xxxs are, for most, just barely more symbolic than the original keycodes. To go on with my previous example, my key 24 produces the xxx <AD01>. Fortunately, my keycode 23 produces <TAB>. Now *that's* helpful. 

evdev looks a priori like a standard for modern keyboards; there are some files in the keycodes directory that come from forgotten eras and dead manufacturers, I don't even know who uses anything besides evdev. My original multilingual french included aliases(qwerty), which I don't remove because I'm scared of the keycodes section. 

I don't even know what model does.

keycodes is where everything remotely relevant takes place. 

Right off the bat, my file has a `"partial modifier_keys"` clause, which maps a specifik key (it seems to be mostly arbitrary?) to a "group", which seems to be... Uh... It seems like a given chord of modifiers produces a given group, and that every regular key produces one value per group, such that, for example, none, shift, and alt-shift produce groups (example) non, shift and `alt_shift`, and that q produces respectively q, Q and {. 

Now Modifier keys have to be mapped like regular ones before being mapped to group chords, using two different syntaxes which I don't fully grasp yet. 

Where *is* that stuff?
======================
On my Debian, it's under /usr/share/X11/xkb


How to create your own keymap!
==============================

Start by harvesting some information on your current keymap. So far, I realized that it's more cautious to start with as much as possible of your current keymap and slim it down and modify it starting from there, as hopefully, your current keymap will be at least *functional*. As you will probably notice while sifting through the configuration settings we'll gather just now, there's a *lot* of stuff in there, and a surprising amount of it is actually useful. I once lost control of so many keys due to a reckless deletion that I had to SSH into that machine to revert it to an earlier setting. 

You can get a raw dump of the current state of the xkb server with 

	xkbcomp $DISPLAY xkbcomp_output.xkb

At the top of this file, you have the `xkb_keycodes` section, which maps xev's raw keycodes into "symbolic keycodes". You probably want to skip immediately to the `xkb_symbols` section. You get a line looking like `xkb_symbols "pc+ca(multix)+inet(evdev)+level3(win_switch)"`, meaning your current keyboard symbols configuration is a mish-mash of all those +-separated sets of symbols. For element say `ca(multix)`, it means that from within the file `$XKB/symbols/ca` the symbol set `multix` is included. As you will probably notice, there is a lot of overlap and settings get overwritten in ways I don't really understand yet. 

It might be a good idea to separate the `xkb_symbols` section of this file and use it as the basis for one's custom symbols list; however, the remainder of the settings can be obtained with 

	setxkbmap -print -verbose 10 >setxkbmap_print_output

The informations thus gathered can be used to create the `.Xkbmap` file. Those seem to be pretty stable; for occidental keyboards, it looks like only the `symbols` part changes. 


So what about with those symbols anyways?
=========================================

There's one obvious thing that's actually really safe to play around: the simplest case of a key map is of the form 

	<symbol> {[keysim1, keysim2, keysim3]};

Take for example 

    key <AD05>	{ [         t,           T,     slash, NoSymbol,
		       tslash,      Tslash ]	};

`<AD05>` is quite simply the `T` key on my keyboard, which is quite simply mapped to `t` when pressed alone, `T` when pressed with shift, `/` when pressed with right `AltCar`, and a bunch of other random stuff when pressed with keys I don't even think I have on my keyboard. 

Hopefully one day I'l know enough about the modifier keys architecture to explain I even got mine to work properly. 
