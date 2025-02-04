
  Features:
  1. ESP por time
  2. Aimbot (cabeça/corpo)
  3. Menu mobile personalizável
  4. Auto-disparo
  5. Configurações em tempo real
]]

--=== Configurações ===--
local settings = {
    ESP = true,
    Aimbot = true,
    AutoShoot = false,
    AimAtHead = true,
    FOV = 80, -- Graus
    MaxDistance = 1500,
    ESPColor = Color3.new(1, 0.2, 0.2),
    MenuKey = Enum.KeyCode.RightShift
}

--=== Serviços ===--
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--=== GUI Mobile ===--
local gui = Instance.new("ScreenGui")
gui.Name = "MobileHaxGUI"
gui.Parent = game.CoreGui

-- Botão do menu
local menuButton = Instance.new("TextButton")
menuButton.Size = UDim2.new(0, 60, 0, 60)
menuButton.Position = UDim2.new(0.95, -70, 0.9, -70)
menuButton.Text = "☰"
menuButton.TextSize = 30
menuButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
menuButton.TextColor3 = Color3.new(1, 1, 1)
menuButton.Parent = gui

-- Painel principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 400)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
mainFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
mainFrame.Visible = false
mainFrame.Parent = gui

-- Elementos do menu
local function CreateControl(text, yPos, isToggle)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 40)
    frame.Position = UDim2.new(0.05, 0, 0, yPos)
    frame.BackgroundTransparency = 1
    
    local label = Instance.new("TextLabel")
    label.Text = text
    label.Size = UDim2.new(0.6, 0, 1, 0)
    label.TextColor3 = Color3.new(1, 1, 1)
    label.Font = Enum.Font.SourceSansBold
    label.Parent = frame
    
    if isToggle then
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(0.3, 0, 0.8, 0)
        button.Position = UDim2.new(0.7, 0, 0.1, 0)
        button.Text = "OFF"
        button.Parent = frame
        return button
    else
        local slider = Instance.new("TextBox")
        slider.Size = UDim2.new(0.3, 0, 0.8, 0)
        slider.Position = UDim2.new(0.7, 0, 0.1, 0)
        slider.BackgroundColor3 = Color3.new(0.25, 0.25, 0.25)
        slider.Parent = frame
        return slider
    end
end

-- Controles
local controls = {
    espToggle = CreateControl("ESP", 10, true),
    aimbotToggle = CreateControl("Aimbot", 60, true),
    autoShootToggle = CreateControl("Auto Shoot", 110, true),
    fovSlider = CreateControl("FOV", 160, false),
    distanceSlider = CreateControl("Max Distance", 210, false)
}

--=== Sistema ESP ===--
local ESPCache = {}

local function UpdateESP()
    if not settings.ESP then return end
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            if not ESPCache[player] then
                local highlight = Instance.new("Highlight")
                highlight.FillColor = settings.ESPColor
                highlight.OutlineColor = settings.ESPColor
                highlight.Parent = character
                ESPCache[player] = highlight
            end
            
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - character.HumanoidRootPart.Position).Magnitude
            ESPCache[player].Enabled = (distance <= settings.MaxDistance)
        end
    end
end

--=== Sistema Aimbot ===--
local function GetClosestEnemy()
    local closestTarget = nil
    local closestAngle = math.rad(settings.FOV)
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") and character.Humanoid.Health > 0 then
            local targetPart = settings.AimAtHead and character:FindFirstChild("Head") or character:FindFirstChild("HumanoidRootPart")
            if targetPart then
                local screenPos, onScreen = Camera:WorldToScreenPoint(targetPart.Position)
                if onScreen then
                    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
                    local angle = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                    
                    if angle < closestAngle then
                        closestTarget = targetPart
                        closestAngle = angle
                    end
                end
            end
        end
    end
    
    return closestTarget
end

--=== Loop Principal ===--
RunService.RenderStepped:Connect(function()
    -- Atualiza ESP
    UpdateESP()
    
    -- Aimbot + AutoShoot
    if settings.Aimbot then
        local target = GetClosestEnemy()
        if target then
            -- Mira
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
            
            -- Auto disparo
            if settings.AutoShoot then
                local args = {
                    [1] = target.Position,
                    [2] = target.Parent.Humanoid
                }
                game:GetService("ReplicatedStorage"):WaitForChild("Events"):WaitForChild("HitPart"):FireServer(unpack(args))
            end
        end
    end
end)

--=== Controles do Menu ===--
menuButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
end)

controls.espToggle.MouseButton1Click:Connect(function()
    settings.ESP = not settings.ESP
    controls.espToggle.Text = settings.ESP and "ON" or "OFF"
end)

controls.aimbotToggle.MouseButton1Click:Connect(function()
    settings.Aimbot = not settings.Aimbot
    controls.aimbotToggle.Text = settings.Aimbot and "ON" or "OFF"
end)

controls.autoShootToggle.MouseButton1Click:Connect(function()
    settings.AutoShoot = not settings.AutoShoot
    controls.autoShootToggle.Text = settings.AutoShoot and "ON" or "OFF"
end)

controls.fovSlider.FocusLost:Connect(function()
    local value = tonumber(controls.fovSlider.Text)
    if value then
        settings.FOV = math.clamp(value, 10, 360)
    end
    controls.fovSlider.Text = ""
end)

controls.distanceSlider.FocusLost:Connect(function()
    local value = tonumber(controls.distanceSlider.Text)
    if value then
        settings.MaxDistance = math.clamp(value, 100, 5000)
    end
    controls.distanceSlider.Text = ""
end) -- [...] (Parte inicial do código permanece igual)

--=== FUNÇÃO DE VERIFICAÇÃO DE INIMIGOS ===--
local function IsEnemy(player)
    if not LocalPlayer.Team or not player.Team then return false end
    return LocalPlayer.Team ~= player.Team
end

--=== SISTEMA ESP ATUALIZADO ===--
local function UpdateESP()
    if not settings.ESP then return end
    
    for _, player in ipairs(Players:GetPlayers()) do
        -- Verificação de time adicionada
        if player == LocalPlayer or not IsEnemy(player) then continue end
        
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            if not ESPCache[player] then
                local highlight = Instance.new("Highlight")
                highlight.FillColor = settings.ESPColor
                highlight.OutlineColor = settings.ESPColor
                highlight.Parent = character
                ESPCache[player] = highlight
            end
            
            -- Atualização de distância
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - character.HumanoidRootPart.Position).Magnitude
            ESPCache[player].Enabled = (distance <= settings.MaxDistance)
        end
    end
end

--=== SISTEMA AIMBOT CORRIGIDO ===--
local function GetClosestEnemy()
    local closestTarget = nil
    local closestAngle = math.rad(settings.FOV)
    
    for _, player in ipairs(Players:GetPlayers()) do
        -- Verificação dupla: time e status
        if player == LocalPlayer or not IsEnemy(player) then continue end
        
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") and character.Humanoid.Health > 0 then
            local targetPart = settings.AimAtHead and character:FindFirstChild("Head") or character:FindFirstChild("HumanoidRootPart")
            if targetPart then
                local screenPos, onScreen = Camera:WorldToScreenPoint(targetPart.Position)
                if onScreen then
                    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
                    local angle = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                    
                    if angle < closestAngle then
                        closestTarget = targetPart
                        closestAngle = angle
                    end
                end
            end
        end
    end
    
    return closestTarget
end

-- [...] (O resto do código permanece igual)
