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

local function addUICorner(parent, radius)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, radius)
    corner.Parent = parent
    return corner
end

local function createTextLabel(props)
    local label = Instance.new("TextLabel")
    for k,v in pairs(props) do label[k] = v end
    label.Parent = Frame
    return label
end

local function createButton(props)
    local btn = Instance.new("TextButton")
    for k,v in pairs(props) do btn[k] = v end
    addUICorner(btn, 14)
    btn.Parent = Frame
    return btn
end

-- Criar GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "IngeniousScriptDupeGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = playerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 380, 0, 430)
Frame.Position = UDim2.new(0.32, 0, 0.25, 0)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui
addUICorner(Frame, 20)

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

local Footer = Instance.new("TextLabel")
Footer.Size = UDim2.new(0.9, 0, 0.07, 0)
Footer.Position = UDim2.new(0.05, 0, 0.93, 0)
Footer.BackgroundTransparency = 1
Footer.Text = "Keep it dark and deadly..."
Footer.TextColor3 = Color3.fromRGB(200, 0, 0)
Footer.Font = Enum.Font.GothamBlack
Footer.TextScaled = true
Footer.TextStrokeTransparency = 0.6
Footer.BorderSizePixel = 0
Footer.Parent = Frame

-- Status label
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.9, 0, 0.08, 0)
StatusLabel.Position = UDim2.new(0.05, 0, 0.83, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.TextSize = 20
StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
StatusLabel.Text = "ESP, Voo e Invisibilidade desativados"
StatusLabel.Parent = Frame

-- Botão duplicar item
local ButtonDupe = createButton({
    Size = UDim2.new(0.9, 0, 0.12, 0),
    Position = UDim2.new(0.05, 0, 0.15, 0),
    BackgroundColor3 = Color3.fromRGB(30, 0, 0),
    TextColor3 = Color3.fromRGB(255, 20, 20),
    Font = Enum.Font.GothamBold,
    TextSize = 26,
    Text = "💀 DUPLICAR ITEM NA MÃO 💀",
    BorderSizePixel = 0,
})

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

local ESPButton = createButton({
    Size = UDim2.new(0.9, 0, 0.1, 0),
    Position = UDim2.new(0.05, 0, 0.28, 0),
    BackgroundColor3 = Color3.fromRGB(40, 0, 0),
    TextColor3 = Color3.fromRGB(255, 0, 0),
    Font = Enum.Font.GothamBold,
    TextSize = 22,
    Text = "🧟‍♂️ ESP: OFF",
    BorderSizePixel = 0,
})

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

local FlyButton = createButton({
    Size = UDim2.new(0.9, 0, 0.1, 0),
    Position = UDim2.new(0.05, 0, 0.40, 0),
    BackgroundColor3 = Color3.fromRGB(40, 0, 0),
    TextColor3 = Color3.fromRGB(255, 0, 0),
    Font = Enum.Font.GothamBold,
    TextSize = 22,
    Text = "🦇 VOAR: OFF",
    BorderSizePixel = 0,
})

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

-- Invisibilidade
local Invisible = false

local function setInvisibility(state)
    local character = player.Character
    if not character then return end
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") or part:IsA("Decal") or part:IsA("Texture") then
            if state then
                part.Transparency = 1
                if part:IsA("Decal") or part:IsA("Texture") then
                    part.Transparency = 1
                end
            else
                part.Transparency = 0
                if part:IsA("Decal") or part:IsA("Texture") then
                    part.Transparency = 0
                end
            end
        elseif part:IsA("Accessory") then
            if part.Handle then
                part.Handle.Transparency = state and 1 or 0
            end
        end
    end
end

local InvisButton = createButton({
    Size = UDim2.new(0.9, 0, 0.1, 0),
    Position = UDim2.new(0.05, 0, 0.52, 0),
    BackgroundColor3 = Color3.fromRGB(40, 0, 0),
    TextColor3 = Color3.fromRGB(255, 0, 0),
    Font = Enum.Font.GothamBold,
    TextSize = 22,
    Text = "🕵️‍♂️ INVISIBILIDADE: OFF",
    BorderSizePixel = 0,
})

InvisButton.MouseButton1Click:Connect(function()
    Invisible = not Invisible
    setInvisibility(Invisible)
    if Invisible then
        InvisButton.Text = "🕵️‍♂️ INVISIBILIDADE: ON"
        InvisButton.TextColor3 = Color3.fromRGB(0, 255, 0)
        StatusLabel.Text = "Invisibilidade ativada"
    else
        InvisButton.Text = "🕵️‍♂️ INVISIBILIDADE: OFF"
        InvisButton.TextColor3 = Color3.fromRGB(255, 0, 0)
        StatusLabel.Text = "Invisibilidade desativada"
    end
end)

-- Teleport para player
local TpInputBox = Instance.new("TextBox")
TpInputBox.Size = UDim2.new(0.6, 0, 0.07, 0)
TpInputBox.Position = UDim2.new(0.05, 0, 0.65, 0)
TpInputBox.PlaceholderText = "Nome do player para TP"
TpInputBox.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
TpInputBox.TextColor3 = Color3.fromRGB(255, 255, 255)
TpInputBox.Font = Enum.Font.Gotham
TpInputBox.TextSize = 20
TpInputBox.BorderSizePixel = 0
TpInputBox.Parent = Frame
addUICorner(TpInputBox, 14)

local TpButton = createButton({
    Size = UDim2.new(0.3, 0, 0.07, 0),
    Position = UDim2.new(0.67, 0, 0.65, 0),
    BackgroundColor3 = Color3.fromRGB(30, 0, 0),
    TextColor3 = Color3.fromRGB(255, 0, 0),
    Font = Enum.Font.GothamBold,
    TextSize = 20,
    Text = "📍 TELEPORTAR",
    BorderSizePixel = 0,
})

TpButton.MouseButton1Click:Connect(function()
    local targetName = TpInputBox.Text
    if targetName == "" then
        StatusLabel.Text = "Digite um nome válido!"
        return
    end
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then
        StatusLabel.Text = "Jogador não encontrado!"
        return
    end
    local char = player.Character
    local targetChar = targetPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then
        StatusLabel.Text = "Seu personagem não está pronto!"
        return
    end
    if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
        StatusLabel.Text = "Personagem do alvo não está pronto!"
        return
    end
    char.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame + Vector3.new(0,5,0)
    StatusLabel.Text = "Teleportado para "..targetName
end)

]])()
