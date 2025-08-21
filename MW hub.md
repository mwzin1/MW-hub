--// MW Hub Mobile Friendly + Azul Suave + Botões Verde-Azulado Condicional + ESP Branco

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")

-- GUI principal
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "MW_Hub"

-- Função arrastar
local function makeDraggable(frame)
    local dragging = false
    local dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement then
            local connection
            connection = RunService.RenderStepped:Connect(function()
                if dragging and input.Position then
                    local delta = input.Position - dragStart
                    frame.Position = UDim2.new(
                        startPos.X.Scale, startPos.X.Offset + delta.X,
                        startPos.Y.Scale, startPos.Y.Offset + delta.Y
                    )
                else
                    connection:Disconnect()
                end
            end)
        end
    end)
end

-- Frame principal
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 320)
MainFrame.Position = UDim2.new(0.5,-125,0.5,-160)
MainFrame.BackgroundColor3 = Color3.fromRGB(0,85,170)
MainFrame.Active = true
makeDraggable(MainFrame)

local UICorner = Instance.new("UICorner", MainFrame)
UICorner.CornerRadius = UDim.new(0,8)

-- Título MW Hub centralizado
local Title = Instance.new("TextLabel", MainFrame)
Title.Text = "MW Hub"
Title.Size = UDim2.new(1,0,0,25)
Title.Position = UDim2.new(0,0,0,0)
Title.TextScaled = true
Title.Font = Enum.Font.SourceSansBold
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Center
Title.TextYAlignment = Enum.TextYAlignment.Top

-- Linha ttk/rblx menor e mais juntinhos
local SubTitle = Instance.new("TextLabel", MainFrame)
SubTitle.Text = "ttk:@zwzin26  rblx:@mwzin_17"
SubTitle.Size = UDim2.new(1,0,0,15)
SubTitle.Position = UDim2.new(0,0,0,25)
SubTitle.TextScaled = true
SubTitle.Font = Enum.Font.SourceSans
SubTitle.TextColor3 = Color3.new(1,1,1)
SubTitle.BackgroundTransparency = 1
SubTitle.TextXAlignment = Enum.TextXAlignment.Center
SubTitle.TextYAlignment = Enum.TextYAlignment.Top

-- Botão minimizar
local MinBtn = Instance.new("TextButton", MainFrame)
MinBtn.Size = UDim2.new(0,30,0,30)
MinBtn.Position = UDim2.new(1,-35,0,5)
MinBtn.Text = "-"
MinBtn.TextScaled = true
MinBtn.BackgroundColor3 = Color3.fromRGB(255,0,0)
MinBtn.AutoButtonColor = true

-- Bolinha arrastável canto superior esquerdo
local BallBtn = Instance.new("TextButton", ScreenGui)
local margin = 20
local btnSize = 60
BallBtn.Size = UDim2.new(0,btnSize,0,btnSize)
BallBtn.Position = UDim2.new(0, margin, 0, margin)
BallBtn.BackgroundColor3 = Color3.fromRGB(100,170,255) -- azul claro
BallBtn.Text = "MW Hub"
BallBtn.TextScaled = true
BallBtn.TextColor3 = Color3.new(1,1,1)
BallBtn.Visible = false
BallBtn.AutoButtonColor = true
local BallCorner = Instance.new("UICorner", BallBtn)
BallCorner.CornerRadius = UDim.new(0,btnSize/2)
makeDraggable(BallBtn)

-- Minimizar / Restaurar
MinBtn.MouseButton1Click:Connect(function()
    local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0,50,0,50)})
    tween:Play()
    tween.Completed:Connect(function()
        MainFrame.Visible = false
        BallBtn.Visible = true
    end)
end)
BallBtn.MouseButton1Click:Connect(function()
    BallBtn.Visible = false
    MainFrame.Visible = true
    local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0,250,0,320)})
    tween:Play()
end)

-- Função criar botão
local function CreateButton(name,y)
    local Btn = Instance.new("TextButton", MainFrame)
    Btn.Size = UDim2.new(1,-10,0,30)
    Btn.Position = UDim2.new(0,5,0,y)
    Btn.BackgroundColor3 = Color3.fromRGB(0,120,220)
    Btn.Text = name.." [OFF]"
    Btn.TextScaled = true
    Btn.TextColor3 = Color3.new(1,1,1)
    Btn.Font = Enum.Font.SourceSansBold
    Btn.AutoButtonColor = true
    return Btn
end

-- Função animar botão suave só quando ativado
local function AnimateButtonPulse(btn,state)
    local baseColor = Color3.fromRGB(0,200,150)
    local pulseColor = Color3.fromRGB(0,255,200)
    btn.BackgroundColor3 = state and baseColor or Color3.fromRGB(0,120,220)
    if state then
        spawn(function()
            local increasing = true
            while state and btn.Parent do
                local current = btn.BackgroundColor3
                local target = increasing and pulseColor or baseColor
                btn.BackgroundColor3 = current:Lerp(target,0.05)
                if (current - target).Magnitude < 0.01 then
                    increasing = not increasing
                end
                wait(0.03)
            end
            btn.BackgroundColor3 = baseColor
        end)
    else
        btn.BackgroundColor3 = Color3.fromRGB(0,120,220)
    end
end

-- Função criar TextBox
local function CreateBox(name,y,callback)
    local Label = Instance.new("TextLabel", MainFrame)
    Label.Size = UDim2.new(0.4,-10,0,30)
    Label.Position = UDim2.new(0,5,0,y)
    Label.BackgroundTransparency = 1
    Label.Text = name..":"
    Label.TextScaled = true
    Label.TextColor3 = Color3.new(1,1,1)
    Label.Font = Enum.Font.SourceSansBold

    local Box = Instance.new("TextBox", MainFrame)
    Box.Size = UDim2.new(0.6,-10,0,30)
    Box.Position = UDim2.new(0.4,5,0,y)
    Box.BackgroundColor3 = Color3.fromRGB(20,20,20)
    Box.Text = ""
    Box.TextScaled = true
    Box.TextColor3 = Color3.new(1,1,1)
    Box.Font = Enum.Font.SourceSans
    Box.FocusLost:Connect(function()
        if tonumber(Box.Text) then callback(tonumber(Box.Text)) end
    end)
end

-- Noclip
local noclip = false
local NoclipBtn = CreateButton("NoClip",50)
NoclipBtn.MouseButton1Click:Connect(function()
    noclip = not noclip
    AnimateButtonPulse(NoclipBtn,noclip)
    NoclipBtn.Text = "NoClip ["..(noclip and "ON" or "OFF").."]"
end)
RunService.Stepped:Connect(function()
    if noclip then
        for _,v in pairs(Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)

-- Pulo Infinito
local InfiniteJump = false
local JumpBtn = CreateButton("Pulo Infinito",90)
JumpBtn.MouseButton1Click:Connect(function()
    InfiniteJump = not InfiniteJump
    AnimateButtonPulse(JumpBtn,InfiniteJump)
    JumpBtn.Text = "Pulo Infinito ["..(InfiniteJump and "ON" or "OFF").."]"
end)
UserInputService.JumpRequest:Connect(function()
    if InfiniteJump and Humanoid then
        Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- X-Ray
local xray = false
local originalTransparency = {}
local XRayBtn = CreateButton("X-Ray",130)
XRayBtn.MouseButton1Click:Connect(function()
    xray = not xray
    AnimateButtonPulse(XRayBtn,xray)
    if xray then
        for _,obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") and not obj:IsDescendantOf(Character) then
                originalTransparency[obj] = obj.Transparency
                obj.Transparency = 0.9
            end
        end
    else
        for obj,trans in pairs(originalTransparency) do
            if obj then obj.Transparency = trans end
        end
        originalTransparency = {}
    end
    XRayBtn.Text = "X-Ray ["..(xray and "ON" or "OFF").."]"
end)

-- ESP com contorno e nomes brancos
local esp = false
local espObjects = {}
local ESPBtn = CreateButton("ESP",170)
local function UpdateESP()
    for _,data in pairs(espObjects) do
        if data.Highlight then data.Highlight:Destroy() end
        if data.NameTag then data.NameTag:Destroy() end
    end
    espObjects = {}
    if esp then
        for _,plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = plr.Character
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = workspace

                local Billboard = Instance.new("BillboardGui", plr.Character)
                Billboard.Adornee = plr.Character:FindFirstChild("Head") or plr.Character:FindFirstChild("HumanoidRootPart")
                Billboard.Size = UDim2.new(0,100,0,40)
                Billboard.StudsOffset = Vector3.new(0,2,0)
                Billboard.AlwaysOnTop = true

                local NameLabel = Instance.new("TextLabel", Billboard)
                NameLabel.Size = UDim2.new(1,0,1,0)
                NameLabel.BackgroundTransparency = 1
                NameLabel.Text = plr.Name
                NameLabel.TextColor3 = Color3.fromRGB(255,255,255) -- branco
                NameLabel.TextScaled = true
                NameLabel.Font = Enum.Font.SourceSansBold

                espObjects[plr] = {Highlight = highlight, NameTag = Billboard}
            end
        end
    end
end
ESPBtn.MouseButton1Click:Connect(function()
    esp = not esp
    AnimateButtonPulse(ESPBtn,esp)
    ESPBtn.Text = "ESP ["..(esp and "ON" or "OFF").."]"
    UpdateESP()
end)
Players.PlayerAdded:Connect(function() if esp then wait(1) UpdateESP() end end)
Players.PlayerRemoving:Connect(function() if esp then UpdateESP() end end)

-- Speed / Jump
CreateBox("Speed",210,function(value) Humanoid.WalkSpeed = value end)
CreateBox("Jump",250,function(value)
    if value > 200 then value = 200 end
    Humanoid.JumpPower = value
end)

-- Hub suave com pulso lento
spawn(function()
    local pulse = 0
    local pulseIncreasing = true
    while MainFrame and MainFrame.Parent do
        if pulseIncreasing then
            pulse = pulse + 0.01
            if pulse >= 0.2 then pulseIncreasing = false end
        else
            pulse = pulse - 0.01
            if pulse <= 0 then pulseIncreasing = true end
        end
        local cornerPulse = 8 + pulse*50
        local pulseColor = Color3.fromRGB(0, math.floor(85 + pulse*170), 255)
        MainFrame.BackgroundColor3 = pulseColor
        UICorner.CornerRadius = UDim.new(0, cornerPulse)
        wait(0.03)
    end
end)
