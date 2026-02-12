local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/minhdepzai-v/LibraryRobloc/refs/heads/main/RedzLibrary.lua"))()

local Window = redzlib:MakeWindow({
  Title = "Cat hub v1 português",
  SubTitle = "by scripts073 and davi lucas and scripts_H54",
  SaveFolder = "CatHubBrookhaven.json"
})

-- // TEMA PRETO E VERMELHO //
redzlib:SetTheme({
    Accent = Color3.fromRGB(255, 0, 0),
    Background = Color3.fromRGB(5, 5, 5),
    Section = Color3.fromRGB(15, 15, 15),
    Text = Color3.fromRGB(255, 255, 255),
    PlaceholderText = Color3.fromRGB(120, 120, 120)
})

-- Botão de Minimizar (Bolinha) com Foto Nova
Window:AddMinimizeButton({
    Button = { 
        Image = "rbxassetid://10800748303", 
        BackgroundTransparency = 0 
    },
    Corner = { CornerRadius = UDim.new(1, 0) },
})

---
-- VARIÁVEIS GLOBAIS
---
local SelectedPlayer = ""
local FlingEnabled = false
local espColor = Color3.fromRGB(255, 0, 0)
local espEnabled = false
local Camera = workspace.CurrentCamera

local function getPlayers()
    local list = {}
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= game.Players.LocalPlayer then
            table.insert(list, plr.Name)
        end
    end
    return list
end

---
-- ABA INFORMAÇÕES
---
local TabInfo = Window:MakeTab({"Informações", "info"})

-- Discord com Foto Nova e Link Atualizado
TabInfo:AddDiscordInvite({
    Name = "Cat hub community",
    Description = "Entre no nosso Discord para novidades!",
    Logo = "rbxassetid://6633546598", 
    Invite = "https://discord.gg/wsjtdbNPYf",
})

TabInfo:AddSection({"Status"})
TabInfo:AddParagraph({"Informações do Script", "O script está: Online 🟢\nVersão: 1.0.2"})

TabInfo:AddSection({"Equipe do Cat Hub"})
TabInfo:AddParagraph({
    "Membros da Staff", 
    "Dono: Scripts073\nSub-Dono: Davi Lucas and scripts_H54\nAdministrador: ninguém\nModerador: ninguém\nStaff: ninguém\nTester: ninguém"
})

---
-- ABA TROLL
---
local TabTroll = Window:MakeTab({"Troll", "skull"})

TabTroll:AddSection({"Seletor de Alvos"})

local PlayerDropdown = TabTroll:AddDropdown({
    Name = "Selecionar Player",
    Description = "Escolha um jogador primeiro",
    Options = getPlayers(),
    Default = "",
    Callback = function(Value)
        SelectedPlayer = Value
    end
})

TabTroll:AddButton({
    Name = "Atualizar Lista de Players",
    Callback = function()
        PlayerDropdown:SetOptions(getPlayers())
        redzlib:SetNotification({Title = "Cat Hub", Content = "Lista atualizada!", Duration = 2})
    end
})

TabTroll:AddSection({"Ações Troll"})

TabTroll:AddToggle({
    Name = "View Player (Espiar)",
    Default = false,
    Callback = function(state)
        if state then
            local target = game.Players:FindFirstChild(SelectedPlayer)
            if target and target.Character then
                Camera.CameraSubject = target.Character:FindFirstChildOfClass("Humanoid")
            end
        else
            Camera.CameraSubject = game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        end
    end
})

TabTroll:AddToggle({
    Name = "Ativar Fling (Sofá Troll)",
    Default = false,
    Callback = function(Value)
        FlingEnabled = Value
        if FlingEnabled then
            if SelectedPlayer == "" then return end
            task.spawn(function()
                while FlingEnabled do
                    pcall(function()
                        local targetPlayer = game.Players:FindFirstChild(SelectedPlayer)
                        if targetPlayer and targetPlayer.Character then
                            local root = game.Players.LocalPlayer.Character.HumanoidRootPart
                            root.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 1)
                            root.Velocity = Vector3.new(99999, 99999, 99999)
                        end
                    end)
                    task.wait(0.1)
                end
            end)
        else
            pcall(function() game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0) end)
        end
    end
})

---
-- ABA VISUAL
---
local TabVisual = Window:MakeTab({"Visual", "eye"})

local function updateESP()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            local char = player.Character
            local highlight = char:FindFirstChild("CatESP") or Instance.new("Highlight", char)
            highlight.Name = "CatESP"
            highlight.FillColor = espColor
            highlight.Enabled = espEnabled
        end
    end
end

TabVisual:AddToggle({
    Name = "Ativar ESP",
    Default = false,
    Callback = function(Value)
        espEnabled = Value
        updateESP()
    end
})

TabVisual:AddDropdown({
    Name = "Cor do ESP",
    Options = {"Vermelho", "Azul", "Verde", "Branco"},
    Default = "Vermelho",
    Callback = function(Value)
        local colors = {["Vermelho"] = Color3.fromRGB(255,0,0), ["Azul"] = Color3.fromRGB(0,0,255), ["Verde"] = Color3.fromRGB(0,255,0), ["Branco"] = Color3.fromRGB(255,255,255)}
        espColor = colors[Value]
        updateESP()
    end
})

---
-- ABA BROOKHAVEN
---
local TabBrook = Window:MakeTab({"Brookhaven", "car"})
TabBrook:AddButton({"Virar Zumbi", function()
    game:GetService("ReplicatedStorage").RE:FindFirstChild("1Avatar1Editor1"):FireServer("UpdateCharacter", {["ZombieAvatar"] = true})
end})

---
-- ABA SCRIPTS & CONFIG
---
local TabScripts = Window:MakeTab({"Scripts", "scroll"})
TabScripts:AddButton({ Name = "Infinite Yield", Callback = function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Edgeiy/infiniteyield/master/source"))() end })

local TabConfig = Window:MakeTab({"Config", "settings"})
TabConfig:AddSlider({
    Name = "Velocidade",
    Min = 16, Max = 200, Default = 16,
    Callback = function(Value)
        pcall(function() game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value end)
    end
})

Window:SelectTab(TabInfo)
