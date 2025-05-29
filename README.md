--!nolint
-- LOSTsCRIPTS - FPS Booster (Versão Aprimorada)
-- by ogart

--[[
    =========================================================================
    ||                        SERVIÇOS E VARIÁVEIS                         ||
    =========================================================================
]]

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local DebrisService = game:GetService("Debris")
local MaterialService = game:GetService("MaterialService")
local Stats = game:GetService("Stats") -- Para algumas otimizações (ex: physics)

local localPlayer = Players.LocalPlayer
local mouse = localPlayer:GetMouse()
local camera = Workspace.CurrentCamera

local gui = Instance.new("ScreenGui")
gui.Name = "LostFpsBoosterGui_V2"
gui.Parent = localPlayer:WaitForChild("PlayerGui")
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.ResetOnSpawn = false

--[[
    =========================================================================
    ||                        CONFIGURAÇÕES DA UI                          ||
    =========================================================================
]]

local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Parent = gui
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15) -- Um pouco mais claro
mainFrame.BorderColor3 = Color3.fromRGB(0, 220, 0) -- Verde vibrante
mainFrame.BorderSizePixel = 2
mainFrame.Size = UDim2.new(0, 500, 0, 400) -- Um pouco maior para mais abas/funções
mainFrame.Position = UDim2.new(0.5, -250, 0.5, -200) -- Centralizado
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Selectable = true
mainFrame.Visible = true -- Visível por padrão (sem sistema de key)

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = mainFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "TitleLabel"
titleLabel.Parent = mainFrame
titleLabel.BackgroundColor3 = Color3.fromRGB(0, 220, 0)
titleLabel.TextColor3 = Color3.fromRGB(10, 10, 10)
titleLabel.Size = UDim2.new(1, 0, 0, 35)
titleLabel.Font = Enum.Font.GothamSemibold
titleLabel.Text = "🚀 LOSTsCRIPTS - FPS Booster V2 🚀"
titleLabel.TextSize = 20

local minimizeButton = Instance.new("TextButton")
minimizeButton.Name = "MinimizeButton"
minimizeButton.Parent = mainFrame
minimizeButton.Size = UDim2.new(0, 30, 0, 30)
minimizeButton.Position = UDim2.new(1, -38, 0, 3) -- Ajustado para novo tamanho de título
minimizeButton.BackgroundColor3 = Color3.fromRGB(15,15,15)
minimizeButton.BorderColor3 = Color3.fromRGB(0, 220, 0)
minimizeButton.TextColor3 = Color3.fromRGB(0, 220, 0)
minimizeButton.Font = Enum.Font.GothamBold
minimizeButton.Text = "-"
minimizeButton.TextSize = 24
local minCorner = corner:Clone()
minCorner.Parent = minimizeButton

local creditsLabel = Instance.new("TextLabel")
creditsLabel.Name = "CreditsLabel"
creditsLabel.Parent = mainFrame
creditsLabel.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
creditsLabel.TextColor3 = Color3.fromRGB(0, 180, 0)
creditsLabel.Size = UDim2.new(1, 0, 0, 25)
creditsLabel.Position = UDim2.new(0, 0, 1, -25)
creditsLabel.Font = Enum.Font.Code
creditsLabel.Text = "✨ by ogart (v2) ✨"
creditsLabel.TextSize = 16

local tabsFrame = Instance.new("Frame")
tabsFrame.Name = "TabsFrame"
tabsFrame.Parent = mainFrame
tabsFrame.Size = UDim2.new(1, -10, 0, 35)
tabsFrame.Position = UDim2.new(0, 5, 0, 40) -- Abaixo do título
tabsFrame.BackgroundTransparency = 1

local contentFrame = Instance.new("ScrollingFrame") -- MUDADO PARA SCROLLINGFRAME
contentFrame.Name = "ContentFrame"
contentFrame.Parent = mainFrame
contentFrame.Size = UDim2.new(1, -10, 1, -105) -- Ajustar tamanho (título + abas + créditos)
contentFrame.Position = UDim2.new(0, 5, 0, 75) -- Abaixo das abas
contentFrame.BackgroundTransparency = 0.8
contentFrame.BackgroundColor3 = Color3.fromRGB(25,25,25)
contentFrame.BorderSizePixel = 0
contentFrame.ScrollBarThickness = 6
contentFrame.ScrollBarImageColor3 = Color3.fromRGB(0,220,0)
contentFrame.CanvasSize = UDim2.new(0,0,0,0) -- Será ajustado pelo UIListLayout

-- Variáveis de estado
local isMinimized = false
local currentTab = nil
local functionsState = {}
local originalValues = {} -- Para tentar reverter algumas configurações

--[[
    =========================================================================
    ||                        LÓGICA DE ARRASTAR UI                        ||
    =========================================================================
]]
local dragging = false
local dragInput, dragStart, startPos

mainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        if input.Position.Y < mainFrame.AbsolutePosition.Y + titleLabel.AbsoluteSize.Y and
           input.Position.X > mainFrame.AbsolutePosition.X and
           input.Position.X < mainFrame.AbsolutePosition.X + mainFrame.AbsoluteSize.X - minimizeButton.AbsoluteSize.X - 5 then -- Não arrasta pelo botão minimizar
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end
end)

mainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch and dragging then
        dragInput = input
    end
end)

RunService.RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

--[[
    =========================================================================
    ||                        LÓGICA DE MINIMIZAR (SEM ANIMAÇÃO)           ||
    =========================================================================
]]
local originalMainFrameSize = mainFrame.Size
minimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        minimizeButton.Text = "+"
        mainFrame.Size = UDim2.new(0, 200, 0, titleLabel.AbsoluteSize.Y) -- Tamanho minimizado
        tabsFrame.Visible = false
        contentFrame.Visible = false
        creditsLabel.Visible = false
    else
        minimizeButton.Text = "-"
        mainFrame.Size = originalMainFrameSize -- Restaura tamanho original
        tabsFrame.Visible = true
        contentFrame.Visible = true
        creditsLabel.Visible = true
    end
end)

--[[
    =========================================================================
    ||                        FUNÇÕES AUXILIARES                           ||
    =========================================================================
]]
local function storeOriginal(key, value)
    if originalValues[key] == nil then
        originalValues[key] = value
    end
end

local function getOriginal(key, default)
    return originalValues[key] or default
end

local function setPropertyRecursive(instance, propertyName, value, classNameFilter)
    if classNameFilter then
        if instance:IsA(classNameFilter) then
            pcall(function()
                storeOriginal(instance:GetFullName() .. "." .. propertyName, instance[propertyName])
                instance[propertyName] = value
            end)
        end
    else
        pcall(function()
            storeOriginal(instance:GetFullName() .. "." .. propertyName, instance[propertyName])
            instance[propertyName] = value
        end)
    end

    for _, child in ipairs(instance:GetChildren()) do
        setPropertyRecursive(child, propertyName, value, classNameFilter)
    end
end

local function revertPropertyRecursive(instance, propertyName, classNameFilter)
    local originalKey = instance:GetFullName() .. "." .. propertyName
    if classNameFilter then
        if instance:IsA(classNameFilter) then
            pcall(function() instance[propertyName] = getOriginal(originalKey, instance[propertyName]) end)
        end
    else
        pcall(function() instance[propertyName] = getOriginal(originalKey, instance[propertyName]) end)
    end

    for _, child in ipairs(instance:GetChildren()) do
        revertPropertyRecursive(child, propertyName, classNameFilter)
    end
end

local function toggleVisibilityRecursive(instance, visible, classNameFilter)
    if instance:IsA(classNameFilter) then
        pcall(function()
            storeOriginal(instance:GetFullName() .. ".Visible", instance.Visible)
            instance.Visible = visible
        end)
    end
    for _, child in ipairs(instance:GetChildren()) do
        toggleVisibilityRecursive(child, visible, classNameFilter)
    end
end

local function toggleEnabledRecursive(instance, enabled, classNameFilter)
    if instance:IsA(classNameFilter) then
        pcall(function()
            storeOriginal(instance:GetFullName() .. ".Enabled", instance.Enabled)
            instance.Enabled = enabled
        end)
    end
    for _, child in ipairs(instance:GetChildren()) do
        toggleEnabledRecursive(child, enabled, classNameFilter)
    end
end

--[[
    =========================================================================
    ||                        DEFINIÇÃO DAS FUNÇÕES FPS BOOSTER             ||
    =========================================================================
]]

-- Categoria: Gráficos Essenciais 🖼️
local essentialGraphicsFunctions = {
    {
        name = "Qualidade: Ultra Baixa 📉",
        description = "Nível de qualidade gráfica no mínimo absoluto (Level01).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("settings.Rendering.QualityLevel", settings().Rendering.QualityLevel)
            settings().Rendering.QualityLevel = state and Enum.QualityLevel.Level01 or getOriginal("settings.Rendering.QualityLevel", Enum.QualityLevel.Automatic)
        end
    },
    {
        name = "Qualidade: Baixa  понизить", -- Exemplo de texto diferente (Baixa em Russo)
        description = "Nível de qualidade gráfica baixo (Level03).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("settings.Rendering.QualityLevel_Low", settings().Rendering.QualityLevel)
            settings().Rendering.QualityLevel = state and Enum.QualityLevel.Level03 or getOriginal("settings.Rendering.QualityLevel_Low", Enum.QualityLevel.Automatic)
        end
    },
    {
        name = "Desabilitar Sombras Globais ☀️",
        description = "Remove todas as sombras projetadas por objetos.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.GlobalShadows", Lighting.GlobalShadows)
            Lighting.GlobalShadows = not state -- Invertido: state TRUE = desabilitar, então GlobalShadows = false
        end,
        defaultRevert = function() Lighting.GlobalShadows = getOriginal("Lighting.GlobalShadows", true) end
    },
    {
        name = "Névoa Agressiva 🌫️",
        description = "Reduz drasticamente a distância de renderização com névoa densa.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.FogStart", Lighting.FogStart)
            storeOriginal("Lighting.FogEnd", Lighting.FogEnd)
            Lighting.FogStart = state and 0 or getOriginal("Lighting.FogStart", 0)
            Lighting.FogEnd = state and 50 or getOriginal("Lighting.FogEnd", 100000)
        end
    },
    {
        name = "Sem Efeitos de Atmosfera 🌌",
        description = "Desabilita o objeto Atmosphere da iluminação.",
        pc = true, mobile = true,
        apply = function(state)
            local atmosphere = Lighting:FindFirstChildOfClass("Atmosphere")
            if atmosphere then
                storeOriginal("Atmosphere.Enabled", atmosphere.Enabled)
                atmosphere.Enabled = not state
            end
        end,
        defaultRevert = function()
            local atmosphere = Lighting:FindFirstChildOfClass("Atmosphere")
            if atmosphere then atmosphere.Enabled = getOriginal("Atmosphere.Enabled", true) end
        end
    },
    {
        name = "Simplificar Céu ☁️",
        description = "Remove texturas complexas do Skybox, usando um Sky simples.",
        pc = true, mobile = true,
        apply = function(state)
            local sky = Lighting:FindFirstChildOfClass("Sky")
            if sky then
                if state then
                    storeOriginal("Sky.SkyboxBk", sky.SkyboxBk) -- Armazena uma das texturas como referência
                    sky.SkyboxBk = "" sky.SkyboxDn = "" sky.SkyboxFt = ""
                    sky.SkyboxLf = "" sky.SkyboxRt = "" sky.SkyboxUp = ""
                    sky.CelestialBodiesShown = false
                else
                    sky.SkyboxBk = getOriginal("Sky.SkyboxBk", "rbxasset://textures/sky/sky512_bk.tex") -- Exemplo de reversão
                    -- Reverter outras texturas seria similar ou usar um Sky padrão
                    sky.CelestialBodiesShown = true
                end
            end
        end
    },
    {
        name = "Baixa Qualidade de Materiais 🧱",
        description = "Usa a variante de baixa qualidade para materiais (se disponível).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("MaterialService.MaterialVariant", MaterialService.MaterialVariant)
            if state then
                -- Isso é mais um conceito, pois MaterialVariant é para assets específicos.
                -- A melhor aproximação é reduzir a fidelidade geral.
                settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
            else
                settings().Rendering.QualityLevel = getOriginal("settings.Rendering.QualityLevel", Enum.QualityLevel.Automatic)
            end
            -- Para verdadeiros MaterialVariants, você precisaria iterar e mudar.
        end
    },
    {
        name = "Otimizar Qualidade Água 💧",
        description = "Reduz a qualidade da renderização da água.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("workspace.Terrain.WaterWaveSize", Workspace.Terrain.WaterWaveSize)
            storeOriginal("workspace.Terrain.WaterWaveSpeed", Workspace.Terrain.WaterWaveSpeed)
            storeOriginal("workspace.Terrain.WaterTransparency", Workspace.Terrain.WaterTransparency)
            if state then
                Workspace.Terrain.WaterWaveSize = 0.05
                Workspace.Terrain.WaterWaveSpeed = 0.1
                Workspace.Terrain.WaterTransparency = 0.8
            else
                Workspace.Terrain.WaterWaveSize = getOriginal("workspace.Terrain.WaterWaveSize", 0.15)
                Workspace.Terrain.WaterWaveSpeed = getOriginal("workspace.Terrain.WaterWaveSpeed", 1)
                Workspace.Terrain.WaterTransparency = getOriginal("workspace.Terrain.WaterTransparency", 0.3)
            end
        end
    },
    {
        name = "Anti-Aliasing Mínimo",
        description = "Força o Anti-Aliasing para o mínimo (geralmente via QualityLevel).",
        pc = true, mobile = false,
        apply = function(state)
            -- Controlado principalmente pelo QualityLevel.
            -- Não há API direta para apenas AA no Roblox.
            if state then
                if settings().Rendering.QualityLevel.Value > Enum.QualityLevel.Level03.Value then
                     settings().Rendering.QualityLevel = Enum.QualityLevel.Level03
                end
            else
                 settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
            end
        end
    },
    {
        name = "Desabilitar Decalques 📜",
        description = "Torna todos os decalques (Decal) invisíveis.",
        pc = true, mobile = true,
        apply = function(state)
            for _, v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("Decal") then
                    storeOriginal(v:GetFullName() .. ".Transparency", v.Transparency)
                    v.Transparency = state and 1 or getOriginal(v:GetFullName() .. ".Transparency", 0)
                end
            end
        end
    }
}

-- Categoria: Jogo e Efeitos Globais ⚙️
local gameAndGlobalEffectsFunctions = {
    {
        name = "Desabilitar Partículas ✨",
        description = "Desativa todos os ParticleEmitters na Workspace.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "ParticleEmitter") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "ParticleEmitter") end
    },
    {
        name = "Desabilitar Explosões (Visual) 💥",
        description = "Reduz o impacto visual de explosões (não o dano).",
        pc = true, mobile = true,
        apply = function(state)
            -- Esta é uma simplificação. Explosões reais são complexas.
            -- Pode desabilitar ParticleEmitters ou Luzes criadas por scripts de explosão.
            -- Aqui, apenas um exemplo genérico de desabilitar efeitos visuais de "Explosion"
            toggleEnabledRecursive(Workspace, not state, "Explosion")
        end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "Explosion") end
    },
    {
        name = "Otimizar Física (Cliente) 🏃",
        description = "Reduz a frequência de cálculo de física para partes não essenciais (experimental).",
        pc = true, mobile = false,
        apply = function(state)
            -- Isso é complexo e arriscado. Exemplo:
            -- game:GetService("RunService"):Set3dRenderingEnabled(not state) -- Isso desabilita TUDO
            -- Uma abordagem mais segura:
            -- for _, part in ipairs(Workspace:GetDescendants()) do
            --  if part:IsA("BasePart") and part.CanCollide and part.Anchored == false then
            --      part:SetNetworkOwner(nil) -- Deixa o servidor cuidar mais
            --  end
            -- end
            -- Para este booster, vamos focar em coisas mais seguras:
            if state then
                Stats:SendRobloxAnalyticsEvent("ClientPhysicsOptimization", {enabled = true})
                 -- Reduzir a densidade de partes não ancoradas dinamicamente (MUITO arriscado)
                -- for _, v in pairs(workspace:GetDescendants()) do
                --     if v:IsA("BasePart") and not v.Anchored then
                --         storeOriginal(v:GetFullName()..".CustomPhysicalProperties", v.CustomPhysicalProperties)
                --         v.CustomPhysicalProperties = PhysicalProperties.new(0.1, v.Friction, v.Elasticity, v.FrictionWeight, v.ElasticityWeight)
                --     end
                -- end
            else
                Stats:SendRobloxAnalyticsEvent("ClientPhysicsOptimization", {enabled = false})
                -- for _, v in pairs(workspace:GetDescendants()) do
                --  if v:IsA("BasePart") and not v.Anchored then
                --      local originalProps = getOriginal(v:GetFullName()..".CustomPhysicalProperties")
                --      if originalProps then v.CustomPhysicalProperties = originalProps end
                --  end
                -- end
            end
            print("Otimização de Física (Cliente): " .. tostring(state) .. " (efeito depende do jogo)")
        end
    },
    {
        name = "Reduzir Efeitos Sonoros 🔊",
        description = "Diminui o volume global de SoundService (não afeta UI).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("SoundService.MasterVolume", game:GetService("SoundService").MasterVolume)
            game:GetService("SoundService").MasterVolume = state and 0.1 or getOriginal("SoundService.MasterVolume", 1)
        end
    },
    {
        name = "Limpar Debris Automaticamente 🗑️",
        description = "Adiciona itens com nome 'Debris' ou 'Trash' ao DebrisService.",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                local function onDescendantAdded(descendant)
                    if string.find(descendant.Name:lower(), "debris") or string.find(descendant.Name:lower(), "trash") then
                        if descendant:IsA("Model") or descendant:IsA("BasePart") then
                            DebrisService:AddItem(descendant, 0.1) -- Remove quase instantaneamente
                        end
                    end
                end
                Workspace.DescendantAdded:Connect(onDescendantAdded)
                -- Poderia ter uma forma de desconectar isso no 'else'
                print("Limpeza Automática de Debris ATIVADA")
            else
                -- Desconectar a função (requer guardar a conexão)
                print("Limpeza Automática de Debris DESATIVADA (se foi conectada)")
            end
        end
    },
    {
        name = "Desabilitar Movimento da Grama 🌿",
        description = "Para Decoration em Terrain.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Workspace.Terrain.Decoration", Workspace.Terrain.Decoration)
            Workspace.Terrain.Decoration = not state
        end,
        defaultRevert = function() Workspace.Terrain.Decoration = getOriginal("Workspace.Terrain.Decoration", true) end
    },
    {
        name = "Desabilitar Roupas (Local) 👕",
        description = "Remove texturas de roupas de todos os personagens (apenas visual local).",
        pc = true, mobile = true,
        apply = function(state)
            for _, player in ipairs(Players:GetPlayers()) do
                local character = player.Character
                if character then
                    for _, child in ipairs(character:GetChildren()) do
                        if child:IsA("Shirt") or child:IsA("Pants") or child:IsA("ShirtGraphic") then
                            storeOriginal(child:GetFullName() .. ".Graphic", child[child:IsA("ShirtGraphic") and "Graphic" or "ShirtTemplate"]) -- Exemplo
                            if state then
                                if child:IsA("ShirtGraphic") then child.Graphic = "" else child[child:IsA("Shirt") and "ShirtTemplate" or "PantsTemplate"] = "" end
                            else
                                if child:IsA("ShirtGraphic") then child.Graphic = getOriginal(child:GetFullName() .. ".Graphic", "")
                                else child[child:IsA("Shirt") and "ShirtTemplate" or "PantsTemplate"] = getOriginal(child:GetFullName() .. ".Graphic", "") end
                            end
                        end
                    end
                end
            end
        end
    },
    {
        name = "Forçar Streaming Baixo (Conceito) 🌍",
        description = "Tenta forçar o StreamingEnabled a ser mais agressivo (se ativado pelo jogo).",
        pc = true, mobile = true,
        apply = function(state)
            if Workspace.StreamingEnabled then
                storeOriginal("Workspace.StreamingMinRadius", Workspace.StreamingMinRadius)
                storeOriginal("Workspace.StreamingTargetRadius", Workspace.StreamingTargetRadius)
                if state then
                    Workspace.StreamingMinRadius = 32
                    Workspace.StreamingTargetRadius = 128
                else
                    Workspace.StreamingMinRadius = getOriginal("Workspace.StreamingMinRadius", 64)
                    Workspace.StreamingTargetRadius = getOriginal("Workspace.StreamingTargetRadius", 1024)
                end
            else
                print("StreamingEnabled não está ativo neste jogo.")
            end
        end
    },
    {
        name = "Reduzir Contagem de Humanoides 🧍",
        description = "Simplifica humanoides distantes (experimental e arriscado).",
        pc = true, mobile = false,
        apply = function(state)
            -- Este é muito arriscado, pode quebrar jogos.
            -- Exemplo: for _, humanoid in ipairs(Workspace:GetDescendants()) do if humanoid:IsA("Humanoid") then ... end end
            print("Reduzir Contagem de Humanoides: Efeito depende do jogo e pode ser instável.")
        end
    },
    {
        name = "Sem Animações (Outros Players) 🕺",
        description = "Tenta parar animações de outros jogadores (localmente).",
        pc = true, mobile = true,
        apply = function(state)
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= localPlayer then
                    local character = player.Character
                    if character then
                        local animator = character:FindFirstChildOfClass("Animator")
                        if animator then
                            if state then
                                animator:Destroy() -- Drástico, mas para o booster...
                            else
                                -- Recriar Animator é complexo, melhor não fazer ou jogo recria.
                            end
                        end
                        local humanoid = character:FindFirstChildOfClass("Humanoid")
                        if humanoid then -- Outra forma mais simples
                            for _, track in ipairs(humanoid:GetPlayingAnimationTracks()) do
                                if state then track:Stop(0) else track:Play(0) end -- Requer guardar quais parou
                            end
                        end
                    end
                end
            end
        end
    },
}

-- Categoria: Renderização Detalhada 🖥️
local detailedRenderingFunctions = {
    {
        name = "RenderFidelity: Performance ⚡",
        description = "Força todas as BaseParts para RenderFidelity Performance.",
        pc = true, mobile = true,
        apply = function(state)
            for _, v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("BasePart") then
                    storeOriginal(v:GetFullName() .. ".RenderFidelity", v.RenderFidelity)
                    v.RenderFidelity = state and Enum.RenderFidelity.Performance or getOriginal(v:GetFullName() .. ".RenderFidelity", Enum.RenderFidelity.Automatic)
                end
            end
        end
    },
    {
        name = "MeshPartLOD: Performance",
        description = "Força MeshParts para Level Of Detail de Performance (se aplicável).",
        pc = true, mobile = true,
        apply = function(state)
            -- RenderFidelity é o controle mais direto para MeshParts.
            -- LOD real (como em Model.LevelOfDetail) é para Modelos com LODs configurados.
            setPropertyRecursive(Workspace, "RenderFidelity", state and Enum.RenderFidelity.Performance or Enum.RenderFidelity.Automatic, "MeshPart")
        end
    },
    {
        name = "ModelLOD: Performance",
        description = "Força Models para Level Of Detail de Performance.",
        pc = true, mobile = true,
        apply = function(state)
            for _, v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("Model") then
                    storeOriginal(v:GetFullName() .. ".LevelOfDetail", v.LevelOfDetail)
                    v.LevelOfDetail = state and Enum.ModelLevelOfDetail.Performance or getOriginal(v:GetFullName() .. ".LevelOfDetail", Enum.ModelLevelOfDetail.Automatic)
                end
            end
        end
    },
    {
        name = "Desabilitar Texturas 🖼️",
        description = "Torna todas as texturas (Texture) invisíveis.",
        pc = true, mobile = true,
        apply = function(state)
            for _, v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("Texture") then
                    storeOriginal(v:GetFullName() .. ".Transparency", v.Transparency)
                    v.Transparency = state and 1 or getOriginal(v:GetFullName() .. ".Transparency", 0)
                end
            end
        end
    },
    {
        name = "Ocultar Beams 💫",
        description = "Torna todos os Beams invisíveis.",
        pc = true, mobile = true,
        apply = function(state) toggleVisibilityRecursive(Workspace, not state, "Beam") end,
        defaultRevert = function() toggleVisibilityRecursive(Workspace, true, "Beam") end
    },
    {
        name = "Ocultar Trails ✨",
        description = "Torna todos os Trails invisíveis.",
        pc = true, mobile = true,
        apply = function(state) toggleVisibilityRecursive(Workspace, not state, "Trail") end,
        defaultRevert = function() toggleVisibilityRecursive(Workspace, true, "Trail") end
    },
    {
        name = "Sem SurfaceAppearance 🎨",
        description = "Remove/Desabilita todas as SurfaceAppearance.",
        pc = true, mobile = true,
        apply = function(state)
            for _, v in ipairs(Workspace:GetDescendants()) do
                if v:IsA("SurfaceAppearance") then
                    -- A melhor forma é desabilitar o parente ou remover, mas é arriscado
                    -- Aqui, tentamos anular seus efeitos se possível (AlphaMode)
                    storeOriginal(v:GetFullName() .. ".AlphaMode", v.AlphaMode)
                    v.AlphaMode = state and Enum.AlphaMode.Transparency or getOriginal(v:GetFullName() .. ".AlphaMode", Enum.AlphaMode.Overlay)
                end
            end
        end
    },
    {
        name = "Sem PartSelectionLasso 🌀",
        description = "Desabilita o efeito visual do lasso de seleção de partes (para devs).",
        pc = true, mobile = false,
        apply = function(state)
            local selectionLasso = game:GetService("CoreGui"):FindFirstChild("PartSelectionLasso", true)
            if selectionLasso then
                selectionLasso.Enabled = not state
            end
        end
    },
    {
        name = "Otimizar Vidro 🧊",
        description = "Reduz a refletividade e transparência do material Vidro.",
        pc = true, mobile = true,
        apply = function(state)
            for _, part in ipairs(Workspace:GetDescendants()) do
                if part:IsA("BasePart") and part.Material == Enum.Material.Glass then
                    storeOriginal(part:GetFullName()..".Reflectance", part.Reflectance)
                    storeOriginal(part:GetFullName()..".Transparency", part.Transparency)
                    if state then
                        part.Reflectance = 0.5
                        part.Transparency = 0.5
                    else
                        part.Reflectance = getOriginal(part:GetFullName()..".Reflectance", 0.4)
                        part.Transparency = getOriginal(part:GetFullName()..".Transparency", 0.1)
                    end
                end
            end
        end
    },
    {
        name = "Forçar Wireframe (Não Funcional) 🕸️",
        description = "Tenta mostrar tudo em wireframe (geralmente não acessível).",
        pc = true, mobile = false,
        apply = function(state)
            -- Roblox não expõe uma API para modo wireframe global para scripts locais.
            print("Modo Wireframe não é suportado diretamente por scripts.")
        end
    }
}

-- Categoria: Limpeza de Cena 🧹
local sceneCleanupFunctions = {
    {
        name = "Remover Decalques Pequenos 🤏",
        description = "Remove decalques com tamanho menor que X (difícil de implementar bem).",
        pc = true, mobile = true,
        apply = function(state) print("Remover Decalques Pequenos: Lógica complexa, não implementado.") end
    },
    {
        name = "Remover Luzes Inativas 💡",
        description = "Remove PointLights, SpotLights, SurfaceLights que estão desabilitadas.",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                for _, light in ipairs(Workspace:GetDescendants()) do
                    if (light:IsA("PointLight") or light:IsA("SpotLight") or light:IsA("SurfaceLight")) and not light.Enabled then
                        light:Destroy()
                    end
                end
            end
        end
    },
    {
        name = "Ocultar Partes Transparentes 👻",
        description = "Define a transparência de partes já muito transparentes para 1.",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                for _, part in ipairs(Workspace:GetDescendants()) do
                    if part:IsA("BasePart") and part.Transparency > 0.85 and part.Transparency < 1 then
                        storeOriginal(part:GetFullName()..".Transparency", part.Transparency)
                        part.Transparency = 1
                    end
                end
            else
                for _, part in ipairs(Workspace:GetDescendants()) do
                    if part:IsA("BasePart") and part.Transparency == 1 then
                         local original = getOriginal(part:GetFullName()..".Transparency")
                         if original and original > 0.85 and original < 1 then part.Transparency = original end
                    end
                end
            end
        end
    },
    {
        name = "Remover Partículas Órfãs 🌬️",
        description = "Remove ParticleEmitters que não têm um parente BasePart (raro).",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                for _, pe in ipairs(Workspace:GetDescendants()) do
                    if pe:IsA("ParticleEmitter") and not pe.Parent:IsA("BasePart") then
                        pe:Destroy()
                    end
                end
            end
        end
    },
    {
        name = "Sem Colisão para Partes Pequenas 🎈",
        description = "Desabilita CanCollide para partes muito pequenas e não ancoradas (PODE QUEBRAR JOGOS).",
        pc = true, mobile = false,
        apply = function(state)
            if state then
                for _, part in ipairs(Workspace:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then
                        local size = part.Size
                        if size.X < 0.5 and size.Y < 0.5 and size.Z < 0.5 then
                            storeOriginal(part:GetFullName()..".CanCollide", part.CanCollide)
                            part.CanCollide = false
                        end
                    end
                end
            else
                 for _, part in ipairs(Workspace:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then
                        local size = part.Size
                        if size.X < 0.5 and size.Y < 0.5 and size.Z < 0.5 then
                             part.CanCollide = getOriginal(part:GetFullName()..".CanCollide", true)
                        end
                    end
                end
            end
        end
    },
    {
        name = "Ocultar Terreno Distante 🏞️",
        description = "Usa Fog para simular ocultação de terreno distante.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.FogColor", Lighting.FogColor)
            if state then
                Lighting.FogColor = Workspace.Terrain.MaterialColors[Enum.Material.Air] or Color3.new(0.5,0.5,0.5)
                Lighting.FogStart = 0
                Lighting.FogEnd = 200 -- Ajustar
            else
                Lighting.FogColor = getOriginal("Lighting.FogColor", Color3.new(0.7,0.7,0.7))
                Lighting.FogStart = 0
                Lighting.FogEnd = 100000
            end
        end
    },
    {
        name = "Desabilitar Ropes/Constraints (Visual) ⛓️",
        description = "Torna RopeConstraints e outros constraints invisíveis.",
        pc = true, mobile = true,
        apply = function(state)
            toggleVisibilityRecursive(Workspace, not state, "Constraint") -- Abrange RopeConstraint, SpringConstraint etc.
        end,
        defaultRevert = function() toggleVisibilityRecursive(Workspace, true, "Constraint") end
    },
    {
        name = "Limpar Acessórios (Local) 👒",
        description = "Remove acessórios do seu personagem e de outros (localmente).",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                for _, player in ipairs(Players:GetPlayers()) do
                    local char = player.Character
                    if char then
                        for _, child in ipairs(char:GetChildren()) do
                            if child:IsA("Accessory") then
                                child.Handle.Transparency = 1 -- Simplificado, poderia destruir
                            end
                        end
                    end
                end
            else
                -- Reverter é complexo, pois exigiria recarregar os acessórios
            end
        end
    },
    {
        name = "Sem Efeitos de GUI (Workspace) 🗺️",
        description = "Desabilita BillboardGuis e SurfaceGuis na Workspace.",
        pc = true, mobile = true,
        apply = function(state)
            toggleEnabledRecursive(Workspace, not state, "BillboardGui")
            toggleEnabledRecursive(Workspace, not state, "SurfaceGui")
        end,
        defaultRevert = function()
            toggleEnabledRecursive(Workspace, true, "BillboardGui")
            toggleEnabledRecursive(Workspace, true, "SurfaceGui")
        end
    },
    {
        name = "Ocultar Decorações de Terreno 🌾",
        description = "Remove grama e outras decorações do terreno.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Workspace.Terrain.Decoration", Workspace.Terrain.Decoration)
            Workspace.Terrain.Decoration = not state
        end,
        defaultRevert = function() Workspace.Terrain.Decoration = getOriginal("Workspace.Terrain.Decoration", true) end
    }
}

-- Categoria: Iluminação Avançada ✨
local advancedLightingFunctions = {
    {
        name = "Tecnologia: Compatibility 💡",
        description = "Muda a tecnologia de iluminação para Compatibility (mais simples).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.Technology", Lighting.Technology)
            Lighting.Technology = state and Enum.Technology.Compatibility or getOriginal("Lighting.Technology", Enum.Technology.Future)
        end
    },
    {
        name = "Tecnologia: Voxel",
        description = "Muda a tecnologia de iluminação para Voxel.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.Technology_Voxel", Lighting.Technology)
            Lighting.Technology = state and Enum.Technology.Voxel or getOriginal("Lighting.Technology_Voxel", Enum.Technology.Future)
        end
    },
    {
        name = "Sem PointLights 🏮",
        description = "Desabilita todas as PointLights.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "PointLight") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "PointLight") end
    },
    {
        name = "Sem SpotLights 🔦",
        description = "Desabilita todas as SpotLights.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "SpotLight") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "SpotLight") end
    },
    {
        name = "Sem SurfaceLights 🖼️",
        description = "Desabilita todas as SurfaceLights.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "SurfaceLight") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "SurfaceLight") end
    },
    {
        name = "Brilho Mínimo ✨",
        description = "Reduz o brilho global da iluminação.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.Brightness", Lighting.Brightness)
            Lighting.Brightness = state and 0 or getOriginal("Lighting.Brightness", 2)
        end
    },
    {
        name = "Sem Ambient Occlusion (SSAO) 🌑",
        description = "Desabilita ScreenSpaceAmbientOcclusion se ativo.",
        pc = true, mobile = true,
        apply = function(state)
            local ssao = Lighting:FindFirstChildOfClass("ScreenSpaceAmbientOcclusion")
            if ssao then
                storeOriginal("SSAO.Enabled", ssao.Enabled)
                ssao.Enabled = not state
            end
        end,
        defaultRevert = function()
            local ssao = Lighting:FindFirstChildOfClass("ScreenSpaceAmbientOcclusion")
            if ssao then ssao.Enabled = getOriginal("SSAO.Enabled", true) end
        end
    },
    {
        name = "Cor Ambiente Escura 🌃",
        description = "Define Ambient e OutdoorAmbient para cores escuras.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.Ambient", Lighting.Ambient)
            storeOriginal("Lighting.OutdoorAmbient", Lighting.OutdoorAmbient)
            if state then
                Lighting.Ambient = Color3.new(0.1,0.1,0.1)
                Lighting.OutdoorAmbient = Color3.new(0.1,0.1,0.1)
            else
                Lighting.Ambient = getOriginal("Lighting.Ambient", Color3.new(0.5,0.5,0.5))
                Lighting.OutdoorAmbient = getOriginal("Lighting.OutdoorAmbient", Color3.new(0.5,0.5,0.5))
            end
        end
    },
    {
        name = "Sem ClockTime/TimeOfDay 🕛",
        description = "Define ClockTime para um valor fixo (ex: meio-dia).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.ClockTime", Lighting.ClockTime)
            -- Para evitar que o jogo mude, teria que usar um loop, o que não é ideal para um booster.
            -- Apenas setar uma vez pode ser sobrescrito.
            if state then Lighting.ClockTime = 12 else Lighting.ClockTime = getOriginal("Lighting.ClockTime", 14) end
        end
    },
    {
        name = "Sem EnvironmentSpecularScale 🌟",
        description = "Zera o EnvironmentSpecularScale da iluminação.",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Lighting.EnvironmentSpecularScale", Lighting.EnvironmentSpecularScale)
            Lighting.EnvironmentSpecularScale = state and 0 or getOriginal("Lighting.EnvironmentSpecularScale", 1)
        end
    }
}

-- Categoria: Efeitos Visuais Específicos 💥
local specificVisualEffectsFunctions = {
    {
        name = "Sem Bloom 🌸",
        description = "Desabilita o efeito Bloom.",
        pc = true, mobile = true,
        apply = function(state)
            local bloom = Lighting:FindFirstChildOfClass("BloomEffect") or camera:FindFirstChildOfClass("BloomEffect")
            if bloom then storeOriginal(bloom:GetFullName()..".Enabled", bloom.Enabled); bloom.Enabled = not state end
        end,
        defaultRevert = function()
            local bloom = Lighting:FindFirstChildOfClass("BloomEffect") or camera:FindFirstChildOfClass("BloomEffect")
            if bloom then bloom.Enabled = getOriginal(bloom:GetFullName()..".Enabled", true) end
        end
    },
    {
        name = "Sem Blur 👓",
        description = "Desabilita o efeito Blur.",
        pc = true, mobile = true,
        apply = function(state)
            local blur = Lighting:FindFirstChildOfClass("BlurEffect") or camera:FindFirstChildOfClass("BlurEffect")
            if blur then storeOriginal(blur:GetFullName()..".Enabled", blur.Enabled); blur.Enabled = not state end
        end,
        defaultRevert = function()
            local blur = Lighting:FindFirstChildOfClass("BlurEffect") or camera:FindFirstChildOfClass("BlurEffect")
            if blur then blur.Enabled = getOriginal(blur:GetFullName()..".Enabled", true) end
        end
    },
    {
        name = "Sem ColorCorrection 🌈",
        description = "Desabilita o efeito ColorCorrection.",
        pc = true, mobile = true,
        apply = function(state)
            local cc = Lighting:FindFirstChildOfClass("ColorCorrectionEffect") or camera:FindFirstChildOfClass("ColorCorrectionEffect")
            if cc then storeOriginal(cc:GetFullName()..".Enabled", cc.Enabled); cc.Enabled = not state end
        end,
        defaultRevert = function()
            local cc = Lighting:FindFirstChildOfClass("ColorCorrectionEffect") or camera:FindFirstChildOfClass("ColorCorrectionEffect")
            if cc then cc.Enabled = getOriginal(cc:GetFullName()..".Enabled", true) end
        end
    },
    {
        name = "Sem SunRays ☀️",
        description = "Desabilita o efeito SunRays.",
        pc = true, mobile = true,
        apply = function(state)
            local sunrays = Lighting:FindFirstChildOfClass("SunRaysEffect") or camera:FindFirstChildOfClass("SunRaysEffect")
            if sunrays then storeOriginal(sunrays:GetFullName()..".Enabled", sunrays.Enabled); sunrays.Enabled = not state end
        end,
        defaultRevert = function()
            local sunrays = Lighting:FindFirstChildOfClass("SunRaysEffect") or camera:FindFirstChildOfClass("SunRaysEffect")
            if sunrays then sunrays.Enabled = getOriginal(sunrays:GetFullName()..".Enabled", true) end
        end
    },
    {
        name = "Sem DepthOfField 📷",
        description = "Desabilita o efeito DepthOfField.",
        pc = true, mobile = true,
        apply = function(state)
            local dof = Lighting:FindFirstChildOfClass("DepthOfFieldEffect") or camera:FindFirstChildOfClass("DepthOfFieldEffect")
            if dof then storeOriginal(dof:GetFullName()..".Enabled", dof.Enabled); dof.Enabled = not state end
        end,
        defaultRevert = function()
            local dof = Lighting:FindFirstChildOfClass("DepthOfFieldEffect") or camera:FindFirstChildOfClass("DepthOfFieldEffect")
            if dof then dof.Enabled = getOriginal(dof:GetFullName()..".Enabled", true) end
        end
    },
    {
        name = "Desabilitar Fogo 🔥",
        description = "Desabilita instâncias de Fogo.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "Fire") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "Fire") end
    },
    {
        name = "Desabilitar Fumaça 💨",
        description = "Desabilita instâncias de Fumaça.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "Smoke") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "Smoke") end
    },
    {
        name = "Desabilitar Faíscas ✨",
        description = "Desabilita instâncias de Sparkles.",
        pc = true, mobile = true,
        apply = function(state) toggleEnabledRecursive(Workspace, not state, "Sparkles") end,
        defaultRevert = function() toggleEnabledRecursive(Workspace, true, "Sparkles") end
    },
    {
        name = "Ocultar SelectionBox/Sphere 🎯",
        description = "Torna SelectionBox e SelectionSphere invisíveis.",
        pc = true, mobile = false,
        apply = function(state)
            toggleVisibilityRecursive(Workspace, not state, "SelectionBox")
            toggleVisibilityRecursive(Workspace, not state, "SelectionSphere")
        end,
        defaultRevert = function()
            toggleVisibilityRecursive(Workspace, true, "SelectionBox")
            toggleVisibilityRecursive(Workspace, true, "SelectionSphere")
        end
    },
    {
        name = "Sem Highlights Visuais ✨",
        description = "Desabilita instâncias de Highlight.",
        pc = true, mobile = true,
        apply = function(state)
            for _, h in ipairs(Workspace:GetDescendants()) do
                if h:IsA("Highlight") then
                    storeOriginal(h:GetFullName()..".Enabled", h.Enabled)
                    h.Enabled = not state
                end
            end
        end,
        defaultRevert = function()
             for _, h in ipairs(Workspace:GetDescendants()) do
                if h:IsA("Highlight") then
                    h.Enabled = getOriginal(h:GetFullName()..".Enabled", true)
                end
            end
        end
    }
}

-- Categoria: Interface e Câmera 👁️‍🗨️
local uiAndCameraFunctions = {
    {
        name = "Ocultar Nomes de Jogadores 👤",
        description = "Desabilita a exibição de nomes e vida sobre as cabeças.",
        pc = true, mobile = true,
        apply = function(state)
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                    if humanoid then
                        storeOriginal(player:GetFullName()..".DisplayNameDistance", humanoid.DisplayDistanceType)
                        humanoid.DisplayDistanceType = state and Enum.HumanoidDisplayDistanceType.None or getOriginal(player:GetFullName()..".DisplayNameDistance", Enum.HumanoidDisplayDistanceType.Viewer)
                        humanoid.HealthDisplayType = state and Enum.HumanoidHealthDisplayType.AlwaysOff or Enum.HumanoidHealthDisplayType.AlwaysOn
                    end
                end
            end
            -- Para novos jogadores:
            Players.PlayerAdded:Connect(function(player)
                player.CharacterAdded:Connect(function(character)
                     if functionsState["Ocultar Nomes de Jogadores 👤"] then -- Verifica se a função está ativa
                        local humanoid = character:FindFirstChildOfClass("Humanoid")
                        if humanoid then
                            humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
                            humanoid.HealthDisplayType = Enum.HumanoidHealthDisplayType.AlwaysOff
                        end
                    end
                end)
            end)
        end
    },
    {
        name = "Ocultar Bolhas de Chat 💬",
        description = "Desabilita as bolhas de chat sobre os jogadores.",
        pc = true, mobile = true,
        apply = function(state)
            local chatService = game:GetService("Chat")
            storeOriginal("ChatService.BubbleChatEnabled", chatService.BubbleChatEnabled)
            chatService.BubbleChatEnabled = not state
        end,
        defaultRevert = function()
            local chatService = game:GetService("Chat")
            chatService.BubbleChatEnabled = getOriginal("ChatService.BubbleChatEnabled", true)
        end
    },
    {
        name = "Campo de Visão (FOV) Menor 🔭",
        description = "Reduz o campo de visão da câmera (pode ajudar em telas menores).",
        pc = true, mobile = true,
        apply = function(state)
            storeOriginal("Camera.FieldOfView", camera.FieldOfView)
            camera.FieldOfView = state and 50 or getOriginal("Camera.FieldOfView", 70)
        end
    },
    {
        name = "Sem Efeitos de Câmera 😵",
        description = "Tenta remover scripts de câmera que causam tremor/efeitos.",
        pc = true, mobile = true,
        apply = function(state)
            -- Isso é genérico, pode não funcionar em todos os jogos.
            if state then
                local camScripts = camera:FindFirstChild("CameraScripts")
                if camScripts then
                    for _, script in ipairs(camScripts:GetChildren()) do
                        if script:IsA("LocalScript") and string.match(script.Name:lower(), "shake|effect|bob") then
                            storeOriginal(script:GetFullName()..".Disabled", script.Disabled)
                            script.Disabled = true
                        end
                    end
                end
            else
                local camScripts = camera:FindFirstChild("CameraScripts")
                if camScripts then
                    for _, script in ipairs(camScripts:GetChildren()) do
                        if script:IsA("LocalScript") and string.match(script.Name:lower(), "shake|effect|bob") then
                            script.Disabled = getOriginal(script:GetFullName()..".Disabled", false)
                        end
                    end
                end
            end
        end
    },
    {
        name = "Ocultar CoreGui (Chat, Leaderboard) 💻",
        description = "Oculta elementos padrão da CoreGui (CUIDADO, PODE QUEBRAR FUNCIONALIDADES).",
        pc = true, mobile = false, -- Mais problemático em mobile
        apply = function(state)
            pcall(function() game:GetService("StarterGui"):SetCoreGuiEnabled(Enum.CoreGuiType.Chat, not state) end)
            pcall(function() game:GetService("StarterGui"):SetCoreGuiEnabled(Enum.CoreGuiType.PlayerList, not state) end)
            pcall(function() game:GetService("StarterGui"):SetCoreGuiEnabled(Enum.CoreGuiType.Backpack, not state) end)
            -- Não é possível armazenar/reverter SetCoreGuiEnabled facilmente sem saber o estado anterior de todos
        end
    },
    {
        name = "Distância de Render. GUI (Conceito) 🗺️",
        description = "Tenta otimizar GUIs 3D (Billboard/Surface) baseada na distância.",
        pc = true, mobile = true,
        apply = function(state)
            -- Conexão de RenderStepped para isso pode ser custosa.
            -- Melhor feito por jogo específico.
            print("Otimização de GUI 3D por distância: Lógica complexa para genérico.")
        end
    },
    {
        name = "Ocultar Emotes (Outros) 😂",
        description = "Tenta ocultar emotes de outros jogadores (se usarem sistema padrão).",
        pc = true, mobile = true,
        apply = function(state)
            -- Depende de como o jogo implementa emotes.
            -- Se for por animações, a função de parar animações já pode ajudar.
            print("Ocultar Emotes: Efeito depende da implementação do jogo.")
        end
    },
    {
        name = "Simplificar Cursores (Padrão) 🖱️",
        description = "Força o uso do cursor padrão do sistema.",
        pc = true, mobile = false,
        apply = function(state)
            if state then
                mouse.Icon = "" -- Reseta para o padrão do Roblox/Sistema
            end
            -- Reverter requer saber qual era o ícone customizado, se houver.
        end
    },
    {
        name = "Sem Notificações InGame 📢",
        description = "Tenta desabilitar notificações de sistema do jogo (se usarem API padrão).",
        pc = true, mobile = true,
        apply = function(state)
            if state then
                pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Opt-Out", Text = "FPS Booster Active"}) end)
                -- Não há API para desabilitar globalmente, apenas para não enviar mais.
            end
        end
    },
    {
        name = "Otimizar Thumbstick Dinâmico (Mobile)🕹️",
        description = "Ajusta o thumbstick dinâmico para performance.",
        pc = false, mobile = true,
        apply = function(state)
            if UserInputService.TouchEnabled then
                localPlayer.DevTouchMovementMode = state and Enum.DevTouchMovementMode.Scriptable or Enum.DevTouchMovementMode.UserChoice
            end
        end
    }
}


local allFunctionCategories = {
    {"Gráficos Essenciais 🖼️", essentialGraphicsFunctions},
    {"Jogo e Efeitos Globais ⚙️", gameAndGlobalEffectsFunctions},
    {"Renderização Detalhada 🖥️", detailedRenderingFunctions},
    {"Limpeza de Cena 🧹", sceneCleanupFunctions},
    {"Iluminação Avançada ✨", advancedLightingFunctions},
    {"Efeitos Visuais Específicos 💥", specificVisualEffectsFunctions},
    {"Interface e Câmera 👁️‍🗨️", uiAndCameraFunctions}
}

--[[
    =========================================================================
    ||                     CRIAÇÃO DINÂMICA DAS ABAS E FUNÇÕES             ||
    =========================================================================
]]
local function createToggleButton(parentFrame, funcData)
    local buttonFrame = Instance.new("Frame")
    buttonFrame.Name = funcData.name:gsub("%s+", "") -- Nome sem espaços para referência
    buttonFrame.Parent = parentFrame
    buttonFrame.Size = UDim2.new(1, -10, 0, 45) -- Altura do botão
    buttonFrame.BackgroundColor3 = Color3.fromRGB(40,40,40)
    buttonFrame.BorderColor3 = Color3.fromRGB(0,220,0)
    buttonFrame.BorderSizePixel = 1
    local btnCorner = corner:Clone()
    btnCorner.CornerRadius = UDim.new(0,6)
    btnCorner.Parent = buttonFrame

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Parent = buttonFrame
    nameLabel.Size = UDim2.new(0.7, -5, 1, 0)
    nameLabel.Position = UDim2.new(0, 10, 0, 0) -- Padding esquerdo
    nameLabel.BackgroundTransparency = 1
    nameLabel.Font = Enum.Font.Gotham
    nameLabel.Text = funcData.name
    nameLabel.TextColor3 = Color3.fromRGB(230,230,230)
    nameLabel.TextXAlignment = Enum.TextXAlignment.Left
    nameLabel.TextSize = 15
    if UserInputService.TouchEnabled then nameLabel.TextSize = 13 end


    local toggleButton = Instance.new("TextButton")
    toggleButton.Name = "ToggleButton"
    toggleButton.Parent = buttonFrame
    toggleButton.Size = UDim2.new(0.25, 0, 0.7, 0) -- Ligeiramente menor e mais à direita
    toggleButton.Position = UDim2.new(0.72, 0, 0.15, 0)
    toggleButton.BackgroundColor3 = Color3.fromRGB(180,50,50) -- Vermelho (OFF)
    toggleButton.TextColor3 = Color3.fromRGB(255,255,255)
    toggleButton.Font = Enum.Font.GothamBold
    toggleButton.Text = "OFF"
    toggleButton.TextSize = 14
    if UserInputService.TouchEnabled then toggleButton.TextSize = 12 end
    local toggleBtnCorner = corner:Clone()
    toggleBtnCorner.Parent = toggleButton

    functionsState[funcData.name] = false -- Inicializa como OFF

    toggleButton.MouseButton1Click:Connect(function()
        functionsState[funcData.name] = not functionsState[funcData.name]
        if functionsState[funcData.name] then
            toggleButton.BackgroundColor3 = Color3.fromRGB(50,180,50) -- Verde (ON)
            toggleButton.Text = "ON"
            pcall(funcData.apply, true)
        else
            toggleButton.BackgroundColor3 = Color3.fromRGB(180,50,50) -- Vermelho (OFF)
            toggleButton.Text = "OFF"
            if funcData.defaultRevert then
                pcall(funcData.defaultRevert)
            else
                pcall(funcData.apply, false) -- Chama com false se não houver reversão customizada
            end
        end
    end)

    local descLabel = Instance.new("TextLabel")
    descLabel.Name = "DescriptionTooltip"
    descLabel.Parent = gui
    descLabel.Size = UDim2.new(0, 220, 0, 60)
    descLabel.BackgroundColor3 = Color3.fromRGB(25,25,25)
    descLabel.BorderColor3 = Color3.fromRGB(0,220,0)
    descLabel.BorderSizePixel = 1
    descLabel.TextColor3 = Color3.fromRGB(210,210,210)
    descLabel.Font = Enum.Font.Code
    descLabel.TextWrapped = true
    descLabel.TextSize = 12
    descLabel.PaddingLeft = UDim.new(0,5)
    descLabel.PaddingRight = UDim.new(0,5)
    descLabel.PaddingTop = UDim.new(0,5)
    descLabel.PaddingBottom = UDim.new(0,5)
    descLabel.Visible = false
    descLabel.ZIndex = 100 -- Alto ZIndex
    local descCorner = corner:Clone()
    descCorner.Parent = descLabel

    buttonFrame.MouseEnter:Connect(function()
        descLabel.Text = funcData.description
        descLabel.Position = UDim2.new(0, mouse.X + 15, 0, mouse.Y + 15)
        descLabel.Visible = true
    end)
    buttonFrame.MouseLeave:Connect(function()
        descLabel.Visible = false
    end)
    mainFrame.MouseMoved:Connect(function(x,y) -- Use mainFrame para mobile
         if descLabel.Visible and buttonFrame:IsAncestorOf(UserInputService:GetElementUnderMouse()) then
            descLabel.Position = UDim2.new(0, x + 15, 0, y + 15)
         end
    end)
    if UserInputService.TouchEnabled then -- Tooltip para mobile precisa de clique extra
        local descVisible = false
        buttonFrame.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.Touch then
                descVisible = not descVisible
                if descVisible then
                    descLabel.Text = funcData.description
                    descLabel.Position = UDim2.new(0.5, -descLabel.AbsoluteSize.X/2, 0.5, -descLabel.AbsoluteSize.Y/2) -- Centraliza tooltip
                    descLabel.Visible = true
                else
                    descLabel.Visible = false
                end
            end
        end)
    end

    return buttonFrame
end

local function switchTab(tabName, functions)
    if currentTab == tabName then return end
    currentTab = tabName

    for _, child in ipairs(contentFrame:GetChildren()) do
        if child:IsA("Frame") or child:IsA("UIListLayout") or child:IsA("UIPadding") then -- Limpa conteúdo anterior
            child:Destroy()
        end
    end

    for _, child in ipairs(tabsFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child.BackgroundColor3 = (child.Name == tabName .. "_TabButton") and Color3.fromRGB(0,200,0) or Color3.fromRGB(50,50,50)
            child.TextColor3 = (child.Name == tabName .. "_TabButton") and Color3.fromRGB(10,10,10) or Color3.fromRGB(0,220,0)
            child.Font = (child.Name == tabName .. "_TabButton") and Enum.Font.GothamBold or Enum.Font.GothamSemibold
        end
    end

    local listLayout = Instance.new("UIListLayout")
    listLayout.Parent = contentFrame
    listLayout.Padding = UDim.new(0, 8)
    listLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

    local padding = Instance.new("UIPadding")
    padding.Parent = contentFrame
    padding.PaddingTop = UDim.new(0,10)
    padding.PaddingBottom = UDim.new(0,10)
    padding.PaddingLeft = UDim.new(0,5)
    padding.PaddingRight = UDim.new(0,5)


    for i, funcData in ipairs(functions) do
        local isMobile = UserInputService.TouchEnabled
        local showFunc = (isMobile and funcData.mobile) or (not isMobile and funcData.pc)

        if showFunc then
            local funcButtonFrame = createToggleButton(contentFrame, funcData)
            funcButtonFrame.LayoutOrder = i
        end
    end
    contentFrame.CanvasSize = UDim2.new(0,0,0, listLayout.AbsoluteContentSize.Y + padding.PaddingTop.Offset + padding.PaddingBottom.Offset)
end

local numTabs = #allFunctionCategories
local tabButtonWidthFraction = 1 / numTabs
local spacing = 0.01 -- Espaçamento relativo entre botões de aba
local actualTabButtonWidth = tabButtonWidthFraction - (spacing * (numTabs - 1) / numTabs)
local currentTabX = 0

for i, categoryData in ipairs(allFunctionCategories) do
    local categoryName = categoryData[1]
    local functions = categoryData[2]

    local tabButton = Instance.new("TextButton")
    tabButton.Name = categoryName .. "_TabButton"
    tabButton.Parent = tabsFrame
    tabButton.Size = UDim2.new(actualTabButtonWidth, -2, 1, 0) -- -2 para pequeno espaço visual
    tabButton.Position = UDim2.new(currentTabX, (i-1) * 2, 0, 0)
    tabButton.BackgroundColor3 = Color3.fromRGB(50,50,50)
    tabButton.BorderColor3 = Color3.fromRGB(0,220,0)
    tabButton.BorderSizePixel = 1
    tabButton.TextColor3 = Color3.fromRGB(0,220,0)
    tabButton.Font = Enum.Font.GothamSemibold
    tabButton.Text = categoryName
    tabButton.TextSize = 12
    tabButton.TextWrapped = true
    if UserInputService.TouchEnabled then tabButton.TextSize = 9; tabButton.TextWrapped = true end
    local tabCorner = corner:Clone()
    tabCorner.CornerRadius = UDim.new(0,5)
    tabCorner.Parent = tabButton

    currentTabX = currentTabX + actualTabButtonWidth + spacing

    tabButton.MouseButton1Click:Connect(function()
        switchTab(categoryName, functions)
    end)

    if i == 1 then -- Abre a primeira aba por padrão
        switchTab(categoryName, functions)
    end
end

print("LOSTsCRIPTS - FPS Booster V2 por ogart CARREGADO! ✨ Interface aprimorada, mais funções, sem keys!")
