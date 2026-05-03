-- TWEAKZ HUB - BLACK CYBER EDITION
-- Enhanced with RGB effects & advanced features

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local ProximityPromptService = game:GetService("ProximityPromptService")
local RunService = game:GetService("RunService")
local TextChatService = game:GetService("TextChatService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")
local Lighting = game:GetService("Lighting")
local Debris = game:GetService("Debris")

local player = Players.LocalPlayer

-- AUTO ACTIVATE FFLAGS (Advanced Optimization)
local FFlags = {
    GameNetPVHeaderRotationalVelocityZeroCutoffExponent = -5000,
    LargeReplicatorWrite5 = true, LargeReplicatorEnabled9 = true,
    AngularVelociryLimit = 360,
    TimestepArbiterVelocityCriteriaThresholdTwoDt = 2147483646,
    S2PhysicsSenderRate = 15000, DisableDPIScale = true,
    MaxDataPacketPerSend = 2147483647, PhysicsSenderMaxBandwidthBps = 20000,
    TimestepArbiterHumanoidLinearVelThreshold = 21,
    MaxMissedWorldStepsRemembered = -2147483648,
    PlayerHumanoidPropertyUpdateRestrict = true,
    SimDefaultHumanoidTimestepMultiplier = 0,
    StreamJobNOUVolumeLengthCap = 2147483647
}

task.spawn(function()
    for name, value in pairs(FFlags) do
        pcall(function() setfflag(tostring(name), tostring(value)) end)
    end
    task.wait(0.2)
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) end
        char:ClearAllChildren()
        local f = Instance.new("Model", workspace)
        player.Character = f; task.wait(); player.Character = char; f:Destroy()
    end
end)

-- CLEANUP SYSTEM
if _G.TweakzHubCleanup then pcall(_G.TweakzHubCleanup) end
_G.TweakzHubCleanup = function()
    if _G.TweakzHubConnections then
        for _, conn in ipairs(_G.TweakzHubConnections) do pcall(function() conn:Disconnect() end) end
        _G.TweakzHubConnections = {}
    end
    for _, part in ipairs(workspace:GetChildren()) do
        if part.Name:find("TweakzHub") or part.Name:find("CyberESP") or part.Name:find("HoloEffect") then
            pcall(function() part:Destroy() end)
        end
    end
    local playerGui = player:FindFirstChild("PlayerGui")
    if playerGui then
        for _, g in ipairs({"TweakzHubUI","TweakzHubProgress"}) do
            local gui = playerGui:FindFirstChild(g)
            if gui then pcall(function() gui:Destroy() end) end
        end
    end
end
pcall(_G.TweakzHubCleanup)

local connections = {}
_G.TweakzHubConnections = connections
if _G.TweakzHubInstaSteal then pcall(_G.TweakzHubCleanup); task.wait(0.1) end
_G.TweakzHubInstaSteal = true

-- ===============================
-- CONFIGURATION
-- ===============================
local tpSystemEnabled = true
local autoAPOnSteal = false
local autoKickOnSteal = false
local autoPotion = false
local speedBoostEnabled = false
local speedValue = 29
local keybindLeftKey = nil
local keybindRightKey = nil
local isWaitingForKeybindLeft = false
local isWaitingForKeybindRight = false
local rainbowMode = false
local rgbSpeed = 1

-- POSITIONS
local pos1 = Vector3.new(-352.98, -7, 74.30)
local pos2 = Vector3.new(-352.98, -6.49, 45.76)
local standing1 = Vector3.new(-336.36, -4.59, 99.51)
local standing2 = Vector3.new(-334.81, -4.59, 18.90)

local spot1_sequence = {
    CFrame.new(-370.810913, -7.00000334, 41.2687263, 0.99984771, 1.22364419e-09, 0.0174523517, -6.54859778e-10, 1, -3.2596418e-08, -0.0174523517, 3.25800258e-08, 0.99984771),
    CFrame.new(-336.355286, -5.10107088, 17.2327671, -0.999883354, -2.76150569e-08, 0.0152716246, -2.88224964e-08, 1, -7.88441525e-08, -0.0152716246, -7.9275118e-08, -0.999883354)
}
local spot2_sequence = {
    CFrame.new(-354.782867, -7.00000334, 92.8209305, -0.999997616, -1.11891862e-09, -0.00218066527, -1.11958298e-09, 1, 3.03415071e-10, 0.00218066527, 3.05855785e-10, -0.999997616),
    CFrame.new(-336.942902, -5.10106993, 99.3276443, 0.999914348, -3.63984611e-08, 0.0130875716, 3.67094941e-08, 1, -2.35254749e-08, -0.0130875716, 2.40038975e-08, 0.999914348)
}

local autoSemiTpLeft  = CFrame.new(-349.325867,-7.00000238,95.0031433, -0.999048233,-8.29406233e-09,-0.0436184891, -1.03892832e-08,1,4.78084594e-08, 0.0436184891,4.82161227e-08,-0.999048233)
local autoSemiTpRight = CFrame.new(-349.560211,-7.00000238,27.0543289, -0.999961913,5.50995267e-08,-0.00872585084, 5.48100907e-08,1,3.34090586e-08, 0.00872585084,3.29295204e-08,-0.999961913)

-- ===============================
-- MISC FUNCTIONS
-- ===============================
local function getHRP()
    local char = player.Character; if not char then return nil end
    return char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("UpperTorso")
end

local function triggerClosestUnlock(targetY, maxY)
    local char = player.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local startPos = hrp.Position
    for yOffset = targetY, maxY, 0.5 do
        hrp.CFrame = CFrame.new(startPos.X, startPos.Y + yOffset, startPos.Z)
        task.wait(0.01)
    end
end

-- ===============================
-- BLACK CYBER ESP BOXES (VIBRANT)
-- ===============================
local teleportHereBoxes = {}
local function createHoloESP(position, labelText, color)
    color = color or Color3.fromRGB(0, 255, 255) -- CYAN default
    local espFolder = Instance.new("Folder")
    espFolder.Name = "TweakzHub_" .. labelText:gsub(" ", "_")
    espFolder.Parent = workspace
    
    -- Main cyber part (black base with vibrant outline)
    local box = Instance.new("Part")
    box.Size = Vector3.new(5, 0.5, 5)
    box.Position = position
    box.Anchored = true
    box.CanCollide = false
    box.Transparency = 0.2
    box.Material = Enum.Material.SmoothPlastic
    box.Color = Color3.fromRGB(10, 10, 10) -- Dark black base
    box.Parent = espFolder
    
    -- Vibrant neon glow
    local glow = Instance.new("PointLight", box)
    glow.Color = color
    glow.Range = 10
    glow.Brightness = 3
    glow.Shadows = false
    
    -- Cyber selection box with vibrant color
    local sel = Instance.new("SelectionBox")
    sel.Adornee = box
    sel.LineThickness = 0.1
    sel.Color3 = color
    sel.Parent = box
    
    -- Billboard with cyber style
    local bb = Instance.new("BillboardGui")
    bb.Size = UDim2.new(0, 200, 0, 45)
    bb.StudsOffset = Vector3.new(0, 2.5, 0)
    bb.AlwaysOnTop = true
    bb.MaxDistance = 300
    bb.Parent = box
    
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = "⚡ " .. labelText .. " ⚡"
    lbl.TextColor3 = color
    lbl.TextSize = 18
    lbl.Font = Enum.Font.GothamBold
    lbl.TextStrokeTransparency = 0.1
    lbl.TextStrokeColor3 = Color3.fromRGB(0, 200, 255)
    lbl.Parent = bb
    
    -- Floating animation
    task.spawn(function()
        local startY = box.Position.Y
        while box and box.Parent do
            local wave = math.sin(tick() * 4) * 0.2
            box.Position = Vector3.new(position.X, startY + wave, position.Z)
            task.wait(0.04)
        end
    end)
    
    return espFolder
end

task.spawn(function()
    teleportHereBoxes[1] = createHoloESP(pos1, "TELEPORT HERE", Color3.fromRGB(0, 255, 255))
    teleportHereBoxes[2] = createHoloESP(pos2, "TELEPORT HERE", Color3.fromRGB(0, 255, 255))
    createHoloESP(standing1, "STANDING POS 1", Color3.fromRGB(150, 0, 255))
    createHoloESP(standing2, "STANDING POS 2", Color3.fromRGB(150, 0, 255))
    createHoloESP(autoSemiTpLeft.Position, "AUTO TP LEFT", Color3.fromRGB(0, 255, 200))
    createHoloESP(autoSemiTpRight.Position, "AUTO TP RIGHT", Color3.fromRGB(0, 255, 200))
end)

-- ===============================
-- SPEED BOOST SYSTEM
-- ===============================
RunService.Heartbeat:Connect(function()
    if not speedBoostEnabled then return end
    local character = player.Character
    if not character then return end
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoid or not rootPart then return end
    if humanoid.MoveDirection.Magnitude > 0 then
        rootPart.Velocity = Vector3.new(
            humanoid.MoveDirection.X * speedValue,
            rootPart.Velocity.Y,
            humanoid.MoveDirection.Z * speedValue
        )
    end
end)

-- ===============================
-- AUTO AP & KICK SYSTEMS
-- ===============================
local function getClosestPlayer()
    local best, bd = nil, math.huge
    local myRoot = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if not myRoot then return nil end
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local d = (myRoot.Position - p.Character.HumanoidRootPart.Position).Magnitude
            if d < bd then bd = d; best = p end
        end
    end
    return best
end

local function spamAPCommands(targetPlayer)
    if not targetPlayer then return end
    local cmds = {"rocket","ragdoll","balloon","inverse","nightvision","jail","tiny","jumpscare","morph"}
    local ch = TextChatService.TextChannels:FindFirstChild("RBXGeneral")
    task.spawn(function()
        for _, cmd in ipairs(cmds) do
            local fc = ";" .. cmd .. " " .. targetPlayer.Name
            if TextChatService.ChatVersion == Enum.ChatVersion.TextChatService and ch then
                pcall(function() ch:SendAsync(fc) end)
            else
                local r = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
                if r and r:FindFirstChild("SayMessageRequest") then
                    pcall(function() r.SayMessageRequest:FireServer(fc,"All") end)
                end
            end
            task.wait(0.1)
        end
    end)
end

local KEYWORD = "you stole"; local KICK_MESSAGE = "You stole brainrot!"
local lastScanTime = 0; local SCAN_COOLDOWN = 0.5
local function setupAutoKick()
    if not autoKickOnSteal then
        if connections["KICK ON STEAL"] then
            for _, c in pairs(connections["KICK ON STEAL"]) do c:Disconnect() end
            connections["KICK ON STEAL"] = nil
        end
        return
    end
    local function hasKW(t) return typeof(t)=="string" and t:lower():find(KEYWORD,1,true)~=nil end
    local function watchObj(obj)
        if not(obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox")) then return end
        if obj.Visible and obj.Text and hasKW(obj.Text) then
            task.spawn(function() pcall(function() player:Kick(KICK_MESSAGE) end) end); return
        end
        if obj.Visible then
            local c = obj:GetPropertyChangedSignal("Text"):Connect(function()
                if hasKW(obj.Text) then
                    task.spawn(function() pcall(function() player:Kick(KICK_MESSAGE) end) end)
                end
            end)
            table.insert(connections, c)
        end
    end
    local function scanDesc(parent)
        local t = tick(); if t - lastScanTime < SCAN_COOLDOWN then return end; lastScanTime = t
        task.spawn(function()
            local objs = {}
            for _, obj in ipairs(parent:GetDescendants()) do
                if (obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox")) and obj.Visible and obj.Text then
                    table.insert(objs, obj)
                end
            end
            for i = 1, #objs, 15 do
                for j = i, math.min(i+14,#objs) do watchObj(objs[j]) end
                task.wait(0.05)
            end
        end)
    end
    local PG = player:WaitForChild("PlayerGui"); task.wait(0.5); scanDesc(PG)
    local c1 = PG.ChildAdded:Connect(function(g) task.wait(0.1)
        if g:IsA("ScreenGui") then scanDesc(g) else watchObj(g) end end)
    local c2 = PG.DescendantAdded:Connect(watchObj)
    connections["KICK ON STEAL"] = {c1, c2}
end

-- ===============================
-- SCANNER & STEAL CORE
-- ===============================
local allAnimalsCache = {}; local PromptMemoryCache = {}; local InternalStealCache = {}
local IsStealing = false; local StealProgress = 0; local CurrentStealTarget = nil

local function isMyBase(plotName)
    local plot = workspace:FindFirstChild("Plots") and workspace.Plots:FindFirstChild(plotName)
    if not plot then return false end
    local sign = plot:FindFirstChild("PlotSign")
    return sign and sign:FindFirstChild("YourBase") and sign.YourBase.Enabled
end

local function scanSinglePlot(plot)
    if not plot or not plot:IsA("Model") or isMyBase(plot.Name) then return end
    local podiums = plot:FindFirstChild("AnimalPodiums"); if not podiums then return end
    for _, podium in ipairs(podiums:GetChildren()) do
        if podium:IsA("Model") and podium:FindFirstChild("Base") then
            table.insert(allAnimalsCache, {
                plot = plot.Name, slot = podium.Name,
                worldPosition = podium:GetPivot().Position,
                uid = plot.Name.."_"..podium.Name,
            })
        end
    end
end

local function initializeScanner()
    task.wait(2)
    local plots = workspace:WaitForChild("Plots", 10); if not plots then return end
    for _, plot in ipairs(plots:GetChildren()) do scanSinglePlot(plot) end
    plots.ChildAdded:Connect(scanSinglePlot)
    task.spawn(function()
        while task.wait(5) do
            table.clear(allAnimalsCache)
            for _, plot in ipairs(plots:GetChildren()) do scanSinglePlot(plot) end
        end
    end)
end

local function findPrompt(animal)
    local cached = PromptMemoryCache[animal.uid]
    if cached and cached.Parent then return cached end
    local plots = workspace:FindFirstChild("Plots")
    local plot = plots and plots:FindFirstChild(animal.plot)
    local pods = plot and plot:FindFirstChild("AnimalPodiums")
    local podium = pods and pods:FindFirstChild(animal.slot)
    local base = podium and podium:FindFirstChild("Base")
    local sp = base and base:FindFirstChild("Spawn")
    local att = sp and sp:FindFirstChild("PromptAttachment")
    local prompt = att and att:FindFirstChildOfClass("ProximityPrompt")
    if prompt then PromptMemoryCache[animal.uid] = prompt end
    return prompt
end

local function buildStealCallbacks(prompt)
    if InternalStealCache[prompt] then return end
    local data = {holdCallbacks={}, triggerCallbacks={}, ready=true}
    local ok1, c1 = pcall(getconnections, prompt.PromptButtonHoldBegan)
    if ok1 then for _, c in ipairs(c1) do table.insert(data.holdCallbacks, c.Function) end end
    local ok2, c2 = pcall(getconnections, prompt.Triggered)
    if ok2 then for _, c in ipairs(c2) do table.insert(data.triggerCallbacks, c.Function) end end
    InternalStealCache[prompt] = data
end

local function getNearestAnimal()
    local hrp = getHRP(); if not hrp then return nil end
    local nearest, dist = nil, 200
    for _, animal in ipairs(allAnimalsCache) do
        local d = (hrp.Position - animal.worldPosition).Magnitude
        if d < dist then dist = d; nearest = animal end
    end
    return nearest
end

local function executeInternalStealLeft(prompt, animalData)
    if not tpSystemEnabled then return end
    local data = InternalStealCache[prompt]
    if not data or not data.ready or IsStealing then return end
    data.ready = false; IsStealing = true; StealProgress = 0; CurrentStealTarget = animalData
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        local bp = player:FindFirstChild("Backpack")
        if hum and bp then
            local carpet = bp:FindFirstChild("Flying Carpet")
            if carpet then hum:EquipTool(carpet) end
        end
    end
    local tpDone = false
    task.spawn(function()
        for _, fn in ipairs(data.holdCallbacks) do task.spawn(fn) end
        local st = tick()
        while tick()-st < 1.3 do
            StealProgress = (tick()-st)/1.3
            if StealProgress >= 0.73 and not tpDone then
                tpDone = true
                local hrp = getHRP()
                if hrp then
                    hrp.CFrame = spot1_sequence[1]; task.wait(0.1)
                    hrp.CFrame = spot1_sequence[2]; task.wait(0.2)
                    local d1=(hrp.Position-pos1).Magnitude; local d2=(hrp.Position-pos2).Magnitude
                    hrp.CFrame = CFrame.new(d1<d2 and pos1 or pos2)
                end
            end
            task.wait()
        end
        StealProgress = 1
        for _, fn in ipairs(data.triggerCallbacks) do task.spawn(fn) end
        if autoAPOnSteal then
            local cl = getClosestPlayer()
            if cl then spamAPCommands(cl) end
        end
        task.wait(0.2)
        data.ready = true; IsStealing = false; StealProgress = 0; CurrentStealTarget = nil
    end)
end

local function executeInternalStealRight(prompt, animalData)
    if not tpSystemEnabled then return end
    local data = InternalStealCache[prompt]
    if not data or not data.ready or IsStealing then return end
    data.ready = false; IsStealing = true; StealProgress = 0; CurrentStealTarget = animalData
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        local bp = player:FindFirstChild("Backpack")
        if hum and bp then
            local carpet = bp:FindFirstChild("Flying Carpet")
            if carpet then hum:EquipTool(carpet) end
        end
    end
    local tpDone = false
    task.spawn(function()
        for _, fn in ipairs(data.holdCallbacks) do task.spawn(fn) end
        local st = tick()
        while tick()-st < 1.3 do
            StealProgress = (tick()-st)/1.3
            if StealProgress >= 0.73 and not tpDone then
                tpDone = true
                local hrp = getHRP()
                if hrp then
                    hrp.CFrame = spot2_sequence[1]; task.wait(0.1)
                    hrp.CFrame = spot2_sequence[2]; task.wait(0.2)
                    local d1=(hrp.Position-pos1).Magnitude; local d2=(hrp.Position-pos2).Magnitude
                    hrp.CFrame = CFrame.new(d1<d2 and pos1 or pos2)
                end
            end
            task.wait()
        end
        StealProgress = 1
        for _, fn in ipairs(data.triggerCallbacks) do task.spawn(fn) end
        if autoAPOnSteal then
            local cl = getClosestPlayer()
            if cl then spamAPCommands(cl) end
        end
        task.wait(0.2)
        data.ready = true; IsStealing = false; StealProgress = 0; CurrentStealTarget = nil
    end)
end

-- ===============================
-- AUTO GRAB COMBO
-- ===============================
local combo_IsStealing = false; local combo_StealProgress = 0
local combo_ComboActive = false; local combo_lastTrigger = 0; local combo_COOLDOWN = 1.5

local function findNearestStealPrompt()
    local char = player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart"); if not root then return nil end
    local nearest, nearestDist = nil, 200
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") then
            local n=obj.Name:lower(); local a=obj.ActionText:lower(); local o=obj.ObjectText:lower()
            if n:find("steal") or a:find("steal") or o:find("steal") then
                local parent = obj.Parent; local partPos = nil
                if parent then
                    if parent:IsA("BasePart") then partPos = parent.Position
                    else local bp=parent:FindFirstChildWhichIsA("BasePart"); if bp then partPos=bp.Position end end
                end
                if partPos then
                    local dist = (root.Position-partPos).Magnitude
                    if dist < nearestDist then nearestDist = dist; nearest = obj end
                end
            end
        end
    end
    return nearest
end

local function startComboStealMonitor()
    if combo_ComboActive then return end
    combo_ComboActive = true
    task.spawn(function()
        while combo_ComboActive do
            if combo_IsStealing and combo_StealProgress >= 0.73 then
                local char = player.Character
                local root = char and char:FindFirstChild("HumanoidRootPart")
                if root then
                    local d1=(root.Position-pos1).Magnitude; local d2=(root.Position-pos2).Magnitude
                    local seq = d1<d2 and spot1_sequence or spot2_sequence
                    root.CFrame = seq[1]
                    task.spawn(function() task.wait(0.1); if root and root.Parent then root.CFrame = seq[2] end end)
                    task.spawn(function()
                        task.wait(0.313)
                        if root and root.Parent then
                            local d1b=(root.Position-pos1).Magnitude; local d2b=(root.Position-pos2).Magnitude
                            root.CFrame = CFrame.new(d1b<d2b and pos1 or pos2)
                        end
                    end)
                    combo_IsStealing = false; combo_StealProgress = 0
                    combo_lastTrigger = tick(); task.wait(0.5)
                end
            end
            task.wait(0.02)
        end
    end)
end

local function startAutoStealLoop()
    task.spawn(function()
        while combo_ComboActive do
            if tick()-combo_lastTrigger < combo_COOLDOWN then task.wait(0.1); continue end
            if not combo_IsStealing and not IsStealing then
                local prompt = findNearestStealPrompt()
                if prompt and prompt.Parent then
                    local holdDuration = prompt.HoldDuration or 1
                    pcall(function() fireproximityprompt(prompt) end)
                    combo_IsStealing = true; combo_StealProgress = 0
                    local startTime = tick()
                    task.spawn(function()
                        while combo_IsStealing do
                            local elapsed = tick()-startTime
                            combo_StealProgress = math.clamp(elapsed/holdDuration, 0, 1)
                            if combo_StealProgress >= 1 then
                                if autoAPOnSteal then
                                    local cl=getClosestPlayer()
                                    if cl then spamAPCommands(cl) end
                                end
                                if tpSystemEnabled then
                                    local char = player.Character
                                    local root = char and char:FindFirstChild("HumanoidRootPart")
                                    if root then
                                        local d1=(root.Position-pos1).Magnitude; local d2=(root.Position-pos2).Magnitude
                                        local seq = d1<d2 and spot1_sequence or spot2_sequence
                                        local bp = player:FindFirstChild("Backpack")
                                        if bp then
                                            local carpet=bp:FindFirstChild("Flying Carpet")
                                            local hum=char:FindFirstChild("Humanoid")
                                            if carpet and hum then hum:EquipTool(carpet) end
                                        end
                                        root.CFrame = seq[1]; task.wait(0.1)
                                        if root and root.Parent then root.CFrame = seq[2] end
                                    end
                                end
                                combo_IsStealing = false; combo_StealProgress = 0; combo_lastTrigger = tick(); break
                            end
                            task.wait(0.03)
                        end
                    end)
                end
            end
            task.wait(0.15)
        end
    end)
end

startComboStealMonitor()
startAutoStealLoop()

-- ===============================
-- PROXIMITY PROMPT HANDLERS
-- ===============================
local currentEquipTask = nil; local isHolding = false
local holdBeganConn = ProximityPromptService.PromptButtonHoldBegan:Connect(function(prompt, plr)
    if plr ~= player or not tpSystemEnabled then return end
    isHolding = true
    if currentEquipTask then task.cancel(currentEquipTask) end
    currentEquipTask = task.spawn(function()
        task.wait(1)
        if isHolding and tpSystemEnabled then
            local bp = player:WaitForChild("Backpack", 2)
            if bp then
                local carpet = bp:FindFirstChild("Flying Carpet")
                if carpet and player.Character and player.Character:FindFirstChild("Humanoid") then
                    player.Character.Humanoid:EquipTool(carpet)
                end
            end
        end
    end)
end)
table.insert(connections, holdBeganConn)

local holdEndedConn = ProximityPromptService.PromptButtonHoldEnded:Connect(function(prompt, plr)
    if plr ~= player then return end
    isHolding = false
    if currentEquipTask then task.cancel(currentEquipTask) end
end)
table.insert(connections, holdEndedConn)

local triggeredConn = ProximityPromptService.PromptTriggered:Connect(function(prompt, plr)
    if plr ~= player or not tpSystemEnabled then return end
    if autoPotion then
        local backpack = player:FindFirstChild("Backpack")
        if backpack then
            local potion = backpack:FindFirstChild("Giant Potion")
            if potion and player.Character and player.Character:FindFirstChild("Humanoid") then
                player.Character.Humanoid:EquipTool(potion)
                task.wait(0.05)
                pcall(function() potion:Activate() end)
            end
        end
    end
    isHolding = false
end)
table.insert(connections, triggeredConn)

_G.TweakzHubCleanup = function()
    combo_ComboActive = false
    for _, conn in ipairs(connections) do pcall(function() conn:Disconnect() end) end
    connections = {}
    for _, part in ipairs(workspace:GetChildren()) do
        if part.Name:find("TweakzHub") or part.Name:find("CyberESP") or part.Name:find("HoloEffect") then
            pcall(function() part:Destroy() end)
        end
    end
    local pg = player:FindFirstChild("PlayerGui")
    if pg then
        for _, g in ipairs({"TweakzHubUI","TweakzHubProgress"}) do
            local gui = pg:FindFirstChild(g); if gui then pcall(function() gui:Destroy() end) end
        end
    end
    _G.TweakzHubInstaSteal = false; _G.TweakzHubConnections = nil
end

-- ===============================
-- RGB COLOR UTILITY (VIBRANT)
-- ===============================
local hue = 0
local function getRainbowColor()
    hue = (hue + rgbSpeed * 0.008) % 1
    return Color3.fromHSV(hue, 1, 1)
end

-- ===============================
-- UI CONSTRUCTION - BLACK CYBER THEME (SMALLER)
-- ===============================
local BG       = Color3.fromRGB(0, 0, 0)       -- Pure black
local BG2      = Color3.fromRGB(8, 8, 12)      -- Dark cyber black
local ACCENT   = Color3.fromRGB(0, 255, 255)   -- Cyan (vibrant)
local ACCENT2  = Color3.fromRGB(150, 0, 255)   -- Purple
local TOGOFF   = Color3.fromRGB(30, 30, 40)    -- Dark grey
local TOGON    = Color3.fromRGB(0, 255, 200)   -- Bright cyan
local WHITE    = Color3.fromRGB(255, 255, 255)
local GREY     = Color3.fromRGB(120, 120, 140)
local BTN_BG   = Color3.fromRGB(15, 15, 20)    -- Dark button

local TweakzHubUI = Instance.new("ScreenGui")
TweakzHubUI.Name = "TweakzHubUI"
TweakzHubUI.Parent = player:WaitForChild("PlayerGui")
TweakzHubUI.ResetOnSpawn = false
TweakzHubUI.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Helper draggable
local function makeDraggable(frame, handle)
    local drag, ds, dp = false, nil, nil
    handle = handle or frame
    handle.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            drag = true; ds = i.Position; dp = frame.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            local d = i.Position - ds
            frame.Position = UDim2.new(dp.X.Scale, dp.X.Offset+d.X, dp.Y.Scale, dp.Y.Offset+d.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then drag = false end
    end)
end

-- Helper toggle with RGB option
local function makeToggle(parent, yOff, labelTxt, descTxt, callback, useRGB)
    local row = Instance.new("Frame", parent)
    row.Size = UDim2.new(1, -20, 0, 42)
    row.Position = UDim2.new(0, 10, 0, yOff)
    row.BackgroundTransparency = 1
    row.BorderSizePixel = 0

    local lbl = Instance.new("TextLabel", row)
    lbl.Size = UDim2.new(1, -52, 0, 18)
    lbl.Position = UDim2.new(0, 0, 0, 3)
    lbl.BackgroundTransparency = 1
    lbl.Text = labelTxt
    lbl.Font = Enum.Font.GothamBold
    lbl.TextSize = 12
    lbl.TextColor3 = WHITE
    lbl.TextXAlignment = Enum.TextXAlignment.Left

    local sub = Instance.new("TextLabel", row)
    sub.Size = UDim2.new(1, -52, 0, 14)
    sub.Position = UDim2.new(0, 0, 0, 22)
    sub.BackgroundTransparency = 1
    sub.Text = descTxt
    sub.Font = Enum.Font.Gotham
    sub.TextSize = 9
    sub.TextColor3 = GREY
    sub.TextXAlignment = Enum.TextXAlignment.Left

    local track = Instance.new("Frame", row)
    track.Size = UDim2.new(0, 38, 0, 20)
    track.Position = UDim2.new(1, -38, 0.5, -10)
    track.BackgroundColor3 = TOGOFF
    track.BorderSizePixel = 0
    Instance.new("UICorner", track).CornerRadius = UDim.new(1, 0)

    local knob = Instance.new("Frame", track)
    knob.Size = UDim2.new(0, 14, 0, 14)
    knob.Position = UDim2.new(0, 3, 0.5, -7)
    knob.BackgroundColor3 = WHITE
    knob.BorderSizePixel = 0
    Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

    local state = false
    local btn = Instance.new("TextButton", row)
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.ZIndex = 5
    
    local colorUpdater
    if useRGB then
        colorUpdater = RunService.RenderStepped:Connect(function()
            if rainbowMode then
                local c = getRainbowColor()
                track.BackgroundColor3 = state and c or TOGOFF
                lbl.TextColor3 = c
            elseif not state then
                track.BackgroundColor3 = TOGOFF
                lbl.TextColor3 = WHITE
            else
                track.BackgroundColor3 = TOGON
                lbl.TextColor3 = WHITE
            end
        end)
        table.insert(connections, colorUpdater)
    end
    
    btn.MouseButton1Click:Connect(function()
        state = not state
        if not useRGB or not rainbowMode then
            TweenService:Create(track, TweenInfo.new(0.18), {BackgroundColor3 = state and TOGON or TOGOFF}):Play()
        end
        TweenService:Create(knob, TweenInfo.new(0.18), {Position = state and UDim2.new(1,-17,0.5,-7) or UDim2.new(0,3,0.5,-7)}):Play()
        if callback then callback(state) end
    end)
    return row
end

-- Separator line with glow
local function makeSep(parent, yOff)
    local s = Instance.new("Frame", parent)
    s.Size = UDim2.new(1, -20, 0, 1)
    s.Position = UDim2.new(0, 10, 0, yOff)
    s.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
    s.BorderSizePixel = 0
    local glow = Instance.new("UIGradient", s)
    glow.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 200, 255)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(100, 0, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 255, 200))
    })
    return s
end

-- ===============================
-- MAIN PANEL - TWEAKZ HUB (SMALLER)
-- ===============================
local MainFrame = Instance.new("Frame", TweakzHubUI)
MainFrame.Size = UDim2.new(0, 280, 0, 360)  -- Smaller size
MainFrame.Position = UDim2.new(0.65, 0, 0.12, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.BackgroundTransparency = 0.15
MainFrame.ZIndex = 10
MainFrame.ClipsDescendants = true
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 12)

-- Cyber border effect
local mStroke = Instance.new("UIStroke", MainFrame)
mStroke.Color = ACCENT
mStroke.Thickness = 2
mStroke.Transparency = 0.3

local mGrad = Instance.new("UIGradient", mStroke)
mGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0,   ACCENT),
    ColorSequenceKeypoint.new(0.33, Color3.fromRGB(0, 255, 200)),
    ColorSequenceKeypoint.new(0.66, ACCENT2),
    ColorSequenceKeypoint.new(1,   ACCENT)
})

-- Animated border
RunService.RenderStepped:Connect(function()
    if rainbowMode then
        mStroke.Color = getRainbowColor()
    else
        mStroke.Color = ACCENT
    end
    mGrad.Rotation = (mGrad.Rotation + 1.5) % 360
end)

-- Header
local Header = Instance.new("Frame", MainFrame)
Header.Size = UDim2.new(1, 0, 0, 42)
Header.BackgroundTransparency = 1
Header.BorderSizePixel = 0

-- Glowing title
local TitleLbl = Instance.new("TextLabel", Header)
TitleLbl.Size = UDim2.new(1, -50, 1, 0)
TitleLbl.Position = UDim2.new(0, 12, 0, 0)
TitleLbl.BackgroundTransparency = 1
TitleLbl.Text = "⚡ TWEAKZ HUB ⚡"
TitleLbl.Font = Enum.Font.GothamBlack
TitleLbl.TextSize = 18
TitleLbl.TextColor3 = ACCENT
TitleLbl.TextXAlignment = Enum.TextXAlignment.Left
TitleLbl.TextStrokeTransparency = 0.2
TitleLbl.TextStrokeColor3 = Color3.fromRGB(0, 200, 255)

-- Title RGB pulse
task.spawn(function()
    while true do
        if rainbowMode then
            for i = 0, 1, 0.05 do
                TitleLbl.TextColor3 = getRainbowColor()
                task.wait(0.02)
            end
        else
            TitleLbl.TextColor3 = ACCENT
            task.wait(0.1)
        end
    end
end)

-- Minimize button
local MinBtn = Instance.new("TextButton", Header)
MinBtn.Size = UDim2.new(0, 24, 0, 24)
MinBtn.Position = UDim2.new(1, -34, 0.5, -12)
MinBtn.BackgroundColor3 = BTN_BG
MinBtn.Text = "−"
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 14
MinBtn.TextColor3 = ACCENT
MinBtn.AutoButtonColor = false
MinBtn.BorderSizePixel = 0
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 6)

makeDraggable(MainFrame, Header)

-- Body
local Body = Instance.new("Frame", MainFrame)
Body.Size = UDim2.new(1, 0, 1, -42)
Body.Position = UDim2.new(0, 0, 0, 42)
Body.BackgroundTransparency = 1

-- TABS
local TabBarFrame = Instance.new("Frame", Body)
TabBarFrame.Size = UDim2.new(1, -16, 0, 40)
TabBarFrame.Position = UDim2.new(0, 8, 0, 6)
TabBarFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 10)
TabBarFrame.BorderSizePixel = 0
Instance.new("UICorner", TabBarFrame).CornerRadius = UDim.new(0, 16)

local tabSteal = Instance.new("TextButton", TabBarFrame)
tabSteal.Size = UDim2.new(0.5, -5, 1, 0)
tabSteal.Position = UDim2.new(0, 0, 0, 0)
tabSteal.BackgroundTransparency = 1
tabSteal.Text = "⚔️ STEAL"
tabSteal.TextColor3 = ACCENT
tabSteal.Font = Enum.Font.GothamBold
tabSteal.TextSize = 13
tabSteal.AutoButtonColor = false

local ulSteal = Instance.new("Frame", tabSteal)
ulSteal.Size = UDim2.new(0.8, 0, 0, 2)
ulSteal.Position = UDim2.new(0.1, 0, 1, -2)
ulSteal.BackgroundColor3 = ACCENT
ulSteal.BorderSizePixel = 0

local tabMisc = Instance.new("TextButton", TabBarFrame)
tabMisc.Size = UDim2.new(0.5, -5, 1, 0)
tabMisc.Position = UDim2.new(0.5, 5, 0, 0)
tabMisc.BackgroundTransparency = 1
tabMisc.Text = "🎮 MISC"
tabMisc.TextColor3 = GREY
tabMisc.Font = Enum.Font.GothamBold
tabMisc.TextSize = 13
tabMisc.AutoButtonColor = false

local ulMisc = Instance.new("Frame", tabMisc)
ulMisc.Size = UDim2.new(0.8, 0, 0, 2)
ulMisc.Position = UDim2.new(0.1, 0, 1, -2)
ulMisc.BackgroundColor3 = ACCENT
ulMisc.BorderSizePixel = 0
ulMisc.Visible = false

-- Pages
local pageSteal = Instance.new("ScrollingFrame", Body)
pageSteal.Size = UDim2.new(1, 0, 1, -52)
pageSteal.Position = UDim2.new(0, 0, 0, 52)
pageSteal.BackgroundTransparency = 1
pageSteal.CanvasSize = UDim2.new(0, 0, 0, 350)
pageSteal.ScrollBarThickness = 3
pageSteal.ScrollBarImageColor3 = ACCENT

local pageMisc = Instance.new("ScrollingFrame", Body)
pageMisc.Size = UDim2.new(1, 0, 1, -52)
pageMisc.Position = UDim2.new(0, 0, 0, 52)
pageMisc.BackgroundTransparency = 1
pageMisc.CanvasSize = UDim2.new(0, 0, 0, 220)
pageMisc.ScrollBarThickness = 3
pageMisc.ScrollBarImageColor3 = ACCENT
pageMisc.Visible = false

local function switchTab(toSteal)
    pageSteal.Visible = toSteal
    pageMisc.Visible = not toSteal
    tabSteal.TextColor3 = toSteal and ACCENT or GREY
    tabMisc.TextColor3  = not toSteal and ACCENT or GREY
    ulSteal.Visible = toSteal
    ulMisc.Visible  = not toSteal
end
tabSteal.MouseButton1Click:Connect(function() switchTab(true) end)
tabMisc.MouseButton1Click:Connect(function() switchTab(false) end)

-- ===============================
-- STEAL PAGE TOGGLES
-- ===============================
local stealY = 4

makeSep(pageSteal, stealY); stealY = stealY + 8
makeToggle(pageSteal, stealY, "ACTIVATE SYSTEM", "Performs desync & maintains optimization flags", function(s) tpSystemEnabled = s end, false)
stealY = stealY + 50

makeSep(pageSteal, stealY); stealY = stealY + 8
makeToggle(pageSteal, stealY, "GIANT POTION", "Activates Giant Potion during steal", function(s) autoPotion = s end, false)
stealY = stealY + 50

makeSep(pageSteal, stealY); stealY = stealY + 8
makeToggle(pageSteal, stealY, "AUTO AP ON STEAL", "Spams AP commands on nearest player", function(s) autoAPOnSteal = s end, false)
stealY = stealY + 50

makeSep(pageSteal, stealY); stealY = stealY + 8
makeToggle(pageSteal, stealY, "AUTO KICK ON STEAL", "Kicks you when someone steals", function(s) autoKickOnSteal = s; setupAutoKick() end, false)
stealY = stealY + 50

-- Speed Boost row
makeSep(pageSteal, stealY); stealY = stealY + 8
local sbRow = Instance.new("Frame", pageSteal)
sbRow.Size = UDim2.new(1, -20, 0, 42)
sbRow.Position = UDim2.new(0, 10, 0, stealY)
sbRow.BackgroundTransparency = 1

local sbLbl = Instance.new("TextLabel", sbRow)
sbLbl.Size = UDim2.new(1, -90, 0, 18)
sbLbl.BackgroundTransparency = 1
sbLbl.Text = "🏃 SPEED BOOST"
sbLbl.Font = Enum.Font.GothamBold
sbLbl.TextSize = 12; sbLbl.TextColor3 = WHITE
sbLbl.TextXAlignment = Enum.TextXAlignment.Left

local sbSub = Instance.new("TextLabel", sbRow)
sbSub.Size = UDim2.new(1, -90, 0, 14)
sbSub.Position = UDim2.new(0, 0, 0, 20)
sbSub.BackgroundTransparency = 1
sbSub.Text = "Increases movement speed"
sbSub.Font = Enum.Font.Gotham; sbSub.TextSize = 9; sbSub.TextColor3 = GREY
sbSub.TextXAlignment = Enum.TextXAlignment.Left

local sbBox = Instance.new("TextBox", sbRow)
sbBox.Size = UDim2.new(0, 40, 0, 22)
sbBox.Position = UDim2.new(1, -90, 0.5, -11)
sbBox.BackgroundColor3 = BTN_BG; sbBox.BorderSizePixel = 0
sbBox.Text = "29"; sbBox.Font = Enum.Font.GothamBold
sbBox.TextSize = 12; sbBox.TextColor3 = ACCENT
Instance.new("UICorner", sbBox).CornerRadius = UDim.new(0, 6)
sbBox.FocusLost:Connect(function()
    local n = tonumber(sbBox.Text)
    speedValue = (n and n > 0) and n or speedValue
    sbBox.Text = tostring(speedValue)
end)

local sbTrack = Instance.new("Frame", sbRow)
sbTrack.Size = UDim2.new(0, 38, 0, 20)
sbTrack.Position = UDim2.new(1, -38, 0.5, -10)
sbTrack.BackgroundColor3 = TOGOFF; sbTrack.BorderSizePixel = 0
Instance.new("UICorner", sbTrack).CornerRadius = UDim.new(1, 0)

local sbKnob = Instance.new("Frame", sbTrack)
sbKnob.Size = UDim2.new(0, 14, 0, 14); sbKnob.Position = UDim2.new(0, 3, 0.5, -7)
sbKnob.BackgroundColor3 = WHITE; sbKnob.BorderSizePixel = 0
Instance.new("UICorner", sbKnob).CornerRadius = UDim.new(1, 0)

local sbBtn = Instance.new("TextButton", sbRow)
sbBtn.Size = UDim2.new(0, 38, 0, 20); sbBtn.Position = UDim2.new(1, -38, 0.5, -10)
sbBtn.BackgroundTransparency = 1; sbBtn.Text = ""; sbBtn.ZIndex = 5
sbBtn.MouseButton1Click:Connect(function()
    speedBoostEnabled = not speedBoostEnabled
    TweenService:Create(sbTrack, TweenInfo.new(0.18), {BackgroundColor3 = speedBoostEnabled and TOGON or TOGOFF}):Play()
    TweenService:Create(sbKnob, TweenInfo.new(0.18), {Position = speedBoostEnabled and UDim2.new(1,-17,0.5,-7) or UDim2.new(0,3,0.5,-7)}):Play()
end)

stealY = stealY + 50

-- Unlock Base Button
makeSep(pageSteal, stealY); stealY = stealY + 8
local unlockBtn = Instance.new("TextButton", pageSteal)
unlockBtn.Size = UDim2.new(1, -20, 0, 42)
unlockBtn.Position = UDim2.new(0, 10, 0, stealY)
unlockBtn.BackgroundColor3 = BTN_BG
unlockBtn.Text = "🔓 UNLOCK BASE 🔓"
unlockBtn.TextColor3 = ACCENT
unlockBtn.Font = Enum.Font.GothamBold
unlockBtn.TextSize = 13
unlockBtn.AutoButtonColor = false
unlockBtn.BorderSizePixel = 0
Instance.new("UICorner", unlockBtn).CornerRadius = UDim.new(0, 8)

unlockBtn.MouseEnter:Connect(function() 
    TweenService:Create(unlockBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(25, 25, 35)}):Play()
end)
unlockBtn.MouseLeave:Connect(function() 
    TweenService:Create(unlockBtn, TweenInfo.new(0.12), {BackgroundColor3 = BTN_BG}):Play()
end)
unlockBtn.MouseButton1Click:Connect(function()
    TweenService:Create(unlockBtn, TweenInfo.new(0.08), {BackgroundColor3 = Color3.fromRGB(0, 200, 255)}):Play()
    task.delay(0.15, function() 
        TweenService:Create(unlockBtn, TweenInfo.new(0.12), {BackgroundColor3 = BTN_BG}):Play()
    end)
    triggerClosestUnlock(-2, 19)
end)

stealY = stealY + 50
pageSteal.CanvasSize = UDim2.new(0, 0, 0, stealY + 20)

-- ===============================
-- MISC PAGE
-- ===============================
local miscY = 4
makeSep(pageMisc, miscY); miscY = miscY + 8

-- Discord button
local discordBtn = Instance.new("TextButton", pageMisc)
discordBtn.Size = UDim2.new(1, -20, 0, 42)
discordBtn.Position = UDim2.new(0, 10, 0, miscY)
discordBtn.BackgroundColor3 = BTN_BG
discordBtn.Text = "💬 JOIN DISCORD"
discordBtn.TextColor3 = ACCENT
discordBtn.Font = Enum.Font.GothamBold
discordBtn.TextSize = 13
discordBtn.AutoButtonColor = false
discordBtn.BorderSizePixel = 0
Instance.new("UICorner", discordBtn).CornerRadius = UDim.new(0, 8)
discordBtn.MouseButton1Click:Connect(function()
    setclipboard("https://discord.gg/bbCuEsuPH")
end)

miscY = miscY + 50

-- RGB Mode Toggle
makeSep(pageMisc, miscY); miscY = miscY + 8
makeToggle(pageMisc, miscY, "🌈 RGB MODE", "Enable rainbow UI effects", function(s) 
    rainbowMode = s
    if s then
        rgbSpeed = 1
    end
end, true)
miscY = miscY + 50

-- RGB Speed Slider
local speedRow = Instance.new("Frame", pageMisc)
speedRow.Size = UDim2.new(1, -20, 0, 42)
speedRow.Position = UDim2.new(0, 10, 0, miscY)
speedRow.BackgroundTransparency = 1

local speedLbl = Instance.new("TextLabel", speedRow)
speedLbl.Size = UDim2.new(0.5, -10, 0, 18)
speedLbl.BackgroundTransparency = 1
speedLbl.Text = "RGB SPEED"
speedLbl.Font = Enum.Font.GothamBold
speedLbl.TextSize = 12; speedLbl.TextColor3 = WHITE
speedLbl.TextXAlignment = Enum.TextXAlignment.Left

local speedVal = Instance.new("TextLabel", speedRow)
speedVal.Size = UDim2.new(0.3, -10, 0, 18)
speedVal.Position = UDim2.new(0.7, 0, 0, 0)
speedVal.BackgroundTransparency = 1
speedVal.Text = "1.0x"
speedVal.Font = Enum.Font.GothamBold
speedVal.TextSize = 12; speedVal.TextColor3 = ACCENT
speedVal.TextXAlignment = Enum.TextXAlignment.Right

local slider = Instance.new("Frame", speedRow)
slider.Size = UDim2.new(1, 0, 0, 4)
slider.Position = UDim2.new(0, 0, 0, 28)
slider.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
slider.BorderSizePixel = 0
Instance.new("UICorner", slider).CornerRadius = UDim.new(0, 2)

local sliderFill = Instance.new("Frame", slider)
sliderFill.Size = UDim2.new(rgbSpeed, 0, 1, 0)
sliderFill.BackgroundColor3 = ACCENT
sliderFill.BorderSizePixel = 0
Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(0, 2)

local sliderButton = Instance.new("TextButton", slider)
sliderButton.Size = UDim2.new(0, 10, 0, 10)
sliderButton.Position = UDim2.new(rgbSpeed, -5, 0.5, -5)
sliderButton.BackgroundColor3 = ACCENT
sliderButton.Text = ""
sliderButton.BorderSizePixel = 0
Instance.new("UICorner", sliderButton).CornerRadius = UDim.new(1, 0)

local dragging = false
sliderButton.MouseButton1Down:Connect(function()
    dragging = true
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local pos = input.Position.X - slider.AbsolutePosition.X
        local width = slider.AbsoluteSize.X
        local val = math.clamp(pos / width, 0.2, 3)
        rgbSpeed = val
        sliderFill.Size = UDim2.new(rgbSpeed, 0, 1, 0)
        sliderButton.Position = UDim2.new(rgbSpeed, -5, 0.5, -5)
        speedVal.Text = string.format("%.1fx", rgbSpeed)
    end
end)

miscY = miscY + 50
pageMisc.CanvasSize = UDim2.new(0, 0, 0, miscY + 20)

-- Minimize main panel
local mainMinimized = false
MinBtn.MouseButton1Click:Connect(function()
    mainMinimized = not mainMinimized
    TweenService:Create(MainFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
        Size = mainMinimized and UDim2.new(0, 280, 0, 42) or UDim2.new(0, 280, 0, 360)
    }):Play()
    MinBtn.Text = mainMinimized and "+" or "−"
end)

-- ===============================
-- TP PANEL - BLACK CYBER
-- ===============================
local TPFrame = Instance.new("Frame", TweakzHubUI)
TPFrame.Size = UDim2.new(0, 220, 0, 85)
TPFrame.Position = UDim2.new(0.18, 0, 0.38, 0)
TPFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
TPFrame.BackgroundTransparency = 0.2
TPFrame.ZIndex = 10
TPFrame.ClipsDescendants = true
Instance.new("UICorner", TPFrame).CornerRadius = UDim.new(0, 10)

local tpStroke = Instance.new("UIStroke", TPFrame)
tpStroke.Color = ACCENT
tpStroke.Thickness = 1.5
tpStroke.Transparency = 0.4

local tpGrad = Instance.new("UIGradient", tpStroke)
tpGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0,   ACCENT),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 255, 150)),
    ColorSequenceKeypoint.new(1,   ACCENT)
})

RunService.RenderStepped:Connect(function()
    if rainbowMode then
        tpStroke.Color = getRainbowColor()
    else
        tpStroke.Color = ACCENT
    end
    tpGrad.Rotation = (tpGrad.Rotation + 1.5) % 360
end)

-- TP Header
local TPHeader = Instance.new("Frame", TPFrame)
TPHeader.Size = UDim2.new(1, 0, 0, 36)
TPHeader.BackgroundColor3 = BG2
TPHeader.BorderSizePixel = 0
Instance.new("UICorner", TPHeader).CornerRadius = UDim.new(0, 10)

local TPTitle = Instance.new("TextLabel", TPHeader)
TPTitle.Size = UDim2.new(1, -44, 1, 0)
TPTitle.Position = UDim2.new(0, 10, 0, 0)
TPTitle.BackgroundTransparency = 1
TPTitle.Text = "⚡ INSTANT STEAL TP ⚡"
TPTitle.Font = Enum.Font.GothamBold
TPTitle.TextSize = 11
TPTitle.TextColor3 = ACCENT
TPTitle.TextXAlignment = Enum.TextXAlignment.Left

local TPMinBtn = Instance.new("TextButton", TPHeader)
TPMinBtn.Size = UDim2.new(0, 22, 0, 22)
TPMinBtn.Position = UDim2.new(1, -30, 0.5, -11)
TPMinBtn.BackgroundColor3 = BTN_BG
TPMinBtn.Text = "−"
TPMinBtn.Font = Enum.Font.GothamBold
TPMinBtn.TextSize = 12
TPMinBtn.TextColor3 = ACCENT
TPMinBtn.AutoButtonColor = false
TPMinBtn.BorderSizePixel = 0
Instance.new("UICorner", TPMinBtn).CornerRadius = UDim.new(0, 5)

makeDraggable(TPFrame, TPHeader)

-- TP Body
local TPBody = Instance.new("Frame", TPFrame)
TPBody.Size = UDim2.new(1, 0, 0, 49)
TPBody.Position = UDim2.new(0, 0, 0, 36)
TPBody.BackgroundTransparency = 1

-- TP LEFT button
local TPLeftBtn = Instance.new("TextButton", TPBody)
TPLeftBtn.Size = UDim2.new(0.5, -10, 0, 34)
TPLeftBtn.Position = UDim2.new(0, 6, 0, 8)
TPLeftBtn.BackgroundColor3 = BTN_BG
TPLeftBtn.BorderSizePixel = 0
TPLeftBtn.Text = "◀ TP LEFT"
TPLeftBtn.Font = Enum.Font.GothamBold
TPLeftBtn.TextSize = 12
TPLeftBtn.TextColor3 = ACCENT
TPLeftBtn.AutoButtonColor = false
Instance.new("UICorner", TPLeftBtn).CornerRadius = UDim.new(0, 6)

TPLeftBtn.MouseEnter:Connect(function() 
    TweenService:Create(TPLeftBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(25, 25, 35)}):Play()
end)
TPLeftBtn.MouseLeave:Connect(function() 
    TweenService:Create(TPLeftBtn, TweenInfo.new(0.12), {BackgroundColor3 = BTN_BG}):Play()
end)
TPLeftBtn.MouseButton1Click:Connect(function()
    if IsStealing then return end
    local animal = getNearestAnimal(); if not animal then return end
    local prompt = findPrompt(animal); if not prompt then return end
    buildStealCallbacks(prompt); executeInternalStealLeft(prompt, animal)
end)

-- TP RIGHT button
local TPRightBtn = Instance.new("TextButton", TPBody)
TPRightBtn.Size = UDim2.new(0.5, -10, 0, 34)
TPRightBtn.Position = UDim2.new(0.5, 4, 0, 8)
TPRightBtn.BackgroundColor3 = BTN_BG
TPRightBtn.BorderSizePixel = 0
TPRightBtn.Text = "TP RIGHT ▶"
TPRightBtn.Font = Enum.Font.GothamBold
TPRightBtn.TextSize = 12
TPRightBtn.TextColor3 = ACCENT
TPRightBtn.AutoButtonColor = false
Instance.new("UICorner", TPRightBtn).CornerRadius = UDim.new(0, 6)

TPRightBtn.MouseEnter:Connect(function() 
    TweenService:Create(TPRightBtn, TweenInfo.new(0.12), {BackgroundColor3 = Color3.fromRGB(25, 25, 35)}):Play()
end)
TPRightBtn.MouseLeave:Connect(function() 
    TweenService:Create(TPRightBtn, TweenInfo.new(0.12), {BackgroundColor3 = BTN_BG}):Play()
end)
TPRightBtn.MouseButton1Click:Connect(function()
    if IsStealing then return end
    local animal = getNearestAnimal(); if not animal then return end
    local prompt = findPrompt(animal); if not prompt then return end
    buildStealCallbacks(prompt); executeInternalStealRight(prompt, animal)
end)

-- TP Minimize
local tpMinimized = false
TPMinBtn.MouseButton1Click:Connect(function()
    tpMinimized = not tpMinimized
    TweenService:Create(TPFrame, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {
        Size = tpMinimized and UDim2.new(0, 220, 0, 36) or UDim2.new(0, 220, 0, 85)
    }):Play()
    TPMinBtn.Text = tpMinimized and "+" or "−"
end)

-- ===============================
-- PROGRESS BAR - BLACK CYBER
-- ===============================
local ProgressGui = Instance.new("ScreenGui", player.PlayerGui)
ProgressGui.Name = "TweakzHubProgress"
ProgressGui.ResetOnSpawn = false
ProgressGui.DisplayOrder = 100

local ProgressContainer = Instance.new("Frame", ProgressGui)
ProgressContainer.Size = UDim2.new(0, 280, 0, 45)
ProgressContainer.Position = UDim2.new(0.5, -140, 1, -100)
ProgressContainer.BackgroundColor3 = BG
ProgressContainer.BackgroundTransparency = 0.15
ProgressContainer.BorderSizePixel = 0
Instance.new("UICorner", ProgressContainer).CornerRadius = UDim.new(0, 8)

-- Cyber border for progress
local progStroke = Instance.new("UIStroke", ProgressContainer)
progStroke.Color = ACCENT
progStroke.Thickness = 1
progStroke.Transparency = 0.5

local pDragging, pDragInput, pDragStart, pStartPos
ProgressContainer.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        pDragging = true; pDragStart = i.Position; pStartPos = ProgressContainer.Position 
    end 
end)
UserInputService.InputChanged:Connect(function(i)
    if pDragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        pDragInput = i 
    end 
end)
RunService.RenderStepped:Connect(function()
    if pDragging and pDragInput then
        local d = pDragInput.Position - pDragStart
        ProgressContainer.Position = UDim2.new(pStartPos.X.Scale, pStartPos.X.Offset+d.X, pStartPos.Y.Scale, pStartPos.Y.Offset+d.Y)
    end 
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then pDragging = false end 
end)

local progText = Instance.new("TextLabel", ProgressContainer)
progText.Size = UDim2.new(1, -10, 0, 16)
progText.Position = UDim2.new(0, 5, 0, 3)
progText.BackgroundTransparency = 1
progText.TextColor3 = ACCENT
progText.Font = Enum.Font.GothamBold
progText.TextSize = 10
progText.Text = "⚡ STEAL PROGRESS ⚡"
progText.TextXAlignment = Enum.TextXAlignment.Left

local progBacking = Instance.new("Frame", ProgressContainer)
progBacking.Size = UDim2.new(1, -10, 0, 12)
progBacking.Position = UDim2.new(0, 5, 0, 24)
progBacking.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
progBacking.BorderSizePixel = 0
Instance.new("UICorner", progBacking).CornerRadius = UDim.new(0, 3)

local progFill = Instance.new("Frame", progBacking)
progFill.Size = UDim2.new(0, 0, 1, 0)
progFill.BackgroundColor3 = ACCENT
progFill.BorderSizePixel = 0
Instance.new("UICorner", progFill).CornerRadius = UDim.new(0, 3)

-- Animated fill gradient
local fillGrad = Instance.new("UIGradient", progFill)
fillGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, ACCENT),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(100, 255, 200)),
    ColorSequenceKeypoint.new(1, ACCENT)
})
task.spawn(function()
    while true do
        fillGrad.Rotation = (fillGrad.Rotation + 2) % 360
        task.wait(0.05)
    end
end)

local pct = Instance.new("TextLabel", progBacking)
pct.Size = UDim2.new(1, -6, 1, 0)
pct.BackgroundTransparency = 1
pct.Text = "0%"
pct.TextColor3 = WHITE
pct.TextSize = 9
pct.Font = Enum.Font.GothamBold
pct.TextXAlignment = Enum.TextXAlignment.Right

-- Progress update loop
task.spawn(function()
    while true do
        local progress = math.max(StealProgress, combo_StealProgress)
        local targetWidth = math.clamp(progress, 0, 1)
        TweenService:Create(progFill, TweenInfo.new(0.1), {Size = UDim2.new(targetWidth, 0, 1, 0)}):Play()
        pct.Text = math.floor(progress * 100 + 0.5) .. "%"
        
        -- Color based on progress
        if progress >= 0.9 then
            progFill.BackgroundColor3 = Color3.fromRGB(0, 255, 200)
        elseif progress >= 0.5 then
            progFill.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
        else
            progFill.BackgroundColor3 = ACCENT
        end
        
        if rainbowMode then
            progFill.BackgroundColor3 = getRainbowColor()
            progText.TextColor3 = getRainbowColor()
        else
            progFill.BackgroundColor3 = ACCENT
            progText.TextColor3 = ACCENT
        end
        
        task.wait(0.02)
    end
end)

-- ===============================
-- KEYBIND SYSTEM (no UI buttons, just logic)
-- ===============================
local function getInputName(input)
    if input.UserInputType == Enum.UserInputType.Gamepad1 then return "GP:"..input.KeyCode.Name end
    return input.KeyCode.Name
end

local function keybindMatches(input, boundKey)
    if boundKey == nil then return false end
    if input.UserInputType == Enum.UserInputType.Gamepad1 then return boundKey == "GP:"..input.KeyCode.Name end
    return boundKey == input.KeyCode.Name
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    local isKey = input.UserInputType == Enum.UserInputType.Keyboard
    local isGP  = input.UserInputType == Enum.UserInputType.Gamepad1

    if isWaitingForKeybindLeft then
        if isKey and input.KeyCode == Enum.KeyCode.Escape then
            keybindLeftKey = nil
        elseif isKey or isGP then
            keybindLeftKey = getInputName(input)
        end
        isWaitingForKeybindLeft = false
        return
    end
    if isWaitingForKeybindRight then
        if isKey and input.KeyCode == Enum.KeyCode.Escape then
            keybindRightKey = nil
        elseif isKey or isGP then
            keybindRightKey = getInputName(input)
        end
        isWaitingForKeybindRight = false
        return
    end
    if isKey and input.KeyCode == Enum.KeyCode.Escape then
        keybindLeftKey = nil
        keybindRightKey = nil
        return
    end
    if keybindMatches(input, keybindLeftKey) then
        if IsStealing then return end
        local animal = getNearestAnimal(); if not animal then return end
        local prompt = findPrompt(animal); if not prompt then return end
        buildStealCallbacks(prompt); executeInternalStealLeft(prompt, animal)
        return
    end
    if keybindMatches(input, keybindRightKey) then
        if IsStealing then return end
        local animal = getNearestAnimal(); if not animal then return end
        local prompt = findPrompt(animal); if not prompt then return end
        buildStealCallbacks(prompt); executeInternalStealRight(prompt, animal)
        return
    end
end)

-- ===============================
-- INITIALIZATION
-- ===============================
task.spawn(initializeScanner)

-- Print success message
task.wait(1)
print("✅ TWEAKZ HUB - Black Cyber Edition Loaded!")
print("💜 Features: Instant Steal TP | Speed Boost | RGB UI | Auto AP/Kick")
