-- Script Unificado (coloque em ServerScriptService)
local replicatedStorage = game:GetService("ReplicatedStorage")
local players = game:GetService("Players")

-- Criar os eventos remotos
local cloneUnitEvent = Instance.new("RemoteEvent")
cloneUnitEvent.Name = "CloneUnitEvent"
cloneUnitEvent.Parent = replicatedStorage

local trocaEvent = Instance.new("RemoteEvent")
trocaEvent.Name = "TrocaEvent"
trocaEvent.Parent = replicatedStorage

-- Função para clonar a unidade
local function cloneUnit(player)
    -- Exemplo de unidade para clonar
    local unitToClone = game.ServerStorage:FindFirstChild("TowerUnit")  -- A unidade deve estar em ServerStorage com o nome "TowerUnit"

    if unitToClone then
        local clonedUnit = unitToClone:Clone()
        clonedUnit.Parent = game.Workspace

        -- Posicionando a unidade clonada perto do jogador (exemplo de posição)
        clonedUnit:SetPrimaryPartCFrame(player.Character.HumanoidRootPart.CFrame * CFrame.new(5, 0, 0))

        -- Enviar a clonagem para todos os jogadores (incluindo quem fez)
        for _, otherPlayer in pairs(players:GetPlayers()) do
            -- A unidade clonada vai aparecer para todos os jogadores
            clonedUnit:Clone().Parent = otherPlayer.Character
        end
    end
end

-- Função para quando a troca é realizada
local function handleTroca(player)
    -- Aqui você pode colocar a lógica para verificar se a troca foi aceita e validada.
    -- Quando a troca for confirmada, disparamos o evento para todos os jogadores.
    
    -- Dispara o evento para o cliente que recebeu a troca e para o jogador original
    trocaEvent:FireClient(player) -- Isso envia para o jogador que está trocando
end

-- Conectar o evento remoto de clonagem
cloneUnitEvent.OnServerEvent:Connect(function(player)
    cloneUnit(player)  -- Chama a função de clonagem para o jogador
end)

-- Lógica de exemplo para quando a troca é aceita
players.PlayerAdded:Connect(function(player)
    -- Aqui você pode usar algum sistema de troca existente ou criar um novo
    -- Para o exemplo, vamos simular que a troca foi aceita por um jogador específico

    -- Ao aceitar a troca, acionamos o evento de troca
    -- Isso poderia ser feito quando algum tipo de interação de troca for feita no jogo
    wait(5) -- Espera 5 segundos para simular uma troca
    handleTroca(player)  -- Dispara a troca para esse jogador
end)

-- LocalScript (para interação do cliente)
players.PlayerAdded:Connect(function(player)
    -- Quando o jogador entrar, adicionamos o LocalScript para a interação com o cliente
    local playerGui = player:WaitForChild("PlayerGui")

    -- Criando a interface do menu
    local menuFrame = Instance.new("Frame")
    menuFrame.Size = UDim2.new(0, 300, 0, 200)
    menuFrame.Position = UDim2.new(0.5, -150, 0.5, -100)
    menuFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    menuFrame.BackgroundTransparency = 0.5
    menuFrame.Visible = false  -- Começa invisível
    menuFrame.Parent = playerGui

    local cloneButton = Instance.new("TextButton")
    cloneButton.Size = UDim2.new(0, 100, 0, 50)
    cloneButton.Position = UDim2.new(0.5, -50, 0.5, -25)
    cloneButton.Text = "Clonar Unidade"
    cloneButton.Parent = menuFrame

    local UserInputService = game:GetService("UserInputService")

    -- Função para alternar a visibilidade do menu
    local function toggleMenu()
        menuFrame.Visible = not menuFrame.Visible
    end

    -- Detectar quando o jogador pressiona a tecla "M" para abrir o menu
    UserInputService.InputBegan:Connect(function(input)
        if input.KeyCode == Enum.KeyCode.M then
            toggleMenu()
        end
    end)

    -- Quando o botão de clonar for pressionado
    cloneButton.MouseButton1Click:Connect(function()
        -- Aciona o evento remoto para clonar a unidade
        cloneUnitEvent:FireServer()  -- Dispara no servidor
    end)

    -- Quando a troca é confirmada, abre a interface e permite a clonagem
    trocaEvent.OnClientEvent:Connect(function()
        -- Aqui você pode adicionar a lógica para a interação com a troca
        -- E fazer com que o jogador consiga visualizar a unidade clonada
    end)
end)
