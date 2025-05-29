-- LOSTsCRIPTS - FPS Booster v2.0
-- By ogart
-- Delta Executor Compatible

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local HttpService = game:GetService("HttpService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

-- Configurações
local API_URL = "https://your-api-domain.com/api" -- Configure sua API aqui
local SCRIPT_KEY = "ogart"
local USER_ID = "og"
local authenticated = false

-- Função para verificar key
local function checkKey(key, user)
local success, result = pcall(function()
local response = syn.request({
Url = API_URL .. "/check-key",
Method = "POST",
Headers = {
["Content-Type"] = "application/json"
},
Body = HttpService:JSONEncode({
key = key,
user = user,
roblox_id = tostring(Player.UserId)
})
})
return HttpService:JSONDecode(response.Body)
end)

if success and result.valid then  
    return true, result.premium or false  
end  
return false, false

end

-- Interface de Login
local function createLoginGUI()
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LOSTsLogin"
ScreenGui.Parent = PlayerGui
ScreenGui.ResetOnSpawn = false

local LoginFrame = Instance.new("Frame")  
LoginFrame.Size = UDim2.new(0, 400, 0, 300)  
LoginFrame.Position = UDim2.new(0.5, -200, 0.5, -150)  
LoginFrame.BackgroundColor3 = Color3.new(0, 0.2, 0)  
LoginFrame.BorderSizePixel = 0  
LoginFrame.Parent = ScreenGui  
  
local UICorner = Instance.new("UICorner")  
UICorner.CornerRadius = UDim.new(0, 15)  
UICorner.Parent = LoginFrame  
  
local Title = Instance.new("TextLabel")  
Title.Size = UDim2.new(1, 0, 0, 50)  
Title.Position = UDim2.new(0, 0, 0, 0)  
Title.BackgroundTransparency = 1  
Title.Text = "🔑 LOSTsCRIPTS - Login 🔑"  
Title.TextColor3 = Color3.new(1, 1, 1)  
Title.TextScaled = true  
Title.Font = Enum.Font.GothamBold  
Title.Parent = LoginFrame  
  
local KeyLabel = Instance.new("TextLabel")  
KeyLabel.Size = UDim2.new(1, -20, 0, 30)  
KeyLabel.Position = UDim2.new(0, 10, 0, 70)  
KeyLabel.BackgroundTransparency = 1  
KeyLabel.Text = "🔐 Key:"  
KeyLabel.TextColor3 = Color3.new(0.8, 0.8, 0.8)  
KeyLabel.TextScaled = true  
KeyLabel.Font = Enum.Font.Gotham  
KeyLabel.TextXAlignment = Enum.TextXAlignment.Left  
KeyLabel.Parent = LoginFrame  
  
local KeyBox = Instance.new("TextBox")  
KeyBox.Size = UDim2.new(1, -20, 0, 40)  
KeyBox.Position = UDim2.new(0, 10, 0, 100)  
KeyBox.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)  
KeyBox.TextColor3 = Color3.new(1, 1, 1)  
KeyBox.PlaceholderText = "Digite sua key aqui..."  
KeyBox.Text = ""  
KeyBox.TextScaled = true  
KeyBox.Font = Enum.Font.Gotham  
KeyBox.Parent = LoginFrame  
  
local KeyCorner = Instance.new("UICorner")  
KeyCorner.CornerRadius = UDim.new(0, 8)  
KeyCorner.Parent = KeyBox  
  
local UserLabel = Instance.new("TextLabel")  
UserLabel.Size = UDim2.new(1, -20, 0, 30)  
UserLabel.Position = UDim2.new(0, 10, 0, 150)  
UserLabel.BackgroundTransparency = 1  
UserLabel.Text = "👤 User:"  
UserLabel.TextColor3 = Color3.new(0.8, 0.8, 0.8)  
UserLabel.TextScaled = true  
UserLabel.Font = Enum.Font.Gotham  
UserLabel.TextXAlignment = Enum.TextXAlignment.Left  
UserLabel.Parent = LoginFrame  
  
local UserBox = Instance.new("TextBox")  
UserBox.Size = UDim2.new(1, -20, 0, 40)  
UserBox.Position = UDim2.new(0, 10, 0, 180)  
UserBox.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)  
UserBox.TextColor3 = Color3.new(1, 1, 1)  
UserBox.PlaceholderText = "Digite seu usuário..."  
UserBox.Text = ""  
UserBox.TextScaled = true  
UserBox.Font = Enum.Font.Gotham  
UserBox.Parent = LoginFrame  
  
local UserCorner = Instance.new("UICorner")  
UserCorner.CornerRadius = UDim.new(0, 8)  
UserCorner.Parent = UserBox  
  
local LoginButton = Instance.new("TextButton")  
LoginButton.Size = UDim2.new(1, -20, 0, 40)  
LoginButton.Position = UDim2.new(0, 10, 0, 240)  
LoginButton.BackgroundColor3 = Color3.new(0, 0.8, 0)  
LoginButton.Text = "✅ Login"  
LoginButton.TextColor3 = Color3.new(1, 1, 1)  
LoginButton.TextScaled = true  
LoginButton.Font = Enum.Font.GothamBold  
LoginButton.Parent = LoginFrame  
  
local LoginCorner = Instance.new("UICorner")  
LoginCorner.CornerRadius = UDim.new(0, 8)  
LoginCorner.Parent = LoginButton  
  
local Credits = Instance.new("TextLabel")  
Credits.Size = UDim2.new(1, 0, 0, 20)  
Credits.Position = UDim2.new(0, 0, 1, -20)  
Credits.BackgroundTransparency = 1  
Credits.Text = "💻 by ogart"  
Credits.TextColor3 = Color3.new(0.6, 0.6, 0.6)  
Credits.TextScaled = true  
Credits.Font = Enum.Font.Gotham  
Credits.Parent = LoginFrame  
  
LoginButton.MouseButton1Click:Connect(function()  
    local key = KeyBox.Text  
    local user = UserBox.Text  
      
    if key == "" or user == "" then  
        KeyBox.PlaceholderText = "❌ Preencha todos os campos!"  
        wait(2)  
        KeyBox.PlaceholderText = "Digite sua key aqui..."  
        return  
    end  
      
    LoginButton.Text = "🔄 Verificando..."  
    local valid, premium = checkKey(key, user)  
      
    if valid then  
        SCRIPT_KEY = key  
        USER_ID = user  
        authenticated = true  
        ScreenGui:Destroy()  
        createMainGUI(premium)  
    else  
        LoginButton.Text = "❌ Key Inválida!"  
        wait(2)  
        LoginButton.Text = "✅ Login"  
    end  
end)

end

-- Interface Principal
local function createMainGUI(isPremium)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LOSTsBooster"
ScreenGui.Parent = PlayerGui
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")  
MainFrame.Size = UDim2.new(0, 600, 0, 400)  
MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)  
MainFrame.BackgroundColor3 = Color3.new(0, 0.15, 0)  
MainFrame.BorderSizePixel = 0  
MainFrame.Parent = ScreenGui  
MainFrame.Active = true  
MainFrame.Draggable = true  
  
local UICorner = Instance.new("UICorner")  
UICorner.CornerRadius = UDim.new(0, 15)  
UICorner.Parent = MainFrame  
  
-- Título  
local TitleBar = Instance.new("Frame")  
TitleBar.Size = UDim2.new(1, 0, 0, 50)  
TitleBar.Position = UDim2.new(0, 0, 0, 0)  
TitleBar.BackgroundColor3 = Color3.new(0, 0.3, 0)  
TitleBar.BorderSizePixel = 0  
TitleBar.Parent = MainFrame  
  
local TitleCorner = Instance.new("UICorner")  
TitleCorner.CornerRadius = UDim.new(0, 15)  
TitleCorner.Parent = TitleBar  
  
local Title = Instance.new("TextLabel")  
Title.Size = UDim2.new(1, -100, 1, 0)  
Title.Position = UDim2.new(0, 10, 0, 0)  
Title.BackgroundTransparency = 1  
Title.Text = "🚀 LOSTsCRIPTS - FPS Booster " .. (isPremium and "👑" or "")  
Title.TextColor3 = Color3.new(1, 1, 1)  
Title.TextScaled = true  
Title.Font = Enum.Font.GothamBold  
Title.TextXAlignment = Enum.TextXAlignment.Left  
Title.Parent = TitleBar  
  
-- Botão Minimizar  
local MinimizeButton = Instance.new("TextButton")  
MinimizeButton.Size = UDim2.new(0, 40, 0, 30)  
MinimizeButton.Position = UDim2.new(1, -50, 0, 10)  
MinimizeButton.BackgroundColor3 = Color3.new(0.8, 0.8, 0)  
MinimizeButton.Text = "➖"  
MinimizeButton.TextColor3 = Color3.new(0, 0, 0)  
MinimizeButton.TextScaled = true  
MinimizeButton.Font = Enum.Font.GothamBold  
MinimizeButton.Parent = TitleBar  
  
local MinCorner = Instance.new("UICorner")  
MinCorner.CornerRadius = UDim.new(0, 8)  
MinCorner.Parent = MinimizeButton  
  
local minimized = false  
local originalSize = MainFrame.Size  
  
MinimizeButton.MouseButton1Click:Connect(function()  
    if not minimized then  
        MainFrame:TweenSize(UDim2.new(0, 300, 0, 50), "Out", "Quad", 0.3)  
        MinimizeButton.Text = "⬆️"  
        minimized = true  
    else  
        MainFrame:TweenSize(originalSize, "Out", "Quad", 0.3)  
        MinimizeButton.Text = "➖"  
        minimized = false  
    end  
end)  
  
-- Container de Abas  
local TabContainer = Instance.new("Frame")  
TabContainer.Size = UDim2.new(1, -20, 0, 40)  
TabContainer.Position = UDim2.new(0, 10, 0, 60)  
TabContainer.BackgroundTransparency = 1  
TabContainer.Parent = MainFrame  
  
-- Container de Conteúdo  
local ContentContainer = Instance.new("Frame")  
ContentContainer.Size = UDim2.new(1, -20, 1, -150)  
ContentContainer.Position = UDim2.new(0, 10, 0, 110)  
ContentContainer.BackgroundColor3 = Color3.new(0, 0, 0)  
ContentContainer.BackgroundTransparency = 0.5  
ContentContainer.BorderSizePixel = 0  
ContentContainer.Parent = MainFrame  
  
local ContentCorner = Instance.new("UICorner")  
ContentCorner.CornerRadius = UDim.new(0, 10)  
ContentCorner.Parent = ContentContainer  
  
-- Créditos  
local Credits = Instance.new("TextLabel")  
Credits.Size = UDim2.new(1, 0, 0, 25)  
Credits.Position = UDim2.new(0, 0, 1, -25)  
Credits.BackgroundTransparency = 1  
Credits.Text = "💻 Created by ogart | Premium: " .. (isPremium and "✅" or "❌")  
Credits.TextColor3 = Color3.new(0.7, 0.7, 0.7)  
Credits.TextScaled = true  
Credits.Font = Enum.Font.Gotham  
Credits.Parent = MainFrame  
  
-- Sistema de Abas  
local tabs = {}  
local currentTab = nil  
  
local function createTab(name, icon, content)  
    local tabButton = Instance.new("TextButton")  
    tabButton.Size = UDim2.new(0, 120, 1, 0)  
    tabButton.Position = UDim2.new(0, #tabs * 125, 0, 0)  
    tabButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)  
    tabButton.Text = icon .. " " .. name  
    tabButton.TextColor3 = Color3.new(0.8, 0.8, 0.8)  
    tabButton.TextScaled = true  
    tabButton.Font = Enum.Font.Gotham  
    tabButton.Parent = TabContainer  
      
    local tabCorner = Instance.new("UICorner")  
    tabCorner.CornerRadius = UDim.new(0, 8)  
    tabCorner.Parent = tabButton  
      
    local tabContent = Instance.new("ScrollingFrame")  
    tabContent.Size = UDim2.new(1, 0, 1, 0)  
    tabContent.Position = UDim2.new(0, 0, 0, 0)  
    tabContent.BackgroundTransparency = 1  
    tabContent.BorderSizePixel = 0  
    tabContent.ScrollBarThickness = 6  
    tabContent.CanvasSize = UDim2.new(0, 0, 0, 0)  
    tabContent.AutomaticCanvasSize = Enum.AutomaticSize.Y  
    tabContent.Visible = false  
    tabContent.Parent = ContentContainer  
      
    local layout = Instance.new("UIListLayout")  
    layout.SortOrder = Enum.SortOrder.LayoutOrder  
    layout.Padding = UDim.new(0, 10)  
    layout.Parent = tabContent  
      
    content(tabContent)  
      
    tabButton.MouseButton1Click:Connect(function()  
        if currentTab then  
            currentTab.button.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)  
            currentTab.button.TextColor3 = Color3.new(0.8, 0.8, 0.8)  
            currentTab.content.Visible = false  
        end  
          
        tabButton.BackgroundColor3 = Color3.new(0, 0.5, 0)  
        tabButton.TextColor3 = Color3.new(1, 1, 1)  
        tabContent.Visible = true  
          
        currentTab = {button = tabButton, content = tabContent}  
    end)  
      
    table.insert(tabs, {button = tabButton, content = tabContent})  
      
    if #tabs == 1 then  
        tabButton.MouseButton1Click()  
    end  
end  
  
-- Função para criar botões  
local function createButton(parent, text, color, callback)  
    local button = Instance.new("TextButton")  
    button.Size = UDim2.new(1, -20, 0, 40)  
    button.Position = UDim2.new(0, 10, 0, 0)  
    button.BackgroundColor3 = color  
    button.Text = text  
    button.TextColor3 = Color3.new(1, 1, 1)  
    button.TextScaled = true  
    button.Font = Enum.Font.Gotham  
    button.Parent = parent  
      
    local corner = Instance.new("UICorner")  
    corner.CornerRadius = UDim.new(0, 8)  
    corner.Parent = button  
      
    button.MouseButton1Click:Connect(callback)  
      
    return button  
end  
  
-- Função para criar toggle  
local function createToggle(parent, text, callback)  
    local frame = Instance.new("Frame")  
    frame.Size = UDim2.new(1, -20, 0, 50)  
    frame.Position = UDim2.new(0, 10, 0, 0)  
    frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)  
    frame.BorderSizePixel = 0  
    frame.Parent = parent  
      
    local corner = Instance.new("UICorner")  
    corner.CornerRadius = UDim.new(0, 8)  
    corner.Parent = frame  
      
    local label = Instance.new("TextLabel")  
    label.Size = UDim2.new(1, -60, 1, 0)  
    label.Position = UDim2.new(0, 10, 0, 0)  
    label.BackgroundTransparency = 1  
    label.Text = text  
    label.TextColor3 = Color3.new(1, 1, 1)  
    label.TextScaled = true  
    label.Font = Enum.Font.Gotham  
    label.TextXAlignment = Enum.TextXAlignment.Left  
    label.Parent = frame  
      
    local toggle = Instance.new("TextButton")  
    toggle.Size = UDim2.new(0, 40, 0, 30)  
    toggle.Position = UDim2.new(1, -50, 0.5, -15)  
    toggle.BackgroundColor3 = Color3.new(0.8, 0, 0)  
    toggle.Text = "❌"  
    toggle.TextColor3 = Color3.new(1, 1, 1)  
    toggle.TextScaled = true  
    toggle.Font = Enum.Font.GothamBold  
    toggle.Parent = frame  
      
    local toggleCorner = Instance.new("UICorner")  
    toggleCorner.CornerRadius = UDim.new(0, 15)  
    toggleCorner.Parent = toggle  
      
    local enabled = false  
      
    toggle.MouseButton1Click:Connect(function()  
        enabled = not enabled  
        if enabled then  
            toggle.BackgroundColor3 = Color3.new(0, 0.8, 0)  
            toggle.Text = "✅"  
        else  
            toggle.BackgroundColor3 = Color3.new(0.8, 0, 0)  
            toggle.Text = "❌"  
        end  
        callback(enabled)  
    end)  
      
    return frame  
end  
  
-- ABA FPS BOOST  
createTab("FPS", "🚀", function(parent)  
    createButton(parent, "🔥 FPS MAX (Ultra Performance)", Color3.new(0.8, 0, 0), function()  
        -- FPS MAX  
        settings().Rendering.QualityLevel = 1  
        for i,v in pairs(Lighting:GetDescendants()) do  
            if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("DepthOfFieldEffect") then  
                v.Enabled = false  
            end  
        end  
        Lighting.GlobalShadows = false  
        Lighting.FogEnd = 9e9  
        Lighting.Brightness = 0  
        settings().Rendering.QualityLevel = "Level01"  
        for i, v in pairs(Workspace:GetDescendants()) do  
            if v:IsA("Part") or v:IsA("Union") or v:IsA("MeshPart") then  
                v.Material = "Plastic"  
                v.Reflectance = 0  
            end  
            if v:IsA("Decal") then  
                v.Transparency = 1  
            end  
            if v:IsA("ParticleEmitter") or v:IsA("Trail") then  
                v.Lifetime = NumberRange.new(0)  
            end  
            if v:IsA("Explosion") then  
                v.BlastPressure = 1  
                v.BlastRadius = 1  
            end  
        end  
    end)  
      
    createButton(parent, "⚡ FPS Medium (Balanced)", Color3.new(0.8, 0.5, 0), function()  
        -- FPS MEDIUM  
        settings().Rendering.QualityLevel = 3  
        Lighting.GlobalShadows = false  
        for i,v in pairs(Lighting:GetDescendants()) do  
            if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") then  
                v.Enabled = false  
            end  
        end  
    end)  
      
    createButton(parent, "💡 FPS Min (Light Boost)", Color3.new(0, 0.6, 0), function()  
        -- FPS MIN  
        settings().Rendering.QualityLevel = 5  
        Lighting.GlobalShadows = false  
    end)  
      
    if isPremium then  
        createButton(parent, "👑 PREMIUM FPS Destroyer", Color3.new(0.5, 0, 0.8), function()  
            -- PREMIUM FPS  
            for i,v in pairs(Workspace:GetDescendants()) do  
                if v:IsA("Texture") or v:IsA("SurfaceGui") then  
                    v:Destroy()  
                end  
            end  
            RunService.Heartbeat:Connect(function()  
                settings().Rendering.QualityLevel = 1  
            end)  
        end)  
    end  
end)  
  
-- ABA GRAPHICS  
createTab("Graphics", "🎨", function(parent)  
    createToggle(parent, "🌟 Remove Textures", function(enabled)  
        for i,v in pairs(Workspace:GetDescendants()) do  
            if v:IsA("Decal") then  
                v.Transparency = enabled and 1 or 0  
            end  
        end  
    end)  
      
    createToggle(parent, "💨 Remove Particles", function(enabled)  
        for i,v in pairs(Workspace:GetDescendants()) do  
            if v:IsA("ParticleEmitter") then  
                v.Enabled = not enabled  
            end  
        end  
    end)  
      
    createToggle(parent, "🌫️ Remove Fog", function(enabled)  
        Lighting.FogEnd = enabled and 9e9 or 100000  
    end)  
      
    createToggle(parent, "☀️ Remove Shadows", function(enabled)  
        Lighting.GlobalShadows = not enabled  
    end)  
end)  
  
-- ABA MOBILE (se for mobile)  
if UserInputService.TouchEnabled then  
    createTab("Mobile", "📱", function(parent)  
        createButton(parent, "📱 Mobile Optimization", Color3.new(0, 0.4, 0.8), function()  
            settings().Rendering.QualityLevel = 1  
            for i,v in pairs(Workspace:GetDescendants()) do  
                if v:IsA("Part") then  
                    v.Material = "SmoothPlastic"  
                    v.TopSurface = "Smooth"  
                    v.BottomSurface = "Smooth"  
                end  
            end  
        end)  
          
        createToggle(parent, "🔋 Battery Saver", function(enabled)  
            RunService:SetRobloxGuiFlagEnabled("NewInGameMenuPerformance", enabled)  
        end)  
    end)  
end  
  
-- ABA SETTINGS  
createTab("Settings", "⚙️", function(parent)  
    createButton(parent, "🔄 Reset All Settings", Color3.new(0.6, 0.6, 0.6), function()  
        settings().Rendering.QualityLevel = "Automatic"  
        Lighting.GlobalShadows = true  
        Lighting.FogEnd = 100000  
    end)  
      
    createButton(parent, "❌ Close Script", Color3.new(0.8, 0, 0), function()  
        ScreenGui:Destroy()  
    end)  
end)

end

-- Inicializar
if not authenticated then
createLoginGUI()
end
