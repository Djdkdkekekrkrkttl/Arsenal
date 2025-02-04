
local Tool = script.Parent
local Player = game.Players.LocalPlayer
local Camera = workspace.CurrentCamera
local MaxDistance = 50 -- Distância máxima para detectar inimigos

local function findNearestEnemy()
    local closest = nil
    local closestDistance = MaxDistance
    
    for _, otherPlayer in ipairs(game.Players:GetPlayers()) do
        if otherPlayer ~= Player and otherPlayer.Character then
            local humanoid = otherPlayer.Character:FindFirstChild("Humanoid")
            local rootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
            
            if humanoid and humanoid.Health > 0 and rootPart then
                local distance = (rootPart.Position - Player.Character.HumanoidRootPart.Position).Magnitude
                
                if distance < closestDistance then
                    closest = rootPart
                    closestDistance = distance
                end
            end
        end
    end
    return closest
end

Tool.Equipped:Connect(function()
    while Tool.Parent == Player.Character do
        task.wait(0.1)
        local target = findNearestEnemy()
        
        if target then
            -- Mira na posição do inimigo
            local targetPosition = target.Position
            local cameraPosition = Camera.CFrame.Position
            Camera.CFrame = CFrame.new(cameraPosition, targetPosition)
        end
    end
end)
  
