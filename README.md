-- ============================================
-- 🔥 MEGA GUI - SPEED, FLY, NOCLIP, AIMBOT, GODMODE, FLING
-- ============================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- ================= VARIÁVEIS =================
local SpeedEnabled = false
local FlyEnabled = false
local InfiniteJumpEnabled = false
local NoclipEnabled = false
local ESPEnabled = false
local FullbrightEnabled = false
local AimbotEnabled = false
local GodmodeEnabled = false
local FlingEnabled = false
local ClickTPEnabled = false

local SpeedValue = 50
local FlySpeed = 100
local AimbotFOV = 150
local OriginalSpeed = 16

local FlyBodyVelocity, FlyBodyGyro, FlyConnection
local NoclipConnection, GodConnection, FlingConnection
local AimbotConnection
local ESPObjects = {}
local GodParts = {}

-- ================= GUI =================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MegaGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Botão flutuante
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(0, 55, 0, 55)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, -27)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
ToggleBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
ToggleBtn.Text = "⚡"
ToggleBtn.TextSize = 30
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.BorderSizePixel = 0
ToggleBtn.Parent = ScreenGui
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(1, 0)
local stroke1 = Instance.new("UIStroke", ToggleBtn)
stroke1.Color = Color3.fromRGB(0, 255, 150)
stroke1.Thickness = 2

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 300, 0, 520)
MainFrame.Position = UDim2.new(0, 90, 0.5, -260)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 14)
local stroke2 = Instance.new("UIStroke", MainFrame)
stroke2.Color = Color3.fromRGB(0, 255, 150)
stroke2.Thickness = 2

-- Header
local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 45)
Header.BackgroundColor3 = Color3.fromRGB(0, 255, 150)
Header.BackgroundTransparency = 0.85
Header.BorderSizePixel = 0
Header.Parent = MainFrame
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 14)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -20, 1, 0)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "🔥 MEGA GUI"
Title.TextColor3 = Color3.fromRGB(0, 255, 150)
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Header

-- Botão Minimizar
local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 30, 0, 30)
MinBtn.Position = UDim2.new(1, -75, 0, 8)
MinBtn.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
MinBtn.Text = "—"
MinBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
MinBtn.TextSize = 18
MinBtn.Font = Enum.Font.GothamBold
MinBtn.BorderSizePixel = 0
MinBtn.Parent = Header
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(1, 0)

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -40, 0, 8)
CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 16
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.BorderSizePixel = 0
CloseBtn.Parent = Header
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(1, 0)

-- Scroll
local Scroll = Instance.new("ScrollingFrame")
Scroll.Size = UDim2.new(1, -20, 1, -60)
Scroll.Position = UDim2.new(0, 10, 0, 50)
Scroll.BackgroundTransparency = 1
Scroll.BorderSizePixel = 0
Scroll.ScrollBarThickness = 5
Scroll.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 150)
Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.Parent = MainFrame

local UIList = Instance.new("UIListLayout")
UIList.Padding = UDim.new(0, 8)
UIList.SortOrder = Enum.SortOrder.LayoutOrder
UIList.Parent = Scroll

-- ================= FUNÇÃO BOTÃO =================
local function CreateButton(name, color)
    color = color or Color3.fromRGB(0, 255, 150)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 42)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 38)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = name
    btn.TextSize = 15
    btn.Font = Enum.Font.GothamSemibold
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Parent = Scroll
    
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    local s = Instance.new("UIStroke", btn)
    s.Color = color
    s.Thickness = 1.5
    
    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(50, 50, 60)}):Play()
    end)
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(30, 30, 38)}):Play()
    end)
    
    return btn, s
end

-- ================= FUNÇÕES CORE =================

-- SPEED
local function ApplySpeed()
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = SpeedEnabled and SpeedValue or 16 end
end

-- INFINITE JUMP
UserInputService.JumpRequest:Connect(function()
    if InfiniteJumpEnabled and LocalPlayer.Character then
        local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

-- NOCLIP
local function ToggleNoclip(state)
    if state and not NoclipConnection then
        NoclipConnection = RunService.Stepped:Connect(function()
            if not NoclipEnabled then return end
            if LocalPlayer.Character then
                for _, p in pairs(LocalPlayer.Character:GetDescendants()) do
                    if p:IsA("BasePart") then p.CanCollide = false end
                end
            end
        end)
    end
end

-- FLY
local function StopFly()
    if FlyConnection then FlyConnection:Disconnect() FlyConnection = nil end
    if FlyBodyVelocity then FlyBodyVelocity:Destroy() FlyBodyVelocity = nil end
    if FlyBodyGyro then FlyBodyGyro:Destroy() FlyBodyGyro = nil end
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.PlatformStand = false end
end

local function StartFly()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hrp or not hum then return end
    
    hum.PlatformStand = true
    
    FlyBodyVelocity = Instance.new("BodyVelocity")
    FlyBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    FlyBodyVelocity.Velocity = Vector3.zero
    FlyBodyVelocity.Parent = hrp
    
    FlyBodyGyro = Instance.new("BodyGyro")
    FlyBodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    FlyBodyGyro.P = 1000
    FlyBodyGyro.D = 50
    FlyBodyGyro.Parent = hrp
    
    FlyConnection = RunService.RenderStepped:Connect(function()
        if not FlyEnabled or not FlyBodyVelocity then return end
        local cam = workspace.CurrentCamera
        local dir = Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir += cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir -= cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir -= cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir += cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir += Vector3.new(0,1,0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then dir -= Vector3.new(0,1,0) end
        FlyBodyVelocity.Velocity = dir.Magnitude > 0 and dir.Unit * FlySpeed or Vector3.zero
        FlyBodyGyro.CFrame = cam.CFrame
    end)
end

-- ESP
local function CreateESP(plr)
    if plr == LocalPlayer or ESPObjects[plr] then return end
    local highlight = Instance.new("Highlight")
    highlight.FillColor = Color3.fromRGB(255, 0, 0)
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Adornee = plr.Character
    highlight.Parent = plr.Character or ScreenGui
    ESPObjects[plr] = highlight
end

local function UpdateESP()
    if not ESPEnabled then
        for _, h in pairs(ESPObjects) do h:Destroy() end
        ESPObjects = {}
        return
    end
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            if not ESPObjects[plr] or not ESPObjects[plr].Parent then
                CreateESP(plr)
            end
            ESPObjects[plr].Adornee = plr.Character
        end
    end
end

-- FULLBRIGHT
local FBBackup = {}
local function ToggleFullbright(state)
    if state then
        FBBackup.Ambient = Lighting.Ambient
        FBBackup.Brightness = Lighting.Brightness
        FBBackup.ClockTime = Lighting.ClockTime
        FBBackup.FogEnd = Lighting.FogEnd
        Lighting.Ambient = Color3.fromRGB(178, 178, 178)
        Lighting.Brightness = 3
        Lighting.ClockTime = 14
        Lighting.FogEnd = 1e6
    else
        Lighting.Ambient = FBBackup.Ambient or Lighting.Ambient
        Lighting.Brightness = FBBackup.Brightness or Lighting.Brightness
        Lighting.ClockTime = FBBackup.ClockTime or Lighting.ClockTime
        Lighting.FogEnd = FBBackup.FogEnd or Lighting.FogEnd
    end
end

-- AIMBOT
local function GetClosestPlayer()
    local closest, minDist = nil, AimbotFOV
    local mousePos = UserInputService:GetMouseLocation()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            if hrp and hum and hum.Health > 0 then
                local screenPos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(mousePos.X, mousePos.Y)).Magnitude
                    if dist < minDist then
                        minDist = dist
                        closest = hrp
                    end
                end
            end
        end
    end
    return closest
end

local function StartAimbot()
    if AimbotConnection then return end
    AimbotConnection = RunService.RenderStepped:Connect(function()
        if not AimbotEnabled then return end
        if UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) or 
           UserInputService:IsKeyDown(Enum.KeyCode.E) then
            local target = GetClosestPlayer()
            if target then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
            end
        end
    end)
end

-- GODMODE
local function ToggleGodmode(state)
    if state then
        local char = LocalPlayer.Character
        if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.MaxHealth = math.huge
            hum.Health = math.huge
        end
        for _, p in pairs(char:GetDescendants()) do
            if p:IsA("BasePart") then
                table.insert(GodParts, {part = p, old = p.CanCollide})
                p.CanCollide = false
            end
        end
        if not GodConnection then
            GodConnection = RunService.Heartbeat:Connect(function()
                if not GodmodeEnabled then return end
                local c = LocalPlayer.Character
                if c then
                    local h = c:FindFirstChildOfClass("Humanoid")
                    if h then
                        h.MaxHealth = math.huge
                        h.Health = math.huge
                    end
                end
            end)
        end
    else
        if GodConnection then GodConnection:Disconnect() GodConnection = nil end
        for _, data in pairs(GodParts) do
            if data.part and data.part.Parent then
                data.part.CanCollide = data.old
            end
        end
        GodParts = {}
    end
end

-- FLING
local function ToggleFling(state)
    if state and not FlingConnection then
        FlingConnection = RunService.Heartbeat:Connect(function()
            if not FlingEnabled then return end
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    local myHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and myHrp then
                        local dist = (hrp.Position - myHrp.Position).Magnitude
                        if dist < 5 then
                            hrp.CFrame = hrp.CFrame * CFrame.new(0, 0, 0.5)
                            hrp.Velocity = Vector3.new(math.random(-500,500), 500, math.random(-500,500))
                        end
                    end
                end
            end
        end)
    end
end

-- CLICK TELEPORT
local function ToggleClickTP(state)
    if state then
        Mouse.Button1Down:Connect(function()
            if ClickTPEnabled and Mouse.Target then
                local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0))
                end
            end
        end)
    end
end

-- TELEPORTAR PARA JOGADOR
local function TeleportToPlayer(targetName)
    for _, plr in pairs(Players:GetPlayers()) do
        if plr.Name:lower():sub(1, #targetName) == targetName:lower() and plr ~= LocalPlayer then
            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                local myHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if myHrp then
                    myHrp.CFrame = plr.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
                end
            end
            return true
        end
    end
    return false
end

-- ================= BOTÕES =================

local SpeedBtn, SpeedS = CreateButton("🏃 Speed: OFF")
SpeedBtn.MouseButton1Click:Connect(function()
    SpeedEnabled = not SpeedEnabled
    SpeedBtn.Text = "🏃 Speed: " .. (SpeedEnabled and "ON" or "OFF")
    SpeedS.Color = SpeedEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    ApplySpeed()
end)

local JumpBtn, JumpS = CreateButton("🦘 Infinite Jump: OFF")
JumpBtn.MouseButton1Click:Connect(function()
    InfiniteJumpEnabled = not InfiniteJumpEnabled
    JumpBtn.Text = "🦘 Infinite Jump: " .. (InfiniteJumpEnabled and "ON" or "OFF")
    JumpS.Color = InfiniteJumpEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
end)

local NoclipBtn, NoclipS = CreateButton("👻 Noclip: OFF")
NoclipBtn.MouseButton1Click:Connect(function()
    NoclipEnabled = not NoclipEnabled
    NoclipBtn.Text = "👻 Noclip: " .. (NoclipEnabled and "ON" or "OFF")
    NoclipS.Color = NoclipEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    ToggleNoclip(NoclipEnabled)
end)

local FlyBtn, FlyS = CreateButton("🕊️ Fly: OFF")
FlyBtn.MouseButton1Click:Connect(function()
    FlyEnabled = not FlyEnabled
    FlyBtn.Text = "🕊️ Fly: " .. (FlyEnabled and "ON" or "OFF")
    FlyS.Color = FlyEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    if FlyEnabled then StartFly() else StopFly() end
end)

local ESPBtn, ESPS = CreateButton("👁️ ESP: OFF")
ESPBtn.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    ESPBtn.Text = "👁️ ESP: " .. (ESPEnabled and "ON" or "OFF")
    ESPS.Color = ESPEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    UpdateESP()
end)

local FBBtn, FBS = CreateButton("💡 Fullbright: OFF")
FBBtn.MouseButton1Click:Connect(function()
    FullbrightEnabled = not FullbrightEnabled
    FBBtn.Text = "💡 Fullbright: " .. (FullbrightEnabled and "ON" or "OFF")
    FBS.Color = FullbrightEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    ToggleFullbright(FullbrightEnabled)
end)

local AimbotBtn, AimbotS = CreateButton("🎯 Aimbot: OFF (Segure E ou Botão Dir.)")
AimbotBtn.MouseButton1Click:Connect(function()
    AimbotEnabled = not AimbotEnabled
    AimbotBtn.Text = "🎯 Aimbot: " .. (AimbotEnabled and "ON" or "OFF") .. " (Segure E)"
    AimbotS.Color = AimbotEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    if AimbotEnabled then StartAimbot() end
end)

local GodBtn, GodS = CreateButton("🛡️ Godmode: OFF")
GodBtn.MouseButton1Click:Connect(function()
    GodmodeEnabled = not GodmodeEnabled
    GodBtn.Text = "🛡️ Godmode: " .. (GodmodeEnabled and "ON" or "OFF")
    GodS.Color = GodmodeEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    ToggleGodmode(GodmodeEnabled)
end)

local FlingBtn, FlingS = CreateButton("💥 Fling: OFF (Chegue perto)")
FlingBtn.MouseButton1Click:Connect(function()
    FlingEnabled = not FlingEnabled
    FlingBtn.Text = "💥 Fling: " .. (FlingEnabled and "ON" or "OFF")
    FlingS.Color = FlingEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    ToggleFling(FlingEnabled)
end)

local TPClickBtn, TPClickS = CreateButton("🖱️ Click Teleport: OFF")
TPClickBtn.MouseButton1Click:Connect(function()
    ClickTPEnabled = not ClickTPEnabled
    TPClickBtn.Text = "🖱️ Click Teleport: " .. (ClickTPEnabled and "ON" or "OFF")
    TPClickS.Color = ClickTPEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 255, 150)
    if ClickTPEnabled then ToggleClickTP(true) end
end)

-- TELEPORT PARA JOGADOR (por nome)
local TPFrame = Instance.new("Frame")
TPFrame.Size = UDim2.new(1, -10, 0, 42)
TPFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 38)
TPFrame.BorderSizePixel = 0
TPFrame.Parent = Scroll
Instance.new("UICorner", TPFrame).CornerRadius = UDim.new(0, 8)
local tpStroke = Instance.new("UIStroke", TPFrame)
tpStroke.Color = Color3.fromRGB(0, 255, 150)
tpStroke.Thickness = 1.5

local TPBox = Instance.new("TextBox")
TPBox.Size = UDim2.new(1, -80, 1, -8)
TPBox.Position = UDim2.new(0, 4, 0, 4)
TPBox.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
TPBox.TextColor3 = Color3.fromRGB(255, 255, 255)
TPBox.PlaceholderText = "Nome do jogador..."
TPBox.Text = ""
TPBox.TextSize = 13
TPBox.Font = Enum.Font.Gotham
TPBox.BorderSizePixel = 0
TPBox.Parent = TPFrame
Instance.new("UICorner", TPBox).CornerRadius = UDim.new(0, 6)

local TPGoBtn = Instance.new("TextButton")
TPGoBtn.Size = UDim2.new(0, 70, 1, -8)
TPGoBtn.Position = UDim2.new(1, -74, 0, 4)
TPGoBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 150)
TPGoBtn.Text = "IR ➜"
TPGoBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
TPGoBtn.TextSize = 14
TPGoBtn.Font = Enum.Font.GothamBold
TPGoBtn.BorderSizePixel = 0
TPGoBtn.Parent = TPFrame
Instance.new("UICorner", TPGoBtn).CornerRadius = UDim.new(0, 6)

TPGoBtn.MouseButton1Click:Connect(function()
    if TPBox.Text ~= "" then
        local ok = TeleportToPlayer(TPBox.Text)
        if not ok then
            TPBox.Text = "❌ Não encontrado!"
            task.wait(1.5)
            TPBox.Text = ""
        end
    end
end)

local ResetBtn, ResetS = CreateButton("💀 Resetar Personagem")
ResetBtn.MouseButton1Click:Connect(function()
    if LocalPlayer.Character then LocalPlayer.Character:BreakJoints() end
end)

-- ================= REAPLICAR AO RESPAWNAR =================
LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(1)
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum then
        if SpeedEnabled then hum.WalkSpeed = SpeedValue end
    end
    if FlyEnabled then FlyEnabled = false StopFly() FlyBtn.Text = "🕊️ Fly: OFF" end
    if NoclipEnabled then ToggleNoclip(true) end
    if GodmodeEnabled then ToggleGodmode(true) end
end)

-- ================= TOGGLE ABRIR/FECHAR =================
local opened = true
local function SetOpen(state)
    opened = state
    MainFrame.Visible = opened
    ToggleBtn.Text = opened and "⚡" or "⚡"
end

ToggleBtn.MouseButton1Click:Connect(function() SetOpen(not opened) end)
MinBtn.MouseButton1Click:Connect(function() SetOpen(false) end)
CloseBtn.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-- Arrastar GUI
local dragging, dragStart, startPos
Header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local d = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
    end
end)

-- Loop ESP
RunService.RenderStepped:Connect(function()
    if ESPEnabled then UpdateESP() end
end)

-- Notificação inicial
local Notif = Instance.new("TextLabel")
Notif.Size = UDim2.new(0, 300, 0, 40)
Notif.Position = UDim2.new(0.5, -150, 0, 20)
Notif.BackgroundColor3 = Color3.fromRGB(0, 255, 150)
Notif.TextColor3 = Color3.fromRGB(0, 0, 0)
Notif.Text = "🔥 MEGA GUI carregada!"
Notif.TextSize = 16
Notif.Font = Enum.Font.GothamBold
Notif.BorderSizePixel = 0
Notif.Parent = ScreenGui
Instance.new("UICorner", Notif).CornerRadius = UDim.new(0, 10)
task.wait(3)
TweenService:Create(Notif, TweenInfo.new(0.5), {BackgroundTransparency = 1, TextTransparency = 1}):Play()
task.wait(0.5)
Notif:Destroy()

print("✅ MEGA GUI carregada!")
