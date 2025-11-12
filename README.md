local q = game.Players.LocalPlayer;
local U = game:GetService("UserInputService");
task.spawn(function()
	while task.wait(1) do
		if q.Character and q.Character:FindFirstChildOfClass("Humanoid") then
			(q.Character:FindFirstChildOfClass("Humanoid")):ChangeState(Enum.HumanoidStateType.Jumping);
		end;
	end;
end);
local f = game:GetService("TweenService");
local i = game:GetService("CoreGui");
if i:FindFirstChild("WZAGRL_Purple_UI") then
	i.WZAGRL_Purple_UI:Destroy();
end;
local B = Instance.new("ScreenGui");
B.Name = "WZAGRL_Purple_UI";
B.IgnoreGuiInset = true;
B.ResetOnSpawn = false;
B.Parent = i;
local l = Instance.new("Frame");
l.Size = UDim2.new(.45, 0, .1, 0);
l.Position = UDim2.new(.5, 0, .15, 0);
l.AnchorPoint = Vector2.new(.5, .5);
l.BackgroundColor3 = Color3.fromRGB(20, 20, 35);
l.BackgroundTransparency = .3;
l.BorderSizePixel = 0;
l.Parent = B;
l.Visible = false;
local x = Instance.new("UICorner");
x.CornerRadius = UDim.new(0, 25);
x.Parent = l;
local C = Instance.new("UIStroke");
C.Thickness = 2;
C.Color = Color3.fromRGB(170, 0, 255);
C.ApplyStrokeMode = Enum.ApplyStrokeMode.Border;
C.Parent = l;
local X = Instance.new("ImageLabel");
X.Size = UDim2.new(1.4, 0, 2, 0);
X.Position = UDim2.new(-0.2, 0, -0.5, 0);
X.BackgroundTransparency = 1;
X.Image = "rbxassetid://4996891970";
X.ImageColor3 = Color3.fromRGB(170, 0, 255);
X.ImageTransparency = .55;
X.ZIndex = -1;
X.Parent = l;
local g = Instance.new("UIGradient");
g.Color = ColorSequence.new({ ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 0, 40)), ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 0, 25)) });
g.Rotation = 45;
g.Parent = l;
local E = Instance.new("TextLabel");
E.Size = UDim2.new(1, 0, 1, 0);
E.BackgroundTransparency = 1;
E.Text = "WZAGRL Hub - Auto Collect Chest";
E.TextColor3 = Color3.fromRGB(200, 0, 255);
E.Font = Enum.Font.GothamBlack;
E.TextScaled = true;
E.TextStrokeTransparency = .5;
E.TextStrokeColor3 = Color3.fromRGB(0, 0, 0);
E.Parent = l;
l.Visible = true;
l.BackgroundTransparency = 1;
l.Size = UDim2.new(.2, 0, .05, 0);
E.TextTransparency = 1;
C.Thickness = 0;
X.ImageTransparency = 1;
(f:Create(l, TweenInfo.new(1, Enum.EasingStyle.Back, Enum.EasingDirection.Out), { BackgroundTransparency = .3, Size = UDim2.new(.45, 0, .1, 0) })):Play();
(f:Create(E, TweenInfo.new(1, Enum.EasingStyle.Quad), { TextTransparency = 0 })):Play();
(f:Create(C, TweenInfo.new(1, Enum.EasingStyle.Quad), { Thickness = 2 })):Play();
(f:Create(X, TweenInfo.new(1, Enum.EasingStyle.Quad), { ImageTransparency = .55 })):Play();
local v = game:GetService("Players");
local H = v.LocalPlayer;
local function Z(q)
	if not q then
		return;
	end;
	if q:FindFirstChild("PurpleAura") then
		q.PurpleAura:Destroy();
	end;
	local U = Instance.new("Highlight");
	U.Name = "PurpleAura";
	U.FillColor = Color3.fromRGB(170, 0, 255);
	U.OutlineColor = Color3.fromRGB(200, 100, 255);
	U.FillTransparency = .3;
	U.OutlineTransparency = 0;
	U.Parent = q;
end;
if H.Character then
	Z(H.Character);
end;
H.CharacterAdded:Connect(function(q)
	q:WaitForChild("HumanoidRootPart");
	task.wait(1);
	Z(q);
end);
local M = game:GetService("Players");
local A = game:GetService("RunService");
local c = M.LocalPlayer;
local F = "WZAGRL Hub";
local e = 18;
local k = Vector3.new(0, 1.8, 0);
local K = 1.0;
local function m(q)
	if not q then
		return;
	end;
	local U = q:FindFirstChild("Head") or q:FindFirstChildWhichIsA("BasePart");
	if not U then
		return;
	end;
	local f = U:FindFirstChild("WZAGRL_FastAttack_Label");
	if f then
		f:Destroy();
	end;
	local i = Instance.new("BillboardGui");
	i.Name = "WZAGRL_FastAttack_Label";
	i.Adornee = U;
	i.AlwaysOnTop = true;
	i.Size = UDim2.new(0, 200, 0, 40);
	i.StudsOffset = k;
	i.Parent = U;
	local B = Instance.new("TextLabel");
	B.Name = "Label";
	B.Size = UDim2.new(1, 0, 1, 0);
	B.BackgroundTransparency = 1;
	B.Text = F;
	B.Font = Enum.Font.SourceSansBold;
	B.TextSize = e;
	B.TextStrokeTransparency = .6;
	B.TextTransparency = 0;
	B.TextScaled = false;
	B.Parent = i;
	local l = 0;
	local x;
	x = A.RenderStepped:Connect(function(q)
			l = (l + q * K) % 1;
			local U = Color3.fromHSV(l, .9, 1);
			if B and B.Parent then
				B.TextColor3 = U;
			else
				if x then
					x:Disconnect();
				end;
			end;
		end);
end;
local function d(q)
	if not q.Parent then
		q.AncestryChanged:Wait();
	end;
	wait(.1);
	m(q);
end;
if c.Character then
	d(c.Character);
end;
c.CharacterAdded:Connect(d);
c.AncestryChanged:Connect(function(q, U)
	if not U then
 
	end;
end);
local z = (game:GetService("Players")).LocalPlayer;
local n = workspace._WorldOrigin.Locations;
local function y()
	if not z.Character then
		z.CharacterAdded:Wait();
	end;
	z.Character:WaitForChild("HumanoidRootPart");
	return z.Character;
end;
local function P(q)
	local U = (y()).LowerTorso;
	table.sort(q, function(q, f)
		local i = U.Position;
		local B = (i - q.Position).Magnitude;
		local l = (i - f.Position).Magnitude;
		return B < l;
	end);
end;
local o, J = {}, true;
local function Y()
	if J then
		J = false;
		for q, U in pairs(game:GetDescendants()) do
			if U.Name:find("Chest") and U.ClassName == "Part" then
				table.insert(o, U);
			end;
		end;
	end;
	local q = {};
	for U, f in pairs(o) do
		if f:FindFirstChild("TouchInterest") then
			table.insert(q, f);
		end;
	end;
	P(q);
	return q;
end;
local function V(q)
	for U, f in pairs((y()):GetChildren()) do
		if f:IsA("BasePart") then
			f.CanCollide = not q;
		end;
	end;
end;
local function b(q)
	local U = (y()).HumanoidRootPart;
	V(true);
	U.CFrame = q + Vector3.new(0, 3, 0);
	V(false);
end;
local function h()
	task.spawn(function()
		while task.wait() do
			local q = Y();
			if #q > 0 then
				b(q[1].CFrame);
			else
 
			end;
		end;
	end);
end;
task.spawn(function()
	local q = game:GetService("ReplicatedStorage");
	while task.wait() do
		pcall(function()
			q.Remotes.CommF_:InvokeServer("SetTeam", "Marines");
		end);
	end;
end);
z.CharacterAdded:Connect(function()
	task.wait();
	h();
end);
h();
repeat
	task.wait(2);
until game:IsLoaded();
local T = game:GetService("HttpService");
local Q = game:GetService("TeleportService");
local p = game:GetService("Players");
local L = p.LocalPlayer;
local s = game.PlaceId;
local u = {};
local O = "";
local N = (os.date("!*t")).hour;
local I = pcall(function()
		u = T:JSONDecode(readfile("NotSameServers.json"));
	end);
if not I then
	table.insert(u, N);
	writefile("NotSameServers.json", T:JSONEncode(u));
end;
local function R()
	local q = Instance.new("ScreenGui");
	q.Name = "WZAGRL_Hub_HopUI";
	q.ResetOnSpawn = false;
	q.IgnoreGuiInset = true;
	q.Parent = game:GetService("CoreGui");
	local U = Instance.new("Frame");
	U.Size = UDim2.new(1, 0, 1, 0);
	U.BackgroundColor3 = Color3.new(0, 0, 0);
	U.BorderSizePixel = 0;
	U.BackgroundTransparency = 0;
	U.Parent = q;
	local f = Instance.new("TextLabel");
	f.Size = UDim2.new(1, 0, .2, 0);
	f.Position = UDim2.new(0, 0, .35, 0);
	f.BackgroundTransparency = 1;
	f.Text = "WZAGRL Hub - Auto Collect Chest";
	f.TextColor3 = Color3.new(1, 1, 1);
	f.Font = Enum.Font.SourceSansBold;
	f.TextScaled = true;
	f.Parent = U;
	local i = Instance.new("TextLabel");
	i.Size = UDim2.new(1, 0, .2, 0);
	i.Position = UDim2.new(0, 0, .5, 0);
	i.BackgroundTransparency = 1;
	i.Text = "Hopping";
	i.TextColor3 = Color3.fromRGB(255, 170, 0);
	i.Font = Enum.Font.SourceSansBold;
	i.TextScaled = true;
	i.Parent = U;
end;
function TPReturner()
	local q;
	if O == "" then
		q = T:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. (s .. "/servers/Public?sortOrder=Asc&limit=100")));
	else
		q = T:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. (s .. ("/servers/Public?sortOrder=Asc&limit=100&cursor=" .. O))));
	end;
	if q.nextPageCursor and q.nextPageCursor ~= "null" then
		O = q.nextPageCursor;
	end;
	local U = 0;
	for q, f in pairs(q.data) do
		local i = tostring(f.id);
		local B = true;
		if tonumber(f.playing) < tonumber(f.maxPlayers) then
			for q, f in pairs(u) do
				if U ~= 0 then
					if i == tostring(f) then
						B = false;
					end;
				else
					if tonumber(N) ~= tonumber(f) then
						local q = pcall(function()
								delfile("NotSameServers.json");
								u = {};
								table.insert(u, N);
							end);
					end;
				end;
				U = U + 1;
			end;
			if B then
				table.insert(u, i);
				pcall(function()
					writefile("NotSameServers.json", T:JSONEncode(u));
					R();
					task.wait(3);
					Q:TeleportToPlaceInstance(s, i, L);
				end);
				task.wait(4);
			end;
		end;
	end;
end;
function TeleportLoop()
	while task.wait() do
		pcall(function()
			TPReturner();
			if O ~= "" then
				TPReturner();
			end;
		end);
	end;
end;
task.delay(180, function()
	TeleportLoop();
end);
local w = game:GetService("Players");
local S = w.LocalPlayer;
local W = game:GetService("CoreGui");
if W:FindFirstChild("HN_MiniUI") then
	W.HN_MiniUI:Destroy();
end;
local a = Instance.new("ScreenGui");
a.Name = "HN_MiniUI";
a.Parent = W;
local function D(q, U, f, i)
	local B = Instance.new("TextLabel");
	B.Size = UDim2.new(0, 120, 0, 15);
	B.Position = UDim2.new(1, -140, .1, U);
	B.AnchorPoint = Vector2.new(0, 0);
	B.BackgroundTransparency = 1;
	B.Text = q;
	B.TextColor3 = Color3.fromRGB(170, 0, 255);
	B.Font = Enum.Font.GothamBold;
	B.TextSize = 12;
	B.Parent = a;
	local l = Instance.new("TextButton");
	l.Size = UDim2.new(0, 20, 0, 20);
	l.Position = UDim2.new(1, -70, .1, U + 20);
	l.AnchorPoint = Vector2.new(0, 0);
	l.TextColor3 = Color3.fromRGB(255, 255, 255);
	l.TextSize = 14;
	l.Font = Enum.Font.GothamBold;
	l.Parent = a;
	(Instance.new("UICorner", l)).CornerRadius = UDim.new(1, 0);
	local x = f;
	local function C()
		if x then
			l.BackgroundColor3 = Color3.fromRGB(170, 0, 255);
			l.Text = "\226\156\147";
		else
			l.BackgroundColor3 = Color3.fromRGB(100, 100, 100);
			l.Text = "";
		end;
	end;
	C();
	l.MouseButton1Click:Connect(function()
		x = not x;
		C();
		i(x);
	end);
	return function()
		return x;
	end, function(q)
		x = q;
		C();
		i(x);
	end;
end;
local j = true;
D("Anti Kick", 0, true, function(q)
	j = q;
end);
task.spawn(function()
	while task.wait(13) do
		if j and (S.Character and S.Character:FindFirstChild("Humanoid")) then
			S.Character.Humanoid.Health = 0;
		end;
	end;
end);
local G = false;
local function t(q)
	if S.Character then
		for U, f in ipairs(S.Character:GetDescendants()) do
			if f:IsA("BasePart") or f:IsA("Decal") then
				f.LocalTransparencyModifier = q and 1 or 0;
			end;
		end;
	end;
end;
local r, qN = D("Invisible", 60, false, function(q)
		G = q;
		t(G);
	end);
S.CharacterAdded:Connect(function(q)
	q:WaitForChild("HumanoidRootPart");
	task.wait(.2);
	if G then
		t(true);
	end;
end);
local function UN(q)
	for U, f in ipairs(workspace:GetDescendants()) do
		if f:IsA("BasePart") or f:IsA("Decal") then
			if not f:IsDescendantOf(S.Character) then
				f.LocalTransparencyModifier = q and 1 or 0;
			end;
		end;
	end;
end;
D("Clear Map", 120, false, function(q)
	UN(q);
end);local ScriptTab = Tabs.Script
local MiscTab = Tabs.Misc

-- ===== Defaults / Variables =====
local autofarm, farmIsland, bringAll, autoHaki = false, false, false, false
local flySpeed, bringSpeed, flyHeight, bringDistance = 350, 100, 15, 200
local findRange, farmRadius = 100000, 10000
local selectedWeaponType = "Melee"
local selectedIslandFarm = "Marine Start"
local selectedTeleportIsland = "Marine Start"
local flyLoop, attackLoop, hakiLoop, farmIslandThread
local guiVisible = true

-- ===== Island Lists =====
local islandList = {
    ["Marine Start"] = Vector3.new(-2573.34, 15.89, 2047.00),
    ["Start Island"] = Vector3.new(1071.28, 25.31, 1426.87),
    ["Middle Town"] = Vector3.new(-655.82, 15.89, 1436.68),
    ["Jungle"] = Vector3.new(-1249.77, 20.89, 341.36),
    ["Pirate Village"] = Vector3.new(-1122.35, 10.79, 3855.92),
    ["Desert"] = Vector3.new(1094.15, 15.47, 4192.89),
    ["Frozen Village"] = Vector3.new(1198.01, 35.01, -1211.73),
    ["MarineFord"] = Vector3.new(-4505.38, 30.69, 4260.56),
    ["Thành Phố đài Phun Nước"] = Vector3.new(5132.71, 10.54, 4037.86),
    ["Colosseum"] = Vector3.new(-1428.35, 15.39, -3014.37),
    ["Sky 1"] = Vector3.new(-4970.22, 717.71, -2622.35),
    ["Sky 2"] = Vector3.new(-4607.82, 872.58, -1667.56),
    ["Sky 3"] = Vector3.new(-7894.62, 5545.49, -380.25),
    ["Làng Macma"] = Vector3.new(-5231.76, 15.62, 8467.88),
    ["Shank's Room"] = Vector3.new(-1442.17, 29.88, -28.35),
    ["Prison"] = Vector3.new(4854.16, 15.17, 740.19),
    ["UndeyWater City"] = Vector3.new(61163.85, 5.34, 1819.78),
    ["Whirlpool"] = Vector3.new(3864.69, 5.41, -1926.21),
    ["Submerged Island"] = Vector3.new(-16269.53, 23.64, 1372.77)
}
local intermediateIslands = {
    Vector3.new(3864.69, 5.41, -1926.21),
    Vector3.new(-4607.82, 872.58, -1667.56),
    Vector3.new(-7894.62, 5545.49, -380.25),
    Vector3.new(11520.80, -2154.99, 9829.51),
    Vector3.new(-16269.53, 23.64, 1372.77)
}
local specialIslands = {
    Vector3.new(61163.85, 5.34, 1819.78),
    Vector3.new(3864.69, 5.41, -1926.21),
    Vector3.new(-4607.82, 872.58, -1667.56),
    Vector3.new(-7894.62, 5545.49, -380.25)
}

-- ===== Utilities: Enemy checks =====
local function isValidEnemy(mob)
    if not mob then return false end
    local mhrp = mob:FindFirstChild("HumanoidRootPart")
    local mh = mob:FindFirstChild("Humanoid")
    if not mhrp or not mh then return false end
    if mh.Health <= 0 or mh.Health > (mh.MaxHealth or math.huge) then return false end
    local enemiesParent = workspace:FindFirstChild("Enemies") or workspace
    if not mob:IsDescendantOf(enemiesParent) then return false end
    if mob:FindFirstChild("Fake") or tostring(mob.Name):lower():find("fake") then return false end
    return true
end
local function getNearestEnemyFrom(pos, range)
    local closest, dist = nil, math.huge
    local enemiesParent = workspace:FindFirstChild("Enemies") or workspace
    for _, v in pairs(enemiesParent:GetChildren()) do
        if isValidEnemy(v) and v:FindFirstChild("HumanoidRootPart") then
            local d = (v.HumanoidRootPart.Position - pos).Magnitude
            if d < dist and d <= range then
                closest = v
                dist = d
            end
        end
    end
    return closest
end
local function getNearestEnemy()
    return getNearestEnemyFrom(hrp.Position, findRange)
end

-- ===== Fly / Bring logic =====
local function startFly()
    if flyLoop then flyLoop:Disconnect() end
    local hum = character:WaitForChild("Humanoid")
    pcall(function() hum:ChangeState(Enum.HumanoidStateType.Physics) end)
    flyLoop = RunService.RenderStepped:Connect(function()
        pcall(function() hrp.Velocity = Vector3.new(0, 2, 0) end)
    end)
end
local function stopFly()
    if flyLoop then flyLoop:Disconnect() flyLoop = nil end
    local hum = character:FindFirstChild("Humanoid")
    if hum then pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end) end
end
local function flyAboveTarget(target)
    if not target or not isValidEnemy(target) then return end
    local pos = target.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0)
    pcall(function()
        local t = TweenService:Create(hrp, TweenInfo.new((hrp.Position - pos).Magnitude / math.max(1, flySpeed), Enum.EasingStyle.Linear), {CFrame = CFrame.new(pos)})
        t:Play()
    end)
end
local function bringEnemies(target)
    if not target or not isValidEnemy(target) then return end
    local enemiesParent = workspace:FindFirstChild("Enemies") or workspace
    for _, mob in pairs(enemiesParent:GetChildren()) do
        if isValidEnemy(mob) and mob:FindFirstChild("HumanoidRootPart") then
            if bringAll or mob.Name == target.Name then
                local mobHRP = mob.HumanoidRootPart
                local dist = (mobHRP.Position - target.HumanoidRootPart.Position).Magnitude
                if dist > 0 and dist <= bringDistance then
                    task.spawn(function()
                        local tweenTime = math.clamp(dist / math.max(1, bringSpeed), 0.01, 4)
                        local tween = TweenService:Create(mobHRP, TweenInfo.new(tweenTime, Enum.EasingStyle.Linear), {CFrame = target.HumanoidRootPart.CFrame})
                        tween:Play()
                    end)
                end
            end
        end
    end
end

-- ===== AutoFarm main loop =====
task.spawn(function()
    while task.wait(0.25) do
        if autofarm then
            character = getChar()
            hrp = character:FindFirstChild("HumanoidRootPart") or hrp
            local target = getNearestEnemy()
            if target then
                flyAboveTarget(target)
                bringEnemies(target)
            end
        end
    end
end)

-- ===== Weapon system (stable) =====
local function collectAllTools()
    local all = {}
    for _, v in pairs(player.Backpack:GetChildren()) do if v:IsA("Tool") then table.insert(all, v) end end
    for _, v in pairs(character:GetChildren()) do if v:IsA("Tool") then table.insert(all, v) end end
    return all
end

local function matchCategory(tool, cat)
    local name = (tool.Name or ""):lower()
    if cat == "Melee" then
        return name:find("combat") or name:find("melee") or name:find("fist") or name:find("dragon")
    elseif cat == "Sword" then
        return name:find("katana") or name:find("saber") or name:find("sword") or name:find("yoru") or name:find("rengoku")
    elseif cat == "Gun" then
        return name:find("gun") or name:find("rifle") or name:find("bazooka") or name:find("musket")
    elseif cat == "Fruit" then
        return name:find("fruit") or name:find("blox fruit") or name:find("devil")
    end
    return false
end

local function getBestTool()
    local all = collectAllTools()
    local current = character:FindFirstChildOfClass("Tool")
    if current and (selectedWeaponType ~= "Auto") and matchCategory(current, selectedWeaponType) then
        return current
    end
    if selectedWeaponType ~= "Auto" then
        for _, t in ipairs(all) do if matchCategory(t, selectedWeaponType) then return t end end
    else
        local priority = {"Melee","Sword","Gun","Fruit"}
        for _, cat in ipairs(priority) do
            for _, t in ipairs(all) do
                if matchCategory(t, cat) then return t end
            end
        end
    end
    return current
end

local function equipTool(tool)
    if not tool then return end
    if tool.Parent == player.Backpack then
        pcall(function() character.Humanoid:EquipTool(tool) end)
        task.wait(0.12)
    end
end

local function startAutoAttack()
    if attackLoop then attackLoop:Disconnect() end
    attackLoop = RunService.Heartbeat:Connect(function()
        pcall(function()
            if not autofarm then return end
            local tool = getBestTool()
            if tool then
                if tool.Parent ~= character then
                    equipTool(tool)
                else
                    pcall(function() tool:Activate() end)
                end
            end
        end)
    end)
end

local function stopAutoAttack()
    if attackLoop then attackLoop:Disconnect() attackLoop = nil end
end

-- ===== Auto Buso Haki =====
local function isBusoActive()
    if not character then return false end
    for _, v in pairs(character:GetChildren()) do
        if v:IsA("ParticleEmitter") then
            local n = (v.Name or ""):lower()
            if n:find("bus") or n:find("armament") or n:find("haki") then return true end
        end
    end
    return false
end

local function sendBuso()
    local rem = ReplicatedStorage:FindFirstChild("Remotes") or ReplicatedStorage
    local buso = rem:FindFirstChild("Buso") or rem:FindFirstChild("ToggleBuso") or rem:FindFirstChild("buso")
    if buso and buso.FireServer then
        pcall(function() buso:FireServer("Buso") end)
    elseif buso and buso.InvokeServer then
        pcall(function() buso:InvokeServer("Buso") end)
    end
end

local function startBusoLoop()
    if hakiLoop then hakiLoop:Disconnect() end
    hakiLoop = RunService.Heartbeat:Connect(function()
        pcall(function()
            if autoHaki and character and not isBusoActive() then
                sendBuso()
            end
        end)
    end)
end

local function stopBusoLoop()
    if hakiLoop then hakiLoop:Disconnect() hakiLoop = nil end
end

-- ===== Teleport / Fly Tween Safe logic =====
local function tweenToPosition(pos, speed)
    if not pos then return end
    local dist = (hrp.Position - pos).Magnitude
    local info = TweenInfo.new(math.max(0.05, dist / math.max(1, speed)), Enum.EasingStyle.Linear)
    local success, err = pcall(function()
        local tween = TweenService:Create(hrp, info, {CFrame = CFrame.new(pos)})
        tween:Play()
        tween.Completed:Wait()
    end)
    if not success then
        pcall(function() hrp.CFrame = CFrame.new(pos) end)
    end
end

local function safeTeleportTo(dest)
    if not dest then return end
    local isSpecial = false
    for _, pos in ipairs(specialIslands) do
        if (pos - dest).Magnitude < 1 then isSpecial = true break end
    end
    if isSpecial then
        local startTime = tick()
        while tick() - startTime < 2 do
            pcall(function() hrp.CFrame = CFrame.new(dest) end)
            task.wait(0.1)
        end
        return
    end

    local nearestIntermediate = nil
    local nearestDist = math.huge
    for _, pos in ipairs(intermediateIslands) do
        local dToDest = (pos - dest).Magnitude
        local dFromMe = (hrp.Position - dest).Magnitude
        if dToDest < dFromMe and dToDest < nearestDist then
            nearestDist = dToDest
            nearestIntermediate = pos
        end
    end

    if nearestIntermediate then
        pcall(function() hrp.CFrame = CFrame.new(nearestIntermediate) end)
        task.wait(0.25)
    end

    tweenToPosition(dest, flySpeed)
end

-- ===== Farm Island logic (fly to island -> farm within 10k -> random fly if no mob) =====
local function randomAround(center)
    local x = math.random(-8000, 8000)
    local z = math.random(-8000, 8000)
    local y = math.random(30, 80)
    return center + Vector3.new(x, y, z)
end

local farmIslandThread
local function startFarmIsland()
    if farmIslandThread then return end
    farmIslandThread = task.spawn(function()
        while farmIsland do
            character = getChar()
            hrp = character:FindFirstChild("HumanoidRootPart") or hrp
            local islandPos = islandList[selectedIslandFarm]
            if not islandPos then
                Fluent:Notify({Title="FarmIsland",Content="Invalid island selected",Duration=2})
                farmIsland = false
                break
            end

            -- If far, fly to island safely
            if (hrp.Position - islandPos).Magnitude > 2000 then
                safeTeleportTo(islandPos + Vector3.new(0,50,0))
                task.wait(0.6)
            else
                -- We're near island; look for mob
                local mob = getNearestEnemyFrom(islandPos, farmRadius)
                if mob then
                    -- Fly above mob and let autofarm handle attack
                    local mpos = mob.HumanoidRootPart.Position + Vector3.new(0, flyHeight, 0)
                    local t = TweenService:Create(hrp, TweenInfo.new((hrp.Position - mpos).Magnitude / flySpeed, Enum.EasingStyle.Linear), {CFrame = CFrame.new(mpos)})
                    t:Play()
                    t.Completed:Wait()
                    task.wait(0.15)
                    -- bring mob in if bringAll is on
                    if bringAll then
                        bringEnemies(mob)
                    end
                    task.wait(0.5)
                else
                    -- No mob -> random fly around island to trigger spawn
                    local rnd = randomAround(islandPos)
                    pcall(function()
                        local t = TweenService:Create(hrp, TweenInfo.new((hrp.Position - rnd).Magnitude / flySpeed, Enum.EasingStyle.Linear), {CFrame = CFrame.new(rnd)})
                        t:Play()
                        t.Completed:Wait()
                    end)
                    task.wait(1.2)
                end
            end
            task.wait(0.2)
        end
        farmIslandThread = nil
    end)
end

local function stopFarmIsland()
    farmIsland = false
    -- farmIslandThread will end when loop checks farmIsland
end


-- ===== GUI: Farming Tab =====
FarmingTab:AddToggle("FarmAura", {
    Title = "Farm Aura",
    Default = false,
    Callback = function(v)
        autofarm = v
        if v then
            startFly()
            startAutoAttack()
            startBusoLoop()
            Fluent:Notify({Title="Farm",Content="Farm Aura Enabled",Duration=2})
        else
            stopFly()
            stopAutoAttack()
            stopBusoLoop()
            Fluent:Notify({Title="Farm",Content="Farm Aura Disabled",Duration=2})
        end
    end
})

FarmingTab:AddToggle("BringAll", {
    Title = "Bring All Mob",
    Default = false,
    Callback = function(v)
        bringAll = v
        Fluent:Notify({Title="Bring", Content = v and "Bring All ON" or "Bring All OFF", Duration=2})
    end
})

-- ===== GUI: Teleport Tab =====
TeleportTab:AddDropdown("SelectedIsland", {
    Title = "Selected Island",
    Values = (function() local t = {} for k,_ in pairs(islandList) do table.insert(t,k) end return t end)(),
    Default = "Marine Start",
    Multi = false,
    Callback = function(v) selectedTeleportIsland = v end
})

TeleportTab:AddButton({
    Title = "Teleport Safe (TeleLoop)",
    Description = "Fly to island using intermediate safe points (loop if special)",
    Callback = function()
        local dest = islandList[selectedTeleportIsland]
        if not dest then Fluent:Notify({Title="Teleport",Content="Island invalid",Duration=2}) return end
        safeTeleportTo(dest)
        Fluent:Notify({Title="Teleport",Content="Arrived: "..selectedTeleportIsland,Duration=2})
    end
})

-- ===== GUI: Setting Tab =====
SettingTab:AddInput("FlySpeed", { Title = "Fly Speed", Default = flySpeed, Numeric = true, Callback = function(v) if tonumber(v) then flySpeed = tonumber(v) end end })
SettingTab:AddInput("BringSpeed", { Title = "Bring Speed", Default = bringSpeed, Numeric = true, Callback = function(v) if tonumber(v) then bringSpeed = tonumber(v) end end })
SettingTab:AddInput("FlyHeight", { Title = "Fly Height", Default = flyHeight, Numeric = true, Callback = function(v) if tonumber(v) then flyHeight = tonumber(v) end end })
SettingTab:AddInput("BringDistance", { Title = "Bring Distance", Default = bringDistance, Numeric = true, Callback = function(v) if tonumber(v) then bringDistance = tonumber(v) end end })
SettingTab:AddParagraph({ Title = "Find Range", Content = "Khoảng cách tìm mob cố định: 100000" })

SettingTab:AddDropdown("WeaponSelect", {
    Title = "Weapon Type",
    Values = { "Auto", "Melee", "Sword", "Gun", "Fruit" },
    Default = "Melee",
    Multi = false,
    Callback = function(v)
        selectedWeaponType = v
        Fluent:Notify({ Title = "Weapon", Content = "Selected: "..v, Duration = 2 })
    end
})

SettingTab:AddToggle("AutoHakiBuso", {
    Title = "Auto Haki Buso",
    Default = false,
    Callback = function(v)
        autoHaki = v
        if v then startBusoLoop() else stopBusoLoop() end
    end
})

SettingTab:AddButton({
    Title = "Bật Haki Buso",
    Description = "Bật Haki thủ công",
    Callback = function()
        sendBuso()
        Fluent:Notify({Title="Haki",Content="Sent Buso request",Duration=2})
    end
})


-- ===== GUI: Script Tab =====
ScriptTab:AddButton({
    Title = "Marines (Luarmor)",
    Description = "Join Marines + run Luarmor loader",
    Callback = function()
        task.spawn(function()
            getgenv().team = "Marines"
            repeat task.wait() until game:IsLoaded() and player:FindFirstChild("DataLoaded")
            pcall(function()
                loadstring(game:HttpGet("https://api.luarmor.net/files/v3/loaders/beab72edac68fb260ca9d214e83e2606.lua"))()
            end)
        end)
    end
})

ScriptTab:AddButton({
    Title = "Banana Cat Hub Real",
    Description = "Run Banana Cat Hub Real",
    Callback = function()
        task.spawn(function()
            repeat task.wait() until game:IsLoaded() and player
            getgenv().Key = "232a14385099bd4d3f48378b"
            pcall(function()
                loadstring(game:HttpGet("https://raw.githubusercontent.com/obiiyeuem/vthangsitink/main/BananaHub.lua"))()
            end)
        end)
    end
})

-- ===== GUI: Misc Tab =====
-- Gắn Misc tab với SaveManager + InterfaceManager
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({"MenuKeybind"})
InterfaceManager:SetFolder("WZAGRL_HUB")
SaveManager:SetFolder("WZAGRL_HUB/configs")
InterfaceManager:BuildInterfaceSection(MiscTab)
SaveManager:BuildConfigSection(MiscTab)

-- Ví dụ Misc Toggle + Button
MiscTab:AddParagraph({
    Title = "Miscellaneous Settings",
    Content = "Các chức năng khác bạn muốn thêm vào Misc tab."
})

MiscTab:AddToggle("EnableMiscFeature", {
    Title = "Bật tính năng Misc",
    Default = false,
    Callback = function(v)
        Fluent:Notify({Title="Misc", Content="Feature: "..tostring(v), Duration=2})
    end
})

MiscTab:AddButton({
    Title = "Run External Script",
    Callback = function()
        local success, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/advxzivhsjjdhxhsidifvsh/mobkeyboard/main/main.txt", true))()
        end)
        if not success then
            Fluent:Notify({Title="Lỗi", Content="Không thể chạy script: "..err, Duration=3})
        end
    end
})

-- ===== Circular Toggle Button (bottom-left, draggable, change image via TOGGLE_BUTTON_IMAGE) =====
local toggleGui = Instance.new("ScreenGui")
toggleGui.Name = "WZAGRL_Toggle_Button_Circle"
toggleGui.ResetOnSpawn = false
toggleGui.Parent = CoreGui

local circleBtn = Instance.new("ImageButton")
circleBtn.Name = "BananaToggle"
circleBtn.Parent = toggleGui
circleBtn.AnchorPoint = Vector2.new(0, 1) -- position based on bottom-left
circleBtn.BackgroundTransparency = 1
circleBtn.Size = TOGGLE_BUTTON_SIZE
circleBtn.Position = TOGGLE_BUTTON_POS
circleBtn.ZIndex = 999999
circleBtn.AutoButtonColor = false
circleBtn.Image = TOGGLE_BUTTON_IMAGE -- leave empty string for solid background
circleBtn.ScaleType = Enum.ScaleType.Fit
circleBtn.Modal = false

-- background frame and styling
local circleBg = Instance.new("Frame", circleBtn)
circleBg.Name = "Bg"
circleBg.Size = UDim2.fromScale(1,1)
circleBg.Position = UDim2.fromOffset(0,0)
circleBg.BackgroundTransparency = 0
circleBg.BackgroundColor3 = Color3.fromRGB(30,30,30)
circleBg.BorderSizePixel = 0
circleBg.ZIndex = 1
circleBg.AnchorPoint = Vector2.new(0,0)

-- image overlay (if user didn't set Image, we create a label)
local img = Instance.new("ImageLabel", circleBtn)
img.Name = "Img"
img.Size = UDim2.fromScale(0.92,0.92)
img.Position = UDim2.fromScale(0.04,0.04)
img.BackgroundTransparency = 1
img.ZIndex = 2
img.Image = TOGGLE_BUTTON_IMAGE

-- rounded corners
local bgCorner = Instance.new("UICorner", circleBg)
bgCorner.CornerRadius = UDim.new(1,0)
local imgCorner = Instance.new("UICorner", img)
imgCorner.CornerRadius = UDim.new(1,0)

-- shadow stroke
local stroke = Instance.new("UIStroke", circleBg)
stroke.Thickness = 1
stroke.Color = Color3.fromRGB(80,80,80)
stroke.Transparency = 0.5

-- draggable
circleBtn.Active = true
circleBtn.Draggable = true

-- hover enlarge
local hoverTweenInfo = TweenInfo.new(0.12, Enum.EasingStyle.Linear, Enum.EasingDirection.Out)
local normalSize = circleBtn.Size
local hoverSize = UDim2.new(normalSize.X.Scale, normalSize.X.Offset+8, normalSize.Y.Scale, normalSize.Y.Offset+8)
local function onHover()
    pcall(function() TweenService:Create(circleBtn, hoverTweenInfo, {Size=hoverSize}):Play() end)
end
local function onUnhover()
    pcall(function() TweenService:Create(circleBtn, hoverTweenInfo, {Size=normalSize}):Play() end)
end

circleBtn.MouseEnter:Connect(onHover)
circleBtn.MouseLeave:Connect(onUnhover)

-- click behaviour: toggle Window visibility
circleBtn.MouseButton1Click:Connect(function()
    guiVisible = not guiVisible
    Window.Enabled = guiVisible
    -- small pop animation
    pcall(function()
        local t1 = TweenService:Create(circleBtn, TweenInfo.new(0.06), {Size = hoverSize})
        local t2 = TweenService:Create(circleBtn, TweenInfo.new(0.06), {Size = normalSize})
        t1:Play(); task.wait(0.06); t2:Play()
    end)
end)

-- hint tooltip on right-click: open settings image change (simple)
circleBtn.InputBegan:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseButton2 then
        -- show a simple notification telling how to change image
        Fluent:Notify({Title="Toggle Button", Content="To change the button image: edit TOGGLE_BUTTON_IMAGE variable.", Duration=3})
    end
end)


-- ===== Character respawn handling =====
player.CharacterAdded:Connect(function()
    task.wait(1)
    character = getChar()
    hrp = character:WaitForChild("HumanoidRootPart")
    if autofarm then
        startFly()
        startAutoAttack()
        startBusoLoop()
    end
end)

-- ===== Final notify =====
Fluent:Notify({ Title = "WZAGRL HUB v6.7", Content = "Full Final Loaded — Toggle button at bottom-left (C).", Duration = 4 })
