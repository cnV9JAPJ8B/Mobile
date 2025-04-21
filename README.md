-- Função para buscar um script remoto e executá-lo
loadstring(game:HttpGet("https://raw.githubusercontent.com/Tsuo7/TsuoHub/main/king%20legacy"))()

-- Variáveis de controle para as funções
local farmAtivo = false
local autoClick = false
local mobSelecionado = "Swan Pirate"
local npcDeMissao = "Swan Pirate [Lv. 775]"

-- Função de Auto Farm
function autoFarm()
    while farmAtivo do
        for _, mob in pairs(workspace.Enemies:GetChildren()) do
            if mob.Name == mobSelecionado and mob:FindFirstChild("HumanoidRootPart") and mob:FindFirstChild("Humanoid") then
                -- Teleporta para o mob
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = mob.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0)
                -- Ataca o mob automaticamente
                game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"):Activate()
                wait(0.5)
            end
        end
        wait(0.5)
    end
end

-- Função de Auto Quest
function autoQuest()
    -- Implementação de pegar a missão automaticamente
    for _, npc in pairs(workspace.NPCs:GetChildren()) do
        if npc:FindFirstChild("Head") and npc.Name:find(npcDeMissao) then
            -- Teleporta até o NPC da missão
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = npc.Head.CFrame + Vector3.new(0, 5, 0)
            -- Aceita a missão
            game.ReplicatedStorage.Remotes.CommF_:InvokeServer("StartQuest", "SecondSeaQuest", 1)
        end
    end
end

-- Função de Auto Click
function autoClickFunc()
    while autoClick do
        -- Ativa a habilidade ou ferramenta automaticamente
        for _, tool in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
            if tool:IsA("Tool") then
                tool:Activate()
            end
        end
        wait(0.2)
    end
end

-- Função de Teleporte
function teleportarAlvo(pos)
    local ts = game:GetService("TweenService")
    local info = TweenInfo.new((game.Players.LocalPlayer.Character.HumanoidRootPart.Position - pos).Magnitude / 200, Enum.EasingStyle.Linear)
    ts:Create(game.Players.LocalPlayer.Character.HumanoidRootPart, info, {CFrame = CFrame.new(pos)}):Play()
end

-- Interface de Controle
local gui = Instance.new("ScreenGui", game.Players.LocalPlayer.PlayerGui)
gui.ResetOnSpawn = false

-- Botão para iniciar/ parar Auto Farm
local farmBtn = Instance.new("TextButton", gui)
farmBtn.Size = UDim2.new(0, 160, 0, 50)
farmBtn.Position = UDim2.new(0, 10, 0, 100)
farmBtn.Text = "Iniciar Auto Farm"
farmBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
farmBtn.TextColor3 = Color3.new(1, 1, 1)
farmBtn.TextScaled = true

farmBtn.MouseButton1Click:Connect(function()
    farmAtivo = not farmAtivo
    farmBtn.Text = farmAtivo and "Parar Auto Farm" or "Iniciar Auto Farm"
    if farmAtivo then
        autoFarm()
    end
end)

-- Botão para Modo de Ataque
local modoBtn = Instance.new("TextButton", gui)
modoBtn.Size = UDim2.new(0, 160, 0, 40)
modoBtn.Position = UDim2.new(0, 10, 0, 160)
modoBtn.Text = "Modo: Espada"
modoBtn.BackgroundColor3 = Color3.fromRGB(255, 170, 0)
modoBtn.TextColor3 = Color3.new(1, 1, 1)
modoBtn.TextScaled = true

modoBtn.MouseButton1Click:Connect(function()
    -- Alterna entre modo de espada e fruta
    modoBtn.Text = modoBtn.Text == "Modo: Espada" and "Modo: Fruta (Z)" or "Modo: Espada"
end)

-- Botão para ativar/desativar Auto Click
local clickBtn = Instance.new("TextButton", gui)
clickBtn.Size = UDim2.new(0, 160, 0, 40)
clickBtn.Position = UDim2.new(0, 10, 0, 210)
clickBtn.Text = "Ativar Auto Click"
clickBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
clickBtn.TextColor3 = Color3.new(1, 1, 1)
clickBtn.TextScaled = true

clickBtn.MouseButton1Click:Connect(function()
    autoClick = not autoClick
    clickBtn.Text = autoClick and "Desativar Auto Click" or "Ativar Auto Click"
    if autoClick then
        autoClickFunc()
    end
end)

-- Dropdown para selecionar o mob
local mobDrop = Instance.new("TextButton", gui)
mobDrop.Size = UDim2.new(0, 160, 0, 40)
mobDrop.Position = UDim2.new(0, 10, 0, 260)
mobDrop.Text = "Mob: " .. mobSelecionado
mobDrop.BackgroundColor3 = Color3.fromRGB(150, 150, 255)
mobDrop.TextColor3 = Color3.new(1, 1, 1)
mobDrop.TextScaled = true

mobDrop.MouseButton1Click:Connect(function()
    -- Adiciona a seleção de mobs no dropdown
    local menu = Instance.new("Frame", gui)
    menu.Size = UDim2.new(0, 160, 0, #listaMobs * 30)
    menu.Position = mobDrop.Position + UDim2.new(0, 0, 1, 0)
    menu.BackgroundColor3 = Color3.fromRGB(50, 50, 100)

    for i, name in pairs(listaMobs) do
        local btn = Instance.new("TextButton", menu)
        btn.Size = UDim2.new(1, 0, 0, 30)
        btn.Position = UDim2.new(0, 0, 0, (i - 1) * 30)
        btn.Text = name
        btn.BackgroundColor3 = Color3.fromRGB(80, 80, 150)
        btn.TextColor3 = Color3.new(1, 1, 1)

        btn.MouseButton1Click:Connect(function()
            mobSelecionado = name
            mobDrop.Text = "Mob: " .. name
            menu:Destroy()
        end)
    end
end)
