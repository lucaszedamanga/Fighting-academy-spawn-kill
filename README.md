local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local barreiraButton = Instance.new("TextButton")
barreiraButton.Size = UDim2.new(0, 150, 0, 50)
barreiraButton.Position = UDim2.new(0.5, -160, 0, 10)
barreiraButton.Text = "Barreira"
barreiraButton.Parent = screenGui

local desativarBarreiraButton = Instance.new("TextButton")
desativarBarreiraButton.Size = UDim2.new(0, 150, 0, 50)
desativarBarreiraButton.Position = UDim2.new(0.5, 10, 0, 10)
desativarBarreiraButton.Text = "Desativar Barreira"
desativarBarreiraButton.Parent = screenGui

local targetTextBox = Instance.new("TextBox")
targetTextBox.Size = UDim2.new(0, 150, 0, 50)
targetTextBox.Position = UDim2.new(0.5, -160, 0, 70)
targetTextBox.PlaceholderText = "Nome do Alvo"
targetTextBox.Parent = screenGui

local targetButton = Instance.new("TextButton")
targetButton.Size = UDim2.new(0, 150, 0, 50)
targetButton.Position = UDim2.new(0.5, 10, 0, 70)
targetButton.Text = "Alvo"
targetButton.Parent = screenGui

local playerListButton = Instance.new("TextButton")
playerListButton.Size = UDim2.new(0, 150, 0, 50)
playerListButton.Position = UDim2.new(1, -160, 0, 10)
playerListButton.Text = "Player List"
playerListButton.Parent = screenGui

local playerListGui = Instance.new("ScreenGui")
playerListGui.Parent = player:WaitForChild("PlayerGui")
playerListGui.Enabled = false

local playerListFrame = Instance.new("Frame")
playerListFrame.Size = UDim2.new(0, 300, 0, 400)
playerListFrame.Position = UDim2.new(1, -310, 0, 70)
playerListFrame.BackgroundTransparency = 0.5
playerListFrame.Parent = playerListGui

local playerListScrollingFrame = Instance.new("ScrollingFrame")
playerListScrollingFrame.Size = UDim2.new(1, 0, 1, 0)
playerListScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
playerListScrollingFrame.Parent = playerListFrame

local uiListLayout = Instance.new("UIListLayout")
uiListLayout.Parent = playerListScrollingFrame

local function updatePlayerList()
    for _, child in pairs(playerListScrollingFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    local players = game.Players:GetPlayers()
    playerListScrollingFrame.CanvasSize = UDim2.new(0, 0, 0, #players * 30)

    for _, plr in pairs(players) do
        local playerButton = Instance.new("TextButton")
        playerButton.Size = UDim2.new(1, 0, 0, 30)
        playerButton.Text = plr.DisplayName
        playerButton.Parent = playerListScrollingFrame

        playerButton.MouseButton1Click:Connect(function()
            targetTextBox.Text = plr.Name
            playerListGui.Enabled = false
        end)
    end
end

game.Players.PlayerAdded:Connect(updatePlayerList)
game.Players.PlayerRemoving:Connect(updatePlayerList)

playerListButton.MouseButton1Click:Connect(function()
    playerListGui.Enabled = not playerListGui.Enabled
    if playerListGui.Enabled then
        updatePlayerList()
    end
end)

local barreira
local barreiraActive = false

local function createBarreira()
    if barreiraActive then return end
    barreiraActive = true
    barreira = Instance.new("Part")
    barreira.Size = Vector3.new(8, 10, 8)
    barreira.Anchored = true
    barreira.CanCollide = true
    barreira.Transparency = 0.5
    barreira.Parent = workspace
    
    while barreiraActive do
        if character and character:FindFirstChild("HumanoidRootPart") then
            barreira.CFrame = character.HumanoidRootPart.CFrame
            barreira.CanCollide = true
        end
        wait(0.1)
    end
end

local function removeBarreira()
    barreiraActive = false
    if barreira then
        barreira:Destroy()
        barreira = nil
    end
end

barreiraButton.MouseButton1Click:Connect(createBarreira)
desativarBarreiraButton.MouseButton1Click:Connect(removeBarreira)

local function teleportPlayerToFront(targetPlayer)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetCharacter = targetPlayer.Character
        local targetCFrame = character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
        targetCharacter.HumanoidRootPart.CFrame = targetCFrame
        targetCharacter.HumanoidRootPart.Anchored = true
    end
end

targetButton.MouseButton1Click:Connect(function()
    local targetName = targetTextBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetName)
    if targetPlayer then
        teleportPlayerToFront(targetPlayer)
    else
        print("Jogador não encontrado.")
    end
end)
