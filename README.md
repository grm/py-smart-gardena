# py-smart-gardena

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/84a54d0be7304ca59c32782b5d99cf73)](https://app.codacy.com/app/grm/py-smart-gardena?utm_source=github.com&utm_medium=referral&utm_content=grm/py-smart-gardena&utm_campaign=Badge_Grade_Dashboard)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PyPI version](https://badge.fury.io/py/py-smart-gardena.svg)](https://badge.fury.io/py/py-smart-gardena)
[![Build Status](https://travis-ci.org/grm/py-smart-gardena.svg?branch=master)](https://travis-ci.org/grm/py-smart-gardena)
[![Python 3](https://pyup.io/repos/github/grm/py-smart-gardena/python-3-shield.svg)](https://pyup.io/repos/github/grm/py-smart-gardena/)
[![Maintainability](https://api.codeclimate.com/v1/badges/e1931021997308c01056/maintainability)](https://codeclimate.com/github/grm/py-smart-gardena/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/e1931021997308c01056/test_coverage)](https://codeclimate.com/github/grm/py-smart-gardena/test_coverage)
[![codecov](https://codecov.io/gh/grm/py-smart-gardena/branch/master/graph/badge.svg)](https://codecov.io/gh/grm/py-smart-gardena)
[![Updates](https://pyup.io/repos/github/grm/py-smart-gardena/shield.svg)](https://pyup.io/repos/github/grm/py-smart-gardena/)
[![Known Vulnerabilities](https://snyk.io/test/github/grm/py-smart-gardena/badge.svg?targetFile=requirements.txt)](https://snyk.io/test/github/grm/py-smart-gardena?targetFile=requirements.txt)
[![Black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/ambv/black)
[![PEP8](https://img.shields.io/badge/code%20style-pep8-orange.svg)](https://www.python.org/dev/peps/pep-0008/)
[![Downloads](https://pepy.tech/badge/py-smart-gardena)](https://pepy.tech/project/py-smart-gardena)

## Description

This library aims to provide python way to communicate with gardena smart systems and 
all gardena smart equipments. Configuration of the equipement and inclusion has still
 to be done using the Gardena application or web site.
For now, this library only supports retrieving information, it should soon be able ot
 interact with devices to integrate in your automation system.

## Support

**This project needs your support.**  
Gardena equipments are expensive, and I need to buy them in order to add support.
If you find this library useful and want to help me support more devices (or if you 
just want to reward me for my spent time), you are very welcome !   
Your help is very much appreciated.

Here are the links if you want to show your support :  
<span class="badge-paypal"><a href="https://paypal.me/grmklein" title="Donate to this project using Paypal"><img src="https://img.shields.io/badge/paypal-donate-yellow.svg" alt="PayPal donate button" /></a></span>

## Requirements

* **Python 3.5+**

## Supported devices

For now, only few devices are supported. I may add new ones in the future :  
* Gateway
* Smart Mower (not tested yet)
* Smart water control
* Smart sensor
* Power plugs

## Installation

```sh
$ pip install py-smart-gardena
```

## Usage

### Data model

The entrypoint of the library is the the SmartSytem class (in gardena.smart_system 
package).
From there, you can get all locations from your account, and for each of these 
locations, get the declared devices.

All communcations are not done directly with the gateway. This library communicates 
with gardena web services hosted on internet and then, Gardena services communicates 
with the gateways and the devices.  That's why, when asking for a refresh, the 
information is not immediately updated but only after a few seconds.

### Authentication

You need to authenticate with your email and passwords created on this [site]
(https://sg-api.dss.husqvarnagroup.net/sg-1/index/ios/) or the IOS/Android application. 
The 
library manages the token 
for you then.
An exception is raised if authentication fails.

```python
from gardena.smart_system import SmartSystem

smart_system = SmartSystem(email="email@gmail.com", password="my_password")
smart_system.authenticate()

```
Once authentication is successful, you can update locations.

### Locations

Locations are the places where the devices are connected. It can be used to manage
 different gardens or different places from one garden, or both. You need first to 
 get the locations before querying devices.

Here is the list of the current available fields and methods :

```python
smart_system.update_locations()

for location in smart_system.locations.values():
    print("location : " + location.name + "(" + location.id + ")")
    print("-> latitude : " + str(location.latitude))
    print("-> longitude : " + str(location.longitude))
    print("-> address : " + location.address)
    print("-> city : " + location.city)
    print("-> sunrise : " + location.sunrise)
    print("-> sunset : " + location.sunset)
    print("-> time zone : " + location.time_zone)
    print("-> time zone offset : " + str(location.time_zone_offset))
    
```

The function *location.update_devices()* is used to update devices for a location.
This function needs to be called first before walking through devices.

### Devices

Before getting devices, you first need to authenticate and update locations.
Devices must be queried on a specific location.

There is two ways to update devices, either on a locations, or all devices at a time.
```python
    smart_system.update_all_devices()
    # Or
    for location in smart_system.locations.values():
        locations.update_devices()
```

All devices fields can also been queried using **get_all_info()**.
Keys of the hashmap are the field names :
```python
    for gateway in location.gateways.values():
        info = gateway.get_all_info()
        print("-> gateway : " + info["name"] + "(" + gateway["id"] + ")")

```

All devices have abilities that can be shared with other devices kind and also have 
their own abilities. When a device inherits of an ability, it gets all the fields and
 methods of the ability.

#### Common for all devices
###### Properties

* **id** : the smart system id of the device
* **name** : the name of the device
* **description** : a string describing the device
* **category** : the category of the device (gateway, mower, ..)
* **device_state** : The current state of the device
* **is_configuration_synchronized** : indicates if the configuration is synchronized

###### Methods

None

#### Abilities
#### AmbientTemperatureSensorAbility
##### Properties

* **ambient_temperature** : the ambient temperature of the device
* **frost_warning** : A field indicating that there frost may occur 

##### Methods

None

#### DeviceInfoAbility
##### Fields

* **serial_number** : Serial number of the device
* **version** : The current firmware/software version
* **last_time_online** : When the device was seen online for the last time
* **sgtin** : A unique product code 
* **manufacturer** : the manufacturer of the device

##### Methods

None

#### DisposableBatteryAbility
##### Fields

* **battery_level** : The battery level in %
* **battery_status** : The current status of the battery

##### Methods

None

#### FirmwareAbility
##### Fields

* **firmware_status** : The status of the firware (up to date or not)
* **firmware_upload_progress** : The progression of the upload of the firmware
* **firmware_update_start** : -
* **firmware_available_version** : The version available for the device

##### Methods

* **upload_firmware()** : start the upload process of the new firmware. Latest 
available version is used
* **firmware_cancel()** : Cancel the firmware upload
* **firmware_flash()** : start the flash procedure of the already downloaded new 
firmware

#### RadioAbility
##### Fields

* **radio_quality** : The quality of the radio link in %
* **radio_connection_status** : Current status of the radio link
* **radio_state** : The current state of the radio link

##### Methods

None

#### RechargeableBatteryAbility
##### Fields

* **battery_level** : The battery level in %
* **battery_status** : The current status of the battery
* **battery_charging** : Boolean indicating if the battery is currently charging

##### Methods

None

#### Devices

Each devices supports a list of abilities and their own fields and methods.

#### Gateway

The gateway is the hardware used to communicate with devices.
There are no commands on it.

##### Supported abilities :

* **RadioAbility**
* **DeviceInfoAbility**

##### Specific properties

* **ip_address** : the ip adress of the gateway in the home network
* **timezone** : the timezone used by the gateway

##### Specific methods

None

#### Mower

Mowers can be controlled through the API. 
Here is the list of available information and commands.

##### Supported abilities :

* **DeviceInfoAbility**
* **RechargeableBatteryAbility**
* **RadioAbility**

##### Specific properties

* **internal_temperature** : the ip adress of the gateway in the home network
* **mower_manual_operation** : the timezone used by the gateway
* **mower_status** : The current status of the mower
* **mower_timestamp_next_start** : A date time indicating when the mower will start 
automatically next time

##### Specific methods

* **mower.park_until_next_timer()** : This function park the mower until the 
next 
scheduled time
* **mower.park_until_further_notice()** : The mower will stay park until you resume the 
schedule (using **mower.start_resume_schedule()**)
* **mower.start_resume_schedule()** : Using this function, the mower will resume from
 the command **mower.park_until_further_notice()**
* **mower.start_override_timer()** : I don't know what it is doing yet :)

#### Sensor

The sensor is used to get information from the garden.
Here are the information and commands available.

##### Supported abilities :

* **AmbientTemperatureSensorAbility**
* **DisposableBatteryAbility**
* **RadioAbility**
* **DeviceInfoAbility**
* **FirmwareAbility**

##### Specific properties

* **sensor_soil_temperature** : the soil temperature
* **sensor_soil_humidity** : the soil humidity in % 
* **sensor_light** : the luminostiy in Lux

##### Specific methods

The commands from the sensor are asynchronous. The information will **not** be 
updated immediately. After a few seconds, you can call **location.update_devices()** 
in order to get the new information.
In a future release, there will probably have a function to refresh information for a
 specific device.
 
* **sensor.refresh_ambient_temperature()** : Asks to refresh of the temperature 
information to the gateway
* **sensor.refresh_light_intensity()** : Asks to refresh of the light intensity 
information to the gateway
* **sensor.refresh_soil_moisture()** : Asks to refresh of the soil moisture 
information to the gateway

#### Water control

The water control equipment is used to control the irrigation fo the plants in the 
garden.

##### Supported abilities :

* **AmbientTemperatureSensorAbility**
* **DisposableBatteryAbility**
* **RadioAbility**
* **DeviceInfoAbility**
* **FirmwareAbility**

##### Specific properties

* **watering_valve_open** : a boolean indicating of the valve is currently opened
* **watering_manual_override** : -

##### Specific methods

The commands from the sensor are asynchronous. The information will **not** be 
updated immediately. After a few seconds, you can call **location.update_devices()** 
in order to get the new information.
In a future release, there will probably have a function to refresh information for a
 specific device.
 
* **water_control.open_valve()** : Asks the water control to open the valve for X 
minutes (default duration is 30 minutes)
* **water_control.close_valve()** : Asks the water control to close the valve( it 
seems to only work when the valve has been manually opened)

#### Power

The power equipment is used to control a smart power plug 

##### Supported abilities :

* **DeviceInfoAbility**
* **DisposableBatteryAbility**
* **FirmwareAbility**

##### Specific properties

* **power_timer** : How long the plug will be on
* **power_error** : indicates if there is an error with the plug

##### Specific methods

* **power.power_on(60)** : Asks the power plug to switch on for 60 seconds. If no 
duration is specified, the plug will remain online all the time.
* **power.power_off()** : Asks the power plug to switch off
* **refresh_link_status** : refresh the current link status

### Sample script

Here is a sample script listing all available functionnalities for fast reference.
Commands have been commented to avoid unintentional triggers.

```python
from gardena.smart_system import SmartSystem

smart_system = SmartSystem(email="email@gmail.com", password="my_password")
smart_system.authenticate()

smart_system.update_locations()

for location in smart_system.locations.values():
    print("location : " + location.name + "(" + location.id + ")")
    print("-> latitude : " + str(location.latitude))
    print("-> longitude : " + str(location.longitude))
    print("-> address : " + location.address)
    print("-> city : " + location.city)
    print("-> sunrise : " + location.sunrise)
    print("-> sunset : " + location.sunset)
    print("-> time zone : " + location.time_zone)
    print("-> time zone offset : " + str(location.time_zone_offset))

    # To update devices information for a location (mower, gateway, sensor, ..)
    location.update_devices()
    # Iterate over gateways
    for gateway in location.gateways.values():
        print("-> gateway : " + gateway.name + "(" + gateway.id + ")")
        print("---> category : " + gateway.category)
        print("---> description : " + gateway.description)
        print("---> is_configuration_synchronized : " + str(
            gateway.is_configuration_synchronized))
        print("---> serial number : " + gateway.serial_number)
        print("---> version : " + gateway.version)
        print("---> last time online : " + gateway.last_time_online)
        print("---> ip address : " + gateway.ip_address)
        print("---> timezone : " + gateway.timezone)
        print("---> device state : " + gateway.device_state)
        print("---> sgtin : " + gateway.sgtin)
        print("---> manufacturer : " + gateway.manufacturer)

    # Iterate over mowers
    for mower in location.mowers.values():
        print("-> mower : " + mower.name + "(" + mower.id + ")")
        print("---> category : " + mower.category)
        print("---> description : " + mower.description)
        print(
            "---> is_configuration_synchronized : " +
            str(mower.is_configuration_synchronized))
        print("---> serial number : " + mower.serial_number)
        print("---> version : " + mower.version)
        print("---> last time online : " + mower.last_time_online)
        print("---> battery level : " + str(mower.battery_level))
        print("---> battery rechargeable status : " + mower.battery_status)
        print("---> battery charging : " + str(mower.battery_charging))
        print("---> radio quality : " + str(mower.radio_quality))
        print("---> radio connection status : " + mower.radio_connection_status)
        print("---> radio state : " + mower.radio_state)
        print("---> internal temperature : " + str(mower.internal_temperature))
        print("---> mower status : " + mower.mower_status)
        print("---> manual operation : " + str(mower.mower_manual_operation))
        print("---> timestamp next start : " + mower.mower_timestamp_next_start)
        print("---> sgtin : " + mower.sgtin)
        print("---> manufacturer : " + mower.manufacturer)
        # Commands (Untested)
        # mower.park_until_next_timer()
        # mower.park_until_further_notice()
        # mower.start_resume_schedule()
        # mower.start_override_timer()

    # Iterate over sensors
    for sensor in location.sensors.values():
        print("-> sensor : " + sensor.name + "(" + sensor.id + ")")
        print("---> category : " + sensor.category)
        print(
            "---> is_configuration_synchronized : " +
            str(sensor.is_configuration_synchronized))
        print("---> serial number : " + sensor.serial_number)
        print("---> version : " + sensor.version)
        print("---> last time online : " + sensor.last_time_online)
        print("---> device state : " + sensor.device_state)
        print("---> battery level : " + str(sensor.battery_level))
        print("---> battery rechargeable status : " + sensor.battery_status)
        print("---> radio quality : " + str(sensor.radio_quality))
        print("---> radio connection status : " + sensor.radio_connection_status)
        print("---> radio state : " + sensor.radio_state)
        print("---> firmware status : " + sensor.firmware_status)
        print("---> firmware upload in progress : " + sensor.firmware_upload_progress)
        print("---> firmware upload start : " + sensor.firmware_update_start)
        print("---> ambient temperature : " + str(sensor.ambient_temperature))
        print("---> frost warning : " + sensor.frost_warning)
        print("---> soil temperature : " + str(sensor.sensor_soil_temperature))
        print("---> soil humidity : " + str(sensor.sensor_soil_humidity))
        print("---> light : " + str(sensor.sensor_light))
        print("---> sgtin : " + sensor.sgtin)
        print("---> manufacturer : " + sensor.manufacturer)
        print("---> firmware_available_version : " + str(sensor.firmware_available_version))
        # Commands :
        # sensor.refresh_ambient_temperature()
        # sensor.refresh_light_intensity()
        # sensor.refresh_soil_moisture()

    # Iterate over water control
    for water_control in location.water_controls.values():
        print("-> water control : " + water_control.name + "(" + water_control.id + ")")
        print("---> category : " + water_control.category)
        print(
            "---> is_configuration_synchronized : " +
            str(water_control.is_configuration_synchronized))
        print("---> serial number : " + water_control.serial_number)
        print("---> version : " + water_control.version)
        print("---> last time online : " + water_control.last_time_online)
        print("---> device state : " + water_control.device_state)
        print("---> battery level : " + str(water_control.battery_level))
        print("---> battery rechargeable status : " + water_control.battery_status)
        print("---> radio quality : " + str(water_control.radio_quality))
        print("---> radio connection status : " + water_control.radio_connection_status)
        print("---> radio state : " + water_control.radio_state)
        print("---> firmware status : " + water_control.firmware_status)
        print("---> firmware upload in progress : " + water_control.firmware_upload_progress)
        print("---> firmware upload start : " + water_control.firmware_update_start)
        print("---> ambient temperature : " + str(water_control.ambient_temperature))
        print("---> frost warning : " + water_control.frost_warning)
        print("---> valve open : " + str(water_control.watering_valve_open))
        print("---> manual override : " + water_control.watering_manual_override)
        print("---> sgtin : " + water_control.sgtin)
        print("---> manufacturer : " + water_control.manufacturer)
        print("---> firmware_available_version : " + str(water_control.firmware_available_version))
        # Commands :
        # water_control.open_valve()  # 30 minutes by default
        # water_control.open_valve(duration=10)  # 10 minutes before closing valve
        # water_control.close_valve()  ## close manual override
        
    # Iterate over water control
    for power in location.powers.values():
        print("->power : " + power.name + "(" + power.id + ")")
        print("---> category : " + power.category)
        print(
            "---> is_configuration_synchronized : " +
            str(power.is_configuration_synchronized))
        print("---> serial number : " + power.serial_number)
        print("---> version : " + power.version)
        print("---> last time online : " + power.last_time_online)
        print("---> device state : " + power.device_state)
        print("---> radio quality : " + str(power.radio_quality))
        print("---> radio connection status : " + str(power.radio_connection_status))
        print("---> radio state : " + str(power.radio_state))
        print("---> firmware status : " + str(power.firmware_status))
        print("---> firmware upload in progress : " + str(
            power.firmware_upload_progress))
        print("---> firmware update start : " + str(
            power.firmware_update_start))
        print("---> power timer : " + str(power.power_timer))
        print("---> power error : " + str(power.power_error))
        # Commands :
        # power.power_on(60) # In seconds
        # power.power_off()
        # power.refresh_link_status()
```

## Development environment

To install the dev environment, you just have to do, in the source code directory :

```sh
$ pip install -e .[dev]
```


## Credits

This library would not have been possible without the work of :
* DXSdata (http://www.dxsdata.com/2016/07/php-class-for-gardena-smart-system-api/)
* Gerrieg (https://www.roboter-forum.com/index.php?thread/16777-gardena-smart-system-analyse/&l=2)
