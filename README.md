local HttpService = game:GetService("HttpService")
local Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"
local Name = game.Players.LocalPlayer.Name
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
local requestfunc = http and http.request or http_request or fluxus and fluxus.request or request
local InventoryCounts = {}
local BackpackCounts = {}

local wiejz9 = tonumber(0xFF0000)

local function sendNotification(itemName, parentFrame)
    local count = 0
    if parentFrame == "WeaponFrame" then
        count = InventoryCounts[itemName] or 0
        InventoryCounts[itemName] = count + 1
    elseif parentFrame == "ItemsFrame" then
        count = InventoryCounts[itemName] or 0
        InventoryCounts[itemName] = count + 1
    elseif parentFrame == "Backpack" then
        count = BackpackCounts[itemName] or 0
        BackpackCounts[itemName] = count + 1
    end

    local req = requestfunc({
        Url = Webhook_URL,
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = HttpService:JSONEncode({
            ["content"] = "",
            ["embeds"] = {{
                ["title"] = "คุณได้ไอเทม",
                ["color"] = tonumber(wiejz9),
                ["description"] = "Username: " .. Name .. "\nGame: " .. GameName .. "\nItem Obtained: " .. itemName .. "\nCount: " .. count,
                ["footer"] = {
                    ["text"] = "#1 Second Piece Script | Reach Hub"
                }
            }}
        })
    })
end

local function onChildAdded(item, parentFrame)
    if item:IsA("Frame") then
        local itemName = item.Name
        sendNotification(itemName, parentFrame)
    end
end

game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "WeaponFrame")
end)

game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame.ChildAdded:Connect(function(item)
    onChildAdded(item, "ItemsFrame")
end)

game.Players.LocalPlayer.Character.ChildAdded:Connect(function(item)
    onChildAdded(item, "Backpack")
end)

while true do
    wait()

    -- Reset counts
    for key, _ in pairs(InventoryCounts) do
        InventoryCounts[key] = 0
    end

    -- Count items in the inventory frames
    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame:GetChildren()) do
        if item:IsA("Frame") then
            InventoryCounts[item.Name] = (InventoryCounts[item.Name] or 0) + 1
        end
    end
    for _, item in ipairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame:GetChildren()) do
        if item:IsA("Frame") then
            InventoryCounts[item.Name] = (InventoryCounts[item.Name] or 0) + 1
        end
    end

    -- Count items in the backpack
    for _, item in ipairs(game.Players.LocalPlayer.Character:GetChildren()) do
        if item:IsA("Frame") then
            BackpackCounts[item.Name] = (BackpackCounts[item.Name] or 0) + 1
        end
    end
end
