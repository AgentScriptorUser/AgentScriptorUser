-- Ensure the game is fully loaded before running scripts
if not game:IsLoaded() then
    game.Loaded:Wait()
end

-- Load external script (Adonis cries)
loadstring(game:HttpGet("https://raw.githubusercontent.com/Pixeluted/adoniscries/main/Source.lua", true))()

-- Rainbow color update function
local function getRainbowColor()
    local t = tick() * 2
    return Color3.fromHSV((t % 1), 1, 1)
end

-- Rayfield UI Library
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Get local player
local player = game.Players.LocalPlayer
local username = player.Name

-- Create the main window
local Window = Rayfield:CreateWindow({
    Name = "CrispyHub",  -- Changed the name to "CrispyHub"
    LoadingTitle = "CrispyHub Loading...",
    LoadingSubtitle = "by YourName",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = nil,
        FileName = "CrispyHubConfig"  -- Changed the config file name to "CrispyHubConfig"
    },
    Discord = {
        Enabled = true,
        Invite = "swaggerst", -- change this to your actual Discord invite
        RememberJoins = true
    },
    KeySystem = false
})

-- HOME Tab
local HomeTab = Window:CreateTab("Home", 4483362458)

-- Welcome message using the player's username
HomeTab:CreateSection("Welcome to CrispyHub, " .. username .. "!")  -- Updated the name here

HomeTab:CreateParagraph({
    Title = "Username",
    Content = username
})

HomeTab:CreateParagraph({
    Title = "Rank",
    Content = "None"
})

-- TARGET Tab
local TargetTab = Window:CreateTab("Target", 4483362458)

TargetTab:CreateInput({
    Name = "Set Target",
    PlaceholderText = "Username",
    RemoveTextAfterFocusLost = true,
    Callback = function(Value)
        print("Target set to:", Value)
    end,
})

-- TOGGLES Tab
local TogglesTab = Window:CreateTab("Toggles", 4483362458)

-- Silent Aim Configuration
getgenv().Silent = {
    Settings = {
        Toggled = false, -- Initially set to false (disabled)
        AimPart = "HumanoidRootPart",
        HitChance = 100,

        Prediction = {
            Enabled = true,
            Horizontal = 0.138,
            Vertical = 0.138,
        },

        Circle = {
            Visible = true,
            Color = Color3.fromRGB(255, 255, 255), -- Will update to rainbow dynamically
            Transparency = 1,
            Thickness = 1.5,
            NumSides = 1000,
            Radius = 250, -- Bigger size
            Filled = false,
        },
    },
}

-- Load Silent Aim logic
local function loadSilentAim()
    local success, err = pcall(function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/TheRealXORA/Roblox/Scripts/Basic%20Silent-Aim", true))()
    end)
    if not success then
        warn("Failed to load Silent Aim script:", err)
    else
        print("Silent Aim Enabled!")
    end
end

-- Function to disable Silent Aim (if applicable)
local function disableSilentAim()
    getgenv().Silent.Settings.Toggled = false
    print("Silent Aim Disabled!")
end

-- Button to enable/disable Silent Aim
TogglesTab:CreateButton({
    Name = "Toggle Silent Aim",
    Callback = function()
        if getgenv().Silent.Settings.Toggled then
            disableSilentAim()
        else
            getgenv().Silent.Settings.Toggled = true
            loadSilentAim()
        end
    end
})

-- Update circle color to rainbow dynamically
spawn(function()
    while true do
        pcall(function()
            if getgenv().Silent.Settings.Circle.Visible and Drawing then
                getgenv().Silent.Settings.Circle.Color = getRainbowColor()
            end
        end)
        task.wait()
    end
end)

-- QUICK BUY Tab (with Auto Buy Armor and Auto Buy Pizza functionality)
local BuyTab = Window:CreateTab("Quick Buy", 4483362458)

-- Auto Buy Armor Script
local autoBuyArmorEnabled = false

-- Function to buy medium armor from the pad
local function buyArmor()
    local pad = workspace:WaitForChild("MAP"):WaitForChild("Pads"):FindFirstChild("[Medium Armor]")
    if pad then
        local clickDetector = pad:FindFirstChildOfClass("ClickDetector")
        if clickDetector then
            fireclickdetector(clickDetector)
        end
    end
end

-- Function to check if player has armor
local function hasArmor()
    local player = game.Players.LocalPlayer
    for _, tool in ipairs(player.Backpack:GetChildren()) do
        if tool:IsA("Tool") and tool.Name:lower():find("armor") then
            return true
        end
    end
    for _, tool in ipairs(player.Character:GetChildren()) do
        if tool:IsA("Tool") and tool.Name:lower():find("armor") then
            return true
        end
    end
    return false
end

-- Monitor tool and auto-buy armor if missing
task.spawn(function()
    while true do
        if autoBuyArmorEnabled and not hasArmor() then
            buyArmor()
        end
        task.wait(1)
    end
end)

-- Auto Buy Pizza Script
local autoBuyPizzaEnabled = false

-- Function to buy pizza from pad index 14
local function buyPizza()
    local pad = workspace:WaitForChild("MAP"):WaitForChild("Pads"):GetChildren()[14]
    if pad then
        local clickDetector = pad:FindFirstChildOfClass("ClickDetector")
        if clickDetector then
            fireclickdetector(clickDetector)
        end
    end
end
