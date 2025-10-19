-- Dragon Hub - Brookhaven Mod Menu V2
-- Script para servidor privado (Versão Melhorada)

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local mouse = player:GetMouse()
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

-- Variáveis de configuração
local speedValue = 16
local jumpValue = 50
local teleportEnabled = false
local flyEnabled = false
local flySpeed = 25
local menuOpen = true
local lockCameraEnabled = false
local autoKillEnabled = false
local selectedTarget = nil

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local ScrollFrame = Instance.new("ScrollingFrame")

-- Botão Dragão Minimizado
local DragonButton = Instance.new("TextButton")
local DragonCorner = Instance.new("UICorner")

-- Configurar GUI
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "DragonHubGUI"
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.ResetOnSpawn = false

-- Frame Principal
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(139, 0, 0)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.3, 0, 0.15, 0)
MainFrame.Size = UDim2.new(0, 420, 0, 600)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true

local MainCorner = Instance.new("UICorner")
MainCorner.Parent = MainFrame
MainCorner.CornerRadius = UDim.new(0, 10)

-- Botão Dragão (Minimizado)
DragonButton.Parent = ScreenGui
DragonButton.BackgroundColor3 = Color3.fromRGB(139, 0, 0)
DragonButton.BorderSizePixel = 0
DragonButton.Position = UDim2.new(0, 10, 0.5, -40)
DragonButton.Size = UDim2.new(0, 80, 0, 80)
DragonButton.Font = Enum.Font.GothamBold
DragonButton.Text = "🐉"
DragonButton.TextColor3 = Color3.fromRGB(255, 255, 255)
DragonButton.TextSize = 40
DragonButton.Visible = false
DragonButton.Active = true
DragonButton.Draggable = true

DragonCorner.Parent = DragonButton
DragonCorner.CornerRadius = UDim.new(0, 15)

-- Título
Title.Parent = MainFrame
Title.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
Title.BorderSizePixel = 0
Title.Size = UDim2.new(1, 0, 0, 50)
Title.Font = Enum.Font.GothamBold
Title.Text = "🐉 DRAGON HUB V2 🐉"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 24
Title.TextStrokeTransparency = 0.5

local TitleCorner = Instance.new("UICorner")
TitleCorner.Parent = Title
TitleCorner.CornerRadius = UDim.new(0, 10)

-- ScrollFrame para os controles
ScrollFrame.Parent = MainFrame
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.Position = UDim2.new(0, 0, 0, 60)
ScrollFrame.Size = UDim2.new(1, 0, 1, -60)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 900)
ScrollFrame.ScrollBarThickness = 8
ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(220, 0, 0)

-- Função para criar labels
local function createLabel(text, yPos)
    local label = Instance.new("TextLabel")
    label.Parent = ScrollFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0, 20, 0, yPos)
    label.Size = UDim2.new(0, 380, 0, 30)
    label.Font = Enum.Font.GothamBold
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 16
    label.TextXAlignment = Enum.TextXAlignment.Left
    return label
end

-- Função para criar input box
local function createInputBox(yPos, placeholder, defaultText)
    local input = Instance.new("TextBox")
    input.Parent = ScrollFrame
    input.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
    input.BorderSizePixel = 0
    input.Position = UDim2.new(0, 20, 0, yPos)
    input.Size = UDim2.new(0, 250, 0, 40)
    input.Font = Enum.Font.Gotham
    input.PlaceholderText = placeholder
    input.Text = defaultText
    input.TextColor3 = Color3.fromRGB(255, 255, 255)
    input.TextSize = 18
    input.ClearTextOnFocus = false
    
    local corner = Instance.new("UICorner")
    corner.Parent = input
    corner.CornerRadius = UDim.new(0, 8)
    
    return input
end

-- Função para criar botão
local function createButton(yPos, text, width)
    width = width or 100
    local xPos = width == 360 and 20 or 280
    
    local button = Instance.new("TextButton")
    button.Parent = ScrollFrame
    button.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
    button.BorderSizePixel = 0
    button.Position = UDim2.new(0, xPos, 0, yPos)
    button.Size = UDim2.new(0, width, 0, width == 360 and 50 or 40)
    button.Font = Enum.Font.GothamBold
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = width == 360 and 16 or 14
    
    local corner = Instance.new("UICorner")
    corner.Parent = button
    corner.CornerRadius = UDim.new(0, 8)
    
    return button
end

-- Criar elementos da GUI
local SpeedLabel = createLabel("⚡ VELOCIDADE (1-500)", 10)
local SpeedSlider = createInputBox(45, "Digite 1-500", "16")
local SpeedButton = createButton(45, "APLICAR")

local JumpLabel = createLabel("🚀 ALTURA DO PULO (1-500)", 100)
local JumpSlider = createInputBox(135, "Digite 1-500", "50")
local JumpButton = createButton(135, "APLICAR")

local FlySpeedLabel = createLabel("✈️ VELOCIDADE DO FLY (1-50)", 190)
local FlySpeedSlider = createInputBox(225, "Digite 1-50", "25")
local FlySpeedButton = createButton(225, "APLICAR")

local TeleportButton = createButton(280, "📍 ATIVAR TELEPORT TOOL", 360)
local FlyButton = createButton(345, "✈️ ATIVAR FLY (E = Subir / Q = Descer)", 360)
local LockCameraButton = createButton(410, "🎯 TRAVAR CAMERA NO ALVO", 360)

-- Seção Auto Kill
local AutoKillLabel = createLabel("🔫 AUTO KILL - SELECIONE O ALVO", 475)
local TargetDropdown = createButton(510, "Selecionar Player ▼", 360)
local AutoKillButton = createButton(575, "❌ AUTO KILL DESATIVADO", 360)

local CloseButton = createButton(820, "➖ MINIMIZAR MENU", 360)
CloseButton.BackgroundColor3 = Color3.fromRGB(180, 0, 0)

-- Variáveis para Fly
local bodyVelocity
local bodyGyro
local flyConnection
local inputBeganConnection
local inputEndedConnection
local moving = {w = false, a = false, s = false, d = false, e = false, q = false}

-- Variáveis para Lock Camera
local lockConnection
local originalCameraType

-- Variáveis para Auto Kill
local autoKillConnection

-- Funções
local function updateSpeed()
    local value = tonumber(SpeedSlider.Text)
    if value and value >= 1 and value <= 500 then
        speedValue = value
        if humanoid then
            humanoid.WalkSpeed = speedValue
        end
        SpeedLabel.Text = "⚡ VELOCIDADE (1-500): " .. speedValue
    else
        SpeedLabel.Text = "⚡ VELOCIDADE (1-500): VALOR INVÁLIDO!"
        task.wait(2)
        SpeedLabel.Text = "⚡ VELOCIDADE (1-500)"
    end
end

local function updateJump()
    local value = tonumber(JumpSlider.Text)
    if value and value >= 1 and value <= 500 then
        jumpValue = value
        if humanoid then
            humanoid.JumpPower = jumpValue
        end
        JumpLabel.Text = "🚀 ALTURA DO PULO (1-500): " .. jumpValue
    else
        JumpLabel.Text = "🚀 ALTURA DO PULO (1-500): VALOR INVÁLIDO!"
        task.wait(2)
        JumpLabel.Text = "🚀 ALTURA DO PULO (1-500)"
    end
end

local function updateFlySpeed()
    local value = tonumber(FlySpeedSlider.Text)
    if value and value >= 1 and value <= 50 then
        flySpeed = value
        FlySpeedLabel.Text = "✈️ VELOCIDADE DO FLY (1-50): " .. flySpeed
    else
        FlySpeedLabel.Text = "✈️ VELOCIDADE DO FLY (1-50): INVÁLIDO!"
        task.wait(2)
        FlySpeedLabel.Text = "✈️ VELOCIDADE DO FLY (1-50)"
    end
end

local function createTeleportTool()
    local existingTool = player.Backpack:FindFirstChild("Teleport Tool")
    if existingTool then
        existingTool:Destroy()
    end
    
    local tool = Instance.new("Tool")
    tool.Name = "Teleport Tool"
    tool.RequiresHandle = false
    tool.Parent = player.Backpack
    
    tool.Activated:Connect(function()
        if mouse.Target then
            local targetPos = mouse.Hit.Position
            local char = player.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
            end
        end
    end)
    
    TeleportButton.Text = "✅ TELEPORT TOOL ATIVADO"
    TeleportButton.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
end

local function cleanupFly()
    if bodyVelocity and bodyVelocity.Parent then
        bodyVelocity:Destroy()
    end
    if bodyGyro and bodyGyro.Parent then
        bodyGyro:Destroy()
    end
    if flyConnection then
        flyConnection:Disconnect()
        flyConnection = nil
    end
    if inputBeganConnection then
        inputBeganConnection:Disconnect()
        inputBeganConnection = nil
    end
    if inputEndedConnection then
        inputEndedConnection:Disconnect()
        inputEndedConnection = nil
    end
    bodyVelocity = nil
    bodyGyro = nil
end

local function updateMovement()
    if not flyEnabled or not bodyVelocity or not bodyGyro then return end
    
    local direction = Vector3.new(0, 0, 0)
    local camera = workspace.CurrentCamera
    
    if moving.w then direction = direction + camera.CFrame.LookVector end
    if moving.s then direction = direction - camera.CFrame.LookVector end
    if moving.d then direction = direction + camera.CFrame.RightVector end
    if moving.a then direction = direction - camera.CFrame.RightVector end
    if moving.e then direction = direction + Vector3.new(0, 1, 0) end
    if moving.q then direction = direction - Vector3.new(0, 1, 0) end
    
    if direction.Magnitude > 0 then
        direction = direction.Unit
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bodyVelocity.Velocity = direction * flySpeed
        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bodyGyro.CFrame = camera.CFrame
    else
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    end
end

local function toggleFly()
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    
    if not flyEnabled then
        flyEnabled = true
        FlyButton.Text = "✅ FLY ATIVADO (E = Subir / Q = Descer)"
        FlyButton.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.Parent = char.HumanoidRootPart
        
        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(0, 0, 0)
        bodyGyro.P = 9000
        bodyGyro.Parent = char.HumanoidRootPart
        
        inputBeganConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if gameProcessed or not flyEnabled then return end
            local key = input.KeyCode.Name:lower()
            if moving[key] ~= nil then
                moving[key] = true
            end
        end)
        
        inputEndedConnection = UserInputService.InputEnded:Connect(function(input)
            if not flyEnabled then return end
            local key = input.KeyCode.Name:lower()
            if moving[key] ~= nil then
                moving[key] = false
            end
        end)
        
        flyConnection = RunService.Heartbeat:Connect(function()
            if flyEnabled then
                updateMovement()
            end
        end)
        
    else
        flyEnabled = false
        FlyButton.Text = "✈️ ATIVAR FLY (E = Subir / Q = Descer)"
        FlyButton.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
        
        for key, _ in pairs(moving) do
            moving[key] = false
        end
        
        cleanupFly()
    end
end

local function toggleLockCamera()
    if not lockCameraEnabled then
        lockCameraEnabled = true
        LockCameraButton.Text = "✅ CAMERA TRAVADA NO ALVO"
        LockCameraButton.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        
        originalCameraType = workspace.CurrentCamera.CameraType
        
        lockConnection = RunService.RenderStepped:Connect(function()
            if lockCameraEnabled and selectedTarget and selectedTarget.Character then
                local targetChar = selectedTarget.Character
                local targetHead = targetChar:FindFirstChild("Head")
                
                if targetHead then
                    workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, targetHead.Position)
                end
            end
        end)
    else
        lockCameraEnabled = false
        LockCameraButton.Text = "🎯 TRAVAR CAMERA NO ALVO"
        LockCameraButton.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
        
        if lockConnection then
            lockConnection:Disconnect()
            lockConnection = nil
        end
        
        if originalCameraType then
            workspace.CurrentCamera.CameraType = originalCameraType
        end
    end
end

local function toggleAutoKill()
    if not selectedTarget then
        AutoKillButton.Text = "⚠️ SELECIONE UM ALVO PRIMEIRO!"
        task.wait(2)
        AutoKillButton.Text = "❌ AUTO KILL DESATIVADO"
        return
    end
    
    if not autoKillEnabled then
        autoKillEnabled = true
        AutoKillButton.Text = "✅ AUTO KILL ATIVADO: " .. selectedTarget.Name
        AutoKillButton.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        
        autoKillConnection = RunService.Heartbeat:Connect(function()
            if autoKillEnabled and selectedTarget and selectedTarget.Character then
                local targetChar = selectedTarget.Character
                local targetHum = targetChar:FindFirstChild("Humanoid")
                
                if targetHum and targetHum.Health > 0 then
                    targetHum.Health = 0
                end
            end
        end)
    else
        autoKillEnabled = false
        AutoKillButton.Text = "❌ AUTO KILL DESATIVADO"
        AutoKillButton.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
        
        if autoKillConnection then
            autoKillConnection:Disconnect()
            autoKillConnection = nil
        end
    end
end

local function showPlayerList()
    local PlayerListFrame = Instance.new("Frame")
    PlayerListFrame.Parent = ScreenGui
    PlayerListFrame.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
    PlayerListFrame.BorderSizePixel = 0
    PlayerListFrame.Position = UDim2.new(0.35, 0, 0.3, 0)
    PlayerListFrame.Size = UDim2.new(0, 300, 0, 400)
    PlayerListFrame.Active = true
    PlayerListFrame.Draggable = true
    
    local listCorner = Instance.new("UICorner")
    listCorner.Parent = PlayerListFrame
    listCorner.CornerRadius = UDim.new(0, 10)
    
    local listTitle = Instance.new("TextLabel")
    listTitle.Parent = PlayerListFrame
    listTitle.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
    listTitle.BorderSizePixel = 0
    listTitle.Size = UDim2.new(1, 0, 0, 40)
    listTitle.Font = Enum.Font.GothamBold
    listTitle.Text = "SELECIONAR ALVO"
    listTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    listTitle.TextSize = 18
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.Parent = listTitle
    titleCorner.CornerRadius = UDim.new(0, 10)
    
    local playerScroll = Instance.new("ScrollingFrame")
    playerScroll.Parent = PlayerListFrame
    playerScroll.BackgroundTransparency = 1
    playerScroll.Position = UDim2.new(0, 10, 0, 50)
    playerScroll.Size = UDim2.new(1, -20, 1, -100)
    playerScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
    playerScroll.ScrollBarThickness = 6
    playerScroll.ScrollBarImageColor3 = Color3.fromRGB(220, 0, 0)
    
    local closeListBtn = Instance.new("TextButton")
    closeListBtn.Parent = PlayerListFrame
    closeListBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
    closeListBtn.Position = UDim2.new(0, 10, 1, -40)
    closeListBtn.Size = UDim2.new(1, -20, 0, 35)
    closeListBtn.Font = Enum.Font.GothamBold
    closeListBtn.Text = "FECHAR"
    closeListBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeListBtn.TextSize = 14
    
    local closeBtnCorner = Instance.new("UICorner")
    closeBtnCorner.Parent = closeListBtn
    closeBtnCorner.CornerRadius = UDim.new(0, 8)
    
    closeListBtn.MouseButton1Click:Connect(function()
        PlayerListFrame:Destroy()
    end)
    
    local yOffset = 0
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= player then
            local playerBtn = Instance.new("TextButton")
            playerBtn.Parent = playerScroll
            playerBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
            playerBtn.Position = UDim2.new(0, 0, 0, yOffset)
            playerBtn.Size = UDim2.new(1, -10, 0, 35)
            playerBtn.Font = Enum.Font.Gotham
            playerBtn.Text = plr.Name
            playerBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            playerBtn.TextSize = 14
            
            local btnCorner = Instance.new("UICorner")
            btnCorner.Parent = playerBtn
            btnCorner.CornerRadius = UDim.new(0, 6)
            
            playerBtn.MouseButton1Click:Connect(function()
                selectedTarget = plr
                TargetDropdown.Text = "Alvo: " .. plr.Name
                PlayerListFrame:Destroy()
            end)
            
            yOffset = yOffset + 40
        end
    end
    
    playerScroll.CanvasSize = UDim2.new(0, 0, 0, yOffset)
end

local function toggleMenu()
    menuOpen = not menuOpen
    MainFrame.Visible = menuOpen
    DragonButton.Visible = not menuOpen
end

-- Eventos dos botões
SpeedButton.MouseButton1Click:Connect(updateSpeed)
JumpButton.MouseButton1Click:Connect(updateJump)
FlySpeedButton.MouseButton1Click:Connect(updateFlySpeed)

TeleportButton.MouseButton1Click:Connect(function()
    if not teleportEnabled then
        createTeleportTool()
        teleportEnabled = true
    end
end)

FlyButton.MouseButton1Click:Connect(toggleFly)
LockCameraButton.MouseButton1Click:Connect(toggleLockCamera)
TargetDropdown.MouseButton1Click:Connect(showPlayerList)
AutoKillButton.MouseButton1Click:Connect(toggleAutoKill)
CloseButton.MouseButton1Click:Connect(toggleMenu)
DragonButton.MouseButton1Click:Connect(toggleMenu)

-- Efeitos visuais nos botões
local function addButtonEffect(button, originalColor)
    button.MouseEnter:Connect(function()
        button.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    end)
    button.MouseLeave:Connect(function()
        if button == FlyButton and flyEnabled then
            button.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        elseif button == TeleportButton and teleportEnabled then
            button.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        elseif button == LockCameraButton and lockCameraEnabled then
            button.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        elseif button == AutoKillButton and autoKillEnabled then
            button.BackgroundColor3 = Color3.fromRGB(0, 139, 0)
        else
            button.BackgroundColor3 = originalColor
        end
    end)
end

addButtonEffect(SpeedButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(JumpButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(FlySpeedButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(TeleportButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(FlyButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(LockCameraButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(AutoKillButton, Color3.fromRGB(220, 0, 0))
addButtonEffect(CloseButton, Color3.fromRGB(180, 0, 0))
addButtonEffect(DragonButton, Color3.fromRGB(139, 0, 0))

-- Aplicar valores iniciais
if humanoid then
    humanoid.WalkSpeed = speedValue
    humanoid.JumpPower = jumpValue
end

-- Atualizar humanoid ao morrer/respawnar
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = newChar:WaitForChild("Humanoid")
    
    -- Limpar fly se estiver ativo
    if flyEnabled then
        flyEnabled = false
        cleanupFly()
        FlyButton.Text = "✈️ ATIVAR FLY (E = Subir / Q = Descer)"
        FlyButton.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
    end
    
    -- Limpar lock camera
    if lockCameraEnabled then
        lockCameraEnabled = false
        LockCameraButton.Text = "🎯 TRAVAR CAMERA NO ALVO"
        LockCameraButton.BackgroundColor3 = Color3.fromRGB(220, 0, 0)
        if lockConnection then
            lockConnection:Disconnect()
            lockConnection = nil
        end
    end
    
    -- Reaplicar valores
    task.wait(0.5)
    if humanoid then
        humanoid.WalkSpeed = speedValue
        humanoid.JumpPower = jumpValue
    end
end)

print("🐉 Dragon Hub V2 carregado com sucesso!")
print("Novas funções: Lock Camera + Auto Kill")
print("Fly Speed: 1-50 | Speed: 1-500 | Jump: 1-500")
print("Clique no botão 🐉 para abrir/fechar o menu")
