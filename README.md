# Track my usage of electricity

## Background

I purchased a EMU-2 home energy display from Puget Sound Energy (PSE).  The EMU-2 unit is manufactured by Rainforest Automation.  PSE intended customers to use the EMU-2 to ascertain how much electricity each appliance uses in an effort to reduce electricity usage.  A customer would put batteries in the unit, walk around and turn on and off various appliances to see the usage.  My intent from the start was to track the kWh electricity usage over time.  I can see what hours of the day have the most usage and I can track the daily usage as I progress through the monthly billing cycle.

## Automating Data Collection

I searched for a way to automate or script the collection of data. I found the emu_power Python library that automated the interface between Python and the EMU-2 serial device.  The same Python script can run on Linux and Windows computers with the only difference being the device name.  Windows 11 has a USB-Serial emulation port.

emu-power 1.51: https://pypi.org/project/emu-power/
