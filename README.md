# ARCSv0.01
Using a ComputerCraft or CC:Tweaked computer this Lua script will automatically adjust the reactor's control rods based upon the current power demand.

## Configuration
Although not required, there are a few different configuration options. A configuration file named `arcs.config` will be generated in the `/` directory if a disk drive is not present, otherwise it will be generated in `disk/`.

```
# The amount in RF that the reactor's buffer is allowed to deviate
# before the program adjusts the buffer back to half-full. The drift 
# comes from inaccuracies of power-adjustment overtime.
#
# Default: 100000
reactorBufferAllowedDrift = 100000

# The threshold in RF that must be exceeded before any adjustments 
# take place. Adjust this accordingly for your reactor.
#
# Default: 1000 
adjustmentThreshold = 1000

# The amount from 1-100 that the reactors control rods are 
# raised/lowered by each time an adjustment is made. A higher
# value will adjust the control rods faster albeit at the cost 
# of accuracy.
#
# Default: 1
controlRodAdjustmentAmount = 1
```
