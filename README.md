-- Configurações básicas
local ESP_COLOR = Color3.new(1, 0, 0)
local MAX_DISTANCE = 2000

-- Serviços essenciais
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Sistema ESP simplificado
local function CreateESP(player)
    if player == LocalPlayer then return end
    
    local highlight = Instance.new("Highlight")
    highlight.FillColor = ESP_COLOR
    highlight.OutlineColor = ESP_COLOR
    highlight.Parent = player.Character
    return highlight
end

-- Mira automática básica (corpo inteiro)
local function SimpleAimbot()
    local closestPos = nil
    local closestDist = MAX_DISTANCE

    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end

        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local rootPart = character.HumanoidRootPart
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - rootPart.Position).Magnitude
            
            if distance < closestDist then
                closestDist = distance
                closestPos = rootPart.Position
            end
        end
    end

    if closestPos then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, closestPos)
    end
end

-- Loop principal otimizado
RunService.RenderStepped:Connect(function()
    -- Atualiza ESP
    for _, player in ipairs(Players:GetPlayers()) do
        if not player.Character or player == LocalPlayer then continue end
        
        if not player.Character:FindFirstChild("Highlight") then
            CreateESP(player)
        end
    end

    -- Atualiza mira
    SimpleAimbot()
end)
