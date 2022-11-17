# Track My Usage of Electricity

## Background

I purchased an EMU-2 home energy display from Puget Sound Energy (PSE).  The EMU-2 unit is manufactured by Rainforest Automation.  PSE intended customers to use the EMU-2 to ascertain how much electricity each appliance uses in an effort to reduce electricity usage.  A customer would put batteries in the unit, walk around and turn on and off various appliances to see the usage.  My intent from the start was to track the kWh electricity usage over time.  I can see what hours of the day have the most usage and I can track the daily usage as I progress through the monthly billing cycle.

## Scripting Electricity Data Collection

I searched for a way to script the collection of data. I found the emu_power Python library that automated the interface between Python and the EMU-2 serial device.  The same Python script can run on Linux and Windows computers with the only difference being the device name.  Windows 11 has a USB-Serial emulation port.

emu_power is based on the XML spec for the Rainforest RAVEN API.  The spec is similar to the API that the EMU-2 device uses.

```
Windows Port: COM5
Linux Port: /dev/ttyACM0
```

emu-power 1.51: https://pypi.org/project/emu-power/

## Python Script Design

### Overview

The EMU-2 reports the instaneous electricity usage when queried.  It returns a value in Kilowatt Hours (kWh) as if that amount was used for a full hour.  I wanted to get a total of electricity used for each whole hour.  I decided to take a reading every minute and add that value to an accumulator.  At the end of the hour, the script divides that amount by the number of readings which is usually 60.  This value is written to a tab-seperated file.  The values are written within an infinite loop.  The script must be externally stopped if desired.

### Database

I soon realized that I needed to put the values into a database and I could use my SQL skills to query the results in different ways.  The next step was to setup a MySQL database to receive the values.

* def insertDB(myDate, myHour, mykWh): A self-contained function to insert a record into the database.
* class MySignalHandler:
  * def setup(self,thePID,theAR): Initialize signal handler and the variables PID and AR
  * def catch(self, signalNumber, frame): Get the current time, set a message and print the message; remove file indicating the script is running; flush STDOUT and STDERR; call os.kill() to kill itself.
  
The script has a main() function that contains the a lot of the code.  main() is used to ensure that the script is run as top-level script.

* if __name__ == '__main__':
  * Get command line arguments with parser
  * Read confifuration file with config
  * If script-is-executing file exists, exit
  * Redirect STDOUT and STDERR to log file
  * Get config values into script variables
  * Setup signal handler
  * Create script-is-executing file
  * Call main()
  
* main():
  * while True:
    * If Stop-File exists, exit
    * Retry loop on call to api.get_instantaneous_demand()
    * Check if call failed and exit if it did
    * Get values: demand, divisor, multiplier, kw
    * Check if we're still in the same hour and add to kWh if so
    * else write kWh out, stop_serial() and start_serial() to refresh serial connection
    * Retry loop on start_serial()
    * Check if it's Midnight and print summary if it is.  Note that this code is obsolete since we're also inserting values into the database.
    * sleep 60 seconds
    
etc