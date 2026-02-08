AllSky Dew Heater Module

Status: Stable
Level: Experienced
Runs In: Periodic

This module allows for control of a dew heater via a relay connected to one of the Raspberry Pis GPIO pins.

The module requires an external temperature and humidity sensor. Currently the following sensors are supported

- SHT31
- DHT11
- DHT22
- AHTx0
- BME280
- HTU21
- SOLO-Cloudwatcher
- DS18B20 (temperature only; use Forced Temperature or OpenWeather humidity for dew point)


The module contains the following options

Sensor Type: The type of external sensor being used
Input Pin: The GPIO pin used for non i2c sensors such as the DHT11/22
i2c Address: Override the default i2c address for the selected device
DS18B20 Address: Filename in /sys/bus/w1/devices
Use OpenWeather Humidity: When using DS18B20, read humidity from OpenWeather to compute dew point with local temperature
Heater Pin: The GPIO pin the heater relay is connected to
Extra Pin: Extra pin that will be triggered with heater pin
Heater Startup State : The state of the heater when starting Allsky
Invert Relay : Normally the GPIO pin will be set High when the heater is required. This option will set the GPIO pin Low when the heater is required
Invert Extra Pin: Normally a GPIO extra pin will go high when enabling the heater. Selecting this option inverts extra pin to go low when enabling heater
Control Mode: Choose On/Off control or PWM control via /sys/class/pwm
PWM Chip: PWM chip number under /sys/class/pwm (e.g. 2 for /sys/class/pwm/pwmchip2)
PWM Channel: PWM channel under the chip (e.g. 0 or 1)
PWM Frequency (Hz): PWM frequency in Hz
PWM Min Duty (%): Minimum duty cycle percentage
PWM Max Duty (%): Maximum duty cycle percentage
Delay: The time in seconds between readings
Limit: If the temperature is this number of degrees above the dew point the heater will be enabled
Forced Temperature: Below this temperature the heater will always be enabled

PWM Notes:
- Requires kernel PWM sysfs support (e.g. dtoverlay=pwm-2chan in /boot/firmware/config.txt) and reboot.
- The module writes to /sys/class/pwm and therefore needs appropriate permissions.

Installation (PWM sysfs):
1. Edit /boot/firmware/config.txt and add:
   dtoverlay=pwm-2chan
2. Reboot the Raspberry Pi.
3. Verify PWM sysfs exists:
   ls /sys/class/pwm/pwmchip*
4. Ensure your user has write access to /sys/class/pwm (or run allsky with sufficient permissions).

Test (PWM sysfs):
1. In module settings:
   - Control Mode = PWM
   - PWM Chip = 2 (or your chip index)
   - PWM Channel = 0 or 1
   - PWM Frequency = 1000 (example)
   - PWM Min Duty = 0
   - PWM Max Duty = 100
2. Configure a sensor and a Limit value.
3. Run Allsky and check logs for:
   "PWM enabled on pwmchipX pwmY @ ...Hz"
4. Observe duty changes:
   - If temperature approaches dew point, duty should increase.
   - If temperature is well above dew point, duty should drop toward PWM Min Duty.

Notes:
- For DS18B20, enable "Use OpenWeather Humidity" if you want a dew point calculation.
- If dew point is unavailable, PWM duty is set to 0.
