# Roblox-AI-Script.-
Universal Roblox AI Chatbot with GUI

---

## **2️⃣ AI.lua**
```lua
-- Roblox AI Assistant Script with GUI Buttons (Educational Purposes Only)

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = Players.LocalPlayer

-- AI state
local AI_ENABLED = false
local memory = {}
local customResponses = {}

-- Default responses
local defaultResponses = {
    greetings = {"Hello!", "Hi there!", "Hey! How can I help you today?"},
    farewell = {"Goodbye!", "See you later!", "Take care!"},
    whoami = {"I am an AI assistant created for educational purposes."},
    unknown = {"Hmm, I’m not sure about that.", "Interesting question! Can you ask me something else?"}
}

local function getRandomResponse(list)
    return list[math.random(1, #list)]
end

-- Create GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SageAI_GUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 250, 0, 120)
Frame.Position = UDim2.new(0, 10, 0, 10)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BackgroundTransparency = 0.2
Frame.Parent = ScreenGui

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1, -20, 0, 40)
StatusLabel.Position = UDim2.new(0, 10, 0, 10)
StatusLabel.BackgroundTransparency = 1
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
StatusLabel.TextScaled = true
StatusLabel.Text = "[AI] Loaded"
StatusLabel.Parent = Frame

local OnButton = Instance.new("TextButton")
OnButton.Size = UDim2.new(0.45, 0, 0, 40)
OnButton.Position = UDim2.new(0, 10, 0, 60)
OnButton.Text = "Turn AI On"
OnButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
OnButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OnButton.Parent = Frame

local OffButton = Instance.new("TextButton")
OffButton.Size = UDim2.new(0.45, 0, 0, 40)
OffButton.Position = UDim2.new(0.5, 10, 0, 60)
OffButton.Text = "Turn AI Off"
OffButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
OffButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OffButton.Parent = Frame

-- Update GUI
local function updateGUI()
    if AI_ENABLED then
        StatusLabel.Text = "[AI] Enabled"
        StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    else
        StatusLabel.Text = "[AI] Disabled"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
    end
end

-- Button clicks
OnButton.MouseButton1Click:Connect(function()
    AI_ENABLED = true
    updateGUI()
end)

OffButton.MouseButton1Click:Connect(function()
    AI_ENABLED = false
    updateGUI()
end)

updateGUI() -- initial state

-- Chat command handling
Player.Chatted:Connect(function(msg)
    local lower = msg:lower()
    if lower:sub(1,1) == "!" then
        local args = string.split(msg:sub(2), " ")
        local command = args[1]:lower()

        if command == "ai" and args[2] == "on" then
            AI_ENABLED = true
            updateGUI()
        elseif command == "ai" and args[2] == "off" then
            AI_ENABLED = false
            updateGUI()
        elseif command == "clear" then
            memory = {}
        elseif command == "learn" and args[2] then
            local data = string.split(table.concat(args, " ", 2), ":")
            if #data == 2 then
                local keyword = data[1]:lower()
                local response = data[2]
                customResponses[keyword] = response
            end
        end
    end
end)

-- AI responder
local function AI_Respond(player, msg)
    if not AI_ENABLED then return end
    if player == Player then return end

    local lowerMsg = msg:lower()
    local reply = nil

    for keyword, response in pairs(customResponses) do
        if lowerMsg:find(keyword) then
            reply = response
            break
        end
    end

    if not reply then
        if lowerMsg:find("hello") or lowerMsg:find("hi") then
            reply = getRandomResponse(defaultResponses.greetings)
        elseif lowerMsg:find("bye") then
            reply = getRandomResponse(defaultResponses.farewell)
        elseif lowerMsg:find("who") then
            reply = getRandomResponse(defaultResponses.whoami)
        else
            reply = getRandomResponse(defaultResponses.unknown)
        end
    end

    table.insert(memory, msg)
    pcall(function()
        ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(reply, "All")
    end)
end

-- Connect to all players
for _,p in ipairs(Players:GetPlayers()) do
    if p ~= Player then
        p.Chatted:Connect(function(msg) AI_Respond(p, msg) end)
    end
end

Players.PlayerAdded:Connect(function(p)
    if p ~= Player then
        p.Chatted:Connect(function(msg) AI_Respond(p, msg) end)
    end
end)
