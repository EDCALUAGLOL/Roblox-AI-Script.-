--[[
    Universal AI Demo Hub
    Educational purposes only
]]

-- Load Kavo UI Library
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("AI Demo Hub", "DarkTheme")

-- Main Tab
local MainTab = Window:NewTab("Main")
local MainSection = MainTab:NewSection("AI Controls")

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

-- AI Response Function
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Player = Players.LocalPlayer

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
    print("[AI Reply]: "..reply)
    pcall(function()
        if ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents") then
            ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(reply,"All")
        end
    end)
end

-- Buttons
MainSection:NewToggle("Enable AI", "Turn AI on/off", function(state)
    AI_ENABLED = state
end)

MainSection:NewButton("Send Demo Messages", "Sends example messages to AI", function()
    local demoMessages = {"hello","I feel sad","I am happy","I feel stressed"}
    customResponses["stress"] = "Take a deep breath. Let's relax."
    for _,msg in ipairs(demoMessages) do
        AI_Respond(msg)
    end
end)

-- Settings Tab
local SettingsTab = Window:NewTab("Settings")
local SettingsSection = SettingsTab:NewSection("Settings")
SettingsSection:NewKeybind("Toggle UI", "Press key to show/hide UI", Enum.KeyCode.F, function()
    Library:ToggleUI()
end)

-- Info / Credits Tab
local InfoTab = Window:NewTab("Info")
local InfoSection = InfoTab:NewSection("Credits")
InfoSection:NewText("This AI Demo was created for educational purposes only.")
InfoSection:NewText("AI Hub GUI by Kavo UI Library")
