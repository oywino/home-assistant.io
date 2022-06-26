---
title: Tibber
description: Instructions on how to integrate Tibber within Home Assistant.
ha_category:
  - Energy
  - Notifications
  - Sensor
ha_release: 0.8
ha_iot_class: Cloud Polling
ha_quality_scale: silver
ha_codeowners:
  - '@danielhiversen'
ha_domain: tibber
ha_config_flow: true
ha_platforms:
  - diagnostics
  - notify
  - sensor
ha_integration_type: integration
---

The `tibber` integration provides a sensor with the current electricity price if you are a [Tibber](https://tibber.com/) customer.
If you have a [Tibber Pulse](https://norge.tibber.com/products/pulse/) or [Watty](https://tibber.com/se/store/produkt/watty-smart-energimatare) it will also show the electricity consumption in real-time. You get a sensor for monthly consumption, monthly cost, and monthly peak hour. If you do have a real-time meter it is updated once every hour, oterwise it is updated once per day. Statistics with hourly consumption and cost data is generated that can be used in the [Energy Dashboard](/docs/energy/). (The ids are `tibber:energy_consumption_HOMEID` and `tibber:energy_totalcost_HOMEID` )

There is currently support for the following device types within Home Assistant:

- [Notifications](#notifications)
- [Sensor](#sensor)

## Setup
First, connect your Tibber Pulse device to your electricity meter as described in your Tibber App (Android or iPhone), and verify that it is connected to your Tibber account and gives correct readings in the App before you move on.
Then, go to [developer.tibber.com/settings/accesstoken](https://developer.tibber.com/settings/accesstoken) and log on using the same email address as you use for your Tibber account, in order to get your API token.
The integration will automatically be linked to the electricity meter where your Tibber Pulse device is connected when you paste the API token into the integration setting in Home Assistant.

{% include integrations/config_flow.md %}

## Notifications

Tibber can send a notification by calling the [`notify` service](/integrations/notify/). It will send a notification to all devices registered in the Tibber account.

To use notifications, please see the [getting started with automation page](/getting-started/automation/).

### Send message

```yaml
action:
  service: notify.tibber
  data:
    title: Your title
    message: This is a message for you!
```

## Sensor

The `tibber` sensor provides the current electricity price if you are a [Tibber](https://tibber.com/) customer.
You also get sensors for monthly consumption, monthly cost, and monthly peak hour
If you have a Tibber Pulse it will also show the electricity consumption in real time.


## Examples

In this section, you will find some real-life examples of how to use this sensor.

### Electricity price

The electricity price can be used to make automations. The sensor has a `max_price` and `min_price` attribute, with max and min price for the current day. Here is an example to get a notification when the price is above 90% of the maximum price for the day:

{% raw %}

```yaml
- alias: "Electricity price"
  trigger:
    platform: time_pattern
  # Matches every hour at 1 minutes past whole
    minutes: 1
  condition:
    condition: template
    value_template: '{{ float(states('sensor.electricity_price_hamretunet_10')) > 0.9 * float(state_attr('sensor.electricity_price_hamretunet_10', 'max_price')) }}'
  action:
   - service: notify.pushbullet
     data:
       title: "Electricity price"
       target: "device/daniel_telefon_cat"
       message: "The electricity price is now {{ states('sensor.electricity_price_hamretunet_10') }}"
```

{% endraw %}
