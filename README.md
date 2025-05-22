--// HUNSFUCK V5 ULTRA ALPHA by ogart (A INTERFACE DEFINITIVA!)
--// Créditos: ogart | Script 100% funcional no Delta Executor

local keyCorreta = "byogart" -- 🔑 A chave para o poder!

-- Cores Premium e Design Sofisticado
local CORES = {
    Primaria = Color3.fromRGB(180, 0, 0), -- Vermelho Profundo
    Secundaria = Color3.fromRGB(30, 0, 0), -- Vinho Muito Escuro
    FundoUI = Color3.fromRGB(20, 20, 20), -- Quase preto para o fundo geral
    FundoComponente = Color3.fromRGB(40, 40, 40), -- Cinza escuro para componentes
    BordaClara = Color3.fromRGB(80, 80, 80),
    TextoClaro = Color3.fromRGB(240, 240, 240),
    TextoEscuro = Color3.fromRGB(180, 180, 180),
    Sucesso = Color3.fromRGB(0, 180, 0), -- Verde Vibrante
    Erro = Color3.fromRGB(255, 50, 50), -- Vermelho Erro
    ToggleON = Color3.fromRGB(0, 150, 0),
    ToggleOFF = Color3.fromRGB(150, 0, 0),
}

-- Áudios do TROLL EXTREMO (IDs de áudio públicos do Roblox)
local AUDIO_IDS = {
    gemidao = 5580009623, -- Exemplo de ID de áudio (substitua por um real se necessário)
    susto = 131976071,     -- Exemplo de ID de áudio
    aplausos = 143521366,  -- Exemplo de ID de áudio
    alerta = 139194218     -- Exemplo de ID de áudio
}

-- Variáveis globais
local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local CollectionService = game:GetService("CollectionService")

-- Salvamento de Configurações (Simples para execuções únicas)
local settings = {}

-- Função para carregar configurações (placeholder)
local function loadSettings()
    settings.noclip = false
    settings.fly = false
    settings.invisible = false
    settings.stealthMode = false
    -- Hotkeys default
    settings.hotkeys = {
        fly = Enum.KeyCode.F,
        noclip = Enum.KeyCode.G,
        invisible = Enum.KeyCode.H,
        minimizar = Enum.KeyCode.M,
        -- Adicione mais hotkeys aqui
    }
end
loadSettings()

-- Notificações visuais animadas
local function showNotification(text, color, duration)
    -- Remove notificações antigas para evitar acúmulo
    for _, notif in pairs(game.CoreGui:GetChildren()) do
        if notif.Name == "HunsfuckNotification" then
            notif:Destroy()
        end
    end

    duration = duration or 3
    local NotificationFrame = Instance.new("Frame")
    NotificationFrame.Size = UDim2.new(0.7, 0, 0, 60) -- Ajustado para escala
    NotificationFrame.Position = UDim2.new(0.5, 0, 1, 0) -- Começa fora da tela, embaixo, centralizado por AnchorPoint
    NotificationFrame.AnchorPoint = Vector2.new(0.5, 1) -- Ancoragem na parte inferior central
    NotificationFrame.BackgroundColor3 = color
    NotificationFrame.BorderSizePixel = 0
    NotificationFrame.Parent = game.CoreGui
    NotificationFrame.ZIndex = 999
    NotificationFrame.ClipsDescendants = true
    NotificationFrame.Name = "HunsfuckNotification"
    NotificationFrame.CornerRadius = UDim.new(0, 8)
    
    -- Adiciona uma sombra sutil
    local uiStroke = Instance.new("UIStroke")
    uiStroke.Color = Color3.fromRGB(0,0,0)
    uiStroke.Transparency = 0.5
    uiStroke.Thickness = 2
    uiStroke.Parent = NotificationFrame

    local NotifText = Instance.new("TextLabel")
    NotifText.Size = UDim2.new(1, 0, 1, 0)
    NotifText.BackgroundColor3 = Color3.new(1,1,1)
    NotifText.BackgroundTransparency = 1
    NotifText.TextColor3 = CORES.TextoClaro
    NotifText.Font = Enum.Font.SourceSansBold
    NotifText.TextSize = 20
    NotifText.TextWrapped = true
    NotifText.TextXAlignment = Enum.TextXAlignment.Center
    NotifText.TextYAlignment = Enum.TextYAlignment.Center
    NotifText.Text = text
    NotifText.Parent = NotificationFrame

    local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
    local goalIn = {Position = UDim2.new(0.5, 0, 1, -70)} -- Sobe para a posição visível

    local tweenIn = TweenService:Create(NotificationFrame, tweenInfo, goalIn)
    tweenIn:Play()
    tweenIn.Completed:Wait()

    task.wait(duration)

    local goalOut = {Position = UDim2.new(0.5, 0, 1, 0), BackgroundTransparency = 1}
    local textGoalOut = {TextTransparency = 1}

    local tweenOut = TweenService:Create(NotificationFrame, tweenInfo, goalOut)
    local tweenTextOut = TweenService:Create(NotifText, tweenInfo, textGoalOut)

    tweenOut:Play()
    tweenTextOut:Play()
    tweenOut.Completed:Wait()

    NotificationFrame:Destroy()
end

-- Logs locais (simples para depuração no console)
local function logMessage(message, level)
    level = level or "INFO"
    local prefix = ""
    if level == "INFO" then
        prefix = "[✨ INFO]"
    elseif level == "WARNING" then
        prefix = "[⚠️ AVISO]"
    elseif level == "ERROR" then
        prefix = "[❌ ERRO]"
    end
    print(prefix .. " " .. message)
end

-- UI PRINCIPAL 💎
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HunsfuckV5ALPHA_UI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui
ScreenGui.Enabled = true -- Garante que o ScreenGui está habilitado

-- Adiciona UIScale para responsividade global
local uiScale = Instance.new("UIScale")
uiScale.Scale = 0.9 -- Ajusta a escala da UI para telas menores
uiScale.Parent = ScreenGui

-- Key Frame 🔑
local keyFrame = Instance.new("Frame")
keyFrame.Size = UDim2.new(0.7, 0, 0.45, 0) -- Ajustado para escala
keyFrame.Position = UDim2.new(0.5, 0, 0.5, 0) -- Centralizado
keyFrame.AnchorPoint = Vector2.new(0.5, 0.5)
keyFrame.BackgroundColor3 = CORES.FundoUI
keyFrame.BorderSizePixel = 0
keyFrame.Parent = ScreenGui
keyFrame.ClipsDescendants = true
keyFrame.Visible = true
keyFrame.CornerRadius = UDim.new(0, 12)
keyFrame.ZIndex = 10 -- Alto o suficiente para aparecer

-- Mantém a proporção do Key Frame
local keyFrameAspectRatio = Instance.new("UIAspectRatioConstraint")
keyFrameAspectRatio.AspectRatio = 400 / 220 -- Proporção original (largura / altura)
keyFrameAspectRatio.AspectType = Enum.AspectType.FitWithoutStretching
keyFrameAspectRatio.DominantAxis = Enum.DominantAxis.Width
keyFrameAspectRatio.Parent = keyFrame

local uiStrokeKey = Instance.new("UIStroke")
uiStrokeKey.Color = CORES.BordaClara
uiStrokeKey.Transparency = 0.8
uiStrokeKey.Thickness = 1
uiStrokeKey.Parent = keyFrame

local tituloKey = Instance.new("TextLabel")
tituloKey.Size = UDim2.new(1, 0, 0.3, 0) -- 30% da altura do pai
tituloKey.Position = UDim2.new(0, 0, 0, 0)
tituloKey.BackgroundColor3 = CORES.Primaria
tituloKey.TextColor3 = CORES.TextoClaro
tituloKey.Font = Enum.Font.SourceSansBold
tituloKey.TextSize = 30 -- Ajustado para mobile
tituloKey.Text = "💀 HUNSFUCK 💀"
tituloKey.Parent = keyFrame
tituloKey.ZIndex = 11

local caixaKey = Instance.new("TextBox")
caixaKey.Size = UDim2.new(0.85, 0, 0.18, 0) -- 18% da altura do pai
caixaKey.Position = UDim2.new(0.075, 0, 0.45, 0)
caixaKey.PlaceholderText = "🔑 Digite a chave secreta..."
caixaKey.Text = ""
caixaKey.BackgroundColor3 = CORES.FundoComponente
caixaKey.TextColor3 = CORES.TextoClaro
caixaKey.Font = Enum.Font.SourceSans
caixaKey.TextSize = 20 -- Ajustado
caixaKey.BorderSizePixel = 0
caixaKey.Parent = keyFrame
caixaKey.CornerRadius = UDim.new(0, 6)
caixaKey.ZIndex = 11

local botaoConfirmar = Instance.new("TextButton")
botaoConfirmar.Size = UDim2.new(0.65, 0, 0.22, 0) -- 22% da altura do pai
botaoConfirmar.Position = UDim2.new(0.175, 0, 0.75, 0)
botaoConfirmar.Text = "🚀 Confirmar Acesso 🚀"
botaoConfirmar.Font = Enum.Font.SourceSansBold
botaoConfirmar.TextColor3 = CORES.TextoClaro
botaoConfirmar.BackgroundColor3 = CORES.Sucesso
botaoConfirmar.BorderSizePixel = 0
botaoConfirmar.Parent = keyFrame
botaoConfirmar.CornerRadius = UDim.new(0, 10)
botaoConfirmar.ZIndex = 11

local creditosKey = Instance.new("TextLabel")
creditosKey.Size = UDim2.new(1, 0, 0.1, 0) -- 10% da altura do pai
creditosKey.Position = UDim2.new(0, 0, 0.9, 0) -- Ajustado para base
creditosKey.Text = "✨ Créditos: ogart - A Lenda! ✨"
creditosKey.TextColor3 = CORES.Sucesso
creditosKey.BackgroundTransparency = 1
creditosKey.Font = Enum.Font.SourceSansItalic
creditosKey.TextSize = 18 -- Ajustado
creditosKey.Parent = keyFrame
creditosKey.ZIndex = 11

-- Painel Principal ULTRA ALPHA 🚀
local painel = Instance.new("Frame")
painel.Size = UDim2.new(0.9, 0, 0.8, 0) -- Tamanho inicial em escala (90% largura, 80% altura)
painel.Position = UDim2.new(0.5, 0, 0.5, 0)
painel.AnchorPoint = Vector2.new(0.5, 0.5)
painel.BackgroundColor3 = CORES.FundoUI
painel.BorderSizePixel = 0
painel.Visible = false
painel.Parent = ScreenGui
painel.ClipsDescendants = true
painel.CornerRadius = UDim.new(0, 15)
painel.ZIndex = 5 -- ZIndex padrão para o painel principal

-- Mantém a proporção do Painel Principal
local painelAspectRatio = Instance.new("UIAspectRatioConstraint")
painelAspectRatio.AspectRatio = 550 / 550 -- Proporção original (1:1)
painelAspectRatio.AspectType = Enum.AspectType.FitWithoutStretching
painelAspectRatio.DominantAxis = Enum.DominantAxis.Height -- Tenta manter a altura dominante
painelAspectRatio.Parent = painel

local uiStrokePainel = Instance.new("UIStroke")
uiStrokePainel.Color = CORES.BordaClara
uiStrokePainel.Transparency = 0.7
uiStrokePainel.Thickness = 1
uiStrokePainel.Parent = painel

-- Título painel
local tituloPainel = Instance.new("TextLabel")
tituloPainel.Size = UDim2.new(1, 0, 0.1, 0) -- 10% da altura do painel
tituloPainel.BackgroundColor3 = CORES.Primaria
tituloPainel.TextColor3 = CORES.TextoClaro
tituloPainel.Font = Enum.Font.SourceSansBold
tituloPainel.TextSize = 28 -- Ajustado
tituloPainel.Text = "💀 HUNSFUCK 💀"
tituloPainel.Parent = painel
tituloPainel.ZIndex = 6

-- Botão minimizar/maximizar (dentro do painel)
local minimizarBtn = Instance.new("TextButton")
minimizarBtn.Size = UDim2.new(0, 45, 0, 45) -- Tamanho fixo, pois é um ícone
minimizarBtn.Position = UDim2.new(1, -50, 0, 7)
minimizarBtn.Text = "➖"
minimizarBtn.BackgroundColor3 = CORES.Secundaria
minimizarBtn.TextColor3 = CORES.TextoClaro
minimizarBtn.Font = Enum.Font.SourceSansBold
minimizarBtn.TextSize = 30
minimizarBtn.Parent = painel
minimizarBtn.CornerRadius = UDim.new(0, 6)
minimizarBtn.ZIndex = 7

local painelAberto = true
local originalSize = painel.Size
local minimizedSize = UDim2.new(painel.Size.X.Scale, painel.Size.X.Offset, 0.1, 0) -- Altura apenas do título (10% do pai)

minimizarBtn.MouseButton1Click:Connect(function()
    painelAberto = not painelAberto
    local targetSize = painelAberto and originalSize or minimizedSize
    local targetText = painelAberto and "➖" or "➕"

    local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    TweenService:Create(painel, tweenInfo, {Size = targetSize}):Play()
    minimizarBtn.Text = targetText

    if painelAberto then
        task.wait(0.1) -- Pequeno delay para a animação do painel
        for _, v in pairs(painel:GetChildren()) do
            if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") or v:IsA("ScrollingFrame") or v:IsA("TextLabel") then
                if v ~= minimizarBtn and v ~= tituloPainel then
                    v.Visible = true
                end
            end
        end
    else
        for _, v in pairs(painel:GetChildren()) do
            if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") or v:IsA("ScrollingFrame") or v:IsA("TextLabel") then
                if v ~= minimizarBtn and v ~= tituloPainel then
                    v.Visible = false
                end
            end
        end
    end
end)

-- Botão Flutuante (Floating Button) 🌀
local floatingButton = Instance.new("TextButton")
floatingButton.Name = "FloatingButton"
floatingButton.Size = UDim2.new(0, 60, 0, 60)
floatingButton.Position = UDim2.new(0.5, 0, 0.95, 0) -- Centralizado na parte inferior
floatingButton.AnchorPoint = Vector2.new(0.5, 0.5)
floatingButton.BackgroundColor3 = CORES.Primaria
floatingButton.TextColor3 = CORES.TextoClaro
floatingButton.Font = Enum.Font.SourceSansBold
floatingButton.TextSize = 36
floatingButton.Text = "💡" -- Emoji de lâmpada ou ícone de menu
floatingButton.Parent = ScreenGui
floatingButton.CornerRadius = UDim.new(0.5, 0) -- Botão redondo
floatingButton.Visible = true -- Visível inicialmente após o login
floatingButton.ZIndex = 8

local uiStrokeFloating = Instance.new("UIStroke")
uiStrokeFloating.Color = CORES.FundoComponente
uiStrokeFloating.Transparency = 0.2
uiStrokeFloating.Thickness = 2
uiStrokeFloating.Parent = floatingButton

local panelIsVisible = false -- Estado do painel (para o botão flutuante)

floatingButton.MouseButton1Click:Connect(function()
    panelIsVisible = not panelIsVisible
    
    if panelIsVisible then
        painel.Visible = true
        painel.BackgroundTransparency = 1 -- Começa transparente
        -- Não tornar todos os filhos transparentes aqui, a animação fará isso gradualmente
        
        local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        TweenService:Create(painel, tweenInfo, {BackgroundTransparency = 0}):Play()
        -- Animação para os filhos aparecerem gradualmente
        task.wait(0.05) -- Pequeno delay para a animação do pai
        for _, v in pairs(painel:GetChildren()) do
            if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") or v:IsA("ScrollingFrame") or v:IsA("TextLabel") then
                TweenService:Create(v, tweenInfo, {BackgroundTransparency = (v.BackgroundTransparency == 1 and 1 or 0), TextTransparency = 0}):Play()
            end
        end
        floatingButton.Text = "❌" -- Mudar ícone para fechar
        showNotification("Painel ABERTO! 👁️", CORES.Sucesso, 2)
    else
        local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        -- Animar todos os filhos para desaparecerem gradualmente
        for _, v in pairs(painel:GetChildren()) do
            if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") or v:IsA("ScrollingFrame") or v:IsA("TextLabel") then
                TweenService:Create(v, tweenInfo, {BackgroundTransparency = 1, TextTransparency = 1}):Play()
            end
        end
        
        task.wait(0.2) -- Espera um pouco para a animação dos filhos começar
        TweenService:Create(painel, tweenInfo, {BackgroundTransparency = 1}):Play()
        
        task.wait(0.3) -- Espera a animação de fade out
        painel.Visible = false
        floatingButton.Text = "💡" -- Mudar ícone para abrir
        showNotification("Painel FECHADO! 🚫", CORES.Erro, 2)
    end
end)


-- Menu Lateral (Abas) 📁
local menuLateral = Instance.new("Frame")
menuLateral.Size = UDim2.new(0.25, 0, 0.9, 0) -- 25% da largura do painel, 90% da altura (resto para título)
menuLateral.Position = UDim2.new(0, 0, 0.1, 0) -- Abaixo do título
menuLateral.BackgroundColor3 = CORES.Secundaria
menuLateral.BorderSizePixel = 0
menuLateral.Parent = painel
menuLateral.CornerRadius = UDim.new(0, 8)
local menuLayout = Instance.new("UIListLayout")
menuLayout.Parent = menuLateral
menuLayout.SortOrder = Enum.SortOrder.LayoutOrder
menuLayout.Padding = UDim.new(0, 8) -- Mais espaçamento entre botões
menuLayout.FillDirection = Enum.FillDirection.Vertical
menuLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
menuLayout.VerticalAlignment = Enum.VerticalAlignment.Top


-- Container de Páginas (onde o conteúdo das abas aparece)
local pageContainer = Instance.new("Frame")
pageContainer.Size = UDim2.new(0.75, 0, 0.9, 0) -- Resto da largura, mesma altura do menu
pageContainer.Position = UDim2.new(0.25, 0, 0.1, 0)
pageContainer.BackgroundColor3 = CORES.FundoUI
pageContainer.BorderSizePixel = 0
pageContainer.Parent = painel
pageContainer.ClipsDescendants = true
pageContainer.CornerRadius = UDim.new(0, 10)
pageContainer.ZIndex = 6

local activePage = nil

local function createTabPage(name, emoji)
    local page = Instance.new("ScrollingFrame")
    page.Name = name .. "Page"
    page.Size = UDim2.new(1, -10, 1, -10) -- Um pouco menor para margem interna
    page.Position = UDim2.new(0.01, 0, 0.01, 0)
    page.BackgroundColor3 = CORES.FundoComponente
    page.BackgroundTransparency = 0 -- Cada página terá seu próprio fundo
    page.BorderSizePixel = 0
    page.Parent = pageContainer
    page.Visible = false
    page.CanvasSize = UDim2.new(0, 0, 0, 0) -- Ajuste automático pelo UIListLayout/UIGridLayout
    page.ScrollBarThickness = 8
    page.ClipsDescendants = true
    page.CornerRadius = UDim.new(0, 8)
    page.ZIndex = 7

    local layout = Instance.new("UIListLayout")
    layout.Parent = page
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 12) -- Mais espaçamento interno
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.VerticalAlignment = Enum.VerticalAlignment.Top

    local tabButton = Instance.new("TextButton")
    tabButton.Size = UDim2.new(0.9, 0, 0, 45) -- Um pouco mais alto, em offset para tamanho consistente
    tabButton.Text = emoji .. " " .. name
    tabButton.Font = Enum.Font.SourceSansBold
    tabButton.TextColor3 = CORES.TextoClaro
    tabButton.TextSize = 20
    tabButton.BackgroundColor3 = CORES.FundoComponente
    tabButton.Parent = menuLateral
    tabButton.LayoutOrder = #menuLateral:GetChildren() -- Garante a ordem
    tabButton.CornerRadius = UDim.new(0, 8)
    tabButton.ZIndex = 7

    tabButton.MouseButton1Click:Connect(function()
        if activePage then
            activePage.Visible = false
            -- Resetar cor do botão ativo anterior
            for _, btn in pairs(menuLateral:GetChildren()) do
                if btn:IsA("TextButton") then
                    btn.BackgroundColor3 = CORES.FundoComponente
                end
            end
        end
        page.Visible = true
        activePage = page
        tabButton.BackgroundColor3 = CORES.Primaria -- Cor de destaque para aba ativa
    end)

    return page, tabButton
end

-- Criação das abas
local funcoesPage, funcoesBtn = createTabPage("Funções", "⚙️")
local teleportesPage, teleportesBtn = createTabPage("Teleportes", "📍")
local trollPage, trollBtn = createTabPage("Troll", "😈")
local hotkeysPage, hotkeysBtn = createTabPage("Hotkeys", "⌨️")
local configPage, configBtn = createTabPage("Config", "🛠️")

-- Ativar a primeira aba por padrão
funcoesBtn.MouseButton1Click:Fire() -- Simula um clique para abrir a primeira aba

-- Função para criar botões de toggle estilizados
local function criarBotaoToggle(nome, parent, emoji)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 55) -- Mais alto, largura em escala
    frame.BackgroundTransparency = 1
    frame.Parent = parent
    frame.Name = nome .. "ToggleFrame"
    frame.ZIndex = 8

    local btnAtivar = Instance.new("TextButton")
    btnAtivar.Size = UDim2.new(0.7, 0, 1, 0)
    btnAtivar.Position = UDim2.new(0, 0, 0, 0)
    btnAtivar.Text = emoji .. " " .. nome
    btnAtivar.Font = Enum.Font.SourceSansBold
    btnAtivar.TextColor3 = CORES.TextoClaro
    btnAtivar.TextSize = 20 -- Texto maior
    btnAtivar.BackgroundColor3 = CORES.FundoComponente
    btnAtivar.Name = "btnFunc"
    btnAtivar.Parent = frame
    btnAtivar.CornerRadius = UDim.new(0, 10)
    btnAtivar.ZIndex = 9

    local btnToggle = Instance.new("TextButton")
    btnToggle.Size = UDim2.new(0.28, 0, 0.8, 0) -- Ajustado para proporção
    btnToggle.Position = UDim2.new(0.72, 0, 0.1, 0)
    btnToggle.Text = "OFF 🔴"
    btnToggle.Font = Enum.Font.SourceSansBold
    btnToggle.TextColor3 = CORES.TextoClaro
    btnToggle.TextSize = 18 -- Ajustado
    btnToggle.BackgroundColor3 = CORES.ToggleOFF
    btnToggle.Name = "btnToggle"
    btnToggle.Parent = frame
    btnToggle.CornerRadius = UDim.new(0, 8)
    btnToggle.ZIndex = 9

    return frame, btnAtivar, btnToggle
end

-- Estados dos sistemas e suas funções
local bodyGyro, bodyVel
local flyConnection
local noclipConnection

-- Noclip 👻
local function noclipLoop()
    if not Character then return end
    for _, part in pairs(Character:GetDescendants()) do
        if part:IsA("BasePart") and part.CanCollide then
            part.CanCollide = false
        end
    end
end

local function toggleNoclip(state)
    settings.noclip = state
    if state then
        showNotification("👻 Noclip ATIVADO!", CORES.Sucesso)
        noclipConnection = RunService.Stepped:Connect(noclipLoop) -- Roda continuamente
    else
        showNotification("Noclip DESATIVADO!", CORES.Erro)
        if noclipConnection then noclipConnection:Disconnect() end
        -- Restaurar colisão
        if Character then
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end

-- Fly ✈️
local function toggleFly(state)
    settings.fly = state
    if state then
        showNotification("✈️ Fly ATIVADO! Use WASD para voar.", CORES.Sucesso)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing, false)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Falling, false)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, false)
        Humanoid.PlatformStand = true

        bodyGyro = Instance.new("BodyGyro", HumanoidRootPart)
        bodyGyro.P = 9e4
        bodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
        bodyGyro.CFrame = HumanoidRootPart.CFrame

        bodyVel = Instance.new("BodyVelocity", HumanoidRootPart)
        bodyVel.velocity = Vector3.new(0, 0, 0)
        bodyVel.maxForce = Vector3.new(9e9, 9e9, 9e9)

        flyConnection = RunService.RenderStepped:Connect(function()
            local moveDirection = Vector3.new()
            local cam = workspace.CurrentCamera

            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDirection += cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDirection -= cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDirection -= cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDirection += cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDirection += Vector3.new(0,1,0) end -- Subir
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDirection -= Vector3.new(0,1,0) end -- Descer

            moveDirection = moveDirection.Unit

            if moveDirection.Magnitude == 0 then
                bodyVel.Velocity = Vector3.new(0, 0, 0)
            else
                bodyVel.Velocity = moveDirection * 50 -- Velocidade de voo
            end
            bodyGyro.CFrame = cam.CFrame
        end)
    else
        showNotification("Fly DESATIVADO!", CORES.Erro)
        if flyConnection then flyConnection:Disconnect() end
        if bodyGyro then bodyGyro:Destroy() end
        if bodyVel then bodyVel:Destroy() end
        Humanoid.PlatformStand = false
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Falling, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, true)
    end
end

-- Invisibilidade 👻
local function toggleInvisivel(state)
    settings.invisible = state
    if state then
        showNotification("👻 Invisibilidade ATIVADA!", CORES.Sucesso)
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") or part:IsA("Decal") then
                part.Transparency = 1
            elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
                part.Enabled = false
            end
        end
        Humanoid.NameDisplayDistance = 0
    else
        showNotification("Invisibilidade DESATIVADA!", CORES.Erro)
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") or part:IsA("Decal") then
                part.Transparency = 0
            elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
                part.Enabled = true
            end
        end
        Humanoid.NameDisplayDistance = 100
    end
end

-- Criar botões de toggle na aba "Funções"
local _, btnNoclip, toggleNoclipBtn = criarBotaoToggle("Noclip", funcoesPage, "👻")
local _, btnFly, toggleFlyBtn = criarBotaoToggle("Fly", funcoesPage, "✈️")
local _, btnInvisivel, toggleInvisivelBtn = criarBotaoToggle("Invisível", funcoesPage, "👁️‍🗨️")

-- Função auxiliar para alternar estados dos toggles UI
local function updateToggleUI(toggleBtn, state)
    toggleBtn.Text = state and "ON 🟢" or "OFF 🔴"
    toggleBtn.BackgroundColor3 = state and CORES.ToggleON or CORES.ToggleOFF
end

-- Eventos dos botões de toggle
btnNoclip.MouseButton1Click:Connect(function()
    toggleNoclip(not settings.noclip)
    updateToggleUI(toggleNoclipBtn, settings.noclip)
end)

btnFly.MouseButton1Click:Connect(function()
    toggleFly(not settings.fly)
    updateToggleUI(toggleFlyBtn, settings.fly)
end)

btnInvisivel.MouseButton1Click:Connect(function()
    toggleInvisivel(not settings.invisible)
    updateToggleUI(toggleInvisivelBtn, settings.invisible)
end)

-- Sistema de Matar Player por Nome (Aba Funções) 💀
local killPlayerFrame = Instance.new("Frame")
killPlayerFrame.Size = UDim2.new(0.9, 0, 0, 95)
killPlayerFrame.BackgroundTransparency = 1
killPlayerFrame.Parent = funcoesPage
killPlayerFrame.ZIndex = 8

local killPlayerLabel = Instance.new("TextLabel")
killPlayerLabel.Size = UDim2.new(1, 0, 0.3, 0) -- 30% da altura do frame
killPlayerLabel.Text = "🔫 Matar Player por Nome:"
killPlayerLabel.TextColor3 = CORES.TextoClaro
killPlayerLabel.BackgroundTransparency = 1
killPlayerLabel.Font = Enum.Font.SourceSansBold
killPlayerLabel.TextSize = 20
killPlayerLabel.Parent = killPlayerFrame
killPlayerLabel.ZIndex = 9

local playerNameBox = Instance.new("TextBox")
playerNameBox.Size = UDim2.new(0.68, 0, 0.45, 0) -- Largura ajustada
playerNameBox.Position = UDim2.new(0, 0, 0.4, 0)
playerNameBox.PlaceholderText = "Digite o nome do alvo..."
playerNameBox.BackgroundColor3 = CORES.FundoComponente
playerNameBox.TextColor3 = CORES.TextoClaro
playerNameBox.Font = Enum.Font.SourceSans
playerNameBox.TextSize = 18
playerNameBox.Parent = killPlayerFrame
playerNameBox.CornerRadius = UDim.new(0, 8)
playerNameBox.ZIndex = 9

local killButton = Instance.new("TextButton")
killButton.Size = UDim2.new(0.3, 0, 0.45, 0) -- Largura ajustada
killButton.Position = UDim2.new(0.7, 0, 0.4, 0)
killButton.Text = "💥 KILL"
killButton.Font = Enum.Font.SourceSansBold
killButton.TextColor3 = CORES.TextoClaro
killButton.BackgroundColor3 = CORES.Primaria
killButton.Parent = killPlayerFrame
killButton.CornerRadius = UDim.new(0, 8)
killButton.ZIndex = 9

killButton.MouseButton1Click:Connect(function()
    local targetName = playerNameBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetName)

    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChildOfClass("Humanoid") then
        local targetHumanoid = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
        targetHumanoid.Health = 0
        showNotification("💀 " .. targetName .. " aniquilado!", CORES.Sucesso)
        logMessage("Matou " .. targetName, "INFO")
    else
        showNotification("❌ Alvo '" .. targetName .. "' não encontrado ou inválido!", CORES.Erro)
        logMessage("Falha ao matar " .. targetName, "ERROR")
    end
    playerNameBox.Text = "" -- Limpa a caixa
end)

-- Teleportes Secretos (Aba Teleportes) 📍
local teleportes = {
    {Name = "Casa Padrão 🏠", Position = Vector3.new(-100, 5, 0)}, -- Exemplo
    {Name = "Banco 🏦", Position = Vector3.new(200, 10, 50)},    -- Exemplo
    {Name = "Hospital 🏥", Position = Vector3.new(50, 15, -150)},  -- Exemplo
    {Name = "Piscina Secreta 🏊", Position = Vector3.new(300, 20, 200)}, -- Exemplo
    {Name = "Loja de Carros 🚗", Position = Vector3.new(0, 5, 100)} -- Exemplo
}

local teleporteLabel = Instance.new("TextLabel")
teleporteLabel.Size = UDim2.new(0.9, 0, 0, 30)
teleporteLabel.Text = "📍 Teleportes Ultra Secretos:"
teleporteLabel.TextColor3 = CORES.TextoClaro
teleporteLabel.BackgroundTransparency = 1
teleporteLabel.Font = Enum.Font.SourceSansBold
teleporteLabel.TextSize = 20
teleporteLabel.Parent = teleportesPage
teleporteLabel.ZIndex = 8

local teleporteDropdown = Instance.new("Frame")
teleporteDropdown.Size = UDim2.new(0.9, 0, 0, 45) -- Altura inicial do botão
teleporteDropdown.BackgroundColor3 = CORES.FundoComponente
teleporteDropdown.Parent = teleportesPage
teleporteDropdown.CornerRadius = UDim.new(0, 8)
teleporteDropdown.ZIndex = 8

local dropdownButton = Instance.new("TextButton")
dropdownButton.Size = UDim2.new(1, 0, 1, 0)
dropdownButton.BackgroundColor3 = CORES.FundoComponente
dropdownButton.TextColor3 = CORES.TextoClaro
dropdownButton.Font = Enum.Font.SourceSans
dropdownButton.TextSize = 18
dropdownButton.TextXAlignment = Enum.TextXAlignment.Left
dropdownButton.Text = "Selecione um local..."
dropdownButton.Parent = teleporteDropdown
dropdownButton.ZIndex = 9

local dropdownArrow = Instance.new("TextLabel")
dropdownArrow.Size = UDim2.new(0, 35, 1, 0)
dropdownArrow.Position = UDim2.new(1, -35, 0, 0)
dropdownArrow.BackgroundColor3 = CORES.FundoComponente
dropdownArrow.BackgroundTransparency = 1
dropdownArrow.TextColor3 = CORES.TextoClaro
dropdownArrow.Font = Enum.Font.SourceSansBold
dropdownArrow.TextSize = 28
dropdownArrow.Text = "▼"
dropdownArrow.Parent = dropdownButton
dropdownArrow.ZIndex = 10

local dropdownList = Instance.new("Frame")
dropdownList.Size = UDim2.new(1, 0, 0, 0) -- Altura 0 inicialmente
dropdownList.Position = UDim2.new(0, 0, 1, 0)
dropdownList.BackgroundColor3 = CORES.Secundaria
dropdownList.Parent = teleporteDropdown
dropdownList.ClipsDescendants = true
dropdownList.CornerRadius = UDim.new(0, 8)
dropdownList.ZIndex = 9 -- Para ficar por cima dos outros elementos

local listLayout = Instance.new("UIListLayout")
listLayout.Parent = dropdownList
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout.Padding = UDim.new(0, 3)

local listOpen = false
dropdownButton.MouseButton1Click:Connect(function()
    listOpen = not listOpen
    local targetHeight = listOpen and (#teleportes * 35 + listLayout.Padding.Offset * (#teleportes - 1)) or 0 -- 35 altura por item
    local tweenInfo = TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    TweenService:Create(dropdownList, tweenInfo, {Size = UDim2.new(1, 0, 0, targetHeight)}):Play()
    dropdownArrow.Text = listOpen and "▲" or "▼"
end)

for i, tele in ipairs(teleportes) do
    local teleOption = Instance.new("TextButton")
    teleOption.Size = UDim2.new(1, 0, 0, 35)
    teleOption.Text = tele.Name
    teleOption.BackgroundColor3 = CORES.Secundaria
    teleOption.BackgroundTransparency = 0.5
    teleOption.TextColor3 = CORES.TextoClaro
    teleOption.Font = Enum.Font.SourceSans
    teleOption.TextSize = 18
    teleOption.Parent = dropdownList
    teleOption.LayoutOrder = i
    teleOption.ZIndex = 10

    teleOption.MouseButton1Click:Connect(function()
        if HumanoidRootPart then
            HumanoidRootPart.CFrame = CFrame.new(tele.Position)
            showNotification("🚀 Teleportado para " .. tele.Name .. "!", CORES.Sucesso)
            logMessage("Teleportado para " .. tele.Name, "INFO")
            dropdownButton.Text = tele.Name
            dropdownButton.MouseButton1Click:Fire() -- Fecha o dropdown
        else
            showNotification("❌ Não foi possível teleportar!", CORES.Erro)
        end
    end)
end

-- Sistema de Spam Sonoro por Palavra (Modo Troll Extremo) 😈
local trollLabel = Instance.new("TextLabel")
trollLabel.Size = UDim2.new(0.9, 0, 0, 30)
trollLabel.Text = "😈 Modo Troll Extremo (Spam Sonoro):"
trollLabel.TextColor3 = CORES.TextoClaro
trollLabel.BackgroundTransparency = 1
trollLabel.Font = Enum.Font.SourceSansBold
trollLabel.TextSize = 20
trollLabel.Parent = trollPage
trollLabel.ZIndex = 8

local soundTextBox = Instance.new("TextBox")
soundTextBox.Size = UDim2.new(0.9, 0, 0, 40)
soundTextBox.PlaceholderText = "Digite 'gemidao', 'susto', 'aplausos', 'alerta'..."
soundTextBox.BackgroundColor3 = CORES.FundoComponente
soundTextBox.TextColor3 = CORES.TextoClaro
soundTextBox.Font = Enum.Font.SourceSans
soundTextBox.TextSize = 18
soundTextBox.Parent = trollPage
soundTextBox.CornerRadius = UDim.new(0, 8)
soundTextBox.ZIndex = 9

local spamSoundButton = Instance.new("TextButton")
spamSoundButton.Size = UDim2.new(0.45, 0, 0, 45)
spamSoundButton.Position = UDim2.new(0, 0, 0.2, 0)
spamSoundButton.Text = "🔊 SPAM SOUND"
spamSoundButton.Font = Enum.Font.SourceSansBold
spamSoundButton.TextColor3 = CORES.TextoClaro
spamSoundButton.BackgroundColor3 = CORES.Primaria
spamSoundButton.Parent = trollPage
spamSoundButton.CornerRadius = UDim.new(0, 10)
spamSoundButton.ZIndex = 9

local stopSoundButton = Instance.new("TextButton")
stopSoundButton.Size = UDim2.new(0.45, 0, 0, 45)
stopSoundButton.Position = UDim2.new(0.55, 0, 0.2, 0)
stopSoundButton.Text = "🔇 STOP"
stopSoundButton.Font = Enum.Font.SourceSansBold
stopSoundButton.TextColor3 = CORES.TextoClaro
stopSoundButton.BackgroundColor3 = CORES.Erro
stopSoundButton.Parent = trollPage
stopSoundButton.CornerRadius = UDim.new(0, 10)
stopSoundButton.ZIndex = 9

local currentSoundSpam = nil
local spamming = false

spamSoundButton.MouseButton1Click:Connect(function()
    if spamming then return end -- Já está spamando
    local soundName = soundTextBox.Text:lower()
    local soundId = AUDIO_IDS[soundName:gsub(" ", "")] -- Remove espaços e busca o ID

    if soundId then
        spamming = true
        showNotification("😈 Iniciando spam de '" .. soundName .. "'!", CORES.Sucesso)
        currentSoundSpam = Instance.new("Sound")
        currentSoundSpam.SoundId = "rbxassetid://" .. soundId
        currentSoundSpam.Parent = workspace
        currentSoundSpam.Volume = 1 -- Ajuste conforme necessário
        currentSoundSpam.Looped = true
        currentSoundSpam:Play()
    else
        showNotification("❌ Som '" .. soundName .. "' não encontrado!", CORES.Erro)
        logMessage("Tentativa de spam de som inválido: " .. soundName, "ERROR")
    end
end)

stopSoundButton.MouseButton1Click:Connect(function()
    if currentSoundSpam and spamming then
        currentSoundSpam:Stop()
        currentSoundSpam:Destroy()
        currentSoundSpam = nil
        spamming = false
        showNotification("🔇 Spam de som PARADO!", CORES.Sucesso)
    end
end)

-- Hotkeys Configuráveis (Aba Hotkeys) ⌨️
local hotkeyLabel = Instance.new("TextLabel")
hotkeyLabel.Size = UDim2.new(0.9, 0, 0, 30)
hotkeyLabel.Text = "⌨️ Hotkeys Personalizadas:"
hotkeyLabel.TextColor3 = CORES.TextoClaro
hotkeyLabel.BackgroundTransparency = 1
hotkeyLabel.Font = Enum.Font.SourceSansBold
hotkeyLabel.TextSize = 20
hotkeyLabel.Parent = hotkeysPage
hotkeyLabel.ZIndex = 8

local hotkeyEntries = {}

local function createHotkeyEntry(funcName, display, parent)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 50)
    frame.BackgroundTransparency = 1
    frame.Parent = parent
    frame.ZIndex = 9

    local funcLabel = Instance.new("TextLabel")
    funcLabel.Size = UDim2.new(0.5, 0, 1, 0)
    funcLabel.Text = display .. ":"
    funcLabel.TextColor3 = CORES.TextoClaro
    funcLabel.BackgroundTransparency = 1
    funcLabel.Font = Enum.Font.SourceSans
    funcLabel.TextSize = 18
    funcLabel.TextXAlignment = Enum.TextXAlignment.Left
    funcLabel.Parent = frame
    funcLabel.ZIndex = 10

    local keyDisplay = Instance.new("TextBox")
    keyDisplay.Size = UDim2.new(0.4, 0, 1, 0)
    keyDisplay.Position = UDim2.new(0.6, 0, 0, 0)
    keyDisplay.Text = tostring(settings.hotkeys[funcName])
    keyDisplay.PlaceholderText = "Pressione uma tecla..."
    keyDisplay.BackgroundColor3 = CORES.FundoComponente
    keyDisplay.TextColor3 = CORES.TextoClaro
    keyDisplay.Font = Enum.Font.SourceSansBold
    keyDisplay.TextSize = 18
    keyDisplay.Parent = frame
    keyDisplay.CornerRadius = UDim.new(0, 8)
    keyDisplay.ZIndex = 10

    keyDisplay.Focused:Connect(function()
        keyDisplay.Text = "Aguardando..."
        local inputConn = UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
            if not gameProcessedEvent and input.UserInputType == Enum.UserInputType.Keyboard then
                settings.hotkeys[funcName] = input.KeyCode
                keyDisplay.Text = tostring(input.KeyCode)
                showNotification("Hotkey para '" .. display .. "' definida para " .. tostring(input.KeyCode), CORES.Sucesso)
                logMessage("Hotkey " .. funcName .. " definida para " .. tostring(input.KeyCode), "INFO")
                inputConn:Disconnect()
            end
        end)
    end)

    hotkeyEntries[funcName] = keyDisplay
end

createHotkeyEntry("fly", "Fly", hotkeysPage)
createHotkeyEntry("noclip", "Noclip", hotkeysPage)
createHotkeyEntry("invisible", "Invisível", hotkeysPage)
createHotkeyEntry("minimizar", "Minimizar UI", hotkeysPage)

-- Modo Stealth (Aba Config) 👻
local stealthToggleFrame, _, toggleStealthBtn = criarBotaoToggle("Modo Stealth", configPage, "👻")
toggleStealthBtn.MouseButton1Click:Connect(function()
    settings.stealthMode = not settings.stealthMode
    updateToggleUI(toggleStealthBtn, settings.stealthMode)
    if settings.stealthMode then
        showNotification("👻 Modo Stealth ATIVADO! UI invisível.", CORES.Sucesso)
        painel.Visible = false
        floatingButton.Visible = false -- Esconde o botão flutuante também
    else
        showNotification("Modo Stealth DESATIVADO! UI visível.", CORES.Erro)
        painel.Visible = panelIsVisible -- Volta ao estado anterior (aberto/fechado)
        floatingButton.Visible = true -- Reaparece o botão flutuante
    end
end)

-- Desenho "HUNSFUCK" no Painel (Visual) 🎨
local hunsfuckArt = Instance.new("TextLabel")
hunsfuckArt.Size = UDim2.new(0.8, 0, 0.15, 0) -- Ajustado para % da altura do painel
hunsfuckArt.Position = UDim2.new(0.1, 0, 0.8, 0) -- Posição no final do painel
hunsfuckArt.BackgroundColor3 = Color3.new(1,1,1)
hunsfuckArt.BackgroundTransparency = 1
hunsfuckArt.TextColor3 = CORES.Primaria
hunsfuckArt.Font = Enum.Font.SciFi
hunsfuckArt.TextSize = 40 -- Ajustado para mobile
hunsfuckArt.TextWrapped = true
hunsfuckArt.Text = "HUNSFUCK"
hunsfuckArt.TextStrokeTransparency = 0 -- Borda no texto
hunsfuckArt.TextStrokeColor3 = CORES.Secundaria
hunsfuckArt.Parent = painel
hunsfuckArt.ZIndex = 6


-- Sistema de Hotkeys Global
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end -- Ignora eventos processados pelo jogo

    if input.KeyCode == settings.hotkeys.fly then
        toggleFly(not settings.fly)
        updateToggleUI(toggleFlyBtn, settings.fly)
    elseif input.KeyCode == settings.hotkeys.noclip then
        toggleNoclip(not settings.noclip)
        updateToggleUI(toggleNoclipBtn, settings.noclip)
    elseif input.KeyCode == settings.hotkeys.invisible then
        toggleInvisivel(not settings.invisible)
        updateToggleUI(toggleInvisivelBtn, settings.invisible)
    elseif input.KeyCode == settings.hotkeys.minimizar then
        -- A hotkey de minimizar agora controla a visibilidade do painel principal,
        -- igual ao botão flutuante.
        floatingButton.MouseButton1Click:Fire()
    end
    -- Adicione mais hotkeys aqui conforme necessário
end)

-- Confirmar key e abrir painel 🔓
botaoConfirmar.MouseButton1Click:Connect(function()
    if caixaKey.Text == keyCorreta then
        local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.Out)
        local goal = {Position = UDim2.new(0.5, 0, -0.5, 0), BackgroundTransparency = 1} -- Move para cima e desaparece
        
        local tweenKeyFrame = TweenService:Create(keyFrame, tweenInfo, goal)
        
        -- Animar todos os filhos do keyFrame para desaparecerem junto
        for _, child in pairs(keyFrame:GetChildren()) do
            if child:IsA("TextLabel") or child:IsA("TextBox") or child:IsA("TextButton") then
                TweenService:Create(child, tweenInfo, {TextTransparency = 1, BackgroundTransparency = 1}):Play()
            end
        end

        tweenKeyFrame:Play()
        tweenKeyFrame.Completed:Wait()
        keyFrame:Destroy()

        -- Após o keyFrame desaparecer, mostra o botão flutuante e o painel (se não estiver em stealth)
        floatingButton.Visible = true
        if not settings.stealthMode then
             painel.Visible = true
             painel.BackgroundTransparency = 0 -- Garante que o painel não comece transparente
             panelIsVisible = true -- Seta o estado do painel como visível
             floatingButton.Text = "❌" -- Ajusta o ícone do botão flutuante
             showNotification("🎉 Acesso LIBERADO! Bem-vindo ao HUNSFUCK!", CORES.Sucesso, 5)
        else
            showNotification("🎉 Acesso LIBERADO! HUNSFUCK está em modo Stealth. Use Hotkeys!", CORES.Sucesso, 5)
            floatingButton.Visible = false -- Mantém o botão flutuante invisível no modo stealth
        end
        logMessage("Acesso concedido. Bem-vindo!", "INFO")
    else
        caixaKey.Text = ""
        caixaKey.PlaceholderText = "❌ Key INCORRETA! Tente novamente..."
        caixaKey.TextColor3 = CORES.Erro
        showNotification("❌ Key incorreta! Tente novamente.", CORES.Erro, 3)
        logMessage("Tentativa de key incorreta.", "WARNING")
    end
end)

-- Inicializa o estado dos toggles na UI
updateToggleUI(toggleNoclipBtn, settings.noclip)
updateToggleUI(toggleFlyBtn, settings.fly)
updateToggleUI(toggleInvisivelBtn, settings.invisible)
updateToggleUI(toggleStealthBtn, settings.stealthMode)

-- Verifica se o char existe em CharacterAdded
Player.CharacterAdded:Connect(function(newChar)
    Character = newChar
    HumanoidRootPart = newChar:WaitForChild("HumanoidRootPart")
    Humanoid = newChar:WaitForChild("Humanoid")
    -- Reativa funções se o personagem for resetado e a função estiver ativa
    task.wait(0.1) -- Pequeno delay para garantir que o HRP esteja pronto
    if settings.fly then toggleFly(true); updateToggleUI(toggleFlyBtn, true) end
    if settings.invisible then toggleInvisivel(true); updateToggleUI(toggleInvisivelBtn, true) end
    if settings.noclip then toggleNoclip(true); updateToggleUI(toggleNoclipBtn, true) end
end)

logMessage("HUNSFUCK by ogart carregado! 🚀", "INFO")
