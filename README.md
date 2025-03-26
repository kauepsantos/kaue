local FischAPI = {}

local VIM = game:GetService("VirtualInputManager")

local VI = {}

local Options = {
    AutoShake = false,
    AutoMinigame = false, --Later version
    AutoMinigameBlatant = false,
    AutoCast = false,
    PerfectCast = false, --Later version
    WebhookURL = "",
    WebhookNotifications = false,
    FloatOnWater = false,
    Lock = false,
    MegaladonHunting = false,
    Priorities={},
	PriorityWebhook=false,
	AutoMap=false,
	AutoTotem=false
}

local Internal = {
    AutoMinigameDownPerUp = 2,
    AutoMinigameDownPerUpInternal = AutoMinigameDownPerUp,
    Timer = 301,
    FloatPart = nil,
    LockedPosition = nil,
    MegaladonPosition = nil,
    Megaladon = false,
    MegHuntPlat = nil,
    MegHuntPos = nil,
	RodToBeEquipped = "",
	FishHunted = "",
	Pr = 0,
}

local Utils = {}

local UI = {}

local CalibrationData = {}

local FischUser = {}

local NEVERLOSE = loadstring(game:HttpGet("https://you.whimper.xyz/sources/ronix/ui.lua"))()

local Notification = NEVERLOSE.Notification();

NEVERLOSE:Theme("dark")

--UI

for Setting, Value in pairs(Options) do
    if Value == false then
        UI[Setting] = function(Val)
            Options[Setting] = Val
        end
    end
end

function UI.Initialize()
    --UI

    local Windows = NEVERLOSE:AddWindow("Ronix Hub","ðŸŒŸRonix Hub - Fisch - V0.1.8 - discord.gg/ronixðŸŒŸ")

    local FishingTab = Windows:AddTab("Fishing", "earth")

    local PlayerTab = Windows:AddTab("Player", "earth")

    local ExclusivesTab = Windows:AddTab("FUN", "earth")

    local Interactions = Windows:AddTab("Interactions", "list")

    local AreaTeleportsTab = Windows:AddTab("Area Teleports", "earth")

    local ShopTab = Windows:AddTab("Shop", "earth")

    local MegaladonHunting = Windows:AddTab("Priority List", "earth")

    local WebhookTab = Windows:AddTab("Webhook", "list")

    local SettingsTab = Windows:AddTab('Settings','earth')

    local MechanicsSection = FishingTab:AddSection("Mechanics", "left")

	local FishingSection = FishingTab:AddSection("Fish AutoFarm", "left")

	local FishingSetSection = FishingTab:AddSection("Fish AutoFarm Settings", "left")

    local Convenience = FishingTab:AddSection("Convenience", "left")

    local CreditsSection = FishingTab:AddSection("Credits", "right")

    local Teleports = AreaTeleportsTab:AddSection("Teleports", "left")

    local Treasure = AreaTeleportsTab:AddSection("Treasure", "right")

    local Actions = Interactions:AddSection("Actions", "left")

    local WebhookSection = WebhookTab:AddSection("Webhook", "left")

    local ShopSection = ShopTab:AddSection("Shop All", "left")

    local SellerSection = FishingTab:AddSection("Seller", "left")

    local TotemsSection = AreaTeleportsTab:AddSection("Totems", "left")

    local WorldEvents = AreaTeleportsTab:AddSection('World Events', "right")

    local ExclusivesSection = ExclusivesTab:AddSection('FUN', "right")
  

    local PlayerSection = PlayerTab:AddSection("Player Modify", "left")

    local MiscSection = PlayerTab:AddSection("Misc Player", "right")

    local SettingsSection = SettingsTab:AddSection("Settings", "right")
    
    SellerSection:AddButton("Sell All",  FischUser.Sell)

    Convenience:AddToggle("Float On Water", false, UI.FloatOnWater)

    Convenience:AddLabel("Turn ON to walk around and choose spot.")
    
	Convenience:AddToggle("Auto Totem Use", false, UI.AutoTotem)

	Convenience:AddToggle("Auto TP To Treasure Map", false, UI.AutoMap)

Treasure:AddToggle("Teleport to Jack Marrow", false, function()
    local Player = game.Players.LocalPlayer
    local HumanoidRootPart = Player.Character:WaitForChild("HumanoidRootPart")
    HumanoidRootPart.CFrame = CFrame.new(-2824.359, 214.311, 1518.130)
end)
local NpcFolder = Workspace:FindFirstChild("world"):WaitForChild("npcs")

function rememberPosition()
    spawn(function()
        local initialCFrame = HumanoidRootPart.CFrame
 
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVelocity.Parent = HumanoidRootPart
 
        local bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bodyGyro.D = 100
        bodyGyro.P = 10000
        bodyGyro.CFrame = initialCFrame
        bodyGyro.Parent = HumanoidRootPart
 
        while AutoFreeze do
            HumanoidRootPart.CFrame = initialCFrame
            task.wait(0.01)
        end
        if bodyVelocity then
            bodyVelocity:Destroy()
        end
        if bodyGyro then
            bodyGyro:Destroy()
        end
    end)
end
SellerSection:AddButton("Sell Fish (In Hand)", false, function()
        local currentPosition = HumanoidRootPart.CFrame
        local sellPosition = CFrame.new(464, 151, 232)
        local wasAutoFreezeActive = false
        if AutoFreeze then
            wasAutoFreezeActive = true
            AutoFreeze = false
        end
        HumanoidRootPart.CFrame = sellPosition
        task.wait(0.5)
        workspace:WaitForChild("world"):WaitForChild("npcs"):WaitForChild("Marc Merchant"):WaitForChild("merchant"):WaitForChild("sell"):InvokeServer()
        task.wait(1)
        HumanoidRootPart.CFrame = currentPosition
        if wasAutoFreezeActive then
            AutoFreeze = true
            rememberPosition()
        end
    end)


local LocalPlayer = game.Players.LocalPlayer
local PlayerGui = LocalPlayer.PlayerGui

Convenience :AddButton("Show Ui Buy Boat", function()
    if PlayerGui and PlayerGui:FindFirstChild("hud") and PlayerGui.hud:FindFirstChild("safezone") and PlayerGui.hud.safezone:FindFirstChild("shipwright") then
        PlayerGui.hud.safezone.shipwright.Visible = not PlayerGui.hud.safezone.shipwright.Visible
    else
        print("Error: Could not find the necessary UI elements.")
    end
end)
FishingSection:AddToggle("Auto Reel (Blatant)", false, UI.AutoMinigameBlatant)
Treasure:AddToggle("Repair Map", false, function()
    local Player = game.Players.LocalPlayer
    for _, v in pairs(Player.Backpack:GetChildren()) do
        if v.Name == "Treasure Map" then
            Player.Character.Humanoid:EquipTool(v)
            workspace.world.npcs["Jack Marrow"].treasure.repairmap:InvokeServer()
        end
    end
end)
FishingSection:AddToggle("Auto Cast", false, UI.AutoCast)
Treasure:AddToggle("Collect Treasure", false, function()
    for _, v in ipairs(game:GetService("Workspace"):GetDescendants()) do
        if v.ClassName == "ProximityPrompt" then
            v.HoldDuration = 0
        end
    end

    for _, v in pairs(workspace.world.chests:GetDescendants()) do
        if v:IsA("Part") and v:FindFirstChild("ChestSetup") then
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.CFrame
            for _, prompt in pairs(workspace.world.chests:GetDescendants()) do
                if prompt.Name == "ProximityPrompt" then
                    fireproximityprompt(prompt)
                end
            end
            task.wait(1)
        end
    end
end)

PlayerSection:AddToggle("Walk On Water", false, function(Value)
    local WalkZone = "Ocean" 

    if Value then

        for _, v in pairs(workspace.zones.fishing:GetChildren()) do
            if v.Name == WalkZone then
                v.CanCollide = true
            end
            if v.Name == "Deep Ocean" and WalkZone == "Ocean" then
                v.CanCollide = true
            end
        end
    else
--bro bdokkx is so sigma!
        for _, v in pairs(workspace.zones.fishing:GetChildren()) do
            if v.Name == WalkZone then
                v.CanCollide = false
            end
            if v.Name == "Deep Ocean" and WalkZone == "Ocean" then
                v.CanCollide = false
            end
        end
    end
end)


local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")


local oldpos = HumanoidRootPart.CFrame
local FreezePlayer = false 

local function FreezeCharacter()
    while FreezePlayer do
        task.wait()
        if HumanoidRootPart then
            HumanoidRootPart.CFrame = oldpos
        end
    end
end


PlayerSection:AddToggle("Freeze Player", false, function(Value)
    FreezePlayer = Value
    if FreezePlayer then
        oldpos = HumanoidRootPart.CFrame
        task.spawn(FreezeCharacter)
        print("Player freezing enabled.")
    else
        print("Player freezing disabled.")
    end
end)




--also i just uhh dont ask :skull got mad so i put everything and it works
local VirtualInputManager = game:GetService("VirtualInputManager")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualUser = game:GetService("VirtualUser")
local HttpService = game:GetService("HttpService")
local GuiService = game:GetService("GuiService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local CoreGui = game:GetService('StarterGui')
local ContextActionService = game:GetService('ContextActionService')
local UserInputService = game:GetService('UserInputService')

local LocalPlayer = Players.LocalPlayer
local LocalCharacter = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = LocalCharacter:FindFirstChild("HumanoidRootPart")
local UserPlayer = HumanoidRootPart:WaitForChild("user")
local ActiveFolder = Workspace:FindFirstChild("active")
local FishingZonesFolder = Workspace:FindFirstChild("zones"):WaitForChild("fishing")
local TpSpotsFolder = Workspace:FindFirstChild("world"):WaitForChild("spawns"):WaitForChild("TpSpots")
local NpcFolder = Workspace:FindFirstChild("world"):WaitForChild("npcs")
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local screenGui = Instance.new("ScreenGui", PlayerGui)
local shadowCountLabel = Instance.new("TextLabel", screenGui)
local RenderStepped = RunService.RenderStepped
local WaitForSomeone = RenderStepped.Wait

Convenience:AddButton("Protect Identity", function()

    getgenv().name = "discord.gg/ronix on top"
    local Plr = game.Players.LocalPlayer
    for Index, Value in next, game:GetDescendants() do 
        if Value.ClassName == "TextLabel" then 
            local has = string.find(Value.Text,Plr.Name) 
            if has then 
                local str = Value.Text:gsub(Plr.Name,name)
                Value.Text = str 
            end
            Value:GetPropertyChangedSignal("Text"):Connect(function()
                local str = Value.Text:gsub(Plr.Name,name)
                Value.Text = str 
            end)
        end
    end
    game.DescendantAdded:Connect(function(Value)
        if Value.ClassName == "TextLabel" then 
            local has = string.find(Value.Text,Plr.Name)
            Value:GetPropertyChangedSignal("Text"):Connect(function()
                local str = Value.Text:gsub(Plr.Name,name)
                Value.Text = str 
            end)
            if has then 
                local str = Value.Text:gsub(Plr.Name,name)
                Value.Text = str 
            end
     
        end
    end)
    if UserPlayer:FindFirstChild("streak") then UserPlayer.streak.Text = "HIDDEN" end
    if UserPlayer:FindFirstChild("level") then UserPlayer.level.Text = "Level: HIDDEN" end
    if UserPlayer:FindFirstChild("level") then UserPlayer.user.Text = "HIDDEN" end
    local hud = LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("hud"):WaitForChild("safezone")
    if hud:FindFirstChild("coins") then hud.coins.Text = "HIDDEN$" end
    if hud:FindFirstChild("lvl") then hud.lvl.Text = "HIDDEN LVL" end
    task.wait(0.01)
end)



PlayerSection:AddDropdown("Walk On Water Zone", {"Ocean", "Desolate Deep", "The Depths"}, Ocean, function(selectedZone)
    WalkZone = selectedZone 
end)

    AllFuncs['To Pos Stand'] = function()
        while Config['To Pos Stand'] and task.wait() do
            if not Config['SelectPositionStand'] then
                Notify("Pls Select Position")
                Config['To Pos Stand'] = false
                return
            end
            pcall(function()
                LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = Config['SelectPositionStand']
            end)
        end
    end

    local Config = {
        ['Toggle Walk Speed'] = false, 
        ['Set Walk Speed'] = 50,
        ['Toggle Jump Power'] = false,
        ['Set Jump Power'] = 50
    }
    
    local LocalPlayer = game.Players.LocalPlayer

        AllFuncs['Toggle Walk Speed'] = function()
        while true do
            if Config['Toggle Walk Speed'] then
                pcall(function()
                    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        humanoid.WalkSpeed = Config['Set Walk Speed']
                    end
                end)
            else
                pcall(function()
                    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        humanoid.WalkSpeed = 16 
                    end
                end)
            end
            task.wait(0.1) 
        end
    end




    
    
    AllFuncs['Toggle Jump Power'] = function()
        while true do
            if Config['Toggle Jump Power'] then
                pcall(function()
                    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        humanoid.JumpPower = Config['Set Jump Power']
                    end
                end)
            else
                pcall(function()
                    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        humanoid.JumpPower = 50
                    end
                end)
            end
            task.wait(0.1)
        end
    end

SettingsSection:AddLabel("Useless because anti afk auto loads!")
	ShopSection:AddLabel("Turn on when you first join")
    PlayerSection:AddToggle('Toggle Walk Speed', false, function(val)
        Config['Toggle Walk Speed'] = val
        print("Walk Speed Toggle:", val)
    end)
    
    PlayerSection:AddToggle('Toggle Jump Power', false, function(val)
        Config['Toggle Jump Power'] = val
        print("Jump Power Toggle:", val)
    end)
    
    PlayerSection:AddSlider('Walk Speed', 1, 500, 50, function(val)
        Config['Set Walk Speed'] = val
        print('Walk Speed Set to:', val)
    end)
    
    PlayerSection:AddSlider('Jump Power', 1, 500, 50, function(val)
        Config['Set Jump Power'] = val
        print('Jump Power Set to:', val)
    end)
    
    task.spawn(AllFuncs['Toggle Walk Speed'])
    task.spawn(AllFuncs['Toggle Jump Power'])
    
Convenience:AddToggle("Remove Fog", false, function(Value)
    if Value then
        if game:GetService("Lighting"):FindFirstChild("Sky") then
            game:GetService("Lighting"):FindFirstChild("Sky").Parent = game:GetService("Lighting").bloom
        end
    else
        if game:GetService("Lighting").bloom:FindFirstChild("Sky") then
            game:GetService("Lighting").bloom:FindFirstChild("Sky").Parent = game:GetService("Lighting")
        end
    end
end)

local RunService = game:GetService("RunService")

local DayOnlyLoop
Convenience:AddToggle("Day", false, function(Value)
    if Value then
        if DayOnlyLoop then return end  
        DayOnlyLoop = RunService.Heartbeat:Connect(function()
            game:GetService("Lighting").TimeOfDay = "12:00:00"
        end)
    else
        if DayOnlyLoop then
            DayOnlyLoop:Disconnect()
            DayOnlyLoop = nil
        end
    end
end)

local NightOnlyLoop
Convenience:AddToggle("Night", false, function(Value)
    if Value then
        if NightOnlyLoop then return end
        NightOnlyLoop = RunService.Heartbeat:Connect(function()
            game:GetService("Lighting").TimeOfDay = "22:00:00"
        end)
    else
        if NightOnlyLoop then
            NightOnlyLoop:Disconnect()
            NightOnlyLoop = nil
        end
    end
end)

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")

Actions:AddButton("Rejoin Server", function()
    TeleportService:TeleportToPlaceInstance(game.placeId, game.JobId, Players.LocalPlayer)
end)

MiscSection:AddToggle("Fish Radar", false, function(Value)

    for _, v in pairs(game:GetService("CollectionService"):GetTagged("radarTag")) do
        if v:IsA("BillboardGui") or v:IsA("SurfaceGui") then
            v.Enabled = Value
        end
    end
end)
--real bdokx super sigma
    local function GetPosition()
        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            return character.HumanoidRootPart.Position
        end
        return Vector3.new(0, 0, 0)  
    end
    
    local function ExportValue(value)
        return string.format("%.2f", value) 
    end
    
    MiscSection:AddToggle("Gps", false, function(Value)
        local PlayerGui = game.Players.LocalPlayer.PlayerGui
        local hud = PlayerGui:WaitForChild("hud")
        local safezone = hud:WaitForChild("safezone")
        local backpack = safezone:WaitForChild("backpack")
    
        if Value then
            local XyzClone = game:GetService("ReplicatedStorage").resources.items.items.GPS.GPS.gpsMain.xyz:Clone()
            XyzClone.Parent = backpack

            local Pos = GetPosition()
            local StringInput = string.format("%s,%s,%s", ExportValue(Pos.X), ExportValue(Pos.Y), ExportValue(Pos.Z))
            XyzClone.Text = "<font color='#ff4949'>X</font><font color='#a3ff81'>Y</font><font color='#626aff'>Z</font>: "..StringInput

            BypassGpsLoop = game:GetService("RunService").Heartbeat:Connect(function()
                local Pos = GetPosition()
                local StringInput = string.format("%s,%s,%s", ExportValue(Pos.X), ExportValue(Pos.Y), ExportValue(Pos.Z))
                XyzClone.Text = "<font color='#ff4949'>X</font><font color='#a3ff81'>Y</font><font color='#626aff'>Z</font>: "..StringInput
            end)
        else
            if backpack:FindFirstChild("xyz") then
                backpack:FindFirstChild("xyz"):Destroy()
            end
            if BypassGpsLoop then
                BypassGpsLoop:Disconnect()
                BypassGpsLoop = nil
            end
        end
    end)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui") 
local hud = PlayerGui:WaitForChild("hud") 
local safezone = hud:WaitForChild("safezone") 


safezone.Visible = true


MiscSection:AddToggle("Show/Hide UIs", false, function(Value)
    if Value then
        safezone.Visible = false
        print("UI is now visible.")
    else
        safezone.Visible = true
        print("UI is now hidden.")
    end
end)



MiscSection:AddToggle("Infinite Oxygen", false, function(Value)
    LocalPlayer.Character.client.oxygen.Disabled = Value
end)

MiscSection:AddToggle("Clear Weather", false, function(Value)
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local weather = ReplicatedStorage:WaitForChild("world"):WaitForChild("weather")
    local OldWEA = OldWEA or weather.Value

    if Value then
        weather.Value = "Clear"
    else
        weather.Value = OldWEA
    end
end)
    

PlayerSection:AddToggle("Noclip", false, function(Value)
    Config['Toggle Noclip'] = Value 

    if Value then
        local charParts = LocalPlayer.Character:GetDescendants()
        
