-- // INICIALIZAÇÃO E IDENTIDADE //
local success, content = pcall(function()
    return game:HttpGet("https://nexviewsservice.shardweb.app/services/s4turn_hub/start")
end)

local RE = game:GetService("ReplicatedStorage"):WaitForChild("RE")
pcall(function()
    RE["1RPNam1eTex1t"]:FireServer("RolePlayBio", "Cat Hub v1 - Edição Completa")
    RE["1RPNam1eTex1t"]:FireServer("RolePlayName", "ã¾ Cat Hub v1")
    RE["1RPNam1eColo1r"]:FireServer("PickingRPBioColor", Color3.new(255, 0, 0))
end)

-- // CARREGAMENTO DA INTERFACE (LIBRARY) //
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/toparemos/Library/refs/heads/main/library.lua"))()

local Window = redzlib:MakeWindow({
    Title = "Cat hub v1 português",
    SubTitle = "by scripts073, davi lucas e scripts_H54",
    SaveFolder = "CatHubBrookhaven.json"
})

Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://14096965095", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(35, 1) },
})

---
-- VARIÁVEIS GLOBAIS
---
local Players = game:GetService("Players")
local LP = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local SelectedCombatPlayer = ""
local SelectedTrollPlayer = nil
local AimbotEnabled = false
local AimPart = "Head"
local espEnabled = false
local espColor = Color3.fromRGB(255, 0, 0)

-- Estados Troll
local isFollowingKill = false
local isFollowingPull = false
local BugAllAtivo = false
local FlingAtivo = false
local selectedFlingMethod = "Sofá"
local selectedKillMethod = "Sofá"

local function getPlayers()
    local list = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LP then table.insert(list, plr.Name) end
    end
    return list
end

---
-- ABA 1: INFORMAÇÕES
---
local TabInfo = Window:MakeTab({"Informações", "info"})
TabInfo:AddSection({"Creditos do Hub"})
TabInfo:AddDiscordInvite({
    Name = "â½¦ Cat Hub Studios",
    Description = "Discord oficial: https://discord.gg/vUNHqeUtQq",
    Logo = "rbxassetid://74041792558354",
    Invite = "https://discord.gg/vUNHqeUtQq",
})
TabInfo:AddSection({"Status do Sistema"})
TabInfo:AddParagraph({"Detalhes", "Status: Online 🟢\nVersão: 1.0.2 - Edição Troll"})

---
-- ABA 2: COMBATE (AIMBOT)
---
local TabCombat = Window:MakeTab({"Combate", "crosshair"})
TabCombat:AddSection({"Configurações de Alvo"})
local CombatDropdown = TabCombat:AddDropdown({
    Name = "Selecionar Alvo (Combate)",
    Options = getPlayers(),
    Default = "",
    Callback = function(v) SelectedCombatPlayer = v end
})
TabCombat:AddButton({Name = "Atualizar Lista", Callback = function() CombatDropdown:SetOptions(getPlayers()) end})
TabCombat:AddDropdown({
    Name = "Parte do Corpo",
    Options = {"Cabeça", "Barriga", "Braço Direito", "Braço Esquerdo"},
    Default = "Cabeça",
    Callback = function(v)
        local parts = {["Cabeça"]="Head", ["Barriga"]="HumanoidRootPart", ["Braço Direito"]="RightUpperArm", ["Braço Esquerdo"]="LeftUpperArm"}
        AimPart = parts[v] or "Head"
    end
})
TabCombat:AddToggle({Name = "Ativar Aimbot", Default = false, Callback = function(v) AimbotEnabled = v end})

---
-- ABA 3: PLAYER
---
local TabPlayer = Window:MakeTab({"Player", "user"})
TabPlayer:AddSlider({Name = "Velocidade (WalkSpeed)", Min = 16, Max = 500, Default = 16, Callback = function(v) if LP.Character then LP.Character.Humanoid.WalkSpeed = v end end})
TabPlayer:AddSlider({Name = "Pulo (JumpPower)", Min = 50, Max = 1000, Default = 50, Callback = function(v) if LP.Character then LP.Character.Humanoid.UseJumpPower = true LP.Character.Humanoid.JumpPower = v end end})
TabPlayer:AddSlider({Name = "Gravidade", Min = 0, Max = 196.2, Default = 196.2, Callback = function(v) workspace.Gravity = v end})

---
-- ABA 4: TROLL (A NOVA TAB COMPLETA)
---
local TabTroll = Window:MakeTab({"troll", "skull"})

TabTroll:AddSection({"Seletor de Alvo"})
local TrollDropdown = TabTroll:AddDropdown({
    Name = "Selecionar Jogador",
    Options = getPlayers(),
    Default = "",
    Callback = function(v) SelectedTrollPlayer = Players:FindFirstChild(v) end
})
TabTroll:AddButton({Name = "Atualizar Lista", Callback = function() TrollDropdown:SetOptions(getPlayers()) end})

TabTroll:AddToggle({
    Name = "View Player (Observar)",
    Default = false,
    Callback = function(state)
        if state and SelectedTrollPlayer and SelectedTrollPlayer.Character then
            Camera.CameraSubject = SelectedTrollPlayer.Character.Humanoid
        else
            Camera.CameraSubject = LP.Character.Humanoid
        end
    end
})

TabTroll:AddSection({"Kill & Pull"})
TabTroll:AddDropdown({
    Name = "Método Kill/Pull",
    Options = {"Sofá", "Ônibus"},
    Default = "Sofá",
    Callback = function(v) selectedKillMethod = v end
})
TabTroll:AddButton({
    Name = "Ativar Matar/Puxar",
    Callback = function()
        if not SelectedTrollPlayer then return end
        if selectedKillMethod == "Sofá" then
            RE["1Too1l"]:InvokeServer("PickingTools", "Couch")
            task.wait(0.3)
            local tool = LP.Backpack:FindFirstChild("Couch") or LP.Character:FindFirstChild("Couch")
            if tool then tool.Parent = LP.Character end
        end
        isFollowingKill = true
    end
})

TabTroll:AddSection({"Flings Avançados"})
TabTroll:AddDropdown({
    Name = "Método de Fling",
    Options = {"Sofá", "Ônibus", "Bola", "Bola V2", "Barco", "Caminhão"},
    Default = "Sofá",
    Callback = function(v) selectedFlingMethod = v end
})
TabTroll:AddToggle({
    Name = "Ativar Fling",
    Default = false,
    Callback = function(state)
        FlingAtivo = state
        if state then
            task.spawn(function()
                while FlingAtivo do
                    pcall(function()
                        if selectedFlingMethod == "Bola" then RE["1Too1l"]:InvokeServer("PickingTools", "SoccerBall")
                        elseif selectedFlingMethod == "Ônibus" then RE["1Ca1r"]:FireServer("CreateVehicle", "SchoolBus", LP.Character.HumanoidRootPart.CFrame)
                        elseif selectedFlingMethod == "Barco" then RE["1Ca1r"]:FireServer("CreateVehicle", "Boat", LP.Character.HumanoidRootPart.CFrame) end
                    end)
                    task.wait(0.1)
                end
            end)
        else
            RE["1Ca1r"]:FireServer("DeleteAllVehicles")
        end
    end
})

TabTroll:AddSection({"Troll Global"})
TabTroll:AddToggle({
    Name = "Bug All (Assault Loop)",
    Default = false,
    Callback = function(state)
        BugAllAtivo = state
        task.spawn(function()
            while BugAllAtivo do
                pcall(function()
                    RE["1Clea1rTool1s"]:FireServer("ClearAllTools")
                    RE["1Too1l"]:InvokeServer("PickingTools", "Assault")
                    task.wait(0.2)
                    for _, p in pairs(Players:GetPlayers()) do
                        if p ~= LP and p.Character then RE["1Gu1n"]:FireServer(p.Character.HumanoidRootPart) end
                    end
                end)
                task.wait(0.5)
            end
        end)
    end
})

TabTroll:AddButton({
    Name = "Parar Tudo",
    Callback = function()
        isFollowingKill = false
        isFollowingPull = false
        BugAllAtivo = false
        FlingAtivo = false
        RE["1Ca1r"]:FireServer("DeleteAllVehicles")
        Camera.CameraSubject = LP.Character.Humanoid
    end
})

---
-- ABA 5: VISUAL
---
local TabVisual = Window:MakeTab({"Visual", "eye"})
TabVisual:AddToggle({
    Name = "Ativar ESP Highlight",
    Default = false,
    Callback = function(v)
        espEnabled = v
        if not v then
            for _, p in pairs(Players:GetPlayers()) do
                if p.Character and p.Character:FindFirstChild("CatHighlight") then p.Character.CatHighlight:Destroy() end
            end
        end
    end
})
TabVisual:AddDropdown({
    Name = "Cor do ESP",
    Options = {"Vermelho", "Verde", "Azul", "Amarelo"},
    Default = "Vermelho",
    Callback = function(v)
        local colors = {["Vermelho"]=Color3.new(1,0,0), ["Verde"]=Color3.new(0,1,0), ["Azul"]=Color3.new(0,0,1), ["Amarelo"]=Color3.new(1,1,0)}
        espColor = colors[v]
    end
})

---
-- ABA 6: MUNDO
---
local TabMundo = Window:MakeTab({"Mundo", "globe"})
TabMundo:AddButton({Name = "Virar Zumbi", Callback = function() RE:FindFirstChild("1Avatar1Editor1"):FireServer("UpdateCharacter", {["ZombieAvatar"] = true}) end})

---
-- ABA 7: CONFIG
---
local TabConfig = Window:MakeTab({"Config", "settings"})
TabConfig:AddButton({Name = "Infinite Yield (Admin)", Callback = function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Edgeiy/infiniteyield/master/source"))() end})

-- // LOOPS DE EXECUÇÃO //
RunService.Heartbeat:Connect(function()
    -- ESP
    if espEnabled then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LP and p.Character then
                local h = p.Character:FindFirstChild("CatHighlight") or Instance.new("Highlight", p.Character)
                h.Name = "CatHighlight"
                h.FillColor = espColor
                h.Enabled = true
            end
        end
    end
    -- Follow/Fling/Kill
    if (isFollowingKill or isFollowingPull or FlingAtivo) and SelectedTrollPlayer and SelectedTrollPlayer.Character then
        pcall(function()
            local targetHrp = SelectedTrollPlayer.Character.HumanoidRootPart
            LP.Character.HumanoidRootPart.CFrame = targetHrp.CFrame * CFrame.Angles(math.rad(tick()*1200), math.rad(tick()*1200), math.rad(tick()*1200))
        end)
    end
end)

RunService.RenderStepped:Connect(function()
    if AimbotEnabled and SelectedCombatPlayer ~= "" then
        local target = Players:FindFirstChild(SelectedCombatPlayer)
        if target and target.Character and target.Character:FindFirstChild(AimPart) then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[AimPart].Position)
        end
    end
end)

redzlib:Notify({Title = "Cat Hub v1", Text = "Hub completo com Nova Tab Troll carregado!", Duration = 5})

 


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
