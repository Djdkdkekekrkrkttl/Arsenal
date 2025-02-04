-- Configurações iniciais
local settings = {
    esp = true,
    aimbot = true,
    max_distance = 1000,
    esp_color = Color3.new(1, 0, 0)
}

-- Serviços
local players = game:GetService("Players")
local run_service = game:GetService("RunService")
local uis = game:GetService("UserInputService")
local local_player = players.LocalPlayer
local camera = workspace.CurrentCamera

-- Menu GUI
local gui = Instance.new("ScreenGui")
gui.Name = "ArsenalHaxGUI"
gui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 250)
frame.Position = UDim2.new(0.5, -100, 0.5, -125)
frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
frame.Parent = gui

-- Elementos do menu
local elements = {
    title = Instance.new("TextLabel"),
    esp_toggle = Instance.new("TextButton"),
    aimbot_toggle = Instance.new("TextButton"),
    distance_slider = Instance.new("TextBox"),
    color_picker = Instance.new("TextBox")
}

-- Configura elementos
local y_offset = 10
for _, element in pairs(elements) do
    element.Size = UDim2.new(0.9, 0, 0, 30)
    element.Position = UDim2.new(0.05, 0, 0, y_offset)
    element.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    element.TextColor3 = Color3.new(1, 1, 1)
    element.Font = Enum.Font.SourceSans
    element.Parent = frame
    y_offset = y_offset + 35
end

-- Personalização
elements.title.Text = "Arsenal Script Menu (Right Shift)"
elements.esp_toggle.Text = "ESP: ON"
elements.aimbot_toggle.Text = "Aimbot: ON"
elements.distance_slider.PlaceholderText = "Distância máxima: "..settings.max_distance
elements.color_picker.PlaceholderText = "Cor (R,G,B): 1,0,0"

-- Funções do menu
elements.esp_toggle.MouseButton1Click:Connect(function()
    settings.esp = not settings.esp
    elements.esp_toggle.Text = "ESP: "..(settings.esp and "ON" or "OFF")
end)

elements.aimbot_toggle.MouseButton1Click:Connect(function()
    settings.aimbot = not settings.aimbot
    elements.aimbot_toggle.Text = "Aimbot: "..(settings.aimbot and "ON" or "OFF")
end)

elements.distance_slider.FocusLost:Connect(function()
    local num = tonumber(elements.distance_slider.Text)
    if num and num > 0 then
        settings.max_distance = num
        elements.distance_slider.PlaceholderText = "Distância: "..num
    end
    elements.distance_slider.Text = ""
end)

elements.color_picker.FocusLost:Connect(function()
    local r, g, b = elements.color_picker.Text:match("([%d.]+),([%d.]+),([%d.]+)")
    if r and g and b then
        settings.esp_color = Color3.new(r, g, b)
    end
    elements.color_picker.Text = ""
end)

-- Toggle menu com RightShift
uis.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.RightShift then
        frame.Visible = not frame.Visible
    end
end)

-- Sistema ESP e Aimbot (atualizado)
-- ... (Use o código da versão anterior aqui, substituindo as variáveis estáticas por 'settings')

-- Linkar configurações ao sistema
local function is_enemy(player)
    return player.Team ~= local_player.Team
end

-- Função ESP modificada
local function update_esp()
    if not settings.esp then return end
    -- ... (código ESP anterior usando settings.esp_color e settings.max_distance)
end

-- Função Aimbot modificada
local function aimbot()
    if not settings.aimbot then return end
    -- ... (código aimbot anterior usando settings.max_distance)
end

-- Loop principal
run_service.RenderStepped:Connect(function()
    update_esp()
    if settings.aimbot then
        aimbot()
    end
end)-- Configurações iniciais
local settings = {
    esp = true,
    aimbot = true,
    max_distance = 1000,
    esp_color = Color3.new(1, 0, 0)
}

-- Serviços
local players = game:GetService("Players")
local run_service = game:GetService("RunService")
local uis = game:GetService("UserInputService")
local local_player = players.LocalPlayer
local camera = workspace.CurrentCamera

-- Menu GUI
local gui = Instance.new("ScreenGui")
gui.Name = "ArsenalHaxGUI"
gui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 250)
frame.Position = UDim2.new(0.5, -100, 0.5, -125)
frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
frame.Parent = gui

-- Elementos do menu
local elements = {
    title = Instance.new("TextLabel"),
    esp_toggle = Instance.new("TextButton"),
    aimbot_toggle = Instance.new("TextButton"),
    distance_slider = Instance.new("TextBox"),
    color_picker = Instance.new("TextBox")
}

-- Configura elementos
local y_offset = 10
for _, element in pairs(elements) do
    element.Size = UDim2.new(0.9, 0, 0, 30)
    element.Position = UDim2.new(0.05, 0, 0, y_offset)
    element.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    element.TextColor3 = Color3.new(1, 1, 1)
    element.Font = Enum.Font.SourceSans
    element.Parent = frame
    y_offset = y_offset + 35
end

-- Personalização
elements.title.Text = "Arsenal Script Menu (Right Shift)"
elements.esp_toggle.Text = "ESP: ON"
elements.aimbot_toggle.Text = "Aimbot: ON"
elements.distance_slider.PlaceholderText = "Distância máxima: "..settings.max_distance
elements.color_picker.PlaceholderText = "Cor (R,G,B): 1,0,0"

-- Funções do menu
elements.esp_toggle.MouseButton1Click:Connect(function()
    settings.esp = not settings.esp
    elements.esp_toggle.Text = "ESP: "..(settings.esp and "ON" or "OFF")
end)

elements.aimbot_toggle.MouseButton1Click:Connect(function()
    settings.aimbot = not settings.aimbot
    elements.aimbot_toggle.Text = "Aimbot: "..(settings.aimbot and "ON" or "OFF")
end)

elements.distance_slider.FocusLost:Connect(function()
    local num = tonumber(elements.distance_slider.Text)
    if num and num > 0 then
        settings.max_distance = num
        elements.distance_slider.PlaceholderText = "Distância: "..num
    end
    elements.distance_slider.Text = ""
end)

elements.color_picker.FocusLost:Connect(function()
    local r, g, b = elements.color_picker.Text:match("([%d.]+),([%d.]+),([%d.]+)")
    if r and g and b then
        settings.esp_color = Color3.new(r, g, b)
    end
    elements.color_picker.Text = ""
end)

-- Toggle menu com RightShift
uis.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.RightShift then
        frame.Visible = not frame.Visible
    end
end)

-- Sistema ESP e Aimbot (atualizado)
-- ... (Use o código da versão anterior aqui, substituindo as variáveis estáticas por 'settings')

-- Linkar configurações ao sistema
local function is_enemy(player)
    return player.Team ~= local_player.Team
end

-- Função ESP modificada
local function update_esp()
    if not settings.esp then return end
    -- ... (código ESP anterior usando settings.esp_color e settings.max_distance)
end

-- Função Aimbot modificada
local function aimbot()
    if not settings.aimbot then return end
    -- ... (código aimbot anterior usando settings.max_distance)
end 
-- Adicione esta parte após criar o 'gui'
local mobileToggleButton = Instance.new("TextButton")
mobileToggleButton.Size = UDim2.new(0, 50, 0, 50)
mobileToggleButton.Position = UDim2.new(0.95, -60, 0.9, -60) -- Canto inferior direito
mobileToggleButton.Text = "☰"
mobileToggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
mobileToggleButton.TextColor3 = Color3.new(1, 1, 1)
mobileToggleButton.Parent = gui

-- Altere esta parte da conexão de input
mobileToggleButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
    mobileToggleButton.Visible = not frame.Visible -- Esconde o botão quando o menu está aberto
end)

-- Garanta que o menu comece oculto
frame.Visible = false
mobileToggleButton.Visible = true
local y_offset = 15
for _, element in pairs(elements) do
    element.Position = UDim2.new(0.05, 0, 0, y_offset)
    y_offset = y_offset + 50 -- Mais espaço entre elementos
end

-- Loop principal
run_service.RenderStepped:Connect(function()
    update_esp()
    if settings.aimbot then
        aimbot()
    end
end)
