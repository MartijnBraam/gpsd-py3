GpsResponse Object Information
-----

### Properties ###
Description and information copied from [http://catb.org/gpsd/gpsd_json.html](http://catb.org/gpsd/gpsd_json.html).

- **mode**
	- *Description:* Indicates the status of the GPS reception
	- *Availability:* Always
	- *Data Type:* Int
	- *Possible Values:* 0=no mode value yet seen, 1=no fix, 2=2D fix, 3=3D fix
- **sats**
	- *Description:* The number of satellites received by the GPS unit
	- *Availability:* Always
	- *Data Type:* Int
- **sats_valid**
	- *Description:* The number of satellites with valid information
	- *Availability:* Always
	- *Data Type:* Int
- **lat**
	- *Description:* Latitude in degrees
	- *Availability:* mode >= 2
	- *Data Type:*
	- *Possible Values:* -90.0 to 90.0
- **lon**
	- *Description:* Longitude in degrees
	- *Availability:* mode >= 2
	- *Data Type:* float
	- *Possible Values:* -180.0 to 180.0
- **track**
	- *Description:* Course over ground, degrees from true north
	- *Availability:* mode >= 2
	- *Data Type:* float
- **hspeed**
	- *Description:* Speed over ground, meters per second
	- *Availability:* mode >= 2
	- *Data Type:* float
- **time**
	- *Description:* Time/date stamp in ISO8601 format, UTC. May have a fractional part of up to .001sec precision May be absent if mode is not 2 or 3.
	- *Availability:* mode >= 2
	- *Data Type:* string
	- *Sample Value:* 2016-08-05T01:51:44.000Z
- **error**
	- *Description:* Error information 
		- c - ecp: Climb/sink error estimate in meters/sec, 95% confidence.
		- s - eps: Speed error estinmate in meters/sec, 95% confidence.
		- t - ept: Estimated timestamp error (%f, seconds, 95% confidence). Present if time is present.
		- v - epv: Estimated vertical error in meters, 95% confidence. Present if mode is 3 and DOPs can be calculated from the satellite view.
		- x - epx: Longitude error estimate in meters, 95% confidence. Present if mode is 2 or 3 and DOPs can be calculated from the satellite view.
		- y - epy: Latitude error estimate in meters, 95% confidence. Present if mode is 2 or 3 and DOPs can be calculated from the satellite view.
	- *Availability:* mode >= 2 *(**NOTE:** c & v require mode >= 3)*
	- *Data Type:* dictionary
	- *Possible Values:*
- **alt**
	- *Description:* Altitude in meters
	- *Availability:* mode >= 3
	- *Data Type:* float
- **climb**
	- *Description:* Climb (positive) or sink (negative) rate, meters per second
	- *Availability:* mode >= 3
	- *Data Type:* float

### Methods ###
- **position**
	- *Description:* Get the latitude and longtitude as tuple
	- *Availability:* mode >= 2
	- *Parameters:* None
	- *Return Type:* tuple (latitude, longitude)
	- *Sample Value:*  (39.8333333, -98.585522)
- **speed**
	- *Description:* Get the horisontal speed with the small movements filtered out
	- *Availability:* mode >= 2
	- *Parameters:* None
	- *Return Type:* float
- **position_precision**
	- *Description:* Get the error margin in meters for the current fix
	- *Availability:* mode >= 2
	- *Parameters:* None
	- *Return Type:* tuple (x-y plane, z direction)
- **get_time(local_time: bool)**
	- *Description:* Get the GPS time UTC or in local timezone
	- *Availability:* mode >= 2
	- *Parameters:* local_time
	- *Return Type:* datetime
- **map_url**
	- *Description:* Get a openstreetmap url for the current position
	- *Availability:* mode >= 2
	- *Parameters:* None
	- *Return Type:* string
	- *Sample Value:* http://www.openstreetmap.org/?mlat=39.8333333&mlon=-98.585522&zoom=15
- **altitude**
	- *Description:* Get the altitude in meters
	- *Availability:* mode >= 3
	- *Parameters:* None
	- *Return Type:* float
- **movement**
	- *Description:* Get the speed and direction of the current movement as dict
	- *Availability:* mode >= 3
	- *Parameters:* None
	- *Return Type:* dictionary
		- speed
		- track
		- climb
- **speed_vertical**
	- *Description:* Get the vertical speed with the small movements filtered out
	- *Availability:* mode >= 3
	- *Parameters:* None
	- *Return Type:* float


Exception Information
-----

- NoFixError: Raised when a value is requested with the mode below the threshold for obtaining the data
	- Needs at least 2D fix
	- Needs at least 3D fix
- General Exceptions
	- Unexpected message received from gps: ...
	- Unexpected data received as welcome. Is the server a gpsd 3 server?


Information on Available Functions
-----

- **connect**
	- *Description:* Connect to a GPSD instance
	- *Parameters:* host="127.0.0.1", port=2947
	- *Return Type:* None
- **get_current**
	- *Description:* Poll gpsd for a new position
	- *Parameters:* None
	- *Return Type:* GpsResponse Object
- **device**
	- *Description:* Get information about current gps device
	- *Parameters:* None
	- *Return Type:* dictionary
		- path
		- speed
		- driver
	- *Sample Data:* {'speed': 9600, 'path': '/dev/ttyS0', 'driver': 'MTK-3301'}


----------

## Sample Code ##

```python
#!/usr/bin/env python3

import gpsd

# Connect to the local gpsd
gpsd.connect()

# Connect somewhere else
gpsd.connect()

# Get gps position
packet = gpsd.get_current()

# See the inline docs for GpsResponse for the available data
print(" ************ PROPERTIES ************* ")
print("  Mode: " + str(packet.mode))
print("Satellites: " + str(packet.sats))
if packet.mode >= 2:
print("  Latitude: " + str(packet.lat))
print(" Longitude: " + str(packet.lon))
print(" Track: " + str(packet.track))
print("  Horizontal Speed: " + str(packet.hspeed))
print(" Time: " + str(packet.time))
print(" Error: " + str(packet.error))
else:
print("  Latitude: NOT AVAILABLE")
print(" Longitude: NOT AVAILABLE")
print(" Track: NOT AVAILABLE")
print("  Horizontal Speed: NOT AVAILABLE")
print(" Error: NOT AVAILABLE")

if packet.mode >= 3:
print("  Altitude: " + str(packet.alt))
print(" Climb: " + str(packet.climb))
else:
print("  Altitude: NOT AVAILABLE")
print(" Climb: NOT AVAILABLE")

print(" ************** METHODS ************** ")
if packet.mode >= 2:
print("  Location: " + str(packet.position()))
print(" Speed: " + str(packet.speed()))
print("Position Precision: " + str(packet.position_precision()))
print("  Time UTC: " + str(packet.time_utc()))
print("Time Local: " + str(packet.time_local()))
print("   Map URL: " + str(packet.map_url()))
else:
print("  Location: NOT AVAILABLE")
print(" Speed: NOT AVAILABLE")
print("Position Precision: NOT AVAILABLE")
print("  Time UTC: NOT AVAILABLE")
print("Time Local: NOT AVAILABLE")
print("   Map URL: NOT AVAILABLE")

if packet.mode >= 3:
print("  Altitude: " + str(packet.altitude()))
# print("  Movement: " + str(packet.movement()))
# print("  Speed Vertical: " + str(packet.speed_vertical()))
else:
print("  Altitude: NOT AVAILABLE")
# print("  Movement: NOT AVAILABLE")
# print(" Speed Vertical: NOT AVAILABLE")

print(" ************* FUNCTIONS ************* ")
print("Device: " + str(gpsd.device()))
```


###Sample Output##
     ************ PROPERTIES *************
                  Mode: 3
            Satellites: 10
              Latitude: 39.8333333
             Longitude: -98.585522
                 Track: 237.31
      Horizontal Speed: 0.0
                  Time: 2016-08-05T03:02:13.000Z
                 Error: {'v': 31.97, 't': 0.005, 'x': 10.055, 'c': (63.94,), 's': 35 .16, 'y': 17.58}
              Altitude: 100.3
                 Climb: 0.0
     ************** METHODS **************
               Location: (39.8333333, -98.585522)
                  Speed: 0
     Position Precision: (17.58, 31.97)
               Time UTC: 2016-08-05 03:02:13
             Time Local: 2016-08-04 23:02:13-04:00
                Map URL: http://www.openstreetmap.org/?mlat=39.8333333&mlon=-98.585522&zoom=15
               Altitude: 100.3
     ************* FUNCTIONS *************
                 Device: {'driver': 'MTK-3301', 'path': '/dev/ttyS0', 'speed': 9600}
