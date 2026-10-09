-- ============================================
-- 🚀 NEXUS GUI - Estilo Dashboard + 20 Funções
-- ============================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local StarterGui = game:GetService("StarterGui")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

-- ================= CORES DO TEMA =================
local COLORS = {
    Background = Color3.fromRGB(10, 8, 20),
    Panel = Color3.fromRGB(18, 15, 30),
    PanelLight = Color3.fromRGB(25, 20, 42),
    Card = Color3.fromRGB(30, 24, 48),
    CardHover = Color3.fromRGB(40, 32, 62),
    Accent = Color3.fromRGB(160, 80, 255),
    AccentLight = Color3.fromRGB(190, 120, 255),
    AccentDark = Color3.fromRGB(100, 40, 180),
    Text = Color3.fromRGB(255, 255, 255),
    TextDim = Color3.fromRGB(160, 150, 180),
    Green = Color3.fromRGB(0, 255, 130),
    Red = Color3.fromRGB(255, 60, 90),
    Yellow = Color3.fromRGB(255, 200, 0),
}

-- ================= VARIÁVEIS =================
local State = {
    Noclip = false, Fly = false, SuperSpeed = false, InfiniteJump = false,
    HighJump = false, LowGravity = false, SuperGravity = false, Dash = false,
    AirWalk = false, WallWalk = false, TeleportDash = false, DoubleJump = false,
    TripleJump = false, BunnyHop = false, Slide = false, Sprint = false,
    LongJump = false, InfiniteStamina = false, SwimBoost = false, SpiderMove = false,
    ESP = false, Fullbright = false, Aimbot = false, Godmode = false,
    Fling = false, ClickTP = false,
}

local Config = {
    SpeedValue = 60,
    FlySpeed = 100,
    JumpPower = 120,
    Gravity = 50,
    LongJumpPower = 250,
    DashPower = 150,
    SwimBoostPower = 120,
    SpiderRange = 100,
}

local Original = { Speed = 16, Jump = 50, Gravity = workspace.Gravity }
local FlyBodyVelocity, FlyBodyGyro, FlyConnection
local NoclipConn, GodConn, FlingConn, AimbotConn, GravConn, StaminaConn
local DoubleJumpCount = 0, TripleJumpCount = 0
local ESPObjects = {}
local GodParts = {}
local AirWalkPlatform

-- ================= GUI PRINCIPAL =================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NexusGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Botão flutuante
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(0, 55, 0, 55)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, -27)
ToggleBtn.BackgroundColor3 = COLORS.Panel
ToggleBtn.TextColor3 = COLORS.Accent
ToggleBtn.Text = "🚀"
ToggleBtn.TextSize = 26
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.BorderSizePixel = 0
ToggleBtn.Parent = ScreenGui
Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(1, 0)
local tbStroke = Instance.new("UIStroke", ToggleBtn)
tbStroke.Color = COLORS.Accent
tbStroke.Thickness = 2

-- Frame Principal (Dashboard)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 720, 0, 480)
MainFrame.Position = UDim2.new(0.5, -360, 0.5, -240)
MainFrame.BackgroundColor3 = COLORS.Background
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 16)
local mfStroke = Instance.new("UIStroke", MainFrame)
mfStroke.Color = COLORS.AccentDark
mfStroke.Thickness = 2

-- Gradiente de fundo
local bgGrad = Instance.new("UIGradient")
bgGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(15, 10, 30)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(8, 5, 18)),
})
bgGrad.Rotation = 45
bgGrad.Parent = MainFrame

-- ================= SIDEBAR =================
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 180, 1, 0)
Sidebar.BackgroundColor3 = COLORS.Panel
Sidebar.BackgroundTransparency = 0.3
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame
Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 16)

-- Logo
local LogoFrame = Instance.new("Frame")
LogoFrame.Size = UDim2.new(1, -20, 0, 60)
LogoFrame.Position = UDim2.new(0, 10, 0, 10)
LogoFrame.BackgroundTransparency = 1
LogoFrame.Parent = Sidebar

local Logo = Instance.new("TextLabel")
Logo.Size = UDim2.new(1, 0, 1, 0)
Logo.BackgroundTransparency = 1
Logo.Text = "◆ NEXUS"
Logo.TextColor3 = COLORS.AccentLight
Logo.TextSize = 22
Logo.Font = Enum.Font.GothamBold
Logo.TextXAlignment = Enum.TextXAlignment.Left
Logo.Parent = LogoFrame

-- Itens da sidebar
local SidebarItems = {
    {name = "Dashboard", icon = "🏠"},
    {name = "Movement", icon = "🏃"},
    {name = "Combat", icon = "⚔️"},
    {name = "Visual", icon = "👁️"},
    {name = "Misc", icon = "⚙️"},
    {name = "Settings", icon = "🔧"},
}

local SidebarButtons = {}
local function CreateSidebarBtn(item, index)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, 80 + (index - 1) * 46)
    btn.BackgroundColor3 = COLORS.Panel
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.AutoButtonColor = false
    btn.BorderSizePixel = 0
    btn.Parent = Sidebar
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 8)
    
    local icon = Instance.new("TextLabel")
    icon.Size = UDim2.new(0, 30, 1, 0)
    icon.Position = UDim2.new(0, 10, 0, 0)
    icon.BackgroundTransparency = 1
    icon.Text = item.icon
    icon.TextSize = 18
    icon.Font = Enum.Font.GothamBold
    icon.TextXAlignment = Enum.TextXAlignment.Left
    icon.Parent = btn
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -50, 1, 0)
    label.Position = UDim2.new(0, 45, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = item.name
    label.TextColor3 = COLORS.TextDim
    label.TextSize = 14
    label.Font = Enum.Font.GothamSemibold
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = btn
    
    btn.MouseEnter:Connect(function()
        if not btn:GetAttribute("Active") then
            TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundTransparency = 0.7}):Play()
        end
    end)
    btn.MouseLeave:Connect(function()
        if not btn:GetAttribute("Active") then
            TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundTransparency = 1}):Play()
        end
    end)
    
    return btn, icon, label
end

for i, item in ipairs(SidebarItems) do
    local btn, icon, label = CreateSidebarBtn(item, i)
    SidebarButtons[item.name] = {button = btn, icon = icon, label = label}
end

-- User info (embaixo da sidebar)
local UserFrame = Instance.new("Frame")
UserFrame.Size = UDim2.new(1, -20, 0, 50)
UserFrame.Position = UDim2.new(0, 10, 1, -60)
UserFrame.BackgroundColor3 = COLORS.Card
UserFrame.BorderSizePixel = 0
UserFrame.Parent = Sidebar
Instance.new("UICorner", UserFrame).CornerRadius = UDim.new(0, 10)

local UserAvatar = Instance.new("ImageLabel")
UserAvatar.Size = UDim2.new(0, 36, 0, 36)
UserAvatar.Position = UDim2.new(0, 7, 0, 7)
UserAvatar.BackgroundColor3 = COLORS.AccentDark
UserAvatar.BorderSizePixel = 0
UserAvatar.Image = "rbxthumb://type=AvatarHeadShot&id=" .. LocalPlayer.UserId .. "&w=48&h=48"
UserAvatar.Parent = UserFrame
Instance.new("UICorner", UserAvatar).CornerRadius = UDim.new(1, 0)

local UserName = Instance.new("TextLabel")
UserName.Size = UDim2.new(1, -55, 0, 18)
UserName.Position = UDim2.new(0, 50, 0, 8)
UserName.BackgroundTransparency = 1
UserName.Text = LocalPlayer.DisplayName
UserName.TextColor3 = COLORS.Text
UserName.TextSize = 13
UserName.Font = Enum.Font.GothamBold
UserName.TextXAlignment = Enum.TextXAlignment.Left
UserName.Parent = UserFrame

local UserStatus = Instance.new("TextLabel")
UserStatus.Size = UDim2.new(1, -55, 0, 14)
UserStatus.Position = UDim2.new(0, 50, 0, 26)
UserStatus.BackgroundTransparency = 1
UserStatus.Text = "● Online"
UserStatus.TextColor3 = COLORS.Green
UserStatus.TextSize = 11
UserStatus.Font = Enum.Font.Gotham
UserStatus.TextXAlignment = Enum.TextXAlignment.Left
UserStatus.Parent = UserFrame

-- ================= ÁREA DE CONTEÚDO =================
local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -190, 1, -20)
Content.Position = UDim2.new(0, 190, 0, 10)
Content.BackgroundTransparency = 1
Content.Parent = MainFrame

-- Header da content
local ContentHeader = Instance.new("Frame")
ContentHeader.Size = UDim2.new(1, 0, 0, 50)
ContentHeader.BackgroundColor3 = COLORS.Panel
ContentHeader.BackgroundTransparency = 0.4
ContentHeader.BorderSizePixel = 0
ContentHeader.Parent = Content
Instance.new("UICorner", ContentHeader).CornerRadius = UDim.new(0, 10)

local PageTitle = Instance.new("TextLabel")
PageTitle.Size = UDim2.new(1, -20, 1, 0)
PageTitle.Position = UDim2.new(0, 20, 0, 0)
PageTitle.BackgroundTransparency = 1
PageTitle.Text = "Dashboard"
PageTitle.TextColor3 = COLORS.Text
PageTitle.TextSize = 20
PageTitle.Font = Enum.Font.GothamBold
PageTitle.TextXAlignment = Enum.TextXAlignment.Left
PageTitle.Parent = ContentHeader

-- Botão minimizar
local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 30, 0, 30)
MinBtn.Position = UDim2.new(1, -75, 0, 10)
MinBtn.BackgroundColor3 = COLORS.Yellow
MinBtn.Text = "—"
MinBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
MinBtn.TextSize = 16
MinBtn.Font = Enum.Font.GothamBold
MinBtn.BorderSizePixel = 0
MinBtn.Parent = ContentHeader
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(1, 0)

-- Botão fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -40, 0, 10)
CloseBtn.BackgroundColor3 = COLORS.Red
CloseBtn.Text = "X"
CloseBtn.TextColor3 = COLORS.Text
CloseBtn.TextSize = 14
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.BorderSizePixel = 0
CloseBtn.Parent = ContentHeader
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(1, 0)

-- ================= PÁGINAS =================
local Pages = {}
local function CreatePage(name)
    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.new(1, 0, 1, -60)
    page.Position = UDim2.new(0, 0, 0, 60)
    page.BackgroundTransparency = 1
    page.BorderSizePixel = 0
    page.ScrollBarThickness = 4
    page.ScrollBarImageColor3 = COLORS.Accent
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    page.Visible = false
    page.Parent = Content
    
    local grid = Instance.new("UIGridLayout")
    grid.CellSize = UDim2.new(0, 165, 0, 110)
    grid.CellPadding = UDim2.new(0, 10, 0, 10)
    grid.SortOrder = Enum.SortOrder.LayoutOrder
    grid.Parent = page
    
    Pages[name] = page
    return page
end

for _, item in ipairs(SidebarItems) do
    CreatePage(item.name)
end

-- ================= CARD DE TOGGLE =================
local function CreateToggleCard(parent, name, desc, icon, config_key, callback)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(0, 165, 0, 110)
    card.BackgroundColor3 = COLORS.Card
    card.BorderSizePixel = 0
    card.Parent = parent
    
    local corner = Instance.new("UICorner", card)
    corner.CornerRadius = UDim.new(0, 12)
    
    local stroke = Instance.new("UIStroke", card)
    stroke.Color = COLORS.AccentDark
    stroke.Thickness = 1.5
    
    -- Ícone
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Size = UDim2.new(0, 40, 0, 40)
    iconLabel.Position = UDim2.new(0, 12, 0, 12)
    iconLabel.BackgroundColor3 = COLORS.PanelLight
    iconLabel.Text = icon
    iconLabel.TextSize = 20
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.BorderSizePixel = 0
    iconLabel.Parent = card
    Instance.new("UICorner", iconLabel).CornerRadius = UDim.new(0, 10)
    
    -- Nome
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, -65, 0, 20)
    nameLabel.Position = UDim2.new(0, 58, 0, 15)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = name
    nameLabel.TextColor3 = COLORS.Text
    nameLabel.TextSize = 13
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.TextXAlignment = Enum.TextXAlignment.Left
    nameLabel.Parent = card
    
    -- Descrição
    local descLabel = Instance.new("TextLabel")
    descLabel.Size = UDim2.new(1, -65, 0, 16)
    descLabel.Position = UDim2.new(0, 58, 0, 33)
    descLabel.BackgroundTransparency = 1
    descLabel.Text = desc
    descLabel.TextColor3 = COLORS.TextDim
    descLabel.TextSize = 10
    descLabel.Font = Enum.Font.Gotham
    descLabel.TextXAlignment = Enum.TextXAlignment.Left
    descLabel.Parent = card
    
    -- Botão toggle
    local toggleBg = Instance.new("Frame")
    toggleBg.Size = UDim2.new(1, -24, 0, 32)
    toggleBg.Position = UDim2.new(0, 12, 1, -44)
    toggleBg.BackgroundColor3 = COLORS.PanelLight
    toggleBg.BorderSizePixel = 0
    toggleBg.Parent = card
    Instance.new("UICorner", toggleBg).CornerRadius = UDim.new(0, 8)
    
    local toggleTxt = Instance.new("TextLabel")
    toggleTxt.Size = UDim2.new(1, 0, 1, 0)
    toggleTxt.BackgroundTransparency = 1
    toggleTxt.Text = "DESATIVADO"
    toggleTxt.TextColor3 = COLORS.TextDim
    toggleTxt.TextSize = 12
    toggleTxt.Font = Enum.Font.GothamBold
    toggleTxt.Parent = toggleBg
    
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(1, 0, 1, 0)
    toggleBtn.BackgroundTransparency = 1
    toggleBtn.Text = ""
    toggleBtn.Parent = toggleBg
    
    local isActive = false
    
    local function UpdateVisual()
        if isActive then
            TweenService:Create(toggleBg, TweenInfo.new(0.2), {BackgroundColor3 = COLORS.Accent}):Play()
            TweenService:Create(stroke, TweenInfo.new(0.2), {Color = COLORS.AccentLight}):Play()
            toggleTxt.Text = "ATIVADO"
            toggleTxt.TextColor3 = COLORS.Text
        else
            TweenService:Create(toggleBg, TweenInfo.new(0.2), {BackgroundColor3 = COLORS.PanelLight}):Play()
            TweenService:Create(stroke, TweenInfo.new(0.2), {Color = COLORS.AccentDark}):Play()
            toggleTxt.Text = "DESATIVADO"
            toggleTxt.TextColor3 = COLORS.TextDim
        end
    end
    
    toggleBtn.MouseButton1Click:Connect(function()
        isActive = not isActive
        if config_key then State[config_key] = isActive end
        UpdateVisual()
        if callback then callback(isActive) end
    end)
    
    card.MouseEnter:Connect(function()
        TweenService:Create(card, TweenInfo.new(0.15), {BackgroundColor3 = COLORS.CardHover}):Play()
    end)
    card.MouseLeave:Connect(function()
        TweenService:Create(card, TweenInfo.new(0.15), {BackgroundColor3 = COLORS.Card}):Play()
    end)
    
    return card, toggleBtn
end

-- ================= CALLBACKS =================

-- SPEED
local function ApplySpeed()
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = State.SuperSpeed and Config.SpeedValue or Original.Speed end
end

-- JUMP (Infinite, High, Double, Triple, Bunny)
local function ApplyJump()
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        if State.HighJump then
            hum.UseJumpPower = true
            hum.JumpPower = Config.JumpPower
        else
            hum.JumpPower = Original.Jump
        end
    end
end

-- GRAVIDADE
local function ApplyGravity()
    if State.LowGravity then
        workspace.Gravity = 20
    elseif State.SuperGravity then
        workspace.Gravity = 250
    else
        workspace.Gravity = Original.Gravity
    end
end

-- NOCLIP
local function ToggleNoclip(state)
    if state and not NoclipConn then
        NoclipConn = RunService.Stepped:Connect(function()
            if not State.Noclip then return end
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
        if not State.Fly or not FlyBodyVelocity then return end
        local cam = workspace.CurrentCamera
        local dir = Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir += cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir -= cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir -= cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir += cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir += Vector3.new(0,1,0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then dir -= Vector3.new(0,1,0) end
        FlyBodyVelocity.Velocity = dir.Magnitude > 0 and dir.Unit * Config.FlySpeed or Vector3.zero
        FlyBodyGyro.CFrame = cam.CFrame
    end)
end

-- DASH
local function DoDash()
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local dir = Camera.CFrame.LookVector
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bv.Velocity = dir * Config.DashPower
    bv.Parent = hrp
    game:GetService("Debris"):AddItem(bv, 0.2)
end

-- TELEPORT DASH
local function DoTeleportDash()
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    hrp.CFrame = hrp.CFrame + (Camera.CFrame.LookVector * 30)
end

-- LONG JUMP
local function DoLongJump()
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    hrp.Velocity = Vector3.new(hrp.Velocity.X, 80, hrp.Velocity.Z) + Camera.CFrame.LookVector * 100
end

-- SPRINT
local function ApplySprint()
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = State.Sprint and 32 or (State.SuperSpeed and Config.SpeedValue or Original.Speed) end
end

-- SLIDE
local function DoSlide()
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if not hrp or not hum then return end
    hum.HipHeight = -1
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(math.huge, 0, math.huge)
    bv.Velocity = Camera.CFrame.LookVector * 80
    bv.Parent = hrp
    game:GetService("Debris"):AddItem(bv, 0.6)
    task.wait(0.6)
    hum.HipHeight = 2
end

-- AIR WALK
local function ToggleAirWalk(state)
    if state then
        local char = LocalPlayer.Character
        if not char then return end
        AirWalkPlatform = Instance.new("Part")
        AirWalkPlatform.Size = Vector3.new(8, 1, 8)
        AirWalkPlatform.Anchored = true
        AirWalkPlatform.CanCollide = true
        AirWalkPlatform.Transparency = 0.7
        AirWalkPlatform.Color = COLORS.Accent
        AirWalkPlatform.Parent = workspace
    else
        if AirWalkPlatform then AirWalkPlatform:Destroy() AirWalkPlatform = nil end
    end
end

RunService.Heartbeat:Connect(function()
    if State.AirWalk and AirWalkPlatform then
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            AirWalkPlatform.CFrame = CFrame.new(hrp.Position.X, hrp.Position.Y - 3, hrp.Position.Z)
        end
    end
end)

-- WALL WALK (simplificado - aplica velocidade na direção da parede)
local function ToggleWallWalk(state)
    if state then
        RunService.Heartbeat:Connect(function()
            if not State.WallWalk then return end
            local char = LocalPlayer.Character
            if not char then return end
            local hrp = char:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            local ray = Ray.new(hrp.Position, hrp.CFrame.LookVector * 5)
            local part = workspace:FindPartOnRay(ray, char)
            if part then
                hrp.Velocity = Vector3.new(hrp.Velocity.X, 30, hrp.Velocity.Z)
            end
        end)
    end
end

-- SPIDER MOVEMENT (teia)
local function ToggleSpiderMove(state)
    if state then
        UserInputService.InputBegan:Connect(function(input, gp)
            if gp then return end
            if not State.SpiderMove then return end
            if input.KeyCode == Enum.KeyCode.Q then
                local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                local ray = Ray.new(hrp.Position, Camera.CFrame.LookVector * Config.SpiderRange)
                local hit, pos = workspace:FindPartOnRay(ray, LocalPlayer.Character)
                if hit then
                    local bv = Instance.new("BodyVelocity")
                    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bv.Velocity = (pos - hrp.Position).Unit * 120
                    bv.Parent = hrp
                    game:GetService("Debris"):AddItem(bv, 0.4)
                end
            end
        end)
    end
end

-- DASH TELEPORT com F
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.F and State.Dash then DoDash() end
    if input.KeyCode == Enum.KeyCode.G and State.TeleportDash then DoTeleportDash() end
    if input.KeyCode == Enum.KeyCode.H and State.LongJump then DoLongJump() end
    if input.KeyCode == Enum.KeyCode.C and State.Slide then DoSlide() end
end)

-- BUNNY HOP
UserInputService.JumpRequest:Connect(function()
    if State.BunnyHop then
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp.Velocity = Vector3.new(hrp.Velocity.X * 1.1, hrp.Velocity.Y, hrp.Velocity.Z * 1.1)
        end
    end
    if State.InfiniteJump and LocalPlayer.Character then
        local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

-- DOUBLE/TRIPLE JUMP
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.Space then
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if not hum then return end
        if hum:GetState() == Enum.HumanoidStateType.Freefall or hum:GetState() == Enum.HumanoidStateType.Jumping then
            if State.TripleJump and TripleJumpCount < 2 then
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
                TripleJumpCount += 1
            elseif State.DoubleJump and DoubleJumpCount < 1 then
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
                DoubleJumpCount += 1
            end
        end
    end
end)

LocalPlayer.CharacterAdded:Connect(function()
    DoubleJumpCount = 0
    TripleJumpCount = 0
end)

-- INFINITE STAMINA
local function ToggleStamina(state)
    if state then
        if not StaminaConn then
            StaminaConn = RunService.Heartbeat:Connect(function()
                if not State.InfiniteStamina then return end
                local char = LocalPlayer.Character
                if char then
                    for _, obj in pairs(char:GetDescendants()) do
                        if obj:IsA("NumberValue") and obj.Name:lower():find("stamina") then
                            obj.Value = obj.Value
                        end
                    end
                end
            end)
        end
    end
end

-- SWIM BOOST
local function ToggleSwim(state)
    if state then
        RunService.Heartbeat:Connect(function()
            if not State.SwimBoost then return end
            local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if hum and hum:GetState() == Enum.HumanoidStateType.Swimming then
                hum.WalkSpeed = Config.SwimBoostPower
            elseif hum and not State.SuperSpeed then
                hum.WalkSpeed = Original.Speed
            end
        end)
    end
end

-- ESP
local function CreateESP(plr)
    if plr == LocalPlayer or ESPObjects[plr] then return end
    local hl = Instance.new("Highlight")
    hl.FillColor = COLORS.Accent
    hl.OutlineColor = COLORS.AccentLight
    hl.FillTransparency = 0.5
    hl.Adornee = plr.Character
    hl.Parent = plr.Character or ScreenGui
    ESPObjects[plr] = hl
end

local function UpdateESP()
    if not State.ESP then
        for _, h in pairs(ESPObjects) do h:Destroy() end
        ESPObjects = {}
        return
    end
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            if not ESPObjects[plr] or not ESPObjects[plr].Parent then CreateESP(plr) end
            ESPObjects[plr].Adornee = plr.Character
        end
    end
end

-- FULLBRIGHT
local FBBackup = {}
local function ToggleFB(state)
    if state then
        FBBackup.Ambient = Lighting.Ambient
        FBBackup.Brightness = Lighting.Brightness
        FBBackup.ClockTime = Lighting.ClockTime
        Lighting.Ambient = Color3.fromRGB(178, 178, 178)
        Lighting.Brightness = 3
        Lighting.ClockTime = 14
    else
        Lighting.Ambient = FBBackup.Ambient or Lighting.Ambient
        Lighting.Brightness = FBBackup.Brightness or Lighting.Brightness
        Lighting.ClockTime = FBBackup.ClockTime or Lighting.ClockTime
    end
end

-- GODMODE
local function ToggleGod(state)
    if state then
        local char = LocalPlayer.Character
        if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum.MaxHealth = math.huge hum.Health = math.huge end
        for _, p in pairs(char:GetDescendants()) do
            if p:IsA("BasePart") then
                table.insert(GodParts, {part = p, old = p.CanCollide})
                p.CanCollide = false
            end
        end
        if not GodConn then
            GodConn = RunService.Heartbeat:Connect(function()
                if not State.Godmode then return end
                local c = LocalPlayer.Character
                if c then
                    local h = c:FindFirstChildOfClass("Humanoid")
                    if h then h.MaxHealth = math.huge h.Health = math.huge end
                end
            end)
        end
    else
        if GodConn then GodConn:Disconnect() GodConn = nil end
        for _, d in pairs(GodParts) do
            if d.part and d.part.Parent then d.part.CanCollide = d.old end
        end
        GodParts = {}
    end
end

-- FLING
local function ToggleFling(state)
    if state and not FlingConn then
        FlingConn = RunService.Heartbeat:Connect(function()
            if not State.Fling then return end
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    local myHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and myHrp and (hrp.Position - myHrp.Position).Magnitude < 5 then
                        hrp.Velocity = Vector3.new(math.random(-500,500), 500, math.random(-500,500))
                    end
                end
            end
        end)
    end
end

-- CLICK TP
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1 and State.ClickTP and Mouse.Target then
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then hrp.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0)) end
    end
end)

-- AIMBOT
local function StartAimbot()
    if AimbotConn then return end
    AimbotConn = RunService.RenderStepped:Connect(function()
        if not State.Aimbot then return end
        if UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) or
           UserInputService:IsKeyDown(Enum.KeyCode.E) then
            local closest, minD = nil, 200
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                    if hrp and hum and hum.Health > 0 then
                        local sp, on = Camera:WorldToViewportPoint(hrp.Position)
                        if on then
                            local d = (Vector2.new(sp.X, sp.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                            if d < minD then minD = d closest = hrp end
                        end
                    end
                end
            end
            if closest then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, closest.Position)
            end
        end
    end)
end

-- ================= POPULAR PÁGINAS =================

-- MOVEMENT (todas as funções que você pediu!)
local MovementPage = Pages["Movement"]
CreateToggleCard(MovementPage, "Noclip", "Atravessar paredes", "👻", "Noclip", ToggleNoclip)
CreateToggleCard(MovementPage, "Fly", "Voar pelo mapa", "🕊️", "Fly", function(v)
    if v then StartFly() else StopFly() end
end)
CreateToggleCard(MovementPage, "Super Speed", "Correr muito rápido", "⚡", "SuperSpeed", ApplySpeed)
CreateToggleCard(MovementPage, "Infinite Jump", "Pular infinitamente", "🦘", "InfiniteJump")
CreateToggleCard(MovementPage, "High Jump", "Pular muito alto", "🦅", "HighJump", ApplyJump)
CreateToggleCard(MovementPage, "Low Gravity", "Gravidade baixa", "🌙", "LowGravity", function(v)
    if v then State.SuperGravity = false end
    ApplyGravity()
end)
CreateToggleCard(MovementPage, "Super Gravity", "Gravidade alta", "🌍", "SuperGravity", function(v)
    if v then State.LowGravity = false end
    ApplyGravity()
end)
CreateToggleCard(MovementPage, "Dash (F)", "Avançar rapidamente", "💨", "Dash")
CreateToggleCard(MovementPage, "Air Walk", "Andar no ar", "☁️", "AirWalk", ToggleAirWalk)
CreateToggleCard(MovementPage, "Wall Walk", "Andar nas paredes", "🧗", "WallWalk", ToggleWallWalk)
CreateToggleCard(MovementPage, "Teleport Dash (G)", "Avançar teleportando", "✨", "TeleportDash")
CreateToggleCard(MovementPage, "Double Jump", "Pulo duplo", "2️⃣", "DoubleJump")
CreateToggleCard(MovementPage, "Triple Jump", "Pulo triplo", "3️⃣", "TripleJump")
CreateToggleCard(MovementPage, "Bunny Hop", "Saltos rápidos", "🐰", "BunnyHop")
CreateToggleCard(MovementPage, "Slide (C)", "Deslizar no chão", "🛹", "Slide")
CreateToggleCard(MovementPage, "Sprint", "Corrida extra", "🏃", "Sprint", ApplySprint)
CreateToggleCard(MovementPage, "Long Jump (H)", "Salto longo", "🚀", "LongJump")
CreateToggleCard(MovementPage, "Infinite Stamina", "Energia infinita", "🔋", "InfiniteStamina", ToggleStamina)
CreateToggleCard(MovementPage, "Swim Boost", "Nadar mais rápido", "🏊", "SwimBoost", ToggleSwim)
CreateToggleCard(MovementPage, "Spider Move (Q)", "Teia estilo Homem-Aranha", "🕷️", "SpiderMove", ToggleSpiderMove)

-- COMBAT
local CombatPage = Pages["Combat"]
CreateToggleCard(CombatPage, "Aimbot", "Segure E/Dir.", "🎯", "Aimbot", function(v) if v then StartAimbot() end end)
CreateToggleCard(CombatPage, "Fling", "Arremessar jogadores", "💥", "Fling", ToggleFling)
CreateToggleCard(CombatPage, "Godmode", "Vida infinita", "🛡️", "Godmode", ToggleGod)
CreateToggleCard(CombatPage, "Click TP", "Teleporte ao clicar", "🖱️", "ClickTP")

-- VISUAL
local VisualPage = Pages["Visual"]
CreateToggleCard(VisualPage, "ESP", "Contorno nos players", "👁️", "ESP", function() UpdateESP() end)
CreateToggleCard(VisualPage, "Fullbright", "Iluminar o mapa", "💡", "Fullbright", ToggleFB)

-- MISC
local MiscPage = Pages["Misc"]
local ResetCard = CreateToggleCard(MiscPage, "Reset", "Resetar personagem", "💀", nil, function()
    if LocalPlayer.Character then LocalPlayer.Character:BreakJoints() end
end)

-- SETTINGS
local SettingsPage = Pages["Settings"]
CreateToggleCard(SettingsPage, "Speed: " .. Config.SpeedValue, "Aumentar/diminuir", "⚙️", nil, nil)

-- ================= NAVEGAÇÃO =================
local currentPage = "Dashboard"

local function ShowPage(name)
    currentPage = name
    for pageName, page in pairs(Pages) do
        page.Visible = (pageName == name)
    end
    PageTitle.Text = name
    for name, data in pairs(SidebarButtons) do
        if name == currentPage then
            data.button:SetAttribute("Active", true)
            data.button.BackgroundTransparency = 0.4
            data.button.BackgroundColor3 = COLORS.AccentDark
            data.label.TextColor3 = COLORS.Text
            data.icon.TextColor3 = COLORS.AccentLight
        else
            data.button:SetAttribute("Active", false)
            data.button.BackgroundTransparency = 1
            data.label.TextColor3 = COLORS.TextDim
            data.icon.TextColor3 = COLORS.TextDim
        end
    end
end

for name, data in pairs(SidebarButtons) do
    data.button.MouseButton1Click:Connect(function()
        ShowPage(name)
    end)
end

ShowPage("Movement")

-- ================= TOGGLE / DRAG =================
local opened = true
local function SetOpen(state)
    opened = state
    MainFrame.Visible = opened
end

ToggleBtn.MouseButton1Click:Connect(function() SetOpen(not opened) end)
MinBtn.MouseButton1Click:Connect(function() SetOpen(false) end)
CloseBtn.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

local dragging, dragStart, startPos
ContentHeader.InputBegan:Connect(function(input)
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

RunService.RenderStepped:Connect(function()
    if State.ESP then UpdateESP() end
end)

-- Notificação
StarterGui:SetCore("SendNotification", {
    Title = "🚀 NEXUS GUI",
    Text = "Carregada com sucesso! 20+ funções disponíveis.",
    Duration = 5
})

print("✅ NEXUS GUI carregada!")
