-- Configurações
local esp_color = Color3.new(1, 0, 0) -- Cor do ESP
local max_distance = 1000 -- Distância máxima

-- Serviços
local players = game:GetService("Players")
local run_service = game:GetService("RunService")
local local_player = players.LocalPlayer
local camera = workspace.CurrentCamera

-- Variáveis
local esp_cache = {}

-- Função para verificar se é inimigo
local function is_enemy(player)
    if not local_player.Team or not player.Team then return true end -- Fallback
    return player.Team ~= local_player.Team
end

-- Função para criar ESP
local function create_esp(player)
    local highlight = Instance.new("Highlight")
    highlight.FillColor = esp_color
    highlight.OutlineColor = esp_color
    highlight.Parent = player.Character
    esp_cache[player] = highlight
end

-- Função para atualizar ESP
local function update_esp()
    for _, player in ipairs(players:GetPlayers()) do
        if player ~= local_player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            if not esp_cache[player] then
                create_esp(player)
            end
            local distance = (local_player.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            esp_cache[player].Enabled = distance <= max_distance and is_enemy(player) -- Só mostra inimigos
        end
    end
end

-- Função de mira automática corrigida
local function aimbot()
    local closest = nil
    local max_dist = math.huge
    
    for _, player in ipairs(players:GetPlayers()) do
        if 
            player ~= local_player and
            is_enemy(player) and -- Verificação crucial aqui
            player.Character and
            player.Character:FindFirstChild("Humanoid") and
            player.Character.Humanoid.Health > 0 
        then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if root then
                local screen_pos, on_screen = camera:WorldToScreenPoint(root.Position)
                if on_screen then
                    local distance = (Vector2.new(screen_pos.X, screen_pos.Y) - Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y/2)).Magnitude
                    if distance < max_dist then
                        closest = root
                        max_dist = distance
                    end
                end
            end
        end
    end
    
    if closest then
        camera.CFrame = CFrame.new(camera.CFrame.Position, closest.Position)
    end
end

-- Conexões (mantidas igual)
players.PlayerRemoving:Connect(function(player)
    if esp_cache[player] then
        esp_cache[player]:Destroy()
        esp_cache[player] = nil
    end
end)

run_service.RenderStepped:Connect(function()
    update_esp()
    aimbot()
end)
