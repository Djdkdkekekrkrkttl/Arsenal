-- Configurações
local ESP_COLOR = Color3.new(1, 0, 0)
local MAX_DISTANCE = 2000

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Verifica se é inimigo
local function IsEnemy(player)
    if not LocalPlayer.Team or not player.Team then return false end
    return LocalPlayer.Team ~= player.Team
end

-- Sistema ESP
local function UpdateESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer or not IsEnemy(player) then continue end
        
        local character = player.Character
        if character and not character:FindFirstChild("Highlight") then
            local highlight = Instance.new("Highlight")
            highlight.FillColor = ESP_COLOR
            highlight.OutlineColor = ESP_COLOR
            highlight.Parent = character
        end
    end
end

-- Mira automática em inimigos
local function TeamAimbot()
    local closestPos = nil
    local closestDist = MAX_DISTANCE

    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer or not IsEnemy(player) then continue end

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

-- Loop principal
RunService.RenderStepped:Connect(function()
    UpdateESP()
    TeamAimbot()
end)
