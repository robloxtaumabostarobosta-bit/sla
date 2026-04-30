-- YORGUTE HUB (legítimo para uso no seu próprio jogo)
-- Coloque como LocalScript em StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Carregar Rayfield
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "YORGUTE HUB",
   LoadingTitle = "YORGUTE HUB",
   LoadingSubtitle = "UI legítima",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = "YorguteHub",
      FileName = "Config"
   }
})

-- Abas
local VisualTab = Window:CreateTab("Visual", 4483362458)
local PlayerTab = Window:CreateTab("Player", 4483362458)

-- ===== VISUAL =====

-- Mostrar nomes (BillboardGui)
local showNames = false
local function applyNameTags(state)
   for _, plr in ipairs(Players:GetPlayers()) do
      if plr ~= LocalPlayer and plr.Character then
         local char = plr.Character
         local existing = char:FindFirstChild("YH_NameTag")
         if state and not existing then
            local bb = Instance.new("BillboardGui")
            bb.Name = "YH_NameTag"
            bb.Size = UDim2.new(0, 120, 0, 40)
            bb.AlwaysOnTop = true
            bb.Adornee = char:FindFirstChild("Head")
            bb.Parent = char

            local txt = Instance.new("TextLabel")
            txt.Size = UDim2.fromScale(1,1)
            txt.BackgroundTransparency = 1
            txt.Text = plr.DisplayName
            txt.TextScaled = true
            txt.TextColor3 = Color3.new(1,1,1)
            txt.Parent = bb
         elseif not state and existing then
            existing:Destroy()
         end
      end
   end
end

VisualTab:CreateToggle({
   Name = "Mostrar nomes (NameTag)",
   CurrentValue = false,
   Callback = function(v)
      showNames = v
      applyNameTags(v)
   end
})

-- Highlight apenas teammates (exemplo de “ESP permitido”)
local highlightTeam = false
local function applyHighlights(state)
   for _, plr in ipairs(Players:GetPlayers()) do
      if plr ~= LocalPlayer and plr.Character then
         local char = plr.Character
         local hl = char:FindFirstChild("YH_Highlight")
         local sameTeam = (plr.Team ~= nil and plr.Team == LocalPlayer.Team)

         if state and sameTeam and not hl then
            hl = Instance.new("Highlight")
            hl.Name = "YH_Highlight"
            hl.FillColor = Color3.fromRGB(0, 170, 255)
            hl.OutlineTransparency = 1
            hl.Parent = char
         elseif (not state or not sameTeam) and hl then
            hl:Destroy()
         end
      end
   end
end

VisualTab:CreateToggle({
   Name = "Destacar teammates",
   CurrentValue = false,
   Callback = function(v)
      highlightTeam = v
      applyHighlights(v)
   end
})

-- FOV (visual/câmera)
local currentFOV = 70
VisualTab:CreateSlider({
   Name = "FOV (Câmera)",
   Range = {60, 120},
   Increment = 1,
   CurrentValue = 70,
   Callback = function(v)
      currentFOV = v
      workspace.CurrentCamera.FieldOfView = v
   end
})

-- ===== PLAYER =====

-- Velocidade (ajuste legítimo; ideal validar no servidor)
local speedValue = 16
local function setSpeed(v)
   speedValue = v
   if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
      LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = v
   end
end

PlayerTab:CreateSlider({
   Name = "Velocidade",
   Range = {8, 24}, -- mantenha limites equilibrados
   Increment = 1,
   CurrentValue = 16,
   Callback = function(v)
      setSpeed(v)
   end
})

-- Reaplicar em respawn
LocalPlayer.CharacterAdded:Connect(function()
   task.wait(1)
   setSpeed(speedValue)
   applyNameTags(showNames)
   applyHighlights(highlightTeam)
end)
