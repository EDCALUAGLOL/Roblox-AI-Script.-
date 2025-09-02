loadstring([[

-- Universal AI Script 
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = Players.LocalPlayer

local AI_ENABLED = true
local memory = {}
local customResponses = {}
local defaultResponses = {greetings={"Hello!","Hi!","Hey there!"},farewell={"Goodbye!","See you!","Take care!"},unknown={"I don't know that yet.","Can you teach me?"}}

local function getRandom(list) return list[math.random(1,#list)] end

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniversalAI_GUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0,300,0,150)
Frame.Position = UDim2.new(0,10,0,10)
Frame.BackgroundColor3 = Color3.fromRGB(40,40,40)
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

local function AI_Respond(playerName,msg)
    if not AI_ENABLED then return end
    local reply = nil
    for keyword,response in pairs(customResponses) do
        if msg:lower():find(keyword) then reply=response break end
    end
    if not reply then
        if msg:lower():find("hello") or msg:lower():find("hi") then reply=getRandom(defaultResponses.greetings)
        elseif msg:lower():find("bye") then reply=getRandom(defaultResponses.farewell)
        else reply=getRandom(defaultResponses.unknown)
        end
    end
    table.insert(memory,msg)
    print("[AI Reply to "..playerName.."]: "..reply)
    pcall(function()
        ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(reply,"All")
    end)
end

-- Demo simulation
customResponses["science"]="I love science!"
AI_Respond("Player1","hello")
AI_Respond("Player2","science")
AI_Respond("Player3","bye")

]])()
