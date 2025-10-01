local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Esperamos que el PlayerGui esté listo
local playerGui = player:WaitForChild("PlayerGui")

-- Crear GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ProHub"
screenGui.ResetOnSpawn = false -- para que no desaparezca al respawnear
screenGui.Parent = playerGui

-- Crear el marco del hub
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 180)
frame.Position = UDim2.new(0, 20, 0, 200)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Parent = screenGui

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Text = "HUB PRO"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20
title.Parent = frame

-- Función rápida para crear botones
local function makeButton(name, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, yPos)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSans
    btn.TextSize = 18
    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    btn.Parent = frame
    btn.MouseButton1Click:Connect(callback)
    return btn
end

---------------------------------------------------------
-- TI: Escudo (Inmunidad)
---------------------------------------------------------
local shieldOn = false
makeButton("TI (Escudo)", 40, function()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end

    shieldOn = not shieldOn
    if shieldOn then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
        print("✅ Escudo activado")
    else
        humanoid.MaxHealth = 100
        humanoid.Health = 100
        print("❌ Escudo desactivado")
    end
end)

---------------------------------------------------------
-- TT: Kill Aura
---------------------------------------------------------
local auraOn = false
makeButton("TT (Kill Aura)", 90, function()
    auraOn = not auraOn
    print("Kill Aura:", auraOn and "✅ Activada" or "❌ Desactivada")

    if auraOn then
        task.spawn(function()
            while auraOn do
                local char = player.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    for _, plr in pairs(Players:GetPlayers()) do
                        if plr ~= player and plr.Character then
                            local thrp = plr.Character:FindFirstChild("HumanoidRootPart")
                            local thum = plr.Character:FindFirstChildOfClass("Humanoid")
                            if thrp and thum and (hrp.Position - thrp.Position).Magnitude < 10 then
                                thum.Health = 0
                            end
                        end
                    end
                end
                task.wait(0.5)
            end
        end)
    end
end)

---------------------------------------------------------
-- TAS: NoClip (Traspasar paredes)
---------------------------------------------------------
local noclipOn = false
makeButton("TAS (NoClip)", 140, function()
    noclipOn = not noclipOn
    print("NoClip:", noclipOn and "✅ Activado" or "❌ Desactivado")

    if noclipOn then
        task.spawn(function()
            while noclipOn do
                local char = player.Character or player.CharacterAdded:Wait()
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and part.CanCollide then
                        part.CanCollide = false
                    end
                end
                task.wait(0.1)
            end
        end)
    else
        local char = player.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end)
