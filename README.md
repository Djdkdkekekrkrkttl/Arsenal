-- Sistema básico de Mira Automática (Corpo)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local function FindClosestEnemy()
    local closestPlayer = nil
    local closestDistance = math.huge
    
    for _, player in ipairs(Players:GetPlayers()) do
        -- Verifica se é inimigo
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team then
            local character = player.Character
            if character then
                local rootPart = character:FindFirstChild("HumanoidRootPart")
                if rootPart then
                    local distance = (LocalPlayer.Character.HumanoidRootPart.Position - rootPart.Position).Magnitude
                    if distance < closestDistance then
                        closestDistance = distance
                        closestPlayer = rootPart
                    end
                end
            end
        end
    end
    return closestPlayer
end

-- Atualização contínua
RunService.RenderStepped:Connect(function()
    local target = FindClosestEnemy()
    if target then
        workspace.CurrentCamera.CFrame = CFrame.new(
            workspace.CurrentCamera.CFrame.Position,
            target.Position
        )
    end
end)
