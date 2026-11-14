<p align="center">
  <img src="https://zendure.com/cdn/shop/files/zendure-logo-infinity-charge_240x.png?v=1717728038" alt="Logo">
</p>

# Zendure Home Assistant Integration
This Home Assistant integration connects your Zendure devices to Home Assistant, making all reported parameters available as entities. You can track battery levels, power input/output, manage charging settings, and integrate your Zendure devices into your home automation routines. The integration also provides a power manager feature that can help balance energy usage across multiple devices when a P1 meter entity is supplied.


[![hacs][hacsbadge]][hacs] [![releasebadge]][release] [![License][license-shield]](LICENSE.md) [![hainstall][hainstallbadge]][hainstall]

## Overview

- **[Installation and ZendureApp Token](https://github.com/Zendure/Zendure-HA/wiki/Installation)**
  - [Troubleshooting Hyper2000](https://github.com/Zendure/Zendure-HA/wiki/Troubleshooting)
  - Tutorials
    - [Domotica & IoT](https://iotdomotica.nl/tutorial/install-zendure-home-assistant-integration-tutorial) 🇬🇧
    - [twoenter blog](https://www.twoenter.nl/blog/en/smarthome-en/zendure-home-battery-home-assistant-integration/) 🇬🇧 or [twoenter blog](https://www.twoenter.nl/blog/home-assistant-nl/zendure-thuisaccu-integratie-met-home-assistant/) 🇳🇱
    - [@Kieft-C](https://github.com/Kieft-C/Zendure-BKW-PV/wiki/Installation-Zendure-Home-Assistant-integration-%E2%80%93-Tutorial) 🇩🇪

- **Configuration:**
  - [Fuse Group](https://github.com/Zendure/Zendure-HA/wiki/Fuse-Group)
  - Zendure Manager
    - [Power distribution strategy](https://github.com/Zendure/Zendure-HA/wiki/Power-distribution-strategy)
  - [Local Mqtt (Legacy devices)](https://github.com/Zendure/Zendure-HA/wiki/Local-Mqtt-(Legacy-Devices))
  - Home Assistent Energy Dashboard

- **Supported devices:**
  - Ace1500
  - Aio2400
  - Hyper2000
  - Hub1200 [German](https://github.com/Zendure/Zendure-HA/wiki/SolarFlow-Hub1200-German)
  - Hub2000
  - [SF800](https://github.com/Zendure/Zendure-HA/wiki/SolarFlow-800)
  - SF800 Pro
  - SF800 Plus
  - SF1600 AC+ *added in this 1.2.4 version additonal
  - SF2400 AC
  - SuperBase V6400

- **Device Automation:**
  - Cheap hours.

## Local Modifications (Fork)

This is a local fork of the Zendure Home Assistant integration (based on version 1.2.4) with the following changes. These changes are **not** part of the official release.

### Added: SolarFlow 1600 AC+ support

The SolarFlow 1600 AC+ was not recognized by version 1.2.4. The following changes enable detection:

- **`devices/solarflow1600.py`** (new file): copied from the upstream Zendure-HA repository. It defines the `SolarFlow1600` device class.
- **`api.py`**: import of `SolarFlow1600` and the mapping `"solarflow 1600 ac+": SolarFlow1600` in `createdevice`.
- **`manager.py`**: device model lookup now uses `prodModel.strip().lower()`. The cloud reports the model as `SolarFlow 1600 AC+ ` with a trailing space, which caused the device to be skipped as "not supported".

### Fixed: Startup and runtime errors

- **`manager.py`, `update_p1meter`**: returns early when `p1meter is None`. Previously, unloading the integration raised `TypeError: expected string or bytes-like object, got 'NoneType'`.
- **`entity.py`, entity IDs**: entity IDs are now built with `slugify(self.device.name)` and `_` as separator. This removes the warning `sets an invalid entity ID`. Existing entities keep their registered IDs, because Home Assistant only uses the suggested ID for new entities.
- **`entity.py`, `updateVersion`**: calls `device_registry.async_update_device` directly instead of `run_callback_threadsafe`. This removes `RuntimeError: Cannot be called from within the event loop` and the version sensor now shows e.g. `v1.1.2` instead of the raw value `4354`.

### Known remaining warnings

These are deprecation warnings from Home Assistant. They do not affect operation, but will stop working in future Home Assistant versions:

- `device_registry.async_get_device` is used in `entity.py` (deprecated, removal planned for 2027.8.0).
- `via_device` is passed as a parameter in `entity.py` (deprecated, removal planned for 2027.8.0).

### Known limitations

- The SolarFlow 1600 AC+ cloud data does not include an IP address (`ip: ''`). Local control via zenSDK requires a fixed IP address that must be set manually.
- The power limits for the SolarFlow 1600 AC+ are hardcoded in `devices/solarflow1600.py` (`setLimits(-1600, 1600)`). These values influence the power distribution of the manager.
- The integration updates are not automatic. Changes need to be re-applied when updating the upstream integration.

## Installation (Fork)

1. Back up the existing `custom_components/zendure_ha` folder.
2. Copy the changed files into `custom_components/zendure_ha`, or replace the folder with the patched version.
3. Restart Home Assistant completely.
4. Check the log for `Adding device` and make sure no `is not supported!` line appears for the SolarFlow 1600 AC+.

## Minimum Requirements
- [Home Assistant](https://github.com/home-assistant/core) 2025.5+

## Installation (Official)

### HACS (Home Assistant Community Store)

To install via HACS:

1. Navigate to HACS -> Integrations -> "+ Explore & Download Repos".
2. Search for "Zendure".
3. Click on the result and select "Download this Repository with HACS".
4. Refresh your browser (due to a known HA bug that may not update the integration list immediately).
5. Go to "Settings" in the Home Assistant sidebar, then select "Devices and Services".
6. Click the blue [+ Add Integration] button at the bottom right, search for "Zendure", and install it.

   [![Set up a new integration in Home Assistant](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start/?domain=zendure_ha)


## Contributing

Contributions are welcome! If you're interested in contributing, please review our [Contribution Guidelines](CONTRIBUTING.md) before submitting a pull request or issue.

## Support

If you find this project helpful and want to support its development, consider buying me a coffee!
[![Buy Me a Coffee][buymecoffeebadge]][buymecoffee]

---

[buymecoffee]: https://www.buymeacoffee.com/fireson
[buymecoffeebadge]: https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png
[license-shield]: https://img.shields.io/github/license/zendure/zendure-ha.svg?style=for-the-badge
[hacs]: https://github.com/zendure/zendure-ha
[hacsbadge]: https://img.shields.io/badge/HACS-Default-orange.svg?style=for-the-badge
[release]: https://github.com/zendure/zendure-ha/releases
[releasebadge]: https://img.shields.io/github/v/release/zendure/zendure-ha?style=for-the-badge
[buildstatus-shield]: https://img.shields.io/github/actions/workflow/status/zendure/zendure-ha/push.yml?branch=main&style=for-the-badge
[buildstatus-link]: https://github.com/zendure/zendure-ha/actions

[hainstall]: https://my.home-assistant.io/redirect/config_flow_start/?domain=zendure_ha
[hainstallbadge]: https://img.shields.io/badge/dynamic/json?style=for-the-badge&logo=home-assistant&logoColor=ccc&label=usage&suffix=%20installs&cacheSeconds=15600&url=https://analytics.home-assistant.io/custom_integrations.json&query=$.zendure_ha.total


## License

MIT License
