-- Configurações
local ESP_COLOR = Color3.new(1, 0, 0) -- Vermelho
local MAX_DISTANCE = 2000 -- Unidades Roblox

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis
local ESPCache = {}

-- Função para verificar inimigos
local function IsEnemy(player)
    if not LocalPlayer.Team or not player.Team then return true end
    return LocalPlayer.Team ~= player.Team
end

-- Sistema ESP
local function UpdateESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer or not player.Character then continue end
        
        local character = player.Character
        local head = character:FindFirstChild("Head")
        
        if IsEnemy(player) and head then
            if not ESPCache[player] then
                local highlight = Instance.new("Highlight")
                highlight.FillColor = ESP_COLOR
                highlight.OutlineColor = ESP_COLOR
                highlight.Parent = character
                ESPCache[player] = highlight
            end
            
            -- Atualiza visibilidade baseado na distância
            local distance = (LocalPlayer.Character:GetPivot().Position - character:GetPivot().Position).Magnitude
            ESPCache[player].Enabled = (distance <= MAX_DISTANCE)
        elseif ESPCache[player] then
            ESPCache[player]:Destroy()
            ESPCache[player] = nil
        end
    end
end

-- Mira automática na cabeça
local function AimAtHead()
    local closestTarget = nil
    local closestAngle = math.rad(5) -- Ângulo máximo de 5 graus
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer or not IsEnemy(player) then continue end
        
        local character = player.Character
        if not character then continue end
        
        local head = character:FindFirstChild("Head")
        if not head or not character:FindFirstChild("Humanoid") or character.Humanoid.Health <= 0 then continue end
        
        -- Cálculo de ângulo na tela
        local headPosition = head.Position
        local screenPos, onScreen = Camera:WorldToViewportPoint(headPosition)
        
        if onScreen then
            local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
            local mousePos = Vector2.new(screenPos.X, screenPos.Y)
            local angle = (mousePos - center).Magnitude
            
            if angle < closestAngle then
                closestAngle = angle
                closestTarget = headPosition
            end
        end
    end
    
    -- Atualiza mira
    if closestTarget then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, closestTarget)
    end
end

-- Loop principal
RunService.RenderStepped:Connect(function()
    UpdateESP()
    AimAtHead()
end)
