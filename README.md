loadstring([[
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove GUI antiga se existir
if playerGui:FindFirstChild("IngeniousScriptDupeGUI") then
    playerGui.IngeniousScriptDupeGUI:Destroy()
end

-- Função helper para corners
local function addUICorner(parent, radius)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, radius)
    corner.Parent = parent
    return corner
end

-- Criar GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "IngeniousScriptDupeGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = playerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 350, 0, 360)
Frame.Position = UDim2.new(0.35, 0, 0.3, 0)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui
addUICorner(Frame, 20)

-- Título macabro
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0.9, 0, 0.12, 0)
Title.Position = UDim2.new(0.05, 0, 0.02, 0)
Title.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Title.BackgroundTransparency = 0.4
Title.Text = "DEV BY Tr1nX_777 | Macabro"
Title.TextColor3 = Color3.fromRGB(255, 0, 0)
Title.Font = Enum.Font.GothamBlack
Title.TextScaled = true
Title.TextStrokeTransparency = 0.5
Title.BorderSizePixel = 0
Title.Parent = Frame
addUICorner(Title, 16)

-- Texto rodapé
local Footer = Instance.new("TextLabel")
Footer.Size = UDim2.new(0.9, 0, 0.07, 0)
Footer.Position = UDim2.new(0.05, 0, 0.9, 0)
Footer.BackgroundTransparency = 1
Footer.Text = "Keep it dark and deadly..."
Footer.TextColor3 = Color3.fromRGB(200, 0, 0)
Footer.Font = Enum.Font.GothamBlack
Footer.TextScaled = true
Footer.TextStrokeTransparency = 0.6
Footer.BorderSizePixel = 0
Footer.Parent = Frame

-- Botão duplicar
local ButtonDupe = Instance.new("TextButton")
ButtonDupe.Size = UDim2.new(0.9, 0, 0.15, 0)
ButtonDupe.Position = UDim2.new(0.05, 0, 0.15, 0)
ButtonDupe.BackgroundColor3 = Color3.fromRGB(30, 0, 0)
ButtonDupe.TextColor3 = Color3.fromRGB(255, 20, 20)
ButtonDupe.Font = Enum.Font.GothamBold
ButtonDupe.TextSize = 26
ButtonDupe.Text = "💀 DUPLICAR ITEM NA MÃO 💀"
ButtonDupe.BorderSizePixel = 0
ButtonDupe.Parent = Frame
addUICorner(ButtonDupe, 16)

local function setDupeButtonText(text, color)
    ButtonDupe.Text = text
    ButtonDupe.BackgroundColor3 = color
    task.delay(1.3, function()
        ButtonDupe.Text = "💀 DUPLICAR ITEM NA MÃO 💀"
        ButtonDupe.BackgroundColor3 = Color3.fromRGB(30, 0, 0)
    end)
end

local function getAnimController(tool)
    return tool:FindFirstChildOfClass("AnimationController") or tool:FindFirstChildOfClass("Humanoid")
end

ButtonDupe.MouseButton1Click:Connect(function()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    local tool = character:FindFirstChildOfClass("Tool")

    if tool and humanoid then
        local clone = tool:Clone()
        clone.Parent = character
        humanoid:EquipTool(clone)

        local origAnimCtrl = getAnimController(tool)
        local cloneAnimCtrl = getAnimController(clone)

        if origAnimCtrl and cloneAnimCtrl then
            for _, track in pairs(origAnimCtrl:GetPlayingAnimationTracks()) do
                local anim = track.Animation
                if anim then
                    local newTrack = cloneAnimCtrl:LoadAnimation(anim)
                    newTrack:Play()
                    newTrack.TimePosition = track.TimePosition
                    newTrack.Speed = track.Speed
                end
            end
        end
        setDupeButtonText("✔️ DUPLICADO!", Color3.fromRGB(0, 180, 0))
    else
        setDupeButtonText("✖️ SEM ITEM NA MÃO!", Color3.fromRGB(180, 0, 0))
    end
end)

-- Status texto
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.9, 0, 0.1, 0)
StatusLabel.Position = UDim2.new(0.05, 0, 0.75, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.TextSize = 20
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.Text = "ESP e Voo desativados"
StatusLabel.Parent = Frame

-- ESP
local ESPEnabled = false
local espHighlights = {}

local function toggleESP()
    ESPEnabled = not ESPEnabled
    if ESPEnabled then
        StatusLabel.Text = "ESP ATIVADO"
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                local hl = Instance.new("Highlight")
                hl.Adornee = plr.Character
                hl.FillColor = Color3.new(1,0,0)
                hl.OutlineColor = Color3.new(1,0,0)
                hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                hl.Parent = player.PlayerGui
                espHighlights[plr] = hl
            end
        end
        Players.PlayerAdded:Connect(function(plr)
            if ESPEnabled and plr ~= player then
                plr.CharacterAdded:Connect(function(char)
                    task.wait(1)
                    if ESPEnabled then
                        local hl = Instance.new("Highlight")
                        hl.Adornee = char
                        hl.FillColor = Color3.new(1,0,0)
                        hl.OutlineColor = Color3.new(1,0,0)
                        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        hl.Parent = player.PlayerGui
                        espHighlights[plr] = hl
                    end
                end)
            end
        end)
    else
        StatusLabel.Text = "ESP DESATIVADO"
        for _, hl in pairs(espHighlights) do
            if hl then hl:Destroy() end
        end
        espHighlights = {}
    end
end

-- Botão ESP
local ESPButton = Instance.new("TextButton")
ESPButton.Size = UDim2.new(0.9, 0, 0.13, 0)
ESPButton.Position = UDim2.new(0.05, 0, 0.35, 0)
ESPButton.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
ESPButton.TextColor3 = Color3.fromRGB(255, 0, 0)
ESPButton.Font = Enum.Font.GothamBold
ESPButton.TextSize = 24
ESPButton.Text = "🧟‍♂️ ESP: OFF"
ESPButton.BorderSizePixel = 0
ESPButton.Parent = Frame
addUICorner(ESPButton, 14)

ESPButton.MouseButton1Click:Connect(function()
    toggleESP()
    if ESPEnabled then
        ESPButton.Text = "🧟‍♂️ ESP: ON"
        ESPButton.TextColor3 = Color3.fromRGB(0, 255, 0)
    else
        ESPButton.Text = "🧟‍♂️ ESP: OFF"
        ESPButton.TextColor3 = Color3.fromRGB(255, 0, 0)
    end
end)

-- Voo
local FlyEnabled = false
local flying = false
local bodyVelocity, bodyGyro
local flySpeed = 100

local function startFly()
    if flying then return end
    flying = true
    local character = player.Character
    if not character then return end
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.P = 9e4
    bodyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
    bodyGyro.CFrame = hrp.CFrame
    bodyGyro.Parent = hrp

    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
    bodyVelocity.Velocity = Vector3.new(0,0,0)
    bodyVelocity.Parent = hrp

    RunService:BindToRenderStep("Fly", 301, function()
        if not flying then return end
        local moveVec = Vector3.new(0,0,0)
        local cam = workspace.CurrentCamera
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            moveVec = moveVec + cam.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            moveVec = moveVec - cam.CFrame.LookVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            moveVec = moveVec - cam.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            moveVec = moveVec + cam.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            moveVec = moveVec + Vector3.new(0,1,0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            moveVec = moveVec - Vector3.new(0,1,0)
        end
        if moveVec.Magnitude > 0 then
            moveVec = moveVec.Unit * flySpeed
        end
        bodyVelocity.Velocity = moveVec
        bodyGyro.CFrame = cam.CFrame
    end)
end

local function stopFly()
    if not flying then return end
    flying = false
    RunService:UnbindFromRenderStep("Fly")
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
    if bodyGyro then
        bodyGyro:Destroy()
        bodyGyro = nil
    end
end

-- Botão Voo
local FlyButton = Instance.new("TextButton")
FlyButton.Size = UDim2.new(0.9, 0, 0.13, 0)
FlyButton.Position = UDim2.new(0.05, 0, 0.5, 0)
FlyButton.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
FlyButton.TextColor3 = Color3.fromRGB(255, 0, 0)
FlyButton.Font = Enum.Font.GothamBold
FlyButton.TextSize = 24
FlyButton.Text = "🦇 VOAR: OFF"
FlyButton.BorderSizePixel = 0
FlyButton.Parent = Frame
addUICorner(FlyButton, 14)

FlyButton.MouseButton1Click:Connect(function()
    if FlyEnabled then
        FlyEnabled = false
        FlyButton.Text = "🦇 VOAR: OFF"
        FlyButton.TextColor3 = Color3.fromRGB(255, 0, 0)
        StatusLabel.Text = "Voo desativado"
        stopFly()
    else
        FlyEnabled = true
        FlyButton.Text = "🦇 VOAR: ON"
        FlyButton.TextColor3 = Color3.fromRGB(0, 255, 0)
        StatusLabel.Text = "Voo ativado - Use WASD, Espaço e Ctrl"
        startFly()
    end
end)

]])()
