+++
categories = ["Home Assistant", "Automation"]
date = 2025-11-24T07:30:00Z
description = "Expanding on existing automation with a bathroom occupancy light."
draft = false
image = "/images/2025/11/occupied.jpg"
images = ["/images/2025/11/occupied.jpg"]
slug = "ha-occupancy-indicator"
summary = "Expanding on existing automation with a bathroom occupancy light."
tags = ["Home Assistant", "Automation"]
title = "Making a Scene: Bathroom Occupancy Indicator in Home Assistant"
aliases = [
    "/images/2025/11/24/ha-occupancy-indicator/amp/",
    "/images/2025/11/24/ha-occupancy-indicator/",
    "/images/2025/11/24/ha-occupancy-indicator"
]
+++

For the last few months, my bathroom lighting automation has been rock solid. It is a simple setup: motion is detected, the lights turn on. Motion stops, the lights turn off. It works perfectly, and I haven't had to touch a light switch in ages.

But recently, I realized I was missing a piece of the puzzle.

I found that I would walk all the way there only to find the door closed with the lights on. I needed a way to know if the room was occupied *before* getting up.

Naturally, I decided to hijack one of the living room ceiling lights to act as an occupancy indicator.

## The Goal

The logic seemed simple enough on paper:

1. When the bathroom is occupied (trigger), turn the living room notify light **Red**.
2. When the bathroom is vacant, turn the living room light back to... well, whatever it was before.

And that is where the trouble started.

## The "Restore" Problem

The first step was basic. I adjusted my existing automation to change my chosen indicator light red at the same time as the bathroom light turns on.

The next step, however, gave me pause: what do I do once the bathroom light turns off? I could hard code the light to my preferred shade of white, but what if someone happened to have changed the lights to a different shade? Or had dimmed them? Or had turned them off entirely?

I considered attempting to copy the state of another light in the room, but then I would have to account for numerous scenarios as setting the state of the light is different if it's white, coloured, or even off.

I needed a time machine. I needed a way to snapshot the light's state exactly as it was, change it for the notification, and then restore it later.

## Enter: The Dynamic Scene

Home Assistant has an action called `scene.create`. It takes a snapshot of an entity's current attributes (color, brightness, or off-state).

```yaml
- action: scene.create
  data:
    scene_id: before_bathroom_notification
    snapshot_entities:
      - light.living_room_notify_light
```

When you're ready to return the light to the previous state, you can then apply the saved snapshot.

```yaml
- action: scene.turn_on
  target:
    entity_id: scene.before_bathroom_notification
```

This solved the restoration problem. Whether the light was Blue, Dim White, or Off, the scene captured it.

## The "Double-Dip" Loop

However, motion sensors are chatty. They trigger, then clear, then trigger again while you are still in the room.

I ran into an issue where the automation would restart while the light was *already* Red. The snapshot action would run again, take a picture of the Red light, and save that as the "restore point."

When I left the bathroom, the automation would dutifully restore the light to... Red.

To fix this, I had to wrap the snapshot in a condition. We only want to take a picture if one doesn't already exist.

```yaml
- if:
    - condition: template
      value_template: "{{ states('scene.before_bathroom_notification') == 'unknown' }}"
  then:
    - action: scene.create
      data:
        scene_id: before_bathroom_notification
        snapshot_entities:
          - light.living_room_notify_light
```

## Cleaning Up (Hygiene Matters)

For the logic above to work, we have to delete the scene when we are done.

If we don't delete it, the next time the bathroom is used (say, 4 hours later), the system will see the old scene still floating in memory. It will skip the new snapshot and eventually restore the lighting state from 4 hours ago.

We use `scene.delete` to ensure the slate is wiped clean for the next run.

## The Final Solution

Here is the complete automation, tweaked a little from my real implementation for the sake of demonstration. It handles the snapshot, the notification, the re-trigger protection, and the cleanup. I also added a timeout safety net: if the sensor gets stuck, the light resets after an hour so the living room doesn't stay Red forever.

```yaml
mode: restart

triggers:

  - entity_id:
      - binary_sensor.bathroom_motion
    from: "off"
    to: "on"
    trigger: state

conditions: []

actions:

  - alias: Turn on the bathroom lights
    action: light.turn_on
    data:
      kelvin: 3000
      brightness_pct: "{{ (states('input_number.nightlightbrightness') | float) + 10 }}"
    target:
      area_id: bathroom

  - alias: Capture scene if not already captured
    if:
      - condition: template
        value_template: "{{ states('scene.before_bathroom_notification') == 'unknown' }}"
        alias: Check for existing scene
    then:
      - alias: Capture current state of Living Room Notify
        action: scene.create
        data:
          scene_id: before_bathroom_notification
          snapshot_entities:
            - light.living_room_notify_light

  - alias: Set Living Room Notify Light to Red
    action: light.turn_on
    metadata: {}
    data:
      rgb_color:
        - 255
        - 0
        - 0
      brightness_pct: "{{ (states('input_number.nightlightbrightness') | float) + 10 }}"
    target:
      entity_id: light.living_room_notify_light

  - alias: Wait for motion to no longer be detected
    wait_for_trigger:
      - entity_id:
          - binary_sensor.bathroom_motion
        from: "on"
        to: "off"
        trigger: state
    timeout:
      hours: 1
      minutes: 0
      seconds: 0
      milliseconds: 0
    continue_on_timeout: true

  - alias: Wait for 30 seconds of no motion
    delay:
      hours: 0
      minutes: 0
      seconds: 30
      milliseconds: 0

  - alias: Turn off the bathroom lights
    data: {}
    action: light.turn_off
    target:
      area_id: bathroom

  - alias: Restore state of Living Room Notify Light
    action: scene.turn_on
    target:
      entity_id: scene.before_bathroom_notification

  - alias: Cleanup temporary light state
    action: scene.delete
    target:
      entity_id: scene.before_bathroom_notification
```

One thing you'll note here is that I'm setting the notify light to the same brightness as the bathroom light. This uses a helper that adjusts the target brightness based on the time of day and it is shared across a number of my Home Assistant automations. It's very helpful in preventing flash banging yourself at 3am.

## Wrap Up

{{< figure src="/images/2025/11/occupied-example.jpg" >}}

It took a few iterations to get from a basic red light to a robust notification system, but the result is seamless. The living room vibe is preserved, and I no longer have to walk down the hall just to find a locked door.

Now, if you’ll excuse me, I have some other automations to shed some light on.

Credit: Photo by [Al Butler](https://unsplash.com/@albutler) on [Unsplash](https://unsplash.com/photos/a-close-up-of-a-sign-on-a-door-ifCDP8ALJOY).
