
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

        -- Verificar prefixos e alterar cor do texto
        local displayName = plr.DisplayName
        if displayName:sub(1, 4):lower() == "tmn_" then
            playerButton.TextColor3 = Color3.fromRGB(0, 0, 255)
        elseif displayName:sub(1, 4):lower() == "mbs_" then
            playerButton.TextColor3 = Color3.fromRGB(0, 0, 255)
        end

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
            barreira.CanCollide = true  -- Manter colisão ativada
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

local function showConfirmationGui(message, onConfirm, onCancel)
    local confirmationGui = Instance.new("ScreenGui")
    confirmationGui.Parent = player:WaitForChild("PlayerGui")

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 300, 0, 200)
    frame.Position = UDim2.new(0.5, -150, 0.5, -100)
    frame.BackgroundTransparency = 0.5
    frame.Parent = confirmationGui

    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 0.6, 0)
    textLabel.Position = UDim2.new(0, 0, 0, 0)
    textLabel.Text = message
    textLabel.TextScaled = true
    textLabel.Parent = frame

    local okButton = Instance.new("TextButton")
    okButton.Size = UDim2.new(0.4, 0, 0.3, 0)
    okButton.Position = UDim2.new(0.1, 0, 0.7, 0)
    okButton.Text = "Ok"
    okButton.Parent = frame

    local cancelButton = Instance.new("TextButton")
    cancelButton.Size = UDim2.new(0.4, 0, 0.3, 0)
    cancelButton.Position = UDim2.new(0.5, 0, 0.7, 0)
    cancelButton.Text = "Cancelar"
    cancelButton.Parent = frame

    okButton.MouseButton1Click:Connect(function()
        onConfirm()
        confirmationGui:Destroy()
    end)

    cancelButton.MouseButton1Click:Connect(function()
        onCancel()
        confirmationGui:Destroy()
    end)
end



local function freezePlayer(targetPlayer)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetCharacter = targetPlayer.Character
        targetCharacter.HumanoidRootPart.CFrame = character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -4)
        targetCharacter.HumanoidRootPart.Anchored = true
    end
end

barreiraButton.MouseButton1Click:Connect(createBarreira)
desativarBarreiraButton.MouseButton1Click:Connect(removeBarreira)

local selectedPlayers = {}

targetButton.MouseButton1Click:Connect(function()
    local targetName = targetTextBox.Text
    local targetPlayer = game.Players:FindFirstChild(targetName)
    if targetPlayer then
        local targetDisplayName = targetPlayer.DisplayName:lower()
        local playerDisplayName = player.DisplayName:lower()

        local function proceed()
            freezePlayer(targetPlayer)
        end

        local key = playerDisplayName .. targetDisplayName

        if selectedPlayers[key] then
            proceed()
        else
            selectedPlayers[key] = true

            if (playerDisplayName:sub(1, 4) == "tmn_" and targetDisplayName:sub(1, 4) == "tmn_") or
               (playerDisplayName:sub(1, 4) == "mbs_" and targetDisplayName:sub(1, 4) == "mbs_") then
                showConfirmationGui(
                    "Você está selecionando membros da sua gangue. Tem certeza que quer continuar?",
                    proceed,
                    function() print("Ação cancelada.") end
                )
            elseif (playerDisplayName:sub(1, 4) == "tmn_" and targetDisplayName:sub(1, 4) == "mbs_") or
                   (playerDisplayName:sub(1, 4) == "mbs_" and targetDisplayName:sub(1, 4) == "tmn_") then
                showConfirmationGui(
                    "Você está selecionando uma gangue aliada. Tem certeza que quer continuar?",
                    proceed,
                    function() print("Ação cancelada.") end
                )
            else
                proceed()
            end
        end
    else
        print("Jogador não encontrado.")
    end
end)
