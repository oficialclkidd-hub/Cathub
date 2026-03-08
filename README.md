-- // INICIALIZAÇÃO E IDENTIDADE //
local success, content = pcall(function()
    return game:HttpGet("https://nexviewsservice.shardweb.app/services/s4turn_hub/start")
end)

-- Sistema de Nome/Bio no Brookhaven
local RE = game:GetService("ReplicatedStorage"):WaitForChild("RE")
pcall(function()
    RE["1RPNam1eTex1t"]:FireServer("RolePlayBio", "Cat Hub v1 - FULL")
    RE["1RPNam1eTex1t"]:FireServer("RolePlayName", "ã¾ Cat Hub v1")
end)

-- Carregamento da Library
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/toparemos/Library/refs/heads/main/library.lua"))()

local Window = redzlib:MakeWindow({
    Title = "Cat hub v1 português",
    SubTitle = "by scripts073, davi lucas e scripts_H54",
    SaveFolder = "CatHubBrookhaven.json"
})

-- Botão de Minimizar (ID: 14096965095)
Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://14096965095", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(35, 1) },
})

---
-- VARIÁVEIS DE CONTROLE
---
local SelectedPlayer = ""
local AimbotEnabled = false
local AimPart = "Head"
local espEnabled = false
local espColor = Color3.fromRGB(255, 0, 0)
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local LP = game.Players.LocalPlayer

local function getPlayers()
    local list = {}
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= LP then table.insert(list, plr.Name) end
    end
    return list
end

---
-- ABA 1: INFORMAÇÕES (CONFORME A IMAGEM)
---
local Tab1 = Window:MakeTab({"Informações", "info"})

Tab1:AddSection({"Creditos do Hub"})

Tab1:AddDiscordInvite({
    Name = "â½¦ Cat Hub Studios",
    Description = "Discord oficial do cat hub entra para saber atualizações do hub: https://discord.gg/vUNHqeUtQq",
    Logo = "rbxassetid://74041792558354",
    Invite = "https://discord.gg/vUNHqeUtQq",
})

Tab1:AddSection({"Status"})
Tab1:AddParagraph({"Informações", "O script está: Online 🟢\nVersão: 1.0.2\nCriado por: Scripts073, Davi Lucas e scripts_H54"})

---
-- ABA 2: COMBATE
---
local TabCombat = Window:MakeTab({"Combate", "crosshair"})
TabCombat:AddSection({"Aimbot"})

local TargetDropdown = TabCombat:AddDropdown({
    Name = "Selecionar Player",
    Options = getPlayers(),
    Default = "",
    Callback = function(v) SelectedPlayer = v end
})

TabCombat:AddButton({
    Name = "Atualizar Players",
    Callback = function() TargetDropdown:SetOptions(getPlayers()) end
})

TabCombat:AddDropdown({
    Name = "Focar Parte do Corpo",
    Options = {"Cabeça", "Barriga", "Braço Direito", "Braço Esquerdo", "Pé Direito", "Pé Esquerdo"},
    Default = "Cabeça",
    Callback = function(v)
        local parts = {["Cabeça"]="Head", ["Barriga"]="HumanoidRootPart", ["Braço Direito"]="RightUpperArm", ["Braço Esquerdo"]="LeftUpperArm", ["Pé Direito"]="RightFoot", ["Pé Esquerdo"]="LeftFoot"}
        AimPart = parts[v] or "Head"
    end
})

TabCombat:AddToggle({
    Name = "Ativar Aimbot",
    Default = false,
    Callback = function(v) AimbotEnabled = v end
})

-- Loop Aimbot
RunService.RenderStepped:Connect(function()
    if AimbotEnabled and SelectedPlayer ~= "" then
        local target = game.Players:FindFirstChild(SelectedPlayer)
        if target and target.Character and target.Character:FindFirstChild(AimPart) then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[AimPart].Position)
        end
    end
end)

---
-- ABA 3: PLAYER (NOVA ABA)
---
local TabPlayer = Window:MakeTab({"Player", "user"})

TabPlayer:AddSection({"Movimentação"})

TabPlayer:AddSlider({
    Name = "Velocidade (WalkSpeed)",
    Min = 16, Max = 500, Default = 16,
    Callback = function(v)
        if LP.Character and LP.Character:FindFirstChild("Humanoid") then
            LP.Character.Humanoid.WalkSpeed = v
        end
    end
})

TabPlayer:AddSlider({
    Name = "Pulo (JumpPower)",
    Min = 50, Max = 1000, Default = 50,
    Callback = function(v)
        if LP.Character and LP.Character:FindFirstChild("Humanoid") then
            LP.Character.Humanoid.UseJumpPower = true
            LP.Character.Humanoid.JumpPower = v
        end
    end
})

TabPlayer:AddSlider({
    Name = "Gravidade",
    Min = 0, Max = 196.2, Default = 196.2,
    Callback = function(v)
        workspace.Gravity = v
    end
})

---
-- ABA 4: TROLL
---
local TabTroll = Window:MakeTab({"Troll", "skull"})
TabTroll:AddToggle({
    Name = "View Player (Espiar)",
    Default = false,
    Callback = function(state)
        local target = game.Players:FindFirstChild(SelectedPlayer)
        if state and target and target.Character then
            Camera.CameraSubject = target.Character.Humanoid
        else
            Camera.CameraSubject = LP.Character.Humanoid
        end
    end
})

---
-- ABA 5: VISUAL
---
local TabVisual = Window:MakeTab({"Visual", "eye"})

TabVisual:AddSection({"Configuração de ESP"})

TabVisual:AddToggle({
    Name = "Ativar ESP Highlight",
    Default = false,
    Callback = function(v)
        espEnabled = v
        if not v then
            for _, p in pairs(game.Players:GetPlayers()) do
                if p.Character and p.Character:FindFirstChild("CatHighlight") then p.Character.CatHighlight:Destroy() end
            end
        end
    end
})

TabVisual:AddDropdown({
    Name = "Cor do ESP",
    Options = {"Vermelho", "Verde", "Azul", "Amarelo", "Branco"},
    Default = "Vermelho",
    Callback = function(v)
        local colors = {
            ["Vermelho"] = Color3.fromRGB(255, 0, 0),
            ["Verde"] = Color3.fromRGB(0, 255, 0),
            ["Azul"] = Color3.fromRGB(0, 0, 255),
            ["Amarelo"] = Color3.fromRGB(255, 255, 0),
            ["Branco"] = Color3.fromRGB(255, 255, 255)
        }
        espColor = colors[v]
    end
})

-- Loop ESP
RunService.Heartbeat:Connect(function()
    if espEnabled then
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= LP and p.Character then
                local h = p.Character:FindFirstChild("CatHighlight") or Instance.new("Highlight", p.Character)
                h.Name = "CatHighlight"
                h.FillColor = espColor
                h.Enabled = true
            end
        end
    end
end)

---
-- ABA 6: MUNDO
---
local TabMundo = Window:MakeTab({"Mundo", "globe"})
TabMundo:AddButton({
    Name = "Virar Zumbi (Brookhaven)",
    Callback = function()
        game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avatar1Editor1"):FireServer("UpdateCharacter", {["ZombieAvatar"] = true})
    end
})

---
-- ABA 7: CONFIG
---
local TabConfig = Window:MakeTab({"Config", "settings"})
TabConfig:AddButton({
    Name = "Infinite Yield",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Edgeiy/infiniteyield/master/source"))()
    end
})

redzlib:Notify({Title = "Cat Hub", Text = "Script completo carregado!", Duration = 5})


local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()

-- 1. Janela Principal
local Window = WindUI:CreateWindow({
    Title = "Cat Hub Admin",
    Icon = "shield",
    Author = "by scripts073 and davi lucas and scripts_H54",
    Folder = "CatHubFix",
    Size = UDim2.fromOffset(580, 460),
    Transparent = true,
    Theme = "Dark",
    SideBarWidth = 200,
    User = {
        Enabled = true,
        Title = "Admin Ativo",
        Subtitle = game.Players.LocalPlayer.Name
    }
})

local lp = game.Players.LocalPlayer
local SelectedPlayer = nil

-- 2. Função para capturar a lista real de jogadores (Você + Outros)
local function GetPlayerNames()
    local names = {}
    for _, v in pairs(game.Players:GetPlayers()) do
        table.insert(names, v.Name)
    end
    return names
end

-- 3. Aba de Comandos
local AdminTab = Window:Tab({ Title = "Comandos", Icon = "terminal" })

-- 4. Dropdown (Inicia com a lista de quem já está no servidor)
local PlayerDropdown = AdminTab:Dropdown({
    Title = "Selecionar Jogador",
    Multi = false,
    Options = GetPlayerNames(),
    Callback = function(v)
        SelectedPlayer = game.Players:FindFirstChild(v)
    end
})

-- 5. Botão de Atualizar (Usa o método SetOptions da WindUI)
AdminTab:Button({
    Title = "Atualizar Lista de Players",
    Icon = "refresh-cw",
    Callback = function()
        local listaAtualizada = GetPlayerNames()
        PlayerDropdown:SetOptions(listaAtualizada) -- Arrumado aqui
        WindUI:Notify({
            Title = "Cat Hub",
            Content = "Lista de jogadores atualizada!",
            Duration = 2
        })
    end
})

AdminTab:Section({ Title = "Ações Administrativas" })

-- 6. Comandos ADM
AdminTab:Button({
    Title = "Kick",
    Callback = function()
        if SelectedPlayer then
            SelectedPlayer:Kick("expulsado pelo o painel ADM do Cat hub")
        end
    end
})

AdminTab:Button({
    Title = "Freeze",
    Callback = function()
        if SelectedPlayer and SelectedPlayer.Character then
            SelectedPlayer.Character.HumanoidRootPart.Anchored = true
        end
    end
})

AdminTab:Button({
    Title = "Unfreeze",
    Callback = function()
        if SelectedPlayer and SelectedPlayer.Character then
            SelectedPlayer.Character.HumanoidRootPart.Anchored = false
        end
    end
})

AdminTab:Button({
    Title = "Jail",
    Callback = function()
        if SelectedPlayer and SelectedPlayer.Character then
            local pos = SelectedPlayer.Character.HumanoidRootPart.Position
            local jailModel = Instance.new("Model", workspace)
            jailModel.Name = "CatJail_" .. SelectedPlayer.Name
            
            local function createPart(size, offset)
                local p = Instance.new("Part", jailModel)
                p.Size = size
                p.Position = pos + offset
                p.Anchored = true
                p.Material = Enum.Material.ForceField
                p.BrickColor = BrickColor.new("Really black")
            end
            
            createPart(Vector3.new(10, 1, 10), Vector3.new(0, -5, 0)) -- Chão
            createPart(Vector3.new(1, 10, 10), Vector3.new(5, 0, 0))  -- Parede
            createPart(Vector3.new(1, 10, 10), Vector3.new(-5, 0, 0)) -- Parede
            createPart(Vector3.new(10, 10, 1), Vector3.new(0, 0, 5))  -- Parede
            createPart(Vector3.new(10, 10, 1), Vector3.new(0, 0, -5)) -- Parede
            
            SelectedPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(pos)
        end
    end
})

AdminTab:Button({
    Title = "Unjail",
    Callback = function()
        if SelectedPlayer then
            local j = workspace:FindFirstChild("CatJail_" .. SelectedPlayer.Name)
            if j then j:Destroy() end
        end
    end
})

AdminTab:Button({
    Title = "Kill",
    Callback = function()
        if SelectedPlayer and SelectedPlayer.Character then
            SelectedPlayer.Character.Humanoid.Health = 0
        end
    end
})

-- 7. Auto-update: Atualiza a lista sozinho quando alguém entra ou sai
game.Players.PlayerAdded:Connect(function()
    PlayerDropdown:SetOptions(GetPlayerNames())
end)

game.Players.PlayerRemoving:Connect(function()
    PlayerDropdown:SetOptions(GetPlayerNames())
end)

WindUI:Notify({
    Title = "Painel Arrumado",
    Content = "Seu nome e dos players agora aparecem!",
    Duration = 5,
    Image = "check"
})
