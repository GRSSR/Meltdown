local reactor = peripheral.find("BigReactors-Reactor")
local screen = peripheral.find("monitor")

local reactorEnergy = 0
local maxEnergyStorage = 10000000
local previousReactorEnergy = maxEnergyStorage

local tickTime = 0.25
local ticksPerLoop = 40
local dt = tickTime * ticksPerLoop
local targetEnergy = maxEnergyStorage * 0.5
local propWeight = 1.5
local diffWeight = 0.5
local intWeight = 0.0

reactor.setActive(true)
local enableReactor = true
local rodDisplay
local rodDisplayLev

screen.setBackgroundColour(colours.black)
screen.clear()

local counter = 0
local integral = 0
local prevError = 0

local function clamp(value)
	if value > 100 then
		return 100
	elseif value < 0 then
		return 0
	else 
		return value
	end
end

local function reactorLogic()
	while enableReactor do
		local rodLevel = reactor.getControlRodLevel(0)
		reactorEnergy = reactor.getEnergyStored()

		local pdiError = reactorEnergy - targetEnergy
		local pdiDiff = (pdiError + prevError) /dt
		integral = integral + pdiError*dt 

		local prop = propWeight*pdiError
		local deriv = diffWeight*pdiDiff
		local int = intWeight * integral

		local totalFactor = (prop + deriv + int)/targetEnergy
		reactor.setAllControlRodLevels(clamp(100*totalFactor))

		prevError = pdiError
		coroutine.yield()
	end
	print("ending reactor coroutine")
end

local function drawLevel(win, win2, value, barColour)
	local width, height = win.getSize()
	local maxBarHeight = height - 1
	win.setBackgroundColour(barColour)
	win.clear()

	win2.reposition(1 , 1, width, value*height)

end

local function drawScreen()
	if rodDisplay  == nil then
		print("first draw")
		local screenX, screenY = screen.getSize()
		screenY = screenY - 3
		rodDisplay = window.create(screen, screenX, 4, 1, screenY)
		rodDisplay.setBackgroundColour(colours.red)
		rodDisplayLev = window.create(rodDisplay, 1, 1, 0, 0)
		rodDisplayLev.setBackgroundColour(colours.white)
		rodDisplay.clear()

		
	end
	screen.setBackgroundColour(colours.black)
	screen.clear()
	screen.setCursorPos(1,4)
	screen.write("Control rods:")
	screen.setCursorPos(1,5)
	screen.write(reactor.getControlRodLevel(0))
	screen.setCursorPos(1,6)

	if reactor.getActive() then
		screen.setBackgroundColour(colours.green)
	else
		screen.setBackgroundColour(colours.red)
	end
	screen.write("Power")
	screen.setBackgroundColour(colours.black)
	drawLevel(rodDisplay, rodDisplayLev, 1- reactor.getEnergyStored()/maxEnergyStorage, colours.red)
end

reactorLoop = coroutine.create(reactorLogic)

while true do
	if (counter  % ticksPerLoop) == 0 then
		coroutine.resume(reactorLoop)
		counter = 0
	end
	counter = counter + 1
	drawScreen()
	sleep(ticktime)
end
