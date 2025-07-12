
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function rainbowColor(t)
    local hue = (tick() * 0.5 + t * 0.5) % 1
    return Color3.fromHSV(hue, 1, 1)
end

local function createUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "SYC_GUI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(0, 400, 0, 300)
    mainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
    mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = screenGui
    
    local outline = Instance.new("Frame")
    outline.Size = UDim2.new(1, 10, 1, 10)
    outline.Position = UDim2.new(0, -5, 0, -5)
    outline.BackgroundTransparency = 1
    outline.BorderSizePixel = 2
    outline.Parent = mainFrame
    
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 30)
    header.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    header.Parent = mainFrame
    
    local title = Instance.new("TextLabel")
    title.Text = "SYC's GUI"
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 20
    title.Size = UDim2.new(1, 0, 1, 0)
    title.BackgroundTransparency = 1
    title.TextColor3 = Color3.new(1, 1, 1)
    title.Parent = header
    
    RunService.Heartbeat:Connect(function()
        local hueColor = rainbowColor(tick() + 0.5)
        title.TextColor3 = hueColor
    end)
    
    local dragging = false
    local dragStart, startPos
    
    header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
        end
    end)
    
    header.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
    
    local tabContainer = Instance.new("Frame")
    tabContainer.Size = UDim2.new(1, 0, 1, -30)
    tabContainer.Position = UDim2.new(0, 0, 0, 30)
    tabContainer.BackgroundTransparency = 1
    tabContainer.Parent = mainFrame
    
    local tabNames = {"Home", "Speed", "Misc"}
    local currentTab = nil
    local tabContents = {}
    
    local tabButtonsContainer = Instance.new("Frame")
    tabButtonsContainer.Size = UDim2.new(1, 0, 0, 30)
    tabButtonsContainer.Position = UDim2.new(0, 0, 0, 0)
    tabButtonsContainer.BackgroundTransparency = 1
    tabButtonsContainer.Parent = mainFrame
    
    for i, name in ipairs(tabNames) do
        local btn = Instance.new("TextButton")
        btn.Text = name
        btn.Size = UDim2.new(0, 133, 1, 0)
        btn.Position = UDim2.new(0, (i - 1) * 133, 0, 0)
        btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        btn.TextColor3 = Color3.new(1, 1, 1)
        btn.Font = Enum.Font.SourceSans
        btn.TextSize = 16
        btn.Parent = tabButtonsContainer
        
        local outline = Instance.new("Frame")
        outline.Size = UDim2.new(1, 4, 1, 4)
        outline.Position = UDim2.new(0, -2, 0, -2)
        outline.BackgroundTransparency = 1
        outline.BorderSizePixel = 2
        outline.BorderColor3 = Color3.new(1, 0, 0)
        outline.Parent = btn

        btn.MouseButton1Click:Connect(function()
            if currentTab then
                currentTab.Visible = false
            end
            currentTab = tabContents[name]
            currentTab.Visible = true
        end)
    end
    
    for _, name in ipairs(tabNames) do
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(1, 0, 1, -30)
        frame.Position = UDim2.new(0, 0, 0, 30)
        frame.BackgroundTransparency = 1
        frame.Visible = false
        frame.Parent = tabContainer
        tabContents[name] = frame
    end
    
    local speedTab = tabContents["Speed"]
    
    local speedLabel = Instance.new("TextLabel")
    speedLabel.Text = "Speed Boost"
    speedLabel.Size = UDim2.new(0, 120, 0, 30)
    speedLabel.Position = UDim2.new(0, 10, 0, 10)
    speedLabel.BackgroundTransparency = 1
    speedLabel.TextColor3 = Color3.new(1, 1, 1)
    speedLabel.Font = Enum.Font.SourceSans
    speedLabel.TextSize = 16
    speedLabel.Parent = speedTab
    
    local speedToggle = Instance.new("TextButton")
    speedToggle.Text = "OFF"
    speedToggle.Size = UDim2.new(0, 60, 0, 30)
    speedToggle.Position = UDim2.new(0, 140, 0, 10)
    speedToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    speedToggle.TextColor3 = Color3.new(1, 1, 1)
    speedToggle.Font = Enum.Font.SourceSans
    speedToggle.TextSize = 16
    speedToggle.Parent = speedTab
    
    local speedEnabled = false
    
    speedToggle.MouseButton1Click:Connect(function()
        speedEnabled = not speedEnabled
        if speedEnabled then
            speedToggle.Text = "ON"
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                LocalPlayer.Character.Humanoid.WalkSpeed = 32
            end
        else
            speedToggle.Text = "OFF"
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                LocalPlayer.Character.Humanoid.WalkSpeed = 16
            end
        end
    end)
    
    if #tabNames > 0 then
        local firstTab = tabContents[tabNames[1]]
        firstTab.Visible = true
        currentTab = firstTab
    end
    
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Text = "☰"
    toggleBtn.Size = UDim2.new(0, 50, 0, 50)
    toggleBtn.Position = UDim2.new(0, 10, 0, 10)
    toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    toggleBtn.TextColor3 = Color3.new(
