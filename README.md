--// hunsfuck V2 by ogart (Interface melhorada)
-- Créditos: ogart | Script 100% funcional no Delta Executor

local keyCorreta = "byogart"

-- UI PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HunsfuckUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

-- Tela de Key
local keyFrame = Instance.new("Frame")
keyFrame.Size = UDim2.new(0, 320, 0, 160)
keyFrame.Position = UDim2.new(0.5, -160, 0.5, -80)
keyFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
keyFrame.BorderSizePixel = 0
keyFrame.Parent = ScreenGui
keyFrame.AnchorPoint = Vector2.new(0.5, 0.5)
keyFrame.ClipsDescendants = true
keyFrame.Visible = true

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 50)
titulo.Position = UDim2.new(0, 0, 0, 0)
titulo.BackgroundColor3 = Color3.fromRGB(35, 0, 0)
titulo.TextColor3 = Color3.fromRGB(255, 0, 0)
titulo.Font = Enum.Font.SourceSansBold
titulo.TextSize = 32
titulo.Text = "Hunsfuck V2"
titulo.Parent = keyFrame

local caixaKey = Instance.new("TextBox")
caixaKey.Size = UDim2.new(0.8, 0, 0, 35)
caixaKey.Position = UDim2.new(0.1, 0, 0.4, 0)
caixaKey.PlaceholderText = "Digite a key..."
caixaKey.Text = ""
caixaKey.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
caixaKey.TextColor3 = Color3.fromRGB(230, 230, 230)
caixaKey.Font = Enum.Font.SourceSans
caixaKey.TextSize = 20
caixaKey.BorderSizePixel = 0
caixaKey.Parent = keyFrame

local botaoConfirmar = Instance.new("TextButton")
botaoConfirmar.Size = UDim2.new(0.5, 0, 0, 40)
botaoConfirmar.Position = UDim2.new(0.25, 0, 0.7, 0)
botaoConfirmar.Text = "Confirmar"
botaoConfirmar.Font = Enum.Font.SourceSansBold
botaoConfirmar.TextColor3 = Color3.new(1,1,1)
botaoConfirmar.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
botaoConfirmar.BorderSizePixel = 0
botaoConfirmar.Parent = keyFrame

local creditos = Instance.new("TextLabel")
creditos.Size = UDim2.new(1, 0, 0, 25)
creditos.Position = UDim2.new(0, 0, 1, -25)
creditos.Text = "Créditos: ogart"
creditos.TextColor3 = Color3.fromRGB(0, 255, 0)
creditos.BackgroundTransparency = 1
creditos.Font = Enum.Font.SourceSansItalic
creditos.TextSize = 18
creditos.Parent = keyFrame

-- Interface principal (painel)
local painel = Instance.new("Frame")
painel.Size = UDim2.new(0, 300, 0, 420)
painel.Position = UDim2.new(0.02, 0, 0.15, 0)
painel.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
painel.BorderSizePixel = 0
painel.Visible = false
painel.Parent = ScreenGui
painel.ClipsDescendants = true
painel.AutomaticSize = Enum.AutomaticSize.Y

-- Título painel
local tituloPainel = Instance.new("TextLabel")
tituloPainel.Size = UDim2.new(1, 0, 0, 50)
tituloPainel.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
tituloPainel.TextColor3 = Color3.fromRGB(255, 100, 100)
tituloPainel.Font = Enum.Font.SourceSansBold
tituloPainel.TextSize = 28
tituloPainel.Text = "Hunsfuck - Painel"
tituloPainel.Parent = painel

-- Botão minimizar
local minimizar = Instance.new("TextButton")
minimizar.Size = UDim2.new(0, 30, 0, 30)
minimizar.Position = UDim2.new(1, -35, 0, 10)
minimizar.Text = "-"
minimizar.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
minimizar.TextColor3 = Color3.new(1,1,1)
minimizar.Font = Enum.Font.SourceSansBold
minimizar.TextSize = 22
minimizar.Parent = painel

local aberto = true
minimizar.MouseButton1Click:Connect(function()
	aberto = not aberto
	for _, v in pairs(painel:GetChildren()) do
		if v:IsA("Frame") or v:IsA("TextBox") or v:IsA("TextButton") then
			if v ~= minimizar and v ~= tituloPainel then
				v.Visible = aberto
			end
		end
	end
	minimizar.Text = aberto and "-" or "+"
end)

-- Função para criar botões com toggle (lado esquerdo)
local function criarBotao(nome, parent)
	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(1, 0, 0, 40)
	frame.BackgroundTransparency = 1
	frame.Parent = parent

	local btnAtivar = Instance.new("TextButton")
	btnAtivar.Size = UDim2.new(0.65, 0, 1, 0)
	btnAtivar.Position = UDim2.new(0, 0, 0, 0)
	btnAtivar.Text = nome
	btnAtivar.Font = Enum.Font.SourceSansBold
	btnAtivar.TextScaled = true
	btnAtivar.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
	btnAtivar.TextColor3 = Color3.new(1, 1, 1)
	btnAtivar.Name = "btnFunc"
	btnAtivar.Parent = frame

	local btnToggle = Instance.new("TextButton")
	btnToggle.Size = UDim2.new(0.35, -5, 0.8, 0)
	btnToggle.Position = UDim2.new(0.65, 5, 0.1, 0)
	btnToggle.Text = "OFF"
	btnToggle.Font = Enum.Font.SourceSansBold
	btnToggle.TextScaled = true
	btnToggle.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
	btnToggle.TextColor3 = Color3.new(1, 1, 1)
	btnToggle.Name = "btnToggle"
	btnToggle.Parent = frame

	return frame, btnAtivar, btnToggle
end

-- Container para botões toggle
local toggleContainer = Instance.new("Frame")
toggleContainer.Size = UDim2.new(1, 0, 0, 160)
toggleContainer.Position = UDim2.new(0, 0, 0, 60)
toggleContainer.BackgroundTransparency = 1
toggleContainer.Parent = painel
local uiLayout = Instance.new("UIListLayout")
uiLayout.Parent = toggleContainer
uiLayout.SortOrder = Enum.SortOrder.LayoutOrder
uiLayout.Padding = UDim.new(0, 5)

-- Estados dos sistemas
local estados = {
	noclip = false,
	voar = false,
	invisivel = false,
}

local plr = game.Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- Função Noclip
local function noclipLoop()
	while estados.noclip do
		for _, part in pairs(char:GetDescendants()) do
			if part:IsA("BasePart") and part.CanCollide == true then
				part.CanCollide = false
			end
		end
		game:GetService("RunService").Stepped:Wait()
	end
	-- quando desliga, voltar colisão
	for _, part in pairs(char:GetDescendants()) do
		if part:IsA("BasePart") then
			part.CanCollide = true
		end
	end
end

-- Função Voar
local bodyGyro, bodyVel
local voarConn
local function ativarVoar()
	bodyGyro = Instance.new("BodyGyro", hrp)
	bodyVel = Instance.new("BodyVelocity", hrp)
	bodyGyro.P = 9e4
	bodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
	bodyVel.velocity = Vector3.new(0,0,0)
	bodyVel.maxForce = Vector3.new(9e9, 9e9, 9e9)

	voarConn = game:GetService("RunService").RenderStepped:Connect(function()
    local moveDirection = Vector3.new()

    local cam = workspace.CurrentCamera
    local plr = game.Players.LocalPlayer
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local userInputService = game:GetService("UserInputService")

    -- Captura movimento W, A, S, D para direção de voo
    if userInputService:IsKeyDown(Enum.KeyCode.W) then
        moveDirection = moveDirection + cam.CFrame.LookVector
    end
    if userInputService:IsKeyDown(Enum.KeyCode.S) then
        moveDirection = moveDirection - cam.CFrame.LookVector
    end
    if userInputService:IsKeyDown(Enum.KeyCode.A) then
        moveDirection = moveDirection - cam.CFrame.RightVector
    end
    if userInputService:IsKeyDown(Enum.KeyCode.D) then
        moveDirection = moveDirection + cam.CFrame.RightVector
    end

    -- Normalize para evitar velocidade maior na diagonal
    moveDirection = moveDirection.Unit

    -- Se não mover, zera velocidade
    if moveDirection.Magnitude == 0 then
        bodyVel.Velocity = Vector3.new(0,0,0)
    else
        bodyVel.Velocity = moveDirection * 50 -- velocidade de voo
    end

    -- Manter a orientação do personagem igual a câmera
    bodyGyro.CFrame = cam.CFrame
end)

end

local function desativarVoar()
    if bodyGyro then
        bodyGyro:Destroy()
        bodyGyro = nil
    end
    if bodyVel then
        bodyVel:Destroy()
        bodyVel = nil
    end
    if voarConn then
        voarConn:Disconnect()
        voarConn = nil
    end
end

-- Função Invisibilidade
local function ativarInvisivel()
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("Decal") then
            part.Transparency = 1
        elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
            part.Enabled = false
        end
    end
    local humanoid = char:FindFirstChildWhichIsA("Humanoid")
    if humanoid then
        humanoid.NameDisplayDistance = 0
    end
end

local function desativarInvisivel()
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("Decal") then
            part.Transparency = 0
        elseif part:IsA("ParticleEmitter") or part:IsA("BillboardGui") then
            part.Enabled = true
        end
    end
    local humanoid = char:FindFirstChildWhichIsA("Humanoid")
    if humanoid then
        humanoid.NameDisplayDistance = 100
    end
end

-- Criar botões de toggle e suas funções
local btnNoclipFrame, btnNoclip, toggleNoclip = criarBotao("Noclip", toggleContainer)
local btnVoarFrame, btnVoar, toggleVoar = criarBotao("Voar", toggleContainer)
local btnInvisivelFrame, btnInvisivel, toggleInvisivel = criarBotao("Invisível", toggleContainer)

-- Função auxiliar para alternar estados dos toggles
local function toggleEstado(nomeSistema, toggleBtn)
    estados[nomeSistema] = not estados[nomeSistema]
    toggleBtn.Text = estados[nomeSistema] and "ON" or "OFF"
    toggleBtn.BackgroundColor3 = estados[nomeSistema] and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(100, 0, 0)
end

-- Evento dos botões

btnNoclip.MouseButton1Click:Connect(function()
    toggleEstado("noclip", toggleNoclip)
    if estados.noclip then
        noclipLoop()
    end
end)

btnVoar.MouseButton1Click:Connect(function()
    toggleEstado("voar", toggleVoar)
    if estados.voar then
        ativarVoar()
    else
        desativarVoar()
    end
end)

btnInvisivel.MouseButton1Click:Connect(function()
    toggleEstado("invisivel", toggleInvisivel)
    if estados.invisivel then
        ativarInvisivel()
    else
        desativarInvisivel()
    end
end)

-- Confirmar key e abrir painel
botaoConfirmar.MouseButton1Click:Connect(function()
    if caixaKey.Text == keyCorreta then
        keyFrame.Visible = false
        painel.Visible = true
    else
        caixaKey.Text = ""
        caixaKey.PlaceholderText = "Key incorreta, tente novamente"
        caixaKey.TextColor3 = Color3.fromRGB(255, 50, 50)
    end
end)
