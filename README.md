-- HUNSFUCK - O Script Definitivo 😈
-- Desenvolvido com carinho (e muita perdição) por ogart, a lenda!
-- Este script oferece uma interface completa para manipulação de gameplay no Roblox.

-- // Variáveis e Serviços Globais \\ --
local Player = game.Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart", 10)
local Humanoid = Character:WaitForChild("Humanoid", 10)
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local CollectionService = game:GetService("CollectionService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local HttpService = game:GetService("HttpService")
local Debris = game:GetService("Debris")

-- // Cores Premium e Design Sofisticado \\ --
local TEMAS = {
    ["Vermelho Sangue"] = {
        Primaria = Color3.fromRGB(180, 0, 0),
        Secundaria = Color3.fromRGB(30, 0, 0),
        FundoUI = Color3.fromRGB(20, 20, 20),
        FundoComponente = Color3.fromRGB(40, 40, 40),
        BordaClara = Color3.fromRGB(80, 80, 80),
        TextoClaro = Color3.fromRGB(240, 240, 240),
        TextoEscuro = Color3.fromRGB(180, 180, 180),
        Sucesso = Color3.fromRGB(0, 180, 0),
        Erro = Color3.fromRGB(255, 50, 50),
        ToggleON = Color3.fromRGB(0, 150, 0),
        ToggleOFF = Color3.fromRGB(150, 0, 0),
        SliderFill = Color3.fromRGB(200, 50, 50)
    },
    ["Azul Cyber"] = {
        Primaria = Color3.fromRGB(0, 100, 200),
        Secundaria = Color3.fromRGB(10, 20, 40),
        FundoUI = Color3.fromRGB(15, 15, 30),
        FundoComponente = Color3.fromRGB(30, 30, 60),
        BordaClara = Color3.fromRGB(50, 100, 180),
        TextoClaro = Color3.fromRGB(200, 220, 255),
        TextoEscuro = Color3.fromRGB(150, 180, 220),
        Sucesso = Color3.fromRGB(0, 180, 0),
        Erro = Color3.fromRGB(255, 50, 50),
        ToggleON = Color3.fromRGB(0, 120, 180),
        ToggleOFF = Color3.fromRGB(100, 50, 150),
        SliderFill = Color3.fromRGB(80, 150, 255)
    },
    ["Verde Matrix"] = {
        Primaria = Color3.fromRGB(0, 150, 50),
        Secundaria = Color3.fromRGB(0, 20, 0),
        FundoUI = Color3.fromRGB(10, 30, 10),
        FundoComponente = Color3.fromRGB(20, 50, 20),
        BordaClara = Color3.fromRGB(40, 80, 40),
        TextoClaro = Color3.fromRGB(200, 255, 200),
        TextoEscuro = Color3.fromRGB(150, 200, 150),
        Sucesso = Color3.fromRGB(0, 180, 0),
        Erro = Color3.fromRGB(255, 50, 50),
        ToggleON = Color3.fromRGB(0, 100, 0),
        ToggleOFF = Color3.fromRGB(100, 100, 0),
        SliderFill = Color3.fromRGB(50, 200, 50)
    }
}
local CORES = TEMAS["Vermelho Sangue"] -- Tema inicial padrão (será carregado das settings)

-- // Áudios do TROLL EXTREMO \\ --
local AUDIO_IDS = {
    gemidao = 5580009623,
    susto = 131976071,
    aplausos = 143521366,
    alerta = 139194218
}

-- // Variáveis e Funções de Salvamento de Configurações \\ --
local settings = {}
local configFileName = "Hunsfuck_Config.json"

local function saveSettings()
    local success, err = pcall(function()
        local data = HttpService:JSONEncode(settings)
        writefile(configFileName, data)
        showNotification("💾 Configurações salvas!", CORES.Sucesso)
    end)
    if not success then
        showNotification("❌ Erro ao salvar config: " .. err, CORES.Erro)
    end
end

local function loadSettings()
    local success, data = pcall(function()
        if isfile(configFileName) then
            return readfile(configFileName)
        
        end
        return nil
    end)

    if success and data then
        local decoded = HttpService:JSONDecode(data)
        for k, v in pairs(decoded) do
            settings[k] = v
        end
        if settings.tema then
            CORES = TEMAS[settings.tema] or TEMAS["Vermelho Sangue"]
        end
        showNotification("💾 Configurações carregadas!", CORES.Sucesso)
    else
        -- Default settings if no file or error
        settings.noclip = false
        settings.fly = false
        settings.invisible = false
        settings.stealthMode = false
        settings.walkSpeed = 16
        settings.jumpPower = 50
        settings.espBoxes = false
        settings.espTracers = false
        settings.antiAFK = false
        settings.tema = "Vermelho Sangue"
        settings.guiPos = UDim2.new(0.5, 0, 0.5, 0) -- Posição inicial centralizada
        settings.hotkeys = {
            fly = Enum.KeyCode.F,
            noclip = Enum.KeyCode.G,
            invisible = Enum.KeyCode.H,
            minimizar = Enum.KeyCode.M,
            clickTp = Enum.KeyCode.J,
            espBoxes = Enum.KeyCode.B,
            espTracers = Enum.KeyCode.T,
            antiAFK = Enum.KeyCode.L,
        }
    end
end
loadSettings() -- Carrega as configs no início do script

-- // Notificações Visuais Animadas \\ --
local function showNotification(text, color, duration)
    -- Remove notificações antigas para evitar acúmulo
    for _, notif in pairs(game.CoreGui:GetChildren()) do
        if notif.Name == "HunsfuckNotification" then
            notif:Destroy()
        end
    end

    duration = duration or 3
    local NotificationFrame = Instance.new("Frame")
    NotificationFrame.Size = UDim2.new(0.7, 0, 0, 60)
    NotificationFrame.Position = UDim2.new(0.5, 0, 1, 0) -- Começa fora da tela, embaixo, centralizado por AnchorPoint
    NotificationFrame.AnchorPoint = Vector2.new(0.5, 1) -- Ancoragem na parte inferior central
    NotificationFrame.BackgroundColor3 = color
    NotificationFrame.BorderSizePixel = 0
    NotificationFrame.Parent = game.CoreGui
    NotificationFrame.ZIndex = 999
    NotificationFrame.ClipsDescendants = true
    NotificationFrame.Name = "HunsfuckNotification"
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 8)
    notifCorner.Parent = NotificationFrame

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
    local goalIn = {Position = UDim2.new(0.5, 0, 1, -70)}

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

-- // UI PRINCIPAL \\ --
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HUNSFUCK_UI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui
ScreenGui.Enabled = true

-- Adiciona UIScale para responsividade global
local uiScale = Instance.new("UIScale")
uiScale.Scale = 0.9 -- Ajusta a escala da UI para telas menores
uiScale.Parent = ScreenGui

-- Painel Principal HUNSFUCK
local painel = Instance.new("Frame")
painel.Size = UDim2.new(0.9, 0, 0.8, 0) -- Tamanho inicial
painel.Position = settings.guiPos -- Carrega posição salva
painel.AnchorPoint = Vector2.new(0.5, 0.5)
painel.BackgroundColor3 = CORES.FundoUI
painel.BorderSizePixel = 0
painel.Visible = false -- Começa invisível, será ativado após a chave
painel.Parent = ScreenGui
painel.ClipsDescendants = true
painel.ZIndex = 100 -- ZIndex alto para garantir visibilidade

local uiCornerPainel = Instance.new("UICorner")
uiCornerPainel.CornerRadius = UDim.new(0, 15)
uiCornerPainel.Parent = painel

local painelAspectRatio = Instance.new("UIAspectRatioConstraint")
painelAspectRatio.AspectRatio = 550 / 550 -- Proporção para manter a forma
painelAspectRatio.AspectType = Enum.AspectType.FitWithinMaxSize -- CORRIGIDO AQUI
painelAspectRatio.DominantAxis = Enum.DominantAxis.Height
painelAspectRatio.Parent = painel

local uiStrokePainel = Instance.new("UIStroke")
uiStrokePainel.Color = CORES.BordaClara
uiStrokePainel.Transparency = 0.7
uiStrokePainel.Thickness = 1
uiStrokePainel.Parent = painel

-- Título painel (arrastável)
local tituloPainel = Instance.new("TextLabel")
tituloPainel.Size = UDim2.new(1, 0, 0.1, 0)
tituloPainel.BackgroundColor3 = CORES.Primaria
tituloPainel.TextColor3 = CORES.TextoClaro
tituloPainel.Font = Enum.Font.SourceSansBold
tituloPainel.TextSize = 28
tituloPainel.Text = "💀 HUNSFUCK 💀"
tituloPainel.Parent = painel
tituloPainel.ZIndex = 101

-- Drag & Drop do Painel
local dragging = false
local dragStart = nil
local initialPos = nil

tituloPainel.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        initialPos = painel.Position
        TweenService:Create(tituloPainel, TweenInfo.new(0.1), {BackgroundTransparency = 0.5}):Play()
        TweenService:Create(uiStrokePainel, TweenInfo.new(0.1), {Color = CORES.Sucesso, Thickness = 2}):Play()
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local deltaX = input.Position.X - dragStart.X
        local deltaY = input.Position.Y - dragStart.Y
        
        local newX = initialPos.X.Offset + deltaX
        local newY = initialPos.Y.Offset + deltaY
        
        -- Clamp para evitar que a GUI saia da tela
        local maxX = ScreenGui.AbsoluteSize.X - painel.AbsoluteSize.X
        local maxY = ScreenGui.AbsoluteSize.Y - painel.AbsoluteSize.Y

        newX = math.max(0, math.min(maxX, newX))
        newY = math.max(0, math.min(maxY, newY))

        painel.Position = UDim2.new(0, newX, 0, newY)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
        TweenService:Create(tituloPainel, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play()
        TweenService:Create(uiStrokePainel, TweenInfo.new(0.1), {Color = CORES.BordaClara, Thickness = 1}):Play()
        settings.guiPos = painel.Position -- Salva a nova posição
        saveSettings() -- Salva as configurações automaticamente
    end
end)

-- Botão minimizar/maximizar
local minimizarBtn = Instance.new("TextButton")
minimizarBtn.Size = UDim2.new(0, 45, 0, 45)
minimizarBtn.Position = UDim2.new(1, -50, 0, 7)
minimizarBtn.Text = "➖"
minimizarBtn.BackgroundColor3 = CORES.Secundaria
minimizarBtn.TextColor3 = CORES.TextoClaro
minimizarBtn.Font = Enum.Font.SourceSansBold
minimizarBtn.TextSize = 30
minimizarBtn.Parent = painel
minimizarBtn.ZIndex = 102

local uiCornerMinimizarBtn = Instance.new("UICorner")
uiCornerMinimizarBtn.CornerRadius = UDim.new(0, 6)
uiCornerMinimizarBtn.Parent = minimizarBtn

-- Animação de Hover/Click para minimizarBtn
minimizarBtn.MouseEnter:Connect(function() TweenService:Create(minimizarBtn, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
minimizarBtn.MouseLeave:Connect(function() TweenService:Create(minimizarBtn, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
minimizarBtn.MouseButton1Down:Connect(function() TweenService:Create(minimizarBtn, TweenInfo.new(0.05), {Size = UDim2.new(0, 43, 0, 43)}):Play() end)
minimizarBtn.MouseButton1Up:Connect(function() TweenService:Create(minimizarBtn, TweenInfo.new(0.1), {Size = UDim2.new(0, 45, 0, 45)}):Play() end)

local painelAberto = true
local originalSize = painel.Size
local minimizedSize = UDim2.new(painel.Size.X.Scale, painel.Size.X.Offset, 0.1, 0)

minimizarBtn.MouseButton1Click:Connect(function()
    painelAberto = not painelAberto
    local targetSize = painelAberto and originalSize or minimizedSize
    local targetText = painelAberto and "➖" or "➕"

    local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    TweenService:Create(painel, tweenInfo, {Size = targetSize}):Play()
    minimizarBtn.Text = targetText

    task.wait(0.1) -- Pequeno delay para a animação do painel
    for _, v in pairs(painel:GetChildren()) do
        if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") or v:IsA("ScrollingFrame") or v:IsA("TextLabel") then
            if v ~= minimizarBtn and v ~= tituloPainel then
                v.Visible = painelAberto
            end
        end
    end
end)


-- Menu Lateral (Abas)
local menuLateral = Instance.new("Frame")
menuLateral.Size = UDim2.new(0.25, 0, 0.9, 0)
menuLateral.Position = UDim2.new(0, 0, 0.1, 0)
menuLateral.BackgroundColor3 = CORES.Secundaria
menuLateral.BorderSizePixel = 0
menuLateral.Parent = painel
menuLateral.ZIndex = 101 -- ZIndex para aparecer sobre o painel principal

local uiCornerMenuLateral = Instance.new("UICorner")
uiCornerMenuLateral.CornerRadius = UDim.new(0, 8)
uiCornerMenuLateral.Parent = menuLateral

local menuLayout = Instance.new("UIListLayout")
menuLayout.Parent = menuLateral
menuLayout.SortOrder = Enum.SortOrder.LayoutOrder
menuLayout.Padding = UDim.new(0, 8)
menuLayout.FillDirection = Enum.FillDirection.Vertical
menuLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
menuLayout.VerticalAlignment = Enum.VerticalAlignment.Top


-- Container de Páginas (onde o conteúdo das abas aparece)
local pageContainer = Instance.new("Frame")
pageContainer.Size = UDim2.new(0.75, 0, 0.9, 0)
pageContainer.Position = UDim2.new(0.25, 0, 0.1, 0)
pageContainer.BackgroundColor3 = CORES.FundoUI
pageContainer.BorderSizePixel = 0
pageContainer.Parent = painel
pageContainer.ClipsDescendants = true
pageContainer.ZIndex = 101 -- ZIndex para aparecer sobre o painel principal

local uiCornerPageContainer = Instance.new("UICorner")
uiCornerPageContainer.CornerRadius = UDim.new(0, 10)
uiCornerPageContainer.Parent = pageContainer

local activePage = nil

local function createTabPage(name, emoji)
    local page = Instance.new("ScrollingFrame")
    page.Name = name .. "Page"
    page.Size = UDim2.new(1, -10, 1, -10) -- Ajustado para padding interno
    page.Position = UDim2.new(0.01, 0, 0.01, 0)
    page.BackgroundColor3 = CORES.FundoComponente
    page.BackgroundTransparency = 0
    page.BorderSizePixel = 0
    page.Parent = pageContainer
    page.Visible = false
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.ScrollBarThickness = 8
    page.ClipsDescendants = true
    page.ZIndex = 102 -- ZIndex para o conteúdo da página

    local uiCornerPage = Instance.new("UICorner")
    uiCornerPage.CornerRadius = UDim.new(0, 8)
    uiCornerPage.Parent = page

    local layout = Instance.new("UIListLayout")
    layout.Parent = page
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 12)
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.VerticalAlignment = Enum.VerticalAlignment.Top

    local tabButton = Instance.new("TextButton")
    tabButton.Size = UDim2.new(0.9, 0, 0, 45)
    tabButton.Text = emoji .. " " .. name
    tabButton.Font = Enum.Font.SourceSansBold
    tabButton.TextColor3 = CORES.TextoClaro
    tabButton.TextSize = 20
    tabButton.BackgroundColor3 = CORES.FundoComponente
    tabButton.Parent = menuLateral
    tabButton.LayoutOrder = #menuLateral:GetChildren()
    tabButton.ZIndex = 102

    local uiCornerTabButton = Instance.new("UICorner")
    uiCornerTabButton.CornerRadius = UDim.new(0, 8)
    uiCornerTabButton.Parent = tabButton

    -- Animações de Hover/Click para botões de aba
    tabButton.MouseEnter:Connect(function() TweenService:Create(tabButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
    tabButton.MouseLeave:Connect(function() TweenService:Create(tabButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
    tabButton.MouseButton1Down:Connect(function() TweenService:Create(tabButton, TweenInfo.new(0.05), {Size = UDim2.new(0.88, 0, 0, 43)}):Play() end)
    tabButton.MouseButton1Up:Connect(function() TweenService:Create(tabButton, TweenInfo.new(0.1), {Size = UDim2.new(0.9, 0, 0, 45)}):Play() end)

    tabButton.MouseButton1Click:Connect(function()
        if activePage then
            activePage.Visible = false
            for _, btn in pairs(menuLateral:GetChildren()) do
                if btn:IsA("TextButton") then
                    TweenService:Create(btn, TweenInfo.new(0.1), {BackgroundColor3 = CORES.FundoComponente, TextColor3 = CORES.TextoClaro}):Play()
                end
            end
        end
        page.Visible = true
        activePage = page
        TweenService:Create(tabButton, TweenInfo.new(0.1), {BackgroundColor3 = CORES.Primaria, TextColor3 = CORES.TextoClaro}):Play()
    end)

    return page, tabButton
end

-- Criação das abas
local funcoesPage, funcoesBtn = createTabPage("Funções", "⚙️")
local teleportesPage, teleportesBtn = createTabPage("Teleportes", "📍")
local trollPage, trollBtn = createTabPage("Troll", "😈")
local hotkeysPage, hotkeysBtn = createTabPage("Hotkeys", "⌨️")
local espPage, espBtn = createTabPage("ESP", "👁️‍🗨️")
local configPage, configBtn = createTabPage("Config", "🛠️")

-- Ativar a primeira aba por padrão
funcoesBtn.MouseButton1Click:Fire()

-- Função para aplicar tema
local function applyTheme(themeColors)
    CORES = themeColors
    -- Atualiza cores de elementos existentes
    painel.BackgroundColor3 = CORES.FundoUI
    tituloPainel.BackgroundColor3 = CORES.Primaria
    minimizarBtn.BackgroundColor3 = CORES.Secundaria
    menuLateral.BackgroundColor3 = CORES.Secundaria
    pageContainer.BackgroundColor3 = CORES.FundoUI
    
    -- Atualizar botões de abas
    for _, btn in pairs(menuLateral:GetChildren()) do
        if btn:IsA("TextButton") then
            if btn == activePage then
                btn.BackgroundColor3 = CORES.Primaria
            else
                btn.BackgroundColor3 = CORES.FundoComponente
            end
            btn.TextColor3 = CORES.TextoClaro
        end
    end

    -- Atualizar cores de componentes nas páginas
    for _, page in pairs(pageContainer:GetChildren()) do
        if page:IsA("ScrollingFrame") then
            page.BackgroundColor3 = CORES.FundoComponente
            for _, child in pairs(page:GetDescendants()) do
                if child:IsA("TextButton") then
                    if child.Name == "btnFunc" then child.BackgroundColor3 = CORES.FundoComponente end
                    if child.Name == "btnToggle" then 
                        local state = settings[child.Parent.Name:gsub("ToggleFrame", "")]
                        child.BackgroundColor3 = state and CORES.ToggleON or CORES.ToggleOFF
                    end
                    child.TextColor3 = CORES.TextoClaro
                elseif child:IsA("TextBox") then
                    child.BackgroundColor3 = CORES.FundoComponente
                    child.TextColor3 = CORES.TextoClaro
                elseif child:IsA("TextLabel") then
                    child.TextColor3 = CORES.TextoClaro
                elseif child:IsA("Frame") and child.Name == "SliderFill" then
                    child.BackgroundColor3 = CORES.SliderFill
                elseif child:IsA("Frame") and child.Name == "ToggleHandle" then
                     local state = settings[child.Parent.Parent.Name:gsub("ToggleFrame", "")]
                     child.BackgroundColor3 = CORES.Primaria
                     child.Position = state and UDim2.new(1, -child.Size.X.Offset, 0.5, 0) or UDim2.new(0, 0, 0.5, 0)
                end
            end
        end
    end
    -- Update strokes
    uiStrokePainel.Color = CORES.BordaClara
end

-- Função para criar botões de toggle estilizados
local function criarBotaoToggle(nome, parent, emoji)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 55)
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
    btnAtivar.TextSize = 20
    btnAtivar.BackgroundColor3 = CORES.FundoComponente
    btnAtivar.Name = "btnFunc"
    btnAtivar.Parent = frame
    btnAtivar.ZIndex = 9

    local uiCornerBtnAtivar = Instance.new("UICorner")
    uiCornerBtnAtivar.CornerRadius = UDim.new(0, 10)
    uiCornerBtnAtivar.Parent = btnAtivar

    -- Animação de Hover/Click para btnAtivar
    btnAtivar.MouseEnter:Connect(function() TweenService:Create(btnAtivar, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
    btnAtivar.MouseLeave:Connect(function() TweenService:Create(btnAtivar, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
    btnAtivar.MouseButton1Down:Connect(function() TweenService:Create(btnAtivar, TweenInfo.new(0.05), {Size = UDim2.new(0.68, 0, 0.9, 0)}):Play() end)
    btnAtivar.MouseButton1Up:Connect(function() TweenService:Create(btnAtivar, TweenInfo.new(0.1), {Size = UDim2.new(0.7, 0, 1, 0)}):Play() end)

    local toggleBg = Instance.new("Frame")
    toggleBg.Size = UDim2.new(0.28, 0, 0.8, 0)
    toggleBg.Position = UDim2.new(0.72, 0, 0.1, 0)
    toggleBg.BackgroundColor3 = CORES.ToggleOFF
    toggleBg.Parent = frame
    toggleBg.ZIndex = 9
    toggleBg.Name = "btnToggle"

    local uiCornerToggleBg = Instance.new("UICorner")
    uiCornerToggleBg.CornerRadius = UDim.new(0.5, 0)
    uiCornerToggleBg.Parent = toggleBg

    local toggleHandle = Instance.new("Frame")
    toggleHandle.Name = "ToggleHandle"
    toggleHandle.Size = UDim2.new(0, 25, 0, 25)
    toggleHandle.AnchorPoint = Vector2.new(0.5, 0.5)
    toggleHandle.Position = UDim2.new(0, toggleHandle.Size.X.Offset / 2, 0.5, 0)
    toggleHandle.BackgroundColor3 = CORES.Primaria
    toggleHandle.BorderSizePixel = 0
    toggleHandle.Parent = toggleBg
    toggleHandle.ZIndex = 10

    local uiCornerToggleHandle = Instance.new("UICorner")
    uiCornerToggleHandle.CornerRadius = UDim.new(0.5, 0)
    uiCornerToggleHandle.Parent = toggleHandle

    toggleBg.MouseButton1Click:Connect(function()
        btnAtivar.MouseButton1Click:Fire()
    end)

    return frame, btnAtivar, toggleBg, toggleHandle
end

-- Função auxiliar para alternar estados dos toggles UI
local function updateToggleUI(toggleBg, toggleHandle, state)
    local targetColor = state and CORES.ToggleON or CORES.ToggleOFF
    local targetPosition = state and UDim2.new(1, -toggleHandle.Size.X.Offset / 2, 0.5, 0) or UDim2.new(0, toggleHandle.Size.X.Offset / 2, 0.5, 0)

    TweenService:Create(toggleBg, TweenInfo.new(0.2), {BackgroundColor3 = targetColor}):Play()
    TweenService:Create(toggleHandle, TweenInfo.new(0.2), {Position = targetPosition}):Play()
end


-- Função para criar sliders estilizados
local function criarSlider(nome, parent, minVal, maxVal, initialVal, step, unit)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 70)
    frame.BackgroundTransparency = 1
    frame.Parent = parent
    frame.ZIndex = 8

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 0.4, 0)
    label.Text = nome .. ": " .. tostring(initialVal) .. unit
    label.TextColor3 = CORES.TextoClaro
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.SourceSansBold
    label.TextSize = 18
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    label.ZIndex = 9

    local sliderBg = Instance.new("Frame")
    sliderBg.Size = UDim2.new(1, 0, 0.3, 0)
    sliderBg.Position = UDim2.new(0, 0, 0.6, 0)
    sliderBg.BackgroundColor3 = CORES.FundoComponente
    sliderBg.BorderSizePixel = 0
    sliderBg.Parent = frame
    sliderBg.ZIndex = 9

    local uiCornerSliderBg = Instance.new("UICorner")
    uiCornerSliderBg.CornerRadius = UDim.new(0.5, 0)
    uiCornerSliderBg.Parent = sliderBg

    local sliderFill = Instance.new("Frame")
    sliderFill.Name = "SliderFill"
    sliderFill.Size = UDim2.new(0, (initialVal - minVal) / (maxVal - minVal) * sliderBg.AbsoluteSize.X, 1, 0)
    sliderFill.BackgroundColor3 = CORES.SliderFill
    sliderFill.BorderSizePixel = 0
    sliderFill.Parent = sliderBg
    sliderFill.ZIndex = 10

    local uiCornerSliderFill = Instance.new("UICorner")
    uiCornerSliderFill.CornerRadius = UDim.new(0.5, 0)
    uiCornerSliderFill.Parent = sliderFill

    local sliderHandle = Instance.new("Frame")
    sliderHandle.Size = UDim2.new(0, 20, 1.8, 0)
    sliderHandle.Position = UDim2.new(0, sliderFill.AbsoluteSize.X, 0.5, 0)
    sliderHandle.BackgroundColor3 = CORES.Primaria
    sliderHandle.BorderSizePixel = 0
    sliderHandle.Parent = sliderBg
    sliderHandle.AnchorPoint = Vector2.new(0.5, 0.5)
    sliderHandle.ZIndex = 11

    local uiCornerSliderHandle = Instance.new("UICorner")
    uiCornerSliderHandle.CornerRadius = UDim.new(0.5, 0)
    uiCornerSliderHandle.Parent = sliderHandle

    local dragging = false

    local function updateSliderValue(pos)
        local x = pos.X - sliderBg.AbsolutePosition.X
        local ratio = math.clamp(x / sliderBg.AbsoluteSize.X, 0, 1)
        
        local value = minVal + (maxVal - minVal) * ratio
        value = math.round(value / step) * step -- Arredonda para o passo
        value = math.clamp(value, minVal, maxVal)

        local fillWidth = (value - minVal) / (maxVal - minVal) * sliderBg.AbsoluteSize.X
        sliderFill.Size = UDim2.new(0, fillWidth, 1, 0)
        sliderHandle.Position = UDim2.new(0, fillWidth, 0.5, 0)
        label.Text = nome .. ": " .. tostring(value) .. unit
        
        frame:SetAttribute("Value", value)
        frame.Changed:Fire("Value") -- Para sinalizar mudança externa
    end

    sliderHandle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            updateSliderValue(input.Position)
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    -- Para permitir arrastar em qualquer ponto do sliderBg
    sliderBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            updateSliderValue(input.Position)
            dragging = true
        end
    end)

    -- Atualiza o slider para o valor inicial do settings
    local initialFillWidth = (initialVal - minVal) / (maxVal - minVal) * sliderBg.AbsoluteSize.X
    sliderFill.Size = UDim2.new(0, initialFillWidth, 1, 0)
    sliderHandle.Position = UDim2.new(0, initialFillWidth, 0.5, 0)
    label.Text = nome .. ": " .. tostring(initialVal) .. unit

    return frame
end


-- // Estados dos Sistemas e Suas Funções \\ --
local bodyGyro, bodyVel
local flyConnection
local noclipConnection
local antiAFKConnection
local espRenderConnection

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
        noclipConnection = RunService.Stepped:Connect(noclipLoop)
    else
        showNotification("Noclip DESATIVADO!", CORES.Erro)
        if noclipConnection then noclipConnection:Disconnect() end
        if Character then
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
    saveSettings()
end

-- Fly ✈️
local function toggleFly(state)
    settings.fly = state
    if state then
        showNotification("✈️ Fly ATIVADO! Use WASD/Touch para voar.", CORES.Sucesso)
        if Humanoid then
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing, false)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Falling, false)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, false)
            Humanoid.PlatformStand = true
        end

        if HumanoidRootPart then
            bodyGyro = Instance.new("BodyGyro", HumanoidRootPart)
            bodyGyro.P = 9e4
            bodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            bodyGyro.CFrame = HumanoidRootPart.CFrame

            bodyVel = Instance.new("BodyVelocity", HumanoidRootPart)
            bodyVel.velocity = Vector3.new(0, 0, 0)
            bodyVel.maxForce = Vector3.new(9e9, 9e9, 9e9)
        end

        flyConnection = RunService.RenderStepped:Connect(function()
            local moveDirection = Vector3.new()
            local cam = workspace.CurrentCamera

            -- Entradas de teclado
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDirection += cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDirection -= cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDirection -= cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDirection += cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDirection += Vector3.new(0,1,0) end -- Subir
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) or UserInputService:IsKeyDown(Enum.KeyCode.C) then moveDirection -= Vector3.new(0,1,0) end -- Descer

            moveDirection = moveDirection.Unit

            if bodyVel then
                if moveDirection.Magnitude == 0 then
                    bodyVel.Velocity = Vector3.new(0, 0, 0)
                else
                    bodyVel.Velocity = moveDirection * settings.walkSpeed * 2.5 -- Fly speed baseada na WalkSpeed
                end
            end
            if bodyGyro then
                bodyGyro.CFrame = cam.CFrame
            end
        end)
    else
        showNotification("Fly DESATIVADO!", CORES.Erro)
        if flyConnection then flyConnection:Disconnect() end
        if bodyGyro then bodyGyro:Destroy() end
        if bodyVel then bodyVel:Destroy() end
        if Humanoid then
            Humanoid.PlatformStand = false
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Falling, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, true)
        end
    end
    saveSettings()
end

-- Invisibilidade 👻
local function toggleInvisivel(state)
    settings.invisible = state
    if state then
        showNotification("👻 Invisibilidade ATIVADA!", CORES.Sucesso)
        if Character then
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") or part:IsA("Decal") then
                    part.Transparency = 1
                elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
                    part.Enabled = false
                end
            end
            if Humanoid then Humanoid.NameDisplayDistance = 0 end
        end
    else
        showNotification("Invisibilidade DESATIVADA!", CORES.Erro)
        if Character then
            for _, part in pairs(Character:GetDescendants()) do
                if part:IsA("BasePart") or part:IsA("Decal") then
                    part.Transparency = 0
                elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
                    part.Enabled = true
                end
            end
            if Humanoid then Humanoid.NameDisplayDistance = 100 end
        end
    end
    saveSettings()
end

-- WalkSpeed & JumpPower
local function updateWalkSpeed(speed)
    settings.walkSpeed = speed
    if Humanoid then Humanoid.WalkSpeed = speed end
    showNotification("⚡️ Velocidade: " .. speed, CORES.Sucesso, 1)
    saveSettings()
end

local function updateJumpPower(power)
    settings.jumpPower = power
    if Humanoid then Humanoid.JumpPower = power end
    showNotification("⬆️ Salto: " .. power, CORES.Sucesso, 1)
    saveSettings()
end

-- Anti-AFK 🚶
local function toggleAntiAFK(state)
    settings.antiAFK = state
    if state then
        showNotification("🚶 Anti-AFK ATIVADO!", CORES.Sucesso)
        antiAFKConnection = RunService.Heartbeat:Connect(function()
            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false)
            task.wait(0.1)
            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false)
            task.wait(5)
        end)
    else
        showNotification("Anti-AFK DESATIVADO!", CORES.Erro)
        if antiAFKConnection then antiAFKConnection:Disconnect() end
    end
    saveSettings()
end

-- Funções de criação de UI na aba "Funções"
local _, btnNoclip, toggleNoclipBg, toggleNoclipHandle = criarBotaoToggle("Noclip", funcoesPage, "👻")
btnNoclip.MouseButton1Click:Connect(function()
    toggleNoclip(not settings.noclip)
    updateToggleUI(toggleNoclipBg, toggleNoclipHandle, settings.noclip)
end)

local _, btnFly, toggleFlyBg, toggleFlyHandle = criarBotaoToggle("Fly", funcoesPage, "✈️")
btnFly.MouseButton1Click:Connect(function()
    toggleFly(not settings.fly)
    updateToggleUI(toggleFlyBg, toggleFlyHandle, settings.fly)
end)

local _, btnInvisivel, toggleInvisivelBg, toggleInvisivelHandle = criarBotaoToggle("Invisível", funcoesPage, "👁️‍🗨️")
btnInvisivel.MouseButton1Click:Connect(function()
    toggleInvisivel(not settings.invisible)
    updateToggleUI(toggleInvisivelBg, toggleInvisivelHandle, settings.invisible)
end)

local walkSpeedSlider = criarSlider("WalkSpeed", funcoesPage, 16, 100, settings.walkSpeed, 1, " studs/s")
walkSpeedSlider.Changed:Connect(function()
    updateWalkSpeed(walkSpeedSlider:GetAttribute("Value"))
end)

local jumpPowerSlider = criarSlider("JumpPower", funcoesPage, 50, 200, settings.jumpPower, 5, " Força")
jumpPowerSlider.Changed:Connect(function()
    updateJumpPower(jumpPowerSlider:GetAttribute("Value"))
end)

local _, btnAntiAFK, toggleAntiAFKBg, toggleAntiAFKHandle = criarBotaoToggle("Anti-AFK", funcoesPage, "🚶")
btnAntiAFK.MouseButton1Click:Connect(function()
    toggleAntiAFK(not settings.antiAFK)
    updateToggleUI(toggleAntiAFKBg, toggleAntiAFKHandle, settings.antiAFK)
end)

-- Sistema de Matar Player por Nome (Aba Funções) 💀
local killPlayerFrame = Instance.new("Frame")
killPlayerFrame.Size = UDim2.new(0.9, 0, 0, 95)
killPlayerFrame.BackgroundTransparency = 1
killPlayerFrame.Parent = funcoesPage
killPlayerFrame.ZIndex = 8

local killPlayerLabel = Instance.new("TextLabel")
killPlayerLabel.Size = UDim2.new(1, 0, 0.3, 0)
killPlayerLabel.Text = "🔫 Matar Player por Nome:"
killPlayerLabel.TextColor3 = CORES.TextoClaro
killPlayerLabel.BackgroundTransparency = 1
killPlayerLabel.Font = Enum.Font.SourceSansBold
killPlayerLabel.TextSize = 20
killPlayerLabel.Parent = killPlayerFrame
killPlayerLabel.ZIndex = 9

local playerNameBox = Instance.new("TextBox")
playerNameBox.Size = UDim2.new(0.68, 0, 0.45, 0)
playerNameBox.Position = UDim2.new(0, 0, 0.4, 0)
playerNameBox.PlaceholderText = "Digite o nome do alvo..."
playerNameBox.BackgroundColor3 = CORES.FundoComponente
playerNameBox.TextColor3 = CORES.TextoClaro
playerNameBox.Font = Enum.Font.SourceSans
playerNameBox.TextSize = 18
playerNameBox.BorderSizePixel = 0
playerNameBox.Parent = killPlayerFrame
playerNameBox.ZIndex = 9

local uiCornerPlayerNameBox = Instance.new("UICorner")
uiCornerPlayerNameBox.CornerRadius = UDim.new(0, 8)
uiCornerPlayerNameBox.Parent = playerNameBox

local killButton = Instance.new("TextButton")
killButton.Size = UDim2.new(0.3, 0, 0.45, 0)
killButton.Position = UDim2.new(0.7, 0, 0.4, 0)
killButton.Text = "💥 KILL"
killButton.Font = Enum.Font.SourceSansBold
killButton.TextColor3 = CORES.TextoClaro
killButton.BackgroundColor3 = CORES.Primaria
killButton.Parent = killPlayerFrame
killButton.ZIndex = 9

local uiCornerKillButton = Instance.new("UICorner")
uiCornerKillButton.CornerRadius = UDim.new(0, 8)
uiCornerKillButton.Parent = killButton

-- Animação de Hover/Click para killButton
killButton.MouseEnter:Connect(function() TweenService:Create(killButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
killButton.MouseLeave:Connect(function() TweenService:Create(killButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
killButton.MouseButton1Down:Connect(function() TweenService:Create(killButton, TweenInfo.new(0.05), {Size = UDim2.new(0.68, 0, 0.43, 0)}):Play() end)
killButton.MouseButton1Up:Connect(function() TweenService:Create(killButton, TweenInfo.new(0.1), {Size = UDim2.new(0.7, 0, 0.45, 0)}):Play() end)

killButton.MouseButton1Click:Connect(function()
    local targetName = playerNameBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetName)

    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChildOfClass("Humanoid") then
        local targetHumanoid = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
        targetHumanoid.Health = 0
        showNotification("💀 " .. targetName .. " aniquilado!", CORES.Sucesso)
    else
        showNotification("❌ Alvo '" .. targetName .. "' não encontrado ou inválido!", CORES.Erro)
    end
    playerNameBox.Text = ""
end)

-- Teleportes Secretos (Aba Teleportes) 📍
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
teleporteDropdown.Size = UDim2.new(0.9, 0, 0, 45)
teleporteDropdown.BackgroundColor3 = CORES.FundoComponente
teleporteDropdown.Parent = teleportesPage
teleporteDropdown.ZIndex = 8

local uiCornerTeleporteDropdown = Instance.new("UICorner")
uiCornerTeleporteDropdown.CornerRadius = UDim.new(0, 8)
uiCornerTeleporteDropdown.Parent = teleporteDropdown

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
dropdownList.Size = UDim2.new(1, 0, 0, 0)
dropdownList.Position = UDim2.new(0, 0, 1, 0)
dropdownList.BackgroundColor3 = CORES.Secundaria
dropdownList.Parent = teleporteDropdown
dropdownList.ClipsDescendants = true
dropdownList.ZIndex = 9

local uiCornerDropdownList = Instance.new("UICorner")
uiCornerDropdownList.CornerRadius = UDim.new(0, 8)
uiCornerDropdownList.Parent = dropdownList

local listLayout = Instance.new("UIListLayout")
listLayout.Parent = dropdownList
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout.Padding = UDim.new(0, 3)

local listOpen = false
dropdownButton.MouseButton1Click:Connect(function()
    listOpen = not listOpen
    local targetHeight = listOpen and (#teleportes * 35 + listLayout.Padding.Offset * (#teleportes - 1)) or 0
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

    local uiCornerTeleOption = Instance.new("UICorner")
    uiCornerTeleOption.CornerRadius = UDim.new(0, 8)
    uiCornerTeleOption.Parent = teleOption

    -- Animação de Hover/Click para teleOption
    teleOption.MouseEnter:Connect(function() TweenService:Create(teleOption, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
    teleOption.MouseLeave:Connect(function() TweenService:Create(teleOption, TweenInfo.new(0.1), {BackgroundTransparency = 0.5}):Play() end)
    teleOption.MouseButton1Down:Connect(function() TweenService:Create(teleOption, TweenInfo.new(0.05), {Size = UDim2.new(0.98, 0, 0, 33)}):Play() end)
    teleOption.MouseButton1Up:Connect(function() TweenService:Create(teleOption, TweenInfo.new(0.1), {Size = UDim2.new(1, 0, 0, 35)}):Play() end)

    teleOption.MouseButton1Click:Connect(function()
        if HumanoidRootPart then
            HumanoidRootPart.CFrame = CFrame.new(tele.Position)
            showNotification("🚀 Teleportado para " .. tele.Name .. "!", CORES.Sucesso)
            dropdownButton.Text = tele.Name
            dropdownButton.MouseButton1Click:Fire()
            
            -- Efeito visual de teleporte
            local indicator = Instance.new("Part")
            indicator.Shape = Enum.PartType.Ball
            indicator.Size = Vector3.new(3,3,3)
            indicator.CFrame = CFrame.new(tele.Position)
            indicator.Transparency = 0.5
            indicator.BrickColor = BrickColor.new(CORES.Sucesso)
            indicator.CanCollide = false
            indicator.Parent = workspace
            Debris:AddItem(indicator, 1)

            local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            TweenService:Create(indicator, tweenInfo, {Size = Vector3.new(10,10,10), Transparency = 1}):Play()
        else
            showNotification("❌ Não foi possível teleportar!", CORES.Erro)
        end
    end)
end

-- Click Teleport 🖱️
local clickTpToggle, _, toggleClickTpBg, toggleClickTpHandle = criarBotaoToggle("Click TP", teleportesPage, "🖱️")
local clickTpActive = false
local clickTpConnection

local function toggleClickTeleport(state)
    clickTpActive = state
    settings.clickTp = state -- Salva o estado
    if state then
        showNotification("🖱️ Click TP ATIVADO! Clique no mundo para teleportar.", CORES.Sucesso)
        clickTpConnection = UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
            if gameProcessedEvent then return end
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                local ray = workspace.CurrentCamera:ScreenPointToRay(input.Position.X, input.Position.Y)
                local raycastParams = RaycastParams.new()
                raycastParams.FilterType = Enum.RaycastFilterType.Exclude
                raycastParams.FilterDescendantsInstances = {Player.Character}
                local result = workspace:Raycast(ray.Origin, ray.Direction * 500, raycastParams)

                if result and HumanoidRootPart then
                    HumanoidRootPart.CFrame = CFrame.new(result.Position)
                    showNotification("🚀 Teleportado para o clique!", CORES.Sucesso, 1)
                    -- Efeito visual de teleporte
                    local indicator = Instance.new("Part")
                    indicator.Shape = Enum.PartType.Ball
                    indicator.Size = Vector3.new(3,3,3)
                    indicator.CFrame = CFrame.new(result.Position)
                    indicator.Transparency = 0.5
                    indicator.BrickColor = BrickColor.new(CORES.Sucesso)
                    indicator.CanCollide = false
                    indicator.Parent = workspace
                    Debris:AddItem(indicator, 1)

                    local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
                    TweenService:Create(indicator, tweenInfo, {Size = Vector3.new(10,10,10), Transparency = 1}):Play()
                end
            end
        end)
    else
        showNotification("Click TP DESATIVADO!", CORES.Erro)
        if clickTpConnection then clickTpConnection:Disconnect() end
    end
    saveSettings()
end

clickTpToggle.MouseButton1Click:Connect(function()
    toggleClickTeleport(not clickTpActive)
    updateToggleUI(toggleClickTpBg, toggleClickTpHandle, clickTpActive)
end)


-- Teleport to Player (dropdown) 👤
local tpPlayerLabel = Instance.new("TextLabel")
tpPlayerLabel.Size = UDim2.new(0.9, 0, 0, 30)
tpPlayerLabel.Text = "👤 Teleportar para Jogador:"
tpPlayerLabel.TextColor3 = CORES.TextoClaro
tpPlayerLabel.BackgroundTransparency = 1
tpPlayerLabel.Font = Enum.Font.SourceSansBold
tpPlayerLabel.TextSize = 20
tpPlayerLabel.Parent = teleportesPage
tpPlayerLabel.ZIndex = 8

local tpPlayerDropdown = Instance.new("Frame")
tpPlayerDropdown.Size = UDim2.new(0.9, 0, 0, 45)
tpPlayerDropdown.BackgroundColor3 = CORES.FundoComponente
tpPlayerDropdown.Parent = teleportesPage
tpPlayerDropdown.ZIndex = 8

local uiCornerTpPlayerDropdown = Instance.new("UICorner")
uiCornerTpPlayerDropdown.CornerRadius = UDim.new(0, 8)
uiCornerTpPlayerDropdown.Parent = tpPlayerDropdown

local tpPlayerDropdownButton = Instance.new("TextButton")
tpPlayerDropdownButton.Size = UDim2.new(1, 0, 1, 0)
tpPlayerDropdownButton.BackgroundColor3 = CORES.FundoComponente
tpPlayerDropdownButton.TextColor3 = CORES.TextoClaro
tpPlayerDropdownButton.Font = Enum.Font.SourceSans
tpPlayerDropdownButton.TextSize = 18
tpPlayerDropdownButton.TextXAlignment = Enum.TextXAlignment.Left
tpPlayerDropdownButton.Text = "Selecione um jogador..."
tpPlayerDropdownButton.Parent = tpPlayerDropdown
tpPlayerDropdownButton.ZIndex = 9

local tpPlayerDropdownArrow = Instance.new("TextLabel")
tpPlayerDropdownArrow.Size = UDim2.new(0, 35, 1, 0)
tpPlayerDropdownArrow.Position = UDim2.new(1, -35, 0, 0)
tpPlayerDropdownArrow.BackgroundColor3 = CORES.FundoComponente
tpPlayerDropdownArrow.BackgroundTransparency = 1
tpPlayerDropdownArrow.TextColor3 = CORES.TextoClaro
tpPlayerDropdownArrow.Font = Enum.Font.SourceSansBold
tpPlayerDropdownArrow.TextSize = 28
tpPlayerDropdownArrow.Text = "▼"
tpPlayerDropdownArrow.Parent = tpPlayerDropdownButton
tpPlayerDropdownArrow.ZIndex = 10

local tpPlayerDropdownList = Instance.new("Frame")
tpPlayerDropdownList.Size = UDim2.new(1, 0, 0, 0)
tpPlayerDropdownList.Position = UDim2.new(0, 0, 1, 0)
tpPlayerDropdownList.BackgroundColor3 = CORES.Secundaria
tpPlayerDropdownList.Parent = tpPlayerDropdown
tpPlayerDropdownList.ClipsDescendants = true
tpPlayerDropdownList.ZIndex = 9

local uiCornerTpPlayerDropdownList = Instance.new("UICorner")
uiCornerTpPlayerDropdownList.CornerRadius = UDim.new(0, 8)
uiCornerTpPlayerDropdownList.Parent = tpPlayerDropdownList

local tpPlayerListLayout = Instance.new("UIListLayout")
tpPlayerListLayout.Parent = tpPlayerDropdownList
tpPlayerListLayout.SortOrder = Enum.SortOrder.LayoutOrder
tpPlayerListLayout.Padding = UDim.new(0, 3)

local tpPlayerListOpen = false
tpPlayerDropdownButton.MouseButton1Click:Connect(function()
    tpPlayerListOpen = not tpPlayerListOpen
    local players = game.Players:GetPlayers()
    local numPlayers = 0
    for _,p in pairs(players) do if p ~= Player then numPlayers += 1 end end

    tpPlayerDropdownList:ClearAllChildren() -- Limpa antes de preencher

    for i, p in ipairs(players) do
        if p ~= Player then
            local playerOption = Instance.new("TextButton")
            playerOption.Size = UDim2.new(1, 0, 0, 35)
            playerOption.Text = p.Name
            playerOption.BackgroundColor3 = CORES.Secundaria
            playerOption.BackgroundTransparency = 0.5
            playerOption.TextColor3 = CORES.TextoClaro
            playerOption.Font = Enum.Font.SourceSans
            playerOption.TextSize = 18
            playerOption.Parent = tpPlayerDropdownList
            playerOption.LayoutOrder = i
            playerOption.ZIndex = 10

            local uiCornerPlayerOption = Instance.new("UICorner")
            uiCornerPlayerOption.CornerRadius = UDim.new(0, 8)
            uiCornerPlayerOption.Parent = playerOption

            -- Animação de Hover/Click para playerOption
            playerOption.MouseEnter:Connect(function() TweenService:Create(playerOption, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
            playerOption.MouseLeave:Connect(function() TweenService:Create(playerOption, TweenInfo.new(0.1), {BackgroundTransparency = 0.5}):Play() end)
            playerOption.MouseButton1Down:Connect(function() TweenService:Create(playerOption, TweenInfo.new(0.05), {Size = UDim2.new(0.98, 0, 0, 33)}):Play() end)
            playerOption.MouseButton1Up:Connect(function() TweenService:Create(playerOption, TweenInfo.new(0.1), {Size = UDim2.new(1, 0, 0, 35)}):Play() end)

            playerOption.MouseButton1Click:Connect(function()
                if p.Character and p.Character:FindFirstChild("HumanoidRootPart") and HumanoidRootPart then
                    HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
                    showNotification("🚀 Teleportado para " .. p.Name .. "!", CORES.Sucesso)
                    tpPlayerDropdownButton.Text = p.Name
                    tpPlayerDropdownButton.MouseButton1Click:Fire()

                    -- Efeito visual de teleporte no player
                    local indicator = Instance.new("Part")
                    indicator.Shape = Enum.PartType.Ball
                    indicator.Size = Vector3.new(3,3,3)
                    indicator.CFrame = p.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
                    indicator.Transparency = 0.5
                    indicator.BrickColor = BrickColor.new(CORES.Sucesso)
                    indicator.CanCollide = false
                    indicator.Parent = workspace
                    Debris:AddItem(indicator, 1)

                    local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
                    TweenService:Create(indicator, tweenInfo, {Size = Vector3.new(10,10,10), Transparency = 1}):Play()
                else
                    showNotification("❌ Não foi possível teleportar para " .. p.Name, CORES.Erro)
                end
            end)
        end
    end

    local targetHeight = tpPlayerListOpen and (numPlayers * 35 + tpPlayerListLayout.Padding.Offset * (numPlayers - 1)) or 0
    local tweenInfo = TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    TweenService:Create(tpPlayerDropdownList, tweenInfo, {Size = UDim2.new(1, 0, 0, targetHeight)}):Play()
    tpPlayerDropdownArrow.Text = tpPlayerListOpen and "▲" or "▼"
end)


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
soundTextBox.ZIndex = 9

local uiCornerSoundTextBox = Instance.new("UICorner")
uiCornerSoundTextBox.CornerRadius = UDim.new(0, 8)
uiCornerSoundTextBox.Parent = soundTextBox

local spamSoundButton = Instance.new("TextButton")
spamSoundButton.Size = UDim2.new(0.45, 0, 0, 45)
spamSoundButton.Position = UDim2.new(0, 0, 0.2, 0)
spamSoundButton.Text = "🔊 SPAM SOUND"
spamSoundButton.Font = Enum.Font.SourceSansBold
spamSoundButton.TextColor3 = CORES.TextoClaro
spamSoundButton.BackgroundColor3 = CORES.Primaria
spamSoundButton.Parent = trollPage
spamSoundButton.ZIndex = 9

local uiCornerSpamSoundButton = Instance.new("UICorner")
uiCornerSpamSoundButton.CornerRadius = UDim.new(0, 10)
uiCornerSpamSoundButton.Parent = spamSoundButton

-- Animação de Hover/Click para spamSoundButton
spamSoundButton.MouseEnter:Connect(function() TweenService:Create(spamSoundButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
spamSoundButton.MouseLeave:Connect(function() TweenService:Create(spamSoundButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
spamSoundButton.MouseButton1Down:Connect(function() TweenService:Create(spamSoundButton, TweenInfo.new(0.05), {Size = UDim2.new(0.43, 0, 0, 43)}):Play() end)
spamSoundButton.MouseButton1Up:Connect(function() TweenService:Create(spamSoundButton, TweenInfo.new(0.1), {Size = UDim2.new(0.45, 0, 0, 45)}):Play() end)


local stopSoundButton = Instance.new("TextButton")
stopSoundButton.Size = UDim2.new(0.45, 0, 0, 45)
stopSoundButton.Position = UDim2.new(0.55, 0, 0.2, 0)
stopSoundButton.Text = "🔇 STOP"
stopSoundButton.Font = Enum.Font.SourceSansBold
stopSoundButton.TextColor3 = CORES.TextoClaro
stopSoundButton.BackgroundColor3 = CORES.Erro
stopSoundButton.Parent = trollPage
stopSoundButton.ZIndex = 9

local uiCornerStopSoundButton = Instance.new("UICorner")
uiCornerStopSoundButton.CornerRadius = UDim.new(0, 10)
uiCornerStopSoundButton.Parent = stopSoundButton

-- Animação de Hover/Click para stopSoundButton
stopSoundButton.MouseEnter:Connect(function() TweenService:Create(stopSoundButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
stopSoundButton.MouseLeave:Connect(function() TweenService:Create(stopSoundButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
stopSoundButton.MouseButton1Down:Connect(function() TweenService:Create(stopSoundButton, TweenInfo.new(0.05), {Size = UDim2.new(0.43, 0, 0, 43)}):Play() end)
stopSoundButton.MouseButton1Up:Connect(function() TweenService:Create(stopSoundButton, TweenInfo.new(0.1), {Size = UDim2.new(0.45, 0, 0, 45)}):Play() end)


local currentSoundSpam = nil
local spamming = false

spamSoundButton.MouseButton1Click:Connect(function()
    if spamming then return end
    local soundName = soundTextBox.Text:lower()
    local soundId = AUDIO_IDS[soundName:gsub(" ", "")]

    if soundId then
        spamming = true
        showNotification("😈 Iniciando spam de '" .. soundName .. "'!", CORES.Sucesso)
        currentSoundSpam = Instance.new("Sound")
        currentSoundSpam.SoundId = "rbxassetid://" .. soundId
        currentSoundSpam.Parent = workspace
        currentSoundSpam.Volume = 1
        currentSoundSpam.Looped = true
        currentSoundSpam:Play()
    else
        showNotification("❌ Som '" .. soundName .. "' não encontrado!", CORES.Erro)
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
    keyDisplay.BorderSizePixel = 0
    keyDisplay.Parent = frame
    keyDisplay.ZIndex = 10

    local uiCornerKeyDisplay = Instance.new("UICorner")
    uiCornerKeyDisplay.CornerRadius = UDim.new(0, 8)
    uiCornerKeyDisplay.Parent = keyDisplay

    keyDisplay.Focused:Connect(function()
        keyDisplay.Text = "Aguardando..."
        local inputConn = UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
            if not gameProcessedEvent and input.UserInputType == Enum.UserInputType.Keyboard then
                settings.hotkeys[funcName] = input.KeyCode
                keyDisplay.Text = tostring(input.KeyCode)
                showNotification("Hotkey para '" .. display .. "' definida para " .. tostring(input.KeyCode), CORES.Sucesso)
                saveSettings()
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
createHotkeyEntry("clickTp", "Click TP", hotkeysPage)
createHotkeyEntry("espBoxes", "ESP Boxes", hotkeysPage)
createHotkeyEntry("espTracers", "ESP Tracers", hotkeysPage)
createHotkeyEntry("antiAFK", "Anti-AFK", hotkeysPage)


-- ESP (Extra Sensory Perception) 👁️‍🗨️
local espPlayers = {}

local function toggleESPBoxes(state)
    settings.espBoxes = state
    if state then
        showNotification("👁️‍🗨️ ESP Boxes ATIVADO!", CORES.Sucesso)
    else
        showNotification("ESP Boxes DESATIVADO!", CORES.Erro)
    end
    saveSettings()
end

local function toggleESPTracers(state)
    settings.espTracers = state
    if state then
        showNotification("👁️‍🗨️ ESP Tracers ATIVADO!", CORES.Sucesso)
    else
        showNotification("ESP Tracers DESATIVADO!", CORES.Erro)
    end
    saveSettings()
end

local _, btnEspBoxes, toggleEspBoxesBg, toggleEspBoxesHandle = criarBotaoToggle("ESP Boxes", espPage, "📦")
btnEspBoxes.MouseButton1Click:Connect(function()
    toggleESPBoxes(not settings.espBoxes)
    updateToggleUI(toggleEspBoxesBg, toggleEspBoxesHandle, settings.espBoxes)
end)

local _, btnEspTracers, toggleEspTracersBg, toggleEspTracersHandle = criarBotaoToggle("ESP Tracers", espPage, "📍")
btnEspTracers.MouseButton1Click:Connect(function()
    toggleESPTracers(not settings.espTracers)
    updateToggleUI(toggleEspTracersBg, toggleEspTracersHandle, settings.espTracers)
end)

-- Loop de Renderização ESP
espRenderConnection = RunService.RenderStepped:Connect(function()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player == Player or not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
            if espPlayers[player.UserId] then
                espPlayers[player.UserId].Box:Destroy()
                espPlayers[player.UserId].Tracer:Destroy()
                espPlayers[player.UserId] = nil
            end
            continue
        end

        if not espPlayers[player.UserId] then
            local box = Instance.new("Frame")
            box.Size = UDim2.new(0, 50, 0, 100)
            box.BackgroundColor3 = CORES.Primaria
            box.BackgroundTransparency = 0.8
            box.BorderSizePixel = 1
            box.BorderColor3 = CORES.BordaClara
            box.Parent = ScreenGui
            box.ZIndex = 99 -- ZIndex alto para aparecer sobre o jogo
            box.Visible = false

            local uiCornerBox = Instance.new("UICorner")
            uiCornerBox.CornerRadius = UDim.new(0, 4)
            uiCornerBox.Parent = box

            local tracer = Instance.new("Frame")
            tracer.Size = UDim2.new(0, 2, 0, 0)
            tracer.BackgroundColor3 = CORES.Sucesso
            tracer.BackgroundTransparency = 0.8
            tracer.Parent = ScreenGui
            tracer.ZIndex = 98
            tracer.Visible = false
            tracer.AnchorPoint = Vector2.new(0.5, 0)

            local uiCornerTracer = Instance.new("UICorner")
            uiCornerTracer.CornerRadius = UDim.new(0.5, 0)
            uiCornerTracer.Parent = tracer

            espPlayers[player.UserId] = {Box = box, Tracer = tracer}
        end

        local char = player.Character
        local rootPart = char.HumanoidRootPart
        local head = char:FindFirstChild("Head") or rootPart

        local screenPoint, onScreen = workspace.CurrentCamera:WorldToScreenPoint(rootPart.Position)
        local headScreenPoint, headOnScreen = workspace.CurrentCamera:WorldToScreenPoint(head.Position + Vector3.new(0, 2, 0))

        if onScreen then
            local box = espPlayers[player.UserId].Box
            local tracer = espPlayers[player.UserId].Tracer

            -- Box ESP
            if settings.espBoxes then
                box.Visible = true
                local height = math.abs(screenPoint.Y - headScreenPoint.Y)
                local width = height * 0.5
                box.Size = UDim2.new(0, width, 0, height)
                box.Position = UDim2.new(0, screenPoint.X - width / 2, 0, headScreenPoint.Y)
            else
                box.Visible = false
            end

            -- Tracer ESP
            if settings.espTracers then
                tracer.Visible = true
                local originX = UserInputService:GetMouseLocation().X -- Mouse X
                local originY = UserInputService:GetMouseLocation().Y -- Mouse Y
                
                local startPoint = Vector2.new(originX, originY)
                local endPoint = Vector2.new(screenPoint.X, screenPoint.Y)

                local distance = (startPoint - endPoint).Magnitude
                local angle = math.atan2(endPoint.Y - startPoint.Y, endPoint.X - startPoint.X)

                tracer.Rotation = math.deg(angle) + 90
                tracer.Position = UDim2.new(0, startPoint.X, 0, startPoint.Y)
                tracer.Size = UDim2.new(0, 2, 0, distance)
            else
                tracer.Visible = false
            end
        else
            if espPlayers[player.UserId] then
                espPlayers[player.UserId].Box.Visible = false
                espPlayers[player.UserId].Tracer.Visible = false
            end
        end
    end
end)


-- Aba Config 🛠️
local _, btnStealth, toggleStealthBg, toggleStealthHandle = criarBotaoToggle("Modo Stealth", configPage, "👻")
toggleStealthBg.MouseButton1Click:Connect(function()
    settings.stealthMode = not settings.stealthMode
    updateToggleUI(toggleStealthBg, toggleStealthHandle, settings.stealthMode)
    if settings.stealthMode then
        showNotification("👻 Modo Stealth ATIVADO! UI invisível.", CORES.Sucesso)
        painel.Visible = false
    else
        showNotification("Modo Stealth DESATIVADO! UI visível.", CORES.Erro)
        painel.Visible = panelIsVisible -- Volta ao estado normal de visibilidade do painel
    end
    saveSettings()
end)

-- Sistema de Temas
local themeLabel = Instance.new("TextLabel")
themeLabel.Size = UDim2.new(0.9, 0, 0, 30)
themeLabel.Text = "🌈 Selecione um Tema:"
themeLabel.TextColor3 = CORES.TextoClaro
themeLabel.BackgroundTransparency = 1
themeLabel.Font = Enum.Font.SourceSansBold
themeLabel.TextSize = 20
themeLabel.Parent = configPage
themeLabel.ZIndex = 8

local themeFrame = Instance.new("Frame")
themeFrame.Size = UDim2.new(0.9, 0, 0, 80)
themeFrame.BackgroundTransparency = 1
themeFrame.Parent = configPage
themeFrame.ZIndex = 9

local themeLayout = Instance.new("UIListLayout")
themeLayout.Parent = themeFrame
themeLayout.FillDirection = Enum.FillDirection.Horizontal
themeLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
themeLayout.Padding = UDim.new(0, 10)

for themeName, themeColors in pairs(TEMAS) do
    local themeButton = Instance.new("TextButton")
    themeButton.Size = UDim2.new(0.3, 0, 0.8, 0)
    themeButton.BackgroundColor3 = themeColors.Primaria
    themeButton.TextColor3 = themeColors.TextoClaro
    themeButton.Text = themeName
    themeButton.Font = Enum.Font.SourceSansBold
    themeButton.TextSize = 16
    themeButton.Parent = themeFrame
    themeButton.ZIndex = 10

    local uiCornerThemeButton = Instance.new("UICorner")
    uiCornerThemeButton.CornerRadius = UDim.new(0, 8)
    uiCornerThemeButton.Parent = themeButton

    -- Animação de Hover/Click para themeButton
    themeButton.MouseEnter:Connect(function() TweenService:Create(themeButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
    themeButton.MouseLeave:Connect(function() TweenService:Create(themeButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
    themeButton.MouseButton1Down:Connect(function() TweenService:Create(themeButton, TweenInfo.new(0.05), {Size = UDim2.new(0.28, 0, 0.78, 0)}):Play() end)
    themeButton.MouseButton1Up:Connect(function() TweenService:Create(themeButton, TweenInfo.new(0.1), {Size = UDim2.new(0.3, 0, 0.8, 0)}):Play() end)


    themeButton.MouseButton1Click:Connect(function()
        settings.tema = themeName
        applyTheme(themeColors)
        showNotification("Tema '" .. themeName .. "' aplicado!", CORES.Sucesso, 2)
        saveSettings()
    end)
end

-- Botão Salvar Configurações Manualmente
local saveConfigButton = Instance.new("TextButton")
saveConfigButton.Size = UDim2.new(0.9, 0, 0, 50)
saveConfigButton.Text = "💾 Salvar Configurações"
saveConfigButton.Font = Enum.Font.SourceSansBold
saveConfigButton.TextColor3 = CORES.TextoClaro
saveConfigButton.BackgroundColor3 = CORES.FundoComponente
saveConfigButton.Parent = configPage
saveConfigButton.ZIndex = 8

local uiCornerSaveConfigButton = Instance.new("UICorner")
uiCornerSaveConfigButton.CornerRadius = UDim.new(0, 10)
uiCornerSaveConfigButton.Parent = saveConfigButton

-- Animação de Hover/Click para saveConfigButton
saveConfigButton.MouseEnter:Connect(function() TweenService:Create(saveConfigButton, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
saveConfigButton.MouseLeave:Connect(function() TweenService:Create(saveConfigButton, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
saveConfigButton.MouseButton1Down:Connect(function() TweenService:Create(saveConfigButton, TweenInfo.new(0.05), {Size = UDim2.new(0.88, 0, 0, 48)}):Play() end)
saveConfigButton.MouseButton1Up:Connect(function() TweenService:Create(saveConfigButton, TweenInfo.new(0.1), {Size = UDim2.new(0.9, 0, 0, 50)}):Play() end)

saveConfigButton.MouseButton1Click:Connect(function()
    saveSettings()
end)

-- Desenho "HUNSFUCK" no Painel (Visual) 🎨
local hunsfuckArt = Instance.new("TextLabel")
hunsfuckArt.Size = UDim2.new(0.8, 0, 0.15, 0)
hunsfuckArt.Position = UDim2.new(0.1, 0, 0.8, 0) -- Ajuste a posição conforme necessário
hunsfuckArt.BackgroundColor3 = Color3.new(1,1,1)
hunsfuckArt.BackgroundTransparency = 1
hunsfuckArt.TextColor3 = CORES.Primaria
hunsfuckArt.Font = Enum.Font.SciFi
hunsfuckArt.TextSize = 40
hunsfuckArt.TextWrapped = true
hunsfuckArt.Text = "HUNSFUCK"
hunsfuckArt.TextStrokeTransparency = 0
hunsfuckArt.TextStrokeColor3 = CORES.Secundaria
hunsfuckArt.Parent = painel
hunsfuckArt.ZIndex = 101 -- Garante que a arte apareça no painel


-- // Novo Sistema de Autenticação de Chave (Flutuante no centro) \\ --
local keyCorreta = "byogart" -- 🔑 A chave para o poder!

local keyFrame = Instance.new("Frame")
keyFrame.Size = UDim2.new(0.7, 0, 0.45, 0)
keyFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
keyFrame.AnchorPoint = Vector2.new(0.5, 0.5)
keyFrame.BackgroundColor3 = CORES.FundoUI
keyFrame.BorderSizePixel = 0
keyFrame.Parent = ScreenGui -- Parentado diretamente ao ScreenGui
keyFrame.ClipsDescendants = true
keyFrame.Visible = true -- Começa visível
keyFrame.ZIndex = 102 -- ZIndex muito alto para garantir que apareça sobre tudo

local uiCornerKeyFrame = Instance.new("UICorner")
uiCornerKeyFrame.CornerRadius = UDim.new(0, 12)
uiCornerKeyFrame.Parent = keyFrame

local keyFrameAspectRatio = Instance.new("UIAspectRatioConstraint")
keyFrameAspectRatio.AspectRatio = 400 / 220
keyFrameAspectRatio.AspectType = Enum.AspectType.FitWithinMaxSize -- CORRIGIDO AQUI
keyFrameAspectRatio.DominantAxis = Enum.DominantAxis.Width
keyFrameAspectRatio.Parent = keyFrame

local uiStrokeKey = Instance.new("UIStroke")
uiStrokeKey.Color = CORES.BordaClara
uiStrokeKey.Transparency = 0.8
uiStrokeKey.Thickness = 1
uiStrokeKey.Parent = keyFrame

local tituloKey = Instance.new("TextLabel")
tituloKey.Size = UDim2.new(1, 0, 0.3, 0)
tituloKey.Position = UDim2.new(0, 0, 0, 0)
tituloKey.BackgroundColor3 = CORES.Primaria
tituloKey.TextColor3 = CORES.TextoClaro
tituloKey.Font = Enum.Font.SourceSansBold
tituloKey.TextSize = 30
tituloKey.Text = "💀 HUNSFUCK 💀"
tituloKey.Parent = keyFrame

local caixaKey = Instance.new("TextBox")
caixaKey.Size = UDim2.new(0.85, 0, 0.18, 0)
caixaKey.Position = UDim2.new(0.075, 0, 0.45, 0)
caixaKey.PlaceholderText = "🔑 Digite a chave secreta..."
caixaKey.Text = ""
caixaKey.BackgroundColor3 = CORES.FundoComponente
caixaKey.TextColor3 = CORES.TextoClaro
caixaKey.Font = Enum.Font.SourceSans
caixaKey.TextSize = 20
caixaKey.BorderSizePixel = 0
caixaKey.Parent = keyFrame

local uiCornerCaixaKey = Instance.new("UICorner")
uiCornerCaixaKey.CornerRadius = UDim.new(0, 6)
uiCornerCaixaKey.Parent = caixaKey

local botaoConfirmar = Instance.new("TextButton")
botaoConfirmar.Size = UDim2.new(0.65, 0, 0.22, 0)
botaoConfirmar.Position = UDim2.new(0.175, 0, 0.75, 0)
botaoConfirmar.Text = "🚀 Confirmar Acesso 🚀"
botaoConfirmar.Font = Enum.Font.SourceSansBold
botaoConfirmar.TextColor3 = CORES.TextoClaro
botaoConfirmar.BackgroundColor3 = CORES.Sucesso
botaoConfirmar.BorderSizePixel = 0
botaoConfirmar.Parent = keyFrame

local uiCornerBotaoConfirmar = Instance.new("UICorner")
uiCornerBotaoConfirmar.CornerRadius = UDim.new(0, 10)
uiCornerBotaoConfirmar.Parent = botaoConfirmar

-- Animação de Hover/Click para botaoConfirmar
botaoConfirmar.MouseEnter:Connect(function() TweenService:Create(botaoConfirmar, TweenInfo.new(0.1), {BackgroundTransparency = 0.2}):Play() end)
botaoConfirmar.MouseLeave:Connect(function() TweenService:Create(botaoConfirmar, TweenInfo.new(0.1), {BackgroundTransparency = 0}):Play() end)
botaoConfirmar.MouseButton1Down:Connect(function() TweenService:Create(botaoConfirmar, TweenInfo.new(0.05), {Size = UDim2.new(0.63, 0, 0.20, 0)}):Play() end)
botaoConfirmar.MouseButton1Up:Connect(function() TweenService:Create(botaoConfirmar, TweenInfo.new(0.1), {Size = UDim2.new(0.65, 0, 0.22, 0)}):Play() end)

local creditosKey = Instance.new("TextLabel")
creditosKey.Size = UDim2.new(1, 0, 0.1, 0)
creditosKey.Position = UDim2.new(0, 0, 0.9, 0)
creditosKey.Text = "✨ Créditos: ogart - A Lenda! ✨"
creditosKey.TextColor3 = CORES.Sucesso
creditosKey.BackgroundTransparency = 1
creditosKey.Font = Enum.Font.SourceSansItalic
creditosKey.TextSize = 18
creditosKey.Parent = keyFrame


-- // Sistema de Hotkeys Global \\ --
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end

    if input.KeyCode == settings.hotkeys.fly then
        toggleFly(not settings.fly)
        updateToggleUI(toggleFlyBg, toggleFlyHandle, settings.fly)
    elseif input.KeyCode == settings.hotkeys.noclip then
        toggleNoclip(not settings.noclip)
        updateToggleUI(toggleNoclipBg, toggleNoclipHandle, settings.noclip)
    elseif input.KeyCode == settings.hotkeys.invisible then
        toggleInvisivel(not settings.invisible)
        updateToggleUI(toggleInvisivelBg, toggleInvisivelHandle, settings.invisible)
    elseif input.KeyCode == settings.hotkeys.minimizar then
        -- O botão de minimizar agora controla a minimização/maximização interna do painel
        minimizarBtn.MouseButton1Click:Fire() 
    elseif input.KeyCode == settings.hotkeys.clickTp then
        toggleClickTeleport(not clickTpActive)
        updateToggleUI(toggleClickTpBg, toggleClickTpHandle, clickTpActive)
    elseif input.KeyCode == settings.hotkeys.espBoxes then
        toggleESPBoxes(not settings.espBoxes)
        updateToggleUI(toggleEspBoxesBg, toggleEspBoxesHandle, settings.espBoxes)
    elseif input.KeyCode == settings.hotkeys.espTracers then
        toggleESPTracers(not settings.espTracers)
        updateToggleUI(toggleEspTracersBg, toggleEspTracersHandle, settings.espTracers)
    elseif input.KeyCode == settings.hotkeys.antiAFK then
        toggleAntiAFK(not settings.antiAFK)
        updateToggleUI(toggleAntiAFKBg, toggleAntiAFKHandle, settings.antiAFK)
    end
end)

-- Confirmar key e abrir painel 🔓
local panelIsVisible = true -- Variável para controlar se o painel principal está aberto (não minimizado)

botaoConfirmar.MouseButton1Click:Connect(function()
    if caixaKey.Text == keyCorreta then
        local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.Out)
        local goal = {Position = UDim2.new(0.5, 0, -0.5, 0), BackgroundTransparency = 1}
        
        local tweenKeyFrame = TweenService:Create(keyFrame, tweenInfo, goal)
        
        for _, child in pairs(keyFrame:GetChildren()) do
            if child:IsA("TextLabel") or child:IsA("TextBox") or child:IsA("TextButton") or child:IsA("UIStroke") or child:IsA("UICorner") or child:IsA("UIAspectRatioConstraint") then
                TweenService:Create(child, tweenInfo, {TextTransparency = 1, BackgroundTransparency = 1, Transparency = 1}):Play()
            end
        end

        tweenKeyFrame:Play()
        tweenKeyFrame.Completed:Wait()
        keyFrame:Destroy()

        if not settings.stealthMode then
             painel.Visible = true
             painel.BackgroundTransparency = 0
             panelIsVisible = true
             showNotification("🎉 Acesso LIBERADO! Bem-vindo ao HUNSFUCK!", CORES.Sucesso, 5)
        else
            showNotification("🎉 Acesso LIBERADO! HUNSFUCK está em modo Stealth. Use Hotkeys!", CORES.Sucesso, 5)
        end
    else
        caixaKey.Text = ""
        caixaKey.PlaceholderText = "❌ Key INCORRETA! Tente novamente..."
        caixaKey.TextColor3 = CORES.Erro
        showNotification("❌ Key incorreta! Tente novamente.", CORES.Erro, 3)
    end
end)

-- Inicializa o estado dos toggles na UI e aplica o tema salvo
local function initializeUIStates()
    -- Reaplicar hotkeys (importante para o UserInputService)
    settings.hotkeys.fly = settings.hotkeys.fly or Enum.KeyCode.F
    settings.hotkeys.noclip = settings.hotkeys.noclip or Enum.KeyCode.G
    settings.hotkeys.invisible = settings.hotkeys.invisible or Enum.KeyCode.H
    settings.hotkeys.minimizar = settings.hotkeys.minimizar or Enum.KeyCode.M
    settings.hotkeys.clickTp = settings.hotkeys.clickTp or Enum.KeyCode.J
    settings.hotkeys.espBoxes = settings.hotkeys.espBoxes or Enum.KeyCode.B
    settings.hotkeys.espTracers = settings.hotkeys.espTracers or Enum.KeyCode.T
    settings.hotkeys.antiAFK = settings.hotkeys.antiAFK or Enum.KeyCode.L

    -- Atualiza UI dos toggles
    updateToggleUI(toggleNoclipBg, toggleNoclipHandle, settings.noclip)
    updateToggleUI(toggleFlyBg, toggleFlyHandle, settings.fly)
    updateToggleUI(toggleInvisivelBg, toggleInvisivelHandle, settings.invisible)
    
    -- Para Click TP, precisamos definir o estado 'clickTpActive' globalmente
    clickTpActive = settings.clickTp
    updateToggleUI(toggleClickTpBg, toggleClickTpHandle, settings.clickTp)
    if settings.clickTp then toggleClickTeleport(true) end
    
    updateToggleUI(toggleEspBoxesBg, toggleEspBoxesHandle, settings.espBoxes)
    updateToggleUI(toggleEspTracersBg, toggleEspTracersHandle, settings.espTracers)
    updateToggleUI(toggleAntiAFKBg, toggleAntiAFKHandle, settings.antiAFK)
    updateToggleUI(toggleStealthBg, toggleStealthHandle, settings.stealthMode)


    -- Aplica valores iniciais aos sliders
    walkSpeedSlider:SetAttribute("Value", settings.walkSpeed)
    walkSpeedSlider.Changed:Fire("Value")
    jumpPowerSlider:SetAttribute("Value", settings.jumpPower)
    jumpPowerSlider.Changed:Fire("Value")

    -- Aplica o tema salvo
    applyTheme(TEMAS[settings.tema] or TEMAS["Vermelho Sangue"])

    -- Se o modo stealth estiver ativo, esconda o painel no início (após o login)
    if settings.stealthMode then
        painel.Visible = false
        panelIsVisible = false -- Indica que o painel está 'fechado' no modo stealth
    else
        painel.Visible = true
        panelIsVisible = true
    end
end

-- Reconexão Automática de Funções (Pós-Death) 🔄
Player.CharacterAdded:Connect(function(newChar)
    Character = newChar
    HumanoidRootPart = newChar:WaitForChild("HumanoidRootPart", 10)
    Humanoid = newChar:WaitForChild("Humanoid", 10)

    if HumanoidRootPart and Humanoid then
        task.wait(0.1)
        if settings.fly then toggleFly(true); updateToggleUI(toggleFlyBg, toggleFlyHandle, true) end
        if settings.invisible then toggleInvisivel(true); updateToggleUI(toggleInvisivelBg, toggleInvisivelHandle, true) end
        if settings.noclip then toggleNoclip(true); updateToggleUI(toggleNoclipBg, toggleNoclipHandle, true) end
        if settings.walkSpeed ~= 16 then updateWalkSpeed(settings.walkSpeed) end
        if settings.jumpPower ~= 50 then updateJumpPower(settings.jumpPower) end
        if settings.antiAFK then toggleAntiAFK(true); updateToggleUI(toggleAntiAFKBg, toggleAntiAFKHandle, true) end
        
        if settings.clickTp then toggleClickTeleport(true) end

        showNotification("Personagem resetado, funções ativas reaplicadas!", CORES.Sucesso, 2)
    end
end)

initializeUIStates() -- Chama a função para inicializar a UI com as configurações salvas

showNotification("HUNSFUCK carregado! 🚀 A lenda está viva!", CORES.Sucesso, 3)
