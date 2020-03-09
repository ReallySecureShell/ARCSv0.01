# vim: syntax=lua

-- GLOBAL VARIABLES
controlRodsArray = {}

configuration = {}
--

-- BigReactors API can be found here: https://ftbwiki.org/Reactor_Computer_Port

-- Possibly how RF generation is calculated: https://github.com/erogenousbeef/BigReactors/blob/master/src/main/java/erogenousbeef/bigreactors/utils/StaticUtils.java

print("Automated Reactor Control System v0.01-Alpha")

function getReactorConfiguration()
    print('[INFO] Gathering data on reactor configuration...')
    
    -- Make the array for indexing reactor control rods
    print('[INFO] Indexing control rods')
	for intControlRod=0,reactor.getNumberOfControlRods()-1,1 do
		controlRodsArray[intControlRod] = intControlRod
	end

	-- Check if a disk drive is present.
	if not fs.isDir("disk/") then
		configurationName = "arcs.config"
	else
		configurationName = "disk/arcs.config"
	end

	if not fs.exists(configurationName) then
		print('[INFO] Creating configuration file: ',configurationName)
		configurationIO = io.open(configurationName,"w")
		io.output(configurationIO)

		writeConfiguration = [[
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
]]
		io.write(writeConfiguration)

		io.close()
		
		writeConfiguration = nil
	end

	print('[INFO] Loading values from configuration file: ', configurationName)

	configurationIO = io.open(configurationName,"r")
	io.input(configurationIO)
 
	for line in configurationIO:lines() do
		for string in string.gmatch(line,"= (.*)") do
			table.insert(configuration,tonumber(string))
		end
	end
	
	io.close()
end

-- Search for peripherals
function findPeripherals()
	for _,side in pairs(peripheral.getNames()) do
		if(peripheral.getType(side) == 'BigReactors-Reactor') then
			print('[INFO] Wrapping to reactor on ',side,' side.')
			reactor = peripheral.wrap(side)
			reactorSide = side
			getReactorConfiguration()
		end
	end
  
	if not reactor then
		print('[WARN] No reactor found. Halting until one is connected...')
		reactor = nil
		haltProgram = true
	end
end
findPeripherals()

function calculateFluctuation(rfGeneration,previousInternalBufferValue)
	-- Set the sleep statement equal to 1 tick.
	sleep(0.050)
	return reactor.getEnergyStored() - previousInternalBufferValue
end

print('[INFO] Setup complete. Executing main program loop')

local timeEvent = os.startTimer(1)

while true do
	local event,data1 = os.pullEventRaw()

	if event == "terminate" then
		print('Caught terminate event')
		reactor = nil
		return

	elseif event == "peripheral" then
		findPeripherals()
		haltProgram = nil
		timeEvent = os.startTimer(1)
        
	elseif event == "peripheral_detach" then
		if data1 == reactorSide then
			print('[WARN] Reactor detached. Halting program.')
			haltProgram = true
		end

	elseif event == "timer" and not haltProgram then
		if data1 == timeEvent then
			fluctuation = calculateFluctuation(reactor.getEnergyProducedLastTick(),reactor.getEnergyStored())

			-- If current energy level is greater than half + Threshold,
			-- reduce power output.
			if reactor.getEnergyStored() > 5000000 + configuration[1] then
				-- Repeat until fluctuation is less-than negative configuration[2].
				if fluctuation > configuration[2]-(configuration[2]*2) then
					for _,controlRod in pairs(controlRodsArray) do
						if reactor.getControlRodLevel(controlRod) + configuration[3] <= 100 then
							reactor.setControlRodLevel(controlRod,reactor.getControlRodLevel(controlRod) + configuration[3])
						end
					end
				end

			-- If current energy level is less than half - Threshold,
			-- increse power output.
			elseif reactor.getEnergyStored() < 5000000 - configuration[1] then
				if fluctuation < configuration[2] then
					for _,controlRod in pairs(controlRodsArray) do
						if reactor.getControlRodLevel(controlRod) - configuration[3] >= 0 then
							reactor.setControlRodLevel(controlRod,reactor.getControlRodLevel(controlRod) - configuration[3])
						end
					end
				end
			end
			timeEvent = os.startTimer(1)
		end
	end
end

