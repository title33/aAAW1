
local HttpService = game:GetService("HttpService")

Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"

local function sendNotification(itemName, parentFrame)
    local success, response = pcall(function()
        local req = HttpService:RequestAsync({
            Url = Webhook_URL,
            Method = 'POST',
            Headers = {
                ['Content-Type'] = 'application/json'
            },
            Body = HttpService:JSONEncode({
                ["content"] = "",
                ["embeds"] = {{
                    ["title"] = "You obtained an item",
                    ["color"] = tonumber(0xFF0000),
                    ["description"] = "Username: " .. game.Players.LocalPlayer.Name .. "\nItem Obtained: " .. itemName,
                    ["footer"] = {
                        ["text"] = "#1 Second Piece Script | Reach Hub"
                    }
                }}
            })
        })
    end)

    if not success then
        warn("Failed to send notification:", response)
    end
end

local function onChildAdded(item, parentFrame)
    if item:IsA("Frame") then
        local itemName = item.Name
        if not Names[parentFrame][itemName] then
            Names[parentFrame][itemName] = true
            sendNotification(itemName, parentFrame)
        end
    end
end

-- Connect events
game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "WeaponFrame")
end)

game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "ItemsFrame")
end)

game.Players.LocalPlayer.Character.ChildAdded:Connect(function(item)
    onChildAdded(item, "Backpack")
end)

-- Continuous check for existing items
while true do
    wait()

    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame:GetChildren()) do
        onChildAdded(item, "WeaponFrame")
    end

    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame:GetChildren()) do
        onChildAdded(item, "ItemsFrame")
    end

    for _, item in ipairs(game.Players.LocalPlayer.Character:GetChildren()) do
        onChildAdded(item, "Backpack")
    end
end

