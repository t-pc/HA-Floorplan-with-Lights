# HA-Floorplan-with-Lights
My Custom Picture-Elements Card with CSS based lights that can be adopted to any layout.

I used the included Picture-Elements card and [Card-Mod](https://github.com/thomasloven/lovelace-card-mod)

<img src="IMG/Advanced.png" width="600"></img>

## Feature Showcase
<details>
<summary>Individual areas and icons light up</summary>
<video src="https://github.com/user-attachments/assets/7c95b974-b189-4f15-a868-d1d5faeb4d9f"></video>
</details>

<details>
<summary>Light colors show up on the icon and the area around it</summary>
<video src="https://github.com/user-attachments/assets/12499bb9-7e13-462c-ad16-fd707c51a58c"></video>
</details>

<details>
<summary>Changing the brightness will make the light area bigger and smaller respectively</summary>
<video src="https://github.com/user-attachments/assets/b159bc04-9420-400a-a609-4c6e73a189f5"></video>
</details>

## Code
### Let's deconstruct how everything comes together

#### Night mode overlay
```yaml
type: icon
title: Night Background
style:
  transform: none
  left: 0%
  top: 0%
  width: 100%
  height: 100%
  background-color: "#37405d"
  mix-blend-mode: hard-light
icon: none
```
#### Light areas

Every light area is made up of 2 'radial-gradients' a white one and a colored one.
These have to be in seperate elements, otherwise the blend-mode won't work.

You can adjust the position of the gradient inside the area using `at 100% 40%` 
```
at 0% 0% = top left
at 0% 100% = bottom right
at 100% 0% = top right
at 100% 100% = bottom right
```

I have them setup inside a conditional element  to turn them on and off with the light respectively.
```yaml
type: conditional
title: RGB-Light
elements:
  - type: icon
    style:
      transform: none
      color: transparent
      opacity: 40%
      left: 48%
      top: 18%
      height: 25%
      width: 25%
      background-image: |
        radial-gradient(at 100% 40% ,rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
      mix-blend-mode: overlay
    icon: mdi:account
    title: White
    hold_action:
      action: none
    tap_action:
      action: none
  - type: icon
    style:
      transform: none
      color: transparent
      opacity: 90%
      left: 48%
      top: 18%
      height: 25%
      width: 25%
      background-image: |
        radial-gradient(at 100% 40%, var(--lightcolor2) var(--brightness), rgba(0, 0, 0, 0) 70%)
      mix-blend-mode: overlay
    icon: mdi:account
    title: Color
    tap_action:
      action: none
    hold_action:
      action: none
    entity: light.YOUR_LIGHT_ENTITY_RGB
    card_mod:
      style: |
        :host {
        --lightcolor2: rgb({{ (state_attr(config.entity,'rgb_color') or [120,120,130]) | join(', ') }});
        --brightness: {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255 * 50) | round(0) }}%;
        }
conditions:
  - condition: state
    entity: light.hue_color_lamp_1
    state: "on"
```

#### Global Styling for the State-Icon Element
(add this to the end of the Picture-Elements Card itself)
```yaml
card_mod:
  style:
    hui-state-icon-element$:
      state-badge$: |
        ha-state-icon
          {
          color: white;
          filter: none !important;
          scale: 111%;
          }
    .: |
      hui-state-icon-element {
      box-shadow: 0 4px 20px rgba(var(--lightcolor1),0.7);
      border: rgba(var(--lightcolor1),1) solid;
      background: linear-gradient(135deg, transparent,rgba(0,0,0,0.3)),
      rgba(var(--lightcolor1),1);
      border-radius: 12px;
      scale: 80%; }
```
#### CSS Variable with template to read your lights color.
Make sure you add a light entity to the State-Icon element.
(Add this at the end of every State-Icon element)
```yaml
card_mod:
  style: |
     :host { --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or [120,120,130]) | join(', ') }};}
```
</details>

---

## Complete Cards

<details>
<summary>Minimal Setup for you to test</summary>
When copying this card, don't forget to change `light.YOUR_LIGHT_ENTITY` to function properly.
<img src="IMG/Minimal.png"></img>

```yaml
type: picture-elements
image: https://demo.home-assistant.io/stub_config/floorplan.png
elements:
  - type: conditional
    conditions:
      - condition: state
        entity: sun.sun
        state: below_horizon
    title: Night
    elements:
      - type: icon
        style:
          transform: none
          left: 0%
          top: 0%
          width: 100%
          height: 100%
          background-color: "#37405d"
          mix-blend-mode: hard-light
        icon: none
        title: Night Background
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 3%
              top: 37.8%
              height: 20%
              width: 20%
              background-image: |
                radial-gradient(at 0% 100% ,rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 30%, rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 80%
              left: 3%
              top: 37.8%
              height: 20%
              width: 20%
              background-image: |
                radial-gradient(at 0% 100%, rgb(242, 228, 194) 0%, rgb(228, 178, 76) var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.YOUR_LIGHT_ENTITY_NO_RGB
            card_mod:
              style: |
                :host { --brightness: {{ ((state_attr(config.entity,'brightness') or 0) | float / 255 * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.YOUR_LIGHT_ENTITY_NO_RGB
            state: "on"
        title: No-RGB-Light
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 40%
              left: 48%
              top: 18%
              height: 25%
              width: 25%
              background-image: |
                radial-gradient(at 100% 40% ,rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 90%
              left: 48%
              top: 18%
              height: 25%
              width: 25%
              background-image: |
                radial-gradient(at 100% 40%, var(--lightcolor2) var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.YOUR_LIGHT_ENTITY_RGB
            card_mod:
              style: |
                :host {
                --lightcolor2: rgb({{ (state_attr(config.entity,'rgb_color') or [120,120,130]) | join(', ') }});
                --brightness: {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255 * 50) | round(0) }}%;
                }
        conditions:
          - condition: state
            entity: light.YOUR_LIGHT_ENTITY_RGB
            state: "on"
        title: RGB-Light
  - type: state-icon
    style:
      top: 67%
      left: 80%
      scale: 1.3
    entity: light.YOUR_LIGHT_ENTITY_GROUP
    tap_action:
      action: toggle
    state_color: false
    icon: mdi:home
    card_mod:
      style: |
        :host { --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 51%
      left: 8%
    entity: light.YOUR_LIGHT_ENTITY_NO_RGB
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: |
        :host {
        --lightcolor1: {{ '255,210,10' if states(config.entity) == 'on' else '120,120,130' }};}
  - type: state-icon
    style:
      top: 28%
      left: 65%
    entity: light.YOUR_LIGHT_ENTITY_RGB
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: |
        :host {
        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or [120,120,130]) | join(', ') }};}
card_mod:
  style:
    hui-state-icon-element$:
      state-badge$: |
        ha-state-icon
          {
          color: white;
          filter: none !important;
          scale: 111%;
          }
    .: |
      hui-state-icon-element {
      box-shadow: 0 4px 20px rgba(var(--lightcolor1),0.7);
      border: rgba(var(--lightcolor1),1) solid;
      background: linear-gradient(135deg, transparent,rgba(0,0,0,0.3)),
      rgba(var(--lightcolor1),1);
      border-radius: 12px;
      scale: 80%; }
```
</details>


<details>
<summary>My personal config</summary>
<img src="IMG/Advanced.png"></img>

```yaml
type: picture-elements
elements:
  - type: conditional
    conditions:
      - condition: state
        entity: sun.sun
        state: below_horizon
    title: Night
    elements:
      - type: icon
        style:
          transform: none
          left: 0%
          top: 0%
          width: 100%
          height: 100%
          background-color: "#37405d"
          mix-blend-mode: hard-light
        icon: none
      - type: conditional
        conditions:
          - condition: or
            conditions:
              - condition: state
                entity: light.wohnzimmer_ambiente
                state: "on"
              - condition: state
                entity: light.wohnzimmer_rechts
                state: "on"
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 50%
              left: 3%
              top: 2.5%
              height: 36%
              width: 22%
              background-image: >-
                radial-gradient(at 0% 40% , rgba(255, 255, 255, 1) 50%, rgba(0,
                0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            tap_action:
              action: none
            hold_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 80%
              left: 3%
              top: 10%
              height: 33%
              width: 25%
              background-image: >-
                radial-gradient(at 20% 55%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            hold_action:
              action: none
            tap_action:
              action: none
            entity: light.wohnzimmer_ambiente
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 80%
              left: 3%
              top: 2.5%
              height: 30%
              width: 25%
              background-image: >-
                radial-gradient(at 0% 0%, var(--lightcolor2) var(--brightness),
                rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            hold_action:
              action: none
            tap_action:
              action: none
            entity: light.wohnzimmer_rechts
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        title: Wohnzimmer - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 3%
              top: 39%
              height: 20%
              width: 30%
              background-image: >-
                radial-gradient(at 0% 100% ,rgba(255, 255, 255, 1) 0%, rgba(255,
                255, 255, 1) 30%, rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 80%
              left: 3%
              top: 39%
              height: 20%
              width: 25%
              background-image: >-
                radial-gradient(at 0% 100%, rgb(242, 228, 194) 0%, rgb(228, 178,
                76) var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.wohnzimmer_schreibtisch
            card_mod:
              style: >
                :host { --brightness: {{ ((state_attr(config.entity,
                'brightness') or 0) | float / 255 * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.wohnzimmer_schreibtisch
            state: "on"
        title: Schreibtisch - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 90%
              left: 26.8%
              top: 2.5%
              height: 22%
              width: 9%
              background-image: >-
                radial-gradient(at 20% 40% , rgba(255, 255, 255, 0.7) 20%,
                rgba(0, 0, 0, 0) 90%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 40%
              left: 26.8%
              top: 2.5%
              height: 22%
              width: 11%
              background-image: >-
                radial-gradient(at 20% 40%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.hue_gradient_lightstrip_1
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.hue_gradient_lightstrip_1
            state: "on"
        title: Stehlampe - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              left: 39%
              top: 2.5%
              height: 35%
              width: 21%
              background-image: >-
                radial-gradient(at 20% 0% ,rgba(255, 255, 255, 1) 0%, rgba(255,
                255, 255, 1) 0%, rgba(0, 0, 0, 0) 25%), radial-gradient(at 50%
                0% ,rgba(255, 255, 255, 1) 0%, rgba(255, 255, 255, 1) 0%,
                rgba(0, 0, 0, 0) 40%), radial-gradient(at 80% 0% ,rgba(255, 255,
                255, 1) 0%, rgba(255, 255, 255, 1) 0%, rgba(0, 0, 0, 0) 25%)
              mix-blend-mode: overlay
            icon: none
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
        conditions:
          - condition: state
            entity: switch.kuche_schrankbeleuchtung_schalter
            state: "on"
        title: Küche - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 38.6%
              top: 40%
              height: 40%
              width: 25%
              background-image: >-
                radial-gradient(at 100% 50% ,rgba(255, 255, 255, 1) 0%,
                rgba(255, 255, 255, 1) 40%, rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: none
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            entity: light.festavia_string_lights_1
            style:
              transform: none
              color: transparent
              opacity: 80%
              left: 38.6%
              top: 39.2%
              height: 20%
              width: 25%
              background-image: >-
                radial-gradient(at 100% 100%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: none
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: or
            conditions:
              - condition: state
                entity: light.festavia_string_lights_1
                state: "on"
        title: Pflanzen - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              left: 71.5%
              top: 2.5%
              height: 16%
              width: 13%
              background-image: >-
                radial-gradient(at 100% 10% , white 0%, white 50%, rgba(0, 0, 0,
                0) 80%)
              mix-blend-mode: overlay
            icon: none
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
            entity: light.wolke
        conditions:
          - condition: state
            entity: light.wolke
            state: "on"
        title: Bad - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 40%
              left: 82%
              top: 44%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 100% 40% ,rgba(255, 255, 255, 1) 0%,
                rgba(255, 255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 90%
              left: 82%
              top: 44%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 100% 40%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.hue_color_lamp_1
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.hue_color_lamp_1
            state: "on"
        title: Tobi - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 40%
              left: 82%
              top: 61%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 100% 80% ,rgba(255, 255, 255, 1) 0%,
                rgba(255, 255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 90%
              left: 82%
              top: 61%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 100% 80%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.hue_color_lamp_1_2
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.hue_color_lamp_1_2
            state: "on"
        title: Lucy - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 40%
              left: 66.5%
              top: 44%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 0% 50% ,rgba(255, 255, 255, 1) 0%, rgba(255,
                255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 90%
              left: 66.5%
              top: 44%
              height: 25%
              width: 15%
              background-image: >-
                radial-gradient(at 0% 45%, var(--lightcolor2) var(--brightness),
                rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.hue_color_lamp_1_3
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.hue_color_lamp_1_3
            state: "on"
        title: Schrank - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 90%
              top: 16.5%
              height: 25%
              width: 7%
              background-image: >-
                radial-gradient(at 100% 70% ,rgba(255, 255, 255, 1) 0%,
                rgba(255, 255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 50%
              left: 90%
              top: 22%
              height: 20%
              width: 7%
              background-image: >-
                radial-gradient(at 90% 100%, var(--lightcolor2)
                var(--brightness), rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
            entity: light.flur_ecke
            card_mod:
              style: >
                :host { --lightcolor2: rgb({{ (state_attr(config.entity,
                'rgb_color') or [120,120,130]) | join(', ') }}); --brightness:
                {{ ((state_attr(config.entity, 'brightness') or 0) | float / 255
                * 50) | round(0) }}%;}
        conditions:
          - condition: state
            entity: light.flur_licht_gruppe
            state: "on"
        title: Eingang - Night
      - type: conditional
        elements:
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 20%
              top: 31%
              height: 7%
              width: 8%
              background-image: >-
                radial-gradient(at 50% 0% ,rgba(255, 255, 255, 1) 0%, rgba(255,
                255, 255, 1) 20%, rgba(0, 0, 0, 0) 65%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: White
            hold_action:
              action: none
            tap_action:
              action: none
          - type: icon
            style:
              transform: none
              color: transparent
              opacity: 70%
              left: 20%
              top: 31%
              height: 7%
              width: 8%
              background-image: >-
                radial-gradient(at 50% 0%, rgb(242, 228, 194) 0%, rgb(228, 178,
                76) 30%, rgba(0, 0, 0, 0) 70%)
              mix-blend-mode: overlay
            icon: mdi:account
            title: Warm
            tap_action:
              action: none
            hold_action:
              action: none
        conditions:
          - condition: state
            entity: light.flur_sofa_nachtlicht
            state: "on"
        title: Nachtlicht - Night
  - type: state-icon
    style:
      top: 88%
      left: 14%
      scale: 1.3
    entity: light.all
    tap_action:
      action: toggle
    state_color: false
    icon: mdi:home
    card_mod:
      style: >
        :host { --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 51%
      left: 8%
    entity: light.wohnzimmer_schreibtisch
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ '255,210,10' if states(config.entity) == 'on' else
        '120,120,130' }};}
  - type: state-icon
    style:
      top: 30%
      left: 8%
    entity: light.wohnzimmer_ambiente
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 8.5%
      left: 8%
    entity: light.wohnzimmer_rechts
    tap_action:
      action: toggle
    state_color: false
    icon: mdi:lava-lamp
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 8.5%
      left: 26%
    entity: light.hue_gradient_lightstrip_1
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host { --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 8.5%
      left: 49%
    entity: switch.kuche_schrankbeleuchtung_schalter
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ '255,210,10' if states(config.entity) == 'on' else
        '120,120,130' }};}
  - type: state-icon
    style:
      top: 8.5%
      left: 77%
    entity: light.wolke
    tap_action:
      action: toggle
    state_color: false
    icon: mdi:lighthouse
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 51%
      left: 58%
    entity: light.kuche_pflanzenregal
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 51%
      left: 71%
    entity: light.hue_color_lamp_1_3
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 51%
      left: 90%
    entity: light.hue_color_lamp_1
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 77%
      left: 90%
    entity: light.hue_color_lamp_1_2
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 91.5%
      left: 75%
    entity: cover.schlafzimmer_rolladen
    icon: ""
    hold_action:
      action: navigate
      navigation_path: /lovelace/rollo-sz
    state_color: false
    card_mod:
      style: |
        :host {
        --lightcolor1: 191, 90, 242;}
  - type: state-icon
    style:
      top: 64%
      left: 37.5%
    entity: cover.wohnzimmer_rolladen
    icon: ""
    hold_action:
      action: none
    state_color: false
    tap_action:
      action: navigate
      navigation_path: "#wz-rollo"
    card_mod:
      style: |
        :host {
        --lightcolor1: 191, 90, 242;}
  - type: state-icon
    style:
      top: 51%
      left: 46%
    entity: light.festavia_string_lights_1
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 30%
      left: 26%
    entity: light.wohnzimmer_licht_gruppe
    tap_action:
      action: navigate
      navigation_path: "#wz"
    hold_action:
      action: toggle
    state_color: false
    icon: mdi:sofa
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }}; background: var(--wz-color) !important;}
  - type: state-icon
    style:
      top: 77%
      left: 71%
    entity: light.schlafzimmer_licht_gruppe
    tap_action:
      action: navigate
      navigation_path: "#sz"
    hold_action:
      action: toggle
    state_color: false
    icon: mdi:bed-king
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }}; background: var(--sz-color) !important;}
  - type: conditional
    title: Nachtlicht
    conditions:
      - condition: state
        entity: light.flur_sofa_nachtlicht
        state: "on"
    elements:
      - type: state-icon
        style:
          top: 51%
          left: 26%
        entity: light.flur_sofa_nachtlicht
        tap_action:
          action: toggle
        hold_action:
          action: toggle
        state_color: false
        icon: ""
        card_mod:
          style: >
            :host {

            --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
            [120,120,130]) | join(', ') }};}
  - type: state-icon
    style:
      top: 34%
      left: 90%
    entity: light.flur_ecke
    tap_action:
      action: toggle
    state_color: false
    icon: ""
    card_mod:
      style: >
        :host {

        --lightcolor1: {{ (state_attr(config.entity, 'rgb_color') or
        [120,120,130]) | join(', ') }};}
image:
  media_content_id: /local/pokemon.png
card_mod:
  style:
    hui-state-icon-element$:
      state-badge$: |
        ha-state-icon
          {
          color: white;
          filter: none !important;
          scale: 111%;
          }
    .: >
      hui-state-icon-element {

      box-shadow: 0 4px 20px rgba(var(--lightcolor1),0.7);

      border: rgba(var(--lightcolor1),1) solid;

      background: linear-gradient(135deg, transparent,rgba(0,0,0,0.3)),
      rgba(var(--lightcolor1),1);

      border-radius: 12px;

      scale: 80%; }
```
</details>
