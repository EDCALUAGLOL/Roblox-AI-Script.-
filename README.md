loadstring([[

-- Universal AI Demo Script (Educational Purposes)
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = Players.LocalPlayer

-- AI State
local AI_ENABLED = true
local memory = {}
local customResponses = {}

local defaultResponses = {
    greetings = {"Hello! How are you feeling?", "Hi! I'm here to listen."},
    sad = {"I understand. It's okay to feel this way.", "Let's talk about it."},
    happy = {"That's wonderful!", "I'm glad to hear that!"},
    unknown = {"Can you tell me more?", "I’m here to listen."}
}

local function getRandom(list)
    return list[math.random(1,#list)]
end

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalAI_GUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0,400,0,200)
Frame.Position = UDim2.new(0,10,0,10)
Frame.BackgroundColor3 = Color3.fromRGB(50,50,50)
Frame.BackgroundTransparency = 0.2
Frame.Parent = ScreenGui

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1,-20,0,40)
StatusLabel.Position = UDim2.new(0,10,0,10)
StatusLabel.BackgroundTransparency = 1
StatusLabel.TextColor3 = Color3.fromRGB(255,255,0)
StatusLabel.TextScaled = true
StatusLabel.Text = "[AI] Enabled"
StatusLabel.Parent = Frame

local OnButton = Instance.new("TextButton")
OnButton.Size = UDim2.new(0.45,0,0,40)
OnButton.Position = UDim2.new(0,10,0,60)
OnButton.Text = "Turn AI On"
OnButton.BackgroundColor3 = Color3.fromRGB(0,200,0)
OnButton.TextColor3 = Color3.fromRGB(255,255,255)
OnButton.Parent = Frame

local OffButton = Instance.new("TextButton")
OffButton.Size = UDim2.new(0.45,0,0,40)
OffButton.Position = UDim2.new(0.5,10,0,60)
OffButton.Text = "Turn AI Off"
OffButton.BackgroundColor3 = Color3.fromRGB(200,0,0)
OffButton.TextColor3 = Color3.fromRGB(255,255,255)
OffButton.Parent = Frame

local ChatLog = Instance.new("TextLabel")
ChatLog.Size = UDim2.new(1,-20,0,80)
ChatLog.Position = UDim2.new(0,10,0,110)
ChatLog.BackgroundTransparency = 1
ChatLog.TextColor3 = Color3.fromRGB(255,255,255)
ChatLog.TextScaled = true
ChatLog.TextWrapped = true
ChatLog.Text = "Chat log will appear here..."
ChatLog.Parent = Frame

-- Button Functions
OnButton.MouseButton1Click:Connect(function()
    AI_ENABLED = true
    StatusLabel.Text = "[AI] Enabled"
    StatusLabel.TextColor3 = Color3.fromRGB(0,255,0)
end)

OffButton.MouseButton1Click:Connect(function()
    AI_ENABLED = false
    StatusLabel.Text = "[AI] Disabled"
    StatusLabel.TextColor3 = Color3.fromRGB(255,0,0)
end)

-- AI Response Function
local function AI_Respond(msg)
    if not AI_ENABLED then return end
    local reply = nil

    for keyword, response in pairs(customResponses) do
        if msg:lower():find(keyword) then reply = response break end
    end

    if not reply then
        if msg:lower():find("sad") then reply = getRandom(defaultResponses.sad)
        elseif msg:lower():find("happy") then reply = getRandom(defaultResponses.happy)
        elseif msg:lower():find("hello") or msg:lower():find("hi") then reply = getRandom(defaultResponses.greetings)
        else reply = getRandom(defaultResponses.unknown)
        end
    end

    table.insert(memory,msg)
    ChatLog.Text = ChatLog.Text.."\nPlayer: "..msg.."\nAI: "..reply
    print("[AI Reply]: "..reply)

    -- Attempt to send message in chat
    pcall(function()
        ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(reply,"All")
    end)
end

-- Demo Messages
local demoMessages = {"hello","I feel sad","I am happy","I feel stressed"}
customResponses["stress"] = "Take a deep breath. Let's relax."
for _,msg in ipairs(demoMessages) do
    AI_Respond(msg)
end

]])()
