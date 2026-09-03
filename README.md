-- سكربت: نسخ سكناتو☝️
-- واجهة تفاعلية مع قائمة لاعبين، زر نسخ، ودائرة تحكم عائمة مع إيموجي الثعلب 🦊

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

-- إزالة أي واجهة قديمة لمنع التكرار
if CoreGui:FindFirstChild("SkinCopyGUI_Shhd") then
    CoreGui.SkinCopyGUI_Shhd:Destroy()
end

-- إنشاء الشاشة الرئيسية
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SkinCopyGUI_Shhd"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

---------------------------------------------------------
-- 1. اللائحة الرئيسية (الخضراء الشفافة ذات الحواف الملونة)
---------------------------------------------------------
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 60, 30)
MainFrame.BackgroundTransparency = 0.35 -- شفافية ناعمة
MainFrame.Position = UDim2.new(0.5, -130, 0.5, -175)
MainFrame.Size = UDim2.new(0, 260, 0, 350)
MainFrame.Active = true
MainFrame.Draggable = true -- حركة سلسة للائحة

-- حواف ملونة متدرجة للائحة
local UIStroke = Instance.new("UIStroke")
UIStroke.Parent = MainFrame
UIStroke.Thickness = 3
UIStroke.Color = Color3.fromRGB(0, 255, 128)

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

-- عنوان اللائحة
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = MainFrame
TitleLabel.BackgroundTransparency = 1
TitleLabel.Position = UDim2.new(0, 0, 0, 8)
TitleLabel.Size = UDim2.new(1, 0, 0, 30)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Text = "نسخ سكناتو ☝️"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 18

---------------------------------------------------------
-- 2. قائمة أسماء اللاعبين (شكل عامودي مع ScrollingFrame)
---------------------------------------------------------
local ScrollingFrame = Instance.new("ScrollingFrame")
ScrollingFrame.Parent = MainFrame
ScrollingFrame.BackgroundTransparency = 1
ScrollingFrame.Position = UDim2.new(0, 10, 0, 45)
ScrollingFrame.Size = UDim2.new(0, 240, 0, 290)
ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollingFrame.ScrollBarThickness = 4

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollingFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

local selectedPlayer = nil

local function updatePlayerList()
    -- مسح القائمة القديمة لتجنب التكرار
    for _, child in pairs(ScrollingFrame:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end

    local ySize = 0
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            ySize = ySize + 44

            -- حاوية اللاعب
            local PlayerItem = Instance.new("Frame")
            PlayerItem.Parent = ScrollingFrame
            PlayerItem.BackgroundColor3 = Color3.fromRGB(30, 80, 45)
            PlayerItem.BackgroundTransparency = 0.5
            PlayerItem.Size = UDim2.new(1, -5, 0, 38)
            
            local ItemCorner = Instance.new("UICorner")
            ItemCorner.CornerRadius = UDim.new(0, 8)
            ItemCorner.Parent = PlayerItem

            -- اسم اللاعب
            local NameLabel = Instance.new("TextLabel")
            NameLabel.Parent = PlayerItem
            NameLabel.BackgroundTransparency = 1
            NameLabel.Position = UDim2.new(0, 10, 0, 0)
            NameLabel.Size = UDim2.new(0, 130, 1, 0)
            NameLabel.Font = Enum.Font.GothamSemibold
            NameLabel.Text = player.Name
            NameLabel.TextColor3 = Color3.fromRGB(240, 240, 240)
            NameLabel.TextSize = 13
            NameLabel.TextXAlignment = Enum.TextXAlignment.Left

            -- زر النسخ الأحمر المطلوب
            local CopyButton = Instance.new("TextButton")
            CopyButton.Parent = PlayerItem
            CopyButton.BackgroundColor3 = Color3.fromRGB(220, 50, 50) -- لون أحمر جذاب
            CopyButton.Position = UDim2.new(1, -85, 0.5, -14)
            CopyButton.Size = UDim2.new(0, 78, 0, 28)
            CopyButton.Font = Enum.Font.GothamBold
            CopyButton.Text = "نسخ السكن"
            CopyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            CopyButton.TextSize = 11

            local BtnCorner = Instance.new("UICorner")
            BtnCorner.CornerRadius = UDim.new(0, 6)
            BtnCorner.Parent = CopyButton

            -- تفعيل حدث النسخ باستخدام الريموت الخاص بك
            CopyButton.MouseButton1Click:Connect(function()
                selectedPlayer = player
                local args = {
                    [1] = selectedPlayer
                }
                
                -- إطلاق الريموت لتغيير السكن
                local success, err = pcall(function()
                    ReplicatedStorage.CopyCharacter:FireServer(unpack(args))
                end)

                if success then
                    CopyButton.Text = "تم! ✓"
                    task.wait(1)
                    CopyButton.Text = "نسخ السكن"
                else
                    CopyButton.Text = "خطأ!"
                    task.wait(1)
                    CopyButton.Text = "نسخ السكن"
                end
            end)
        end
    end
    ScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
end

updatePlayerList()
Players.PlayerAdded:Connect(updatePlayerList)
Players.PlayerRemoving:Connect(updatePlayerList)

---------------------------------------------------------
-- 3. دائرة التحكم الصغيرة المتحركة مع إيموجي الثعلب 🦊
---------------------------------------------------------
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "FoxToggleButton"
ToggleButton.Parent = ScreenGui
ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 220, 130)
ToggleButton.Position = UDim2.new(0, 20, 0.5, -25)
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Image = "rbxassetid://6034293936" -- أيقونة دائرية أساسية متوافقة
ToggleButton.ScaleType = Enum.ScaleType.Fit

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(1, 0) -- شكل دائري تماماً
ToggleCorner.Parent = ToggleButton

local ToggleStroke = Instance.new("UIStroke")
ToggleStroke.Parent = ToggleButton
ToggleStroke.Thickness = 2
ToggleStroke.Color = Color3.fromRGB(255, 255, 255)

-- إضافة إيموجي الثعلب 🦊 داخل الدائرة
local FoxEmoji = Instance.new("TextLabel")
FoxEmoji.Parent = ToggleButton
FoxEmoji.BackgroundTransparency = 1
FoxEmoji.Size = UDim2.new(1, 0, 1, 0)
FoxEmoji.Font = Enum.Font.GothamBold
FoxEmoji.Text = "🦊"
FoxEmoji.TextSize = 22
FoxEmoji.TextColor3 = Color3.fromRGB(255, 255, 255)

-- جعل الدائرة قابلة للسحب بسلاسة وثبات على الشاشة
local dragging, dragInput, dragStart, startPos

ToggleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = ToggleButton.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

ToggleButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        ToggleButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- وظيفة إظهار وإخفاء اللائحة عند الضغط على الدائرة
local isVisible = true
ToggleButton.MouseButton1Click:Connect(function()
    isVisible = not isVisible
    MainFrame.Visible = isVisible
    
    -- تأثير انتقالي خفيف للحركة والسلاسة
    local tweenInfo = TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    if isVisible then
        TweenService:Create(ToggleButton, tweenInfo, {Rotation = 0}):Play()
    else
        TweenService:Create(ToggleButton, tweenInfo, {Rotation = 360}):Play()
    end
end)
