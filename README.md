-- Trava de Segurança: Só executa no Sintonia RP
-- IMPORTANTE: Mude o ID abaixo para o ID oficial do Sintonia RP
local PlaceID = 123456789
if game.PlaceId ~= PlaceID then
return
end

local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/REDZHUB/RedzLibV2/main/source.lua"))()

-- Criando a Janela com detalhes Vermelhos
local Window = redzlib:MakeWindow({
Title = "SX HUB | by SX",
SubTitle = "Sintonia RP",
Color = Color3.fromRGB(255, 0, 0) -- Detalhes em Vermelho
})

-- HUD do Usuário (Canto Superior Esquerdo)
local Player = game.Players.LocalPlayer
local UserHUD = Instance.new("ScreenGui", Player.PlayerGui)
local MainFrame = Instance.new("Frame", UserHUD)
MainFrame.Size = UDim2.new(0, 180, 0, 60)
MainFrame.Position = UDim2.new(0, 10, 0, 10)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
MainFrame.BorderColor3 = Color3.fromRGB(255, 0, 0) -- Borda Vermelha
MainFrame.BorderSizePixel = 2

local Avatar = Instance.new("ImageLabel", MainFrame)
Avatar.Size = UDim2.new(0, 50, 0, 50)
Avatar.Position = UDim2.new(0, 5, 0, 5)
Avatar.Image = game.Players:GetUserThumbnailAsync(Player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)

local Name = Instance.new("TextLabel", MainFrame)
Name.Size = UDim2.new(0, 120, 0, 50)
Name.Position = UDim2.new(0, 55, 0, 5)
Name.Text = Player.Name
Name.TextColor3 = Color3.fromRGB(255, 255, 255)
Name.BackgroundTransparency = 1
Name.TextScaled = true

-- ABAS
local TabCombat = Window:MakeTab({"Combat", "crosshair"})
local TabVisual = Window:MakeTab({"Visual", "eye"})

--- FUNÇÕES COMBAT ---

TabCombat:AddToggle({
Name = "Aimbot",
Default = false,
Callback = function(Value)
_G.Aimbot = Value
-- Lógica de Aimbot (Suave para evitar ban)
spawn(function()
while G.Aimbot do
local target = nil
local dist = math.huge
for , v in pairs(game.Players:GetPlayers()) do
if v ~= Player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
local mag = (v.Character.HumanoidRootPart.Position - Player.Character.HumanoidRootPart.Position).Magnitude
if mag < dist then
dist = mag
target = v
end
end
end
if target then
workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, target.Character.HumanoidRootPart.Position)
end
task.wait()
end
end)
end
})

TabCombat:AddToggle({
Name = "Silent Aim",
Default = false,
Callback = function(Value)
_G.SilentAim = Value
end
})

TabCombat:AddToggle({
Name = "Godmode",
Default = false,
Callback = function(Value)
if Value then
Player.Character.Humanoid.MaxHealth = 9e9
Player.Character.Humanoid.Health = 9e9
else
Player.Character.Humanoid.MaxHealth = 100
end
end
})

-- FOV CIRCLE (Desenho na tela)
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.Color = Color3.fromRGB(255, 0, 0)
FOVCircle.Filled = false
FOVCircle.Visible = false

TabCombat:AddSlider({
Name = "FOV Radius",
Min = 0,
Max = 500,
Default = 100,
Callback = function(Value)
FOVCircle.Radius = Value
FOVCircle.Position = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)
FOVCircle.Visible = true
end
})

--- FUNÇÕES VISUAL ---

local function CreateESP(target)
local Box = Drawing.new("Square")
Box.Visible = false
Box.Color = Color3.fromRGB(255, 0, 0)
Box.Thickness = 1

game:GetService("RunService").RenderStepped:Connect(function()
if target.Character and target.Character:FindFirstChild("HumanoidRootPart") and _G.EspBox then
local Pos, OnScreen = workspace.CurrentCamera:WorldToViewportPoint(target.Character.HumanoidRootPart.Position)
if OnScreen then
