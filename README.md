-- [[ POWER HUB X - TUDO-EM-UM (SEM LINKS EXTERNOS) ]] --
local p = game.Players.LocalPlayer
local RS = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

-- 1. TELA DE CARREGAMENTO GIGANTE (0/200)
local g = Instance.new("ScreenGui", p:WaitForChild("PlayerGui"))
g.ResetOnSpawn = false
g.IgnoreGuiInset = true

local bg = Instance.new("Frame", g)
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(5, 5, 5)
bg.BorderSizePixel = 0

local mainTitle = Instance.new("TextLabel", bg)
mainTitle.Size = UDim2.new(1, 0, 0.2, 0)
mainTitle.Position = UDim2.new(0, 0, 0.3, 0)
mainTitle.Text = "POWER HUB X"
mainTitle.TextColor3 = Color3.fromRGB(255, 215, 0)
mainTitle.TextScaled = true
mainTitle.Font = Enum.Font.GothamBlack
mainTitle.BackgroundTransparency = 1

local resourceLabel = Instance.new("TextLabel", bg)
resourceLabel.Size = UDim2.new(1, 0, 0.1, 0)
resourceLabel.Position = UDim2.new(0, 0, 0.5, 0)
resourceLabel.Text = "Sincronizando: 0 / 200"
resourceLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
resourceLabel.TextSize = 25
resourceLabel.Font = Enum.Font.GothamMedium
resourceLabel.BackgroundTransparency = 1

local barBg = Instance.new("Frame", bg)
barBg.Size = UDim2.new(0.4, 0, 0.02, 0)
barBg.Position = UDim2.new(0.3, 0, 0.6, 0)
barBg.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Instance.new("UICorner", barBg)

local bar = Instance.new("Frame", barBg)
bar.Size = UDim2.new(0, 0, 1, 0)
bar.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
Instance.new("UICorner", bar)

-- LOGICA CARREGAMENTO
local n = 0
spawn(function()
    while n < 200 do
        n = n + math.random(5, 15)
        if n > 200 then n = 200 end
        bar.Size = UDim2.new(n/200, 0, 1, 0)
        resourceLabel.Text = "Sincronizando: " .. n .. " / 200"
        task.wait(0.05)
    end
    task.wait(0.3)
    g:Destroy()

    -- 2. INICIAR WIND UI E CRIAR TABS DIRETAMENTE
    local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
    local Window = WindUI:CreateWindow({
        Title = "Power Panel",
        Icon = "rbxassetid://10723415535",
        Author = "Power Hub",
        Folder = "PowerHubConfig"
    })

    local NomeAlvo = ""

    -- TAB PLAYER
    local Tab1 = Window:Tab("Player", "user")
    Tab1:Section("Física")
    Tab1:Slider("Velocidade", 16, 500, 16, function(v) p.Character.Humanoid.WalkSpeed = v end)
    Tab1:Slider("Pulo", 50, 500, 50, function(v) p.Character.Humanoid.JumpPower = v end)
    Tab1:Toggle("Noclip", false, function(s) _G.noclip = s end)

    -- TAB MUNDO (BROOKHAVEN SERVER)
    local Tab2 = Window:Tab("Mundo", "globe")
    Tab2:Section("Eventos do Servidor")
    Tab2:Button("Explodir Banco", function()
        RS.RE:FindFirstChild("1Bank0Item1"):FireServer("BankRobbery", "PlaceC4")
        task.wait(0.2)
        RS.RE:FindFirstChild("1Bank0Item1"):FireServer("BankRobbery", "Detonate")
    end)
    Tab2:Button("Gigante (Todos Vêem)", function() RS.RE:FindFirstChild("1Avatar0Customization1"):FireServer("CharacterSize", 1.8) end)
    Tab2:Button("Fogo na Casa", function() RS.RE.RE:FireServer("HouseOptions", "HouseFire", true) end)

    -- TAB TROLL (COM KICK, KILL, JAIL)
    local Tab3 = Window:Tab("Troll", "zap")
    Tab3:Section("Alvo por Nome")
    Tab3:Input("Nome do Jogador", "Digite aqui...", function(t) NomeAlvo = t end)

    Tab3:Section("Ações de Bug")
    Tab3:Button("Kick (Crash/Kill)", function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v then for i=1,100 do RS.RE:FindFirstChild("1Property0Emergency1"):FireServer(v, "SkateBoard") end end
    end)

    Tab3:Button("Kill (Fling)", function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v and v.Character then
            local s = Instance.new("BodyAngularVelocity", p.Character.HumanoidRootPart)
            s.AngularVelocity = Vector3.new(0, 99999, 0)
            s.MaxTorque = Vector3.new(0, math.huge, 0)
            for i=1,30 do p.Character.HumanoidRootPart.CFrame = v.Character.HumanoidRootPart.CFrame task.wait() end
            s:Destroy()
        end
    end)

    Tab3:Button("Jail (Bug 2s)", function()
        local v = game.Players:FindFirstChild(NomeAlvo)
        if v then
            local start = tick()
            while tick() - start < 2 do
                RS.RE:FindFirstChild("1Property0Emergency1"):FireServer(v, "CratePlayer")
                task.wait()
            end
        end
    end)

    -- TAB EXTRAS
    local Tab4 = Window:Tab("Extras", "plus")
    Tab4:Section("Itens")
    Tab4:Button("Pegar Arma (Pistola)", function() RS.RE:FindFirstChild("1Item0Inventory1"):FireServer("PickingUpItem", "Gun") end)
    Tab4:Button("Cartão do Banco", function() RS.RE:FindFirstChild("1Item0Inventory1"):FireServer("PickingUpItem", "BankKeycard") end)
end)

-- LOOP NOCLIP
RunService.Stepped:Connect(function()
    if _G.noclip and p.Character then
        for _, v in pairs(p.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)
