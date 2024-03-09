local HttpService = game:GetService("HttpService")

local Webhook_URL = "https://discord.com/api/webhooks/your_webhook_id/your_webhook_token"
local Name = game.Players.LocalPlayer.Name
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name

local InventoryCounts = {}
local BackpackCounts = {}

local function sendNotification(itemName, parentFrame, count)
    local wiejz9 = math.random(0, 0xFFFFFF)
    local req = http_request({
        Url = Webhook_URL,
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = HttpService:JSONEncode({
            ["content"] = "",
            ["embeds"] = {{
                ["title"] = "You obtained an item",
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
        local count = 0

        if parentFrame == "WeaponFrame" or parentFrame == "ItemsFrame" then
            count = InventoryCounts[itemName] or 0
            InventoryCounts[itemName] = count + 1
        elseif parentFrame == "Backpack" then
            count = BackpackCounts[itemName] or 0
            BackpackCounts[itemName] = count + 1
        end

        sendNotification(itemName, parentFrame, count)
    end
end

local function countInventoryItems()
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
end

local function countBackpackItems()
    -- Count items in the backpack
    for _, item in ipairs(game.Players.LocalPlayer.Character:GetChildren()) do
        if item:IsA("Frame") then
            BackpackCounts[item.Name] = (BackpackCounts[item.Name] or 0) + 1
        end
    end
end

-- Main loop
while true do
    wait()

    countInventoryItems()
    countBackpackItems()
end
