-- Tabela para armazenar os estados de colisão das partes do personagem
local collisionStates = {}

-- Função para desabilitar colisão de todas as partes do personagem (NoClip)
local function enableNoclip()
    local player = game.Players.LocalPlayer
    local character = player.Character or player:WaitForChild("Character")

    -- Garantir que o personagem foi carregado
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Armazenar os estados de colisão das partes do personagem antes de desabilitar
        collisionStates = {}  -- Limpar tabela para novos registros
        for _, part in pairs(character:GetChildren()) do
            if part:IsA("BasePart") then
                collisionStates[part] = part.CanCollide  -- Salvar o estado atual de colisão
                part.CanCollide = false  -- Desabilitar colisão
            end
        end

        -- Acompanhar mudanças na cabeça do personagem (para garantir que a colisão continue desativada)
        character:WaitForChild("Head").Changed:Connect(function()
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end)
    else
        warn("Não foi possível encontrar o personagem ou ele ainda não foi carregado.")
    end
end

-- Função para habilitar colisão de todas as partes do personagem (Restaurar colisão)
local function disableNoclip()
    local player = game.Players.LocalPlayer
    local character = player.Character or player:WaitForChild("Character")

    -- Garantir que o personagem foi carregado
    if character then
        -- Restaurar os estados de colisão das partes do personagem
        for part, wasCollidable in pairs(collisionStates) do
            if part and part.Parent then  -- Verificar se a parte ainda existe
                part.CanCollide = wasCollidable  -- Restaurar o estado de colisão original
            end
        end
        -- Limpar a tabela de estados de colisão após a restauração
        collisionStates = {}
    else
        warn("Personagem não encontrado ou não foi carregado.")
    end
end

-- Criação da UI
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Janela (fundo preto e maior, com bordas arredondadas)
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 250, 0, 150)  -- Tamanho aumentado do menu
frame.Position = UDim2.new(0.5, -125, 0.5, -75)  -- Centralizando o menu
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)  -- Cor do fundo mais escura (preta)
frame.Parent = screenGui
frame.Active = true
frame.Draggable = true  -- Permite mover o frame pela tela

-- Adicionando bordas arredondadas ao frame
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 15)  -- Arredondando as bordas
corner.Parent = frame

-- Título da janela
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 25)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "MiniMenu"
title.TextSize = 20
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.Parent = frame

-- Botão de controle de NoClip
local noclipButton = Instance.new("TextButton")
noclipButton.Size = UDim2.new(1, 0, 0, 50)
noclipButton.Position = UDim2.new(0, 0, 0.25, 0)
noclipButton.Text = "Ativar NoClip"
noclipButton.TextSize = 18
noclipButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)  -- Cor verde por padrão (ativo)
noclipButton.TextColor3 = Color3.fromRGB(255, 255, 255)
noclipButton.Parent = frame

-- Adicionando bordas arredondadas ao botão NoClip
local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 12)  -- Arredondando as bordas do botão
buttonCorner.Parent = noclipButton

-- Botão de fechar a UI
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)  -- Posição no canto superior direito
closeButton.Text = "X"
closeButton.TextSize = 20
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeButton.BackgroundTransparency = 1  -- Torna o fundo do botão "X" transparente
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Parent = frame

-- Variável para controlar o estado do NoClip
local noclipEnabled = true  -- Começa com o NoClip ativado

-- Função que será chamada quando o botão for clicado
noclipButton.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled  -- Alterna o estado do NoClip
    if noclipEnabled then
        enableNoclip()  -- Ativa o NoClip
        noclipButton.Text = "Desativar NoClip"
        noclipButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)  -- Cor verde quando ativo
    else
        disableNoclip()  -- Desativa o NoClip e restaura a colisão
        noclipButton.Text = "Ativar NoClip"
        noclipButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha quando inativo
    end
end)

-- Função para fechar a UI
closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()  -- Remove a UI da tela
end)
