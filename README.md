As Doug Palmer said, geometry is the most useless part of the xkb. The only purpose I could find to this config is the xkbprint utility, which, well, prints out a map of one's current xkb configuration, with some flags such as what modifier levels should be included, and the output file formats, which are afaict all pretty annoying and pretty 90's in flavour. [include my own nice xkbprint output here]

Keycodes is already more interesting, or rather, it's kind of fundamental. Let me first present the xev. You already have it installed on your machine, but being aimed, I guess, at people who are *seriously* hacking the X instead of just tweaking their keymaps, it's kind of a firehose. It looks like it returns every single input event that the X receives at that point. I decided to modify this nice lil' util so that it prints out only the keyboard events, which is much more manageable. 

The xkb, then, prints out a lot of stuff, but here we're interested in the _keycode_ element. Keycodes, it seems, are what the X receives in terms of pre-processed, raw key input events. For example, my q key produces KeyPress 24, then KeyRelease 24. The key chord "shift q" merely produces KeyPress 50, KeyPress 24, KeyRelease 24 then KeyRelease 50. So far, it looks like all keycodes are small integers. The job of the keycodes config, then, is to translate those keycodes into the xkb's symbolic representation.


[nota: I don't know how to call that mid-tier representation, so it's gonna be xxx for a future regex's sake]

As typical for the X style of overengineered software, xxxs are, for most, just barely more symbolic than the original keycodes. To go on with my previous example, my key 24 produces the xxx <AD01>. Fortunately, my keycode 23 produces <TAB>. Now *that's* helpful. 

evdev looks a priori like a standard for modern keyboards; there are some files in the keycodes directory that come from forgotten eras and dead manufacturers, I don't even know who uses anything besides evdev. My original multilingual french included aliases(qwerty), which I don't remove because I'm scared of the keycodes section. 

I don't even know what model does.

keycodes is where everything remotely relevant takes place. 

Right off the bat, my file has a "partial modifier_keys" clause, which maps a specifik key (it seems to be mostly arbitrary?) to a "group", which seems to be... Uh... It seems like a given chord of modifiers produces a given group, and that every regular key produces one value per group, such that, for example, none, shift, and alt-shift produce groups (example) non, shift and alt_shift, and that q produces respectively q, Q and {. 

Now Modifier keys have to be mapped like regular ones before being mapped to group chords, using two different syntaxes which I don't fully grasp yet. 
