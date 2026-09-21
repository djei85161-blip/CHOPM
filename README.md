-- ====================================================================
-- CHOMPER v16 | FTAP | VORTEX-STYLE
-- PART 1/2: KICK + DEFENSE (Main + Extra + AntiKick)
-- Запусти ПОСЛЕ этого part2
-- ====================================================================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local Lighting = game:GetService("Lighting")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local RS = ReplicatedStorage

repeat task.wait() until LocalPlayer.Character
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents", 15)
local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
local DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
local ExtendGrabLine = GrabEvents:FindFirstChild("ExtendGrabLine")
local EndGrabEarly = GrabEvents:FindFirstChild("EndGrabEarly")
local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents", 15)
local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")
local Struggle = CharacterEvents:FindFirstChild("Struggle")
local MenuToys = ReplicatedStorage:WaitForChild("MenuToys", 15)
local SpawnToyRF = MenuToys:WaitForChild("SpawnToyRemoteFunction")
local DestroyToy = MenuToys:WaitForChild("DestroyToy")
local PlayerEvents = ReplicatedStorage:WaitForChild("PlayerEvents", 15)
local StickyPartEvent = PlayerEvents:FindFirstChild("StickyPartEvent")

local function notify(t, d, dur)
    pcall(function()
        game:GetService("StarterGui"):SetCore("SendNotification", { Title = t, Text = d, Duration = dur or 3 })
    end)
end

-- ============================================================
-- Shared в _G для part2
-- ============================================================
_G.C16 = {
    Players = Players, RunService = RunService, RS = RS, Workspace = Workspace,
    Debris = Debris, Lighting = Lighting, UIS = UserInputService, TweenService = TweenService,
    LP = LocalPlayer, Cam = Camera,
    SNO = SetNetworkOwner, CGL = CreateGrabLine, DGL = DestroyGrabLine,
    EGL = ExtendGrabLine, EGE = EndGrabEarly,
    RR = RagdollRemote, SG = Struggle,
    STR = SpawnToyRF, DT = DestroyToy, SPE = StickyPartEvent,
    notify = notify,
}

-- ============================================================
-- BARRIER DISABLE + HAMBURGER TP
-- ============================================================
local function disableBarriers()
    local plots = Workspace:FindFirstChild("Plots")
    if not plots then return 0 end
    local n = 0
    for _, p in ipairs(plots:GetDescendants()) do
        if p:IsA("BasePart") and p.Name == "PlotBarrier" then
            p.CanCollide = false
            p.CanTouch = false
            p.CanQuery = false
            p.Transparency = 1
            n = n + 1
        end
    end
    return n
end
task.spawn(function() while true do disableBarriers() task.wait(3) end end)

local function hamburgerTP()
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then notify("CHOMPER", "HRP не найден", 3) return end
    local orig = hrp.CFrame
    local MT = RS:FindFirstChild("MenuToys")
    local STR = MT and MT:FindFirstChild("SpawnToyRemoteFunction")
    if not STR then return end
    STR:InvokeServer("FoodHamburger", hrp.CFrame, Vector3.zero)
    local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not folder then return end
    local burger = folder:WaitForChild("FoodHamburger", 5)
    if not burger then return end
    local hp = burger:FindFirstChild("HoldPart")
    local holdRF = hp and hp:FindFirstChild("HoldItemRemoteFunction")
    if holdRF then holdRF:InvokeServer(burger, char) end
    local plots = Workspace:FindFirstChild("Plots")
    local p3 = plots and plots:FindFirstChild("Plot3")
    local pa = p3 and p3:FindFirstChild("PlotArea")
    if pa then
        hrp.CFrame = pa.CFrame
        task.wait(0.1)
        hrp.CFrame = orig
    end
    if MT and MT:FindFirstChild("DestroyToy") then
        MT.DestroyToy:FireServer(burger)
    end
    notify("CHOMPER", "Barrier broken", 3)
end
_G.C16.hamburgerTP = hamburgerTP

-- ============================================================
-- GUI
-- ============================================================
for _, v in pairs(LocalPlayer.PlayerGui:GetChildren()) do
    if v.Name == "ChomperHub" then v:Destroy() end
end

local gui = Instance.new("ScreenGui")
gui.Name = "ChomperHub"
gui.Parent = LocalPlayer.PlayerGui
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Global

local main = Instance.new("Frame")
main.Parent = gui
main.Size = UDim2.new(0, 640, 0, 460)
main.Position = UDim2.new(0.5, -320, 0.5, -230)
main.BackgroundColor3 = Color3.fromRGB(15, 15, 18)
main.BorderSizePixel = 1
main.BorderColor3 = Color3.fromRGB(45, 45, 55)
main.Active = true
main.Draggable = true

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = main

local titleBar = Instance.new("Frame")
titleBar.Parent = main
titleBar.Size = UDim2.new(1, 0, 0, 32)
titleBar.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
titleBar.BorderSizePixel = 0

local cornerTitle = Instance.new("UICorner")
cornerTitle.CornerRadius = UDim.new(0, 10)
cornerTitle.Parent = titleBar

local title = Instance.new("TextLabel")
title.Parent = titleBar
title.Size = UDim2.new(1, -60, 1, 0)
title.Position = UDim2.new(0, 12, 0, 0)
title.BackgroundTransparency = 1
title.Text = "CHOMPER v16 | VORTEX EDITION"
title.TextColor3 = Color3.fromRGB(255, 200, 100)
title.TextSize = 14
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left

local closeBtn = Instance.new("TextButton")
closeBtn.Parent = titleBar
closeBtn.Size = UDim2.new(0, 24, 0, 22)
closeBtn.Position = UDim2.new(1, -28, 0.5, -11)
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeBtn.BorderSizePixel = 0
closeBtn.Text = "×"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.TextSize = 16
closeBtn.Font = Enum.Font.GothamBold
closeBtn.MouseButton1Click:Connect(function() main.Visible = false end)

local cornerClose = Instance.new("UICorner")
cornerClose.CornerRadius = UDim.new(0, 4)
cornerClose.Parent = closeBtn

local showBtn = Instance.new("TextButton")
showBtn.Parent = gui
showBtn.Size = UDim2.new(0, 90, 0, 32)
showBtn.Position = UDim2.new(0.02, 0, 0.85, 0)
showBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
showBtn.BorderSizePixel = 1
showBtn.BorderColor3 = Color3.fromRGB(50, 50, 65)
showBtn.Text = "CHOMPER"
showBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
showBtn.TextSize = 11
showBtn.Font = Enum.Font.GothamBold
showBtn.Active = true
showBtn.Draggable = true
showBtn.MouseButton1Click:Connect(function() main.Visible = not main.Visible end)

local cornerShow = Instance.new("UICorner")
cornerShow.CornerRadius = UDim.new(0, 6)
cornerShow.Parent = showBtn

local tabBar = Instance.new("Frame")
tabBar.Parent = main
tabBar.Size = UDim2.new(1, -16, 0, 30)
tabBar.Position = UDim2.new(0, 8, 0, 40)
tabBar.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
tabBar.BorderSizePixel = 1
tabBar.BorderColor3 = Color3.fromRGB(40, 40, 50)

-- 7 вкладок с SCROLL: KICK, DEFENSE, MOVE, VISUAL, SERVER, FUN
local tabNames = {"KICK", "DEFENSE", "MOVE", "VISUAL", "SERVER", "FUN"}
local tabBtns = {}
local tabConts = {}

for i, name in ipairs(tabNames) do
    local btn = Instance.new("TextButton")
    btn.Parent = tabBar
    btn.Size = UDim2.new(1/#tabNames, -2, 1, 0)
    btn.Position = UDim2.new((i-1)/#tabNames, 1, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
    btn.BorderSizePixel = 0
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(150, 150, 170)
    btn.TextSize = 8
    btn.Font = Enum.Font.GothamBold
    tabBtns[i] = btn
end
tabBtns[1].BackgroundColor3 = Color3.fromRGB(55, 55, 70)
tabBtns[1].TextColor3 = Color3.fromRGB(255, 255, 255)

for i = 1, #tabNames do
    local scr = Instance.new("ScrollingFrame")
    scr.Parent = main
    scr.Size = UDim2.new(1, -16, 1, -82)
    scr.Position = UDim2.new(0, 8, 0, 74)
    scr.BackgroundColor3 = Color3.fromRGB(12, 12, 15)
    scr.BorderSizePixel = 1
    scr.BorderColor3 = Color3.fromRGB(40, 40, 50)
    scr.CanvasSize = UDim2.new(0, 0, 0, 1000)
    scr.ScrollBarThickness = 4
    scr.Visible = (i == 1)
    scr.Name = "Tab" .. i
    tabConts[i] = scr
end

for i, btn in ipairs(tabBtns) do
    btn.MouseButton1Click:Connect(function()
        for j, c in ipairs(tabConts) do c.Visible = (j == i) end
        for j, b in ipairs(tabBtns) do
            b.BackgroundColor3 = (j == i) and Color3.fromRGB(55, 55, 70) or Color3.fromRGB(28, 28, 36)
            b.TextColor3 = (j == i) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 150, 170)
        end
    end)
end

-- Helper functions
local function createSection(parent, text, xPos, yPos, width)
    local sec = Instance.new("TextLabel")
    sec.Parent = parent
    sec.Size = UDim2.new(width, 0, 0, 20)
    sec.Position = UDim2.new(xPos, 0, yPos, 0)
    sec.BackgroundTransparency = 1
    sec.Text = text
    sec.TextColor3 = Color3.fromRGB(255, 200, 100)
    sec.TextSize = 11
    sec.Font = Enum.Font.GothamBold
    sec.TextXAlignment = Enum.TextXAlignment.Left
    return sec
end
_G.C16.createSection = createSection

local function createToggle(parent, name, xPos, yPos, width, cb)
    local tg = Instance.new("TextButton")
    tg.Parent = parent
    tg.Size = UDim2.new(width, 0, 0, 22)
    tg.Position = UDim2.new(xPos, 0, yPos, 0)
    tg.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
    tg.BorderSizePixel = 1
    tg.BorderColor3 = Color3.fromRGB(40, 40, 50)
    tg.Text = "  " .. name
    tg.TextColor3 = Color3.fromRGB(180, 180, 200)
    tg.TextSize = 9
    tg.Font = Enum.Font.Gotham
    tg.TextXAlignment = Enum.TextXAlignment.Left
    local indicator = Instance.new("Frame")
    indicator.Parent = tg
    indicator.Size = UDim2.new(0, 8, 0, 8)
    indicator.Position = UDim2.new(1, -14, 0.5, -4)
    indicator.BackgroundColor3 = Color3.fromRGB(60, 60, 75)
    indicator.BorderSizePixel = 0
    local cornerT = Instance.new("UICorner")
    cornerT.CornerRadius = UDim.new(0, 3)
    cornerT.Parent = tg
    local act = false
    tg.MouseButton1Click:Connect(function()
        act = not act
        if act then
            tg.TextColor3 = Color3.fromRGB(255, 255, 255)
            tg.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
            indicator.BackgroundColor3 = Color3.fromRGB(80, 200, 120)
        else
            tg.TextColor3 = Color3.fromRGB(180, 180, 200)
            tg.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
            indicator.BackgroundColor3 = Color3.fromRGB(60, 60, 75)
        end
        local ok, err = pcall(cb, act)
        if not ok then warn("[CHOMPER] Toggle error:", err) end
    end)
    return tg
end
_G.C16.createToggle = createToggle

local function createButton(parent, name, xPos, yPos, width, cb)
    local btn = Instance.new("TextButton")
    btn.Parent = parent
    btn.Size = UDim2.new(width, 0, 0, 22)
    btn.Position = UDim2.new(xPos, 0, yPos, 0)
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    btn.BorderSizePixel = 1
    btn.BorderColor3 = Color3.fromRGB(55, 55, 70)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 9
    btn.Font = Enum.Font.GothamBold
    local cornerB = Instance.new("UICorner")
    cornerB.CornerRadius = UDim.new(0, 4)
    cornerB.Parent = btn
    btn.MouseButton1Click:Connect(function()
        local ok, err = pcall(cb)
        if not ok then warn("[CHOMPER] Button error:", err) end
    end)
    return btn
end
_G.C16.createButton = createButton
_G.C16.tabConts = tabConts

-- ====================================================================
-- ВКЛАДКА 1: KICK
-- ====================================================================
local kickCont = tabConts[1]

-- Player list
local playerList = Instance.new("ScrollingFrame")
playerList.Parent = kickCont
playerList.Size = UDim2.new(0.30, -5, 0, 420)
playerList.Position = UDim2.new(0, 5, 0, 30)
playerList.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
playerList.BorderSizePixel = 1
playerList.BorderColor3 = Color3.fromRGB(40, 40, 50)
playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
playerList.ScrollBarThickness = 3
local cornerList = Instance.new("UICorner")
cornerList.CornerRadius = UDim.new(0, 4)
cornerList.Parent = playerList
local lay = Instance.new("UIListLayout")
lay.Parent = playerList
lay.SortOrder = Enum.SortOrder.Name
lay.Padding = UDim.new(0, 2)

local selectedTarget = nil
_G.C16.getTarget = function() return selectedTarget end

local function updatePlayerList()
    for _, c in pairs(playerList:GetChildren()) do
        if c:IsA("TextButton") then c:Destroy() end
    end
    local count = 0
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            count = count + 1
            local btn = Instance.new("TextButton")
            btn.Parent = playerList
            btn.Size = UDim2.new(1, -6, 0, 20)
            btn.Text = "  " .. plr.DisplayName
            btn.TextColor3 = Color3.fromRGB(200, 200, 210)
            btn.TextSize = 9
            btn.Font = Enum.Font.Gotham
            btn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
            btn.BorderSizePixel = 0
            btn.TextXAlignment = Enum.TextXAlignment.Left
            local cornerB = Instance.new("UICorner")
            cornerB.CornerRadius = UDim.new(0, 3)
            cornerB.Parent = btn
            btn.MouseButton1Click:Connect(function()
                selectedTarget = plr.Name
                for _, c in pairs(playerList:GetChildren()) do
                    if c:IsA("TextButton") then c.BackgroundColor3 = Color3.fromRGB(25, 25, 32) end
                end
                btn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
                notify("CHOMPER", "Цель: " .. plr.Name, 2)
            end)
        end
    end
    playerList.CanvasSize = UDim2.new(0, 0, 0, count * 22 + 10)
end

updatePlayerList()
Players.PlayerAdded:Connect(function() task.wait(0.5) updatePlayerList() end)
Players.PlayerRemoving:Connect(function() task.wait(0.5) updatePlayerList() end)

createSection(kickCont, "Target", 0.02, 0.02, 0.30)
createButton(kickCont, "REFRESH", 0.02, 0.94, 0.30, updatePlayerList)

-- ====================================================================
-- OWNERSHIP KICK (Vortex: Kick spam Ragdoll & Lag)
-- ====================================================================
createSection(kickCont, "OwnerShip", 0.34, 0.02, 0.64)

local shipActive = false
local shipTask = nil
createToggle(kickCont, "OWNERSHIP KICK (Ragdoll & Lag)", 0.34, 0.08, 0.64, function(v)
    shipActive = v
    if not v then
        if shipTask then pcall(task.cancel, shipTask) shipTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) shipActive = false return end

    shipTask = task.spawn(function()
        local tname = selectedTarget
        local bPos, bGyro, lagLoop = nil, nil, true

        -- Отдельный поток для ЛАГА цели (ExtendGrabLine spam)
        task.spawn(function()
            while lagLoop and shipActive do
                pcall(function()
                    if ExtendGrabLine then
                        ExtendGrabLine:FireServer(string.rep("X", 20000))
                    end
                end)
                task.wait(0.05)
            end
        end)

        while shipActive do
            local t = Players:FindFirstChild(tname)
            if not t or not t.Character then
                if bPos then bPos:Destroy() end
                if bGyro then bGyro:Destroy() end
                break
            end
            local myChar = LocalPlayer.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            local tRoot = t.Character:FindFirstChild("HumanoidRootPart")
            local tHum = t.Character:FindFirstChild("Humanoid")
            if myRoot and tRoot and tHum and tHum.Health > 0 then
                -- Спам владения + grabline + рагдолл + анти-сит
                pcall(function()
                    if EndGrabEarly then EndGrabEarly:FireServer(tRoot) end
                    for i = 1, 6 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                    CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    RagdollRemote:FireServer(tRoot, 0.01)
                end)

                -- BodyPosition чтобы дёргать цель (Ragdoll)
                local lockPos = (myRoot.CFrame * CFrame.new(0, 20, 0)).Position
                if not bPos or not bPos.Parent then
                    if bPos then bPos:Destroy() end
                    bPos = Instance.new("BodyPosition")
                    bPos.MaxForce = Vector3.new(9e9, 9e9, 9e9)
                    bPos.P = 500000
                    bPos.D = 2000
                    bPos.Position = lockPos
                    bPos.Parent = tRoot
                else
                    bPos.Position = lockPos
                end

                if not bGyro or not bGyro.Parent then
                    if bGyro then bGyro:Destroy() end
                    bGyro = Instance.new("BodyGyro")
                    bGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
                    bGyro.P = 500000
                    bGyro.D = 2000
                    bGyro.CFrame = CFrame.new(lockPos)
                    bGyro.Parent = tRoot
                else
                    bGyro.CFrame = CFrame.new(lockPos)
                end

                tHum.PlatformStand = true
                tHum.Sit = false
            end
            RunService.Heartbeat:Wait()
        end

        lagLoop = false
        if bPos then bPos:Destroy() end
        if bGyro then bGyro:Destroy() end
    end)
end)

-- ====================================================================
-- GRAB SECTION (Vortex)
-- ====================================================================
createSection(kickCont, "Grab", 0.34, 0.18, 0.64)

-- Pen Kill (просто "перо в жопу" — kill через прямоту)
local penKillActive = false
local penKillTask = nil
createToggle(kickCont, "PEN KILL", 0.34, 0.24, 0.30, function(v)
    penKillActive = v
    if not v then
        if penKillTask then pcall(task.cancel, penKillTask) penKillTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) penKillActive = false return end
    penKillTask = task.spawn(function()
        while penKillActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local tRoot = t.Character:FindFirstChild("HumanoidRootPart")
            local tHum = t.Character:FindFirstChild("Humanoid")
            if tRoot and tHum and tHum.Health > 0 then
                pcall(function()
                    if EndGrabEarly then EndGrabEarly:FireServer(tRoot) end
                    for i = 1, 10 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                    CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    tHum.Health = 0
                    tHum.BreakJointsOnDeath = false
                    tHum:ChangeState(Enum.HumanoidStateType.Dead)
                end)
            end
            RunService.Heartbeat:Wait()
        end
    end)
end)

-- Loop Kill (Grab)
local loopKillGrabActive = false
local loopKillGrabTask = nil
createToggle(kickCont, "LOOP KILL (GRAB)", 0.66, 0.24, 0.32, function(v)
    loopKillGrabActive = v
    if not v then
        if loopKillGrabTask then pcall(task.cancel, loopKillGrabTask) loopKillGrabTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) loopKillGrabActive = false return end
    loopKillGrabTask = task.spawn(function()
        while loopKillGrabActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local tRoot = t.Character:FindFirstChild("HumanoidRootPart")
            local tHum = t.Character:FindFirstChild("Humanoid")
            local myRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if tRoot and tHum and myRoot and tHum.Health > 0 then
                pcall(function()
                    if EndGrabEarly then EndGrabEarly:FireServer(tRoot) end
                    for i = 1, 8 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                    CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    tRoot.CFrame = myRoot.CFrame * CFrame.new(0, 17, 0)
                    tRoot.AssemblyLinearVelocity = Vector3.zero
                    tHum.PlatformStand = true
                end)
            end
            task.wait(0.05)
        end
    end)
end)

-- Destroy Gucci [BLOB]
local destroyGucciBlobActive = false
local destroyGucciBlobTask = nil
createToggle(kickCont, "DESTROY GUCCI [BLOB]", 0.34, 0.32, 0.30, function(v)
    destroyGucciBlobActive = v
    if not v then
        if destroyGucciBlobTask then pcall(task.cancel, destroyGucciBlobTask) destroyGucciBlobTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) destroyGucciBlobActive = false return end
    destroyGucciBlobTask = task.spawn(function()
        while destroyGucciBlobActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local folder = Workspace:FindFirstChild(t.Name .. "SpawnedInToys")
            if folder then
                for _, obj in ipairs(folder:GetChildren()) do
                    if obj.Name == "CreatureBlobman" then
                        pcall(function() DestroyToy:FireServer(obj) end)
                    end
                end
            end
            task.wait(0.2)
        end
    end)
end)

-- Destroy Gucci [Jump/Sit]
local destroyGucciJSActive = false
local destroyGucciJSTask = nil
createToggle(kickCont, "DESTROY GUCCI [JUMP/SIT]", 0.66, 0.32, 0.32, function(v)
    destroyGucciJSActive = v
    if not v then
        if destroyGucciJSTask then pcall(task.cancel, destroyGucciJSTask) destroyGucciJSTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) destroyGucciJSActive = false return end
    destroyGucciJSTask = task.spawn(function()
        while destroyGucciJSActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local tHum = t.Character:FindFirstChild("Humanoid")
            if tHum and tHum.SeatPart then
                pcall(function()
                    tHum.Sit = false
                    tHum.Jump = true
                end)
            end
            task.wait(0.05)
        end
    end)
end)

-- Loop Kill (Kick)
local loopKickActive = false
local loopKickTask = nil
createToggle(kickCont, "LOOP KICK", 0.34, 0.40, 0.64, function(v)
    loopKickActive = v
    if not v then
        if loopKickTask then pcall(task.cancel, loopKickTask) loopKickTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) loopKickActive = false return end
    loopKickTask = task.spawn(function()
        while loopKickActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local tRoot = t.Character:FindFirstChild("HumanoidRootPart")
            local tHum = t.Character:FindFirstChild("Humanoid")
            if tRoot and tHum and tHum.Health > 0 then
                pcall(function()
                    if EndGrabEarly then EndGrabEarly:FireServer(tRoot) end
                    for i = 1, 10 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                    CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    tRoot.CFrame = CFrame.new(0, 1e9, 0)
                    tHum.PlatformStand = true
                end)
            end
            task.wait(0.05)
        end
    end)
end)

-- Fling (секция)
createSection(kickCont, "Fling", 0.34, 0.50, 0.64)

local flingTargetActive = false
local flingTargetTask = nil
createToggle(kickCont, "FLING TARGET", 0.34, 0.56, 0.64, function(v)
    flingTargetActive = v
    if not v then
        if flingTargetTask then pcall(task.cancel, flingTargetTask) flingTargetTask = nil end
        return
    end
    if not selectedTarget then notify("CHOMPER", "Выбери цель", 3) flingTargetActive = false return end
    flingTargetTask = task.spawn(function()
        while flingTargetActive do
            local t = Players:FindFirstChild(selectedTarget)
            if not t or not t.Character then break end
            local tRoot = t.Character:FindFirstChild("HumanoidRootPart")
            if tRoot then
                pcall(function()
                    for i = 1, 6 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                    local bv = Instance.new("BodyVelocity")
                    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bv.Velocity = Vector3.new(math.random(-5000, 5000), 5000, math.random(-5000, 5000))
                    bv.Parent = tRoot
                    Debris:AddItem(bv, 0.3)
                end)
            end
            task.wait(0.1)
        end
    end)
end)

-- KICK ALL
createButton(kickCont, "KICK ALL", 0.34, 0.66, 0.30, function()
    for _, pl in ipairs(Players:GetPlayers()) do
        if pl ~= LocalPlayer and pl.Character then
            local tRoot = pl.Character:FindFirstChild("HumanoidRootPart")
            local tHum = pl.Character:FindFirstChild("Humanoid")
            if tRoot and tHum and tHum.Health > 0 then
                task.spawn(function()
                    pcall(function()
                        if EndGrabEarly then EndGrabEarly:FireServer(tRoot) end
                        for i = 1, 10 do SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                        CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        tRoot.CFrame = CFrame.new(0, 1e9, 0)
                        tHum.PlatformStand = true
                    end)
                end)
            end
        end
    end
    notify("CHOMPER", "KICK ALL запущен", 2)
end)

-- STOP ALL
createButton(kickCont, "STOP ALL", 0.66, 0.66, 0.32, function()
    shipActive = false; penKillActive = false; loopKillGrabActive = false
    destroyGucciBlobActive = false; destroyGucciJSActive = false
    loopKickActive = false; flingTargetActive = false
    if shipTask then pcall(task.cancel, shipTask) end
    if penKillTask then pcall(task.cancel, penKillTask) end
    if loopKillGrabTask then pcall(task.cancel, loopKillGrabTask) end
    if destroyGucciBlobTask then pcall(task.cancel, destroyGucciBlobTask) end
    if destroyGucciJSTask then pcall(task.cancel, destroyGucciJSTask) end
    if loopKickTask then pcall(task.cancel, loopKickTask) end
    if flingTargetTask then pcall(task.cancel, flingTargetTask) end
    notify("CHOMPER", "Всё остановлено", 2)
end)

-- Bar / Rejoin
createButton(kickCont, "REJOIN", 0.34, 0.76, 0.30, function()
    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
end)
createButton(kickCont, "BREAK BARRIER", 0.66, 0.76, 0.32, function()
    hamburgerTP()
    task.wait(0.3)
    local n = disableBarriers()
    notify("CHOMPER", "Барьеров: " .. n, 3)
end)

-- ====================================================================
-- ВКЛАДКА 2: DEFENSE
-- ====================================================================
local defCont = tabConts[2]

-- ============ Main Defense ============
createSection(defCont, "Main Defense", 0.02, 0.02, 0.48)

-- Anti-Grab
local aGrabActive = false
local aGrabTask = nil
createToggle(defCont, "ANTI-GRAB", 0.02, 0.08, 0.48, function(v)
    aGrabActive = v
    if not v then
        if aGrabTask then pcall(task.cancel, aGrabTask) aGrabTask = nil end
        return
    end
    aGrabTask = task.spawn(function()
        while aGrabActive do
            pcall(function()
                local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                if isHeld and isHeld.Value then
                    local c = LocalPlayer.Character
                    local h = c and c:FindFirstChild("Humanoid")
                    local r = c and c:FindFirstChild("HumanoidRootPart")
                    if h and r then
                        Struggle:FireServer(LocalPlayer)
                        RagdollRemote:FireServer(r, 0.00000000001)
                        if h.Sit then h.Sit = false end
                    end
                end
            end)
            task.wait(0.05)
        end
    end)
end)

-- Anti-Grab (Ragdoll)
local aGrabRagActive = false
local aGrabRagTask = nil
createToggle(defCont, "ANTI-GRAB (RAGDOLL)", 0.5, 0.08, 0.48, function(v)
    aGrabRagActive = v
    if not v then
        if aGrabRagTask then pcall(task.cancel, aGrabRagTask) aGrabRagTask = nil end
        return
    end
    aGrabRagTask = task.spawn(function()
        while aGrabRagActive do
            pcall(function()
                local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                if isHeld and isHeld.Value then
                    local c = LocalPlayer.Character
                    local h = c and c:FindFirstChild("Humanoid")
                    local r = c and c:FindFirstChild("HumanoidRootPart")
                    if h and r then
                        Struggle:FireServer(LocalPlayer)
                        RagdollRemote:FireServer(r, 3)
                        if h.Sit then h.Sit = false end
                        task.wait(0.4)
                        if h then h.Sit = false end
                    end
                end
            end)
            task.wait(0.05)
        end
    end)
end)

-- Anti-Gucci (Blobman)
local aGucciBlobActive = false
local aGucciBlobTask = nil
createToggle(defCont, "ANTI-GUCCI (BLOBMAN)", 0.02, 0.18, 0.48, function(v)
    aGucciBlobActive = v
    if not v then
        if aGucciBlobTask then pcall(task.cancel, aGucciBlobTask) aGucciBlobTask = nil end
        return
    end
    aGucciBlobTask = task.spawn(function()
        while aGucciBlobActive do
            pcall(function()
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                if h and h.SeatPart and h.SeatPart.Parent and h.SeatPart.Parent.Name == "CreatureBlobman" then
                    -- Мы на блобмане который принадлежит другому — сбегаем
                    local blob = h.SeatPart.Parent
                    if blob then
                        h.Sit = false
                        h.Jump = true
                    end
                end
            end)
            task.wait(0.1)
        end
    end)
end)

-- Anti-Gucci (Ragdoll)
local aGucciRagActive = false
local aGucciRagTask = nil
createToggle(defCont, "ANTI-GUCCI (RAGDOLL)", 0.5, 0.18, 0.48, function(v)
    aGucciRagActive = v
    if not v then
        if aGucciRagTask then pcall(task.cancel, aGucciRagTask) aGucciRagTask = nil end
        return
    end
    aGucciRagTask = task.spawn(function()
        while aGucciRagActive do
            pcall(function()
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if h and r and h.SeatPart then
                    local parent = h.SeatPart.Parent
                    if parent and parent.Name == "CreatureBlobman" then
                        RagdollRemote:FireServer(r, 0.001)
                    end
                end
            end)
            task.wait(0.05)
        end
    end)
end)

-- Anti-Gucci (Tractor)
local aGucciTractActive = false
local aGucciTractTask = nil
createToggle(defCont, "ANTI-GUCCI (TRACTOR)", 0.02, 0.28, 0.48, function(v)
    aGucciTractActive = v
    if not v then
        if aGucciTractTask then pcall(task.cancel, aGucciTractTask) aGucciTractTask = nil end
        return
    end
    aGucciTractTask = task.spawn(function()
        while aGucciTractActive do
            pcall(function()
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                if h and h.SeatPart and h.SeatPart.Parent then
                    local name = h.SeatPart.Parent.Name
                    if name:lower():find("tractor") then
                        h.Sit = false
                        h.Jump = true
                    end
                end
            end)
            task.wait(0.1)
        end
    end)
end)

-- Anti-Grab (TP)
local aGrabTPActive = false
local aGrabTPTask = nil
createToggle(defCont, "ANTI-GRAB (TP)", 0.5, 0.28, 0.48, function(v)
    aGrabTPActive = v
    if not v then
        if aGrabTPTask then pcall(task.cancel, aGrabTPTask) aGrabTPTask = nil end
        return
    end
    aGrabTPTask = task.spawn(function()
        while aGrabTPActive do
            pcall(function()
                local c = LocalPlayer.Character
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if r then
                    -- сохраняем текущую позицию, слегка дёргаем чтобы сбить grab-владельца
                    local pos = r.Position
                    r.CFrame = CFrame.new(pos)
                end
            end)
            task.wait(0.03)
        end
    end)
end)

-- Auto Attacker
local autoAttackActive = false
local autoAttackConn = nil
local attackMode = "Death"
createToggle(defCont, "AUTO ATTACKER", 0.02, 0.38, 0.30, function(v)
    autoAttackActive = v
    if autoAttackConn then autoAttackConn:Disconnect() autoAttackConn = nil end
    if not v then return end
    autoAttackConn = RunService.Heartbeat:Connect(function()
        if not autoAttackActive then return end
        local c = LocalPlayer.Character
        if not c or not c:FindFirstChild("Head") then return end
        local owner = c.Head:FindFirstChild("PartOwner")
        if not owner or not owner:IsA("StringValue") then return end
        local attacker = Players:FindFirstChild(owner.Value)
        if not attacker or not attacker.Character then return end
        local aRoot = attacker.Character:FindFirstChild("HumanoidRootPart")
        local aHum = attacker.Character:FindFirstChild("Humanoid")
        if not aRoot or not aHum then return end
        pcall(function()
            if attackMode == "Death" then
                for i = 1, 5 do SetNetworkOwner:FireServer(aRoot, aRoot.CFrame) end
                CreateGrabLine:FireServer(aRoot, Vector3.zero, aRoot.Position, false)
                aHum.Health = 0
                aHum.BreakJointsOnDeath = false
            elseif attackMode == "Fling" then
                for i = 1, 5 do SetNetworkOwner:FireServer(aRoot, aRoot.CFrame) end
                local away = (aRoot.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
                local bv = Instance.new("BodyVelocity")
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Vector3.new(away.X, 0.5, away.Z) * 90000
                bv.Parent = aRoot
                Debris:AddItem(bv, 0.01)
            elseif attackMode == "Heaven" then
                aRoot.CFrame = CFrame.new(0, 500, 0)
            end
        end)
    end)
end)

-- Counter Mode dropdown
local cmBtn = Instance.new("TextButton")
cmBtn.Parent = defCont
cmBtn.Size = UDim2.new(0.30, 0, 0, 22)
cmBtn.Position = UDim2.new(0.34, 0, 0.38, 0)
cmBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
cmBtn.BorderSizePixel = 1
cmBtn.BorderColor3 = Color3.fromRGB(40, 40, 50)
cmBtn.Text = "Mode: Death"
cmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
cmBtn.TextSize = 10
cmBtn.Font = Enum.Font.Gotham
local cornerCM = Instance.new("UICorner")
cornerCM.CornerRadius = UDim.new(0, 4)
cornerCM.Parent = cmBtn

local cmList = Instance.new("ScrollingFrame")
cmList.Parent = defCont
cmList.Size = UDim2.new(0.30, 0, 0, 90)
cmList.Position = UDim2.new(0.34, 0, 0.45, 0)
cmList.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
cmList.BorderSizePixel = 1
cmList.BorderColor3 = Color3.fromRGB(40, 40, 50)
cmList.CanvasSize = UDim2.new(0, 0, 0, 0)
cmList.ScrollBarThickness = 3
cmList.Visible = false
local cornerCML = Instance.new("UICorner")
cornerCML.CornerRadius = UDim.new(0, 4)
cornerCML.Parent = cmList
local cmLay = Instance.new("UIListLayout")
cmLay.Parent = cmList
cmLay.Padding = UDim.new(0, 2)

for _, m in ipairs({"Death", "Fling", "Heaven"}) do
    local b = Instance.new("TextButton")
    b.Parent = cmList
    b.Size = UDim2.new(1, -6, 0, 20)
    b.Text = "  " .. m
    b.TextColor3 = Color3.fromRGB(200, 200, 210)
    b.TextSize = 9
    b.Font = Enum.Font.Gotham
    b.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    b.BorderSizePixel = 0
    b.TextXAlignment = Enum.TextXAlignment.Left
    local cornerB = Instance.new("UICorner")
    cornerB.CornerRadius = UDim.new(0, 3)
    cornerB.Parent = b
    b.MouseButton1Click:Connect(function()
        attackMode = m
        cmBtn.Text = "Mode: " .. m
        cmList.Visible = false
    end)
end
cmList.CanvasSize = UDim2.new(0, 0, 0, 3 * 22 + 10)
cmBtn.MouseButton1Click:Connect(function() cmList.Visible = not cmList.Visible end)

-- ============ Extra Defense ============
createSection(defCont, "Extra Defense", 0.68, 0.02, 0.30)

local aVoidActive = false
local aVoidConn = nil
createToggle(defCont, "ANTI-VOID", 0.68, 0.08, 0.30, function(v)
    aVoidActive = v
    if aVoidConn then aVoidConn:Disconnect() aVoidConn = nil end
    if not v then return end
    aVoidConn = RunService.Heartbeat:Connect(function()
        if not aVoidActive then return end
        local c = LocalPlayer.Character
        if c and c.PrimaryPart and c.PrimaryPart.Position.Y < -50 then
            local p = c.PrimaryPart.Position
            c:SetPrimaryPartCFrame(CFrame.new(p.X, 100, p.Z))
            c.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
        end
    end)
end)

local aBurnActive = false
local aBurnConn = nil
createToggle(defCont, "ANTI-BURN", 0.68, 0.16, 0.30, function(v)
    aBurnActive = v
    if aBurnConn then aBurnConn:Disconnect() aBurnConn = nil end
    if not v then return end
    aBurnConn = RunService.Heartbeat:Connect(function()
        if not aBurnActive then return end
        pcall(function()
            local c = LocalPlayer.Character
            local r = c and c:FindFirstChild("HumanoidRootPart")
            if r then
                for _, obj in ipairs(r:GetChildren()) do
                    if obj:IsA("Fire") or (obj:IsA("ParticleEmitter") and obj.Name:lower():find("fire")) then
                        obj:Destroy()
                    end
                end
            end
        end)
    end)
end)

local aExpVisActive = false
local aExpVisConn = nil
createToggle(defCont, "ANTI-EXPLOSION (VISUAL)", 0.68, 0.24, 0.30, function(v)
    aExpVisActive = v
    if aExpVisConn then aExpVisConn:Disconnect() aExpVisConn = nil end
    if not v then return end
    aExpVisConn = Workspace.DescendantAdded:Connect(function(obj)
        if not aExpVisActive then return end
        if obj:IsA("Explosion") then obj:Destroy() end
    end)
end)

local aExpActive = false
local aExpConn = nil
createToggle(defCont, "ANTI-EXPLOSION", 0.68, 0.32, 0.30, function(v)
    aExpActive = v
    if aExpConn then aExpConn:Disconnect() aExpConn = nil end
    if not v then return end
    aExpConn = Workspace.ChildAdded:Connect(function(m)
        if not aExpActive then return end
        if m.Name == "Part" then
            pcall(function()
                local c = LocalPlayer.Character
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if r and (m.Position - r.Position).Magnitude <= 20 then
                    r.Anchored = true
                    task.wait(0.1)
                    if aExpActive then r.Anchored = false end
                end
            end)
        end
    end)
end)

local aStickyActive = false
local aStickyConn = nil
createToggle(defCont, "ANTI-STICKY", 0.68, 0.40, 0.30, function(v)
    aStickyActive = v
    if aStickyConn then aStickyConn:Disconnect() aStickyConn = nil end
    if not v then return end
    aStickyConn = Workspace.DescendantAdded:Connect(function(obj)
        if not aStickyActive then return end
        if obj.Name == "StickyWeld" or obj.Name == "StickyPart" then
            task.defer(function()
                if obj and obj.Parent then obj:Destroy() end
            end)
        end
    end)
end)

-- ============ Anti-Kick ============
createSection(defCont, "Anti Kick", 0.02, 0.50, 0.96)

local breakPcldActive = false
local breakPcldConn = nil
createToggle(defCont, "BREAK PCLD", 0.02, 0.56, 0.30, function(v)
    breakPcldActive = v
    if breakPcldConn then breakPcldConn:Disconnect() breakPcldConn = nil end
    if not v then return end
    breakPcldConn = Workspace.ChildAdded:Connect(function(obj)
        if not breakPcldActive then return end
        if obj.Name == "PlayerCharacterLocationDetector" then
            task.defer(function() pcall(function() obj:Destroy() end) end)
        end
    end)
    -- сразу почистим существующие
    for _, o in ipairs(Workspace:GetChildren()) do
        if o.Name == "PlayerCharacterLocationDetector" then pcall(function() o:Destroy() end) end
    end
end)

-- Anti-Kick [ITEM]
local antikItemActive = false
local antikItemTask = nil
local antikItemType = "SpookyCandle1"
createToggle(defCont, "ANTI-KICK [ITEM]", 0.34, 0.56, 0.30, function(v)
    antikItemActive = v
    if not v then
        if antikItemTask then pcall(task.cancel, antikItemTask) antikItemTask = nil end
        return
    end
    antikItemTask = task.spawn(function()
        while antikItemActive do
            pcall(function()
                local c = LocalPlayer.Character
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if r then
                    -- спамим спавн итемов (они "забивают" кик-предметы)
                    local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    if folder then
                        for _, obj in ipairs(folder:GetChildren()) do
                            if obj.Name == antikItemType then
                                local part = obj:FindFirstChild("SoundPart") or obj:FindFirstChild("Hitbox")
                                if part then
                                    SetNetworkOwner:FireServer(part, part.CFrame)
                                end
                            end
                        end
                    end
                end
            end)
            task.wait(0.1)
        end
    end)
end)

-- Anti-Kick (Basic)
local antikActive = false
local antikConn = nil
createToggle(defCont, "ANTI-KICK", 0.66, 0.56, 0.32, function(v)
    antikActive = v
    if antikConn then antikConn:Disconnect() antikConn = nil end
    if not v then return end
    antikConn = RunService.Heartbeat:Connect(function()
        if not antikActive then return end
        pcall(function()
            local c = LocalPlayer.Character
            local h = c and c:FindFirstChild("Humanoid")
            local r = c and c:FindFirstChild("HumanoidRootPart")
            if h and r then
                -- отменяем любой ragdoll
                RagdollRemote:FireServer(r, 0.0001)
                if h.Sit then h.Sit = false end
            end
        end)
    end)
end)

-- Anti-Kick (Pencil)
local antikPencilActive = false
local antikPencilTask = nil
createToggle(defCont, "ANTI-KICK (PENCIL)", 0.02, 0.64, 0.30, function(v)
    antikPencilActive = v
    if not v then
        if antikPencilTask then pcall(task.cancel, antikPencilTask) antikPencilTask = nil end
        return
    end
    antikPencilTask = task.spawn(function()
        local lastSpawn = 0
        while antikPencilActive do
            pcall(function()
                local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                if folder then
                    local hasPencil = folder:FindFirstChild("ToolPencil")
                    if not hasPencil and tick() - lastSpawn > 2 then
                        local c = LocalPlayer.Character
                        local r = c and c:FindFirstChild("HumanoidRootPart")
                        if r then
                            SpawnToyRF:InvokeServer("ToolPencil", r.CFrame * CFrame.new(0, 5, 10), Vector3.zero)
                            lastSpawn = tick()
                        end
                    end
                    -- держим pencil "своим"
                    if hasPencil then
                        for _, prt in ipairs(hasPencil:GetDescendants()) do
                            if prt:IsA("BasePart") then
                                SetNetworkOwner:FireServer(prt, prt.CFrame)
                            end
                        end
                    end
                end
            end)
            task.wait(0.3)
        end
    end)
end)

-- Auto-Reset
local autoResetActive = false
local autoResetTask = nil
createToggle(defCont, "AUTO-RESET", 0.34, 0.64, 0.30, function(v)
    autoResetActive = v
    if not v then
        if autoResetTask then pcall(task.cancel, autoResetTask) autoResetTask = nil end
        return
    end
    autoResetTask = task.spawn(function()
        while autoResetActive do
            pcall(function()
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                if h and h.Health > 0 then
                    -- проверяем нет ли isHeld или ragdoll
                    local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                    if isHeld and isHeld.Value then
                        h.Health = 0
                    end
                end
            end)
            task.wait(0.5)
        end
    end)
end)

-- Auto-Leave
local autoLeaveActive = false
local autoLeaveTask = nil
createToggle(defCont, "AUTO-LEAVE", 0.66, 0.64, 0.32, function(v)
    autoLeaveActive = v
    if not v then
        if autoLeaveTask then pcall(task.cancel, autoLeaveTask) autoLeaveTask = nil end
        return
    end
    autoLeaveTask = task.spawn(function()
        while autoLeaveActive do
            pcall(function()
                local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                if isHeld and isHeld.Value then
                    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, tostring(math.random(1, 9999999)), LocalPlayer)
                end
            end)
            task.wait(0.5)
        end
    end)
end)

-- ============ Anti-Kill ============
createSection(defCont, "Anti-Kill", 0.02, 0.74, 0.96)

local aBlobKillActive = false
local aBlobKillTask = nil
createToggle(defCont, "ANTI-BLOB (KILL)", 0.02, 0.80, 0.22, function(v)
    aBlobKillActive = v
    if not v then
        if aBlobKillTask then pcall(task.cancel, aBlobKillTask) aBlobKillTask = nil end
        return
    end
    aBlobKillTask = task.spawn(function()
        while aBlobKillActive do
            pcall(function()
                local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                if folder then
                    for _, obj in ipairs(folder:GetChildren()) do
                        if obj.Name == "CreatureBlobman" then
                            pcall(function() DestroyToy:FireServer(obj) end)
                        end
                    end
                end
            end)
            task.wait(0.3)
        end
    end)
end)

local aBlobAuraActive = false
local aBlobAuraTask = nil
createToggle(defCont, "ANTI-BLOB (AURA)", 0.26, 0.80, 0.22, function(v)
    aBlobAuraActive = v
    if not v then
        if aBlobAuraTask then pcall(task.cancel, aBlobAuraTask) aBlobAuraTask = nil end
        return
    end
    aBlobAuraTask = task.spawn(function()
        while aBlobAuraActive do
            pcall(function()
                local c = LocalPlayer.Character
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if r then
                    local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    if folder then
                        for _, obj in ipairs(folder:GetChildren()) do
                            if obj.Name == "CreatureBlobman" and obj.PrimaryPart then
                                local d = (obj.PrimaryPart.Position - r.Position).Magnitude
                                if d < 20 then
                                    DestroyToy:FireServer(obj)
                                end
                            end
                        end
                    end
                end
            end)
            task.wait(0.2)
        end
    end)
end)

local aKillHouseActive = false
local aKillHouseTask = nil
createToggle(defCont, "ANTI-KILL (HOUSE)", 0.50, 0.80, 0.22, function(v)
    aKillHouseActive = v
    if not v then
        if aKillHouseTask then pcall(task.cancel, aKillHouseTask) aKillHouseTask = nil end
        return
    end
    aKillHouseTask = task.spawn(function()
        while aKillHouseActive do
            pcall(function()
                -- проверяем, не в чужом ли доме мы (там кикают через barrier)
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if h and r and h.Health > 0 then
                    -- легкий вариант: держим HRP на месте + отменяем ragdoll
                    RagdollRemote:FireServer(r, 0.0001)
                end
            end)
            task.wait(0.2)
        end
    end)
end)

local aKillBypassActive = false
local aKillBypassConn = nil
createToggle(defCont, "ANTI-KILL (BYPASS)", 0.74, 0.80, 0.24, function(v)
    aKillBypassActive = v
    if aKillBypassConn then aKillBypassConn:Disconnect() aKillBypassConn = nil end
    if not v then return end
    aKillBypassConn = RunService.Heartbeat:Connect(function()
        if not aKillBypassActive then return end
        pcall(function()
            local c = LocalPlayer.Character
            local h = c and c:FindFirstChild("Humanoid")
            local r = c and c:FindFirstChild("HumanoidRootPart")
            if h and r then
                -- отменяем бесконечный ragdoll (bypass kill через ragdoll freeze)
                if h.PlatformStand then h.PlatformStand = false end
                if h.Sit then h.Sit = false end
            end
        end)
    end)
end)

createToggle(defCont, "ANTI-KILL (GRAB)", 0.02, 0.88, 0.22, function(v)
    -- аналогично Anti-Grab, но с большей частотой
    if not v then return end
    task.spawn(function()
        while true do
            local isHeld = LocalPlayer:FindFirstChild("IsHeld")
            if isHeld and isHeld.Value then
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if h and r then
                    Struggle:FireServer(LocalPlayer)
                    RagdollRemote:FireServer(r, 0.000000001)
                    if h.Sit then h.Sit = false end
                end
            end
            task.wait(0.02)
        end
    end)
end)

local invisActive = false
local invisConn = nil
createToggle(defCont, "INVISIBILITY", 0.26, 0.88, 0.22, function(v)
    invisActive = v
    if invisConn then invisConn:Disconnect() invisConn = nil end
    if not v then
        -- восстанавливаем
        local c = LocalPlayer.Character
        if c then
            for _, p in ipairs(c:GetDescendants()) do
                if p:IsA("BasePart") then
                    p.LocalTransparencyModifier = 0
                end
            end
        end
        return
    end
    invisConn = RunService.Heartbeat:Connect(function()
        if not invisActive then return end
        local c = LocalPlayer.Character
        if c then
            for _, p in ipairs(c:GetDescendants()) do
                if p:IsA("BasePart") then
                    p.LocalTransparencyModifier = 1
                end
            end
        end
    end)
end)

local aRagActive = false
local aRagConn = nil
createToggle(defCont, "ANTI-RAGDOLL", 0.50, 0.88, 0.22, function(v)
    aRagActive = v
    if aRagConn then aRagConn:Disconnect() aRagConn = nil end
    if not v then return end
    aRagConn = RunService.Heartbeat:Connect(function()
        if not aRagActive then return end
        pcall(function()
            local c = LocalPlayer.Character
            local h = c and c:FindFirstChild("Humanoid")
            local r = c and c:FindFirstChild("HumanoidRootPart")
            if h and r then
                local rv = h:FindFirstChild("Ragdolled")
                if rv and rv.Value then
                    RagdollRemote:FireServer(r, 0.0001)
                end
            end
        end)
    end)
end)

local aRagBlobActive = false
local aRagBlobTask = nil
createToggle(defCont, "ANTI-RAGDOLL (ON BLOB)", 0.74, 0.88, 0.24, function(v)
    aRagBlobActive = v
    if not v then
        if aRagBlobTask then pcall(task.cancel, aRagBlobTask) aRagBlobTask = nil end
        return
    end
    aRagBlobTask = task.spawn(function()
        while aRagBlobActive do
            pcall(function()
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("Humanoid")
                local r = c and c:FindFirstChild("HumanoidRootPart")
                if h and r and h.SeatPart and h.SeatPart.Parent and h.SeatPart.Parent.Name == "CreatureBlobman" then
                    RagdollRemote:FireServer(r, 3)
                    task.wait(0.4)
                    if h then h.Sit = false; h.Sit = true end
                end
            end)
            task.wait(0.1)
        end
    end)
end)

-- Anti-Sit / Anti-Banana / Anti-Paint
createToggle(defCont, "ANTI-SIT", 0.02, 0.96, 0.22, function(v)
    if not v then return end
    task.spawn(function()
        while true do
            pcall(function()
                local h = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
                if h and h.Sit then h.Sit = false end
            end)
            task.wait(0.05)
        end
    end)
end)

createToggle(defCont, "ANTI-BANANA", 0.26, 0.96, 0.22, function(v)
    if not v then return end
    Workspace.DescendantAdded:Connect(function(o)
        if o.Name == "FoodBanana" or o.Name:lower():find("banana") then
            task.defer(function() pcall(function() o:Destroy() end) end)
        end
    end)
end)

createToggle(defCont, "ANTI-PAINT", 0.50, 0.96, 0.22, function(v)
    if not v then return end
    Workspace.DescendantAdded:Connect(function(o)
        if o:IsA("BasePart") and o.Name == "PaintPlayerPart" then
            task.defer(function() pcall(function() o:Destroy() end) end)
        end
    end)
    for _, o in ipairs(Workspace:GetDescendants()) do
        if o:IsA("BasePart") and o.Name == "PaintPlayerPart" then
            pcall(function() o:Destroy() end)
        end
    end
end)

print("[CHOMPER v16] PART 1 загружен — KICK + DEFENSE. Запусти PART 2!")
notify("CHOMPER v16", "PART 1/2 загружен. Запусти PART 2.", 5)-- ====================================================================
-- CHOMPER v16 | FTAP | VORTEX-STYLE
-- PART 2/2: MOVE + VISUAL (SHADERS) + SERVER + FUN
-- Запусти ПОСЛЕ part1
-- ====================================================================

local Ref = _G.C16
if not Ref then
    warn("[CHOMPER v16] PART 2: запусти сначала PART 1!")
    return
end

local Players          = Ref.Players
local RunService       = Ref.RunService
local RS               = Ref.RS
local Workspace        = Ref.Workspace
local Debris           = Ref.Debris
local Lighting         = Ref.Lighting
local UIS              = Ref.UIS
local TweenService     = Ref.TweenService
local LocalPlayer      = Ref.LP
local Camera           = Ref.Cam
local SNO              = Ref.SNO
local CGL              = Ref.CGL
local DGL              = Ref.DGL
local EGL              = Ref.EGL
local EGE              = Ref.EGE
local RR               = Ref.RR
local SG               = Ref.SG
local STR              = Ref.STR
local DT               = Ref.DT
local SPE              = Ref.SPE
local notify           = Ref.notify

local createSection    = Ref.createSection
local createToggle     = Ref.createToggle
local createButton     = Ref.createButton
local tabConts         = Ref.tabConts

if not createSection then
    warn("[CHOMPER v16] PART 2: helpers не найдены, нужен PART 1!")
    return
end

-- ====================================================================
-- ВКЛАДКА 3: MOVE
-- ====================================================================
local moveCont = tabConts[3]

createSection(moveCont, "Flight", 0.02, 0.02, 0.48)

local flyActive = false
local flySpeed  = 100
local flyBV, flyConn = nil, nil

createToggle(moveCont, "FLY", 0.02, 0.08, 0.48, function(v)
    flyActive = v
    if not v then
        if flyConn then flyConn:Disconnect() flyConn = nil end
        if flyBV then flyBV:Destroy() flyBV = nil end
        local h = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if h then h.PlatformStand = false end
        return
    end
    task.spawn(function()
        local c = LocalPlayer.Character
        local h = c and c:FindFirstChild("Humanoid")
        local r = c and c:FindFirstChild("HumanoidRootPart")
        if not h or not r then flyActive = false return end
        h.PlatformStand = true
        flyBV = Instance.new("BodyVelocity")
        flyBV.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        flyBV.Parent = r
        flyConn = RunService.RenderStepped:Connect(function()
            if not flyActive then return end
            local cam = Workspace.CurrentCamera
            local dir = Vector3.zero
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then dir = dir + Vector3.new(0, 1, 0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then dir = dir - Vector3.new(0, 1, 0) end
            flyBV.Velocity = (dir.Magnitude > 0) and (dir.Unit * flySpeed) or Vector3.zero
        end)
    end)
end)

local flyLabel = Instance.new("TextLabel")
flyLabel.Parent = moveCont
flyLabel.Size = UDim2.new(0.48, 0, 0, 14)
flyLabel.Position = UDim2.new(0.02, 0, 0.16, 0)
flyLabel.BackgroundTransparency = 1
flyLabel.Text = "Fly Speed: 100"
flyLabel.TextColor3 = Color3.fromRGB(200, 200, 220)
flyLabel.TextSize = 9
flyLabel.Font = Enum.Font.Gotham
flyLabel.TextXAlignment = Enum.TextXAlignment.Left

local flyBar = Instance.new("TextButton")
flyBar.Parent = moveCont
flyBar.Size = UDim2.new(0.48, 0, 0, 10)
flyBar.Position = UDim2.new(0.02, 0, 0.20, 0)
flyBar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
flyBar.BorderSizePixel = 1
flyBar.BorderColor3 = Color3.fromRGB(50, 50, 65)
flyBar.Text = ""
flyBar.AutoButtonColor = false

local flyFill = Instance.new("Frame")
flyFill.Parent = flyBar
flyFill.Size = UDim2.new(0.18, 0, 1, 0)
flyFill.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
flyFill.BorderSizePixel = 0

local flyDrag = false
flyBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        flyDrag = true
    end
end)
flyBar.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        flyDrag = false
    end
end)
UIS.InputChanged:Connect(function(i)
    if flyDrag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local pct = math.clamp((i.Position.X - flyBar.AbsolutePosition.X) / flyBar.AbsoluteSize.X, 0, 1)
        local val = math.floor(10 + pct * 490)
        flySpeed = val
        flyFill.Size = UDim2.new(pct, 0, 1, 0)
        flyLabel.Text = "Fly Speed: " .. val
    end
end)

-- Noclip
local noclipActive = false
createToggle(moveCont, "NOCLIP", 0.5, 0.08, 0.48, function(v)
    noclipActive = v
    if v then
        task.spawn(function()
            while noclipActive do
                local c = LocalPlayer.Character
                if c then
                    for _, p in ipairs(c:GetDescendants()) do
                        if p:IsA("BasePart") then p.CanCollide = false end
                    end
                end
                task.wait(0.1)
            end
        end)
    end
end)

-- Infinite Jump
local infJumpActive = false
createToggle(moveCont, "INFINITE JUMP", 0.02, 0.28, 0.48, function(v) infJumpActive = v end)
UIS.JumpRequest:Connect(function()
    if infJumpActive then
        local h = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if h then h:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

-- Speed
local speedValue = 16
local speedLabel = Instance.new("TextLabel")
speedLabel.Parent = moveCont
speedLabel.Size = UDim2.new(0.48, 0, 0, 14)
speedLabel.Position = UDim2.new(0.5, 0, 0.28, 0)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed: 16"
speedLabel.TextColor3 = Color3.fromRGB(200, 200, 220)
speedLabel.TextSize = 9
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextXAlignment = Enum.TextXAlignment.Left

local speedBar = Instance.new("TextButton")
speedBar.Parent = moveCont
speedBar.Size = UDim2.new(0.48, 0, 0, 10)
speedBar.Position = UDim2.new(0.5, 0, 0.32, 0)
speedBar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
speedBar.BorderSizePixel = 1
speedBar.BorderColor3 = Color3.fromRGB(50, 50, 65)
speedBar.Text = ""
speedBar.AutoButtonColor = false

local speedFill = Instance.new("Frame")
speedFill.Parent = speedBar
speedFill.Size = UDim2.new(0, 0, 1, 0)
speedFill.BackgroundColor3 = Color3.fromRGB(100, 255, 100)
speedFill.BorderSizePixel = 0

local speedDrag = false
speedBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        speedDrag = true
    end
end)
speedBar.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        speedDrag = false
    end
end)
UIS.InputChanged:Connect(function(i)
    if speedDrag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local pct = math.clamp((i.Position.X - speedBar.AbsolutePosition.X) / speedBar.AbsoluteSize.X, 0, 1)
        local val = math.floor(16 + pct * 484)
        speedValue = val
        speedFill.Size = UDim2.new(pct, 0, 1, 0)
        speedLabel.Text = "Speed: " .. val
        local h = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if h then h.WalkSpeed = val end
    end
end)

-- Third Person
createToggle(moveCont, "3RD PERSON", 0.02, 0.40, 0.48, function(v)
    LocalPlayer.CameraMaxZoomDistance = 1e9
    LocalPlayer.CameraMode = v and Enum.CameraMode.Classic or Enum.CameraMode.LockFirstPerson
end)

-- ====================================================================
-- ВКЛАДКА 4: VISUAL (ESP + SHADERS + OCEAN)
-- ====================================================================
local visCont = tabConts[4]

createSection(visCont, "Camera", 0.02, 0.02, 0.48)

local defaultFOV = 70
createToggle(visCont, "CUSTOM FOV (70)", 0.02, 0.08, 0.48, function(v)
    local cam = Workspace.CurrentCamera
    if cam then cam.FieldOfView = v and 70 or defaultFOV end
end)

-- ESP
createSection(visCont, "ESP", 0.5, 0.02, 0.48)

local espEnabled = false
local espColor   = Color3.fromRGB(0, 255, 0)
local espObjects = {}

local function createESP(plr)
    if espObjects[plr] then return end
    local hl = Instance.new("Highlight")
    hl.FillColor = espColor
    hl.OutlineColor = espColor
    hl.FillTransparency = 1
    hl.OutlineTransparency = 1
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Enabled = false
    hl.Parent = Workspace
    local bb = Instance.new("BillboardGui")
    bb.Size = UDim2.new(0, 200, 0, 80)
    bb.StudsOffset = Vector3.new(0, 3, 0)
    bb.AlwaysOnTop = true
    bb.LightInfluence = 0
    bb.MaxDistance = 600
    bb.Enabled = false
    bb.ResetOnSpawn = false
    bb.Parent = game:GetService("CoreGui")
    local nLabel = Instance.new("TextLabel")
    nLabel.Size = UDim2.new(1, 0, 0, 20)
    nLabel.BackgroundTransparency = 1
    nLabel.TextColor3 = espColor
    nLabel.Font = Enum.Font.SourceSansBold
    nLabel.TextSize = 14
    nLabel.TextStrokeTransparency = 0.5
    nLabel.Parent = bb
    local dLabel = Instance.new("TextLabel")
    dLabel.Size = UDim2.new(1, 0, 0, 16)
    dLabel.Position = UDim2.new(0, 0, 0, 20)
    dLabel.BackgroundTransparency = 1
    dLabel.TextColor3 = espColor
    dLabel.Font = Enum.Font.SourceSans
    dLabel.TextSize = 12
    dLabel.Parent = bb
    espObjects[plr] = {hl = hl, bb = bb, name = nLabel, dist = dLabel}
end

local function removeESP(plr)
    local o = espObjects[plr]
    if not o then return end
    if o.hl then o.hl:Destroy() end
    if o.bb then o.bb:Destroy() end
    espObjects[plr] = nil
end

createToggle(visCont, "ENABLE ESP", 0.5, 0.08, 0.48, function(v)
    espEnabled = v
    if not v then
        for _, o in pairs(espObjects) do
            o.hl.Enabled = false
            o.bb.Enabled = false
        end
    end
end)

task.spawn(function()
    while task.wait(0.1) do
        if espEnabled then
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer then
                    if not espObjects[plr] then createESP(plr) end
                    local o = espObjects[plr]
                    local char = plr.Character
                    if char then
                        local root = char:FindFirstChild("HumanoidRootPart")
                        local hum = char:FindFirstChild("Humanoid")
                        local head = char:FindFirstChild("Head")
                        if root and hum and hum.Health > 0 then
                            o.hl.OutlineTransparency = 0
                            o.hl.Adornee = char
                            o.hl.Enabled = true
                            o.bb.Adornee = head or root
                            o.bb.Enabled = true
                            o.name.Text = plr.DisplayName
                            o.dist.Text = string.format("%.0f studs", (root.Position - Workspace.CurrentCamera.CFrame.Position).Magnitude)
                        else
                            o.hl.Enabled = false
                            o.bb.Enabled = false
                        end
                    end
                end
            end
        end
    end
end)
Players.PlayerRemoving:Connect(removeESP)

-- ====================================================================
-- SHADERS
-- ====================================================================
createSection(visCont, "Shaders & Lighting", 0.02, 0.20, 0.96)

local origLighting = {
    Brightness = Lighting.Brightness,
    ClockTime  = Lighting.ClockTime,
    GlobalShadows = Lighting.GlobalShadows,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    Ambient    = Lighting.Ambient,
    FogStart   = Lighting.FogStart,
    FogEnd     = Lighting.FogEnd,
    FogColor   = Lighting.FogColor,
    ExposureCompensation = Lighting.ExposureCompensation,
}
local origSky = nil
local sky = Lighting:FindFirstChildOfClass("Sky")
if sky then
    origSky = {
        SkyboxBk = sky.SkyboxBk, SkyboxDn = sky.SkyboxDn, SkyboxFt = sky.SkyboxFt,
        SkyboxLf = sky.SkyboxLf, SkyboxRt = sky.SkyboxRt, SkyboxUp = sky.SkyboxUp,
    }
end

local shaderPresets = {
    ["Default"] = function()
        for k, v in pairs(origLighting) do Lighting[k] = v end
        if sky and origSky then
            sky.SkyboxBk = origSky.SkyboxBk; sky.SkyboxDn = origSky.SkyboxDn
            sky.SkyboxFt = origSky.SkyboxFt; sky.SkyboxLf = origSky.SkyboxLf
            sky.SkyboxRt = origSky.SkyboxRt; sky.SkyboxUp = origSky.SkyboxUp
        end
    end,
    ["Red Night"] = function()
        Lighting.Brightness = 0.5; Lighting.ClockTime = 0
        Lighting.OutdoorAmbient = Color3.fromRGB(50, 0, 0)
        Lighting.Ambient = Color3.fromRGB(20, 0, 0)
        Lighting.FogStart = 0; Lighting.FogEnd = 500
        Lighting.FogColor = Color3.fromRGB(30, 0, 0)
        Lighting.ExposureCompensation = 0.2
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset/?id=401664839"
        sky.SkyboxDn = "http://www.roblox.com/asset/?id=401664862"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=401664960"
        sky.SkyboxLf = "http://www.roblox.com/asset/?id=401664881"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=401664901"
        sky.SkyboxUp = "http://www.roblox.com/asset/?id=401664936"
    end,
    ["Deep Space"] = function()
        Lighting.Brightness = 1; Lighting.ClockTime = 0
        Lighting.OutdoorAmbient = Color3.fromRGB(10, 10, 30)
        Lighting.Ambient = Color3.fromRGB(5, 5, 15)
        Lighting.FogStart = 0; Lighting.FogEnd = 1000
        Lighting.FogColor = Color3.fromRGB(5, 5, 15)
        Lighting.ExposureCompensation = 0.5
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset/?id=149397692"
        sky.SkyboxDn = "http://www.roblox.com/asset/?id=149397686"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=149397697"
        sky.SkyboxLf = "http://www.roblox.com/asset/?id=149397684"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=149397688"
        sky.SkyboxUp = "http://www.roblox.com/asset/?id=149397702"
    end,
    ["Blue Nebula"] = function()
        Lighting.Brightness = 1; Lighting.ClockTime = 0
        Lighting.OutdoorAmbient = Color3.fromRGB(0, 20, 50)
        Lighting.Ambient = Color3.fromRGB(0, 10, 30)
        Lighting.FogStart = 0; Lighting.FogEnd = 800
        Lighting.FogColor = Color3.fromRGB(0, 10, 30)
        Lighting.ExposureCompensation = 0.3
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset?id=135207744"
        sky.SkyboxDn = "http://www.roblox.com/asset?id=135207662"
        sky.SkyboxFt = "http://www.roblox.com/asset?id=135207770"
        sky.SkyboxLf = "http://www.roblox.com/asset?id=135207615"
        sky.SkyboxRt = "http://www.roblox.com/asset?id=135207695"
        sky.SkyboxUp = "http://www.roblox.com/asset?id=135207794"
    end,
    ["Pink Skies"] = function()
        Lighting.Brightness = 2; Lighting.ClockTime = 14
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 150, 200)
        Lighting.Ambient = Color3.fromRGB(255, 100, 150)
        Lighting.FogStart = 0; Lighting.FogEnd = 600
        Lighting.FogColor = Color3.fromRGB(255, 150, 200)
        Lighting.ExposureCompensation = 0.1
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset/?id=151165214"
        sky.SkyboxDn = "http://www.roblox.com/asset/?id=151165197"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=151165224"
        sky.SkyboxLf = "http://www.roblox.com/asset/?id=151165191"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=151165206"
        sky.SkyboxUp = "http://www.roblox.com/asset/?id=151165227"
    end,
    ["Realistic"] = function()
        Lighting.Brightness = 3; Lighting.ClockTime = 14
        Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        Lighting.Ambient = Color3.fromRGB(70, 70, 70)
        Lighting.FogStart = 0; Lighting.FogEnd = 100000
        Lighting.FogColor = Color3.fromRGB(200, 200, 200)
        Lighting.ExposureCompensation = 0.2
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "rbxassetid://653719502"; sky.SkyboxDn = "rbxassetid://653718790"
        sky.SkyboxFt = "rbxassetid://653719067"; sky.SkyboxLf = "rbxassetid://653719190"
        sky.SkyboxRt = "rbxassetid://653718931"; sky.SkyboxUp = "rbxassetid://653719321"
    end,
    ["Snow"] = function()
        Lighting.Brightness = 2; Lighting.ClockTime = 12
        Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 255)
        Lighting.Ambient = Color3.fromRGB(150, 150, 200)
        Lighting.FogStart = 0; Lighting.FogEnd = 400
        Lighting.FogColor = Color3.fromRGB(200, 200, 255)
        Lighting.ExposureCompensation = 0.3
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset/?id=155657655"
        sky.SkyboxDn = "http://www.roblox.com/asset/?id=155674246"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=155657609"
        sky.SkyboxLf = "http://www.roblox.com/asset/?id=155657671"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=155657619"
        sky.SkyboxUp = "http://www.roblox.com/asset/?id=155674931"
    end,
    ["Stormy"] = function()
        Lighting.Brightness = 1; Lighting.ClockTime = 0
        Lighting.OutdoorAmbient = Color3.fromRGB(50, 50, 70)
        Lighting.Ambient = Color3.fromRGB(30, 30, 50)
        Lighting.FogStart = 0; Lighting.FogEnd = 300
        Lighting.FogColor = Color3.fromRGB(50, 50, 70)
        Lighting.ExposureCompensation = 0.1
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "http://www.roblox.com/asset/?id=18703245834"
        sky.SkyboxDn = "http://www.roblox.com/asset/?id=18703243349"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=18703240532"
        sky.SkyboxLf = "http://www.roblox.com/asset/?id=18703237556"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=18703235430"
        sky.SkyboxUp = "http://www.roblox.com/asset/?id=18703232671"
    end,
    ["Sunset"] = function()
        Lighting.Brightness = 2; Lighting.ClockTime = 17
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 150, 100)
        Lighting.Ambient = Color3.fromRGB(200, 100, 50)
        Lighting.FogStart = 0; Lighting.FogEnd = 800
        Lighting.FogColor = Color3.fromRGB(255, 150, 100)
        Lighting.ExposureCompensation = 0.2
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "rbxassetid://600830446"; sky.SkyboxDn = "rbxassetid://600831635"
        sky.SkyboxFt = "rbxassetid://600832720"; sky.SkyboxLf = "rbxassetid://600886090"
        sky.SkyboxRt = "rbxassetid://600833862"; sky.SkyboxUp = "rbxassetid://600835177"
    end,
    ["Galaxy"] = function()
        Lighting.Brightness = 0.5; Lighting.ClockTime = 0
        Lighting.OutdoorAmbient = Color3.fromRGB(20, 10, 40)
        Lighting.Ambient = Color3.fromRGB(10, 5, 30)
        Lighting.FogStart = 0; Lighting.FogEnd = 1000
        Lighting.FogColor = Color3.fromRGB(20, 10, 40)
        Lighting.ExposureCompensation = 0.5
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "rbxassetid://15983968922"; sky.SkyboxDn = "rbxassetid://15983966825"
        sky.SkyboxFt = "rbxassetid://15983965025"; sky.SkyboxLf = "rbxassetid://15983967420"
        sky.SkyboxRt = "rbxassetid://15983966246"; sky.SkyboxUp = "rbxassetid://15983964246"
    end,
    ["Minecraft"] = function()
        Lighting.Brightness = 2; Lighting.ClockTime = 12
        Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        Lighting.Ambient = Color3.fromRGB(80, 80, 80)
        Lighting.FogStart = 0; Lighting.FogEnd = 200
        Lighting.FogColor = Color3.fromRGB(200, 220, 255)
        Lighting.ExposureCompensation = 0
        if not sky then sky = Instance.new("Sky", Lighting) end
        sky.SkyboxBk = "rbxassetid://8735166756"; sky.SkyboxDn = "http://www.roblox.com/asset/?id=8735166707"
        sky.SkyboxFt = "http://www.roblox.com/asset/?id=8735231668"; sky.SkyboxLf = "http://www.roblox.com/asset/?id=8735166755"
        sky.SkyboxRt = "http://www.roblox.com/asset/?id=8735166751"; sky.SkyboxUp = "http://www.roblox.com/asset/?id=8735166729"
    end,
}

local shaderDropdown = Instance.new("TextButton")
shaderDropdown.Parent = visCont
shaderDropdown.Size = UDim2.new(0.48, 0, 0, 22)
shaderDropdown.Position = UDim2.new(0.02, 0, 0.28, 0)
shaderDropdown.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
shaderDropdown.BorderSizePixel = 1
shaderDropdown.BorderColor3 = Color3.fromRGB(40, 40, 50)
shaderDropdown.Text = "Select Shader"
shaderDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
shaderDropdown.TextSize = 10
shaderDropdown.Font = Enum.Font.Gotham
local csShader = Instance.new("UICorner") csShader.CornerRadius = UDim.new(0, 4) csShader.Parent = shaderDropdown

local shaderList = Instance.new("ScrollingFrame")
shaderList.Parent = visCont
shaderList.Size = UDim2.new(0.48, 0, 0, 150)
shaderList.Position = UDim2.new(0.02, 0, 0.36, 0)
shaderList.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
shaderList.BorderSizePixel = 1
shaderList.BorderColor3 = Color3.fromRGB(40, 40, 50)
shaderList.CanvasSize = UDim2.new(0, 0, 0, 0)
shaderList.ScrollBarThickness = 3
shaderList.Visible = false
local csSL = Instance.new("UICorner") csSL.CornerRadius = UDim.new(0, 4) csSL.Parent = shaderList
local sLay = Instance.new("UIListLayout") sLay.Parent = shaderList sLay.Padding = UDim.new(0, 2)

local function applyShader(name)
    if shaderPresets[name] then
        shaderPresets[name]()
        shaderDropdown.Text = "Shader: " .. name
        notify("CHOMPER", "Shader: " .. name, 2)
    end
end

local shaderKeys = {}
for k in pairs(shaderPresets) do table.insert(shaderKeys, k) end
table.sort(shaderKeys)

for _, name in ipairs(shaderKeys) do
    local b = Instance.new("TextButton")
    b.Parent = shaderList
    b.Size = UDim2.new(1, -6, 0, 20)
    b.Text = "  " .. name
    b.TextColor3 = Color3.fromRGB(200, 200, 210)
    b.TextSize = 9
    b.Font = Enum.Font.Gotham
    b.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    b.BorderSizePixel = 0
    b.TextXAlignment = Enum.TextXAlignment.Left
    local csB = Instance.new("UICorner") csB.CornerRadius = UDim.new(0, 3) csB.Parent = b
    b.MouseButton1Click:Connect(function()
        applyShader(name)
        shaderList.Visible = false
    end)
end
shaderList.CanvasSize = UDim2.new(0, 0, 0, #shaderKeys * 22 + 10)
shaderDropdown.MouseButton1Click:Connect(function() shaderList.Visible = not shaderList.Visible end)

-- ====================================================================
-- OCEAN
-- ====================================================================
createSection(visCont, "Ocean", 0.5, 0.40, 0.48)

local oceanMatDropdown = Instance.new("TextButton")
oceanMatDropdown.Parent = visCont
oceanMatDropdown.Size = UDim2.new(0.48, 0, 0, 22)
oceanMatDropdown.Position = UDim2.new(0.5, 0, 0.48, 0)
oceanMatDropdown.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
oceanMatDropdown.BorderSizePixel = 1
oceanMatDropdown.BorderColor3 = Color3.fromRGB(40, 40, 50)
oceanMatDropdown.Text = "Ocean Material: Water"
oceanMatDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
oceanMatDropdown.TextSize = 10
oceanMatDropdown.Font = Enum.Font.Gotham
local csOM = Instance.new("UICorner") csOM.CornerRadius = UDim.new(0, 4) csOM.Parent = oceanMatDropdown

local oceanMatList = Instance.new("ScrollingFrame")
oceanMatList.Parent = visCont
oceanMatList.Size = UDim2.new(0.48, 0, 0, 100)
oceanMatList.Position = UDim2.new(0.5, 0, 0.56, 0)
oceanMatList.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
oceanMatList.BorderSizePixel = 1
oceanMatList.BorderColor3 = Color3.fromRGB(40, 40, 50)
oceanMatList.CanvasSize = UDim2.new(0, 0, 0, 0)
oceanMatList.ScrollBarThickness = 3
oceanMatList.Visible = false
local csOL = Instance.new("UICorner") csOL.CornerRadius = UDim.new(0, 4) csOL.Parent = oceanMatList
local oLay = Instance.new("UIListLayout") oLay.Parent = oceanMatList oLay.Padding = UDim.new(0, 2)

local function getOceanParts()
    local map = Workspace:FindFirstChild("Map")
    if not map then return {} end
    map = map:FindFirstChild("AlwaysHereTweenedObjects")
    if not map then return {} end
    map = map:FindFirstChild("Ocean")
    if not map then return {} end
    map = map:FindFirstChild("Object")
    if not map then return {} end
    map = map:FindFirstChild("ObjectModel")
    if not map then return {} end
    local parts = {}
    for _, obj in ipairs(map:GetDescendants()) do
        if obj:IsA("BasePart") then table.insert(parts, obj) end
    end
    return parts
end

local function setOceanMat(matName)
    local mat = Enum.Material[matName]
    if not mat then return end
    for _, p in ipairs(getOceanParts()) do p.Material = mat end
    oceanMatDropdown.Text = "Ocean Material: " .. matName
    notify("CHOMPER", "Ocean: " .. matName, 2)
end

local oceanMats = {"Water", "Glass", "Neon", "SmoothPlastic", "Ice", "Sand", "Rock", "Basalt", "Slate", "Mud", "Cobblestone"}
for _, matName in ipairs(oceanMats) do
    local b = Instance.new("TextButton")
    b.Parent = oceanMatList
    b.Size = UDim2.new(1, -6, 0, 20)
    b.Text = "  " .. matName
    b.TextColor3 = Color3.fromRGB(200, 200, 210)
    b.TextSize = 9
    b.Font = Enum.Font.Gotham
    b.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    b.BorderSizePixel = 0
    b.TextXAlignment = Enum.TextXAlignment.Left
    local csB = Instance.new("UICorner") csB.CornerRadius = UDim.new(0, 3) csB.Parent = b
    b.MouseButton1Click:Connect(function()
        setOceanMat(matName)
        oceanMatList.Visible = false
    end)
end
oceanMatList.CanvasSize = UDim2.new(0, 0, 0, #oceanMats * 22 + 10)
oceanMatDropdown.MouseButton1Click:Connect(function() oceanMatList.Visible = not oceanMatList.Visible end)

-- ====================================================================
-- ВКЛАДКА 5: SERVER
-- ====================================================================
local servCont = tabConts[5]

createButton(servCont, "BREAK BARRIER (TP)", 0.02, 0.02, 0.48, function()
    if _G.C16.hamburgerTP then _G.C16.hamburgerTP() end
end)

-- Line Lag
local lineLagActive = false
createToggle(servCont, "LINE LAG", 0.5, 0.02, 0.48, function(v)
    lineLagActive = v
    if v then
        task.spawn(function()
            while lineLagActive do
                for _, pl in ipairs(Players:GetPlayers()) do
                    if pl.Character then
                        local torso = pl.Character:FindFirstChild("Torso") or pl.Character:FindFirstChild("UpperTorso") or pl.Character:FindFirstChild("HumanoidRootPart")
                        if torso then
                            pcall(function() CGL:FireServer(torso, torso.CFrame) end)
                            pcall(function() CGL:FireServer(torso, Vector3.zero, torso.Position, false) end)
                            for i = 1, 5 do pcall(function() SNO:FireServer(torso, torso.CFrame) end) end
                            if EGL then pcall(function() EGL:FireServer(string.rep("X", 10000)) end) end
                            pcall(function() DGL:FireServer(torso) end)
                        end
                    end
                end
                task.wait()
            end
        end)
    end
end)

-- Packet Lag Loop
local packetLagActive = false
createToggle(servCont, "PACKET LAG", 0.02, 0.10, 0.48, function(v)
    packetLagActive = v
    if v then
        task.spawn(function()
            while packetLagActive do
                pcall(function() if EGL then EGL:FireServer(string.rep("X", 500000)) end end)
                task.wait(1)
            end
        end)
    end
end)

-- Packet Lag Once
createButton(servCont, "PACKET LAG (ONCE)", 0.5, 0.10, 0.48, function()
    pcall(function() if EGL then EGL:FireServer(string.rep("X", 500000)) end end)
    notify("CHOMPER", "Packet sent", 2)
end)

-- Rejoin / Hop
createButton(servCont, "REJOIN", 0.02, 0.18, 0.48, function()
    notify("CHOMPER", "Rejoining...", 2)
    task.wait(0.3)
    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
end)

createButton(servCont, "SERVER HOP", 0.5, 0.18, 0.48, function()
    notify("CHOMPER", "Hopping...", 2)
    task.wait(0.3)
    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, tostring(math.random(1, 9999999)), LocalPlayer)
end)

-- ====================================================================
-- ВКЛАДКА 6: FUN
-- ====================================================================
local funCont = tabConts[6]

-- Jerk
local jerkActive = false
local jerkTask = nil
local jerkTrack = nil
createToggle(funCont, "JERK OFF", 0.02, 0.02, 0.48, function(v)
    jerkActive = v
    if not v then
        if jerkTask then pcall(task.cancel, jerkTask) jerkTask = nil end
        if jerkTrack then pcall(function() jerkTrack:Stop() end) jerkTrack = nil end
        return
    end
    jerkTask = task.spawn(function()
        while jerkActive do
            local c = LocalPlayer.Character
            local h = c and c:FindFirstChild("Humanoid")
            if h then
                local anim = h:FindFirstChildOfClass("Animator") or Instance.new("Animator", h)
                local a = Instance.new("Animation")
                a.AnimationId = "rbxassetid://33796059"
                jerkTrack = anim:LoadAnimation(a)
                jerkTrack.Priority = Enum.AnimationPriority.Action4
                jerkTrack.Looped = true
                jerkTrack:Play(0.1, 1, 1e6)
            end
            task.wait(0.5)
        end
    end)
end)

-- Cat Ears
createSection(funCont, "CAT EARS", 0.5, 0.02, 0.48)

local catEarsActive = false
local catModel = nil
local catColor = Color3.fromRGB(255, 170, 190)

local function createCatEars()
    if catModel then catModel:Destroy() end
    local c = LocalPlayer.Character
    local head = c and c:FindFirstChild("Head")
    if not head then return end
    catModel = Instance.new("Model")
    catModel.Name = "Chomper_CatEars"
    catModel.Parent = c
    for side = 1, 2 do
        local s = side == 1 and 1 or -1
        local inner = Instance.new("Part")
        inner.Size = Vector3.new(0.3, 0.5, 0.08)
        inner.Color = catColor
        inner.Material = Enum.Material.SmoothPlastic
        inner.Anchored = false
        inner.CanCollide = false
        inner.Massless = true
        inner.Name = "Inner"
        inner.Parent = catModel
        inner.CFrame = head.CFrame * CFrame.new(s * 0.35, 0.85, -0.05) * CFrame.Angles(0, 0, math.rad(-s * 15))
        local w = Instance.new("Weld")
        w.Part0 = head
        w.Part1 = inner
        w.C0 = CFrame.new(s * 0.35, 0.85, -0.05) * CFrame.Angles(0, 0, math.rad(-s * 15))
        w.Parent = inner
        local outer = Instance.new("Part")
        outer.Size = Vector3.new(0.4, 0.6, 0.05)
        outer.Color = Color3.fromRGB(40, 40, 50)
        outer.Material = Enum.Material.SmoothPlastic
        outer.Anchored = false
        outer.CanCollide = false
        outer.Massless = true
        outer.Name = "Outer"
        outer.Parent = catModel
        outer.CFrame = head.CFrame * CFrame.new(s * 0.35, 0.85, -0.07) * CFrame.Angles(0, 0, math.rad(-s * 15))
        local w2 = Instance.new("Weld")
        w2.Part0 = head
        w2.Part1 = outer
        w2.C0 = CFrame.new(s * 0.35, 0.85, -0.07) * CFrame.Angles(0, 0, math.rad(-s * 15))
        w2.Parent = outer
    end
end

createToggle(funCont, "CAT EARS", 0.5, 0.08, 0.48, function(v)
    catEarsActive = v
    if v then createCatEars()
    else if catModel then catModel:Destroy() catModel = nil end end
end)

local catColors = {Color3.fromRGB(255,170,190), Color3.fromRGB(150,200,255), Color3.fromRGB(255,200,150), Color3.fromRGB(200,150,255)}
local catIdx = 1
local catBtn = Instance.new("TextButton")
catBtn.Parent = funCont
catBtn.Size = UDim2.new(0.48, 0, 0, 22)
catBtn.Position = UDim2.new(0.5, 0, 0.16, 0)
catBtn.BackgroundColor3 = catColor
catBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
catBtn.Text = "CAT EAR COLOR"
catBtn.TextSize = 9
catBtn.Font = Enum.Font.GothamBold
local csCB = Instance.new("UICorner") csCB.CornerRadius = UDim.new(0, 3) csCB.Parent = catBtn
catBtn.MouseButton1Click:Connect(function()
    catIdx = catIdx % #catColors + 1
    catColor = catColors[catIdx]
    catBtn.BackgroundColor3 = catColor
    if catEarsActive then createCatEars() end
end)

-- Emotes
createSection(funCont, "EMOTES", 0.02, 0.20, 0.96)

local emoteTrack = nil
local function playEmote(id)
    if emoteTrack then pcall(function() emoteTrack:Stop() end) end
    local c = LocalPlayer.Character
    local h = c and c:FindFirstChild("Humanoid")
    if not h then return end
    local anim = h:FindFirstChildOfClass("Animator") or Instance.new("Animator", h)
    local a = Instance.new("Animation")
    a.AnimationId = "rbxassetid://" .. id
    emoteTrack = anim:LoadAnimation(a)
    emoteTrack:Play()
end

createButton(funCont, "DANCE",  0.02, 0.26, 0.22, function() playEmote("507771019") end)
createButton(funCont, "WAVE",   0.26, 0.26, 0.22, function() playEmote("507770239") end)
createButton(funCont, "POINT",  0.50, 0.26, 0.22, function() playEmote("507770453") end)
createButton(funCont, "LAUGH",  0.74, 0.26, 0.22, function() playEmote("507770818") end)
createButton(funCont, "STOP EMOTE", 0.02, 0.34, 0.48, function()
    if emoteTrack then pcall(function() emoteTrack:Stop() end) emoteTrack = nil end
end)

-- Fling All
createButton(funCont, "FLING ALL", 0.5, 0.34, 0.48, function()
    for _, pl in ipairs(Players:GetPlayers()) do
        if pl ~= LocalPlayer and pl.Character then
            local r = pl.Character:FindFirstChild("HumanoidRootPart")
            if r then
                local bv = Instance.new("BodyVelocity")
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Vector3.new(math.random(-5000, 5000), 5000, math.random(-5000, 5000))
                bv.Parent = r
                Debris:AddItem(bv, 0.3)
            end
        end
    end
end)

print("[CHOMPER v16] PART 2 загружен — все 6 вкладок готовы!")
notify("CHOMPER v16", "PART 2/2 — готово! 6 вкладок: KICK / DEF / MOVE / VIS / SERVER / FUN", 5)
