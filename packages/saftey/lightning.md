### Lightning Detection Automation ###
This is part of the Sentry Guard functionality

Components Used:
- [Blitzortung.org lightning detector](https://github.com/mrk-its/homeassistant-blitzortung) by [Mariusz Kryński](https://github.com/mrk-its)
- [Google Translate](https://www.home-assistant.io/integrations/google_translate/) or [Nabu Casu TTS](https://support.nabucasa.com/hc/en-us/articles/25619386304541-Text-to-speech-TTS)
- [LG WebOS](https://www.home-assistant.io/integrations/webostv/)
- [Companion App](https://companion.home-assistant.io/)
- [Input Helpers](https://www.home-assistant.io/integrations/?search=helper)



--------------------------------------------------------------------------------------
### Input Helpers 
This section creates various [input helpers](https://www.home-assistant.io/integrations/?search=helper) (including boolean's, selects and number's) 
```yaml
############################
# INPUTS
############################
input_boolean:
  sentry_indy_lightning_snooze:
    name: Indy Lightning Snooze

  sentry_shelby_lightning_snooze:
    name: Shelbyville Lightning Snooze

  sentry_auto_protect_enabled:
    name: Auto Protect

  sentry_voice_alerts:
    name: Voice Alerts

input_select:
  sentry_user_role:
    name: Sentry User Role
    options:
      - normal
      - elevated
      - admin
    initial: normal

############################
# THRESHOLDS
############################
input_number:
  sentry_danger_close:
    name: Danger Close Distance
    min: 1
    max: 50
    step: 1
    initial: 10

  sentry_warning_distance:
    name: Warning Distance
    min: 10
    max: 100
    step: 5
    initial: 25
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
*Trigger automation when lightning has been detected*
```yaml
############################
# TEMPLATE SENSORS
############################
template:

  #################################################
  # 📉 TREND
  #################################################
  - trigger:
      - platform: time_pattern
        minutes: "/1"
    sensor:
      - name: sentry_indy_lightning_trend
        unique_id: sentry_indy_lightning_trend
        state: >
          {% set current = states('sensor.blitzortung_lightning_distance') | float(999) %}
          {% set prev = state_attr(this.entity_id, 'last') | float(current) %}
          {% if current < prev %} approaching
          {% elif current > prev %} departing
          {% else %} steady
          {% endif %}
        attributes:
          last: "{{ states('sensor.blitzortung_lightning_distance') | float(999) }}"

      - name: sentry_shelby_lightning_trend
        unique_id: sentry_shelby_lightning_trend
        state: >
          {% set current = states('sensor.lightning_distance_shelby') | float(999) %}
          {% set prev = state_attr(this.entity_id, 'last') | float(current) %}
          {% if current < prev %} approaching
          {% elif current > prev %} departing
          {% else %} steady
          {% endif %}
        attributes:
          last: "{{ states('sensor.lightning_distance_shelby') | float(999) }}"

  #################################################
  # ⏱️ ETA
  #################################################
  - sensor:
      - name: sentry_indy_lightning_eta
        unique_id: sentry_indy_lightning_eta
        unit_of_measurement: "min"
        state: >
          {% set d = states('sensor.blitzortung_lightning_distance') | float(999) %}
          {% if d < 50 %}
            {{ (d / 0.5) | int }}
          {% else %}
            0
          {% endif %}

      - name: sentry_shelby_lightning_eta
        unique_id: sentry_shelby_lightning_eta
        unit_of_measurement: "min"
        state: >
          {% set d = states('sensor.lightning_distance_shelby') | float(999) %}
          {% if d < 50 %}
            {{ (d / 0.5) | int }}
          {% else %}
            0
          {% endif %}
```
*After the Companion App-Actionable Notification the Snooze Lightning Alerts. Then the alerts will be disabled until after midnight when the Snooze (input_boolean) will reset*
```yaml
    - platform: event
      event_type: mobile_app_notification_action
      event_data:
        action: SNOOZE_LIGHTNING
      id: Snooze Lightning Alerts (Actionable Notification)
```
**
```yaml
    - platform: numeric_state
      entity_id: sensor.lightning_distance_miles
      below: 26
      id: Below 26
```
