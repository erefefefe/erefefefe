


-- Script made by vaultxz if anyone else says this is theres tell me THANKS AND HAVE FUN :) -- 


local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Drop offs GUI | Made by vaultzx", "DarkTheme")


-- Updates
local Updates = Window:NewTab("Updates")
local UpdatesSection = Updates:NewSection("Updates")
local UpdatesSection = Updates:NewSection("Made on 8/11/2022")

-- Scripts
local Scripts = Window:NewTab("Scripts")
local ScriptsSection = Scripts:NewSection("Scripts")
ScriptsSection:NewButton("Aimbot", "Double space 85 PWR", function()
   -- Gui to Lua
-- Version: 3.2

-- Instances:

local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local AttachButton = Instance.new("TextButton")
local UICorner_2 = Instance.new("UICorner")
local LockButton = Instance.new("TextButton")
local UICorner_3 = Instance.new("UICorner")

--Properties:

ScreenGui.Parent = game:GetService("RunService"):IsStudio() and game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui") or game:GetService("CoreGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Name = "FollowGui"



UICorner.CornerRadius = UDim.new(0.125, 0)
UICorner.Parent = Frame

--AttachButton.Name = "AttachButton"
--AttachButton.Parent = Frame
--AttachButton.AnchorPoint = Vector2.new(1, 0.5)
--AttachButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
--AttachButton.BorderColor3 = Color3.fromRGB(191, 0, 0)
--AttachButton.BorderSizePixel = 0
--AttachButton.Position = UDim2.new(0.5, -10, 0.5, 0)
--AttachButton.Size = UDim2.new(0, 128, 0, 32)
--AttachButton.ZIndex = 2
--AttachButton.Font = Enum.Font.SourceSansSemibold
--AttachButton.Text = "ATTACH"
--AttachButton.TextColor3 = Color3.fromRGB(255, 255, 255)
--AttachButton.TextSize = 26.000

--UICorner_2.CornerRadius = UDim.new(0.125, 0)
--UICorner_2.Parent = AttachButton

LockButton.Name = "LockButton"
LockButton.Parent = Frame
LockButton.AnchorPoint = Vector2.new(0, 0.5)
LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
LockButton.BorderColor3 = Color3.fromRGB(191, 0, 0)
LockButton.BorderSizePixel = 0
LockButton.Position = UDim2.new(0, 10, 0.5, 0)
LockButton.Size = UDim2.new(0, 128, 0, 32)
LockButton.ZIndex = 2
LockButton.Font = Enum.Font.SourceSansSemibold
LockButton.Text = "LOCK ON [R]"
LockButton.TextColor3 = Color3.fromRGB(255, 255, 255)
LockButton.TextSize = 26.000

UICorner_3.CornerRadius = UDim.new(0.125, 0)
UICorner_3.Parent = LockButton

-- Personal Code --

local players = game:GetService("Players")
local p = players.LocalPlayer

local runService = game:GetService("RunService")
local uis = game:GetService("UserInputService")

local pdata = p:WaitForChild("PlayerData", 5)
local gdata = pdata:WaitForChild("GameData", 5)

local HerePart = nil
local ShootPart = nil
local Opponents = {}
local Teammates = {}
local Locked = false

function Flatten (v3)
	return Vector3.new(v3.X, 0, v3.Z)
end

function Lerp (n1, n2, a)
	return (n2-n1)*a + n1
end

function GetHerePart (court)
	if court:FindFirstChild("here") then
		return court["DunkHit"], court["DunkHit"]
	else
		local char = p.Character
		if not char then
			return nil
		end
		local head = p.Character:FindFirstChild("Head")
		if not head then
			return nil
		end

		local dunkHits = {}

		local allObjects = court:GetChildren ()
		for _, object in pairs (allObjects) do
			if object.Name == "DunkHit" then
				table.insert (dunkHits, object)
			end
		end

		local d1 = (Flatten(dunkHits[1].CFrame.p) - Flatten(head.CFrame.p)).Magnitude
		local d2 = (Flatten(dunkHits[2].CFrame.p) - Flatten(head.CFrame.p)).Magnitude

		if (d1 > d2) then
			return dunkHits[2], dunkHits[1]
		else
			return dunkHits[1], dunkHits[2]
		end
	end
end

local attachReady = true
function Attach ()
	if not attachReady then 
		return false
	end

	attachReady = false

	if not gdata["Playing"].Value or not gdata["Court"].Value or gdata["Team"].Value == "" then
		-- Cannnot attach
		warn ("DE follow - attach failed")
		warn (debug.traceback())
		attachReady = true

		--AttachButton.BackgroundColor3 = Color3.fromRGB(243, 32, 32)
		--AttachButton.Text = "ERROR"

		--wait (1)

		--AttachButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		--AttachButton.Text = "ATTACH"

		--attachReady = true
	else
		-- Can attach
		local court = workspace:FindFirstChild(gdata["Court"].Value)
		HerePart, ShootPart = GetHerePart(court)


		for _, player in pairs (players:GetPlayers()) do
			local pd = player:FindFirstChild("PlayerData")
			-- print("P: FOR PLAYER " .. player.Name)
			if pd then
				-- print("P: player data found")
				local gd = pd:FindFirstChild("GameData")
				if gd then
					-- print("P: game data found")
					if gd["Playing"].Value then
						--print("P: is playing")
						if gd["Court"].Value == gdata["Court"].Value then
							--print("P: FOR PLAYER " .. player.Name)
							--print("P: same court")
							if gd["Team"].Value ~= gdata["Team"].Value then
								--print("P: opposite teams")
								table.insert (Opponents, player)
							elseif gd["Team"].Value == gdata["Team"].Value then
								table.insert (Teammates, player)
							end
						end
					end
				end
			end
		end

		if #Opponents <= 0 then
			warn ("NASTY ERROR - SEARCH CODE 102")
			warn (debug.traceback())
		end

		repeat
			gdata["Playing"].Changed:Wait()
		until not gdata["Playing"].Value

		HerePart = nil
		ShootPart = nil
		Opponents = {}
		Teammates = {}

		attachReady = true

		LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		LockButton.Text = "LOCK ON [R]"

		Locked = false		
	end	
end

local lockReady = true
function LockOn ()
	if not lockReady then
		return nil
	end

	lockReady = false

	if not HerePart then
		LockButton.BackgroundColor3 = Color3.fromRGB(243, 32, 32)
		LockButton.Text = "NOT IN GAME"

		wait (1)

		LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		LockButton.Text = "LOCK ON [R]"

		lockReady = true
	else
		if not Locked then
			LockButton.BackgroundColor3 = Color3.fromRGB(32, 243, 48)
			LockButton.Text = "LOCKED"

			Locked = true

			lockReady = true
		else
			LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
			LockButton.Text = "LOCK ON [R]"

			Locked = false

			lockReady = true
		end
	end
end

LockButton.MouseButton1Click:Connect(LockOn)
uis.InputBegan:Connect(function(input, processed)
	if processed then
		return nil
	end

	if (input.KeyCode == Enum.KeyCode.R) then
		LockOn()
	end

	if (input.KeyCode == Enum.KeyCode.Space) then
		local char = p.Character
		if char then
			local h = char:FindFirstChild("Humanoid")
			if h then
				if h:GetState() == Enum.HumanoidStateType.Freefall then
					print("shoot fired")
					Shoot (char)
				end
			end
		end
	end
end)

local DATASET = {
	{60.61, 0.964},
	{66.83, 0.856},
	{50.36, 1.099},
	{33.88, 1.288},
	{26.92, 1.346},
	{57.56, 1.019},
	{67.5, 0.846},
	{63.54, 0.917},
	{61.23, 0.95},
	{53.82, 1.082},
	{6.87, 1.505},
	{47.12, 1.13},
	{44.57, 1.181},

	{64.46, 0.904},
	{73.44, 0.625},
	{71.5, 0.634},
	{69.5, 0.774},
	{68.5, 0.829},
	{66.93, 0.885},
}
table.sort(DATASET,function (a, b)
	return a[1] < b[1]
end)
for index, value in pairs (DATASET) do
	print (value[1], " : ", value[2])
end

function GetShotAngle (x)
	if x < DATASET[1][1] then
		return DATASET[1][2]
	elseif x > DATASET[#DATASET][1] then
		return DATASET[#DATASET][2]
	else
		for key, value in pairs (DATASET) do
			if value[1] > x then
				local last = DATASET[key - 1]
				local alpha = (x - last[1]) / (value[1] - last[1])

				return Lerp (last[2], value[2], alpha)
			end
		end
	end
end

function Shoot (char)
	if not ShootPart then
		print("no shootpart FAIL")
		return nil
	end

	local ball = char:FindFirstChild("Ball")
	if not ball then
		print("no ball FAIL")
		return nil
	end

	local head = char:FindFirstChild("Head")
	if not head then
		return nil
	end

	local player = players:GetPlayerFromCharacter(char)
	if (player ~= p) then
		print("wrong player FAIL")
		return nil
	end
	
	local gui = p["PlayerGui"]["PowerGuiPC"]["Frame"]["Power"]
	local thePower = gui.Text
	thePower = tonumber(thePower)

	local headPos = head.CFrame.p
	local shootVector = SafeUnit( Flatten (ShootPart.CFrame.p - headPos) )
	local randomY = 1 + math.random() * 0.02

	if thePower > 84 then
		local flatDistance = Flatten (ShootPart.CFrame.p - headPos).Magnitude
		local shotAngle = GetShotAngle (flatDistance)

		shootVector = Vector3.new(shootVector.X, math.tan(shotAngle), shootVector.Z)
	else
		shootVector = Vector3.new(shootVector.X, 0.668 + 0.002 * math.random(), shootVector.Z)
	end
	shootVector = SafeUnit (shootVector)
	shootVector = shootVector * (9987 + math.random()*4) 

	local shootEvent = ball["Events"]["Shoot"]
	shootEvent:FireServer(shootVector + headPos, headPos, thePower)
end

function OnCharacterAdded (c)
	print("locking")
	local humanoid = c:WaitForChild("Humanoid", 5)
	humanoid:GetPropertyChangedSignal ("WalkSpeed"):Connect(function ()
		if humanoid.WalkSpeed > 8 then
			Attach ()
		end
	end)
end
p.CharacterAdded:Connect(OnCharacterAdded)
if p.Character then
	OnCharacterAdded (p.Character)
end

function GetIdealMovementVector (netPos, targetPos, selfPos)
	netPos = Flatten(netPos)
	targetPos = Flatten(targetPos)
	selfPos = Flatten(selfPos)

	if (targetPos - netPos).Magnitude <= 1 then
		return Vector3.new()  -- Be safe in underneath-net condition
	end

	local lineDir = (targetPos - netPos)
	--targetPos = targetPos - SafeUnit(lineDir)/2
	--lineDir = (targetPos - netPos)
	local lineLength = lineDir.Magnitude

	local function GetTargetDistance (alpha)
		if alpha > 1 then
			alpha = 1
		elseif alpha < 0 then
			alpha = 0
		end

		local realDistance = (lineLength * (1 - alpha))
		return realDistance + 4

		--local posAlongLine = netPos + lineDir * alpha
		--return (targetPos - posAlongLine).Magnitude + 4, posAlongLine
	end

	local function GetSelfDistance (alpha)
		if alpha > 1 then
			alpha = 1
		elseif alpha < 0 then
			alpha = 0
		end

		local posAlongLine = netPos + lineDir * alpha
		return (selfPos - posAlongLine).Magnitude, posAlongLine
	end

	local function GetIdealPointAlongLine ()
		if GetTargetDistance (0) < GetSelfDistance (0) then
			-- print("instant return 1")
			return netPos  -- Save computation time by returning immediately
		elseif GetTargetDistance (1) > GetSelfDistance (1) then
			-- print ("instant return 2")
			local x, result = GetSelfDistance (1)  -- Same as above
			return result
		end

		for i = 10, 0, -1 do  -- 10 iterations max
			local d1 = GetTargetDistance (i / 10)
			local d2, pos = GetSelfDistance (i / 10)

			if (d2 < d1) then
				-- We know our solution is in between i and i + 0.1
				for j = 10, 0, -1 do
					d1 = GetTargetDistance (i / 10 + (j / 100))
					d2, pos = GetSelfDistance (i / 10 + (j / 100))

					if (d2 < d1) then
						for k = 10, 0, -1 do
							d1 = GetTargetDistance (i / 10 + (j / 100) + (k / 1000))
							d2, pos = GetSelfDistance (i / 10 + (j / 100) + (k / 1000))

							if (d2 < d1) then
								return pos
							end
						end

						break
					end
				end

				break
			end
		end

		warn("Bad return")
		warn(debug.traceback())
		return netPos  -- Redundant return (but we will leave it for safety)
	end

	local bestPosition = GetIdealPointAlongLine ()
	local movementVector = bestPosition - selfPos

	if (movementVector).Magnitude < (1/8) then
		return Vector3.new()
	end

	return movementVector	
end

--function CalculateNearestAngle (s, e, third)
--	local dir = (e - s)
--	if (dir.Magnitude < 1/1028) then
--		error ("'s' and 'e' cannot be identical")
--	end
--	dir = Flatten (dir)

--	s = Flatten (s)
--	e = Flatten (e)
--	third = Flatten (third)

--	local t = (third - s):Dot(dir) / dir:Dot(dir)

--	if t <= 0 then
--		return -(third - s)
--	elseif t >= 1 then
--		return -(third - e)
--	else
--		return -(third - (s + t * dir))
--	end
--end

--function CalculateBestAngle (netPos, targetPos, selfPos)
--	-- Get the vector direction from third to nearest point
--	netPos = Flatten (netPos)
--	targetPos = Flatten (targetPos)
--	selfPos = Flatten (selfPos)

--	local shortestVector = CalculateNearestAngle (netPos, targetPos, selfPos)

--	local lineDir = (targetPos - netPos)
--	local m = shortestVector.Magnitude

--	local goalPosition = selfPos + 





--	netPos = Flatten ()s = Flatten (s)
--	e = Flatten (e)
--	third = Flatten (third)

--	local dir = (e - s)
--	dir = Flatten (dir)

--	local m = nearest.Magnitude

--	if distance > m then
--		local addDistance = math.sqrt(distance^2 - m^2)
--		local r = nearest + addDistance * dir.Unit

--		if ((r + third) - s).Magnitude > dir.Magnitude then
--			print("CLOSE CONDITION")
--			return (e - third)
--		end

--		return r
--	else
--		return nearest
--	end
--end

function SafeUnit (v)
	if (v.Magnitude > 1/64) then
		return v.Unit
	else
		return Vector3.new()
	end
end

local target = {nil, 0}
local liveTarget = false

runService:BindToRenderStep("move",
	-- run after the character
	Enum.RenderPriority.Character.Value + 2,

	function()
		if not Locked then
			-- print("Not locked")
			target = {nil, 0}
			return nil
		else
			-- print("Locked")
		end

		for _, player in pairs (Opponents) do
			local char = player.Character
			if char then
				local ball = char:FindFirstChild("Ball")
				if ball then 
					target = {char, -1}
				end
			end
		end

		target[2] = target[2] + 1  --> So, a new target will have target[2] = 0. Else, target[2] > 0

		-- print("Opponents:", unpack(Opponents))

		--if not target then
		--	-- print("No target")
		--	return nil
		--else
		--	-- print("Target")
		--end

		if p.Character and p.Character:FindFirstChild("Humanoid") then
			local humanoid = p.Character["Humanoid"]
			-- Now we can start looking for a target
			if target[1] ~= nil and target[2] <= 0 then
				-- Assume normal on ball defense
				local head = target[1]["Head"]
				local bestAngle = GetIdealMovementVector(
					HerePart.CFrame.p, 
					head.CFrame.p + SafeUnit(head.Velocity) * 4 + head.CFrame.lookVector * 2, 
					p.Character["Head"].CFrame.p
				)
				humanoid:Move(SafeUnit(bestAngle), false)
			elseif target[1] ~= nil and target[2] < 60 then
				local teamHasBall = false
				for _, mate in pairs (Teammates) do
					if mate.Character and mate.Character:FindFirstChild("Ball") then
						teamHasBall = true
					end
				end

				if teamHasBall then
					--print("team on ball!")
					return nil
				end

				-- print("toss up defense!")

				-- Assume toss-up on ball defense
				local head = target[1]["Head"]
				local bestAngle = GetIdealMovementVector(
					HerePart.CFrame.p,
					head.CFrame.p - SafeUnit(head.CFrame.p - HerePart.CFrame.p) * 2,
					p.Character["Head"].CFrame.p
				)
				humanoid:Move(SafeUnit(bestAngle), false)
			end
		end

		--local lineDir = (targetPos - netPos)
		--targetPos = targetPos - SafeUnit(lineDir)/2

		--if p.Character then
		--	local humanoid = p.Character:FindFirstChild("Humanoid")
		--	if humanoid then
		--		local head = target["Head"]
		--		local bestAngle = GetIdealMovementVector(
		--			HerePart.CFrame.p, 
		--			head.CFrame.p + SafeUnit(head.Velocity) * 2 + head.CFrame.lookVector * 2, 
		--			p.Character["Head"].CFrame.p
		--		)
		--		humanoid:Move(SafeUnit(bestAngle), false)
		--	end
		--end
		end
)
end)


ScriptsSection:NewButton("Auto guard", "Click r ", function()
    -- Gui to Lua
-- Version: 3.2

-- Instances:

local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local AttachButton = Instance.new("TextButton")
local UICorner_2 = Instance.new("UICorner")
local LockButton = Instance.new("TextButton")
local UICorner_3 = Instance.new("UICorner")

--Properties:

ScreenGui.Parent = game:GetService("RunService"):IsStudio() and game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui") or game:GetService("CoreGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Name = "FollowGui"



UICorner.CornerRadius = UDim.new(0.125, 0)
UICorner.Parent = Frame

--AttachButton.Name = "AttachButton"
--AttachButton.Parent = Frame
--AttachButton.AnchorPoint = Vector2.new(1, 0.5)
--AttachButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
--AttachButton.BorderColor3 = Color3.fromRGB(191, 0, 0)
--AttachButton.BorderSizePixel = 0
--AttachButton.Position = UDim2.new(0.5, -10, 0.5, 0)
--AttachButton.Size = UDim2.new(0, 128, 0, 32)
--AttachButton.ZIndex = 2
--AttachButton.Font = Enum.Font.SourceSansSemibold
--AttachButton.Text = "ATTACH"
--AttachButton.TextColor3 = Color3.fromRGB(255, 255, 255)
--AttachButton.TextSize = 26.000

--UICorner_2.CornerRadius = UDim.new(0.125, 0)
--UICorner_2.Parent = AttachButton

LockButton.Name = "LockButton"
LockButton.Parent = Frame
LockButton.AnchorPoint = Vector2.new(0, 0.5)
LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
LockButton.BorderColor3 = Color3.fromRGB(191, 0, 0)
LockButton.BorderSizePixel = 0
LockButton.Position = UDim2.new(0, 10, 0.5, 0)
LockButton.Size = UDim2.new(0, 128, 0, 32)
LockButton.ZIndex = 2
LockButton.Font = Enum.Font.SourceSansSemibold
LockButton.Text = "LOCK ON [R]"
LockButton.TextColor3 = Color3.fromRGB(255, 255, 255)
LockButton.TextSize = 26.000

UICorner_3.CornerRadius = UDim.new(0.125, 0)
UICorner_3.Parent = LockButton

-- Personal Code --

local players = game:GetService("Players")
local p = players.LocalPlayer

local runService = game:GetService("RunService")
local uis = game:GetService("UserInputService")

local pdata = p:WaitForChild("PlayerData", 5)
local gdata = pdata:WaitForChild("GameData", 5)

local HerePart = nil
local ShootPart = nil
local Opponents = {}
local Teammates = {}
local Locked = false

function Flatten (v3)
	return Vector3.new(v3.X, 0, v3.Z)
end

function Lerp (n1, n2, a)
	return (n2-n1)*a + n1
end

function GetHerePart (court)
	if court:FindFirstChild("here") then
		return court["DunkHit"], court["DunkHit"]
	else
		local char = p.Character
		if not char then
			return nil
		end
		local head = p.Character:FindFirstChild("Head")
		if not head then
			return nil
		end

		local dunkHits = {}

		local allObjects = court:GetChildren ()
		for _, object in pairs (allObjects) do
			if object.Name == "DunkHit" then
				table.insert (dunkHits, object)
			end
		end

		local d1 = (Flatten(dunkHits[1].CFrame.p) - Flatten(head.CFrame.p)).Magnitude
		local d2 = (Flatten(dunkHits[2].CFrame.p) - Flatten(head.CFrame.p)).Magnitude

		if (d1 > d2) then
			return dunkHits[2], dunkHits[1]
		else
			return dunkHits[1], dunkHits[2]
		end
	end
end

local attachReady = true
function Attach ()
	if not attachReady then 
		return false
	end

	attachReady = false

	if not gdata["Playing"].Value or not gdata["Court"].Value or gdata["Team"].Value == "" then
		-- Cannnot attach
		warn ("DE follow - attach failed")
		warn (debug.traceback())
		attachReady = true

		--AttachButton.BackgroundColor3 = Color3.fromRGB(243, 32, 32)
		--AttachButton.Text = "ERROR"

		--wait (1)

		--AttachButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		--AttachButton.Text = "ATTACH"

		--attachReady = true
	else
		-- Can attach
		local court = workspace:FindFirstChild(gdata["Court"].Value)
		HerePart, ShootPart = GetHerePart(court)


		for _, player in pairs (players:GetPlayers()) do
			local pd = player:FindFirstChild("PlayerData")
			-- print("P: FOR PLAYER " .. player.Name)
			if pd then
				-- print("P: player data found")
				local gd = pd:FindFirstChild("GameData")
				if gd then
					-- print("P: game data found")
					if gd["Playing"].Value then
						--print("P: is playing")
						if gd["Court"].Value == gdata["Court"].Value then
							--print("P: FOR PLAYER " .. player.Name)
							--print("P: same court")
							if gd["Team"].Value ~= gdata["Team"].Value then
								--print("P: opposite teams")
								table.insert (Opponents, player)
							elseif gd["Team"].Value == gdata["Team"].Value then
								table.insert (Teammates, player)
							end
						end
					end
				end
			end
		end

		if #Opponents <= 0 then
			warn ("NASTY ERROR - SEARCH CODE 102")
			warn (debug.traceback())
		end

		repeat
			gdata["Playing"].Changed:Wait()
		until not gdata["Playing"].Value

		HerePart = nil
		ShootPart = nil
		Opponents = {}
		Teammates = {}

		attachReady = true

		LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		LockButton.Text = "LOCK ON [R]"

		Locked = false		
	end	
end

local lockReady = true
function LockOn ()
	if not lockReady then
		return nil
	end

	lockReady = false

	if not HerePart then
		LockButton.BackgroundColor3 = Color3.fromRGB(243, 32, 32)
		LockButton.Text = "NOT IN GAME"

		wait (1)

		LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
		LockButton.Text = "LOCK ON [R]"

		lockReady = true
	else
		if not Locked then
			LockButton.BackgroundColor3 = Color3.fromRGB(32, 243, 48)
			LockButton.Text = "LOCKED"

			Locked = true

			lockReady = true
		else
			LockButton.BackgroundColor3 = Color3.fromRGB(33, 150, 243)
			LockButton.Text = "LOCK ON [R]"

			Locked = false

			lockReady = true
		end
	end
end

LockButton.MouseButton1Click:Connect(LockOn)
uis.InputBegan:Connect(function(input, processed)
	if processed then
		return nil
	end

	if (input.KeyCode == Enum.KeyCode.R) then
		LockOn()
	end

	if (input.KeyCode == Enum.KeyCode.Space) then
		local char = p.Character
		if char then
			local h = char:FindFirstChild("Humanoid")
			if h then
				if h:GetState() == Enum.HumanoidStateType.Freefall then
					print("shoot fired")
					Shoot (char)
				end
			end
		end
	end
end)

local DATASET = {
	{60.61, 0.964},
	{66.83, 0.856},
	{50.36, 1.099},
	{33.88, 1.288},
	{26.92, 1.346},
	{57.56, 1.019},
	{67.5, 0.846},
	{63.54, 0.917},
	{61.23, 0.95},
	{53.82, 1.082},
	{6.87, 1.505},
	{47.12, 1.13},
	{44.57, 1.181},

	{64.46, 0.904},
	{73.44, 0.625},
	{71.5, 0.634},
	{69.5, 0.774},
	{68.5, 0.829},
	{66.93, 0.885},
}
table.sort(DATASET,function (a, b)
	return a[1] < b[1]
end)
for index, value in pairs (DATASET) do
	print (value[1], " : ", value[2])
end

function GetShotAngle (x)
	if x < DATASET[1][1] then
		return DATASET[1][2]
	elseif x > DATASET[#DATASET][1] then
		return DATASET[#DATASET][2]
	else
		for key, value in pairs (DATASET) do
			if value[1] > x then
				local last = DATASET[key - 1]
				local alpha = (x - last[1]) / (value[1] - last[1])

				return Lerp (last[2], value[2], alpha)
			end
		end
	end
end

function Shoot (char)
	if not ShootPart then
		print("no shootpart FAIL")
		return nil
	end

	local ball = char:FindFirstChild("Ball")
	if not ball then
		print("no ball FAIL")
		return nil
	end

	local head = char:FindFirstChild("Head")
	if not head then
		return nil
	end

	local player = players:GetPlayerFromCharacter(char)
	if (player ~= p) then
		print("wrong player FAIL")
		return nil
	end
	
	local gui = p["PlayerGui"]["PowerGuiPC"]["Frame"]["Power"]
	local thePower = gui.Text
	thePower = tonumber(thePower)

	local headPos = head.CFrame.p
	local shootVector = SafeUnit( Flatten (ShootPart.CFrame.p - headPos) )
	local randomY = 1 + math.random() * 0.02

	if thePower > 84 then
		local flatDistance = Flatten (ShootPart.CFrame.p - headPos).Magnitude
		local shotAngle = GetShotAngle (flatDistance)

		shootVector = Vector3.new(shootVector.X, math.tan(shotAngle), shootVector.Z)
	else
		shootVector = Vector3.new(shootVector.X, 0.668 + 0.002 * math.random(), shootVector.Z)
	end
	shootVector = SafeUnit (shootVector)
	shootVector = shootVector * (9987 + math.random()*4) 

	local shootEvent = ball["Events"]["Shoot"]
	shootEvent:FireServer(shootVector + headPos, headPos, thePower)
end

function OnCharacterAdded (c)
	print("locking")
	local humanoid = c:WaitForChild("Humanoid", 5)
	humanoid:GetPropertyChangedSignal ("WalkSpeed"):Connect(function ()
		if humanoid.WalkSpeed > 8 then
			Attach ()
		end
	end)
end
p.CharacterAdded:Connect(OnCharacterAdded)
if p.Character then
	OnCharacterAdded (p.Character)
end

function GetIdealMovementVector (netPos, targetPos, selfPos)
	netPos = Flatten(netPos)
	targetPos = Flatten(targetPos)
	selfPos = Flatten(selfPos)

	if (targetPos - netPos).Magnitude <= 1 then
		return Vector3.new()  -- Be safe in underneath-net condition
	end

	local lineDir = (targetPos - netPos)
	--targetPos = targetPos - SafeUnit(lineDir)/2
	--lineDir = (targetPos - netPos)
	local lineLength = lineDir.Magnitude

	local function GetTargetDistance (alpha)
		if alpha > 1 then
			alpha = 1
		elseif alpha < 0 then
			alpha = 0
		end

		local realDistance = (lineLength * (1 - alpha))
		return realDistance + 4

		--local posAlongLine = netPos + lineDir * alpha
		--return (targetPos - posAlongLine).Magnitude + 4, posAlongLine
	end

	local function GetSelfDistance (alpha)
		if alpha > 1 then
			alpha = 1
		elseif alpha < 0 then
			alpha = 0
		end

		local posAlongLine = netPos + lineDir * alpha
		return (selfPos - posAlongLine).Magnitude, posAlongLine
	end

	local function GetIdealPointAlongLine ()
		if GetTargetDistance (0) < GetSelfDistance (0) then
			-- print("instant return 1")
			return netPos  -- Save computation time by returning immediately
		elseif GetTargetDistance (1) > GetSelfDistance (1) then
			-- print ("instant return 2")
			local x, result = GetSelfDistance (1)  -- Same as above
			return result
		end

		for i = 10, 0, -1 do  -- 10 iterations max
			local d1 = GetTargetDistance (i / 10)
			local d2, pos = GetSelfDistance (i / 10)

			if (d2 < d1) then
				-- We know our solution is in between i and i + 0.1
				for j = 10, 0, -1 do
					d1 = GetTargetDistance (i / 10 + (j / 100))
					d2, pos = GetSelfDistance (i / 10 + (j / 100))

					if (d2 < d1) then
						for k = 10, 0, -1 do
							d1 = GetTargetDistance (i / 10 + (j / 100) + (k / 1000))
							d2, pos = GetSelfDistance (i / 10 + (j / 100) + (k / 1000))

							if (d2 < d1) then
								return pos
							end
						end

						break
					end
				end

				break
			end
		end

		warn("Bad return")
		warn(debug.traceback())
		return netPos  -- Redundant return (but we will leave it for safety)
	end

	local bestPosition = GetIdealPointAlongLine ()
	local movementVector = bestPosition - selfPos

	if (movementVector).Magnitude < (1/8) then
		return Vector3.new()
	end

	return movementVector	
end

--function CalculateNearestAngle (s, e, third)
--	local dir = (e - s)
--	if (dir.Magnitude < 1/1028) then
--		error ("'s' and 'e' cannot be identical")
--	end
--	dir = Flatten (dir)

--	s = Flatten (s)
--	e = Flatten (e)
--	third = Flatten (third)

--	local t = (third - s):Dot(dir) / dir:Dot(dir)

--	if t <= 0 then
--		return -(third - s)
--	elseif t >= 1 then
--		return -(third - e)
--	else
--		return -(third - (s + t * dir))
--	end
--end

--function CalculateBestAngle (netPos, targetPos, selfPos)
--	-- Get the vector direction from third to nearest point
--	netPos = Flatten (netPos)
--	targetPos = Flatten (targetPos)
--	selfPos = Flatten (selfPos)

--	local shortestVector = CalculateNearestAngle (netPos, targetPos, selfPos)

--	local lineDir = (targetPos - netPos)
--	local m = shortestVector.Magnitude

--	local goalPosition = selfPos + 





--	netPos = Flatten ()s = Flatten (s)
--	e = Flatten (e)
--	third = Flatten (third)

--	local dir = (e - s)
--	dir = Flatten (dir)

--	local m = nearest.Magnitude

--	if distance > m then
--		local addDistance = math.sqrt(distance^2 - m^2)
--		local r = nearest + addDistance * dir.Unit

--		if ((r + third) - s).Magnitude > dir.Magnitude then
--			print("CLOSE CONDITION")
--			return (e - third)
--		end

--		return r
--	else
--		return nearest
--	end
--end

function SafeUnit (v)
	if (v.Magnitude > 1/64) then
		return v.Unit
	else
		return Vector3.new()
	end
end

local target = {nil, 0}
local liveTarget = false

runService:BindToRenderStep("move",
	-- run after the character
	Enum.RenderPriority.Character.Value + 2,

	function()
		if not Locked then
			-- print("Not locked")
			target = {nil, 0}
			return nil
		else
			-- print("Locked")
		end

		for _, player in pairs (Opponents) do
			local char = player.Character
			if char then
				local ball = char:FindFirstChild("Ball")
				if ball then 
					target = {char, -1}
				end
			end
		end

		target[2] = target[2] + 1  --> So, a new target will have target[2] = 0. Else, target[2] > 0

		-- print("Opponents:", unpack(Opponents))

		--if not target then
		--	-- print("No target")
		--	return nil
		--else
		--	-- print("Target")
		--end

		if p.Character and p.Character:FindFirstChild("Humanoid") then
			local humanoid = p.Character["Humanoid"]
			-- Now we can start looking for a target
			if target[1] ~= nil and target[2] <= 0 then
				-- Assume normal on ball defense
				local head = target[1]["Head"]
				local bestAngle = GetIdealMovementVector(
					HerePart.CFrame.p, 
					head.CFrame.p + SafeUnit(head.Velocity) * 4 + head.CFrame.lookVector * 2, 
					p.Character["Head"].CFrame.p
				)
				humanoid:Move(SafeUnit(bestAngle), false)
			elseif target[1] ~= nil and target[2] < 60 then
				local teamHasBall = false
				for _, mate in pairs (Teammates) do
					if mate.Character and mate.Character:FindFirstChild("Ball") then
						teamHasBall = true
					end
				end

				if teamHasBall then
					--print("team on ball!")
					return nil
				end

				-- print("toss up defense!")

				-- Assume toss-up on ball defense
				local head = target[1]["Head"]
				local bestAngle = GetIdealMovementVector(
					HerePart.CFrame.p,
					head.CFrame.p - SafeUnit(head.CFrame.p - HerePart.CFrame.p) * 2,
					p.Character["Head"].CFrame.p
				)
				humanoid:Move(SafeUnit(bestAngle), false)
			end
		end

		--local lineDir = (targetPos - netPos)
		--targetPos = targetPos - SafeUnit(lineDir)/2

		--if p.Character then
		--	local humanoid = p.Character:FindFirstChild("Humanoid")
		--	if humanoid then
		--		local head = target["Head"]
		--		local bestAngle = GetIdealMovementVector(
		--			HerePart.CFrame.p, 
		--			head.CFrame.p + SafeUnit(head.Velocity) * 2 + head.CFrame.lookVector * 2, 
		--			p.Character["Head"].CFrame.p
		--		)
		--		humanoid:Move(SafeUnit(bestAngle), false)
		--	end
		--end
		end
)
end)


ScriptsSection:NewButton("No steal", "Click y to enable", function()
    game:GetService("UserInputService").InputBegan:Connect(function(key, gpe)
   if not gpe and key.KeyCode == Enum.KeyCode.Y then
       for i,v in pairs(plr.Character.Ball:GetChildren()) do
           local new = Instance.new("Folder")
           new.Parent = plr.Character.Ball
           new.Name = "TouchInterest"

           plr.Character.Ball:FindFirstChildOfClass("TouchTransmitter"):Destroy()
       end
   end
end)
end)

ScriptsSection:NewButton("Slient walkspeed", "Click z to enable and disable", function()
   local SPEED = 21

local p = game:GetService("Players").LocalPlayer
local uis = game:GetService("UserInputService")
local runService = game:GetService("RunService")

local speedy = false

uis.InputBegan:Connect(function(input, processed)
    if processed then
        return nil
    end

    if (input.KeyCode == Enum.KeyCode.Z) then
        local char = p.Character
        if not char then
            print("expected character, got nil")
            return nil
        end

        local humanoid = char:FindFirstChild("Humanoid")
        if not humanoid then
            print("expected Humanoid, got nil")
            return nil
        end

        local rp = char:FindFirstChild("HumanoidRootPart")
        if not rp then
            print("expected RootPart, got nil")
            return nil
        end

        if not speedy then
            runService:BindToRenderStep ("SpeedyVick", 295, function (deltaTime)
                local state = humanoid:GetState()

                if (state == Enum.HumanoidStateType.Running) or (state == Enum.HumanoidStateType.RunningNoPhysics) or (state == Enum.HumanoidStateType.Freefall) then
                    local dir = humanoid.MoveDirection
                    rp.CFrame = rp.CFrame + dir * (SPEED-20) * deltaTime
                end
            end)

            print("speed on")
            speedy = true
        else
            runService:UnbindFromRenderStep("SpeedyVick")

            print("speed off")
            speedy = false
        end
    end
end)

print("speedyvick")
end)

ScriptsSection:NewButton("Shift tap", "Shift tap to speed", function()
    local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local Noclip = Instance.new("TextButton")
local Speedtapshift = Instance.new("TextButton")
local Fefling = Instance.new("TextButton")
local Aimlock = Instance.new("TextButton")
local TextLabel = Instance.new("TextLabel")
 
--Properties:
 
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
 
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Position = UDim2.new(0.616788387, 0, 0.502645493, 0)
Frame.Size = UDim2.new(0, 361, 0, 263)
 
Noclip.Name = "Noclip"
Noclip.Parent = Frame
Noclip.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Noclip.Position = UDim2.new(0, 0, 0.30038023, 0)
Noclip.Size = UDim2.new(0, 179, 0, 50)
Noclip.Font = Enum.Font.SourceSans
Noclip.Text = "Noclip"
Noclip.TextColor3 = Color3.fromRGB(0, 0, 0)
Noclip.TextSize = 14.000
Noclip.MouseButton1Down:connect(function()
	noclip = false
	game:GetService('RunService').Stepped:connect(function()
		if noclip then
			game.Players.LocalPlayer.Character.Humanoid:ChangeState(11)
		end
	end)
	plr = game.Players.LocalPlayer
	mouse = plr:GetMouse()
	mouse.KeyDown:connect(function(key)
 
		if key == "e" then
			noclip = not noclip
			game.Players.LocalPlayer.Character.Humanoid:ChangeState(11)
		end
	end)
	print('Loaded')
	print('Press "E" to noclip')
end)
 
Speedtapshift.Name = "Speed (tap shift)"
Speedtapshift.Parent = Frame
Speedtapshift.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Speedtapshift.Position = UDim2.new(0, 0, 0.646387815, 0)
Speedtapshift.Size = UDim2.new(0, 179, 0, 50)
Speedtapshift.Font = Enum.Font.SourceSans
Speedtapshift.Text = "Speed tap/hold shift)"
Speedtapshift.TextColor3 = Color3.fromRGB(0, 0, 0)
Speedtapshift.TextSize = 14.000
Speedtapshift.MouseButton1Down:connect(function()
	local Players = game:service('Players')
	local Player = Players.LocalPlayer
 
	local userInput = game:service('UserInputService')
	local runService = game:service('RunService')
 
	repeat wait() until Player.Character
 
	local Character = Player.Character
	local pHum = Character:WaitForChild('Humanoid')
	local humRoot = Character:WaitForChild('HumanoidRootPart')
 
	local Multiplier = 1.4
 
	userInput.InputBegan:connect(function(Key)
		if Key.KeyCode == Enum.KeyCode.LeftBracket then
			Multiplier = Multiplier + 0.1
			print(Multiplier)
			wait(0.2)
			while userInput:IsKeyDown(Enum.KeyCode.LeftBracket) do
				wait()
				Multiplier = Multiplier + 0.1
				print(Multiplier)
			end
		end
 
		if Key.KeyCode == Enum.KeyCode.RightBracket then
			Multiplier = Multiplier - 0.1
			print(Multiplier)
			wait(0.2)
			while userInput:IsKeyDown(Enum.KeyCode.RightBracket) do
				wait()
				Multiplier = Multiplier - 0.1
				print(Multiplier)
			end
		end
	end)
 
	runService.Stepped:connect(function()
		if userInput:IsKeyDown(Enum.KeyCode.LeftShift) then
			humRoot.CFrame = humRoot.CFrame + pHum.MoveDirection * Multiplier
		end
	end)
end)
 
Fefling.Name = "Fe fling"
Fefling.Parent = Frame
Fefling.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Fefling.Position = UDim2.new(0.55401665, 0, 0.30038023, 0)
Fefling.Size = UDim2.new(0, 161, 0, 50)
Fefling.Font = Enum.Font.SourceSans
Fefling.Text = "Fling (requires 3 hats)"
Fefling.TextColor3 = Color3.fromRGB(0, 0, 0)
Fefling.TextSize = 14.000
Fefling.MouseButton1Down:connect(function()
	spawn(function()
		while true do game:GetService("RunService").Heartbeat:wait()
			for i,v in pairs(game.Players:GetPlayers()) do
				if v == game.Players.LocalPlayer == false then
					game.Players.LocalPlayer.MaximumSimulationRadius = math.pow(math.huge,math.huge)*math.huge
					game.Players.LocalPlayer.SimulationRadius = math.pow(math.huge,math.huge)*math.huge
					v.MaximumSimulationRadius = 0
					v.SimulationRadius = 0
					game:GetService("RunService").Stepped:wait()
				end
			end
		end
	end)
 
	local Player = game:GetService("Players").LocalPlayer
	local Players = game:GetService("Players")
	local HatList = {}
	local i = 0
	for _,l in pairs(Player.Character:GetChildren()) do
		if l:IsA("Accessory") then if i>0 then l.Parent = workspace  end i = i + 1 end;
	end
	wait(.1)
 
 
 
	for _,v in pairs(workspace:GetDescendants()) do
		if v.Name == "Handle" and v:IsA("BasePart") and v.Parent:IsA("Accessory") and v:IsDescendantOf(Player.Character)==false and Players:GetPlayerFromCharacter(v.Parent.Parent)==nil then
			table.insert(HatList,v)
		end
	end
 
	for _,hat in pairs(HatList) do
		hat.Parent = workspace
		hat.Position = Player.Character.HumanoidRootPart.Position + Player.Character.HumanoidRootPart.CFrame.lookVector * 5
		local BodyPos = Instance.new("BodyPosition",hat)
		local BodyAng = Instance.new("BodyAngularVelocity",hat)
		BodyAng.AngularVelocity = Vector3.new(0,9e12,0)
		BodyAng.P = 9e12
		BodyPos.MaxForce = Vector3.new(9e9,9e9,9e9)
		BodyPos.P = 9e8
		spawn(function()
			while wait() do
				if pcall(function()
						hat.CanCollide = false
						BodyPos.Position = Player.Character.HumanoidRootPart.Position + Vector3.new(math.random(-2,2),math.random(-2,2),math.random(-2,2))
					end) then
				else
					BodyPos:Destroy()
					hat.CanCollide = true
				end
			end
		end)
 
	end
end)
 
Aimlock.Name = "Aimlock"
Aimlock.Parent = Frame
Aimlock.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Aimlock.Position = UDim2.new(0.55401665, 0, 0.646387815, 0)
Aimlock.Size = UDim2.new(0, 161, 0, 50)
Aimlock.Font = Enum.Font.SourceSans
Aimlock.Text = "Aimbot"
Aimlock.TextColor3 = Color3.fromRGB(0, 0, 0)
Aimlock.TextSize = 14.000
Aimlock.MouseButton1Down:connect(function()
	--[[
IronBrew:tm: obfuscation; Version 2.7.2
]]
	return(function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIllIIlIlIlIlllIIIIlll)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll=string.char;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=string.sub;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI=table.concat;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI=math.ldexp;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIllIIIIIIII=getfenv or function()return _ENV end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl=select;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=unpack or table.unpack;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=tonumber;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll="","",{}local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=256;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll={}for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=0,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI-1 do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI),36)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl-1),36)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll())localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;while localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI<#localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII do local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll()if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl..localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,1,1)end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl..localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,1,1)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[#localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1],localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI+1 end;return table.concat(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll)end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII('24324927524B24027524926M26U26J26Q26I26H26W26Y27024B24427926226K26J26J27026V26L26226W26S27026J26W24B24E27925T26U26T27126O26V27227727927328727126626U26W26T26I24B24D27927227V27J27427526627026L25M27X26N26O27I24B23T27925O26I27X25S26V26H26K28U28W28Y28P27929629826L26327028N26V28027926Y26U26V26V27026Y26L24B28Q24929F29926426V27127027123L2792482A627924B2A927927827524A27923P27923O2AI27923R2AL2AN2752AM27523Q2792AS27524X2792AW2AV2AT27924Z2B12792532B32752B52752A527523V2552A82752552AF2792492AA2492AJ2A72BI23V23U2492A72BH2BJ25A2BR2BI2492BP2BW28L2762BV2A72BN2BY23W2BJ2492C12BI2AA2C42BY27624924C2792BD2C82AA24F2CE2C92492812BX2AA28L2AA2CJ2AF2AA2412492CV2492AF27824F24Y2CG2C82A72BK2AD2BW2AC2BX2792422D12D62A72BG2DG2AA2742552AH2AF2D22BU2D62BT2492DF2AF2CH2BN24H2BE2CO2752CM2762CH27527G26O26J28J2DA27B27D27F27H27J24727928S26L26527026I27I2A126W27S28J2CA25Z28O28K27925P26J26O2892E324925L26W27Q27S2462792CA2FA2F92FC2752CA2E52D62DD2FG2F22F22CA2CQ2492DA2F82BB24L2E023V2752D82CI2FV2BW2DT2FW2DD2AB2CA2492452BW2DJ2CH28L28L25B24G2D62742A72G92FQ2492GF2C92GI2G62A72BC2D62BK24923T24I2CO2CM2CH24F2AQ2492CM2FW24D2GI2H12752DZ2FY2G22FN24B2FG2E72E928A2752EC27E27G28Z2DF28R28T26226P26O2852F62EW2752EU27W2HT2492EY2F024B2CY27525L26U26I26O26L26O29P2HF24926S26W27226V2I726K2A22AJ27527L2IL2792IM2492IP2IR2792EH2752IU2BX2IW2GI2BI2GI2912752J22GS27923S2J62792FU2752BQ27523X27923Z27923Y2JH2JJ2IN27523K27923M2AG2AK2FR2CO2C52JA2CH2GR2CJ2CH2CH2DT2GX2DD2CA2CM2G42JN2DI2DQ2CY2GB2GD2782GK2GH2792GK2H32792K72H02JI2A727L2CD2KI2IN2A72KQ2492C72K82FO2492CY2E32B22DA2782DJ2DQ28124F2FU2AF2KK2CS2GI2KK2D72KY2CB2C82BT2KT2LD2CC2LF2BF2K92H02752L12DC2DJ2AA2782L62AM28L2H52LB2792H52A82GR2492H72DB2BX2482JC2BJ2FN25U27026826226U2A22HX2A026K26S24B2C525D2HX28N2HW2FN25L26T26W26827X28J2HL24925X26U26Y28H2MU2MW27X2IB2HO2F527H26L2N82FG25P27C26I26U2I12792I42I62I82IA2FN2832852872892CA26M26W2I724926A23X21J26623C26B1T24M24B2F226226727Y2HW2BT29R26M2MN27926H29M27525R29S26L27C24Q2G22OR1P2642612A72D52BF2G22CJ2CZ2FX2OY2P32P12DD2P02542792P82752PA2492PC2PE2P92PG2752572792PJ2752592792PN2PM2792582PR2PT2752PS2PV2PU2492PW2PZ2PY2Q02Q32Q22Q527525B2792Q82Q72AX2792OX2H02KO2DG2C52KT2AF2KS2GI2B22KW2GR2GO2BD2CW2CI2DP2QH2CZ2QU2QU24F2JG2DL2FZ2GL2KG2C02QM2DB23N2C82BO2PN2AF2CA2D02DW2P52QY2RG2DG2812RE2LF2RL2DA2CK2R92AE2M72JA2AF29W2GP2AF2N023V2GA2G52DD2QS2C52QV2BB2FE2IQ2P22S02IW2DH2P22JZ2S82JY2D62F82CJ2CM2LU2CN2812J42CJ2812812EH2JA2CY2J72S62KX2JA2CN2742SV2BI2812742M42CM2CM2FN2GV2C82J22AF27L28L2RT2GJ2Q92RR2JE2A72BQ2KT2TJ2H62E02CB2CA2OK2492MD2MF2MH27J2CA2MK2MM2MO2TS2NR2862882G62752QE2U82QD2UA2U72UB2BX2QE2512792UG2B72792U62KL2BW2DF2KP2GI2UO2QW2RQ2QQ2DK2QT2DG2QK2US2QX2H02R02KX2BS2R32752782QL2DE2DB2AJ2M42BY2BQ2CS2BI2M62S42AB');local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=(bit or bit32);local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI and localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI.bxor or function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=1,0,10 while localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI>0 and localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl>0 do local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI%2,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl%2 if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII~=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll end localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII)/2,(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll)/2,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll*2 end if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI<localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl end while localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI>0 do local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI%2 if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl>0 then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll end localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)/2,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll*2 end return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll end local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll)if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl/2^(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1))%2^((localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll-1)-(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1)+1);return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI%1;else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=2^(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1);return(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl%(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)>=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)and 1 or 0;end;end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+3);localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+4;return(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl*16777216)+(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll*65536)+(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll*256)+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII;end;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI),153);localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1;return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;end;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+2);localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,153)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+2;return(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll*256)+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;end;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIIlIIIIIlIIlllll()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl();local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl();local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,1,20)*(2^32))+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,21,31);local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=((-1)^localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,32));if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==0)then if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==0)then return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI*0;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=0;end;elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==2047)then return(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==0)and(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI*(1/0))or(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI*(0/0));end;return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl-1023)*(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII+(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll/(2^52)));end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll;if(not localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI();if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==0)then return'';end;end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl-1);localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl={}for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1,#localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)),153))end return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl);end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI(...)return{...},localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl('#',...)end local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII={};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII={};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII={};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll={[#{"1 + 1 = 111";"1 + 1 = 111";}]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,[#{{933;976;158;25};{857;756;476;312};"1 + 1 = 111";}]=nil,[#{"1 + 1 = 111";"1 + 1 = 111";{787;954;747;1};{76;409;227;890};}]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII,[#{{95;136;897;661};}]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll={}for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI do local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll();local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI;if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==1)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll()~=0);elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==0)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIIlIIIIIlIIlllll();elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl==2)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII();end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI;end;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl();end;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()do local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll();if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,1,1)==0)then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,2,3);local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,4,6);local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll(),localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll(),nil,nil};if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII==0)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Dh3")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll();localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("QSoA")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll();elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII==1)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";}]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl();elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII==2)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Oh8")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()-(2^16)elseif(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII==3)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("0xu")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()-(2^16)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("qN9e")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll();end;if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI,1,1)==1)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Hj")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JG")]]end if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI,2,2)==1)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";{413;797;144;400};}]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("WCy")]]end if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI,3,3)==1)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("YBLs")]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{969;596;415;725};{478;711;214;695};{189;239;937;375};"1 + 1 = 111";}]]end localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI;end end;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl()do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI();end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll[3]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll();return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll;end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIllIIIIlllIIlIlllIl=pcall local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll)localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll==true and localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI())or localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll;return(function(...)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=-1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII={...};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIIlIIIIIlIIlllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl('#',...)-1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[#{{725;406;886;410};}];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[#{"1 + 1 = 111";"1 + 1 = 111";{3;818;317;6};}];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[#{"1 + 1 = 111";"1 + 1 = 111";}];local function localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIllIIIIIIII()local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI={};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll={};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl={};for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=0,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIIlIIIIIlIIlllll do if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI>=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll)then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIIlIlIlIlIlIIIIIlIIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1];end;end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIIlIIIIIlIIlllll-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1 local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;while true do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("G")];if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("3SOp9ie0uc6dNLHYFnpnFW2Znr37Gdl")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("l4ljh4XDexBGs31")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("nE1MynQ")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("ZZb")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("p")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("")then do return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Wd")]]end else if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("PV")]]then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{301;103;211;768};"1 + 1 = 111";}];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("q0")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("hL")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("2ij")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{815;612;867;238};"1 + 1 = 111";"1 + 1 = 111";}]];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ct")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("jVz")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("2TAyQ")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("BXih")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{541;810;556;836};"1 + 1 = 111";}]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1])else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("eY")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Vh1")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("35")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1])localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("km")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("aPm")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("fV")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("M0W")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("mL")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("PuW")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JxNL")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("a9")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";}]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Oo")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{559;533;753;388};{753;461;663;600};{847;728;30;959};}]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("iL6J")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dH")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("nvL")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{695;354;328;62};{928;708;238;932};"1 + 1 = 111";}]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("p3")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("7OD")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";{254;563;825;440};"1 + 1 = 111";}]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("hD")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{595;879;368;218};{996;5;370;986};"1 + 1 = 111";}]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Pd")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ARX")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("zG4J")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("XC")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ojC")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("mP")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("sXf")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("BL")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("xdP")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("LK")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("sxR")]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("j4")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("rIk")]]+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("gexg")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("tb")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dRq")]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("uc")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("xQN")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("0gbD")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("9b")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("fLQ")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("EO")]]==localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("49yW")])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("NuX")];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("vGuAQU")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("xE9")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{467;478;681;661};{896;882;181;828};}]];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("pei")];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#{"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{660;288;791;542};"1 + 1 = 111";{589;228;389;2};"1 + 1 = 111";"1 + 1 = 111";{679;345;689;834};{778;157;7;145};"1 + 1 = 111";}then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("cjP6bhX0Y")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("lKeIVEsR")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("SA")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("8UW")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("mEbo")]];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("kz")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("mu3")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("Kxrfqj3Gud")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("EX")]]();else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("in")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Gid")]))end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("TvKkflQJts33n")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("yM3PsZakXNLU")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("HC")]]();else if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("hf")]]then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("2E8")];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#{"1 + 1 = 111";{905;490;61;915};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{196;657;422;940};"1 + 1 = 111";"1 + 1 = 111";{459;828;237;557};{25;790;853;184};{608;529;20;956};"1 + 1 = 111";"1 + 1 = 111";{226;820;393;621};}then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ktC")];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("6X")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("aber")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+2 local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1],localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII])};for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI];end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[1]if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("yRg")];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("y9QDej2H90coxII6JuhrWmQ")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("hXyJMalTbrXY7hQ4Gph")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("zId9qdtiCG6lsnvqV")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("59OtmfUU9BOnWGe4")then if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Lt")]]==localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("abgt")])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("KgI")];end;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{922;831;73;824};{188;538;821;968};}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JUA")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dDee")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#{"1 + 1 = 111";{988;106;893;642};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{781;502;839;651};{682;518;610;986};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{713;815;32;129};{285;215;830;634};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";}then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Ix9")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dS")]];else if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";}]]<localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("do0W")]])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("3bK")];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("hfv8oHd2EnUAgykXHxcxt")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("bRVhnqPpfLl7X7nVjUEX")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("g2")]local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1 local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];end;else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("r8")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("RYC")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("0Z")]]();localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("am")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("roR")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("GC")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Fxq")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("cPm8")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("tv")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("NxF")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("c5")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("NZb")]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("rg")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{38;995;124;883};{876;604;585;765};"1 + 1 = 111";}]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("rkcq")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{608;740;342;897};"1 + 1 = 111";}];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Sda")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("5EPQ")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("dV4GiI81Vi7YGdOmgtXnoi")then if not localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("TB")]]then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("EvS")];end;else if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";}]]~=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("eaes")]])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("UQe")];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("oiKnJWmr2Hb850MfSRytCojq3KI")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#{"1 + 1 = 111";{719;858;898;568};"1 + 1 = 111";"1 + 1 = 111";{840;760;630;847};"1 + 1 = 111";{216;186;298;992};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{369;687;934;43};"1 + 1 = 111";{126;9;928;612};{577;54;808;273};"1 + 1 = 111";"1 + 1 = 111";{804;300;978;62};{865;375;772;979};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{736;677;23;208};"1 + 1 = 111";{359;666;95;172};}then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("S99tu9nj1l7RdtkoIzrrULj2")then if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("fA")]]~=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ZaR5")]])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("pMP")];end;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("XZ")]]=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("zPs")]~=0);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("Y1lJnMdDf4Qxo9A2A93tXbzmpP")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Ef")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("EEx")]];else if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("nL")]]==localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("B5sL")])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("1T9")];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("oKeg0hHGibLOJ3n8zE1tz1zuLqI8X")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("9sI5Y3bX0pRDq1ZutBKd9SyvrxkU")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ms")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{845;591;637;416};"1 + 1 = 111";{295;183;808;506};}]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("8Opa")]];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("1T")]]=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("FPW")]~=0);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("JJITQbrgOfu6MNMN5LIFp7hzTDq4mO")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("DF")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("vYfk")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+2 local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1],localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII])};for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI];end;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[1]if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("olz")];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;end;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("sY")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Phk")]]-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("09ZO")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("SqrrRiTLZIoQvpgxeWTvWNopMBj9mUx49OMQtv4El9S7brW")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("NgmiDsPfbcPp9UzekuQRNBcT1ojX5rotP9xgIoB")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("8HOrQPECLNXQLeGmdEqni2jUXvL9xsH649T")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("WVcmURlrFITKEkde7mIgx4EudFfrg0WMv")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("ArBdDBQ2ppMbGmpC8RBMU2qRjpM4lOWg")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dM")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("s5a")]];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("4h")]local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI-1 local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("dUVN3ThBEOoSc83cO2ekUPIbU4nZWRLDGX")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("iR")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{751;471;5;118};{554;696;894;920};}]]+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JXyJ")]];else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("kA")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{566;503;705;19};"1 + 1 = 111";"1 + 1 = 111";}]]+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("cjI9")]];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("CEZe8tlpnc8grT1HWR6EzmU39GsPXcVmpGBIZ")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("g1Jzt9WX6f4jZVKsdoU5fDbGDkqV1VrsR9Le")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{386;221;912;126};"1 + 1 = 111";}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("RZO")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("CJ")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("vOh")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("c7qx")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("re")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("RDe")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("x1Bf")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("8D")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ZBg")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("qoP0")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{840;373;768;312};}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("qiH")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("p44V")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("TZ")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("tB2")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Zkds")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{202;176;739;474};{98;257;639;203};}]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1])localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("XU")]]=(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("S83")]~=0);localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Y3Y")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("FJ")]];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Je")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1])end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("93UhmKmUNn9N6DmX3SO0c6iSa5kYMC0kFxdvaY")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Gb")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("xII")]))else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("u1")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("INX")]],nil,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("2YW07qi4rfFYq7o3dUbgqzzhkyZiJA0XeYVbpglC2b3")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("gOJmqE9M8SMEAOPKB9u2oRTCmmN7JKooFPbGYM1fA")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("ZxmqMMBMa2QaEz5gad1bcyDy8UNiVZhpCa2vp5Lb")then if not localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Zb")]]then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{406;349;573;918};{805;30;961;394};"1 + 1 = 111";}];end;else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Yb")]local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII))};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";{262;548;450;724};"1 + 1 = 111";}]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll];end end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("6JxJFlMLRLXoylC4Zr18r0lUAIEHJ0fDZFgYgYEJbH")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("pf")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("1Al")]]-localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("YgqM")]];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JDZ")]];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll={};localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIllIIlIlIlIlllIIIIlll({},{__index=function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI];return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[1][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[2]];end,__newindex=function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[1][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[2]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;end;});for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("yITE")]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("T")]==9 then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll-1]={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("L65")]};else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll-1]={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("6Sd")]};end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[#localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("IQ")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#{"1 + 1 = 111";"1 + 1 = 111";{407;66;962;932};{961;980;272;268};{127;624;334;623};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{162;445;174;532};{386;335;593;452};"1 + 1 = 111";{860;402;281;138};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{826;548;397;743};{227;535;948;757};{306;15;347;408};{182;606;513;102};{752;191;127;970};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{197;860;561;104};{744;676;827;188};"1 + 1 = 111";{76;857;874;161};{554;649;913;503};{192;571;50;476};"1 + 1 = 111";{896;242;420;564};{641;761;306;920};{633;115;221;253};{979;586;891;94};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{774;20;8;932};"1 + 1 = 111";{264;171;936;300};"1 + 1 = 111";"1 + 1 = 111";{272;36;896;646};{201;193;809;917};}then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("aBEPI16FmR9NlTXOB0CeEtzmUCb75CeLLMfmtvpBbqKr")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("RA")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("UXC")]];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ORn")]];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll={};localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIllIIlIlIlIlllIIIIlll({},{__index=function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI];return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[1][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[2]];end,__newindex=function(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl)local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[1][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[2]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl;end;});for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll=1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("9IT6")]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("x")]==9 then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll-1]={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("2RP")]};else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll-1]={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("vGv")]};end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[#localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Hl")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("Vs7tD8LrTXQbr4fZ9JVMzbJNLueLadnUhW8jeNDDDt2Zzl")then if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Bl")]]<localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("tiHz")]])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ldf")];end;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("t6")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("WjW")];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#{"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{725;612;500;952};{307;532;267;997};{900;780;361;42};"1 + 1 = 111";{894;917;853;978};{611;931;583;322};{578;277;525;901};"1 + 1 = 111";{805;164;975;405};"1 + 1 = 111";{499;263;86;163};{285;677;4;249};"1 + 1 = 111";{109;982;596;200};"1 + 1 = 111";{129;676;751;419};{296;446;988;833};"1 + 1 = 111";{561;323;998;728};{598;469;886;899};{91;585;374;398};"1 + 1 = 111";{20;132;373;837};"1 + 1 = 111";"1 + 1 = 111";{775;45;321;143};"1 + 1 = 111";{611;166;793;509};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{636;489;784;389};"1 + 1 = 111";{441;123;186;965};"1 + 1 = 111";{183;257;770;848};"1 + 1 = 111";"1 + 1 = 111";{427;728;652;207};{261;447;137;390};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";{328;308;613;346};"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";"1 + 1 = 111";}then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("m0PMHG6KaetZpJ2NdbBVRH9CVSQc6EWhWioIv4z6XOAQKFt4kJl")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("0UYQ1n1Y03NGsmdhttrjMF8chs1pzW2ysvqctJRidKf3b4yyC")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("K0rbYvWWJIxLzebpm75bYVMigbzebn5DYmlbgrH5pHaiWClb")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{673;759;502;188};{851;338;916;701};}]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1])else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";}]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ekW")]))end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("VRBDBxYoi4X6SVvN3Xm29j4BaVqVExO0ylpGEaxZxX7xT8IWOG")then do return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("l4")]]end else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{702;783;589;897};}]local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII))};local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ioWd")]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];end end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("P8i2MXaDQgdUVp6JGFZi2AxFmlHRbmFg8E91otNSEIRTtclgXAcqZ")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("aoMNQBptBDL6Wqqetm1hOuSNkj9A3LjnrLEUoMNmDkgoiyq40Sg0")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("jl")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("NrD")]))else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("1I")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("JAp")]],nil,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll);end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("RSQH9CGBZA0syOkXGZSJPPEnEEX2fKlEtYK3YnpjgWNVp5VUght2zq")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("e7")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("DBl")];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("S1")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI+1])end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("coCdFPOvpnx9Jdr8sKIC2bT7ITdW7psecdIPvjUEDBxH35lQYjbr0Y2aFFF")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("M1ya5r6GGNMiZe9CPmeMIy7YnannlHj1P82McvML9QjlH2CWZAvsB2no3")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("X4dSu9DVE1mxAtLDN1nVqvICyVk1BWVNSCJRIqvjTWyLJCabS8oxaisB")then if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("b9")]]~=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("148s")])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("m31")];end;else do return end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll>#("MtJYMuRfJP7U35vFdcF7726UjjDIyMKjhv57WvgBQFaXdqYc317fXEzTyZ")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("o3")]][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Yks")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{81;565;917;322};"1 + 1 = 111";{906;452;437;546};{110;189;666;702};}]];else if(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("AG")]]~=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("k8dc")])then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;else localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";"1 + 1 = 111";{245;593;282;478};}];end;end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("D2jS6fYNH9zTS6eqC2iQ2uAOHPEvO7mJ6QOJHXdRoOqdEhojTm60oiXhCFkqM")then if localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("9KW0WbrFUOZCCndvkzBuMBavtX3HhbNWfWPRrNvm1oDsbXRJdguV0q0IKAG8")then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{392;922;273;123};{237;361;490;913};}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("NWM")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("TO")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("agU")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Rv")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("4XP")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("SlBL")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("om")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll-1 localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII];end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("dx")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII))};localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("rCg7")]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII];end localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("nYf")];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll;local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{{971;181;636;578};"1 + 1 = 111";}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("qj5")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("ho")]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("15f")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("hj")];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("r4H")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("G9tM")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("vK")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlllIlIl(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]))localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlIIIlIllIlllI+localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll-1 localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII];end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("M7")]localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll={localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll](localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIIlllIIlIllIlIlIII))};localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=0;for localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll,localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Jumb")]do localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIllllIIIlIlIII];end localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IllIllllIlIllIII[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("1R0")];end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll<=#("VySbcxPJxeHdnDy8RuHgtDaR15Y6hg1hxeg1Ur8AcaQbXRNYDJ2f92YrxbjhIT")then do return end;elseif localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll==#("pnARixGoAxgdtW93ksaDIoWhEpYFSi11tJAYxaEC6tCS2ZFGyh8YIp2bBrm2IbH")then localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#{"1 + 1 = 111";{488;864;29;674};}]]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlllIIll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("nKn")]];else local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("Tn")];local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("YPm")]];localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll+1]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_llIIIIIlIlIIIlIl[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIllIlllIIIIlIIIlll]=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll[localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI[#("EJBC")]];end;localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll+1;end;end;A,B=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lllIlIllI(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIllIIIIlllIIlIlllIl(localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIllIIIIIIII))if not A[1]then local localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI=localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIlIIllIllIllIIll[4][localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIlIIIIlIIIlIlllIlllllll]or'?';error('ERROR IN IRONBREW SCRIPT [LINE '..localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIllIlllI..']:'..A[2]);wait(9e9);else return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IIIIIllIlllIllI(A,2,B);end;end);end;return localcloselocalparentlocalmattisthebestlocalzexisgaytreue_IlllIllIIlIll(true,{},localcloselocalparentlocalmattisthebestlocalzexisgaytreue_lIIllIIIIIIII())();end)(string.byte,table.insert,setmetatable);
end)
 
TextLabel.Parent = Frame
TextLabel.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
TextLabel.Size = UDim2.new(0, 360, 0, 52)
TextLabel.Font = Enum.Font.SourceSans
TextLabel.Text = "GunSquadKayy's Drop Offs Script"
TextLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
TextLabel.TextSize = 14.000
end)

local ScriptsSection = Scripts:NewSection("Guis")
ScriptsSection:NewButton("inf yield", "goated", function()
   loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
end)

ScriptsSection:NewButton("Rooftop gui", "roofed", function()
    loadstring(game:HttpGet(('https://pastebin.com/raw/JxsE00B2'),true))()
end)

-- Credits
local Credits = Window:NewTab("Credits")
local CreditsSection = Credits:NewSection("first drop offs gui")
local CreditsSection = Credits:NewSection("made by vaults")
local CreditsSection = Credits:NewSection("IF YOU HAVE THIS GUI U ARE ONE LUCKY PERSON")
local CreditsSection = Credits:NewSection("More fun scripts will be added soon")
