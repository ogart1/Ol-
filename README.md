
-- Créditos: ogart | Script 100% funcional no Delta Executor

-- CONFIGURAÇÃO
local keyCorreta = "byogart"

-- UI PRINCIPAL
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "HunsfuckUI"
ScreenGui.ResetOnSpawn = false

-- Tela de Key
local keyFrame = Instance.new("Frame", ScreenGui)
keyFrame.Size = UDim2.new(0, 300, 0, 150)
keyFrame.Position = UDim2.new(0.5, -150, 0.5, -75)
keyFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
keyFrame.BorderSizePixel = 0

local titulo = Instance.new("TextLabel", keyFrame)
titulo.Size = UDim2.new(1, 0, 0.3, 0)
titulo.Text = "hunsfuck"
titulo.TextColor3 = Color3.fromRGB(255, 0, 0)
titulo.BackgroundTransparency = 1
titulo.Font = Enum.Font.SourceSansBold
titulo.TextScaled = true

local creditos = Instance.new("TextLabel", keyFrame)
creditos.Size = UDim2.new(1, 0, 0.2, 0)
creditos.Position = UDim2.new(0, 0, 0.8, 0)
creditos.Text = "Créditos: ogart"
creditos.TextColor3 = Color3.fromRGB(0, 255, 0)
creditos.BackgroundTransparency = 1
creditos.Font = Enum.Font.SourceSans
creditos.TextScaled = true

local caixaKey = Instance.new("TextBox", keyFrame)
caixaKey.Size = UDim2.new(0.8, 0, 0.2, 0)
caixaKey.Position = UDim2.new(0.1, 0, 0.4, 0)
caixaKey.PlaceholderText = "Digite a key..."
caixaKey.Text = ""
caixaKey.Font = Enum.Font.SourceSans
caixaKey.TextColor3 = Color3.new(1, 1, 1)
caixaKey.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
caixaKey.BorderSizePixel = 0

local botaoConfirmar = Instance.new("TextButton", keyFrame)
botaoConfirmar.Size = UDim2.new(0.5, 0, 0.2, 0)
botaoConfirmar.Position = UDim2.new(0.25, 0, 0.65, 0)
botaoConfirmar.Text = "Confirmar"
botaoConfirmar.Font = Enum.Font.SourceSansBold
botaoConfirmar.TextColor3 = Color3.new(1,1,1)
botaoConfirmar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
botaoConfirmar.BorderSizePixel = 0

-- INTERFACE FUNCIONAL
local function criarBotao(nome, parent)
	local frame = Instance.new("Frame", parent)
	frame.Size = UDim2.new(1, 0, 0, 40)
	frame.BackgroundTransparency = 1
	frame.LayoutOrder = #parent:GetChildren() + 1
	
	local btnAtivar = Instance.new("TextButton", frame)
	btnAtivar.Size = UDim2.new(0.65, 0, 1, 0)
	btnAtivar.Text = nome
	btnAtivar.Font = Enum.Font.SourceSansBold
	btnAtivar.TextScaled = true
	btnAtivar.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
	btnAtivar.TextColor3 = Color3.new(1, 1, 1)
	btnAtivar.Name = "btnFunc"
	
	local btnToggle = Instance.new("TextButton", frame)
	btnToggle.Size = UDim2.new(0.35, -5, 0.8, 0)
	btnToggle.Position = UDim2.new(0.65, 5, 0.1, 0)
	btnToggle.Text = "OFF"
	btnToggle.Font = Enum.Font.SourceSansBold
	btnToggle.TextScaled = true
	btnToggle.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
	btnToggle.TextColor3 = Color3.new(1, 1, 1)
	btnToggle.Name = "btnToggle"
	
	return frame, btnAtivar, btnToggle
end

local painel = Instance.new("Frame", ScreenGui)
painel.Size = UDim2.new(0, 250, 0, 400)
painel.Position = UDim2.new(0.02, 0, 0.2, 0)
painel.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
painel.Visible = false
painel.ClipsDescendants = true
painel.AutomaticSize = Enum.AutomaticSize.Y

local minimizar = Instance.new("TextButton", painel)
minimizar.Size = UDim2.new(0, 30, 0, 30)
minimizar.Position = UDim2.new(1, -35, 0, 5)
minimizar.Text = "-"
minimizar.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
minimizar.TextColor3 = Color3.new(1,1,1)
minimizar.Font = Enum.Font.SourceSansBold
minimizar.TextScaled = true

local aberto = true
minimizar.MouseButton1Click:Connect(function()
	aberto = not aberto
	for i,v in pairs(painel:GetChildren()) do
		if v:IsA("Frame") then
			v.Visible = aberto
		end
	end
	minimizar.Text = aberto and "-" or "+"
end)

-- Input para nick
local inputBox = Instance.new("TextBox", painel)
inputBox.Size = UDim2.new(1, 0, 0, 30)
inputBox.PlaceholderText = "Nome do player"
inputBox.Text = ""
inputBox.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
inputBox.TextColor3 = Color3.new(1,1,1)
inputBox.Font = Enum.Font.SourceSans
inputBox.Position = UDim2.new(0, 0, 1, -30)
inputBox.ZIndex = 10

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

    voarConn = game:GetService("RunService").Heartbeat:Connect(function()
        if estados.voar then
            bodyVel.velocity = hrp.CFrame.LookVector * 60
            bodyGyro.CFrame = workspace.CurrentCamera.CFrame
        end
    end)
end

local function desativarVoar()
    if voarConn then voarConn:Disconnect() end
    if bodyGyro then bodyGyro:Destroy() end
    if bodyVel then bodyVel:Destroy() end
end

-- Função Invisível
local function ativarInvisivel()
    for _,v in pairs(char:GetDescendants()) do
        if v:IsA("BasePart") and v.Name ~= "HumanoidRootPart" then
            v.Transparency = 1
        end
    end
end
local function desativarInvisivel()
    for _,v in pairs(char:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Transparency = 0
        end
    end
end

-- Criar botões com toggle

local btnNoclipFrame, btnNoclip, toggleNoclip = criarBotao("Noclip", painel)
toggleNoclip.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
toggleNoclip.Text = "OFF"
toggleNoclip.MouseButton1Click:Connect(function()
    estados.noclip = not estados.noclip
    if estados.noclip then
        toggleNoclip.Text = "ON"
        toggleNoclip.BackgroundColor3 = Color3.fromRGB(0, 100, 0)
        spawn(noclipLoop)
    else
        toggleNoclip.Text = "OFF"
        toggleNoclip.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
    end
end)

local btnVoarFrame, btnVoar, toggleVoar = criarBotao("Voar", painel)
toggleVoar.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
toggleVoar.Text = "OFF"
toggleVoar.MouseButton1Click:Connect(function()
    estados.voar = not estados.voar
    if estados.voar then
        toggleVoar.Text = "ON"
        toggleVoar.BackgroundColor3 = Color3.fromRGB(0, 100, 0)
        ativarVoar()
    else
        toggleVoar.Text = "OFF"
        toggleVoar.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
        desativarVoar()
    end
end)

local btnInvisivelFrame, btnInvisivel, toggleInvisivel = criarBotao("Invisível", painel)
toggleInvisivel.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
toggleInvisivel.Text = "OFF"
toggleInvisivel.MouseButton1Click:Connect(function()
    estados.invisivel = not estados.invisivel
    if estados.invisivel then
        toggleInvisivel.Text = "ON"
        toggleInvisivel.BackgroundColor3 = Color3.fromRGB(0, 100, 0)
        ativarInvisivel()
    else
        toggleInvisivel.Text = "OFF"
        toggleInvisivel.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
        desativarInvisivel()
    end
end)

-- Botões fixos que não tem toggle

local btnMatarFrame, btnMatar, _ = criarBotao("Matar (nick)", painel)
btnMatar.MouseButton1Click:Connect(function()
	local nome = game:GetService("Players"):FindFirstChild(inputBox.Text)
	if nome and nome.Character then
		nome.Character:BreakJoints()
	end
end)

local btnTeleportBancoFrame, btnTeleportBanco, _ = criarBotao("Teleportar: Banco", painel)
btnTeleportBanco.MouseButton1Click:Connect(function()
	game.Players.LocalPlayer.Character:MoveTo(Vector3.new(-498, 36, -284))
end)

local btnTeleportEscolaFrame, btnTeleportEscola, _ = criarBotao("Teleportar: Escola", painel)
btnTeleportEscola.MouseButton1Click:Connect(function()
	game.Players.LocalPlayer.Character:MoveTo(Vector3.new(-707, 36, -141))
end)

local btnTeleportInicioFrame, btnTeleportInicio, _ = criarBotao("Teleportar: Início", painel)
btnTeleportInicio.MouseButton1Click:Connect(function()
	game.Players.LocalPlayer.Character:MoveTo(Vector3.new(-84, 36, -57))
end)

local btnTpCasaFrame, btnTpCasa, _ = criarBotao("TP para Casa (nick)", painel)
btnTpCasa.MouseButton1Click:Connect(function()
	local nome = inputBox.Text
	local alvo = game.Players:FindFirstChild(nome)
	if alvo and alvo.Character then
		local root = alvo.Character:FindFirstChild("HumanoidRootPart")
		if root then
			game.Players.LocalPlayer.Character:MoveTo(root.Position + Vector3.new(0, 5, 0))
		end
	end
end)

-- Ativação com Key
botaoConfirmar.MouseButton1Click:Connect(function()
	if caixaKey.Text == keyCorreta then
		keyFrame.Visible = false
		painel.Visible = true
	else
		caixaKey.Text = "Key incorreta!"
	end
end)
