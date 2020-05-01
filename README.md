# Automated Reactor Control System (ARCS)
Using a ComputerCraft or CC:Tweaked computer this Lua script will automatically adjust the reactor's control rods based upon the current power demand.

ARCS was written for BigReactors version 0.4.3A using ComputerCraft CraftOS 1.7. However, this script <i>should</i> work with ExtremeReactors and CC:Tweaked seeing as those mods are forks of the aforementioned BigReactors and ComputerCraft.

## Setup
In-order to connect the computer to the reactor, place the computer directly against the `Reactor Computer Port` and copy this script into your `/` or `disk/` directory. To make sure the script will run automatically whenever the computer is started, rename it to `startup` if it isn't already.

## Configuration
A configuration file named `arcs.config` will be generated in the `/` directory if a disk drive is not present, otherwise it will be generated in `disk/`.

```
# The amount in RF that the reactor's buffer is allowed to deviate
# from half-full before adjustments are made. The drift comes from
# inaccuracies in power adjustment.
#
# Default: 0
reactorBufferAllowedDrift = 0

# The threshold in RF that must be exceeded before any adjustments 
# take place. Adjust this accordingly for your reactor.
#
# Default: 1
adjustmentThreshold = 1

# The amount from 1-99 that the reactors control rods are 
# raised/lowered by each time an adjustment is made. A higher
# value will adjust the control rods faster albeit at the cost 
# of accuracy.
#
# Default: 1
controlRodAdjustmentAmount = 1

# Emit redstone when the factor of the maximum capacity of fuel
# divided by the current amount of fuel is greater-than or equal
# to the specified value. Eq. getFuelAmountMax() / getFuelAmount()
#
# Default: 2
emitRedstoneWhenLowOnFuel = 2

# Emit redstone from the specified side. Redstone signal is
# emitted when the reactor is low on fuel.
#
# Default: none
emitRedstoneFromSide = none
```
