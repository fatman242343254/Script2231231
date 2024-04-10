local localPlayer = game:GetService("Players").LocalPlayer
local camera = game.Workspace.CurrentCamera
local userInputService = game:GetService("UserInputService")

local function findPlayerInDirection()
    local direction = camera.CFrame.LookVector
    local target = nil
    local minAngle = math.rad(45) -- Minimum angle between the camera's look vector and the direction to the player
    
    for _, player in pairs(game:GetService("Players"):GetPlayers()) do
        if player.Name ~= localPlayer.Name then
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Head") and player.Character.Humanoid.Health > 0 then
                local playerDirection = (player.Character.Head.Position - camera.CFrame.Position).unit
                local angle = math.acos(direction:Dot(playerDirection))
                
                if angle <= minAngle then
                    target = player
                    minAngle = angle
                end
            end
        end
    end
    
    return target
end

local stickyAimEnabled = false
local stickyAimTarget = nil

game:GetService("RunService").RenderStepped:Connect(function()
    if stickyAimEnabled and stickyAimTarget then
        camera.CFrame = CFrame.new(camera.CFrame.Position, stickyAimTarget.Character.Head.Position)
    end
end)

userInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Q then
        stickyAimEnabled = not stickyAimEnabled
        if stickyAimEnabled then
            stickyAimTarget = findPlayerInDirection()
        else
            stickyAimTarget = nil
        end
    end
end)
