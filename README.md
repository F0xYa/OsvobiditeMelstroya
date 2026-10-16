local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = game:Workspace.CurrentCamera

local GuiSettings = {
    CrosshairColor = Color3.fromRGB(0, 255, 0),
    CrosshairGap = 4,
    CrosshairLength = 12,
    ClickDelay = 0.05,
    MagnetRadius = 18,
    LowHealthThreshold = 25,
    GlowColor = Color3.fromRGB(218, 165, 32),
    VisualsActive = true,
    AutoClick = false,
    HungerSound = false,
    LowHealth = true,
    NeonSuit = false
}

local function CreateMainHUD()
    local sg = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
    sg.Name = "NightsVisualsRUS"
    sg.ResetOnSpawn = false

    local wm = Instance.new("Frame", sg)
    wm.Size = UDim2.new(0, 160, 0, 35)
    wm.Position = UDim2.new(0.5, -80, 0, 10)
    wm.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    wm.BackgroundTransparency = 0.2
    
    local wmCorner = Instance.new("UICorner", wm)
    wmCorner.CornerRadius = UDim.new(0, 14)
    
    local wmText = Instance.new("TextLabel", wm)
    wmText.Size = UDim2.new(1, 0, 1, 0)
    wmText.BackgroundTransparency = 1
    wmText.TextColor3 = Color3.fromRGB(218, 165, 32)
    wmText.Font = Enum.Font.SourceSansBold
    wmText.TextSize = 15
    wmText.Text = "( fps: -- | ping: -- )"

    local fpsCount = 0
    RunService.RenderStepped:Connect(function()
        fpsCount = fpsCount + 1
    end)
    
    task.spawn(function()
        while task.wait(1) do
            local ping = math.floor(LocalPlayer:GetNetworkPing() * 1000)
            wmText.Text = string.format("( fps: %d | ping: %d )", fpsCount, ping)
            fpsCount = 0
        end
    end)

    local openBtn = Instance.new("TextButton", sg)
    openBtn.Size = UDim2.new(0, 90, 0, 40)
    openBtn.Position = UDim2.new(0, 15, 0, 90)
    openBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    openBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    openBtn.Font = Enum.Font.SourceSansBold
    openBtn.TextSize = 16
    openBtn.Text = "МЕНЮ"
    Instance.new("UICorner", openBtn).CornerRadius = UDim.new(0, 8)

    local mainFrame = Instance.new("Frame", sg)
    mainFrame.Size = UDim2.new(0, 280, 0, 420)
    mainFrame.Position = UDim2.new(0.5, -140, 0.5, -210)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    mainFrame.Visible = false
    Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)

    local title = Instance.new("TextLabel", mainFrame)
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "99 НОЧЕЙ — ВИЗУАЛ ПАК"
    title.TextColor3 = Color3.fromRGB(218, 165, 32)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 18
    title.BackgroundTransparency = 1

    local scroll = Instance.new("ScrollingFrame", mainFrame)
    scroll.Size = UDim2.new(1, -20, 1, -60)
    scroll.Position = UDim2.new(0, 10, 0, 50)
    scroll.BackgroundTransparency = 1
    scroll.CanvasSize = UDim2.new(0, 0, 0, 500)
    scroll.ScrollBarThickness = 4

    local list = Instance.new("UIListLayout", scroll)
    list.Padding = UDim.new(0, 8)

    local function createToggle(name, default, key)
        local btn = Instance.new("TextButton", scroll)
        btn.Size = UDim2.new(1, -10, 0, 40)
        btn.BackgroundColor3 = default and Color3.fromRGB(40, 100, 40) or Color3.fromRGB(40, 40, 40)
        btn.Text = name .. (default and " [ВКЛ]" or " [ВЫКЛ]")
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Font = Enum.Font.SourceSans
        btn.TextSize = 16
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

        btn.MouseButton1Click:Connect(function()
            GuiSettings[key] = not GuiSettings[key]
            btn.BackgroundColor3 = GuiSettings[key] and Color3.fromRGB(40, 100, 40) or Color3.fromRGB(40, 40, 40)
            btn.Text = name .. (GuiSettings[key] and " [ВКЛ]" or " [ВЫКЛ]")
        end)
    end

    local function createSlider(name, min, max, default, callback)
        local sliderFrame = Instance.new("Frame", scroll)
        sliderFrame.Size = UDim2.new(1, -10, 0, 50)
        sliderFrame.BackgroundTransparency = 1

        local lbl = Instance.new("TextLabel", sliderFrame)
        lbl.Size = UDim2.new(1, 0, 0, 20)
        lbl.Text = name .. ": " .. tostring(default)
        lbl.TextColor3 = Color3.fromRGB(200, 200, 200)
        lbl.TextSize = 14
        lbl.Font = Enum.Font.SourceSans
        lbl.BackgroundTransparency = 1

        local bg = Instance.new("Frame", sliderFrame)
        bg.Size = UDim2.new(1, 0, 0, 10)
        bg.Position = UDim2.new(0, 0, 0, 25)
        bg.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        Instance.new("UICorner", bg)

        local fill = Instance.new("Frame", bg)
        fill.Size = UDim2.new((default - min)/(max - min), 0, 1, 0)
        fill.BackgroundColor3 = Color3.fromRGB(218, 165, 32)
        Instance.new("UICorner", fill)

        local btn = Instance.new("TextButton", bg)
        btn.Size = UDim2.new(0, 20, 0, 20)
        btn.Position = UDim2.new((default - min)/(max - min), -10, 0.5, -10)
        btn.Text = ""
        btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        Instance.new("UICorner", btn)

        local dragging = false
        btn.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
            end
        end)
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                local relativeX = math.clamp((input.Position.X - bg.AbsolutePosition.X) / bg.AbsoluteSize.X, 0, 1)
                local val = math.floor(min + (max - min) * relativeX)
                btn.Position = UDim2.new(relativeX, -10, 0.5, -10)
                fill.Size = UDim2.new(relativeX, 0, 1, 0)
                lbl.Text = name .. ": " .. tostring(val)
                callback(val)
            end
        end)
    end

    openBtn.MouseButton1Click:Connect(function()
        mainFrame.Visible = not mainFrame.Visible
    end)

    return mainFrame, createToggle, createSlider, sg
end

local mainFrame, createToggle, createSlider, mainGui = CreateMainHUD()

local function SetupShaders(state)
    local cc = Lighting:FindFirstChild("VisualShaders") or Instance.new("ColorCorrectionEffect", Lighting)
    cc.Name = "VisualShaders"
    cc.Enabled = state
    cc.Brightness = state and 0.05 or 0
    cc.Contrast = state and 0.1 or 0
    cc.Saturation = state and 0.15 or 0
end

local function CreateCustomCrosshair()
    local dot = Drawing.new("Circle")
    dot.Radius = 2
    dot.Filled = true
    dot.Color = GuiSettings.CrosshairColor
    dot.Visible = true

    local lines = {}
    for i = 1, 4 do
        local line = Drawing.new("Line")
        line.Thickness = 2
        line.Color = GuiSettings.CrosshairColor
        line.Visible = true
        table.insert(lines, line)
    end

    RunService.RenderStepped:Connect(function()
        local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
        dot.Position = center
        dot.Visible = GuiSettings.VisualsActive
        
        for _, l in pairs(lines) do 
            l.Color = GuiSettings.CrosshairColor 
            l.Visible = GuiSettings.VisualsActive
        end

        if GuiSettings.VisualsActive then
            lines.From = center - Vector2.new(GuiSettings.CrosshairLength + GuiSettings.CrosshairGap, 0)
            lines.To = center - Vector2.new(GuiSettings.CrosshairGap, 0)
            lines.From = center + Vector2.new(GuiSettings.CrosshairGap, 0)
            lines.To = center + Vector2.new(GuiSettings.CrosshairLength + GuiSettings.CrosshairGap, 0)
            lines.From = center - Vector2.new(0, GuiSettings.CrosshairLength + GuiSettings.CrosshairGap)
            lines.To = center - Vector2.new(0, GuiSettings.CrosshairGap)
            lines.From = center + Vector2.new(0, GuiSettings.CrosshairGap)
            lines.To = center + Vector2.new(0, GuiSettings.CrosshairLength + GuiSettings.CrosshairGap)
        end
    end)
end

local function UpdateCharacterCosmetics(char)
    if not char then return end
    for _, part in pairs(char:GetChildren()) do
        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
            if GuiSettings.NeonSuit then
                part.Material = Enum.Material.Neon
                part.Color = GuiSettings.GlowColor
            end
            if not part:FindFirstChild("HandGlow") and (part.Name:find("Hand") or part.Name:find("Arm")) then
                local box = Instance.new("SelectionBox")
                box.Name = "HandGlow"
                box.Color3 = GuiSettings.GlowColor
                box.LineThickness = 0.04
                box.Adornee = part
                box.Parent = part
            end
        end
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(0.5)
    UpdateCharacterCosmetics(char)
end)

task.spawn(function()
    while task.wait(0.3) do
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChildOfClass("Humanoid")

        if hum and GuiSettings.LowHealth and hum.Health <= GuiSettings.LowHealthThreshold then
            local flash = mainGui:FindFirstChild("HealthFlash") or Instance.new("Frame", mainGui)
            flash.Name = "HealthFlash"
flash.Size = UDim2.new(1, 0, 1, 0)
flash.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
flash.BackgroundTransparency = 0.8
TweenService:Create(flash, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingStyle.InOut, 0, true), {BackgroundTransparency = 1}):Play()
end
if root and GuiSettings.MagnetRadius > 0 then
for _, item in pairs(workspace:GetChildren()) do
if item:IsA("Tool") and item:FindFirstChild("Handle") then
if (item.Handle.Position - root.Position).Magnitude < GuiSettings.MagnetRadius then
item.Handle.CFrame = root.CFrame
end
end
end
end
end
end)
task.spawn(function()
while task.wait(5) do
if GuiSettings.HungerSound then
local hunger = LocalPlayer:FindFirstChild("Data") and LocalPlayer.Data:FindFirstChild("Hunger")
if hunger and hunger.Value <= 20 then
local sound = Instance.new("Sound", Workspace)
sound.SoundId = "rbxassetid://9114223191"
sound.Volume = 1
sound:Play()
game:GetService("Debris"):AddItem(sound, 2)
end
end
end
end)
task.spawn(function()
while true do
if GuiSettings.AutoClick then
local tool = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Tool")
if tool then tool:Activate() end
end
task.wait(GuiSettings.ClickDelay)
end
end)
createToggle("Кастомный прицел", true, "VisualsActive")
createToggle("Косметика (Неон тело)", false, "NeonSuit")
createToggle("Звук при голоде", false, "HungerSound")
createToggle("Оповещение о низком ХП", true, "LowHealth")
createToggle("Авто-замах (Кликер)", false, "AutoClick")
createToggle("Улучшенные Шейдеры", false, "Shaders")
mainGui.NightsVisualsRUS.Frame.ScrollingFrame.ChildAdded:Connect(function(child)
if child:IsA("TextButton") and child.Text:find("Шейдеры") then
child.MouseButton1Click:Connect(function()
task.wait(0.1)
SetupShaders(GuiSettings.Shaders)
end)
end
end)
createSlider("Скроллер замаха (сек)", 1, 10, 5, function(val) GuiSettings.ClickDelay = val / 100 end)
createSlider("Магнит предметов (м)", 5, 50, 18, function(val) GuiSettings.MagnetRadius = val end)
CreateCustomCrosshair()
if LocalPlayer.Character then UpdateCharacterCosmetics(LocalPlayer.Character) end
