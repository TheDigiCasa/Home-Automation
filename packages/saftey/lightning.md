### Lightning Detection Automation ###
This is part of the Sentry Guard functionality

Components Used:
- [Blitzortung.org lightning detector](https://github.com/mrk-its/homeassistant-blitzortung) by [Mariusz Kryński](https://github.com/mrk-its)
- [Google Translate](https://www.home-assistant.io/integrations/google_translate/) or [Nabu Casu TTS](https://support.nabucasa.com/hc/en-us/articles/25619386304541-Text-to-speech-TTS)
- [LG WebOS](https://www.home-assistant.io/integrations/webostv/)
- [Companion App](https://companion.home-assistant.io/)




This section creats a template sensor to convert the distance that Blitzortung provides in to miles. 
```yaml
sensor:
  - platform: template
    sensors:
      lightning_distance_miles:
        friendly_name: "Lightning Distance"
        unit_of_measurement: 'miles'
        value_template: >
          {% if is_state("sensor.blitzortung_lightning_distance","unknown") -%}
            Unknown
          {% else -%}
            {{ (states("sensor.blitzortung_lightning_distance") | float * 0.621371 )| int}}
          {% endif %}
```

*Group together the entities that the Blitzortung HACS intergration provides*
```yaml
group:
  Blitzortung Alerts:
    - sensor.blitzortung_lightning_azimuth
    - sensor.blitzortung_lightning_counter
    - sensor.blitzortung_lightning_distance
    - sensor.lightning_distance_miles
    - input_boolean.snooze_lightning
```
