-- [[ POWER HUB X - VERSÃO COMPLETA COM TROLL AVANÇADO ]] --
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
resourceLabel.Text = "Carregando Recursos: 0 / 200"
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

local recursos = 0
local total = 200
spawn(function()
    while recursos < total do
        recursos = recursos + math.random(3, 8)
        if recursos > total then recursos = total end
        bar.Size = UDim2.new(recursos/total, 0, 1, 0)
        resourceLabel.Text = "Carregando Recursos: " .. recursos .. " / " .. total
        task.wait(0.05)
    end
    task.wait(0.5)
    g:Destroy()

    -- 2. INICIALIZAÇÃO DA WIND UI
    local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()
    local Window = WindUI:CreateWindow({
        Title = "Power Panel",
        Icon = "rbxassetid://10723415535",
        Author = "Power Hub",
        Folder = "PowerPanelConfig"
    })

    local NomeAlvo = ""

    -- ABA PLAYER
    local Tab1 = Window:Tab("Player", "user")
    Tab1:Section("Atributos")
    Tab1:Slider("Velocidade", 16, 500, 16, function(v) p.Character.Humanoid.WalkSpeed = v end)
    Tab1:Slider("Pulo", 50, 500, 50, function(v) p.Character.Humanoid.JumpPower = v end)

    -- ABA MUNDO
    local Tab2 = Window:Tab("Mundo", "globe")
    Tab2:Section("Servidor")
    Tab2:Button("Explodir Banco", function()
        RS.RE:FindFirstChild("1Bank0Item1"):FireServer("BankRobbery", "PlaceC4")
        task.wait(0.2)
        RS.RE:FindFirstChild("1Bank0Item1"):FireServer("BankRobbery", "Detonate")
    end)
    Tab2:Button("Ficar Gigante (Server)", function() RS.RE:FindFirstChild("1Avatar0Customization1"):FireServer("CharacterSize", 1.8) end)

    -- ABA TROLL (COM COMANDOS DE NOME)
    local Tab3 = Window:Tab("Troll", "zap")
    Tab3:Section("Alvo Específico")
    
    Tab3:Input("Nome do Jogador", "Digite o nome aqui", function(txt)
        NomeAlvo = txt
    end)

    Tab3:Section("Ações Fatais")

    -- KICK (Simula um erro para o jogador ou tenta dar kick se tiver permissão de rede)
    Tab3:Button("Kick (Crash)", function()
        local alvo = game.Players:FindFirstChild(NomeAlvo)
        if alvo then
            print("Tentando crashar: " .. alvo.Name)
            -- Nota: Kicks reais dependem de vulnerabilidades, aqui tentamos sobrecarregar a rede do alvo
            for i = 1, 100 do
                RS.RE:FindFirstChild("1Property0Emergency1"):FireServer(alvo, "SkateBoard") 
            end
        end
    end)

    -- KILL (Usa o Fling para matar)
    Tab3:Button("Kill (Fling)", function()
        local alvo = game.Players:FindFirstChild(NomeAlvo)
        if alvo and alvo.Character then
            local posAntiga = p.Character.HumanoidRootPart.CFrame
            local spin = Instance.new("BodyAngularVelocity", p.Character.HumanoidRootPart)
            spin.AngularVelocity = Vector3.new(0, 99999, 0)
            spin.MaxTorque = Vector3.new(0, math.huge, 0)
            
            for i = 1, 50 do
                p.Character.HumanoidRootPart.CFrame = alvo.Character.HumanoidRootPart.CFrame
                task.wait()
            end
            spin:Destroy()
            p.Character.HumanoidRootPart.CFrame = posAntiga
        end
    end)

    -- JAIL (Buga o jogador por 2 segundos)
    Tab3:Button("Jail (Bug 2s)", function()
        local alvo = game.Players:FindFirstChild(NomeAlvo)
        if alvo and alvo.Character then
            local posOriginal = alvo.Character.HumanoidRootPart.CFrame
            spawn(function()
                local t = tick()
                while tick() - t < 2 do
                    -- Teleporta o alvo para uma "prisão" invisível ou fica puxando ele
                    RS.RE:FindFirstChild("1Property0Emergency1"):FireServer(alvo, "CratePlayer")
                    task.wait()
                end
            end)
        end
    end)

    -- ABA EXTRAS
    local Tab4 = Window:Tab("Extras", "plus")
    Tab4:Button("Pegar Arma", function() RS.RE:FindFirstChild("1Item0Inventory1"):FireServer("PickingUpItem", "Gun") end)
end)
