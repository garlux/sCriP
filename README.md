-- // Constants \\ --
-- [ Services ] --
local Services = setmetatable({}, {__index = function(Self, Index)
local NewService = game.GetService(game, Index)
if NewService then
Self[Index] = NewService
end
return NewService
end})

-- [ Modules ] --
local UserInterface = loadstring(game:HttpGet("https://raw.githubusercontent.com/icuck/collection-dump/main/AbstractUI", true))()
local Drawing = loadstring(game:HttpGet("https://raw.githubusercontent.com/iHavoc101/Genesis-Studios/main/Modules/DrawingAPI.lua", true))()

local ToolTip = require(Services.ReplicatedStorage.Modules_client.TooltipModule)

-- [ LocalPlayer ] --
local LocalPlayer = Services.Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- [ Raycast Parameters ] --
local RaycastParameters = RaycastParams.new()
RaycastParameters.IgnoreWater = true
RaycastParameters.FilterType = Enum.RaycastFilterType.Blacklist
RaycastParameters.FilterDescendantsInstances = {LocalPlayer.Character}

-- // Variables \\ --
-- [ Info ] --
local Info = {
   SilentAIMEnabled = false;
   TriggeredEnabled = false;
   ArmsCheckEnabled = true;
   TeamWhitelist = "";
   FieldOfView = 250;
}

local LastArrest = time()

-- [ Interface ] --
local FOVCircle = Drawing.new("Circle", {
   Thickness = 2.5,
   Color = Color3.fromRGB(200, 200, 200),
   NumSides = 25,
   Radius = _G.FOV
})

local Target = Drawing.new("Triangle", {
   Thickness = 5,
   Color = Color3.fromRGB(0, 200, 255)
})

-- [ Weapons ] --
local Weapons = {
   "Remington 870";
   "AK-47";
   "M9";
   "M4A1";
   "Hammer";
   "Crude Knife";
}

-- [ Metatable ] --
local RawMetatable = getrawmetatable(game)
local __NameCall = RawMetatable.__namecall
local __Index = RawMetatable.__index


-- // Functions \\ --
local function ValidCharacter(Character)
   return Character and (Character.FindFirstChildWhichIsA(Character, "Humanoid") and Character.FindFirstChildWhichIsA(Character, "Humanoid").Health ~= 0) or false
end

local function NotObstructing(Destination, Ancestor)
   -- [ Camera ] --
   local ObstructingParts = Camera.GetPartsObscuringTarget(Camera, {Destination}, {Ancestor, LocalPlayer.Character})

   for i,v in ipairs(ObstructingParts) do
       pcall(function()
           if v.Transparency >= 1 then
               table.remove(ObstructingParts, i)
           end
       end)
   end

   if #ObstructingParts <= 0 then
       return true
   end

   -- [ Raycast ] --
   RaycastParameters.FilterDescendantsInstances = {LocalPlayer.Character}

   local Origin = Camera.CFrame.Position
   local Direction = (Destination - Origin).Unit * 500
   local RayResult = workspace.Raycast(workspace, Origin, Direction, RaycastParameters) or {
       Instance = nil;
       Position = Origin + Direction;
       Material = Enum.Material.Air;
   }
"-- [DIO's Time Stop Script] --

-- Based on the main ability of DIO's Stand, The World, from Part 3 of JoJo's Bizarre Adventure.

-- MOST of everything in this script is written by Tsuagon, except for the screenshake part, that was taken from a tutorial. Credit to the people to made said tutorials and scripts.

----------------------------------------------------------------------------------------------------

-- Player Stuff --

local player = game:GetService("Players").LocalPlayer
local char = player.Character
local head = char.Head
local torso = char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
local root = char.HumanoidRootPart
local humanoid = char.Humanoid

local playerhealth = humanoid.Health

local mouse = player:GetMouse()

----------------------------------------------------------------------------------------------------

-- General Stuff --

local tween = game:GetService("TweenService")
local lighting = game:GetService("Lighting")
local input = game:GetService("UserInputService")

local frozenobjectstable = {}

seconds = 10 -- You can change the length in which time is stopped for by modifying this number.

stoppedtime = false

-- Time Stop Effect

local timestopeffect = Instance.new("ColorCorrectionEffect")
timestopeffect.Parent = lighting
timestopeffect.Saturation = 0
timestopeffect.Contrast = 0
timestopeffect.Enabled = true

-- Sphere Effect

local timestopsphere1 = Instance.new("Part")
timestopsphere1.Parent = torso
timestopsphere1.Material = Enum.Material.ForceField
timestopsphere1.Size = Vector3.new(0, 0, 0)
timestopsphere1.Shape = Enum.PartType.Ball
timestopsphere1.CanCollide = false
timestopsphere1.Massless = true
timestopsphere1.Color = Color3.new(1, 1, 1)
timestopsphere1.CastShadow = false

local sphere1weld = Instance.new("Weld")
sphere1weld.Part0 = timestopsphere1
sphere1weld.Part1 = torso
sphere1weld.C0 = torso.CFrame
sphere1weld.C1 = torso.CFrame
sphere1weld.Parent = timestopsphere1

local timestopsphere2 = Instance.new("Part")
timestopsphere2.Parent = torso
timestopsphere2.Material = Enum.Material.ForceField
timestopsphere2.Size = Vector3.new(0, 0, 0)
timestopsphere2.Shape = Enum.PartType.Ball
timestopsphere2.CanCollide = false
timestopsphere2.Massless = true
timestopsphere2.Color = Color3.new(1, 1, 1)
timestopsphere2.CastShadow = false

local sphere2weld = Instance.new("Weld")
sphere2weld.Part0 = timestopsphere2
sphere2weld.Part1 = torso
sphere2weld.C0 = torso.CFrame
sphere2weld.C1 = torso.CFrame
sphere2weld.Parent = timestopsphere2

local timestopsphere3 = Instance.new("Part")
timestopsphere3.Parent = torso
timestopsphere3.Material = Enum.Material.ForceField
timestopsphere3.Size = Vector3.new(0, 0, 0)
timestopsphere3.Shape = Enum.PartType.Ball
timestopsphere3.CanCollide = false
timestopsphere3.Massless = true
timestopsphere3.Color = Color3.new(1, 1, 1)
timestopsphere3.CastShadow = false

local sphere3weld = Instance.new("Weld")
sphere3weld.Part0 = timestopsphere3
sphere3weld.Part1 = torso
sphere3weld.C0 = torso.CFrame
sphere3weld.C1 = torso.CFrame
sphere3weld.Parent = timestopsphere3

-- Sounds

local timestopvoiceline = Instance.new("Sound", head)
timestopvoiceline.SoundId = "rbxassetid://7514417921"
timestopvoiceline.Volume = 5

local injuredtimestopvoiceline = Instance.new("Sound", head)
injuredtimestopvoiceline.SoundId = "rbxassetid://6043864223"
injuredtimestopvoiceline.Volume = 5

local tssfx = Instance.new("Sound", head) 
tssfx.SoundId = "rbxassetid://5679636294"
tssfx.Volume = 5

local timeresumevoiceline = Instance.new("Sound", head)
timeresumevoiceline.SoundId = "rbxassetid://4329802996"
timeresumevoiceline.Volume = 5

local injuredtimeresumevoiceline = Instance.new("Sound", head)
injuredtimeresumevoiceline.SoundId = "rbxassetid://6043853981"
injuredtimeresumevoiceline.Volume = 5

local countervoiceline = Instance.new("Sound", head)
countervoiceline.SoundId = "rbxassetid://6675048510"
countervoiceline.Volume = 5

local countersfx = Instance.new("Sound", head)
countersfx.SoundId = "rbxassetid://4572672240"
countersfx.Volume = 4.5

local tsteleportsfx = Instance.new("Sound", torso)
tsteleportsfx.SoundId = "rbxassetid://3077287610"
tsteleportsfx.Volume = 4.5

local clock = Instance.new("Sound", workspace)
clock.SoundId = "rbxassetid://4940109913"
clock.Volume = 4.5
clock.Looped = true

----------------------------------------------------------------------------------------------------

-- Main Script --

function timestop()
	if stoppedtime == true then return end
	if humanoid.Health < 50 then
		injuredtimestopvoiceline:Play()
		wait(1)
		tssfx:Play()
	elseif humanoid.Health > 50 then
		timestopvoiceline:Play()
		wait(1.6)
	end
        settings().Network.IncomingReplicationLag = math.huge
	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") then
			if not v.Anchored == true then
				if not v:IsDescendantOf(char) then
					v.Anchored = true
					table.insert(frozenobjectstable, v)
				end
			end
		end
	end
	coroutine.resume(coroutine.create(function()
		coroutine.resume(coroutine.create(function()
			timestopeffect.Enabled = true
			tween:Create(workspace.CurrentCamera, TweenInfo.new(1.5, Enum.EasingStyle.Exponential), {FieldOfView = 250}):Play();
			coroutine.resume(coroutine.create(function()
				while stoppedtime == false do
					tween:Create(timestopeffect, TweenInfo.new(0.8, Enum.EasingStyle.Quart), {Contrast = -2}):Play();
					wait(0.3)
					tween:Create(timestopeffect, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {Saturation = -1}):Play();
					wait(0.2)
					tween:Create(timestopeffect, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {Saturation = -2}):Play();
					wait()
					tween:Create(timestopeffect, TweenInfo.new(0.8, Enum.EasingStyle.Quart), {Contrast = -2.4}):Play();
					wait(0.3)
					tween:Create(timestopeffect, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {Saturation = 1}):Play();
					wait(0.2)
					tween:Create(timestopeffect, TweenInfo.new(0.5, Enum.EasingStyle.Quart), {Saturation = -2}):Play();
				end
			end))
			wait(1.7)
			tween:Create(workspace.CurrentCamera, TweenInfo.new(1, Enum.EasingStyle.Quart), {FieldOfView = 70}):Play();
			tween:Create(timestopeffect, TweenInfo.new(1, Enum.EasingStyle.Quart), {Contrast = 0}):Play();
			tween:Create(timestopeffect, TweenInfo.new(1, Enum.EasingStyle.Quart), {Saturation = -0.8}):Play();
		end))
		coroutine.resume(coroutine.create(function()
			for _ = 1, 65 do
				wait()
				local offset1 = math.random(-650, 650) / 700
				local offset2 = math.random(-650, 650) / 700
				local offset3 = math.random(-650, 650) / 700
				tween:Create(humanoid, TweenInfo.new(0.1), {CameraOffset = Vector3.new(offset1, offset2, offset3)}):Play();
			end
			tween:Create(humanoid, TweenInfo.new(0.1), {CameraOffset = Vector3.new(0, 0, 0)}):Play();
		end))
		coroutine.resume(coroutine.create(function()
			tween:Create(timestopsphere1, TweenInfo.new(1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(45, 45, 45)}):Play();
			tween:Create(timestopsphere2, TweenInfo.new(1.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(40, 40, 40)}):Play();
			tween:Create(timestopsphere3, TweenInfo.new(1.7, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(35, 35, 35)}):Play();
			wait(1.7)
			tween:Create(timestopsphere1, TweenInfo.new(1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(0, 0, 0)}):Play();
			tween:Create(timestopsphere2, TweenInfo.new(1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(0, 0, 0)}):Play();
			tween:Create(timestopsphere3, TweenInfo.new(1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out, 0), {Size = Vector3.new(0, 0, 0)}):Play();
		end))
		coroutine.resume(coroutine.create(function()
			for _, v in pairs(workspace:GetDescendants()) do
				if v:IsA("ParticleEmitter") then
					tween:Create(v, TweenInfo.new(3), {TimeScale = 0}):Play();
				end
			end
			for _, v in pairs(workspace:GetDescendants()) do
				if v:IsA("Fire") then
					tween:Create(v, TweenInfo.new(3), {TimeScale = 0}):Play();
				end
			end
		end))
		coroutine.resume(coroutine.create(function()
			for _, v in pairs(workspace:GetDescendants()) do
				if v:IsA("Sound") then
					if not v:IsDescendantOf(char) then
						tween:Create(v, TweenInfo.new(4), {PlaybackSpeed = 0}):Play();
					end
				end
			end
		end))
	end))
	stoppedtime = true
	wait(seconds)
	timeresume()
end

function timeresume()
	if stoppedtime == false then return end
	if humanoid.Health < 50 then
		injuredtimeresumevoiceline:Play()
		wait(0.6)
	elseif humanoid.Health > 50 then
		timeresumevoiceline:Play()
		wait(0.9)
	end
        settings().Network.IncomingReplicationLag = 0
	for _, v in pairs(frozenobjectstable) do
		if v:IsA("BasePart") then
			v.Anchored = false
		end
	end
	coroutine.resume(coroutine.create(function()
		for _, v in pairs(workspace:GetDescendants()) do
			if v:IsA("ParticleEmitter") then
				tween:Create(v, TweenInfo.new(3), {TimeScale = 1}):Play();
			end
		end
		for _, v in pairs(workspace:GetDescendants()) do
			if v:IsA("Fire") then
				tween:Create(v, TweenInfo.new(3), {TimeScale = 1}):Play();
			end
		end
	end))
	coroutine.resume(coroutine.create(function()
		for _, v in pairs(workspace:GetDescendants()) do
			if v:IsA("Sound") then
				if not v:IsDescendantOf(char) then
					tween:Create(v, TweenInfo.new(2), {PlaybackSpeed = 1}):Play();
				end
			end
		end
	end))
	tween:Create(timestopeffect, TweenInfo.new(2, Enum.EasingStyle.Quart), {Saturation = 0}):Play();
	stoppedtime = false
end

function tsteleport()
	if stoppedtime == true then return end
	tsteleportsfx:Play()
	coroutine.resume(coroutine.create(function()
		tween:Create(timestopeffect, TweenInfo.new(0.1), {TintColor = Color3.new(0, 0, 0)}):Play();
		wait(0.2)
		tween:Create(timestopeffect, TweenInfo.new(0.1), {TintColor = Color3.new(1, 1, 1)}):Play();
	end))
	root.CFrame = CFrame.new(mouse.Hit.p.X, mouse.Hit.p.Y, mouse.Hit.p.Z)
end

--[[function counter()
	countervoiceline:Play()
	wait(0.6)
	humanoid.HealthChanged:Connect(function(health)
		if playerhealth > 100 then
			countersfx:Play()
			root.CFrame = root.CFrame + root.CFrame.lookVector * -20
		elseif health == playerhealth then return end
	end)
end]]--

mouse.KeyDown:Connect(function(key)
	if key == "f" and stoppedtime == false then
		timestop()
	elseif key == "f" and stoppedtime == true then
		timeresume()
	end
	if key == "v" and stoppedtime == false then
		tsteleport()
	end
end)
   if RayResult.Instance and (RayResult.Instance.IsDescendantOf(RayResult.Instance, Ancestor) or RayResult.Instance == Ancestor) then
       return true
   end

   -- [ Obstructed ] --
   return false
end

local function IsArmed(Player)
   for i,v in ipairs(Weapons) do
       local Tool = Player.Backpack.FindFirstChild(Player.Backpack, v) or Player.Character.FindFirstChild(Player.Character, v)
       if Tool then
           return true
       end
   end
   return false
end

local function ClosestPlayerToCursor(Distance)
   local Closest = nil
   local Position = nil
   local ShortestDistance = Distance or math.huge

   local MousePosition = Services.UserInputService.GetMouseLocation(Services.UserInputService)

   for i, v in ipairs(Services.Players.GetPlayers(Services.Players)) do
       if v ~= LocalPlayer and (v.Team ~= LocalPlayer.Team and tostring(v.Team) ~= Info.TeamWhitelist) and ValidCharacter(v.Character) then
           if Info.ArmsCheckEnabled and (v.Team == Services.Teams.Inmates and IsArmed(v) == false) then
               continue
           end

           local ViewportPosition, OnScreen = Camera.WorldToViewportPoint(Camera, v.Character.PrimaryPart.Position)
           local Magnitude = (Vector2.new(ViewportPosition.X, ViewportPosition.Y) - MousePosition).Magnitude

           if OnScreen == false or NotObstructing(v.Character.PrimaryPart.Position, v.Character) == false then
               continue
           end

           if Magnitude < ShortestDistance  then
               Closest = v
               Position = ViewportPosition
               ShortestDistance = Magnitude
           end
       end
   end

   return Closest, Position
end

local function SwitchGuns()
   if LocalPlayer.Character.FindFirstChild(LocalPlayer.Character, "Remington 870") then
       local Tool = LocalPlayer.Backpack.FindFirstChild(LocalPlayer.Backpack, "M4A1") or LocalPlayer.Backpack.FindFirstChild(LocalPlayer.Backpack, "AK-47") or LocalPlayer.Backpack.FindFirstChild(LocalPlayer.Backpack, "M9")

       local Humanoid = LocalPlayer.Character.FindFirstChildWhichIsA(LocalPlayer.Character, "Humanoid")
       Humanoid.EquipTool(Humanoid, Tool)
   else
       local Tool = LocalPlayer.Backpack.FindFirstChild(LocalPlayer.Backpack, "Remington 870")

       local Humanoid = LocalPlayer.Character.FindFirstChildWhichIsA(LocalPlayer.Character, "Humanoid")
       Humanoid.EquipTool(Humanoid, Tool)
   end
end

local function Crash(Gun, BulletCount, ShotCount)
   local ShootEvent = Services.ReplicatedStorage.ShootEvent
   local StartTime = time()
   local BulletTable = {}

   for i = 1, BulletCount do
       BulletTable[i] = {
           Cframe = CFrame.new(),
           Distance = math.huge
       }
   end
   for i = 1, ShotCount do
       ShootEvent:FireServer(BulletTable, Gun)
       if time() - StartTime > 5 then
           break
       end
   end
end

-- // User Interface \\ --
-- [ Window ] --
local Window = UserInterface.new("Confinement X", UDim2.new(0, 420, 0, 420))

-- [ Assists ] --
Window:Divider("Assists")

Window:Toggle("Silent Aim", "Shoots toward the nearest player to your cursor.", false, function(State)
   Info.SilentAIMEnabled = State
end)

Window:Toggle("Trigger Bot", "Press G to temporarily disable.", false, function(State)
   Info.TriggeredEnabled = State
end)

Window:Slider("Field Of View", "Recommended: 250", 50, 500, 250, function(Value)
   Info.FieldOfView = Value
end)

Window:Dropdown("Team Whitelist", "Team for Silent-Aim to ignore.", {"Guards", "Inmates", "Criminals"}, function(Value)
   Info.TeamWhitelist = Value
end)

Window:Toggle("Danger Check", "Checks if an Inmate has gun.", false, function(State)
   Info.ArmsCheckEnabled = State
end)

-- [ Rage ] --
Window:Divider("Rage")

Window:Button("Kill All", "Kills everyone in-game", function()
   local GunScript = (LocalPlayer.Backpack:FindFirstChild("GunInterface", true) or LocalPlayer.Character:FindFirstChild("GunInterface", true))
   if GunScript then
       for i,v in ipairs(game.Players:GetPlayers()) do
           if v ~= LocalPlayer then
               local BulletInfo = {
                   [1] = {
                       ["RayObject"] = Ray.new(Vector3.new(845.555908, 101.429337, 2269.43945), Vector3.new(-391.152252, 8.65560055, -83.2166901)),
                       ["Distance"] = 3.2524313926697,
                       ["Cframe"] = CFrame.new(840.310791, 101.334137, 2267.87988, 0.0636406094, 0.151434347, -0.986416459, 0, 0.988420188, 0.151741937, 0.997972965, -0.00965694897, 0.0629036576),
                       ["Hit"] = v.Character.Head
                   },
                   [2] = {
                       ["RayObject"] = Ray.new(Vector3.new(845.555908, 101.429337, 2269.43945), Vector3.new(-392.481476, -8.44939327, -76.7261353)),
                       ["Distance"] = 3.2699294090271,
                       ["Cframe"] = CFrame.new(840.290466, 101.184189, 2267.93506, 0.0964837447, 0.0589403138, -0.993587971, 4.65661287e-10, 0.998245299, 0.0592165813, 0.995334625, -0.00571343815, 0.0963144377),
                       ["Hit"] = v.Character.Head
                   },
                   [3] = {
                       ["RayObject"] = Ray.new(Vector3.new(845.555908, 101.429337, 2269.43945), Vector3.new(-389.21701, -2.50536323, -92.2163162)),
                       ["Distance"] = 3.1665518283844,
                       ["Cframe"] = CFrame.new(840.338867, 101.236496, 2267.80371, 0.0166504811, 0.0941716284, -0.995416701, 1.16415322e-10, 0.995554805, 0.0941846818, 0.999861419, -0.00156822044, 0.0165764652),
                       ["Hit"] = v.Character.Head
                   },
                   [4] = {
                       ["RayObject"] = Ray.new(Vector3.new(845.555908, 101.429337, 2269.43945), Vector3.new(-393.353973, 3.13988972, -72.5452042)),
                       ["Distance"] = 3.3218522071838,
                       ["Cframe"] = CFrame.new(840.277222, 101.285957, 2267.9707, 0.117109694, 0.118740402, -0.985994935, -1.86264515e-09, 0.992826641, 0.119563118, 0.993119001, -0.0140019981, 0.116269611),
                       ["Hit"] = v.Character.Head
                   },
                   [5] = {
                       ["RayObject"] = Ray.new(Vector3.new(845.555908, 101.429337, 2269.43945), Vector3.new(-390.73172, 3.2097764, -85.5477524)),
                       ["Distance"] = 3.222757101059,
                       ["Cframe"] = CFrame.new(840.317993, 101.286423, 2267.86035, 0.0517584644, 0.123365127, -0.991010666, 0, 0.992340803, 0.123530701, 0.99865967, -0.00639375951, 0.0513620302),
                       ["Hit"] = v.Character.Head
                   }
               }
               Services.ReplicatedStorage.ShootEvent:FireServer(BulletInfo, GunScript.Parent)
               Services.ReplicatedStorage.ShootEvent:FireServer(BulletInfo, GunScript.Parent)
           end
       end
   else
       ToolTip.update("No gun found!")
   end
end)

Window:Button("Gun Modification", "Modifies the current gun you are holding.", function()
   local GunStates = LocalPlayer.Character:FindFirstChild("GunStates", true)
   if GunStates then
       local GunInfo = require(GunStates)
       GunInfo.ReloadTime = 0
       GunInfo.FireRate = 0
       GunInfo.AutoFire = true
       GunInfo.StoredAmmo = math.huge
       GunInfo.MaxAmmo = math.huge
       GunInfo.CurrentAmmo = math.huge
   end
end)

-- [ Miscellaneous ] --
Window:Divider("Miscellaneous")

Window:Button("Get Guns", "Grabs all", function()
   local HasSWAT = Services.MarketplaceService:UserOwnsGamePassAsync(LocalPlayer.UserId, 96651)

   workspace.Remote.ItemHandler:InvokeServer(workspace.Prison_ITEMS.giver["Remington 870"].ITEMPICKUP)
   if HasSWAT then
       workspace.Remote.ItemHandler:InvokeServer(workspace.Prison_ITEMS.giver["M4A1"].ITEMPICKUP)
   end
   workspace.Remote.ItemHandler:InvokeServer(workspace.Prison_ITEMS.giver["AK-47"].ITEMPICKUP)
   workspace.Remote.ItemHandler:InvokeServer(workspace.Prison_ITEMS.giver["M9"].ITEMPICKUP)

   if HasSWAT then
       workspace.Remote.ItemHandler:InvokeServer(workspace.Prison_ITEMS.clothes["Riot Police"].ITEMPICKUP)
   end
end)

-- [ Credits ] --
Window:Divider("Credits")

Window:Button("OminousVibes#7259", "Script Creator", function()
   setclipboard("OminousVibes#7259")
end)


-- // Metatable \\ --
setreadonly(RawMetatable, false)

RawMetatable.__index = newcclosure(function(Self, Index)
   if Info.SilentAIMEnabled == true and checkcaller() == false then
       if typeof(Self) == "Instance" and (Self:IsA("PlayerMouse") or Self:IsA("Mouse")) then
           if Index == "Hit" then
               local Closest = ClosestPlayerToCursor(Info.FieldOfView)
               if Closest then
                   local Velocity = Closest.Character.PrimaryPart.AssemblyLinearVelocity
                   local Prediction = Velocity.Unit
                   if Velocity.Magnitude == 0 then
                       Prediction = Vector3.new(0, 0, 0)
                   end
                   return CFrame.new(Closest.Character.Head.Position + Prediction)
               end
           end
       end
   end

   return __Index(Self, Index)
end)


setreadonly(RawMetatable, true)

-- // Event Listeners \\ --
Services.RunService.RenderStepped:Connect(function()
   if Info.SilentAIMEnabled == true then
       -- FOV --
       FOVCircle.Visible = true
       FOVCircle.Radius = Info.FieldOfView
       FOVCircle.Position = Services.UserInputService:GetMouseLocation()

       -- Target --
       local Closest, Position = ClosestPlayerToCursor(Info.FieldOfView)
       if Closest then
           Target.PointA = Vector2.new(Position.X - 25, Position.Y + 25)
           Target.PointB = Vector2.new(Position.X + 25, Position.Y + 25)
           Target.PointC = Vector2.new(Position.X, Position.Y - 25)
           if Info.TriggeredEnabled and not Services.UserInputService:IsKeyDown(Enum.KeyCode.G) then
               mouse1click()
           end
       end
       Target.Visible = Closest ~= nil
   else
       FOVCircle.Visible = false
       Target.Visible = false
   end
end)

LocalPlayer.Chatted:Connect(function(Message)
   if string.find(Message:lower(), "-lag") then
       local GunScript = (LocalPlayer.Backpack:FindFirstChild("GunInterface", true) or LocalPlayer.Character:FindFirstChild("GunInterface", true))
       if GunScript then
           ToolTip.update("Lagging...")
           Crash(GunScript.Parent, 100, 10)
           ToolTip.update("Lagged!")
       else
          ToolTip.update("Error: No gun found!")
       end
   end
end)

local LastShotDetected = time()
for i,v in ipairs(getconnections(Services.ReplicatedStorage.ReplicateEvent.OnClientEvent)) do
   local OldFunction = v.Function
   v.Function = function(BulletStats, IsTaser)
       if #BulletStats > 25 or time() - LastShotDetected > 0.02 then
           ToolTip.update("Bullet Overload: Removing...")
           return
       end
       LastShotDetected = time()
       OldFunction(BulletStats, IsTaser)
   end
end

local LastSoundDetected = time()
for i,v in ipairs(getconnections(Services.ReplicatedStorage.SoundEvent.OnClientEvent)) do
   local OldFunction = v.Function
   v.Function = function(Sound)
       if time() - LastSoundDetected > 0.02 then
           ToolTip.update("Audio Overload: Removing...")
           return
       end
       LastSoundDetected = time()
       OldFunction(Sound)
   end
end


-- // KeyBinds \\ --
Services.UserInputService.InputBegan:Connect(function(Input, GameProcessed)
   if _G.ArrestAssist == false or GameProcessed or LocalPlayer.Character:FindFirstChild("Handcuffs") == nil then
       return
   end

   local Delta = time() - LastArrest
   if Delta <= 15 then
       ToolTip.update("Wait " .. tostring(math.floor(Delta)) .. " seconds before arresting again!")
   end

   if Input.UserInputType == Enum.UserInputType.MouseButton1 then
       local Closest = ClosestPlayerToCursor(_G.FOV)
       if Closest then
           local Result = workspace.Remote.arrest:InvokeServer(Closest.Character.HumanoidRootPart)
           ToolTip.update(Result == true and "Successfully arrested!" or Result)
           if Result == true then
               LastArrest = time()
           end
       end
   end
end)

Services.ContextActionService:BindAction("Switch Bind", function(actionName, InputState, inputObject)
if InputState == Enum.UserInputState.End then
return
   end
   pcall(SwitchGuns)
end, false, Enum.KeyCode.Q)

-- // Actions \\ --
LocalPlayer.PlayerGui.Home.fadeFrame.Visible = false

return {};
