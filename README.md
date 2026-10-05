local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- ========== 配置区域 ==========
local CONFIG = {
    HubName = "Tony's Hub",
    GameName = "Steal an Egg",
    Theme = {
        Background = Color3.fromRGB(25, 25, 30),
        Card = Color3.fromRGB(35, 35, 42),
        Accent = Color3.fromRGB(80, 120, 255),
        Text = Color3.fromRGB(240, 240, 240),
        TextDim = Color3.fromRGB(150, 150, 160),
    }
}
-- 修改此处可更换主题颜色

-- ========== 状态 ==========
local State = {
    IsOpen = true,
    CurrentStatus = "Status: Pronto",
    EggList = {},        -- 缓存扫描到的鸡蛋
    ScannedEggs = {},    -- {Name, Value, Position, Model}
}
-- 修改此处可调整初始状态

-- ========== UI 创建（简化示例） ==========
local function CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "TonysHub"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    -- 主窗口
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainWindow"
    MainFrame.Size = UDim2.new(0, 320, 0, 420)
    MainFrame.Position = UDim2.new(0.5, -160, 0.5, -210)
    MainFrame.BackgroundColor3 = CONFIG.Theme.Background
    MainFrame.BorderSizePixel = 0
    MainFrame.Active = true
    MainFrame.Draggable = true  -- 允许拖拽
    MainFrame.Parent = ScreenGui

    -- 圆角
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 12)
    UICorner.Parent = MainFrame

    -- 标题栏
    local TitleBar = Instance.new("Frame")
    TitleBar.Size = UDim2.new(1, 0, 0, 48)
    TitleBar.BackgroundColor3 = CONFIG.Theme.Card
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame

    local TitleCorner = Instance.new("UICorner")
    TitleCorner.CornerRadius = UDim.new(0, 12)
    TitleCorner.Parent = TitleBar

    -- 标题文字
    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Size = UDim2.new(1, -60, 0, 24)
    TitleLabel.Position = UDim2.new(0, 16, 0, 6)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Text = CONFIG.HubName
    TitleLabel.TextColor3 = CONFIG.Theme.Text
    TitleLabel.TextSize = 18
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    TitleLabel.Parent = TitleBar

    -- 副标题
    local SubTitleLabel = Instance.new("TextLabel")
    SubTitleLabel.Size = UDim2.new(1, -60, 0, 16)
    SubTitleLabel.Position = UDim2.new(0, 16, 0, 26)
    SubTitleLabel.BackgroundTransparency = 1
    SubTitleLabel.Text = '"' .. CONFIG.GameName .. '"'
    SubTitleLabel.TextColor3 = CONFIG.Theme.TextDim
    SubTitleLabel.TextSize = 12
    SubTitleLabel.Font = Enum.Font.Gotham
    SubTitleLabel.TextXAlignment = Enum.TextXAlignment.Left
    SubTitleLabel.Parent = TitleBar

    -- 最小化按钮
    local MinimizeBtn = Instance.new("TextButton")
    MinimizeBtn.Size = UDim2.new(0, 32, 0, 32)
    MinimizeBtn.Position = UDim2.new(1, -40, 0, 8)
    MinimizeBtn.BackgroundColor3 = CONFIG.Theme.Accent
    MinimizeBtn.Text = "−"
    MinimizeBtn.TextColor3 = CONFIG.Theme.Text
    MinimizeBtn.TextSize = 20
    MinimizeBtn.Font = Enum.Font.GothamBold
    MinimizeBtn.Parent = TitleBar

    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 6)
    BtnCorner.Parent = MinimizeBtn

    -- 信息区域
    local InfoFrame = Instance.new("Frame")
    InfoFrame.Size = UDim2.new(1, -24, 0, 56)
    InfoFrame.Position = UDim2.new(0, 12, 0, 56)
    InfoFrame.BackgroundColor3 = CONFIG.Theme.Card
    InfoFrame.BorderSizePixel = 0
    InfoFrame.Parent = MainFrame

    local InfoCorner = Instance.new("UICorner")
    InfoCorner.CornerRadius = UDim.new(0, 8)
    InfoCorner.Parent = InfoFrame

    -- 鸡蛋计数
    local CountLabel = Instance.new("TextLabel")
    CountLabel.Name = "EggCount"
    CountLabel.Size = UDim2.new(1, -16, 0, 24)
    CountLabel.Position = UDim2.new(0, 8, 0, 6)
    CountLabel.BackgroundTransparency = 1
    CountLabel.Text = "Ovos disponíveis: 0"
    CountLabel.TextColor3 = CONFIG.Theme.Text
    CountLabel.TextSize = 14
    CountLabel.Font = Enum.Font.GothamMedium
    CountLabel.TextXAlignment = Enum.TextXAlignment.Left
    CountLabel.Parent = InfoFrame

    -- 状态文字
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusText"
    StatusLabel.Size = UDim2.new(1, -16, 0, 20)
    StatusLabel.Position = UDim2.new(0, 8, 0, 30)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Text = State.CurrentStatus
    StatusLabel.TextColor3 = CONFIG.Theme.TextDim
    StatusLabel.TextSize = 12
    StatusLabel.Font = Enum.Font.Gotham
    StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
    StatusLabel.Parent = InfoFrame

    -- 刷新按钮
    local RefreshBtn = Instance.new("TextButton")
    RefreshBtn.Size = UDim2.new(1, -24, 0, 36)
    RefreshBtn.Position = UDim2.new(0, 12, 0, 120)
    RefreshBtn.BackgroundColor3 = CONFIG.Theme.Accent
    RefreshBtn.Text = "🔄 Atualizar Ovos"
    RefreshBtn.TextColor3 = CONFIG.Theme.Text
    RefreshBtn.TextSize = 14
    RefreshBtn.Font = Enum.Font.GothamBold
    RefreshBtn.Parent = MainFrame

    local RefreshCorner = Instance.new("UICorner")
    RefreshCorner.CornerRadius = UDim.new(0, 8)
    RefreshCorner.Parent = RefreshBtn

    -- 鸡蛋列表容器（滚动）
    local ScrollFrame = Instance.new("ScrollingFrame")
    ScrollFrame.Name = "EggList"
    ScrollFrame.Size = UDim2.new(1, -24, 1, -180)
    ScrollFrame.Position = UDim2.new(0, 12, 0, 164)
    ScrollFrame.BackgroundTransparency = 1
    ScrollFrame.ScrollBarThickness = 4
    ScrollFrame.ScrollBarImageColor3 = CONFIG.Theme.Accent
    ScrollFrame.Parent = MainFrame

    local ListLayout = Instance.new("UIListLayout")
    ListLayout.Padding = UDim.new(0, 8)
    ListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ListLayout.Parent = ScrollFrame

    return {
        ScreenGui = ScreenGui,
        MainFrame = MainFrame,
        CountLabel = CountLabel,
        StatusLabel = StatusLabel,
        RefreshBtn = RefreshBtn,
        EggList = ScrollFrame,
        MinimizeBtn = MinimizeBtn,
    }
end

-- ========== 鸡蛋扫描（框架，需根据实际游戏调整） ==========
local function ScanEggs()
    -- ⚠️ 这里需要根据“Steal an Egg”的实际游戏结构来调整
    -- 通常需要遍历 Workspace 中的鸡蛋模型
    -- 以下仅为示例逻辑，实际无法直接工作
    
    local foundEggs = {}
    
    -- 示例：遍历 Workspace 中所有以 "Egg" 命名的模型
    -- 实际游戏中鸡蛋的命名可能不同，需要用 Dex 工具探索
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name:find("Egg") then
            -- 尝试获取鸡蛋的属性和位置
            local eggInfo = {
                Name = obj.Name,
                Model = obj,
                Position = obj:GetPivot().Position,
                Value = "??",  -- 需要从游戏内部数据获取
            }
            table.insert(foundEggs, eggInfo)
        end
    end
    
    return foundEggs
end

-- ========== 更新鸡蛋列表 UI ==========
local function UpdateEggList(UI, eggs)
    -- 清空现有列表
    for _, child in pairs(UI.EggList:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
    
    -- 更新计数
    UI.CountLabel.Text = "Ovos disponíveis: " .. tostring(#eggs)
    
    -- 为每个鸡蛋创建卡片
    for i, egg in ipairs(eggs) do
        local Card = Instance.new("Frame")
        Card.Name = "EggCard_" .. i
        Card.Size = UDim2.new(1, 0, 0, 72)
        Card.BackgroundColor3 = CONFIG.Theme.Card
        Card.BorderSizePixel = 0
        Card.Parent = UI.EggList
        
        local CardCorner = Instance.new("UICorner")
        CardCorner.CornerRadius = UDim.new(0, 8)
        CardCorner.Parent = Card
        
        -- 鸡蛋名称
        local NameLabel = Instance.new("TextLabel")
        NameLabel.Size = UDim2.new(1, -80, 0, 20)
        NameLabel.Position = UDim2.new(0, 12, 0, 8)
        NameLabel.BackgroundTransparency = 1
        NameLabel.Text = "🥚 " .. egg.Name
        NameLabel.TextColor3 = CONFIG.Theme.Text
        NameLabel.TextSize = 14
        NameLabel.Font = Enum.Font.GothamBold
        NameLabel.TextXAlignment = Enum.TextXAlignment.Left
        NameLabel.Parent = Card
        
        -- 价值
        local ValueLabel = Instance.new("TextLabel")
        ValueLabel.Size = UDim2.new(1, -80, 0, 16)
        ValueLabel.Position = UDim2.new(0, 12, 0, 28)
        ValueLabel.BackgroundTransparency = 1
        ValueLabel.Text = "💰 Valor: " .. tostring(egg.Value)
        ValueLabel.TextColor3 = CONFIG.Theme.TextDim
        ValueLabel.TextSize = 12
        ValueLabel.Font = Enum.Font.Gotham
        ValueLabel.TextXAlignment = Enum.TextXAlignment.Left
        ValueLabel.Parent = Card
        
        -- 偷取按钮
        local StealBtn = Instance.new("TextButton")
        StealBtn.Size = UDim2.new(0, 60, 0, 32)
        StealBtn.Position = UDim2.new(1, -72, 0, 20)
        StealBtn.BackgroundColor3 = CONFIG.Theme.Accent
        StealBtn.Text = "ROUBAR"
        StealBtn.TextColor3 = CONFIG.Theme.Text
        StealBtn.TextSize = 11
        StealBtn.Font = Enum.Font.GothamBold
        StealBtn.Parent = Card
        
        local StealCorner = Instance.new("UICorner")
        StealCorner.CornerRadius = UDim.new(0, 6)
        StealCorner.Parent = StealBtn
        
        -- ⚠️ 注意：这里的点击事件不应包含实际的自动偷取逻辑
        -- 因为这会违反游戏规则
        StealBtn.MouseButton1Click:Connect(function()
            -- 仅更新状态文字作为演示
            UI.StatusLabel.Text = "Status: Função não disponível"
        end)
    end
end

-- ========== 主执行 ==========
local UI = CreateUI()

UI.RefreshBtn.MouseButton1Click:Connect(function()
    local eggs = ScanEggs()
    UpdateEggList(UI, eggs)
end)

UI.MinimizeBtn.MouseButton1Click:Connect(function()
    -- 最小化逻辑
    State.IsOpen = not State.IsOpen
    -- 实现略
end)

print("[Tony's Hub] UI 框架已加载")
