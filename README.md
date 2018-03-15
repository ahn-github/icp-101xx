# ICP-101xx Library and Breakout Board

The TDK InvenSense ICP-101xx is a family of very low power, very high accuracy barometric perssure sensors. These sensors can measure pressure difference of as little as +/- 1 Pascal, which is equivalent to altitude differences of 8.5cm.

Documentation by TDK InvenSense:
* Product page: [TDK InvenSense](https://www.invensense.com/products/1-axis/icp-101xx/)
* Datasheet: [ICP-101xx](http://www.invensense.com/wp-content/uploads/2018/01/DS-000186-ICP-101xx-v1.0.pdf)
* Application Note: [Differential Pressure Sensing for Drones](https://www.invensense.com/download-pdf/an-000119-differential-pressure-sensing-using-icm-20789-for-altitude-hold-in-drones/)

## Arduino Library

The Arduino library ICP101xx provides a simple API to read temperature and pressure. The library was developed and tested with the ICP-10100, but should work with the other sensors of the family.

The library includes 3 examples that demonstrate its use.

### The ICP101xx Object

Use the `ICP101xx` object to create an instance of the sensor. For example by declaring a global variable:

```
ICP101xx mysensor;
```

### begin()

To initialize the sensor, use the `begin` method.

```
mysensor.begin();
```

By default, the sensor will use the Arduino `Wire` object for I2C. If you want to use the sensor on a different I2C port, pass a pointer to the `Wire` object the library should use. For example:

```
mysensor.begin(&Wire1);
```

The `begin` method connects to the sensor and reads the calibration data. It will return `false`, if the sensor does not respond. This can be used for error handling.

```
if (!mysensor.begin()) {
	// sensor did not respond, do error handling here
}
```

### isConnected()

This method is used to check whether the sensor is responding. This method returns `true` if the sensor can be reached.

```
if (mysensor.isConnected()) {
	// do some measurements
} else {
	// report an error
}
```

`isConnected` will return false in the following situations:

- No response from the sensor. Check you connections and pull-up resistors.
- Sensor responds with an unknown or unsupported device ID.

### measure()

The method `measure` performs a measurement, and returns when it is complete.

The time to complete a measurement depends on the selected sensor mode that can be passed as an optional parameter. The options are:

|Mode|Duration|Noise|Notes|
|--|--|--|--|
|FAST|3 ms|±3.2 Pa| |
|NORMAL|7 ms|±1.6 Pa|default|
|ACCURATE|24 ms|±0.8 Pa| |
|VERY_ACCURATE|95 ms|±0.4 Pa| |

If mode is not specified, NORMAL will be used.

```
mysensor.measure(mysensor.VERY_ACCURATE);	// high accuracy measurement
// process data
```

### measureStart(), dataReady()

Sometimes you may not want to wait idly until a measurement is complete, but do something useful while the sensor is busy. Especially when using the VERY_ACCURATE mode, which takes almost 100ms to complete.

`measureStart` initates a new measurement and immediatly returns control to the calling program. Like the method `measure`, an optional parameter can be used to specify the accuracy. You can then use `dataReady` to check whether the sensor completed its task.

```
mysensor.measureStart(mysensor.VERY_ACCURATE);
while (!mysensor.dataReady()) {
	// do some other useful stuff
}
// the sensor is done, process the data
```

### readPressurePa()

When measurement is complete, the results are stored in the mysensor object and can be read with dedicated methods.

`readPressurePa` returns the pressure in Pascal (Pa) as a `float`.

```
mysensor.measure();
Serial.print("Pressure is ");
Serial.print(mysensor.readPressure);
Serial.println(" Pa");
```

Pascal is mostly of interest to measure tiny pressure differences. For absolute pressure, you may want to apply some simple math.

|Target unit|Formula|
|--|--|
|hPa|Pa / 100|
|mbar|Pa / 100|
|mmHg|Pa / 133.322365|
|inHg|Pa / 3386.389|
|atm|Pa / 101325|

### readTemperatureC(), readTemperatureF()

The ICP-101xx also includes a very precise temperature sensor with an absolute accuracy of ±0.4 Celcius. The temperature is sampled together with the pressure during each measurement cylce. Use readTemperatureC or readTemperatureF to get the temperature in Celsius or Fahrenheit respectively.

```
mysensor.measure();
Serial.print("The current temperature is ");
Serial.print(mysensor.readTemperatureF());
Serial.println(" Fahrenheit");
```

## Breakout Board

KiCad project: coming soon

Circuit boards: [OSH Park](https://oshpark.com/projects/QdVsvLNH)

Contact me if you are interested in buying populated and tested breakout boards.
