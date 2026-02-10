local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/minhdepzai-v/LibraryRobloc/refs/heads/main/RedzLibrary.lua"))()

local Window = redzlib:MakeWindow({
  Title = "Reset Hub Oficial Português",
  SubTitle = "by c00lkidd & davi lucas",
  SaveFolder = "ResetHubBrookhaven.json"
})

-- // TEMA PRETO E VERMELHO //
redzlib:SetTheme({
    Accent = Color3.fromRGB(255, 0, 0),
    Background = Color3.fromRGB(5, 5, 5),
    Section = Color3.fromRGB(15, 15, 15),
    Text = Color3.fromRGB(255, 255, 255),
    PlaceholderText = Color3.fromRGB(120, 120, 120)
})

-- Botão de Minimizar (Bolinha Redonda)
Window:AddMinimizeButton({
    Button = { 
        Image = "rbxassetid://129694133855303", 
        BackgroundTransparency = 0 
    },
    Corner = { CornerRadius = UDim.new(1, 0) },
})

---
-- VARIÁVEIS GLOBAIS E FUNÇÕES AUXILIARES
---
local SelectedPlayer = ""
local FlingEnabled = false
local espColor = Color3.fromRGB(255, 0, 0)
local espEnabled = false

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

TabInfo:AddDiscordInvite({
    Name = "Reset Hub Community",
    Description = "Entre no nosso Discord para novidades!",
    Logo = "rbxassetid://18751483361",
    Invite = "https://discord.gg/resethub",
})

TabInfo:AddSection({"Status"})
TabInfo:AddParagraph({"Informações do Script", "O script está: Online 🟢\nVersão: 1.0.2"})

TabInfo:AddSection({"Equipe do Reset Hub"})
TabInfo:AddParagraph({
    "Membros da Staff", 
    "Dono: c00lkidd\nSub-dono: Davi Lucas\nAdministrador: Ninguém\nModerador: Ninguém\nTester: Ninguém"
})

---
-- ABA TROLL
---
local TabTroll = Window:MakeTab({"Troll", "skull"})

TabTroll:AddSection({"Seletor de Alvos"})

local PlayerDropdown = TabTroll:AddDropdown({
    Name = "Selecionar Player",
    Description = "Escolha um jogador para as ações",
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
        redzlib:SetNotification({
            Title = "Reset Hub",
            Content = "Lista de jogadores sincronizada!",
            Duration = 2
        })
    end
})

TabTroll:AddSection({"Ações Troll"})

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
                            local targetPos = targetPlayer.Character.HumanoidRootPart.CFrame
                            local root = game.Players.LocalPlayer.Character.HumanoidRootPart
                            root.CFrame = targetPos * CFrame.new(0, 0, 1)
                            root.Velocity = Vector3.new(99999, 99999, 99999)
                        end
                    end)
                    task.wait(0.1)
                end
            end)
        else
            pcall(function()
                game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
            end)
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
            local highlight = char:FindFirstChild("ResetESP") or Instance.new("Highlight", char)
            highlight.Name = "ResetESP"
            highlight.FillColor = espColor
            highlight.OutlineColor = (espColor == Color3.fromRGB(0,0,0) and Color3.fromRGB(50,50,50) or espColor)
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
    Options = {"Vermelho", "Azul", "Laranja", "Verde", "Rosa", "Roxo", "Preto", "Branco"},
    Default = "Vermelho",
    Callback = function(Value)
        local colors = {
            ["Vermelho"] = Color3.fromRGB(255, 0, 0),
            ["Azul"] = Color3.fromRGB(0, 102, 255),
            ["Laranja"] = Color3.fromRGB(255, 165, 0),
            ["Verde"] = Color3.fromRGB(0, 255, 0),
            ["Rosa"] = Color3.fromRGB(255, 20, 147),
            ["Roxo"] = Color3.fromRGB(138, 43, 226),
            ["Preto"] = Color3.fromRGB(0, 0, 0),
            ["Branco"] = Color3.fromRGB(255, 255, 255)
        }
        espColor = colors[Value] or colors["Vermelho"]
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

TabBrook:AddToggle({
    Name = "Pulo Infinito",
    Default = false,
    Callback = function(s)
        _G.InfJump = s
        game:GetService("UserInputService").JumpRequest:Connect(function()
            if _G.InfJump then
                game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
            end
        end)
    end
})

---
-- ABA SCRIPTS
---
local TabScripts = Window:MakeTab({"Scripts", "scroll"})
TabScripts:AddButton({ Name = "Infinite Yield", Callback = function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Edgeiy/infiniteyield/master/source"))() end })
TabScripts:AddButton({ Name = "Fly (Voo)", Callback = function() loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.lua"))() end })
TabScripts:AddButton({ Name = "CMD-X", Callback = function() loadstring(game:HttpGet("https://raw.githubusercontent.com/CMD-X/CMD-X/master/Source", true))() end })

---
-- ABA CONFIGURAÇÕES
---
local TabConfig = Window:MakeTab({"Config", "settings"})
TabConfig:AddSlider({
    Name = "Velocidade",
    Min = 16, Max = 200, Default = 16,
    Callback = function(Value)
        pcall(function() game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value end)
    end
})

Window:SelectTab(TabInfo)
