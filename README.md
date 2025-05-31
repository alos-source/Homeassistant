# Home Assistant Freifunk Router Monitoring Package

This Home Assistant package allows you to monitor the status and key metrics of your Freifunk router (specifically for the Freifunk Franken community) by fetching data from their public API.
## Features
- Online Status: See if your Freifunk router is currently online or offline.
- Connected Clients: Track the number of devices connected to your Freifunk router.
- Load Average: Monitor the router's CPU load.
- Firmware Version: Display the current firmware running on your router.
- Last Contact: See the timestamp of the last successful API contact with your router.
- Easy Customization: Easily adjust the monitored router, sensor names, and icons.

Preview to my card:

![](/media/firefox_2025-05-31_14-40-08.png)

## Prerequisites
- A running Home Assistant instance.
- Your Freifunk nickname used to register your router with the Freifunk Franken community. This is crucial for the API to identify your specific router.

## Installation

Follow these steps to integrate the Freifunk Router Monitoring into your Home Assistant:

1. Create the packages Directory:
    If you don't already have one, create a folder named packages in your Home Assistant config directory.
    ````
        config/
        └── packages/
    ````
1. Create the Integration Folder:
    Inside the packages directory, create a new folder named freifunk_router.

    ````
        config/
        └── packages/
            └── freifunk_router/
    ````

1. Download the Package File:
    Download the freifunk_router.yaml file from this repository.

1. Place the File:
    Place the downloaded freifunk_router.yaml file inside the newly created config/packages/freifunk_router/ folder.

    ````
        config/
        └── packages/
            └── freifunk_router/
                └── freifunk_router.yaml  <-- Your downloaded file goes here
    ````

1. Include Packages in configuration.yaml:
    Open your main Home Assistant configuration.yaml file. Add the following lines to enable package inclusion. If you already have a homeassistant: section, just add the packages: line under it.

    ```` YAML
        # configuration.yaml
        homeassistant:
        packages: !include_dir_named packages/
    ````

## Configuration & Customization

This package is designed to be easily customizable.
1. Set Your Freifunk Nickname (Mandatory)

This is the most important step. You must replace the placeholder in the freifunk_router.yaml file with your actual Freifunk nickname.
- Open the freifunk_router.yaml file.
- Find the resource: line under the platform: rest sensor.
- Replace DEIN_FREIFUNK_BENUTZERNAME with your Freifunk nickname:

    ```` YAML

        # freifunk_router.yaml
        sensor:
        - platform: rest
            name: "Freifunk My Router Data"
            # REPLACE 'DEIN_FREIFUNK_BENUTZERNAME' with your actual Freifunk nickname!
            resource: "https://monitoring.freifunk-franken.de/api/routers_by_nickname/YOUR_FREIFUNK_NICKNAME" # <-- Change this line
            # ...
    ````

    Verify: You can test if your nickname works by opening the URL https://monitoring.freifunk-franken.de/api/routers_by_nickname/YOUR_FREIFUNK_NICKNAME in your web browser. You should see JSON data for your router. If it's empty or shows an error, your nickname might be incorrect or not registered.

2. Adjust Sensor Names

    You can change the friendly_name of any sensor to better suit your preferences in the Home Assistant UI.

    ```` YAML
    # freifunk_router.yaml
        mein_freifunk_router_clients:
            friendly_name: "My Awesome Router Clients" # Change this name
            # ...
    ````

3. Change Sensor Icons

    Icons are defined using Material Design Icons. You can find a vast library of icons at https://pictogrammers.com/library/mdi/. Simply replace the icon string under icon_template:.

    ```` YAML
    # freifunk_router.yaml
            icon_template: mdi:wifi-marker # Change this icon
    ````

4. Modify Data Refresh Interval

    By default, Home Assistant determines the refresh interval for RESTful sensors. If you want to specify it (e.g., to fetch data every 5 minutes instead of more frequently), add scan_interval: to the platform: rest sensor:

    ````YAML
    # freifunk_router.yaml
    - platform: rest
        name: "Freifunk My Router Data"
        resource: "https://monitoring.freifunk-franken.de/api/routers_by_nickname/YOUR_FREIFUNK_NICKNAME"
        # ...
        scan_interval: 300 # Refresh data every 300 seconds (5 minutes)
    ````
5. Add More Sensors

    The Freifunk API provides more data points (e.g., hardware, hood, position, mac, name, id). You can create additional template sensors by copying an existing sensor block and adapting the value_template: to extract the desired information.

    Example: Adding a sensor for Router Hardware:

    ```` YAML
    # freifunk_router.yaml (add this below other template sensors)
        mein_freifunk_router_hardware:
            friendly_name: "My Freifunk Router Hardware"
            value_template: >
            {% set my_router = state_attr('sensor.freifunk_mein_router_daten', 'nodes')[0] | default({}) %}
            {{ my_router.hardware | default('Unknown') }}
            icon_template: mdi:developer-board
    ````

## Restart Home Assistant

After making any changes to configuration.yaml or freifunk_router.yaml, you need to restart or reload your Home Assistant configuration:

1. Go to Settings > Developer Tools > YAML configurations.
1. Click on "Reload RESTful sensors" and "Reload Template sensors".
1. If issues persist, perform a full Home Assistant restart.

## Troubleshooting

- Sensors show "unknown":
    - Check your Freifunk Nickname: Ensure the resource URL in freifunk_router.yaml uses your exact, correct nickname. Test the URL in your browser.
    - Network Access: Verify your Home Assistant instance has internet access and can reach https://monitoring.freifunk-franken.de.
    - YAML Syntax: Double-check for any typos or incorrect indentations in your YAML files. Use the YAML validator in Home Assistant Developer Tools.
    - Logs: Check the Home Assistant logs (Settings > Logs) for any error messages related to rest or template sensors.

- Icons are not displayed:
    - Ensure you are using icon_template: instead of icon: for template sensors.

Feel free to open an issue on this GitHub repository if you encounter any problems or have suggestions for improvements!