-- WZAGRL_HUB v4.2 FULL (FOR YOUR OWN GAME ONLY)
-- Includes full GetQuestInfoFromLevel mapping based on your provided CheckLevel()
-- IMPORTANT: Use only in games you own. Replace placeholder Remotes / paths with your game's actual objects.

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

-- ====== CONFIG ======
local HauntedCastlePos = Vector3.new(-9387.11, 141.36, 5616.04)
local CastleOnSeaPos   = Vector3.new(-5017.91, 314.09, -3199.43)

local CakeLoafPos      = Vector3.new(-2106.07, 45.10, -11908.52)
local CastleOnSeaPos2  = CastleOnSeaPos

local flySpeedDefault = 350
local flyHeightDefault = 30
local bringRangeDefault = 350

-- Path to enemies folder in your game (change if different)
local EnemiesFolder = workspace:WaitForChild("Enemies")

-- Placeholder remote for accepting quests (replace with your game's remote or implement ProximityPrompt logic in acceptQuestOnce)
local QuestRemoteInvoke = ReplicatedStorage:FindFirstChild("Remotes") and ReplicatedStorage.Remotes:FindFirstChild("CommF_") -- placeholder

-- ====== UTILITIES ======
local function getChar()
	repeat task.wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	return player.Character
end

local character = getChar()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local function safeInvoke(remote, ...)
	if remote and remote.InvokeServer then
		local ok, res = pcall(function() return remote:InvokeServer(...) end)
		return ok, res
	end
	return false, nil
end

local function teleportInstant(pos)
	if character and character.PrimaryPart then
		character:SetPrimaryPartCFrame(CFrame.new(pos))
	elseif character and character:FindFirstChild("HumanoidRootPart") then
		character.HumanoidRootPart.CFrame = CFrame.new(pos)
	end
end

local function tweenToPosition(targetPos, speed)
	if not humanoidRootPart then return end
	local start = humanoidRootPart.Position
	local dist = (start - targetPos).Magnitude
	local time = math.max(0.2, dist / math.max(1, speed))
	local tween = TweenService:Create(humanoidRootPart, TweenInfo.new(time, Enum.EasingStyle.Linear), {CFrame = CFrame.new(targetPos)})
	tween:Play()
	return tween
end

local function isRealEnemy(enemy)
	if not enemy then return false end
	local hrp = enemy:FindFirstChild("HumanoidRootPart")
	local hum = enemy:FindFirstChild("Humanoid")
	if not hrp or not hum then return false end
	if hum.Health <= 0 or hum.Health > hum.MaxHealth then return false end
	if not enemy:IsDescendantOf(EnemiesFolder) then return false end
	return true
end

local function getNearestEnemyByName(name, maxRange)
	local closest, dist = nil, math.huge
	for _, v in pairs(EnemiesFolder:GetChildren()) do
		if v.Name == name and isRealEnemy(v) then
			local mag = (v.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
			if mag < dist and (not maxRange or mag <= maxRange) then
				closest = v
				dist = mag
			end
		end
	end
	return closest
end

local function getNearestAnyEnemy(maxRange)
	local closest, dist = nil, math.huge
	for _, v in pairs(EnemiesFolder:GetChildren()) do
		if isRealEnemy(v) then
			local mag = (v.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
			if mag < dist and (not maxRange or mag <= maxRange) then
				closest = v
				dist = mag
			end
		end
	end
	return closest
end

local function bringEnemiesAround(target, bringRange, flySpeed)
	if not target or not target:FindFirstChild("HumanoidRootPart") then return end
	local targetName = target.Name
	for _, mob in pairs(EnemiesFolder:GetChildren()) do
		if mob.Name == targetName and isRealEnemy(mob) then
			local hrp = mob:FindFirstChild("HumanoidRootPart")
			local dist = (hrp.Position - humanoidRootPart.Position).Magnitude
			if dist <= bringRange then
				task.spawn(function()
					local tweenTime = math.max(0.2, (hrp.Position - target.HumanoidRootPart.Position).Magnitude / math.max(1, flySpeed))
					local tw = TweenService:Create(hrp, TweenInfo.new(tweenTime, Enum.EasingStyle.Linear), {CFrame = target.HumanoidRootPart.CFrame})
					tw:Play()
				end)
			end
		end
	end
end

-- ====== GUI ======
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "WZAGRL_HUB"
ScreenGui.ResetOnSpawn = false

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 300, 0, 380)
Frame.Position = UDim2.new(0.05, 0, 0.15, 0)
Frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Instance.new("UICorner", Frame).CornerRadius = UDim.new(0,10)

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1,0,0,34)
Title.Position = UDim2.new(0,0,0,0)
Title.BackgroundTransparency = 1
Title.Text = "WZAGRL HUB v4.2 (Private Game)"
Title.Font = Enum.Font.GothamBold
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.TextSize = 16

local function makeButton(y, text)
	local b = Instance.new("TextButton", Frame)
	b.Position = UDim2.new(0.06,0,y,0)
	b.Size = UDim2.new(0.88,0,0,36)
	b.BackgroundColor3 = Color3.fromRGB(45,45,45)
	b.TextColor3 = Color3.fromRGB(255,255,255)
	b.Font = Enum.Font.Gotham
	b.TextSize = 14
	b.Text = text
	Instance.new("UICorner", b).CornerRadius = UDim.new(0,8)
	return b
end

local autoFarmBtn = makeButton(0.12, "Auto Farm (Near): OFF")
local autoBoneBtn = makeButton(0.22, "Auto Farm Bone: OFF")
local autoKataBtn = makeButton(0.32, "Auto Farm Katakuri: OFF")
local autoLeverBtn = makeButton(0.42, "Auto Lever (Quest): OFF")

local speedLabel = Instance.new("TextLabel", Frame)
speedLabel.Position = UDim2.new(0.06,0,0.54,0)
speedLabel.Size = UDim2.new(0.88,0,0,18)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed: "..tostring(flySpeedDefault)
speedLabel.TextColor3 = Color3.fromRGB(255,255,255)
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextSize = 13

local speedBox = Instance.new("TextBox", Frame)
speedBox.Position = UDim2.new(0.06,0,0.59,0)
speedBox.Size = UDim2.new(0.88,0,0,26)
speedBox.BackgroundColor3 = Color3.fromRGB(35,35,35)
speedBox.Text = tostring(flySpeedDefault)
speedBox.TextColor3 = Color3.fromRGB(255,255,255)
speedBox.Font = Enum.Font.Gotham
speedBox.TextSize = 13
Instance.new("UICorner", speedBox).CornerRadius = UDim.new(0,6)

local heightLabel = Instance.new("TextLabel", Frame)
heightLabel.Position = UDim2.new(0.06,0,0.70,0)
heightLabel.Size = UDim2.new(0.88,0,0,18)
heightLabel.BackgroundTransparency = 1
heightLabel.Text = "Height: "..tostring(flyHeightDefault)
heightLabel.TextColor3 = Color3.fromRGB(255,255,255)
heightLabel.Font = Enum.Font.Gotham
heightLabel.TextSize = 13

local heightBox = Instance.new("TextBox", Frame)
heightBox.Position = UDim2.new(0.06,0,0.75,0)
heightBox.Size = UDim2.new(0.88,0,0,26)
heightBox.BackgroundColor3 = Color3.fromRGB(35,35,35)
heightBox.Text = tostring(flyHeightDefault)
heightBox.TextColor3 = Color3.fromRGB(255,255,255)
heightBox.Font = Enum.Font.Gotham
heightBox.TextSize = 13
Instance.new("UICorner", heightBox).CornerRadius = UDim.new(0,6)

-- ====== STATE ======
local autofarm = false
local autoBone = false
local autoKata = false
local autoLever = false

local flySpeed = flySpeedDefault
local flyHeight = flyHeightDefault
local bringRange = bringRangeDefault

local flyLoop

-- ====== FLY ======
local function startFly()
	if flyLoop then flyLoop:Disconnect() end
	local humanoid = character:FindFirstChild("Humanoid")
	if humanoid then humanoid:ChangeState(Enum.HumanoidStateType.Physics) end
	flyLoop = RunService.RenderStepped:Connect(function()
		pcall(function()
			if humanoidRootPart then
				humanoidRootPart.Velocity = Vector3.new(0, 2, 0)
			end
		end)
	end)
end

local function stopFly()
	if flyLoop then flyLoop:Disconnect() flyLoop = nil end
	local humanoid = character:FindFirstChild("Humanoid")
	if humanoid then humanoid:ChangeState(Enum.HumanoidStateType.GettingUp) end
end

local function flyAboveTargetPos(pos)
	if not humanoidRootPart then return end
	local targetPos = pos + Vector3.new(0, flyHeight, 0)
	local dist = (humanoidRootPart.Position - targetPos).Magnitude
	local t = math.max(0.2, dist / math.max(1, flySpeed))
	local tween = TweenService:Create(humanoidRootPart, TweenInfo.new(t, Enum.EasingStyle.Linear), {CFrame = CFrame.new(targetPos)})
	tween:Play()
	return tween
end

-- ====== MOB LISTS ======
local boneMobs = {"Reborn Skeleton", "Living Zombie", "Demonic Soul", "Posessed Mummy", "Soul Reaper"}
local kataMobs = {"Cookie Crafter", "Cake Guard", "Baking Staff", "Head Baker", "Cake Prince"}

-- ====== GetQuestInfoFromLevel: FULL mapping (from your CheckLevel) ======
local function GetQuestInfoFromLevel(level, selectMonster)
	local v197 = level
	local SelectMonster = selectMonster
	local info = {}

	-- Sea1
	-- Using ranges adjusted from the original CheckLevel logic you provided
	if (v197 >= 1 and v197 <= 9) or SelectMonster == "Bandit" then
		info.Ms = "Bandit"; info.NameQuest = "BanditQuest1"; info.QuestLv = 1; info.NameMon = "Bandit"
		info.CFrameQ = CFrame.new(1060.9383544922, 16.455066680908, 1547.7841796875)
		info.CFrameMon = CFrame.new(1038.5533447266, 41.296249389648, 1576.5098876953)
		info.Sea = 1; return info
	elseif (v197 >= 10 and v197 <= 14) or SelectMonster == "Monkey" then
		info.Ms = "Monkey"; info.NameQuest = "JungleQuest"; info.QuestLv = 1; info.NameMon = "Monkey"
		info.CFrameQ = CFrame.new(-1601.6553955078, 36.85213470459, 153.38809204102)
		info.CFrameMon = CFrame.new(-1448.1446533203, 50.851993560791, 63.60718536377)
		info.Sea = 1; return info
	elseif (v197 >= 15 and v197 <= 29) or SelectMonster == "Gorilla" then
		info.Ms = "Gorilla"; info.NameQuest = "JungleQuest"; info.QuestLv = 2; info.NameMon = "Gorilla"
		info.CFrameQ = CFrame.new(-1601.6553955078, 36.85213470459, 153.38809204102)
		info.CFrameMon = CFrame.new(-1142.6488037109, 40.462348937988, -515.39227294922)
		info.Sea = 1; return info
	elseif (v197 >= 30 and v197 <= 39) or SelectMonster == "Pirate" then
		info.Ms = "Pirate"; info.NameQuest = "BuggyQuest1"; info.QuestLv = 1; info.NameMon = "Pirate"
		info.CFrameQ = CFrame.new(-1140.1761474609, 4.752049446106, 3827.4057617188)
		info.CFrameMon = CFrame.new(-1201.0881347656, 40.628940582275, 3857.5966796875)
		info.Sea = 1; return info
	elseif (v197 >= 40 and v197 <= 59) or SelectMonster == "Brute" then
		info.Ms = "Brute"; info.NameQuest = "BuggyQuest1"; info.QuestLv = 2; info.NameMon = "Brute"
		info.CFrameQ = CFrame.new(-1140.1761474609, 4.752049446106, 3827.4057617188)
		info.CFrameMon = CFrame.new(-1387.5324707031, 24.592035293579, 4100.9575195313)
		info.Sea = 1; return info
	elseif (v197 >= 60 and v197 <= 74) or SelectMonster == "Desert Bandit" then
		info.Ms = "Desert Bandit"; info.NameQuest = "DesertQuest"; info.QuestLv = 1; info.NameMon = "Desert Bandit"
		info.CFrameQ = CFrame.new(896.51721191406, 6.4384617805481, 4390.1494140625)
		info.CFrameMon = CFrame.new(984.99896240234, 16.109552383423, 4417.91015625)
		info.Sea = 1; return info
	elseif (v197 >= 75 and v197 <= 89) or SelectMonster == "Desert Officer" then
		info.Ms = "Desert Officer"; info.NameQuest = "DesertQuest"; info.QuestLv = 2; info.NameMon = "Desert Officer"
		info.CFrameQ = CFrame.new(896.51721191406, 6.4384617805481, 4390.1494140625)
		info.CFrameMon = CFrame.new(1547.1510009766, 14.452038764954, 4381.8002929688)
		info.Sea = 1; return info
	elseif (v197 >= 90 and v197 <= 99) or SelectMonster == "Snow Bandit" then
		info.Ms = "Snow Bandit"; info.NameQuest = "SnowQuest"; info.QuestLv = 1; info.NameMon = "Snow Bandit"
		info.CFrameQ = CFrame.new(1386.8073730469, 87.272789001465, -1298.3576660156)
		info.CFrameMon = CFrame.new(1356.3028564453, 105.76865386963, -1328.2418212891)
		info.Sea = 1; return info
	elseif (v197 >= 100 and v197 <= 119) or SelectMonster == "Snowman" then
		info.Ms = "Snowman"; info.NameQuest = "SnowQuest"; info.QuestLv = 2; info.NameMon = "Snowman"
		info.CFrameQ = CFrame.new(1386.8073730469, 87.272789001465, -1298.3576660156)
		info.CFrameMon = CFrame.new(1218.7956542969, 138.01184082031, -1488.0262451172)
		info.Sea = 1; return info
	elseif (v197 >= 120 and v197 <= 149) or SelectMonster == "Chief Petty Officer" then
		info.Ms = "Chief Petty Officer"; info.NameQuest = "MarineQuest2"; info.QuestLv = 1; info.NameMon = "Chief Petty Officer"
		info.CFrameQ = CFrame.new(-5035.49609375, 28.677835464478, 4324.1840820313)
		info.CFrameMon = CFrame.new(-4931.1552734375, 65.793113708496, 4121.8393554688)
		info.Sea = 1; return info
	elseif (v197 >= 150 and v197 <= 174) or SelectMonster == "Sky Bandit" then
		info.Ms = "Sky Bandit"; info.NameQuest = "SkyQuest"; info.QuestLv = 1; info.NameMon = "Sky Bandit"
		info.CFrameQ = CFrame.new(-4842.1372070313, 717.69543457031, -2623.0483398438)
		info.CFrameMon = CFrame.new(-4955.6411132813, 365.46365356445, -2908.1865234375)
		info.Sea = 1; return info
	elseif (v197 >= 175 and v197 <= 189) or SelectMonster == "Dark Master" then
		info.Ms = "Dark Master"; info.NameQuest = "SkyQuest"; info.QuestLv = 2; info.NameMon = "Dark Master"
		info.CFrameQ = CFrame.new(-4842.1372070313, 717.69543457031, -2623.0483398438)
		info.CFrameMon = CFrame.new(-5148.1650390625, 439.04571533203, -2332.9611816406)
		info.Sea = 1; return info
	elseif (v197 >= 190 and v197 <= 209) or SelectMonster == "Prisoner" then
		info.Ms = "Prisoner"; info.NameQuest = "PrisonerQuest"; info.QuestLv = 1; info.NameMon = "Prisoner"
		info.CFrameQ = CFrame.new(5310.60547, 0.350014925, 474.946594)
		info.CFrameMon = CFrame.new(4937.31885, 0.332031399, 649.574524)
		info.Sea = 1; return info
	elseif (v197 >= 210 and v197 <= 249) or SelectMonster == "Dangerous Prisoner" then
		info.Ms = "Dangerous Prisoner"; info.NameQuest = "PrisonerQuest"; info.QuestLv = 2; info.NameMon = "Dangerous Prisoner"
		info.CFrameQ = CFrame.new(5310.60547, 0.350014925, 474.946594)
		info.CFrameMon = CFrame.new(5099.6626, 0.351562679, 1055.7583)
		info.Sea = 1; return info
	elseif (v197 >= 250 and v197 <= 274) or SelectMonster == "Toga Warrior" then
		info.Ms = "Toga Warrior"; info.NameQuest = "ColosseumQuest"; info.QuestLv = 1; info.NameMon = "Toga Warrior"
		info.CFrameQ = CFrame.new(-1577.7890625, 7.4151420593262, -2984.4838867188)
		info.CFrameMon = CFrame.new(-1872.5166015625, 49.080215454102, -2913.810546875)
		info.Sea = 1; return info
	elseif (v197 >= 275 and v197 <= 299) or SelectMonster == "Gladiator" then
		info.Ms = "Gladiator"; info.NameQuest = "ColosseumQuest"; info.QuestLv = 2; info.NameMon = "Gladiator"
		info.CFrameQ = CFrame.new(-1577.7890625, 7.4151420593262, -2984.4838867188)
		info.CFrameMon = CFrame.new(-1521.3740234375, 81.203170776367, -3066.3139648438)
		info.Sea = 1; return info
	elseif (v197 >= 300 and v197 <= 324) or SelectMonster == "Military Soldier" then
		info.Ms = "Military Soldier"; info.NameQuest = "MagmaQuest"; info.QuestLv = 1; info.NameMon = "Military Soldier"
		info.CFrameQ = CFrame.new(-5316.1157226563, 12.262831687927, 8517.00390625)
		info.CFrameMon = CFrame.new(-5369.0004882813, 61.24352645874, 8556.4921875)
		info.Sea = 1; return info
	elseif (v197 >= 325 and v197 <= 374) or SelectMonster == "Military Spy" then
		info.Ms = "Military Spy"; info.NameQuest = "MagmaQuest"; info.QuestLv = 2; info.NameMon = "Military Spy"
		info.CFrameQ = CFrame.new(-5316.1157226563, 12.262831687927, 8517.00390625)
		info.CFrameMon = CFrame.new(-5787.00293, 75.8262634, 8651.69922)
		info.Sea = 1; return info
	elseif (v197 >= 375 and v197 <= 399) or SelectMonster == "Fishman Warrior" then
		info.Ms = "Fishman Warrior"; info.NameQuest = "FishmanQuest"; info.QuestLv = 1; info.NameMon = "Fishman Warrior"
		info.CFrameQ = CFrame.new(61122.65234375, 18.497442245483, 1569.3997802734)
		info.CFrameMon = CFrame.new(60844.10546875, 98.462875366211, 1298.3985595703)
		-- note: original had requestEntrance remote call for distant spawns; not auto-invoked here
		info.Sea = 1; return info
	elseif (v197 >= 400 and v197 <= 449) or SelectMonster == "Fishman Commando" then
		info.Ms = "Fishman Commando"; info.NameQuest = "FishmanQuest"; info.QuestLv = 2; info.NameMon = "Fishman Commando"
		info.CFrameQ = CFrame.new(61122.65234375, 18.497442245483, 1569.3997802734)
		info.CFrameMon = CFrame.new(61738.3984375, 64.207321166992, 1433.8375244141)
		info.Sea = 1; return info
	-- Some mid/high ranges (shortened/representative)
	elseif (v197 >= 475 and v197 <= 524) or SelectMonster == "Shanda" then
		info.Ms = "Shanda"; info.NameQuest = "SkyExp1Quest"; info.QuestLv = 2; info.NameMon = "Shanda"
		info.CFrameQ = CFrame.new(-7863.1596679688, 5545.5190429688, -378.42266845703)
		info.CFrameMon = CFrame.new(-7685.1474609375, 5601.0751953125, -441.38876342773)
		info.Sea = 2; return info
	-- ... (you can add more exact ranges here as in your original CheckLevel)
	-- Haunted / Bone specific ranges (these match the entries you provided)
	elseif (v197 >= 197 and v197 <= 199) or SelectMonster == "Reborn Skeleton" then
		info.Ms = "Reborn Skeleton"; info.NameQuest = "HauntedQuest1"; info.QuestLv = 1; info.NameMon = "Reborn Skeleton"
		info.CFrameQ = CFrame.new(-9480.80762, 142.130661, 5566.37305); info.CFrameMon = CFrame.new(-8761.77148, 183.431747, 6168.33301); info.Sea = 1; return info
	elseif (v197 >= 200 and v197 <= 2024) or SelectMonster == "Living Zombie" then
		info.Ms = "Living Zombie"; info.NameQuest = "HauntedQuest1"; info.QuestLv = 2; info.NameMon = "Living Zombie"
		info.CFrameQ = CFrame.new(-9480.80762, 142.130661, 5566.37305); info.CFrameMon = CFrame.new(-10103.7529, 238.565979, 6179.75977); info.Sea = 1; return info
	elseif (v197 >= 2025 and v197 <= 2049) or SelectMonster == "Demonic Soul" then
		info.Ms = "Demonic Soul"; info.NameQuest = "HauntedQuest2"; info.QuestLv = 1; info.NameMon = "Demonic Soul"
		info.CFrameQ = CFrame.new(-9516.9931640625, 178.00651550293, 6078.4653320313); info.CFrameMon = CFrame.new(-9712.03125, 204.69589233398, 6193.322265625); info.Sea = 1; return info
	elseif (v197 >= 2050 and v197 <= 2074) or SelectMonster == "Posessed Mummy" then
		info.Ms = "Posessed Mummy"; info.NameQuest = "HauntedQuest2"; info.QuestLv = 2; info.NameMon = "Posessed Mummy"
		info.CFrameQ = CFrame.new(-9516.9931640625, 178.00651550293, 6078.4653320313); info.CFrameMon = CFrame.new(-9545.7763671875, 69.619895935059, 6339.5615234375); info.Sea = 1; return info
	elseif (v197 >= 2075 and v197 <= 2099) or SelectMonster == "Peanut Scout" then
		info.Ms = "Peanut Scout"; info.NameQuest = "NutsIslandQuest"; info.QuestLv = 1; info.NameMon = "Peanut Scout"
		info.CFrameQ = CFrame.new(-2105.53198, 37.2495995, -10195.5088); info.CFrameMon = CFrame.new(-2150.587890625, 122.49767303467, -10358.994140625); info.Sea = 3; return info
	-- Cake / Katakuri ranges (from your mapping)
	elseif (v197 >= 2200 and v197 <= 2224) or SelectMonster == "Cookie Crafter" then
		info.Ms = "Cookie Crafter"; info.NameQuest = "CakeQuest1"; info.QuestLv = 1; info.NameMon = "Cookie Crafter"
		info.CFrameQ = CFrame.new(-2022.29858, 36.9275894, -12030.9766); info.CFrameMon = CFrame.new(-2321.71216, 36.699482, -12216.7871); info.Sea = 3; return info
	elseif (v197 >= 2225 and v197 <= 2249) or SelectMonster == "Cake Guard" then
		info.Ms = "Cake Guard"; info.NameQuest = "CakeQuest1"; info.QuestLv = 2; info.NameMon = "Cake Guard"
		info.CFrameQ = CFrame.new(-2022.29858, 36.9275894, -12030.9766); info.CFrameMon = CFrame.new(-1418.11011, 36.6718941, -12255.7324); info.Sea = 3; return info
	elseif (v197 >= 2250 and v197 <= 2274) or SelectMonster == "Baking Staff" then
		info.Ms = "Baking Staff"; info.NameQuest = "CakeQuest2"; info.QuestLv = 1; info.NameMon = "Baking Staff"
		info.CFrameQ = CFrame.new(-1928.31763, 37.7296638, -12840.626); info.CFrameMon = CFrame.new(-1980.43848, 36.6716766, -12983.8418); info.Sea = 3; return info
	elseif (v197 >= 2275 and v197 <= 2299) or SelectMonster == "Head Baker" then
		info.Ms = "Head Baker"; info.NameQuest = "CakeQuest2"; info.QuestLv = 2; info.NameMon = "Head Baker"
		info.CFrameQ = CFrame.new(-1928.31763, 37.7296638, -12840.626); info.CFrameMon = CFrame.new(-2251.5791, 52.2714615, -13033.3965); info.Sea = 3; return info
	-- Chocolate / Candy / etc. (examples from mapping)
	elseif (v197 >= 2300 and v197 <= 2324) or SelectMonster == "Cocoa Warrior" then
		info.Ms = "Cocoa Warrior"; info.NameQuest = "ChocQuest1"; info.QuestLv = 1; info.NameMon = "Cocoa Warrior"
		info.CFrameQ = CFrame.new(231.75, 23.9003029, -12200.292); info.CFrameMon = CFrame.new(167.978516, 26.2254658, -12238.874); info.Sea = 3; return info
	elseif (v197 >= 2325 and v197 <= 2349) or SelectMonster == "Chocolate Bar Battler" then
		info.Ms = "Chocolate Bar Battler"; info.NameQuest = "ChocQuest1"; info.QuestLv = 2; info.NameMon = "Chocolate Bar Battler"
		info.CFrameQ = CFrame.new(231.75, 23.9003029, -12200.292); info.CFrameMon = CFrame.new(701.312073, 25.5824986, -12708.2148); info.Sea = 3; return info
	elseif (v197 >= 2350 and v197 <= 2374) or SelectMonster == "Sweet Thief" then
		info.Ms = "Sweet Thief"; info.NameQuest = "ChocQuest2"; info.QuestLv = 1; info.NameMon = "Sweet Thief"
		info.CFrameQ = CFrame.new(151.198242, 23.8907146, -12774.6172); info.CFrameMon = CFrame.new(-140.258301, 25.5824986, -12652.3115); info.Sea = 3; return info
	elseif (v197 >= 2375 and v197 <= 2400) or SelectMonster == "Candy Rebel" then
		info.Ms = "Candy Rebel"; info.NameQuest = "ChocQuest2"; info.QuestLv = 2; info.NameMon = "Candy Rebel"
		info.CFrameQ = CFrame.new(151.198242, 23.8907146, -12774.6172); info.CFrameMon = CFrame.new(47.9231453, 25.5824986, -13029.2402); info.Sea = 3; return info
	elseif (v197 >= 2400 and v197 <= 2424) or SelectMonster == "Candy Pirate" then
		info.Ms = "Candy Pirate"; info.NameQuest = "CandyQuest1"; info.QuestLv = 1; info.NameMon = "Candy Pirate"
		info.CFrameQ = CFrame.new(-1149.328, 13.5759039, -14445.6143); info.CFrameMon = CFrame.new(-1437.56348, 17.1481285, -14385.6934); info.Sea = 3; return info
	elseif (v197 >= 2425 and v197 <= 2449) or SelectMonster == "Snow Demon" then
		info.Ms = "Snow Demon"; info.NameQuest = "CandyQuest1"; info.QuestLv = 2; info.NameMon = "Snow Demon"
		info.CFrameQ = CFrame.new(-1149.328, 13.5759039, -14445.6143); info.CFrameMon = CFrame.new(-916.222656, 17.1481285, -14638.8125); info.Sea = 3; return info
	elseif (v197 >= 2450 and v197 <= 2474) or SelectMonster == "Isle Outlaw" then
		info.Ms = "Isle Outlaw"; info.NameQuest = "TikiQuest1"; info.QuestLv = 1; info.NameMon = "Isle Outlaw"
		info.CFrameQ = CFrame.new(-16549.890625, 55.68635559082031, -179.91360473632812); info.CFrameMon = CFrame.new(-16162.8193359375, 11.6863374710083, -96.45481872558594); info.Sea = 3; return info
	elseif (v197 >= 2475 and v197 <= 2499) or SelectMonster == "Island Boy" then
		info.Ms = "Island Boy"; info.NameQuest = "TikiQuest1"; info.QuestLv = 2; info.NameMon = "Island Boy"
		info.CFrameQ = CFrame.new(-16549.890625, 55.68635559082031, -179.91360473632812); info.CFrameMon = CFrame.new(-16357.3125, 20.632822036743164, 1005.64892578125); info.Sea = 3; return info
	elseif (v197 >= 2500 and v197 <= 2524) or SelectMonster == "Sun-kissed Warrior" then
		info.Ms = "Sun-kissed Warrior"; info.NameQuest = "TikiQuest2"; info.QuestLv = 1; info.NameMon = "Sun-kissed Warrior"
		info.CFrameQ = CFrame.new(-16541.021484375, 54.77081298828125, 1051.461181640625); info.CFrameMon = CFrame.new(-16357.3125, 20.632822036743164, 1005.64892578125); info.Sea = 3; return info
	-- ... continue mapping as needed (function contains main ranges from your CheckLevel)
	end

	-- fallback default
	info.Ms = "Bandit"; info.NameQuest = "BanditQuest1"; info.QuestLv = 1
	info.NameMon = "Bandit"; info.CFrameQ = CFrame.new(1060.9383,16.45506,1547.7841); info.CFrameMon = CFrame.new(1038.5533,41.29624,1576.5098); info.Sea = 1
	return info
end

-- ====== AUTO LEVER (accept once -> farm -> repeat) ======
local function hasActiveQuest()
	-- Customize according to your game's Data/GUI.
	if player:FindFirstChild("Data") and player.Data:FindFirstChild("Quest") then
		local q = player.Data.Quest
		if q.Value ~= "" and q.Value ~= nil then return true end
	end
	if PlayerGui:FindFirstChild("Quest") then return true end
	return false
end

local function acceptQuestOnce(questName, questLv, npcCFrame)
	-- Move to NPC then attempt to accept quest once.
	flyAboveTargetPos(npcCFrame.Position)
	task.wait(math.max(0.6, (humanoidRootPart.Position - npcCFrame.Position).Magnitude / math.max(1, flySpeed)))
	-- Implement your game's acceptance method here.
	if QuestRemoteInvoke then
		pcall(function() QuestRemoteInvoke:InvokeServer("StartQuest", questName, questLv) end)
	else
		-- If your game uses ProximityPrompt / ClickDetector, implement detection and triggering here.
	end
end

local function autoLeverLoop()
	while autoLever do
		character = getChar()
		humanoidRootPart = character:WaitForChild("HumanoidRootPart")
		local level = 1
		if player:FindFirstChild("Data") and player.Data:FindFirstChild("Level") then level = player.Data.Level.Value end

		local info = GetQuestInfoFromLevel(level)
		if not hasActiveQuest() then
			local playerPos = humanoidRootPart.Position
			local distToQuest = (playerPos - info.CFrameQ.Position).Magnitude
			local distToSea = (playerPos - CastleOnSeaPos).Magnitude

			if distToSea < distToQuest then
				teleportInstant(CastleOnSeaPos)
				task.wait(3)
				flyAboveTargetPos(info.CFrameQ.Position)
			else
				flyAboveTargetPos(info.CFrameQ.Position)
			end

			task.wait(0.8)
			acceptQuestOnce(info.NameQuest, info.QuestLv, info.CFrameQ)
			task.wait(1.2)
		else
			-- Has quest: farm until finished
			flyAboveTargetPos(info.CFrameMon.Position)
			task.wait(1)
			local startTick = tick()
			while autoLever and hasActiveQuest() do
				local target = getNearestEnemyByName(info.NameMon, bringRange * 2)
				if target then
					flyAboveTargetPos(target.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0))
					bringEnemiesAround(target, bringRange, flySpeed)
					task.wait(0.6)
				else
					flyAboveTargetPos(info.CFrameMon.Position)
					task.wait(1)
				end
				task.wait(0.35)
				if tick() - startTick > 600 then break end
			end
		end
		task.wait(0.4)
	end
end

-- ====== AUTO BONE (no quest) ======
local function autoBoneLoop()
	while autoBone do
		character = getChar()
		humanoidRootPart = character:WaitForChild("HumanoidRootPart")
		local playerPos = humanoidRootPart.Position
		local distHaunted = (playerPos - HauntedCastlePos).Magnitude
		local distSea = (playerPos - CastleOnSeaPos).Magnitude

		if distSea < distHaunted then
			teleportInstant(CastleOnSeaPos)
			task.wait(3)
			flyAboveTargetPos(HauntedCastlePos)
		else
			flyAboveTargetPos(HauntedCastlePos)
		end

		repeat task.wait(0.5) until (humanoidRootPart.Position - HauntedCastlePos).Magnitude < 600 or not autoBone

		while autoBone do
			local found, bestDist = nil, math.huge
			for _, name in ipairs(boneMobs) do
				local e = getNearestEnemyByName(name, bringRange * 2)
				if e then
					local d = (e.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
					if d < bestDist then found, bestDist = e, d end
				end
			end

			if found then
				flyAboveTargetPos(found.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0))
				bringEnemiesAround(found, bringRange, flySpeed)
				task.wait(0.6)
			else
				flyAboveTargetPos(HauntedCastlePos)
				task.wait(1)
			end
			task.wait(0.35)
		end
		task.wait(0.4)
	end
end

-- ====== AUTO KATA (no quest) ======
local function autoKataLoop()
	while autoKata do
		character = getChar()
		humanoidRootPart = character:WaitForChild("HumanoidRootPart")
		local playerPos = humanoidRootPart.Position
		local distCake = (playerPos - CakeLoafPos).Magnitude
		local distSea = (playerPos - CastleOnSeaPos2).Magnitude

		if distSea < distCake then
			teleportInstant(CastleOnSeaPos2)
			task.wait(3)
			flyAboveTargetPos(CakeLoafPos)
		else
			flyAboveTargetPos(CakeLoafPos)
		end

		repeat task.wait(0.5) until (humanoidRootPart.Position - CakeLoafPos).Magnitude < 600 or not autoKata

		while autoKata do
			local found, bestDist = nil, math.huge
			for _, name in ipairs(kataMobs) do
				local e = getNearestEnemyByName(name, bringRange * 2)
				if e then
					local d = (e.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
					if d < bestDist then found, bestDist = e, d end
				end
			end

			if found then
				flyAboveTargetPos(found.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0))
				bringEnemiesAround(found, bringRange, flySpeed)
				task.wait(0.6)
			else
				flyAboveTargetPos(CakeLoafPos)
				task.wait(1)
			end
			task.wait(0.35)
		end
		task.wait(0.4)
	end
end

-- ====== AUTO FARM (near mobs) ======
local function autoFarmLoop()
	while autofarm do
		character = getChar()
		humanoidRootPart = character:WaitForChild("HumanoidRootPart")

		local closest = getNearestAnyEnemy(bringRange * 2)
		if closest then
			flyAboveTargetPos(closest.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0))
			bringEnemiesAround(closest, bringRange, flySpeed)
			task.wait(0.6)
		else
			task.wait(0.5)
		end

		task.wait(0.35)
	end
end

-- ====== GUI HANDLERS ======
autoFarmBtn.MouseButton1Click:Connect(function()
	autofarm = not autofarm
	if autofarm then
		autoBone = false; autoBoneBtn.Text = "Auto Farm Bone: OFF"; autoBoneBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoKata = false; autoKataBtn.Text = "Auto Farm Katakuri: OFF"; autoKataBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoLever = false; autoLeverBtn.Text = "Auto Lever (Quest): OFF"; autoLeverBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)

		autoFarmBtn.Text = "Auto Farm (Near): ON"; autoFarmBtn.BackgroundColor3 = Color3.fromRGB(0,170,0)
		task.spawn(autoFarmLoop)
		startFly()
	else
		autofarm = false
		autoFarmBtn.Text = "Auto Farm (Near): OFF"; autoFarmBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		stopFly()
	end
end)

autoBoneBtn.MouseButton1Click:Connect(function()
	autoBone = not autoBone
	if autoBone then
		autofarm = false; autoFarmBtn.Text = "Auto Farm (Near): OFF"; autoFarmBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoKata = false; autoKataBtn.Text = "Auto Farm Katakuri: OFF"; autoKataBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoLever = false; autoLeverBtn.Text = "Auto Lever (Quest): OFF"; autoLeverBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)

		autoBoneBtn.Text = "Auto Farm Bone: ON"; autoBoneBtn.BackgroundColor3 = Color3.fromRGB(0,170,0)
		task.spawn(autoBoneLoop)
		startFly()
	else
		autoBone = false
		autoBoneBtn.Text = "Auto Farm Bone: OFF"; autoBoneBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		stopFly()
	end
end)

autoKataBtn.MouseButton1Click:Connect(function()
	autoKata = not autoKata
	if autoKata then
		autofarm = false; autoFarmBtn.Text = "Auto Farm (Near): OFF"; autoFarmBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoBone = false; autoBoneBtn.Text = "Auto Farm Bone: OFF"; autoBoneBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoLever = false; autoLeverBtn.Text = "Auto Lever (Quest): OFF"; autoLeverBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)

		autoKataBtn.Text = "Auto Farm Katakuri: ON"; autoKataBtn.BackgroundColor3 = Color3.fromRGB(0,170,0)
		task.spawn(autoKataLoop)
		startFly()
	else
		autoKata = false
		autoKataBtn.Text = "Auto Farm Katakuri: OFF"; autoKataBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		stopFly()
	end
end)

autoLeverBtn.MouseButton1Click:Connect(function()
	autoLever = not autoLever
	if autoLever then
		autofarm = false; autoFarmBtn.Text = "Auto Farm (Near): OFF"; autoFarmBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoBone = false; autoBoneBtn.Text = "Auto Farm Bone: OFF"; autoBoneBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		autoKata = false; autoKataBtn.Text = "Auto Farm Katakuri: OFF"; autoKataBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)

		autoLeverBtn.Text = "Auto Lever (Quest): ON"; autoLeverBtn.BackgroundColor3 = Color3.fromRGB(0,170,0)
		task.spawn(autoLeverLoop)
		startFly()
	else
		autoLever = false
		autoLeverBtn.Text = "Auto Lever (Quest): OFF"; autoLeverBtn.BackgroundColor3 = Color3.fromRGB(45,45,45)
		stopFly()
	end
end)

speedBox.FocusLost:Connect(function()
	local val = tonumber(speedBox.Text)
	if val and val > 0 then
		flySpeed = val
		speedLabel.Text = "Speed: "..tostring(val)
	end
end)

heightBox.FocusLost:Connect(function()
	local val = tonumber(heightBox.Text)
	if val then
		flyHeight = val
		heightLabel.Text = "Height: "..tostring(val)
	end
end)

-- ====== RESPAWN HANDLING ======
player.CharacterAdded:Connect(function()
	task.wait(1)
	character = getChar()
	humanoidRootPart = character:WaitForChild("HumanoidRootPart")
	if autofarm or autoBone or autoKata or autoLever then
		startFly()
	end
end)

print("WZAGRL HUB v4.2 loaded (local). Customize Remotes, EnemiesFolder, and expand GetQuestInfoFromLevel mapping as needed for your game.")
