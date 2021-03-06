# gd-ant-plus

A node.js module for ANT+ 

with some small enhancements that enable 
- setting up a FitnessEquipment Device 
- sending messages from FitnessEquipmentSensor to Device and checking status of these messages

## Prerequisites

Libusb is included as a submodule. On Linux, you'll need libudev to build libusb. On Ubuntu/Debian: `sudo apt-get install build-essential libudev-dev`

### Windows

Use [Zadig](http://sourceforge.net/projects/libwdi/files/zadig/) to install the WinUSB driver for your USB device. Otherwise you will get `LIBUSB_ERROR_NOT_SUPPORTED` when attempting to open devices.

## Install

```sh
npm install gd-ant-plus
```

## usage

```javascript
var Ant = require('gd-ant-plus');
```

#### Create USB stick

```javascript
var stick = new Ant.GarminStick3;
```

#### Create sensors

```javascript
var sensor = new Ant.HeartRateSensor(stick);
```

#### Attach events

```javascript
sensor.on('hbData', function (data) {
    console.log(data.DeviceID, data.ComputedHeartRate);
});

stick.on('startup', function () {
    sensor.attach(0, 0);
});
```

#### Open stick

```javascript
if (!stick.open()) {
    console.log('Stick not found!');
}
```

### scanning

```javascript
sensor.on('hbData', function (data) {
    console.log(data.DeviceID, data.ComputedHeartRate);
});

stick.on('startup', function () {
    sensor.scan();
);

if (!stick.open()) {
    console.log('Stick not found!');
}
```

## Important notes

* never attach a sensor before receiveing the startup event
* never attach a new sensor before receiving the attached or detached event of the previous sensor
* never detach a sensor before receiving the attached or detached event of the previous sensor

## Objects

### GarminStick2 and GarminStick3

#### properties

##### maxChannels

The maximum number of channels that this stick supports; valid only after startup event fired.

#### methods

##### is_present()

Checks if the stick is present. Returns true if it is, false otherwise.

##### open()

Tries to open the stick. Returns false on failure.

##### openAsync(callback)

Tries to open the stick, waiting for it if not available right now. Returns a cancelation token with a method `cancel` you can use to stop waiting.
`callback` is a funcion accepting a single `Error` parameter and it will be called when the stick is open (with the parameter undefined) or in case of failure (with the parameter set to the error).

##### close()

Closes the stick.

#### events

##### startup

Fired after the stick is correctly initialized.

##### shutdown

Fired after the stick is correctly closed.

### Common to all Sensors

#### methods

##### attach(channel, deviceId)

Attaches the sensors, using the specified channel and deviceId (use 0 to connect to the first device found).

##### detach()

Detaches the sensor.

#### events

##### attached

Fired after the sensor is correctly attached.

##### detached

Fired after the sensor is correctly detached.

### Common to all Scanners

#### methods

##### scan()

Attaches the sensors and starts scanning for data from every devices in range.

##### detach()

Detaches the sensor.

#### events

##### attached

Fired after the sensor is correctly attached.

##### detached

Fired after the sensor is correctly detached.

### HeartRate

#### events

##### hbData

Fired when new heartbeat data is received.

### SpeedCadence

#### methods

#### setWheelCircumference(circumferenceInMeters)

Calibrates the speed sensor. Defaults to an ~ 700c wheel, 2.118

#### events

##### speedData

Fired when a new wheel speed is calculated

##### cadenceData

Fired when a new pedal cadence is calculated

### StrideSpeedDistance

#### events

##### ssdData

Fired when new data been calculated.

### BicyclePower

#### events

##### powerData

Fired when new power has been calculated.

### FitnessEquipment

#### events

##### fitnessData

Fired when new data is received.
