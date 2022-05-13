# tl;dr: Mapping all devices/gear in a Helvar system to entities in Home Assistant wouldn't support realtime state changes

The sad truth is that there's no - publicly supported, at least - way of listening to state changes to the individual pieces of hardware in any of Helvar's router-based lighting control systems.

If such a mechanism would exist, it would be straight-forward to expose each piece of hardware - luminaires, PIRs, relays, ... - as entities in the appropriate domains in Home Assistant - light, sensor, switch, ... - and get realtime state changes for free.

But hey... all is not lost!

Helvar relies heavily on DALI *groups* and DALI *scenes*, and these *do* propagate state changes to any third parties that are interested in them. So why is this great news? Depending on *how* you set up your DALI groups and DALI scenes, you can workaround *some* of the above limitations.

This document discusses some ways you could do this, and how you would configure this component to reach your desired behaviour.

But first, some background...

## Helvar's router-based lighting control system

Helvar provides [router-based lighting control systems](https://helvar.com/product-category/lighting-control/routers/), which can be likened to a standard [DALI system](https://en.wikipedia.org/wiki/Digital_Addressable_Lighting_Interface) with some sugar on the top (think: more groups, more scenes).

Since it's pretty annoying to type a *router-based lighting control system from Helvar*, we'll refer to such a system as a *Helvar system* going forward. Sorry, not sorry.

### Helvar Designer

If you've only interacted with your Helvar system through the wallplates in your conference room, you should know that [Helvar's Designer software suite](https://helvar.com/product/designer-software-suite/) is used to provision and configure all Helvar systems.

At some point, you might want to use this software to tweak your DALI groups and DALI scenes.

### Helvar and DALI: Devices, groups, broadcasts...?

If you're familiar with DALI, you know that a DALI system can contain *input devices* and *control gear*. We're talking luminaires, relays, blinds controllers, PIRs, physical buttons... No, we're no going to list them all, but trust us - the list goes on.

Each physical device you connect to a DALI system
will gets its own unique address. Think of it as something similar to an IPv4 address.

You interact with your devices by sending DALI commands across the system. Commands can target...

- a specific device (read: address), or...
- all devices (read: broadcast), or...
- a group of devices

A Helvar system, and the public protocol it exposes, is primarily designed around the interaction with groups. Hence, we'll be focusing the discussion on groups going forward.

### The group entity

The concept of groups is very simple: a group in a Helvar system has a unique identifier (*integer, range 1-16340*). It also has a list of all devices that you've indicated should belong to the group.

Each group may also have an optional name.

And that's it, really. Really? Really!

### Some group/scene gotchas

While not important to the everyday user, the following is key for people who need to configure a Helvar system: a *single* device might be present in *multiple* groups. This is nothing new for seasoned DALI users, but the way the Helvar software suite presents groups... might lead you to think that scene information lives on the group level.

When/if this happens, remember what we said about groups above: a group is just an entity with a unique ID, itself containing a list of devices (addresses) that you indicated should belong to the group.

In other words: If you're recalling scene #24 for group #152, all group #152 can do is tell each device belonging to it to to recall *their* own scene #24.

In practice, this only matters if - and only if - you've got devices belonging to multiple groups.

### Example: A group representing a single conference room

Let's play around with the idea that your building has an imaginary conference room named "The Abyss".

This imaginary room features a couple of luminaires, a few relays, a blinds controller, a PIR and some control buttons on the wall (for recalling different scenes).



