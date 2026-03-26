### Lightning Detection Automation ###
This is part of the Sentry Guard functionality

Components Used:
- [Blitzortung.org lightning detector](https://github.com/mrk-its/homeassistant-blitzortung) by [Mariusz Kryński](https://github.com/mrk-its)
- [Google Translate](https://www.home-assistant.io/integrations/google_translate/) or [Nabu Casu TTS](https://support.nabucasa.com/hc/en-us/articles/25619386304541-Text-to-speech-TTS)
- [LG WebOS](https://www.home-assistant.io/integrations/webostv/)
- [Companion App](https://companion.home-assistant.io/)




This section creats a template sensor to convert the distance that Blitzortung provides in to miles. https://github.com/TheDigiCasa/Home-Automation/blob/732a06d3c1202f3ac39c62af40a25be52d4222a8/packages/saftey/lightning.yaml#L12-L22
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
