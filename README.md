# Silvaxxx-hub
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local mouse = LocalPlayer:GetMouse()

local aimbotEnabled = false
local teiaEnabled = false
local indoParaBase = false

game.StarterGui:SetCore("SendNotification", {
    Title = "Silvaxx Hub ativado!",
    Text = "Tudo pronto, bora lá!",
    Duration = 5
})

local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "SilvaxxHub"
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 300, 0, 440)
Frame.Position = UDim2.new(0, 10, 0, 10)
Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Frame.Active = true
Frame.Draggable = true
Instance.new("UICorner", Frame)

local function createButton(text, posY, callback)
    local btn = Instance.new("TextButton", Frame)
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, posY)
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 16
    btn.AutoButtonColor = true
    btn.MouseButton1Click:Connect(callback)
end

local function invisibilizar(obj)
    for _, part in ipairs(obj:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            part.Transparency = 1
            part.CanCollide = false
            local decal = part:FindFirstChildOfClass("Decal")
            if decal then decal:Destroy() end
        elseif part:IsA("Decal") then
            part:Destroy()
        end
    end
end

createButton("🕵️ Stealth Mode", 45, function()
    local char = LocalPlayer.Character
    if not char then return end
    invisibilizar(char)
    for _, tool in ipairs(char:GetChildren()) do
        if tool:IsA("Tool") then invisibilizar(tool) end
    end
    game.StarterGui:SetCore("SendNotification", {
        Title = "Stealth ativado",
        Text = "Você + objetos invisíveis",
        Duration = 4
    })
end)

createButton("🛡️ Anti-Hit", 90, function()
    local conn
    conn = RunService.Stepped:Connect(function()
        local ch = LocalPlayer.Character
        if ch then
            for _, part in pairs(ch:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        else
            conn:Disconnect()
        end
    end)
    game.StarterGui:SetCore("SendNotification", {
        Title = "Anti‑Hit ativado",
        Text = "Você não toma dano físico",
        Duration = 4
    })
end)

createButton("📦 ESP Caixa Players", 135, function()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            if not p.Character:FindFirstChild("SilvaxxBox") then
                local box = Instance.new("BoxHandleAdornment", p.Character)
                box.Name = "SilvaxxBox"
                box.Adornee = p.Character.HumanoidRootPart
                box.Size = Vector3.new(4, 6, 2)
                box.Color3 = Color3.fromRGB(255, 0, 0)
                box.Transparency = 0.3
                box.AlwaysOnTop = true
            end
        end
    end
end)
createButton("🔴 ESP Nome Brainrots", 180, function()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and not Players:GetPlayerFromCharacter(obj) then
            if obj:FindFirstChild("Head") and not obj:FindFirstChild("ESPName") then
                local gui = Instance.new("BillboardGui", obj)
                gui.Name = "ESPName"
                gui.Adornee = obj.Head
                gui.Size = UDim2.new(0, 120, 0, 30)
                gui.AlwaysOnTop = true
                gui.StudsOffset = Vector3.new(0, 3, 0)

                local label = Instance.new("TextLabel", gui)
                label.Size = UDim2.new(1, 0, 1, 0)
                label.BackgroundTransparency = 1
                label.Text = "🧠 Brainrot"
                label.TextColor3 = Color3.new(1, 0, 0)
                label.Font = Enum.Font.GothamBold
                label.TextScaled = true
            end
        end
    end
end)

createButton("💰 Scan Bicos", 225, function()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("TextLabel") and tostring(obj.Text):find("$") then
            print("[Bico] "..obj.Text.." em "..obj.Parent:GetFullName())
        end
    end
end)

createButton("🚀 Melhor Bico", 270, function()
    local best, bestPos = 0, nil
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("TextLabel") then
            local val = tonumber(obj.Text:match("%$(%d+)"))
            if val and val > best then
                best = val
                if obj.Parent and obj.Parent:IsA("BasePart") then
                    bestPos = obj.Parent.Position
                elseif obj.Parent and obj.Parent:FindFirstChild("HumanoidRootPart") then
                    bestPos = obj.Parent.HumanoidRootPart.Position
                end
            end
        end
    end
    if bestPos then
        local hrp = Character:FindFirstChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(bestPos + Vector3.new(0, 3, 0))
    else
        game.StarterGui:SetCore("SendNotification", {
            Title = "Melhor Bico",
            Text = "Nenhum bico encontrado",
            Duration = 3
        })
    end
end)

createButton("🚁 Teleguiado Base (Toggle)", 315, function()
    indoParaBase = not indoParaBase
    if not indoParaBase then
        game.StarterGui:SetCore("SendNotification", {
            Title = "Cancelado",
            Text = "Viagem abortada",
            Duration = 3
        })
        return
    end
    local base = workspace:FindFirstChild("sua base")
    if not base then
        game.StarterGui:SetCore("SendNotification", {
            Title = "Erro",
            Text = "Base não encontrada",
            Duration = 3
        })
        indoParaBase = false
        return
    end
    local pos = base.GetModelCFrame and base:GetModelCFrame().p or base.Position
    pos = pos + Vector3.new(0, 5, 0)
    game.StarterGui:SetCore("SendNotification", {
        Title = "A caminho da base",
        Text = "Flutuando até lá",
        Duration = 3
    })
    for _, part in pairs(Character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
    task.spawn(function()
        local hrp = Character:FindFirstChild("HumanoidRootPart")
        while indoParaBase and hrp and (hrp.Position - pos).Magnitude > 5 do
            hrp.CFrame = hrp.CFrame:Lerp(CFrame.new(pos), 0.1)
            task.wait(0.05)
        end
        if hrp then hrp.CFrame = CFrame.new(pos + Vector3.new(0, 2, 0)) end
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = true end
        end
        indoParaBase = false
        game.StarterGui:SetCore("SendNotification", {
            Title = "Chegou!",
            Text = "Você está na base",
            Duration = 3
        })
    end)
end)
createButton("🎯 Aimbot + Teia", 360, function()
    aimbotEnabled = not aimbotEnabled
    teiaEnabled = aimbotEnabled -- ativa/desativa teia junto
    game.StarterGui:SetCore("SendNotification", {
        Title = aimbotEnabled and "Aimbot ativado" or "Aimbot desativado",
        Text = "Use qualquer arma para mira automática",
        Duration = 3
    })
end)

RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local closestDist = math.huge
        local targetHRP = nil
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local hrp = p.Character:FindFirstChild("HumanoidRootPart")
                local humanoid = p.Character:FindFirstChild("Humanoid")
                if hrp and humanoid and humanoid.Health > 0 then
                    local dist = (hrp.Position - Character.HumanoidRootPart.Position).Magnitude
                    if dist < closestDist then
                        closestDist = dist
                        targetHRP = hrp
                    end
                end
            end
        end
        if targetHRP then
            local direction = (targetHRP.Position - Character.HumanoidRootPart.Position).Unit
            -- Ajusta o mouse.Hit para mirar no player
            mouse.Hit = CFrame.new(Character.HumanoidRootPart.Position + direction * 5, targetHRP.Position)

            -- Aqui você pode adicionar funcionalidade da teia (exemplo: lançar objeto que prende o player)
            -- Depende do jogo, então fica aberto para você implementar
        end
    end
end)

createButton("❌ Fechar Menu", 405, function()
    ScreenGui:Destroy()
end)
